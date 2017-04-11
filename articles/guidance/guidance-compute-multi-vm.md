<properties
   pageTitle="Executando várias VMs | Arquitetura de referência | Microsoft Azure"
   description="Como executar várias instâncias de máquina virtual no Azure para escalabilidade, resiliência, capacidade de gerenciamento e segurança."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/19/2016"
   ms.author="mwasson"/>

# <a name="running-multiple-vms-on-azure-for-scalability-and-availability"></a>Várias VMs em execução no Azure para escalabilidade e disponibilidade 

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve um conjunto de práticas comprovadas para executar várias instâncias de máquinas virtuais (VM) para melhorar a escalabilidade, disponibilidade, capacidade de gerenciamento e segurança.   

Nesta arquitetura, a carga de trabalho é distribuída entre as instâncias de máquina virtual. Há um único endereço IP público e o tráfego de Internet é distribuído para VMs usando um balanceador de carga. Essa arquitetura pode ser usada para um aplicativo de camada única, como um cluster de armazenamento ou aplicativo web sem estado. Também é um bloco de construção para aplicativos de N camadas. 

Este artigo se baseia [executando uma única VM no Azure][single vm]. As recomendações nesse artigo também se aplicam a essa arquitetura.

## <a name="architecture-diagram"></a>Diagrama de arquitetura

VMs no Azure exigem recursos de rede e armazenamento de suporte.

> Um documento do Visio que inclui este diagrama de arquitetura está disponível para download no [Centro de download da Microsoft][visio-download]. Este diagrama é sobre a "computação - guia de máquina virtual multi." 

![[0]][0]

A arquitetura tem os seguintes componentes: 

- **Conjunto de disponibilidade.** A [disponibilidade definida] [ availability set] contém as VMs e é necessário para oferecer suporte a [disponibilidade de SLA para VMs Azure][vm-sla].

- **VNet**. Cada máquina virtual no Azure é implantado em uma rede virtual (VNet) que está dividida em **sub-redes**.

- **Balanceador de carga Azure.** O [balanceador de carga] distribui solicitações de entrada de Internet para as instâncias de máquina virtual em um conjunto de disponibilidade. O balanceador de carga inclui alguns recursos relacionados:

  - **Endereço IP público.** Um endereço IP público é necessária para o balanceador de carga receber o tráfego de Internet.

  - **Configuração de front-end.** Associa o endereço IP público balanceador de carga.

  - **Pool de endereços de back-end.** Contém as interfaces de rede (NICs) para VMs que vai receber o tráfego de entrada.

- **Regras de Balanceador de carga.** Usado para distribuir o tráfego de rede entre todas as VMs no pool de endereços de back-end. 

- **Regras NAT.** Usado para rotear o tráfego para uma máquina virtual específica. Por exemplo, para habilitar o protocolo da área de trabalho remota (RDP) para VMs, crie uma regra de conversão (NAT) de endereço de rede separada para cada máquina virtual. 

- **Interfaces de rede (NICs)**. Cada VM tem uma NIC para se conectar à rede.

- **Armazenamento.** Contas de armazenamento mantêm as imagens de máquina virtual e outros recursos relacionados a arquivos, como dados de diagnóstico de máquina virtual capturados pelo Azure.

## <a name="recommendations"></a>Recomendações

Azure oferece muitos recursos diferentes e tipos de recurso, portanto, essa arquitetura de referência pode ser provisionado muitas maneiras diferentes. Fornecemos um modelo do Gerenciador de recursos do Azure para instalar a arquitetura de referência que segue as recomendações descritas abaixo. Se você optar por criar sua própria arquitetura de referência que você deve seguir essas recomendações, a menos que tenha um requisito específico que não oferece suporte para uma recomendação. 

### <a name="availability-set-recommendations"></a>Disponibilidade definida recomendações

Você deve criar pelo menos duas VMs na disponibilidade definida para dar suporte a [disponibilidade de SLA para VMs Azure][vm-sla]. Observe que o balanceador de carga Azure também requer que VMs balanceamento de carga pertencem o mesmo conjunto de disponibilidade.

### <a name="network-recommendations"></a>Recomendações de rede

Todas as VMs atrás balanceador de carga devem ser colocadas dentro da mesma sub-rede. Não expõem as VMs diretamente à Internet, mas em vez disso, dê a cada máquina virtual um endereço IP particular. Os clientes se conectam usando o endereço IP público de Balanceador de carga.

### <a name="load-balancer-recommendations"></a>Recomendações de Balanceador de carga

Adicione todas as VMs na disponibilidade definida para o pool de endereços de back-end do balanceador de carga.

Defina regras de Balanceador de carga para tráfego de rede direto para as VMs. Por exemplo, para permitir o tráfego HTTP, crie uma regra que mapeie porta 80 da configuração de front-end à porta 80 no pool de endereço de back-end. Quando o balanceador de carga recebe uma solicitação na porta 80 do endereço IP público, ele encaminhará a solicitação para a porta 80 em uma das NICs no pool de endereços de back-end.

Defina regras NAT para rotear o tráfego para uma máquina virtual específica. Por exemplo, para habilitar RDP às VMs criar uma regra NAT separada para cada máquina virtual. Cada regra deve mapear um número de porta distintas à 3389, a porta padrão para RDP. (Por exemplo, utilizam a porta 50001 para "VM1," porta 50002 para "Da VM2," e assim por diante). Atribua as regras NAT a NICs nas VMs. 

### <a name="storage-account-recommendations"></a>Recomendações de conta de armazenamento

Criar contas de armazenamento do Azure separada para cada máquina virtual acomodar os rígido VHDs (discos virtuais), para evitar atingir as operações de entrada/saída por segundo [(IOPS) limites] [ vm-disk-limits] para contas de armazenamento. 

Crie uma conta de armazenamento para os logs de diagnóstico. Esta conta de armazenamento pode ser compartilhada por todas as VMs.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

Há duas opções para dimensionar VMs no Azure: 

- Use um balanceador de carga para distribuir o tráfego de rede em um conjunto de VMs. Para dimensionar, provisionar VMs adicionais e colocá-los por trás de Balanceador de carga. 

- Usar [conjuntos de escala de máquina Virtual][vmss]. Um conjunto de escala contém um número de especificar de VMs idênticos atrás de um balanceador de carga. Escala de máquina virtual define autoscaling de suporte, com base em métricas de desempenho. À medida que aumenta a carga VMs, VMs adicionais são adicionadas automaticamente ao balanceador de carga. 

As próximas seções comparam essas duas opções.

### <a name="load-balancer-without-vm-scale-sets"></a>Balanceador de carga sem conjuntos de escala de máquina virtual

Um balanceador de carga leva solicitações de entrada de rede e distribui-las entre as NICs no pool de endereços de back-end. Para dimensionar horizontalmente, adicionar mais ocorrências de máquina virtual para o conjunto de disponibilidade (ou desalocar VMs dimensionar para baixo). 

Por exemplo, suponha que você está executando um servidor web. Você adiciona uma regra de Balanceador de carga para porta 80 e/ou porta 443 (para SSL). Quando um cliente envia uma solicitação HTTP, balanceador de carga seleciona um endereço IP de back-end usando um [algoritmo de hash] [ load balancer hashing] que inclua o endereço IP de origem. Dessa maneira, solicitações de cliente são distribuídas por todas as VMs. 

> [AZURE.TIP] Quando você adiciona uma nova VM a disponibilidade de uma conjunto, verifique se criar uma NIC para a máquina virtual e adicionar a NIC ao pool de endereços de back-end no balanceador de carga. Caso contrário, o tráfego de Internet não ser roteado para a máquina virtual novo.

Cada assinatura Azure tem limites padrão no lugar, incluindo um número máximo de VMs por região. Você pode aumentar o limite preenchendo uma solicitação de suporte. Para obter mais informações, consulte [assinatura do Azure e limites de serviço, cotas e restrições][subscription-limits].  

### <a name="vm-scale-sets"></a>Conjuntos de escala de máquina virtual 

Conjuntos de escala de máquina virtual ajudam você a implantar e gerenciar um conjunto de VMs idênticos. Todas as VMs configurada da mesma, conjuntos de escala de máquina virtual dá suporte a escala true automática, sem previamente provisionamento VMs, tornando mais fácil criar serviços grande escala direcionamento computação grande, dados grandes e cargas de trabalho contida em recipiente. 

Para obter mais informações sobre conjuntos de escala de máquina virtual, consulte [Visão geral sobre conjuntos de escala Máquina Virtual][vmss].

Considerações para usar conjuntos de escala de máquina virtual:

- Considere a possibilidade de conjuntos de escala se você precisar rapidamente dimensionar VMs ou precisa escala automática. 

- Atualmente, conjuntos de escala não suportam discos de dados. As opções para armazenar dados estão armazenamento de arquivos Azure, na unidade do sistema operacional, unidade Temp ou um armazenamento externo, como o armazenamento do Azure. 

- Todas as instâncias de máquina virtual dentro de uma escala definido automaticamente pertencem ao mesmo conjunto de disponibilidade, com domínios de falhas de 5 e 5 atualização.

- Por padrão, o conjuntos de escala usam "excesso de provisionamento", o que significa que o conjunto de escala inicialmente provisiona VMs mais do que você solicita, em seguida, exclui as VMs extras. Isso melhora a taxa de sucesso geral ao provisionar VMs. 

- Recomendamos não mais depois que 20 VMs por armazenamento conta com excesso de provisionamento VMs habilitadas ou não mais de 40 com excesso de provisionamento desabilitado.  

- Você pode encontrar modelos do Gerenciador de recursos para implantação escala define nos [Modelos de início rápido do Azure][vmss-quickstart].

- Há duas maneiras básicas para configurar VMs implantadas em um conjunto de escala: criar uma imagem personalizada ou usar extensões para configurar a máquina virtual depois que ela é provisionada.

    - Um conjunto de escala criado em uma imagem personalizada deve criar todos os VHDs de disco do sistema operacional dentro de uma conta de armazenamento. 

    - Com imagens personalizadas, você precisa manter a imagem atualizados.

    - Com as extensões, pode levar mais tempo para uma máquina virtual recentemente provisionada girar.

Para considerações adicionais, consulte [Criando máquina virtual escala conjuntos de escala][vmss-design].

> [AZURE.TIP]  Ao usar qualquer solução dimensionar automaticamente, testá-lo com cargas de trabalho de nível de produção bem com antecedência. 

## <a name="availability-considerations"></a>Considerações de disponibilidade

A disponibilidade conjunto faz com que seu aplicativo mais flexível para planejadas e eventos de manutenção.

- _Manutenção planejada_ ocorre quando o Microsoft atualiza a plataforma subjacente, às vezes, causando VMs ser reiniciado. Azure torna-se de que as VMs em um conjunto de disponibilidade não são todos reiniciado ao mesmo tempo, pelo menos uma é mantida em execução enquanto outras pessoas estiver reiniciando.

- _Manutenção planejada_ acontece se houver uma falha de hardware. Azure garante que VMs em um conjunto de disponibilidade são provisionadas em mais de um rack de servidor. Isso ajuda a reduzir o impacto de falhas de hardware, interrupções de rede, interrupções de energia e assim por diante.

Para obter mais informações, consulte [Gerenciar a disponibilidade de máquinas virtuais][availability set]. O vídeo a seguir também tem uma boa visão geral dos conjuntos de disponibilidade: [Como eu posso configurar um conjunto de disponibilidade para VMs de escala][availability set ch9]. 

> [AZURE.WARNING]  Certifique-se de configurar a disponibilidade definida quando você provisionar a máquina virtual. Atualmente, não há nenhuma maneira de adicionar uma VM Gerenciador de recursos a uma disponibilidade definida após a máquina virtual está provisionada.

O balanceador de carga usa [testes de integridade] para monitorar a disponibilidade de instâncias de máquina virtual. Se um teste não puder alcançar uma instância dentro de um período de tempo limite, o balanceador de carga interrompe o envio de tráfego para essa máquina virtual. No entanto, o balanceador de carga continuarão a teste e se a máquina virtual ficar disponível novamente, o balanceador de carga currículos enviando o tráfego para essa máquina virtual.

Aqui estão algumas recomendações sobre testes de integridade de Balanceador de carga:

- Testes podem testar HTTP ou TCP. Se executar VMs um servidor HTTP (IIS, nginx, Node aplicativo e assim por diante), crie um teste HTTP. Caso contrário, crie um teste TCP.

- Para um teste HTTP, especifique o caminho para o ponto de extremidade HTTP. O teste verifica se há uma resposta HTTP 200 deste caminho. Isso pode ser o caminho raiz ("/") ou um ponto de extremidade monitoramento de integridade que implementa algumas lógica personalizada para verificar a integridade do aplicativo. O ponto de extremidade deve permitir anônimas solicitações HTTP.

- O teste é enviado de um [conhecido] [ health-probe-ip] endereço IP, 168.63.129.16. Verifique se que você não bloquear o tráfego de ou para este IP em qualquer diretivas de firewall ou regras de grupo (NSG) de segurança de rede.

- Usar [logs de teste de integridade] [ health probe log] para exibir o status dos testes integridade. Habilite registro em log no portal do Azure para cada balanceador de carga. Logs são gravados ao armazenamento de Blob do Azure. Os logs mostram quantas VMs no back-end não está recebendo o tráfego de rede devido a respostas de falha de teste.

## <a name="manageability-considerations"></a>Considerações de capacidade de gerenciamento

Com várias VMs, é importante automatizar processos, para que eles são confiáveis e repetitivo. Você pode usar a [Automação do Azure] [ azure-automation] para automatizar a implantação, patches de sistema operacional e outras tarefas. Automação Azure é um serviço de automação que é executado no Azure e baseia-se no Windows PowerShell. Scripts de automação de exemplo estão disponíveis na [Galeria de Runbook] da TechNet.

## <a name="security-considerations"></a>Considerações de segurança

Redes virtuais são um limite de isolamento de tráfego no Azure. VMs em um que vnet não conseguir se comunicar diretamente às VMs em um VNet diferente. VMs dentro do mesmo VNet possam se comunicar, a menos que você crie [grupos de segurança de rede] [ nsg] (NSGs) para restringir o tráfego. Para obter mais informações, consulte [Serviços de nuvem da Microsoft e segurança da rede][network-security].

Para tráfego da Internet, as regras de Balanceador de carga definem qual tráfego pode alcançar back-end. No entanto, as regras de Balanceador de carga não oferecem suporte a lista de exceções IP, isso se quiser endereços de IP específico público branca, adicionar um NSG à sub-rede.

## <a name="solution-deployment"></a>Implantação da solução

Uma implantação para uma arquitetura de referência que implementa essas recomendações está disponível no GitHub. Essa arquitetura de referência inclui uma rede virtual (VNet), o grupo de segurança de rede (NSG), balanceador de carga e duas máquinas virtuais (VMs).

A arquitetura de referência pode ser implantada com Windows ou Linux VMs seguindo as instruções abaixo: 

1. Clique com botão direito no botão abaixo e selecione um dos "Abrir link em uma nova guia" ou "Abrir link em nova janela":  
[![Implantar para o Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-multi-vm%2Fazuredeploy.json)

2. Depois que o link for aberto no portal do Azure, você deve inserir valores para algumas das configurações: 
    - O nome do **grupo de recursos** já está definido no arquivo de parâmetros, portanto selecione **Usar existente** e insira `ra-multi-vm-rg` na caixa de texto.
    - Selecione a região na caixa suspensa **local** .
    - Não edite o **Modelo raiz Uri** ou as caixas de texto de **Uri de raiz de parâmetro** .
    - Selecione o **Tipo de sistema operacional** na lista suspensa de caixa, **windows** ou **linux**. 
    - Examine os termos e condições, clique na caixa de seleção **para concordar com os termos e condições mencionadas acima** .
    - Clique no botão de **compra** .

3. Aguarde a implantação ser concluída.

4. Arquivos de parâmetro incluem um nome de usuário de administrador embutidos e a senha, e é altamente recomendável que você altere imediatamente ambos. Clique na máquina virtual chamada `ra-multi-vm1` no portal do Azure. Em seguida, clique em **Redefinir senha** na lâmina **suporte + solução de problemas** . Selecione **Redefinir a senha** na caixa de lista suspensa **modo** e, em seguida, selecione um novo **nome de usuário** e **senha**. Clique no botão de **atualização** para salvar o novo nome de usuário e senha. Repita para a máquina virtual chamada `ra-multi-vm2`.

Para obter informações sobre outras maneiras para implantar essa arquitetura de referência, consulte o arquivo Leiame na [orientação-único-máquina virtual] [ github-folder] GitHub pasta. 

## <a name="next-steps"></a>Próximas etapas

Colocar várias VMs atrás de um balanceador de carga é um bloco de construção para a criação de arquiteturas de vários níveis. Para obter mais informações, consulte [Executando o Windows VMs para uma arquitetura de N camadas no Azure] [ n-tier-windows] e [Executando Linux VMs para uma arquitetura de N camadas no Azure][n-tier-linux]

<!-- Links -->
[availability set]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[availability set ch9]: https://channel9.msdn.com/Series/Microsoft-Azure-Fundamentals-Virtual-Machines/08
[azure-automation]: https://azure.microsoft.com/en-us/documentation/services/automation/
[azure-cli]: ../virtual-machines-command-line-tools.md
[bastion host]: https://en.wikipedia.org/wiki/Bastion_host
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-multi-vm
[health probe log]: ../load-balancer/load-balancer-monitor-log.md
[testes de integridade]: ../load-balancer/load-balancer-overview.md#service-monitoring
[health-probe-ip]: ../virtual-network/virtual-networks-nsg.md#special-rules
[Balanceador de carga]: ../load-balancer/load-balancer-get-started-internet-arm-cli.md
[load balancer hashing]: ../load-balancer/load-balancer-overview.md#hash-based-distribution
[n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[n-tier-windows]: guidance-compute-n-tier-vm.md
[naming conventions]: guidance-naming-conventions.md
[network-security]: ../best-practices-network-security.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md 
[Galeria de runbook]: ../automation/automation-runbook-gallery.md#runbooks-in-runbook-gallery
[single vm]: guidance-compute-single-vm.md
[subscription-limits]: ../azure-subscription-service-limits.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_2/
[vmss]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md
[vmss-design]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md
[vmss-quickstart]: https://azure.microsoft.com/documentation/templates/?term=scale+set
[VM-sizes]: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
[0]: ./media/blueprints/compute-multi-vm.png "Arquitetura de uma solução de multi-máquina virtual no Azure que compõem uma disponibilidade definida com duas VMs e um balanceador de carga"
