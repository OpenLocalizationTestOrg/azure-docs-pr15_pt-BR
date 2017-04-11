As etapas para essa tarefa usam um VNet baseado nos valores abaixo. Nomes e configurações adicionais também são descritos nesta lista. Não usamos esta lista diretamente em qualquer uma das etapas, embora adicionamos variáveis com base nos valores nessa lista. Você pode copiar a lista para usar como uma referência, substituindo os valores com seus próprios.

Lista de referência de configuração:
    
- Nome de rede virtual = "TestVNet"
- Espaço de endereço de rede virtual = 192.168.0.0/16
- Grupo de recursos = "TestRG"
- Subnet1 nome = "FrontEnd" 
- Espaço de endereço de Subnet1 = "192.168.0.0/16"
- Nome do gateway sub-rede: "GatewaySubnet", você sempre deve nomear uma sub-rede de gateway *GatewaySubnet*.
- Espaço de endereço do gateway sub-rede = "192.168.200.0/26"
- Região = "Leste EUA"
- Nome do gateway = "GW"
- Nome do gateway IP = "GWIP"
- Configuração de IP do gateway nome = "gwipconf"
-  Tipo = "Rota expressa" Este tipo é necessário para uma configuração de rota expressa.
- Nome do gateway público IP = "gwpip"


## <a name="add-a-gateway"></a>Adicionar um gateway

1. Conectar-se à sua assinatura do Azure. 

        Login-AzureRmAccount
        Get-AzureRmSubscription 
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

2. Declare as variáveis para este exercício. Este exemplo usará a usar as variáveis no exemplo abaixo. Certifique-se de editar esta para refletir as configurações que você deseja usar. 
        
        $RG = "TestRG"
        $Location = "East US"
        $GWName = "GW"
        $GWIPName = "GWIP"
        $GWIPconfName = "gwipconf"
        $VNetName = "TestVNet"

3. Armazene o objeto de rede virtual como uma variável.

        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG

4. Adicione uma sub-rede de gateway à sua rede Virtual. A sub-rede de gateway deve ser nomeada "GatewaySubnet". Você vai querer criar um gateway que está /27 ou maior (/ 26, / 25, etc.).
            
        Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26

5. Defina a configuração.

            Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

6. Armazene a sub-rede de gateway como uma variável.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet

7. Solicite um endereço IP público. O endereço IP é solicitado antes de criar o gateway. Você não pode especificar o endereço IP que você deseja usar; ela é alocada dinamicamente. Você usará esse endereço IP da próxima seção de configuração. O AllocationMethod deve ser dinâmico.

        $pip = New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic

8. Crie a configuração do seu gateway. A configuração do gateway define a sub-rede e o endereço IP público para usar. Nesta etapa, você está especificando a configuração que será usada quando você criar o gateway. Esta etapa não cria realmente o objeto de gateway. Use o exemplo abaixo para criar a configuração do gateway. 

        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

9. Crie o gateway. Nesta etapa, o **-GatewayType** é especialmente importante. Você deve usar o valor **rota expressa**. Observe que após a execução esses cmdlets, o gateway pode levar 20 minutos ou mais para criar.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard

## <a name="verify-the-gateway-was-created"></a>Verificar que o gateway foi criado

Use o comando a seguir para verificar se o gateway foi criado.

    Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG

## <a name="resize-a-gateway"></a>Redimensionar um gateway

Há um número de [SKUs de Gateway](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Você pode usar o comando a seguir para alterar o SKU de Gateway a qualquer momento.

>[AZURE.IMPORTANT] Este comando não funciona para UltraPerformance gateway. Para alterar seu gateway para um gateway UltraPerformance, primeiro remova o gateway rota expressa existente e, em seguida, criar um novo gateway UltraPerformance. Para fazer um downgrade seu gateway de um gateway UltraPerformance, primeiro remova o gateway UltraPerformance e, em seguida, criar um novo gateway.

    $gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

## <a name="remove-a-gateway"></a>Remover um gateway

Use o comando a seguir para remover um gateway

    Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG  
