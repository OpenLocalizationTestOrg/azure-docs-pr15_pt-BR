<properties
   pageTitle="Criar VNet correspondência usando cmdlets do Powershell | Microsoft Azure"
   description="Aprenda a criar uma rede virtual usando o portal de Azure no Gerenciador de recursos."
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai; annahar"/>

# <a name="create-vnet-peering-using-powershell-cmdlets"></a>Criar VNet correspondência usando cmdlets do Powershell

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para criar um VNet correspondência usando o PowerShell, execute as etapas abaixo:

1. Se você nunca usou o PowerShell do Azure, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) e siga as instruções totalmente ao final para entrar no Azure e selecione sua assinatura.

> [AZURE.NOTE] Cmdlet do PowerShell para gerenciar a correspondência de VNet é fornecido com o [Azure PowerShell 1,6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. Ler objetos de rede virtual:

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
        $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2

3. Para estabelecer VNet correspondência, você precisa criar dois links, uma para cada direção. A seguinte etapa criará um link de correspondência VNet para VNet1 para VNet2 primeiro:

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id

    Mostra a saída:

        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Initiated
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

4. Esta etapa criará um link de correspondência VNet para VNet2 para VNet1:

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id

    Mostra a saída:

        Name            : LinkToVNet1
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet2
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

5. Depois que o link de correspondência VNet é criado, você pode ver o estado de link abaixo:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2

    Mostra a saída:

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Há algumas propriedades configuráveis para correspondência de VNet:

  	|Opção|Descrição|Padrão|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|Se o espaço de VNet de ponto a serem incluídos como parte da marca Virtual_network de endereço|Sim|
  	|AllowForwardedTraffic|Se o tráfego não provenientes um VNet peered for aceito ou descartado|Não|
  	|AllowGatewayTransit|Permite que o ponto VNet para usar seu gateway VNet|Não|
  	|UseRemoteGateways|Use o gateway de VNet do seu correspondente. O ponto VNet deve ter um gateway configurado e AllowGatewayTransit selecionado. Você não pode usar esta opção se você tiver um gateway configurado|Não|

    Cada link no VNet correspondência tem o conjunto de propriedades acima. Por exemplo, você pode definir AllowVirtualNetworkAccess como True para VNet aos link VNet1 para VNet2 e defina-a False para o link de correspondência VNet na outra direção.

        $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name LinkToVNet2
        $LinktoVNet2.AllowForwardedTraffic = $true
        Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2

    Você pode executar o Get-AzureRmVirtualNetworkPeering Verifique o valor da propriedade após a alteração. A saída, você pode ver que allowforwardedtraffic altera conjunto como True depois de executar os cmdlets acima.

        Name            : LinkToVNet2
        Id          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic       : True
        AllowGatewayTransit     : False
        UseRemoteGateways       : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

    Depois de correspondência for estabelecida neste cenário, você deve ser capaz de iniciar as conexões de qualquer máquina virtual para qualquer máquina virtual do ambas as VNets. Por padrão, AllowVirtualNetworkAccess for verdadeiro e VNet correspondência será provisionar os ACLs adequadas para permitir a comunicação entre VNets. Você ainda pode aplicar regras de grupo (NSG) de segurança de rede para bloquear a conectividade entre sub-redes específicas ou máquinas virtuais Obtenha refinados controle de acesso entre duas redes virtuais.  Para obter mais informações sobre como criar regras NSG, consulte este [artigo](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Para criar VNet correspondência em assinaturas usando o PowerShell, execute as etapas abaixo:

1. Entrar no Azure com privilégios usuário-A da conta para A assinatura e execute o seguinte cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5

    Isso não é um requisito, correspondência pode ser estabelecida mesmo se usuários individualmente elevar solicitações de correspondência para seus respectivos VNets, desde que as solicitações correspondem. Adicionando um usuário privilegiado da outro VNet como um usuário no VNet local torna mais fácil de fazer a instalação.

2. Entrar no Azure com a conta com privilégios-do usuário B para assinatura-B e execute o seguinte cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3

3. No usuário-A é sessão de login, execute o cmdlet a seguir:

        $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess

4. Na sessão de logon do usuário-B, execute o cmdlet abaixo:

        $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess

5. Depois correspondência for estabelecida, qualquer máquina virtual em VNet3 deve ser capaz de se comunicar com qualquer máquina virtual em VNet5.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Neste cenário, você pode executar cmdlets do PowerShell abaixo para estabelecer a correspondência de VNet.  Você precisa definir a propriedade AllowForwardedTraffic como True e vincular VNET1 HubVNet, que permite que o tráfego de entrada de fora do espaço de endereço aos VNet.

        $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

        Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id

2. Depois correspondência for estabelecida, você pode se referir a este [artigo](virtual-network-create-udr-arm-ps.md) e definir uma rota definidos pelo usuário (UDR) para redirecionar o tráfego de VNet1 por meio de um dispositivo virtual para usar seus recursos. Quando você especifica o endereço do próximo salto no roteiro, você pode defini-lo para o endereço IP do dispositivo virtual no ponto VNet HubVNet. Abaixo está um exemplo:

        $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

        $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Para criar um VNet correspondência entre uma rede virtual clássica e uma rede virtual do Gerenciador de recursos do Azure no PowerShell, siga as etapas abaixo:

1. Leia o objeto de rede virtual para **VNET1**, a rede virtual do Gerenciador de recursos do Azure da seguinte maneira:

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

2. Para estabelecer VNet correspondência nesse cenário, somente um link é necessária, especificamente um link de **VNET1** para **VNET2**. Esta etapa requer sabendo ID do recurso. do seu VNet clássico O formato de ID do grupo de recursos é semelhante a:

        /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}

    Certifique-se de substituir SubscriptionID, ResourceGroupName e VirtualNetworkName com os nomes apropriados.

    Isso pode ser feito pelo seguinte:

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2

3. Uma vez o VNet aos link é criado, você pode ver o estado de link conforme mostrado na saída abaixo:

        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-vnet-peering"></a>Remover a correspondência de VNet

1.  Para remover o VNet correspondência, você precisa executar o seguinte cmdlet:

        Remove-AzureRmVirtualNetworkPeering  

        Remove both links, using the following commands:

        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2

2. Depois que você remove um link em uma correspondência de VNET, o estado de link ponto irão para desconectado. Nesse estado, você não pode recriar o link até que o estado de link ponto muda para iniciada. Recomendamos que você remova ambos os links antes de recriar a correspondência de VNet.
