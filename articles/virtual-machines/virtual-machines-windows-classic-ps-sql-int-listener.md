<properties
    pageTitle="Configurar um ouvinte ILB para sempre nos grupos de disponibilidade | Microsoft Azure"
    description="Este tutorial usa recursos criados com o modelo clássico de implantação e cria um sempre na disponibilidade grupo ouvinte no Azure usando um balanceador de carga interno (ILB)."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Configurar um ouvinte de ILB para sempre em grupos de disponibilidade no Azure

> [AZURE.SELECTOR]
- [Ouvinte interno](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Ouvinte externo](virtual-machines-windows-classic-ps-sql-ext-listener.md)

## <a name="overview"></a>Visão geral

Este tópico mostra como configurar um ouvinte para um sempre no grupo de disponibilidade usando um **Balanceador de carga interno (ILB)**.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para configurar um ouvinte de ILB para um grupo de disponibilidade sempre no modelo do Gerenciador de recursos, consulte [Configurar um balanceador de carga interna para um grupo de disponibilidade sempre no Azure](virtual-machines-windows-portal-sql-alwayson-int-listener.md).


O grupo de disponibilidade podem conter réplicas que são locais somente Azure apenas, ou abranja locais e Azure configurações híbridas. Azure réplicas podem residem dentro da mesma região ou entre várias regiões usando várias redes virtuais (VNets). As etapas a seguir presumem que você já tiver [configurado um grupo de disponibilidade](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md) , mas não configurou um ouvinte.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Diretrizes e limitações para ouvintes internos
Observe as seguintes diretrizes sobre o ouvinte de grupo de disponibilidade no Azure usando ILB:

- O ouvinte de grupo de disponibilidade é suportado no Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.

- Somente um ouvinte de grupo de disponibilidade interna é suportado por serviço em nuvem, pois o ouvinte é configurado para o ILB e há apenas uma ILB por serviço de nuvem. No entanto, é possível criar várias escutas externas. Para obter mais informações, consulte [Configurar um ouvinte externo para sempre em grupos de disponibilidade no Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).

- Não há suporte para criar um ouvinte interno no serviço de nuvem mesmo onde você também tiver um ouvinte externo usando VIP público do serviço na nuvem.

## <a name="determine-the-accessibility-of-the-listener"></a>Determinar a acessibilidade do ouvinte

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo se concentra em criar um ouvinte que usa um **Balanceador de carga interno (ILB)**. Se precisar de um ouvinte público/externa, consulte a versão deste artigo que fornece etapas para configurar um [ouvinte externo](virtual-machines-windows-classic-ps-sql-ext-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Crie pontos de extremidade de balanceamento de carga máquina virtual com o servidor direta retorno

Para ILB, você deve primeiro criar balanceador de carga interno. Isso é feito no script abaixo.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Para **ILB**, você deve atribuir um endereço IP estático. Primeiro, examine a configuração atual do VNet executando o seguinte comando:

        (Get-AzureVNetConfig).XMLConfiguration

1. Observe o nome de **sub-rede** para a sub-rede que contém as VMs que hospedam réplicas. Isso será usado no parâmetro **$SubnetName** no script.

1. Observe o nome de **VirtualNetworkSite** e o **AddressPrefix** inicial para a sub-rede que contém as VMs que hospedam réplicas. Procure por um endereço IP disponível passando os dois valores para o comando **AzureStaticVNetIP de teste** e examinando o **AvailableAddresses**. Por exemplo, se o VNet foi chamada *MyVNet* e tinha um endereço de sub-rede intervalo iniciado em *172.16.0.128*, o comando a seguir lista endereços disponíveis:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses

1. Escolha um dos endereços disponíveis e usá-lo no parâmetro **$ILBStaticIP** do script a seguir.

3. Copie o script PowerShell abaixo para um editor de texto e defina os valores de variáveis de acordo com seu ambiente (Observe que os padrões foram fornecidos para alguns parâmetros). Observe que as implantações existentes que usar grupos de afinidade não é possível adicionar ILB. Para obter mais informações sobre os requisitos de ILB, consulte [Interno balanceador de carga](../load-balancer/load-balancer-internal-overview.md). Além disso, se seu grupo de disponibilidade ocupar regiões Azure, você deve executar o script uma vez em cada data center para o serviço de nuvem e nós que residem em que data center.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load balanced endpoint for each node in $AGNodes using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

1. Depois que você tiver definido as variáveis, copie o script do editor de texto em sua sessão do PowerShell do Azure para executá-la. Se o prompt ainda mostra >>, digite ENTER novamente para certificar-se de que o script começa a ser executado. Observação

>[AZURE.NOTE] O portal de clássico Azure não suporta balanceador de carga interno no momento, você não verá o ILB ou os pontos de extremidade no portal de clássico do Azure. No entanto, **Get-AzureEndpoint** retorna um endereço IP interno se balanceador de carga está sendo executado nele. Caso contrário, retorna null.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Verificar se KB2854082 está instalado, se necessário

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abra as portas do firewall em nós de grupo de disponibilidade

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Criar o ouvinte de grupo de disponibilidade

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Para ILB, você deve usar o endereço IP do interno carga balanceador (ILB) criado anteriormente. Use o script a seguir para obter este endereço IP no PowerShell.

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

1. Em uma das VMs, copie o script do PowerShell para seu sistema operacional para um editor de texto e defina as variáveis para os valores que você anotou anteriormente.

    Para o Windows Server 2012 ou superior, use o seguinte script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
        
    Para o Windows Server 2008 R2, use o seguinte script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255
    

1. Uma vez que você definiu as variáveis, abra uma janela do Windows PowerShell elevada, e em seguida, copie o script a partir do editor de texto e cole sua sessão do PowerShell do Azure para executá-la. Se o prompt ainda mostra >>, digite ENTER novamente para certificar-se de que o script começa a ser executado.

2. Repita esse procedimento em cada máquina virtual. Esse script configura o recurso de endereço IP com o endereço IP do serviço de nuvem e define outros parâmetros como a porta de teste. Quando o recurso de endereço IP é colocado online, ele poderá então responder para a sondagem na porta de teste do ponto de extremidade de balanceamento de carga criado anteriormente neste tutorial.

## <a name="bring-the-listener-online"></a>Trazer o ouvinte on-line

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Itens de acompanhamento

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testar o ouvinte de grupo de disponibilidade (dentro do mesmo VNet)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]
