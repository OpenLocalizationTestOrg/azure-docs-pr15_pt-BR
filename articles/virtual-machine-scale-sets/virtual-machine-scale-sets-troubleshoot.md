<properties
    pageTitle="Solucionar problemas de escala automática com conjuntos de escala de máquina Virtual | Microsoft Azure"
    description="Solucionar problemas de escala automática com conjuntos de escala de máquina Virtual. Compreenda típicos problemas encontrados e como resolvê-los."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="guybo"/>

# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Solução de problemas de escala automática com conjuntos de escala de máquina Virtual

**Problema** – você criou uma infraestrutura de autoscaling no Azure Gerenciador de recursos usando conjuntos de escala de máquina virtual – por exemplo, implantando um modelo como este: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – você tem suas regras de escala definidas e ele funciona muito bem, exceto que não importa quanto carga colocar nas VMs, ele não escala automática.

## <a name="troubleshooting-steps"></a>Etapas de solução de problemas

Alguns aspectos a considerar incluem:

- Quantas cores cada máquina virtual tem, e você estiver carregando cada núcleo?
 O modelo de início rápido do Azure de exemplo acima tem um script de do_work.php, que carrega um único núcleo. Se você estiver usando uma máquina virtual maior do que um único núcleo tamanho de máquina virtual como Standard_A1 ou D1 e em seguida, você precisará executar este carregamento várias vezes. Verificar quantos núcleos suas VMs revisando [máquinas virtuais tamanhos para Windows Azure](../virtual-machines/virtual-machines-windows-sizes.md)

- Quantos VMs no conjunto de escala de máquina virtual, você está fazendo o trabalho em cada uma delas?

    Uma escala de check-out evento apenas ocorrerá quando a média de CPU em **todas** as VMs em um conjunto de escala excede o valor de limite, ao longo do tempo interno definido nas regras de escala automática.

- Você perder quaisquer eventos de escala?

    Verifique os logs de auditoria no portal do Azure para eventos de escala. Talvez houve uma escala para cima e uma escala para baixo que foi perdida. Você pode filtrar por "Escala"..

    ![Logs de auditoria][audit]

- Os limites de escala e escala-out estão suficientemente diferente?

    Suponha que você definir uma regra de expandir quando CPU média for maior do que 50% mais de 5 minutos e a escala em quando CPU média é menos de 50%. Isso faria com que um problema "falta de sincronismo" quando o uso da CPU for próximo esse limite, com ações de escala constantemente aumentar e diminuir o tamanho do conjunto de. Por isso, o serviço de escala automática tenta evitar "oscilação", que pode manifesto como não dimensionamento. Portanto, verifique se sua escala-out e limites de escala são diferentes o suficiente para permitir que o espaço entre as de dimensionamento.

- Você tenha escrito seu próprio modelo JSON?

    É fácil cometer erros, então comece com um modelo como um acima do qual vem provando trabalhar e fazer pequenas alterações incrementais. 

- Você pode manualmente dimensionar ou reduzir?

    Tente reimplantar o recurso de conjunto de escala de máquina virtual com uma configuração de "capacidade" diferente para alterar o número de VMs manualmente. Um modelo de exemplo para fazer isso é aqui: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – você pode precisar editar o modelo para garantir que ela tem o mesmo tamanho de máquina como seu conjunto de escala está usando. Se você pode alterar com êxito o número de VMs manualmente, você saberá que o problema é isolado em escala automática.

- Verificar seu Microsoft.Compute/virtualMachineScaleSet e os recursos de Microsoft.Insights no [Gerenciador de recursos do Azure](https://resources.azure.com/)

    Esta é uma ferramenta de solução de problemas indispensável que mostra o estado de seus recursos do Gerenciador de recursos do Azure. Clique em sua assinatura e examine o grupo de recursos que você está solucionando problemas. No provedor de recursos de computação examinar o conjunto de escala de máquina virtual que você criou e verifique a exibição da instância, que mostra o estado de uma implantação. Verifique também a exibição da instância de VMs no conjunto de escala de máquina virtual. Vá para o provedor de recursos Microsoft.Insights e verifique as regras de escala automática boas aparência.

- A extensão de diagnóstico está trabalhando e emitir dados de desempenho?

    __Atualização:__ Escala automática Azure foi aprimorada para usar um pipeline de métricas com base em host que já não requer uma extensão de diagnóstico para ser instalado. Isso significa que o próximo que vários parágrafos não se aplicam mais se você criar um aplicativo de autoscaling usando o pipeline de novo. Exemplos de modelos Azure que foram convertidos para usar o pipeline de host: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale, https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale. 

    Usando métricas com base em host para escala automática é melhor pelos seguintes motivos:

    - Menor movimentação de peças como sem extensões de diagnóstico precisa estar instalado.
    - Modelos mais simples. Basta adicione regras de escala automática de ideias para um modelo de conjunto de escala existente.
    - Mais confiável com mais rapidez iniciando de novas VMs e geração de relatórios.

    Os motivos somente, que talvez você queira manter usando uma extensão de diagnóstico seria se você precisa de memória diagnóstico reporting/dimensionamento. Métricas de host com base não relatam memória.

    Com isso em mente, somente siga o restante deste artigo se você ainda estiver usando extensões de diagnósticos para seu autoscaling.

    Escala automática no Gerenciador de recursos do Azure pode trabalhar (mas não precisa mais) por meio de uma máquina virtual extensão chamada a extensão de diagnóstico. Ele emite dados de desempenho para uma conta de armazenamento que você define no modelo. Esses dados, em seguida, são agregados pelo serviço Monitor do Azure.

    Se o serviço de obtenção de informações não pode ler dados de VMs, ele deve enviar um email – por exemplo, se as VMs foram para baixo, portanto, verifique seu email (aquele que você especificou ao criar a conta do Microsoft Azure).

    Você também pode ir e examine os dados por conta própria. Examine a conta de armazenamento do Azure usando um explorer de nuvem. Por exemplo, usando o [Visual Studio nuvem Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), login e escolha a assinatura do Azure você está usando, e o nome de conta de armazenamento de diagnóstico referenciadas na definição de extensão diagnóstico no seu modelo de implantação..

    ![Explorador de nuvem][explorer]

    Aqui, você verá um monte de tabelas onde os dados de cada máquina virtual está sendo armazenados. Levando Linux e a métrica de CPU como exemplo, procure linhas mais recentes. O Gerenciador de nuvem do Visual Studio dá suporte a uma linguagem de consulta para que você possa executar uma consulta como "carimbo de hora gt datetime'2016-02-02T21:20:00Z'" para garantir que você obtenha os eventos mais recentes (suponha tempo é no UTC). Significa os dados que você vê nos correspondem lá as regras de escala você configurar? No exemplo abaixo, a CPU para máquina 20 iniciado aumentando a 100% sobre os últimos 5 minutos..

    ![Tabelas de armazenamento][tables]

    Se os dados não estiverem lá, ele indica que o problema é com a extensão de diagnóstico em execução no VMs. Se os dados estiverem lá, isso significa que há um problema com as regras de escala ou com o serviço de obtenção de informações. Verificar [Status Azure](https://azure.microsoft.com/status/).

    Depois que você esteve essas etapas, se você ainda estiver enfrentando problemas de escala automática você poderia tente os fóruns no [MSDN](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp)ou [empilhar excedente](http://stackoverflow.com/questions/tagged/azure)ou registrar uma chamada de suporte. Esteja preparado para compartilhar o modelo e um modo de exibição de dados de desempenho.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
