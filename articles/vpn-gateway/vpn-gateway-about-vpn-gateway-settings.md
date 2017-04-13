<properties 
   pageTitle="Sobre as configurações de Gateway VPN para gateways de rede virtual | Microsoft Azure"
   description="Saiba mais sobre as configurações de Gateway VPN para rede Virtual do Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc" />

# <a name="about-vpn-gateway-settings"></a>Sobre as configurações de Gateway VPN

Uma solução de conexão de gateway VPN depende da configuração de vários recursos para enviar tráfego de rede entre redes virtuais e locais no local. Cada recurso contém configurações. A combinação dos recursos e configurações determina o resultado de conexão.

As seções neste artigo discutem os recursos e configurações que relacionam um gateway VPN no modelo de implantação do **Gerenciador de recursos** . Talvez seja útil para exibir as configurações disponíveis usando diagramas de topologia de conexão. Você pode obter as descrições e diagramas de topologia de cada solução de conexão no artigo [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) . 

## <a name="gwtype"></a>Tipos de gateway

Cada rede virtual só pode ter um gateway de rede virtual de cada tipo. Quando você estiver criando um gateway de rede virtual, você deve garantir que o tipo de gateway está correto para a sua configuração.

Os valores disponíveis para - GatewayType são: 

- VPN
- Rota expressa

Um gateway VPN requer o `-GatewayType` *Vpn*.  

Exemplo:

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
    -VpnType RouteBased
 

## <a name="gwsku"></a>SKUs de gateway


[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configuring-the-gateway-sku"></a>Configurando o gateway SKU

**Especifica o gateway SKU no portal do Azure**

Se você usar o portal do Azure para criar um gateway de rede virtual do Gerenciador de recursos, você pode selecionar o gateway SKU usando na lista suspensa. As opções que são apresentadas correspondem ao tipo de Gateway e tipo VPN que você selecionar.

Por exemplo, se você selecionar o tipo de gateway 'VPN' e a VPN 'política baseados no tipo', verá apenas o SKU 'Básico' porque é a única SKU disponível para PolicyBased VPNs. Se você selecionar 'Baseado em rota', você pode selecionar no Basic, Standard e HighPerformance SKUs. 


**Especifica o gateway SKU usando o PowerShell**


O exemplo a seguir PowerShell Especifica a `-GatewaySku` como *padrão*.

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
    -GatewayType Vpn -VpnType RouteBased

**Alterando um gateway SKU**

Se você deseja atualizar seu gateway SKU para uma SKU mais eficazes (a partir do Basic padrão para HighPerformance) você pode usar o `Resize-AzureRmVirtualNetworkGateway` cmdlet do PowerShell. Você também pode fazer um downgrade o tamanho SKU usando esse cmdlet do gateway.

O PowerShell de exemplo a seguir mostra um gateway SKU sendo redimensionada para HighPerformance.

    $gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

### <a name="estimated-aggregate-throughput-by-gateway-sku-and-type"></a>Estimada tipo e transferência agregada pelo gateway SKU

A tabela a seguir mostra os tipos de gateway e a taxa de transferência agregada estimada. Esta tabela se aplica ao gerente de recursos e modelos de implantação clássico.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 


## <a name="connectiontype"></a>Tipos de Conexão

No modelo de implantação do Gerenciador de recursos, cada configuração requer um tipo de conexão de gateway de rede virtual específico. O PowerShell Gerenciador de recursos disponíveis valores para `-ConnectionType` são:

- IPsec
- Vnet2Vnet
- Rota expressa
- VPNClient

No exemplo a seguir do PowerShell, podemos criar uma conexão de S2S que requer que o tipo de conexão *IPsec*.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>Tipos VPN

Quando você cria o gateway de rede virtual para uma configuração de gateway VPN, você deve especificar um tipo VPN. O tipo VPN que você escolher depende da topologia de conexão que você deseja criar. Por exemplo, uma conexão de P2S requer um tipo de RouteBased VPN. Um tipo VPN também pode depender do hardware que você usará. Configurações de S2S exigem um dispositivo VPN. Alguns dispositivos VPN só oferece suporte a um determinado tipo VPN.

O tipo VPN que você selecionar deve satisfazer todos os requisitos de conexão para a solução que você deseja criar. Por exemplo, se você quiser criar uma conexão de gateway S2S VPN e uma conexão de gateway VPN de P2S para a mesma rede virtual, você usaria o tipo VPN *RouteBased* porque P2S requer um tipo de RouteBased VPN. Você também precisa verificar se o seu dispositivo VPN dá suporte a uma conexão de RouteBased VPN. 

Depois de um gateway de rede virtual tiver sido criado, você não pode alterar o tipo VPN. Você precisa excluir o gateway de rede virtual e criar um novo. Há dois tipos VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]


O exemplo a seguir PowerShell Especifica a `-VpnType` como *RouteBased*. Quando você estiver criando um gateway, você deve garantir que o VpnType - está correto para a sua configuração. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig `
    -GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Requisitos de gateway

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 


## <a name="gwsub"></a>Sub-rede de gateway

Para configurar um gateway de rede virtual, primeiro é necessário criar uma sub-rede de gateway para sua VNet. A sub-rede de gateway deve ser nomeada *GatewaySubnet* funcione corretamente. Esse nome informa Azure que esta sub-rede deve ser usada para o gateway.

O tamanho mínimo de sua sub-rede gateway depende totalmente a configuração que você deseja criar. Embora seja possível criar uma sub-rede de gateway o menor /29, recomendamos que você crie uma sub-rede de gateway de /28 ou maior (/ 28, /27, /26, etc.). 

Criar um tamanho maior de gateway impede a execução para cima contra limitações de tamanho de gateway. Por exemplo, você pode ter criado um gateway de rede virtual com um tamanho de sub-rede de gateway /29 para uma conexão de S2S. Você agora deseja configurar uma S2S/rota expressa coexistência configuração. Essa configuração requer um tamanho de mínimo de sub-rede /28 do gateway. Para criar sua configuração, você precisa modificar a sub-rede de gateway para acomodar o requisito mínimo para a conexão, que é /28.

O Gerenciador de recursos do PowerShell exemplo a seguir mostra uma sub-rede de gateway denominada GatewaySubnet. Você pode ver que a notação CIDR Especifica um /27, que permite endereços IP suficientes para a maioria das configurações que existem atualmente.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 


## <a name="lng"></a>Gateways de rede local

Ao criar uma configuração de gateway VPN, o gateway de rede local geralmente representa seu local de local. No modelo clássico de implantação, o gateway de rede local era conhecido como um Site Local. 

Dê um nome, o endereço IP público do dispositivo VPN local, para o gateway de rede local e especifique os prefixos de endereço que se encontram no local onde local. Azure examina os prefixos de endereço de destino para tráfego de rede, consulta a configuração que você especificou para seu gateway de rede local e roteia pacotes adequadamente. Você também pode especificar os gateways de rede local para configurações de VNet para VNet que usam uma conexão de gateway VPN.

O PowerShell de exemplo a seguir cria um novo gateway de rede local:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Às vezes você precisa modificar as configurações de gateway de rede local. Por exemplo, quando você adicionar ou modificar o intervalo de endereços ou se o endereço IP do dispositivo VPN muda. Para uma VNet clássico, você pode alterar essas configurações no portal do clássico na página redes locais. Para o Gerenciador de recursos, consulte [modificar as configurações de gateway de rede local usando o PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>Cmdlets APIs REST e PowerShell

Para requisitos de sintaxe específica ao usar APIs REST e cmdlets do PowerShell para configurações de Gateway VPN e recursos técnicos adicionais, consulte as seguintes páginas:

|**Clássico** | **Gerenciador de recursos**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## <a name="next-steps"></a>Próximas etapas

Consulte [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) para obter mais informações sobre configurações de conexão disponíveis. 







 
