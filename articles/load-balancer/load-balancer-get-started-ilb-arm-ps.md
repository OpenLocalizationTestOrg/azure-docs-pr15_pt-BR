<properties
   pageTitle="Criar um balanceador de carga interno usando o PowerShell no Gerenciador de recursos | Microsoft Azure"
   description="Aprenda a criar um balanceador de carga interno usando o PowerShell no Gerenciador de recursos"
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

# <a name="create-an-internal-load-balancer-using-powershell"></a>Criar um balanceador de carga interno usando o PowerShell

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implantação clássico](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


As etapas a seguir explicam como criar um balanceador de carga interno usando o Gerenciador de recursos do Azure com o PowerShell. Com o Gerenciador de recursos do Azure, os itens para criar um balanceador de carga internos são configurados individualmente e, em seguida, combinados para criar um balanceador de carga.

Você precisa criar e configurar os objetos a seguir para implantar um balanceador de carga:

- Front-configuração de IP do end - configurará o endereço IP privado tráfego de rede
- Pool de endereços de back-end - configurará as interfaces de rede que receberão o tráfego de balanceamento de carga que vêm do pool IP de front-end
- Regras - origem e configuração de porta local para o balanceador de carga de balanceamento de carga.
- Testes - configura o teste de status de integridade para as instâncias de máquina Virtual.
- Regras NAT de entrada - configura as regras de porta para acessar diretamente uma das instâncias Máquina Virtual.

Você pode obter mais informações sobre carga componentes de Balanceador com Gerenciador de recursos Azure no [Gerenciador de recursos do Azure suporte do balanceador de carga](load-balancer-arm.md).

As etapas a seguir explicam como configurar um balanceador de carga entre duas máquinas virtuais.


## <a name="setup-powershell-to-use-resource-manager"></a>Configurar o PowerShell para usar o Gerenciador de recursos

Verifique se você tiver a versão mais recente de produção do Azure módulo do PowerShell e que a instalação do PowerShell corretamente para acessar a sua assinatura do Azure.

### <a name="step-1"></a>Etapa 1

        Login-AzureRmAccount

### <a name="step-2"></a>Etapa 2

Verificar as assinaturas para a conta

        Get-AzureRmSubscription

Você será solicitado a autenticar com as suas credenciais.<BR>

### <a name="step-3"></a>Etapa 3

Escolha quais das suas assinaturas Azure usar. <BR>


        Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="create-resource-group-for-load-balancer"></a>Criar o grupo de recursos para balanceador de carga

### <a name="step-4"></a>Etapa 4

Criar um novo grupo de recursos (ignorar esta etapa se usando um grupo existente do recurso)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

Gerenciador de recursos de Azure requer que todos os grupos de recursos especificam um local. Isso é usado como o local padrão para os recursos desse grupo de recursos. Verifique se todos os comandos para criar um balanceador de carga usará o mesmo grupo de recursos.

No exemplo acima, criamos um grupo de recursos chamado "NRP-RG" e local "Oeste EUA".

## <a name="create-virtual-network-and-a-private-ip-address-for-front-end-ip-pool"></a>Criar rede Virtual e um endereço IP particular para pool IP de front-end


### <a name="step-1"></a>Etapa 1

Cria uma sub-rede para a rede virtual e atribui a variável $backendSubnet

    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

Crie uma rede virtual:

    $vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

Cria a rede virtual e adiciona a sub-rede lb-sub-rede-ser à rede virtual NRPVNet e atribui a variável $vnet



## <a name="create-front-end-ip-pool-and-backend-address-pool"></a>Criar pool IP Front-end e back-end pool de endereços

Configurando um pool de front-end IP para entrada carga balanceador tráfego e back-end pool endereços de rede receber a carga equilibrada tráfego.

### <a name="step-1"></a>Etapa 1

Crie um pool de front-end IP usando o endereço IP privado 10.0.2.5 para o 10.0.2.0/24 sub-rede que será o ponto de extremidade de tráfego de rede recebidas.

    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id

### <a name="step-2"></a>Etapa 2

Configure um pool de endereços de back-end usado para receber o tráfego de entrada front-end do pool de IP:

    $beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## <a name="create-lb-rules-nat-rules-probe-and-load-balancer"></a>Criar regras LB, regras NAT, teste e balanceador de carga

Depois de criar o pool IP de front-end e o pool de endereços de back-end, você precisará criar as regras que pertencem o recurso de Balanceador de carga:

### <a name="step-1"></a>Etapa 1

    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

     $lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


O exemplo anterior está criando os seguintes itens:

- Regra NAT que todo o tráfego de entrada à porta 3441 irão para a porta 3389.
- uma segunda regra NAT que todo o tráfego de entrada à porta 3442 irão para a porta 3389.
- uma regra de Balanceador de carga que carregará saldo todos tráfego na porta pública 80 a porta local 80 no pool de endereços de back-end.
- uma regra de teste que irá verificar o status de integridade do caminho "HealthProbe.aspx"



### <a name="step-2"></a>Etapa 2

Crie o balanceador de carga adicionando todos os objetos (regras NAT, regras de Balanceador de carga, configurações de teste) juntos:

    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe


## <a name="create-network-interfaces"></a>Criar interfaces de rede

Depois de criar o balanceador de carga interno, você precisa definir quais interfaces de rede estará recebendo a entrada tráfego de rede de balanceamento de carga, regras NAT e teste. Nesse caso, a interface de rede está configurada individualmente e pode ser atribuída a uma máquina virtual posteriormente.


### <a name="step-1"></a>Etapa 1


Obter a rede virtual do recurso e sub-rede para criar interfaces de rede:

    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet


Esta etapa cria uma interface de rede que pertencem ao pool de back-end de Balanceador de carga e associar a primeira regra NAT para RDP para a interface de rede:

    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### <a name="step-2"></a>Etapa 2

Criar uma segunda chamada de interface de rede LB-Nic2-ser:

Esta etapa cria uma segunda interface de rede, atribuindo o mesmo pool de back-end de Balanceador de carga e associando a segunda regra NAT criado para RDP:

     $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

O resultado final mostrará o seguinte:

    $backendnic1

Saída esperada:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
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
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3"></a>Etapa 3

Use o comando Adicionar AzureRmVMNetworkInterface atribuir a NIC a uma máquina virtual.

Você pode encontrar as instruções passo a passo para criar uma máquina virtual e atribuir a uma NIC seguindo a documentação: [criar uma máquina virtual do Azure usando o PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md).

Se você já tiver uma máquina virtual criada, você pode adicionar a interface de rede com as seguintes etapas:

#### <a name="step-1"></a>Etapa 1

Carrega o recurso de Balanceador de carga em uma variável (se você ainda não tiver feito isso ainda). A variável usada é chamado $lb e use os mesmos nomes do recurso de Balanceador de carga criado acima.

    $lb= Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG

#### <a name="step-2"></a>Etapa 2

Carrega a configuração de back-end a uma variável.

    $backend= Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

#### <a name="step-3"></a>Etapa 3

Carrega a interface de rede já criado em uma variável. o nome da variável usado é NIC $. O nome da interface de rede usado é a mesma do exemplo acima.

    $nic=Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG

#### <a name="step-4"></a>Etapa 4

Altere a configuração de back-end na interface de rede.

    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

#### <a name="step-5"></a>Etapa 5

Salve o objeto de interface de rede.

    Set-AzureRmNetworkInterface -NetworkInterface $nic

Depois de uma interface de rede é adicionada ao pool de back-end de Balanceador de carga, ele começa a receber o tráfego de rede com base em regras para esse recurso de Balanceador de carga de balanceamento de carga.

## <a name="update-an-existing-load-balancer"></a>Atualizar um balanceador de carga existente


### <a name="step-1"></a>Etapa 1

Usando o balanceador de carga do exemplo acima, atribuir o objeto de Balanceador de carga a variável $slb usando Get-AzureRmLoadBalancer

    $slb=get-azureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### <a name="step-2"></a>Etapa 2

No exemplo a seguir, você irá adicionar uma nova regra de entrada NAT usando porta 81 no front-end e 8181 para o pool de back-end para um balanceador de carga existente

    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### <a name="step-3"></a>Etapa 3

Salvar a nova configuração usando Set-AzureLoadBalancer

    $slb | Set-AzureRmLoadBalancer

## <a name="remove-a-load-balancer"></a>Remover um balanceador de carga

Use o comando AzureRmLoadBalancer remover para excluir um balanceador de carga criado anteriormente chamado "NRP kg" em um grupo de recursos chamado "NRP-RG"

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Você pode usar o opcional alternar - Force para evitar o prompt para exclusão.



## <a name="next-steps"></a>Próximas etapas

[Configurar um modo de distribuição de Balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite TCP ociosas para seu balanceador de carga](load-balancer-tcp-idle-timeout.md)