Você deve criar uma VNet e uma sub-rede de gateway primeiro, antes de trabalhar nas tarefas a seguir. Consulte o artigo [Configurar uma rede Virtual usando o portal de clássico](../articles/expressroute/expressroute-howto-vnet-portal-classic.md) para obter mais informações.   

## <a name="add-a-gateway"></a>Adicionar um gateway

Use o comando a seguir para criar um gateway. Certifique-se de substituir qualquer valores para seu próprio.

    New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType Dedicated -GatewaySKU  Standard

## <a name="verify-the-gateway-was-created"></a>Verificar que o gateway foi criado

Use o comando a seguir para verificar se o gateway foi criado. Este comando também recupera a identificação de gateway, o que você precisa para outras operações.

    Get-AzureVirtualNetworkGateway

## <a name="resize-a-gateway"></a>Redimensionar um gateway

Há um número de [SKUs de Gateway](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Você pode usar o comando a seguir para alterar o SKU de Gateway a qualquer momento.

>[AZURE.IMPORTANT] Este comando não funciona para UltraPerformance gateway. Para alterar seu gateway para um gateway UltraPerformance, primeiro remova o gateway rota expressa existente e, em seguida, criar um novo gateway UltraPerformance. Para fazer um downgrade seu gateway de um gateway UltraPerformance, primeiro remova o gateway UltraPerformance e, em seguida, criar um novo gateway. 

    Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## <a name="remove-a-gateway"></a>Remover um gateway

Use o comando a seguir para remover um gateway

    Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>