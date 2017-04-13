<properties
   pageTitle="Configurar sempre no grupo de disponibilidade ouvintes – Microsoft Azure"
   description="Configure ouvintes de grupo de disponibilidade no modelo de Gerenciador de recursos do Azure, usando um balanceador de carga interno com um ou mais endereços IP."
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="10/20/2016"
   ms.author="MikeRayMSFT"/>

# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Configurar um ou mais sempre na disponibilidade grupo ouvintes - Gerenciador de recursos 

Este tópico mostra como fazer duas coisas:

- Crie um balanceador de carga interno para grupos de disponibilidade do SQL Server usando cmdlets do PowerShell.

- Adicione endereços IP adicionais para um balanceador de carga para dar suporte a mais de um grupo de disponibilidade do SQL Server. 

No SQL Server um ouvinte de grupo de disponibilidade é um nome de rede virtual que os clientes se conecta para acessar um banco de dados na réplica primário ou secundário. Nas máquinas virtuais Azure, um balanceador de carga mantém o endereço IP para o ouvinte. O balanceador de carga rotear o tráfego para a instância do SQL Server que é escutar na porta teste. Na maioria dos casos, um grupo de disponibilidade usa um balanceador de carga interno. Um balanceador de carga interno Azure pode hospedar um ou vários endereços IP. Cada endereço IP usa uma porta de teste específicos. Este documento mostra como usar o PowerShell para criar um novo balanceador de carga ou adicione endereços IP para um balanceador de carga existentes para os grupos de disponibilidade do SQL Server. 

A capacidade de atribuir vários endereços IP para um balanceador de carga interna é nova no Azure e só está disponível no modelo do Gerenciador de recursos. Para concluir essa tarefa, você precisa ter um grupo de disponibilidade do SQL Server implantado em máquinas virtuais Azure no modelo do Gerenciador de recursos. As duas máquinas virtuais de SQL Server deve pertencer ao mesmo conjunto de disponibilidade. Você pode usar o [modelo do Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para criar o grupo de disponibilidade automaticamente no Gerenciador de recursos do Azure. Este modelo cria automaticamente o grupo de disponibilidade, inclusive o balanceador de carga interno para você. Se preferir, você pode [Configurar manualmente um grupo de disponibilidade sempre ativado](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Este tópico requer que seus grupos de disponibilidade já estão configurados.  

Tópicos relacionados incluem:

- [Configurar grupos de disponibilidade sempre ativado no Azure máquina virtual (interface gráfica)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   

- [Configurar uma conexão de VNet para VNet usando o Gerenciador de recursos do Azure e PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Configurar o Firewall do Windows

Configure o Firewall do Windows para permitir acesso ao SQL Server. Você precisará configurar o firewall para permitir conexões de TCP com o uso de portas por instância do SQL Server, bem como a porta usada pelo teste de ouvinte. Para obter instruções detalhadas, consulte [Configurar um Firewall do Windows para o acesso do mecanismo de banco de dados](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Crie uma regra de entrada para porta do SQL Server e para a porta de teste.

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Script de exemplo: Criar um balanceador de carga interno com PowerShell

> [AZURE.NOTE] Se você criou seu grupo de disponibilidade com o [modelo do Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) a carga você não precisa concluir esta etapa. 

O seguinte script do PowerShell cria um balanceador de carga interno, configura a regras de balanceamento de carga e define um endereço IP para o balanceador de carga. Para executar o script, abra o Windows PowerShell ISE e cole o script no painel Script. Use `Login-AzureRMAccount` para fazer login no PowerShell. Se você tiver várias assinaturas Azure, use `Select-AzureRmSubscription ` para definir a assinatura. 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the Front End configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the Back End configuration

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a>Script de exemplo: adicionar um endereço IP para um balanceador de carga existente com o PowerShell

Para usar mais de um grupo de disponibilidade, use o PowerShell para adicionar um endereço IP adicional a um balanceador de carga existente. Cada endereço IP requer sua própria regra, teste e portas frontal de balanceamento de carga.

A porta de front-end é a porta que aplicativos usam para se conectar à instância do SQL Server. Endereços IP para grupos de disponibilidade diferentes podem usar a mesma porta de front-end.

>[AZURE.NOTE] Para grupos de disponibilidade do SQL Server, cada endereço IP exige uma porta de teste específicos. Por exemplo, se um endereço IP em um balanceador de carga usa a porta de teste 59999, sem outros endereços IP que balanceador de carga podem usar porta de teste 59999. 

- Para obter informações sobre balanceador de carga limites consulte **frente particular encerrar IP por balanceador de carga** em [Limites de rede - Gerenciador de recursos do Azure](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

- Para obter informações sobre a disponibilidade de limites de grupo consulte [restrições (disponibilidade grupos)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

O script a seguir adiciona um novo endereço IP para um balanceador de carga existente. Atualize as variáveis para seu ambiente. O ILB usa a porta ouvinte para a porta de front-end de balanceamento de carga. Essa porta pode ser a porta que o SQL Server está atendendo em. Para instâncias padrão do SQL Server, essa é a porta 1433. A regra para um grupo de disponibilidade balanceamento de carga requer um IP flutuante (servidor direta retorno) para que a porta de back-end é a mesma que a porta de front-end.

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
```



## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurar o cluster para usar o endereço IP de Balanceador de carga 

A próxima etapa é configurar o ouvinte no cluster e trazer o ouvinte online. Para fazer isso, faça o seguinte: 

1. Criar o ouvinte de grupo de disponibilidade no cluster failover  

2. Trazer o ouvinte on-line

## <a name="1-create-the-availability-group-listener-on-the-failover-cluster"></a>1. criar o ouvinte de grupo de disponibilidade no cluster failover

Nesta etapa, você adicionar um ponto de acesso do cliente ao cluster de failover com o Gerenciador de Cluster de Failover e, em seguida, usa o PowerShell para configurar o recurso de cluster para escutar a porta de teste. 

1. Use RDP para se conectar à máquina virtual Azure que hospeda a réplica principal. 

2. Abra o Gerenciador de Cluster de Failover.

3. Selecione o nó de **redes** e observe o nome de rede de cluster. Use esse nome no `$ClusterNetworkName` variável no script PowerShell.

4. Expanda o nome do cluster e, em seguida, clique em **funções**.

5. No painel de **funções** , clique com botão direito no nome do grupo de disponibilidade e selecione **Adicionar recurso** > **Ponto de acesso de cliente**.

6. Na caixa **nome** , crie um nome para este novo ouvinte, e em seguida, clique duas vezes em **Avançar** e, em seguida, clique em **Concluir**. Não reduza o ouvinte ou recurso online neste momento.
 
    O nome para o novo ouvinte é o nome de rede que aplicativos usará para se conectar aos bancos de dados no grupo de disponibilidade do SQL Server.

7. Clique na guia **recursos** , expanda o ponto de acesso de cliente que você acabou de criar. O recurso de IP de atalho e clique em Propriedades. Observe o nome do endereço IP. Você usará esse nome no `$IPResourceName` variável no script PowerShell.

8. Em **Endereço IP** clique em **Endereço IP estático** e definir o endereço IP estático para o mesmo endereço que você usou quando você define o endereço IP de Balanceador de carga no portal do Azure. 

9. Desativar o NetBIOS para este endereço e clique em **Okey**. Repita esta etapa para cada recurso IP se sua solução abrange várias VNets do Azure. 

10. Torne o recurso de grupo de disponibilidade do SQL Server dependentes no endereço IP. Botão direito do mouse no recurso no Gerenciador de cluster, isso é na guia **recursos** em **Outros recursos**. 

11. No nó de cluster que atualmente hospeda a réplica principal, abra um elevado PowerShell ISE e cole os seguintes comandos em um novo script. Na guia **dependências** , clique no nome do ouvinte.
 
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>

    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```
 
6. Atualize as variáveis e execute o script do PowerShell para configurar o endereço IP e porta para o novo ouvinte.

 >[AZURE.NOTE] Se seus servidores SQL em regiões separadas, você precisa executar o script do PowerShell duas vezes. Na primeira vez que use o nome de rede de cluster, nome do recurso IP de cluster e carregar o endereço IP de Balanceador do primeiro grupo de recursos. Na segunda vez use o nome de rede de cluster, nome do recurso IP de cluster e carregue o endereço IP de Balanceador do grupo de recursos segundo.

Agora o cluster tem um recurso de ouvinte do grupo de disponibilidade.

## <a name="2-bring-the-listener-online"></a>2. trazer o ouvinte on-line

Com o recurso de ouvinte de grupo disponibilidade configurado, você pode trazer o ouvinte online para que os aplicativos podem se conectar aos bancos de dados no grupo de disponibilidade com o ouvinte.

1. Navegar de volta ao Cluster Gerenciador de Failover. Expanda **funções** e realce seu grupo de disponibilidade. Na guia **recursos** , o nome do ouvinte de atalho e clique em **Propriedades**.

1. Clique na guia **dependências** . Se houver vários recursos listados, verifique se os endereços IP tem ou não e, dependências. Clique em **Okey**.

1. O nome do ouvinte de atalho e clique em **Colocar on-line**.

1. Depois que o ouvinte é online, na guia **recursos** , o grupo de disponibilidade de atalho e clique em **Propriedades**.

1. Crie uma dependência no recurso de nome de ouvinte (não o nome de recursos IP endereço). Clique em **Okey**.

1. Inicie o SQL Server Management Studio e se conectam a réplica primária.

1. Navegue até **AlwaysOn alta disponibilidade** | **grupos de disponibilidade** | **ouvintes de grupo de disponibilidade**. 

1. Agora você deve ver o nome do ouvinte que você criou no Gerenciador de Cluster de Failover. O nome do ouvinte de atalho e clique em **Propriedades**.

1. Na caixa **porta** , especifique o número da porta para o ouvinte de grupo de disponibilidade usando o $EndpointPort utilizado anteriormente (1433 era o padrão), clique em **Okey**.

Agora você tem um grupo de disponibilidade do SQL Server no Azure máquinas virtuais em execução no modo do Gerenciador de recursos. 

## <a name="test-the-connection-to-the-listener"></a>Testar a conexão para o ouvinte

Para testar a conexão:

1. RDP a um SQL Server que está na mesma rede virtual, mas não possui a réplica. Isso pode ser a outras do SQL Server no cluster.

2. Use o utilitário **sqlcmd** para testar a conexão. Por exemplo, o seguinte script estabelece uma conexão **sqlcmd** a réplica primária através do ouvinte com autenticação do Windows:

    ```
    sqlmd -S <listenerName> -E
    ```

    Se o ouvinte está usando uma porta diferente do padrão porta (1433), especifique a porta na cadeia de conexão. Por exemplo, o seguinte comando sqlcmd se conecta a um ouvinte na porta 1435: 
    
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

A conexão de SQLCMD se conectará automaticamente ao qualquer instância do SQL Server hospeda a réplica principal. 

>[AZURE.NOTE] Certifique-se de que a porta que você especificar é aberta no firewall de ambos os servidores de SQL. Ambos os servidores exigem uma regra de entrada para a porta TCP que você usar. Consulte [Adicionar ou editar regra de Firewall](http://technet.microsoft.com/library/cc753558.aspx) para obter mais informações. 

## <a name="guidelines-and-limitations"></a>Diretrizes e limitações

Observe as seguintes diretrizes no ouvinte de grupo de disponibilidade no Azure usando interno balanceador de carga:

- Com um balanceador de carga interno só você acessar o ouvinte de dentro da mesma rede virtual.

## <a name="for-more-information"></a>Para obter mais informações

Para obter mais informações, consulte [Configurar sempre disponibilidade do grupo na máquina virtual do Azure manualmente](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

### <a name="powershell-cmdlets"></a>Cmdlets do PowerShell

Use os seguintes cmdlets do PowerShell para criar um balanceador de carga interno para Azure máquinas virtuais.

- `New-AzureRmLoadBalancer`cria um balanceador de carga. Consulte [New-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) para obter mais informações. 

- `New-AzureRMLoadBalancerFrontendIpConfig`cria uma configuração de IP front-end para um balanceador de carga. Consulte [New-AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) para obter mais informações.

- `New-AzureRmLoadBalancerRuleConfig`cria uma configuração de regra para um balanceador de carga. Consulte [New-AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) para obter mais informações. 

- `New-AzureRMLoadBalancerBackendAddressPoolConfig`cria uma configuração de pool de endereço de back-end para um balanceador de carga. Consulte [New-AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) para obter mais informações. 

- `New-AzureRmLoadBalancerProbeConfig`cria uma configuração de teste para um balanceador de carga. Consulte [New-AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) para obter mais informações.

Se você precisar remover um balanceador de carga de um grupo de recursos Azure, use `Remove-AzureRmLoadBalancer`. Para obter mais informações, consulte [Remover AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx).
