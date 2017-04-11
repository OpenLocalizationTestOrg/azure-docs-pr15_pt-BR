<properties
    pageTitle="Criar um opostas balanceador de carga com o IPv6 usando o PowerShell para o Gerenciador de recursos de Internet | Microsoft Azure"
    description="Aprenda a criar um opostas balanceador de carga com o IPv6 usando o PowerShell para o Gerenciador de recursos de Internet"
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    tags="azure-resource-manager"
    keywords="IPv6, balanceador de carga azure, pilha dupla, ip público, ipv6 nativo, mobile, iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>Começar a criar um opostas balanceador de carga com o IPv6 usando o PowerShell para o Gerenciador de recursos de Internet

> [AZURE.SELECTOR]
- [PowerShell](./load-balancer-ipv6-internet-ps.md)
- [CLI Azure](./load-balancer-ipv6-internet-cli.md)
- [Modelo](./load-balancer-ipv6-internet-template.md)

Um balanceador de carga Azure é um balanceador de carga de camada-4 (TCP, UDP). O balanceador de carga fornece alta disponibilidade distribuindo o tráfego de entrada entre instâncias de serviço eficaz, nos serviços de nuvem ou máquinas virtuais em um conjunto de Balanceador de carga. Azure balanceador de carga também pode apresentar esses serviços em várias portas, vários endereços IP ou ambos.

## <a name="example-deployment-scenario"></a>Cenário de implantação de exemplo

O diagrama a seguir ilustra a solução está sendo implantada neste artigo de balanceamento de carga.

![Cenário de Balanceador de carga](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

Neste cenário, você criará os seguintes recursos Azure:

- um balanceador de carga voltado para a Internet com um IPv4 e um endereço IP público IPv6
- dois carregar balanceamento regras para mapear os VIPs públicos para os pontos de extremidade particulares
- um conjunto de disponibilidade para que contém as duas VMs
- duas máquinas virtuais (VMs)
- uma interface de rede virtual para cada máquina virtual com endereços IPv4 e IPv6 atribuídos

## <a name="deploying-the-solution-using-the-azure-powershell"></a>Implantação da solução usando o PowerShell do Azure

As etapas a seguir mostram como criar uma Internet opostas balanceador de carga usando o Gerenciador de recursos do Azure com o PowerShell. Com o Gerenciador de recursos do Azure, cada recurso é criado e configurado individualmente, em seguida, coloque juntos para criar um recurso.

Para implantar um balanceador de carga, crie e configure os seguintes objetos:

- Configuração de IP front-end - contém os endereços IP públicos tráfego de rede.
- Pool de endereços de back-end - contém interfaces de rede (NICs) para as máquinas virtuais receber o tráfego de rede de Balanceador de carga.
- Regras de balanceamento de carga - contém regras mapeando uma porta pública no balanceador de carga para porta no pool de endereços de back-end.
- Regras NAT de entrada - contém regras mapeando uma porta pública no balanceador de carga para uma porta para uma máquina virtual específica no pool de endereços de back-end.
- Testes - contém testes de integridade usadas para verificar a disponibilidade de instâncias de máquinas virtuais no pool de endereços de back-end.

Para obter mais informações, consulte [Gerenciador de recursos do Azure suporte do balanceador de carga](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurar o PowerShell para usar o Gerenciador de recursos

Verifique se que você tem a versão mais recente de produção do Gerenciador de recursos do Azure módulo do PowerShell.

1. Entrar no Azure

        Login-AzureRmAccount

    Insira suas credenciais quando solicitado.

2. Verificar as assinaturas para a conta

        Get-AzureRmSubscription

3. Escolha quais das suas assinaturas Azure usar.

        Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

4. Criar um grupo de recursos (ignorar esta etapa se usando um grupo existente do recurso)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Criar uma rede virtual e um endereço IP público para o pool IP front-end

1. Crie uma rede virtual com uma sub-rede.

        $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
        $vnet = New-AzureRmvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

2. Crie endereço IP público do Azure recursos (PIP) para o pool de endereços IP front-end.

        $publicIPv4 = New-AzureRmPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
        $publicIPv6 = New-AzureRmPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6

    >[AZURE.IMPORTANT] O balanceador de carga usa o rótulo de domínio do IP público como prefixo para seu FQDN. Neste exemplo, os FQDNs são *lbnrpipv4.westus.cloudapp.azure.com* e *lbnrpipv6.westus.cloudapp.azure.com*.

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>Criar uma configuração de IP de front-end e um Pool de endereços de Back-End

1. Crie a configuração de endereço front-end que usa os endereços de IP público que você criou.

        $FEIPConfigv4 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
        $FEIPConfigv6 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6

2. Crie pools de endereço de back-end.

        $backendpoolipv4 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
        $backendpoolipv6 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"


## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>Criar regras LB, regras NAT, um teste e um balanceador de carga

Este exemplo cria os seguintes itens:

- uma regra NAT para traduzir todo o tráfego de entrada na porta 443 à porta 4443
- uma regra de Balanceador de carga para saldo todo o tráfego de entrada na porta 80 à porta 80 nos endereços do pool de back-end.
- uma regra de Balanceador de carga para permitir a conexão de RDP às VMs na porta 3389.
- uma regra de teste para verificar o status de integridade em uma página denominada *HealthProbe.aspx* ou um serviço na porta 8080
- um balanceador de carga que usa todos esses objetos

1. Crie as regras NAT.

        $inboundNATRule1v4 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
        $inboundNATRule1v6 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443

2. Crie um teste de integridade. Há duas maneiras de configurar um teste:

    Teste HTTP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    ou TCP sondagem

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
        $RDPprobe = New-AzureRmLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2


    Neste exemplo, vamos usar os testes TCP.

3. Crie uma regra de Balanceador de carga.

        $lbrule1v4 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
        $lbrule1v6 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
        $RDPrule = New-AzureRmLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389

4. Crie o balanceador de carga usando os objetos criados anteriormente.

        $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule

## <a name="create-nics-for-the-back-end-vms"></a>Criar NICs para as VMs de back-end

1. Obtenha a rede Virtual e Virtual sub-rede de rede, onde as NICs precisam ser criados.

        $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
        $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet

2. Crie configurações de IP e placas de rede para as VMs.

        $nic1IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
        $nic1IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
        $nic1 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

        $nic2IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
        $nic2IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
        $nic2 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>Criar máquinas virtuais e atribuir as NICs recém-criado

Para obter mais informações sobre a criação de uma máquina virtual, consulte [criar e predefinir uma máquina de Virtual do Windows com o Gerenciador de recursos e Azure PowerShell](..\virtual-machines\virtual-machines-windows-ps-create.md)

1. Criar uma conta de armazenamento e disponibilidade definida

        New-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
        $availabilitySet = Get-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
        New-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName $LRS
        $CreatedStorageAccount = Get-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'

2. Criar cada máquina virtual e atribuir anterior criado NICs

        $mySecureCredentials= Get-Credential -Message “Type the username and password of the local administrator account.”

        $vm1 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM0' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
        $vm1 = Set-AzureRmVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
        $vm1 = Set-AzureRmVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        $vm1 = Add-AzureRmVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
        $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
        $vm1 = Set-AzureRmVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
        New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

        $vm2 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM1' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
        $vm2 = Set-AzureRmVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
        $vm2 = Set-AzureRmVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        $vm2 = Add-AzureRmVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
        $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
        $vm2 = Set-AzureRmVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
        New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2

## <a name="next-steps"></a>Próximas etapas

[Começar a configurar um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição de Balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite TCP ociosas para seu balanceador de carga](load-balancer-tcp-idle-timeout.md)
