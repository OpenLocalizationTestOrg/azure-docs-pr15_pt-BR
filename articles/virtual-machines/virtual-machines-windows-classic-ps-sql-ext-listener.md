<properties
    pageTitle="Configurar um ouvinte externo para sempre em grupos de disponibilidade | Microsoft Azure"
    description="Este tutorial orienta você pelas etapas de criação de um sempre na disponibilidade grupo ouvinte no Azure que é acessível externamente usando o endereço IP Virtual público do serviço de nuvem associado."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/12/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Configurar um ouvinte externo para sempre em grupos de disponibilidade no Azure

> [AZURE.SELECTOR]
- [Ouvinte interno](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Ouvinte externo](virtual-machines-windows-classic-ps-sql-ext-listener.md)

Este tópico mostra como configurar um ouvinte para um sempre no grupo de disponibilidade externamente acessível na internet. Isso se tornou possível associando endereço de **IP público Virtual (VIP)** do serviço na nuvem com o ouvinte.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


O grupo de disponibilidade podem conter réplicas que são locais somente Azure apenas, ou abranja locais e Azure configurações híbridas. Azure réplicas podem residem dentro da mesma região ou entre várias regiões usando várias redes virtuais (VNets). As etapas a seguir presumem que você já tiver [configurado um grupo de disponibilidade](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md) , mas não configurou um ouvinte.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Diretrizes e limitações para escutas externas

Observe as seguintes diretrizes sobre o ouvinte de grupo de disponibilidade no Azure quando você estiver implantando usando o endereço VIP público do serviço de nuvem:

- O ouvinte de grupo de disponibilidade é suportado no Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.

- O aplicativo cliente deve residem em um serviço de nuvem diferente daquela que contém o grupo de disponibilidade VMs. Azure não dá suporte a retorno de servidor direta com o cliente e servidor no serviço de nuvem do mesmo.

- Por padrão, as etapas neste artigo mostram como configurar um ouvinte para usar o endereço de IP Virtual (VIP) do serviço de nuvem. No entanto, é possível reservar e criar vários endereços de VIP para seu serviço de nuvem. Isso permite que você use as etapas neste artigo para criar vários ouvintes cada associados um VIP diferentes. Para obter informações sobre como criar vários endereços de VIP, consulte [VIPs múltiplos por serviço de nuvem](../load-balancer/load-balancer-multivip.md).

- Se você estiver criando um ouvinte para um ambiente híbrido, a rede local deve ter conectividade com a Internet pública além da VPN to-site com a rede virtual Azure. Quando na sub-rede Azure, o ouvinte de grupo de disponibilidade é acessível somente pelo endereço IP público do serviço de nuvem respectivos.

- Não há suporte para criar um ouvinte externo no serviço de nuvem mesmo onde você também tiver um ouvinte interno usando o balanceador de carga interno (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Determinar a acessibilidade do ouvinte

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo se concentra em criar um ouvinte que usa **balanceamento de carga de externa**. Se você quiser um ouvinte que é sua rede virtual privado, consulte a versão deste artigo que fornece etapas para configurar um [ouvinte com ILB](virtual-machines-windows-classic-ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Crie pontos de extremidade de balanceamento de carga máquina virtual com o servidor direta retorno

Balanceamento de carga externa usa o virtual o endereço IP Virtual público do serviço na nuvem que hospeda seu máquinas virtuais. Assim você não precisa criar ou configurar o balanceador de carga nesse caso.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Copie o script PowerShell abaixo para um editor de texto e defina os valores de variáveis de acordo com seu ambiente (padrões foi fornecidos para alguns parâmetros). Observe que se seu grupo de disponibilidade ocupar regiões Azure, você deve executar o script uma vez em cada data center para o serviço de nuvem e nós que residem em que data center.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas

        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

1. Depois que você tiver definido as variáveis, copie o script do editor de texto em sua sessão do PowerShell do Azure para executá-la. Se o prompt ainda mostra >>, digite ENTER novamente para certificar-se de que o script começa a ser executado.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Verificar se KB2854082 está instalado, se necessário

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abra as portas do firewall em nós de grupo de disponibilidade

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Criar o ouvinte de grupo de disponibilidade

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Para balanceamento de carga externa, você deve obter o endereço IP virtual público do serviço na nuvem que contém suas réplicas. Faça logon no portal de clássico do Azure. Navegue até o serviço de nuvem que contém o grupo de disponibilidade máquina virtual. Abra o **painel** de exibição.

3. Observe o endereço mostrado em **endereço IP público Virtual (VIP)**. Se sua solução abrange VNets, repita essa etapa para cada serviço de nuvem que contém uma máquina virtual que hospeda uma réplica.

4. Em uma das VMs, copie o script PowerShell abaixo para um editor de texto e defina as variáveis para os valores que você anotou anteriormente.

        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service

        Import-Module FailoverClusters

        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.

        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255


1. Uma vez que você definiu as variáveis, abra uma janela do Windows PowerShell elevada, e em seguida, copie o script a partir do editor de texto e cole sua sessão do PowerShell do Azure para executá-la. Se o prompt ainda mostra >>, digite ENTER novamente para certificar-se de que o script começa a ser executado.

1. Repita esse procedimento em cada máquina virtual. Esse script configura o recurso de endereço IP com o endereço IP do serviço de nuvem e define outros parâmetros como a porta de teste. Quando o recurso de endereço IP é colocado online, ele poderá então responder para a sondagem na porta de teste do ponto de extremidade de balanceamento de carga criado anteriormente neste tutorial.

## <a name="bring-the-listener-online"></a>Trazer o ouvinte on-line

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Itens de acompanhamento

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testar o ouvinte de grupo de disponibilidade (dentro do mesmo VNet)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testar o ouvinte de grupo de disponibilidade (pela internet)

Para acessar o ouvinte de fora da rede virtual, você deve estar usando balanceamento de carga de externo/público (descrito neste tópico) em vez de ILB, que é acessível apenas por dentro do mesmo VNet. Na cadeia de conexão, você especificar o nome do serviço de nuvem. Por exemplo, se você tivesse um serviço na nuvem com o nome *mycloudservice*, a instrução sqlcmd seria assim:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Diferentemente do exemplo anterior, a autenticação do SQL deve ser usada, porque o chamador não pode usar a autenticação do windows pela internet. Para obter mais informações, consulte [sempre no grupo de disponibilidade na máquina virtual do Azure: cenários de conectividade de cliente](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Ao usar a autenticação do SQL, certifique-se de que você criar o mesmo logon em ambas as réplicas. Para obter mais informações sobre como solucionar problemas de logon com grupos de disponibilidade, consulte [como mapear logon ou usar contidos usuário de banco de dados SQL para se conectar a outras réplicas e mapear aos bancos de dados de disponibilidade](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Se as réplicas sempre estão em sub-redes diferentes, clientes especifique **MultisubnetFailover = True** na cadeia de conexão. Isso resulta em tentativas de conexão paralela para réplicas as sub-redes diferentes. Observe que este cenário inclui uma implantação de sempre no grupo de disponibilidade entre região.

## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]
