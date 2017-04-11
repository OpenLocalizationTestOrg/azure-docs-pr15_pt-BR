<properties
   pageTitle="Criar um balanceador de carga voltado para a Internet no Gerenciador de recursos usando o PowerShell | Microsoft Azure"
   description="Aprenda a criar um balanceador de carga voltado para a Internet no Gerenciador de recursos usando o PowerShell"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
  ms.service="load-balancer"
  ms.devlang="na"
  ms.topic="get-started-article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
   ms.date="10/24/2016"
  ms.author="sewhee" />

# <a name="get-started"></a>Criando um balanceador de carga voltado para a Internet no Gerenciador de recursos usando o PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo discute o modelo de implantação do Gerenciador de recursos. Você também pode [aprender a criar um balanceador de carga voltado para a Internet usando o modelo clássico de implantação](load-balancer-get-started-internet-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-by-using-azure-powershell"></a>Implantação da solução usando o PowerShell do Azure

Os procedimentos a seguir explicam como criar um balanceador de carga voltado para a Internet usando o Gerenciador de recursos do Azure com o PowerShell. Com o Gerenciador de recursos do Azure, cada recurso é criado configurado individualmente e, em seguida, coloque juntos para criar um balanceador de carga.

Você deve criar e configurar os objetos a seguir para implantar um balanceador de carga:

- Configuração de IP front-end: contém endereços de IP (PIP) públicos tráfego de rede.
- Pool de endereços de back-end: contém interfaces de rede (NICs) para as máquinas virtuais receber o tráfego de rede de Balanceador de carga.
- Regras de balanceamento de carga: contém regras que mapeiam uma porta pública no balanceador de carga para uma porta no pool de endereços de back-end.
- Regras NAT de entrada: contém regras que mapeiam uma porta pública no balanceador de carga a uma porta para uma máquina virtual específica no pool de endereços de back-end.
- Testes: contém testes de integridade usadas para verificar a disponibilidade de instâncias de máquina virtual do pool de endereço de back-end.

Para obter mais informações, consulte [Gerenciador de recursos do Azure suporte do balanceador de carga](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurar o PowerShell para usar o Gerenciador de recursos

Verifique se que você tem a versão mais recente de produção do Gerenciador de recursos do Azure módulo do PowerShell:

1. Entrar no Azure.

        Login-AzureRmAccount

    Insira suas credenciais quando solicitado.

2. Verifique as assinaturas para a conta.

        Get-AzureRmSubscription

3. Escolha quais das suas assinaturas Azure usar.

        Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

4. Crie um grupo de recursos. (Pule esta etapa se você estiver usando um grupo existente do recurso).

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Criar uma rede virtual e um endereço IP público para o pool IP front-end

1. Crie uma sub-rede e uma rede virtual.

        $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
        New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

2. Crie Azure público recurso de endereço IP, chamado **PublicIP**, a ser usado por um pool IP front-end com o nome do DNS **loadbalancernrp.westus.cloudapp.azure.com**. O comando a seguir usa o tipo de alocação estática.

        $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' –AllocationMethod Static -DomainNameLabel loadbalancernrp

    >[AZURE.IMPORTANT]O balanceador de carga usa o rótulo de domínio do IP pública como um prefixo para seu FQDN. Isso é diferente do modelo clássico de implantação, que usa o serviço de nuvem como o balanceador de carga FQDN.
    >Neste exemplo, o FQDN é **loadbalancernrp.westus.cloudapp.azure.com**.

## <a name="create-a-front-end-ip-pool-and-a-back-end-address-pool"></a>Criar um pool de front-end de IP e um pool de endereços de back-end

1. Crie um pool IP front-end denominado **LB Frontend** que usa o recurso de **PublicIp** .

        $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP

2. Crie um pool de endereços de back-end denominado **LB-back-end**.

        $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend

## <a name="create-nat-rules-a-load-balancer-rule-a-probe-and-a-load-balancer"></a>Criar regras NAT, uma regra de Balanceador de carga, um teste e um balanceador de carga

Este exemplo cria os seguintes itens:

- Uma regra NAT para traduzir todo o tráfego de entrada na porta 3441 a porta 3389
- Uma regra NAT para traduzir todo o tráfego de entrada na porta 3442 a porta 3389
- Uma regra de teste para verificar o status de integridade em uma página denominada **HealthProbe.aspx**
- Uma regra de Balanceador de carga para saldo todo o tráfego de entrada na porta 80 à porta 80 nos endereços do pool de back-end
- Um balanceador de carga que usa todos esses objetos

Use estas etapas:

1. Crie as regras NAT.

        $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

        $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

2. Crie um teste de integridade. Há duas maneiras de configurar um teste:

    Teste HTTP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    Teste TCP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2

3. Crie uma regra de Balanceador de carga.

        $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

4. Crie o balanceador de carga usando os objetos criados anteriormente.

        $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe

## <a name="create-nics"></a>Criar NICs

Crie interfaces de rede (ou modificar as existentes) e, em seguida, associá-los para regras, regras de Balanceador de carga e testes NAT:

1. Obtenha a rede virtual e uma sub-rede de rede virtual, onde as NICs precisam ser criados.

        $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
        $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet

2. Criar uma NIC denominada **lb-nic1-ser**e associá-la com a primeira regra NAT e o pool de endereços de back-end primeira (e única).

        $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

3. Criar uma NIC denominada **lb-nic2-ser**e associá-la com a segunda regra NAT e o pool de endereços de back-end primeira (e única).

        $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

4. Verifique as NICs.

        $backendnic1

    Saída esperada:

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
                            "PublicIpAddress": {
                                "Id": null
                            },
                            "LoadBalancerBackendAddressPools": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                                }
                            ],
                            "LoadBalancerInboundNatRules": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                                }
                            ],
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              :

5. Use o `Add-AzureRmVMNetworkInterface` cmdlet para atribuir as NICs para VMs diferentes.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para obter orientação sobre como criar uma máquina virtual e atribuindo uma NIC, consulte [criar uma máquina virtual do Azure usando o PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md).

## <a name="add-the-network-interface-to-the-load-balancer"></a>Adicionar a interface de rede ao balanceador de carga

1. Recupere o balanceador de carga do Azure.

    Carrega o recurso de Balanceador de carga em uma variável (se você ainda não tiver feito isso ainda). A variável é chamada **$lb**. Use os mesmos nomes do recurso de Balanceador de carga que você criou anteriormente.

        $lb= get-azurermloadbalancer –name NRP-LB -resourcegroupname NRP-RG

2. Carrega a configuração de back-end a uma variável.

        $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

3. Carrega a interface de rede já criado em uma variável. O nome da variável é **$nic**. O nome da interface de rede é a mesma do exemplo anterior.

        $nic =get-azurermnetworkinterface –name lb-nic1-be -resourcegroupname NRP-RG

4. Altere a configuração de back-end na interface de rede.

        $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

5. Salve o objeto de interface de rede.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Depois de uma interface de rede é adicionada ao pool de back-end de Balanceador de carga, ele começa a receber o tráfego de rede com base nas regras de balanceamento de carga para esse recurso de Balanceador de carga.

## <a name="update-an-existing-load-balancer"></a>Atualizar um balanceador de carga existente

1. Usando o balanceador de carga do exemplo anterior, atribua um objeto de Balanceador de carga para a variável **$slb** usando `Get-AzureLoadBalancer`.

        $slb = get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

2. No exemplo a seguir, você pode adicionar uma regra de entrada NAT - usando porta 81 do pool de front-end e 8181 para o pool de back-end — um balanceador de carga existente.

        $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP

3. Salvar a nova configuração usando `Set-AzureLoadBalancer`.

        $slb | Set-AzureRmLoadBalancer

## <a name="remove-a-load-balancer"></a>Remover um balanceador de carga

Use o comando `Remove-AzureLoadBalancer` para excluir um balanceador de carga criado anteriormente chamado **NRP kg** em um grupo de recursos denominado **NRP-RG**.

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Você pode usar a opção opcional **-força** para evitar o prompt para exclusão.

## <a name="next-steps"></a>Próximas etapas

[Começar a configurar um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição de Balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite TCP ociosas para seu balanceador de carga](load-balancer-tcp-idle-timeout.md)
