<properties
   pageTitle="Criar uma rede virtual com uma conexão de VPN to-Site usando o Gerenciador de recursos do Azure e PowerShell | Microsoft Azure"
   description="Este artigo orienta a criação de um VNet usando o modelo de implantação do Gerenciador de recursos e conectá-lo à sua rede local local usando uma conexão de gateway VPN de S2S."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-powershell"></a>Criar um VNet com uma conexão de-to-Site usando o PowerShell

> [AZURE.SELECTOR]
- [Gerenciador de recursos - Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Gerenciador de recursos - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Clássico - Portal clássico](vpn-gateway-site-to-site-create.md)

Este artigo explica a criação de uma rede virtual e uma conexão de gateway-to-Site VPN à sua rede local usando o modelo de implantação do Gerenciador de recursos do Azure. Conexões de-to-Site podem ser usadas para entre locais e híbridos configurações.

![Diagrama de-to-Site] (./media/vpn-gateway-create-site-to-site-rm-powershell/s2srmps.png "to-site") 


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelos de implantação e métodos para conexões to-Site

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

A tabela a seguir mostra os métodos para configurações de Site para Site e modelos de implantação disponível no momento. Quando um artigo com etapas de configuração estiver disponível, podemos vincular diretamente a ele desta tabela. 

[AZURE.INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurações adicionais

Se você deseja conectar VNets juntos, mas não estiver criando uma conexão para um local no local, consulte [Configurar uma conexão de VNet para VNet](vpn-gateway-vnet-vnet-rm-ps.md). Se você quiser adicionar uma conexão de-to-Site a uma VNet que já tenha uma conexão, consulte [Adicionar uma conexão de S2S para um VNet com uma conexão de gateway VPN existente](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Antes de começar

Verifique se você tem os seguintes itens antes de iniciar a configuração.

- Um dispositivo VPN compatível e alguém que esteja configurá-la. Consulte [sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se você não estiver familiarizado com a configuração de seu dispositivo VPN ou não estiver familiarizado com o endereço IP intervalos localizados na configuração de rede do seu local, você precisa coordenar com alguém que pode fornecer esses detalhes para você.

- Um externamente opostas público endereço IP para o seu dispositivo VPN. Este endereço IP não pode ser localizado atrás de um NAT.
    
- Uma assinatura do Azure. Se você ainda não tiver uma assinatura do Azure, você pode ativar seus [benefícios de assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou sinal para cima, para uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
    
- A versão mais recente do cmdlets do PowerShell do Gerenciador de recursos do Azure. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.


## <a name="Login"></a>1. se conectar à sua assinatura 

Verifique se que você alterna para o modo de PowerShell para usar os cmdlets do Gerenciador de recursos. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de recursos](../powershell-azure-resource-manager.md).

Abra o console do PowerShell e conectar à sua conta. Use o exemplo a seguir para ajudá-lo a se conectar:

    Login-AzureRmAccount

Verifique as assinaturas para a conta.

    Get-AzureRmSubscription 

Especifica a assinatura que você deseja usar.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="VNet"></a>2. Crie uma rede virtual e uma sub-rede de gateway

Os exemplos usam uma sub-rede de gateway de /28. Embora seja possível criar uma sub-rede de gateway o menor /29, recomendamos que você crie uma sub-rede maior que inclui mais endereços selecionando pelo menos /28 ou /27. Isso permitirá endereços suficientes acomodar possíveis configurações adicionais que podem ser no futuro.

Se você já tiver uma rede virtual com sub-rede gateway/29 ou maior, você pode pular para [Adicionar seu gateway de rede local](#localnet).


[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]  

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>Para criar uma rede virtual e uma sub-rede de gateway

Use o exemplo a seguir para criar uma rede virtual e uma sub-rede de gateway. Substitua os valores para seu próprio. 

Primeiro, crie um grupo de recursos:
    
    New-AzureRmResourceGroup -Name testrg -Location 'West US'

Em seguida, crie sua rede virtual. Verifique se que os espaços de endereço que você especificar não se sobreponham qualquer um dos espaços de endereço de que você tem em sua rede local.

O exemplo a seguir cria uma rede virtual denominado *testvnet* e duas sub-redes, uma chamada *GatewaySubnet* e a outra chamada *Subnet1*. É importante criar uma sub-rede denominada especificamente *GatewaySubnet*. Se você nomear-algo mais, sua configuração de conexão falhará. 

Defina as variáveis.

    $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'

Crie o VNet.

    New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
    -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Para adicionar uma sub-rede de gateway a uma rede virtual que você já tiver criado

Esta etapa é necessária somente se você precisar adicionar uma sub-rede de gateway a um VNet que você criou anteriormente.

Você pode criar sua sub-rede de gateway usando o exemplo a seguir. Certifique-se de nomear a sub-rede de gateway 'GatewaySubnet'. Se você nomear-algo mais, você criar uma sub-rede, mas Azure não tratado como uma sub-rede de gateway.

Defina as variáveis.

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet

Crie sub-rede o gateway.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Defina a configuração. 

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3. <a name="localnet"> </a>adicionar seu gateway de rede local

Em uma rede virtual, o gateway de rede local geralmente se refere ao seu local de local. Você nomeie o site pelo qual Azure pode se referir a ela e também especificar o prefixo de espaço de endereço para o gateway de rede local. 

Azure usa o prefixo de endereço IP que você especificar para identificar o tráfego para enviar para seu local na. Isso significa que você precisa especificar cada prefixo de endereço que você deseja ser associadas a seu gateway de rede local. Você pode atualizar facilmente esses prefixos se mudar de sua rede local. 

Ao usar os exemplos do PowerShell, observe o seguinte:
    
- O *GatewayIPAddress* é o endereço IP do seu dispositivo VPN local. Seu dispositivo VPN não pode ser localizado atrás de um NAT. 
- O *AddressPrefix* é o seu espaço de endereço do local.

Para adicionar um gateway de rede local com um prefixo de endereço único:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Para adicionar um gateway de rede local com vários prefixos de endereços:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### <a name="to-modify-ip-address-prefixes-for-your-local-network-gateway"></a>Para modificar prefixos de endereço IP do seu gateway de rede local

Às vezes, alterar prefixos de gateway sua rede local. As etapas executadas para modificar seus prefixos de endereço IP dependem se você tiver criado uma conexão de gateway VPN. Consulte a seção de [prefixos de endereço de IP modificar para um gateway de rede local](#modify) deste artigo.


## <a name="PublicIP"></a>4. solicitar um endereço IP público para o gateway VPN

Em seguida, solicite um endereço IP público a ser alocado para o gateway VPN de VNet do Azure. Este não é o mesmo endereço IP que foi atribuído ao seu dispositivo VPN; em vez disso, ela é atribuída ao gateway VPN do Azure em si. Você não pode especificar o endereço IP que você deseja usar. Ele está dinamicamente alocado para o gateway. Usar este endereço IP ao configurar seu dispositivo VPN local para se conectar ao gateway.

O gateway VPN do Azure para o modelo de implantação do Gerenciador de recursos atualmente só oferece suporte a endereços IP públicos usando o método de alocação dinâmica. No entanto, isso não significa que o endereço IP será alterado. A única vez as alterações de endereço IP de gateway Azure VPN é quando o gateway é excluído e recriado. O endereço IP público do gateway não mudará em redimensionamento, redefinir ou outras manutenção/atualizações internas do seu gateway VPN do Azure.

Use o PowerShell de exemplo a seguir:

    $gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## <a name="GatewayIPConfig"></a>5. Crie a configuração de endereçamento de IP do gateway

A configuração do gateway define a sub-rede e o endereço IP público para usar. Use o exemplo a seguir para criar a configuração do gateway.

    $vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## <a name="CreateGateway"></a>6. criar o gateway de rede virtual

Nesta etapa, você criar o gateway de rede virtual. Criar um gateway pode levar muito tempo para ser concluída. Muitas vezes 45 minutos ou mais. 

Use os seguintes valores:

- A *-GatewayType* para uma configuração de-to-Site é *Vpn*. O tipo de gateway é sempre específico da configuração que você está implementando. Por exemplo, outras configurações de gateway podem exigir - GatewayType rota expressa. 

- A *-VpnType* pode ser *RouteBased* (referido como um Gateway dinâmico no alguns documentação) ou *PolicyBased* (referido como um Gateway estático no alguns documentação). Para obter mais informações sobre tipos de gateway VPN, consulte [Sobre Gateways de VPN](vpn-gateway-about-vpngateways.md#vpntype).
- A *-GatewaySku* pode ser *básico*, *padrão*ou *HighPerformance*.   

        New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
        -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

## <a name="ConfigureVPNDevice"></a>7. configurar seu dispositivo VPN

Neste ponto, é necessário o endereço IP público do gateway rede virtual para configurar seu dispositivo VPN local. Trabalhar com o fabricante do seu dispositivo para obter informações de configuração específica. Você pode se referir a [Dispositivos VPN](vpn-gateway-about-vpn-devices.md) para obter mais informações.

Para localizar o endereço IP público do seu gateway de rede virtual, use o exemplo a seguir:

    Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## <a name="CreateConnection"></a>8. Crie a conexão VPN

Em seguida, crie a conexão-to-Site VPN entre seu gateway de rede virtual e o dispositivo VPN. Certifique-se de que substitua os valores por suas próprias. A chave compartilhada deve corresponder ao valor que você usou para sua configuração de dispositivo VPN. Observe que o `-ConnectionType` -to-Site é *IPsec*. 

Defina as variáveis.

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

Crie a conexão.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Após um curto enquanto, a conexão será estabelecido. 

## <a name="toverify"></a>Para verificar uma conexão VPN

Há algumas maneiras diferentes para verificar sua conexão de VPN.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>Para modificar prefixos de endereço IP para um gateway de rede local

Se você precisar alterar os prefixos para seu gateway de rede local, use as instruções a seguir. Dois conjuntos de instruções são fornecidos. As instruções que você escolher dependem se você já criou sua conexão de gateway. 

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Para modificar o endereço IP do gateway para um gateway de rede local

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Próximas etapas

- Você pode adicionar máquinas virtuais às suas redes virtuais. Consulte [criar uma máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter as etapas.

- Para obter informações sobre BGP, consulte a [Visão geral de BGP](vpn-gateway-bgp-overview.md) e [como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).

