

Para aplicativos que precisa dimensionar recursos de computação e, em escala operações implicitamente são balanceadas entre domínios falhas e atualização. Para uma introdução a escala de máquina virtual conjuntos consultem o recente [comunicado de blog Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/).

Dê uma olhada estes vídeos para saber mais sobre conjuntos de escala de máquina virtual:

 - [Marcar Russinovich fala conjuntos de escala do Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  

 - [Conjuntos de escala de máquina virtual com cara Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>Criar e gerenciar escala de máquina virtual define

Escala de máquina virtual conjuntos podem ser definidos e implantado usando modelos JSON e [APIs REST](https://msdn.microsoft.com/library/mt589023.aspx) apenas como VMs de gerente de recursos individuais do Azure. Portanto, quaisquer métodos de implantação do Gerenciador de recursos do Azure padrão podem ser usados. Para obter mais informações sobre modelos, consulte [modelos de coautoria Gerenciador de recursos do Azure](../articles/resource-group-authoring-templates.md).

Um conjunto de modelos de exemplo para conjuntos de escala de máquina virtual pode ser encontrado no início rápido do Azure modelos GitHub repositório aqui:

[https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) - procure por modelos com _vmss_ no título.

As páginas de detalhes para esses modelos, você verá um botão que vincule ao recurso de implantação do portal. Para implantar o conjunto de escala de máquina virtual, clique no botão e preencha os parâmetros que são necessários no portal. Se você não tiver certeza se um recurso suporta superior ou maiusculas e minúsculas é mais seguro sempre usar valores de parâmetro de letras minúsculas. Também há um dissection prático de vídeo de um modelo de conjunto de escala de máquina virtual aqui:

[Dissection de modelo de conjunto de escala de máquina virtual](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>A escala de uma escala de máquina virtual definida in e check-out

Para aumentar ou diminuir o número de máquinas virtuais em um conjunto de escala de máquina virtual, simplesmente alterar a propriedade de _capacidade_ e reimplantar o modelo. Essa simplicidade torna mais fácil de escrever sua própria camada de dimensionamento personalizada se você deseja definir eventos de escala personalizada que não são suportados pelo escala Azure automática.

Se você estiver reimplantar um modelo para alterar a capacidade, você pode definir um muito modelo menor que inclui somente o SKU e a capacidade de atualização. Um exemplo disso é mostrado aqui: [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json).

Para percorrer as etapas para criar um conjunto de escala que é redimensionado automaticamente, consulte [Automaticamente máquinas de escala em um conjunto de escala de máquina Virtual](../articles/virtual-machines/virtual-machines-windows-vmss-powershell-creating.md)

## <a name="monitoring-your-vm-scale-set"></a>Monitoramento sua escala de máquina virtual definido

Recomenda-se no momento que você usar o [Gerenciador de recursos do Azure](https://resources.azure.com) para exibir conjuntos de escala de máquina virtual. Conjuntos de escala de máquina virtual são um recurso em Microsoft.Compute, portanto deste site, você pode vê-los expandindo os links a seguir:

    subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## <a name="vm-scale-set-scenarios"></a>Escala de máquina virtual definida cenários

Esta seção lista alguns cenários de conjunto de escala de máquina virtual típicos. Alguns serviços Azure níveis superior (como lote, serviço tecidos, serviço de contêiner do Azure) vai usar esses cenários.

 - **RDP / SSH como escala de máquina virtual de definir instâncias** - máquina virtual de um conjunto de escala é criado dentro de uma VNET e VMs individuais no não estão alocadas endereços IP públicos. Isso é bom porque você geralmente não quer a sobrecarga de despesas e o gerenciamento de alocação de endereços IP separados a todos os recursos sem estado em sua grade de computação, e você pode facilmente se conectar a essas VMs de outros recursos em sua VNET inclusive aqueles que têm endereços IP públicos como balanceadores de carga ou máquinas virtuais de autônomo.

 - **Conectar-se ao VMs usando as regras de NAT** - você pode criar um endereço IP público, atribua-o a um balanceador de carga e definir regras de NAT de entrada que mapeiam uma porta no endereço IP para uma porta em uma máquina virtual do conjunto de escala de máquina virtual. Por exemplo

    IP público -> porta 50000 -> vmss\_0 -> porta 22 público IP - > porta 50001 -> vmss\_1 -> porta 22 público IP - > porta 50002 -> vmss\_2 -> porta 22

    Aqui está um exemplo de criação de um conjunto de escala de máquina virtual que usa regras NAT para habilitar conexão SSH para cada máquina virtual em uma escala definida usando um único IP público: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

    Aqui está um exemplo de fazer o mesmo com RDP e Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

 - **Conectar-se ao VMs usando um "jumpbox"** - se você criar uma escala de máquina virtual definir e autônoma máquina virtual na mesma VNET, o standalone máquina virtual e o conjunto de escala de máquina virtual que VMs podem se conectar a outro usando seu IP interno endereços conforme definido pelo VNET/sub-rede. Se você criar um endereço IP público e atribuí-la a autônomo máquina virtual pode RDP ou SSH para o standalone máquina virtual e, em seguida, se conecte de máquina para suas instâncias de conjunto de escala de máquina virtual. Você pode perceber neste momento que um conjunto de escala de máquina virtual simples é naturalmente mais seguro que autônoma simple máquina virtual com um endereço IP público em sua configuração padrão.

    Para obter um exemplo dessa abordagem, este modelo cria um cluster Mesos simples consiste autônoma VM mestre que gerencia um cluster de máquina virtual com base em conjunto de escala de VMs: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

 - **Round robin balanceamento de carga de escala de máquina virtual definir instâncias** - se quiser enviar trabalho para um cluster de cálculo de VMs usando uma abordagem de "alternada", você pode configurar um balanceador de carga Azure com balanceamento de carga de regras adequadamente. Você também pode definir testes para verificar se o aplicativo é executado pelo ping portas com um caminho de protocolo, intervalo e solicitação especificado.

    Aqui está um exemplo que cria um conjunto de escala de máquina virtual de VMs executando o servidor web do IIS e usa um balanceador de carga equilibrando a carga que recebe de cada máquina virtual. Ele também usa o protocolo HTTP para ping uma URL específica em cada máquina virtual: [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) - examinar o tipo de recurso de Microsoft.Network/loadBalancers e networkProfile e extensionProfile na virtualMachineScaleSet.

 - **Implantando uma escala de máquina virtual definida como um cluster de cálculo em um gerente de cluster PaaS** - escala de máquina virtual conjuntos, às vezes, são descritos como uma função de trabalho de próxima geração. É uma descrição válida mas também funciona o risco de escala confuso recursos de conjunto com recursos de função PaaS v1 trabalhador. Em um sentido escala de máquina virtual conjuntos fornecem um verdadeiro "função de trabalho" ou recurso de trabalho, em que eles fornecem um recurso de computação generalizado que é plataforma/runtime independente, personalizável e integra Azure Gerenciador de recursos de IaaS.

    Uma função de trabalho de v1 PaaS, enquanto limitados em termos de suporte de plataforma de execução (somente imagens de plataforma Windows) também inclui serviços como trocar de VIP, configurações de atualização configuráveis, runtime/aplicativo implantação configurações específicas que não são _ainda_ disponíveis na máquina virtual dimensionar conjuntos ou serão entregues por outros serviços de PaaS de nível superiores como tecidos de serviço. Com isso em se que você pode examinar conjuntos de escala de máquina virtual como uma infraestrutura que suporta PaaS. Isto é Soluções PaaS como tecidos de serviço ou gerentes de cluster como Mesos pode construir na parte superior de máquina virtual dimensionar conjuntos como uma camada de computação scalable.

    Aqui está um exemplo de um cluster de Mesos que implante um conjunto de escala de máquina virtual na mesma VNET maneira autônoma máquina virtual. O standalone máquina virtual é um mestre de Mesos e o conjunto de escala de máquina virtual representa um conjunto de nós do auxiliar: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json). Versões futuras do [Serviço de contêiner do Azure](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) implantará mais complexo/protegida versões desse cenário com base em conjuntos de escala de máquina virtual.

## <a name="vm-scale-set-performance-and-scale-guidance"></a>Diretrizes de desempenho e dimensionamento de conjunto de escala do máquina virtual

- Durante a visualização pública, não crie mais de 500 VMs em vários conjuntos de escala de máquina virtual por vez.
- Planejar não mais de 40 VMs por conta de armazenamento.
- Espaçar as primeiras letras de nomes de conta de armazenamento tanto quanto possíveis.  Os modelos VMSS de exemplo em [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/) fornecem exemplos de como fazer isso.
- Se usando VMs personalizadas, planeje não mais de 40 VMs por conjunto de escala de máquina virtual, em uma conta de armazenamento único.  Você precisará a imagem previamente copiada para a conta de armazenamento antes de começar a implantação de conjunto de escala de máquina virtual. Consulte as perguntas Frequentes para obter mais informações.
- Planejar não mais do que 2048 VMs por VNET.  Esse limite será aumentado no futuro.
- O número de VMs que você pode criar é limitado pela sua cota de núcleo de computação em qualquer região. Você talvez precise contatar o suporte ao cliente para aumentar o limite de cota de computação aumentou mesmo que você tenha um limite de alto de cores para uso com serviços de nuvem ou IaaS v1 hoje. Para consultar sua cota, você pode executar o seguinte comando CLI Azure: _lista de máquina virtual azure - uso_e o seguinte comando do PowerShell: _Get-AzureRmVMUsage_ (se estiver usando uma versão do PowerShell abaixo 1.0 use _Get-AzureVMUsage_).

## <a name="vm-scale-set-frequently-asked-questions"></a>Escala de máquina virtual definir perguntas frequentes

**P.** VMs quantos você tem em um conjunto de escala de máquina virtual?

**R.** 100 se você usar imagens de plataforma que podem ser distribuídas em várias contas de armazenamento. Se você usar imagens personalizadas, até 40, desde que imagens personalizadas limitam-se a uma conta de armazenamento único durante a visualização.

**P** quais outros limites de recurso existem para conjuntos de escala de máquina virtual?

**R.** Você está limitado à criação de VMs não mais do que 500 em vários conjuntos de escala por região durante o período de visualização. O existente [limites de serviço de assinatura do Azure /](../articles/azure-subscription-service-limits.md) aplicar.

**P.** São discos de dados com suporte em conjuntos de escala de máquina virtual?

**R.** Não na versão inicial. As opções para armazenar dados são:

- Arquivos do Azure (unidades SMB compartilhado)

- Unidade do sistema operacional

- Unidade TEMP (local, não é feito por armazenamento do Azure)

- Serviço de dados externos (por exemplo, banco de dados remoto, tabelas do Azure, blobs Azure)

**P.** Quais são as regiões Azure oferece suporte para conjuntos de escala de máquina virtual?

**R.** Qualquer região que suporta o Gerenciador de recursos do Azure oferece suporte a conjuntos de escala de máquina virtual.

**P.** Como você criar uma escala de máquina virtual conjuntos usando uma imagem personalizada?

**R.** A propriedade vhdContainers deixe em branco, por exemplo:

    "storageProfile": {
        "osDisk": {
            "name": "vmssosdisk",
            "caching": "ReadOnly",
            "createOption": "FromImage",
            "image": {
                "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
            },
            "osType": "Windows"
        }
    },


**P.** Se eu reduzir minha escala de máquina virtual definir capacidade de 20 15, que VMs serão removidas?

**R.** Máquinas virtuais são removidas da escala de definir uniformemente em domínios de atualização e domínios de falha para maximizar a disponibilidade.

**P.** Quanto ele se eu depois aumentar a capacidade de 15 para 18?

**R.** Se você aumentar a 18, VMs com índice 15, 16, 17 será criado. Em ambos os casos VMs são balanceadas entre FDs e UDs.

**P.** Ao usar várias extensões em um conjunto de escala de máquina virtual, é possível impor uma sequência de execução?

**R.** Não diretamente, mas para a extensão de customScript, o script poderia esperar outra extensão concluir (por exemplo, monitorando a extensão de logon - consulte [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)).

**P.** Conjuntos de escala de máquina virtual funcionam com conjuntos de disponibilidade Azure?

**R.** Sim. Um conjunto de escala de máquina virtual é uma disponibilidade implícita definido com 3 FDs e 5 UDs. Não é necessário configurar nada em virtualMachineProfile. Em futuras versões, conjuntos de escala de máquina virtual serão prováveis que abranja vários locatários, mas agora definir uma escala é um único disponibilidade conjunto.
