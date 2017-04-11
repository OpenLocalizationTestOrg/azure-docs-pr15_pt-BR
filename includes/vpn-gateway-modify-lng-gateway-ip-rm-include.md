Para modificar o endereço IP do gateway, use o `New-AzureRmVirtualNetworkGatewayConnection` cmdlet. Como manter o nome do gateway rede local exatamente como o nome existente, substituirá as configurações. No momento, o cmdlet "Definir" não suporta modificando o endereço IP do gateway.

### <a name="gwipnoconnection"></a>Como modificar o endereço IP do gateway - nenhuma conexão de gateway

Para atualizar o endereço IP do gateway para o gateway de rede local que ainda não tiver uma conexão, use o exemplo a seguir. Você também pode atualizar os prefixos de endereço ao mesmo tempo. As configurações que você especifica substituirá as configurações existentes. Certifique-se de usar o nome existente do seu gateway de rede local. Se não estiver, você criará um novo gateway de rede local, substituindo não existente.

Use o exemplo a seguir, substituindo os valores para seu próprio.

    New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
    -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
    -GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName


### <a name="gwipwithconnection"></a>Como modificar o endereço IP do gateway - existentes conexão de gateway

Se uma conexão de gateway já existir, você precisará primeiro remover a conexão. Em seguida, você pode modificar o endereço IP do gateway e recriar uma nova conexão. Isso resultará em algum tempo de inatividade para sua conexão de VPN.


>[AZURE.IMPORTANT] Não exclua o gateway VPN. Se você fizer isso, você precisará voltar as etapas para criá-lo novamente, bem como reconfigurar o roteador local com o endereço IP que será atribuído ao gateway recém-criado.
 

1. Remova a conexão. Você pode encontrar o nome da sua conexão usando o `Get-AzureRmVirtualNetworkGatewayConnection` cmdlet.

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName

2. Modificar o valor de GatewayIpAddress. Você também pode modificar seu prefixos de endereço no momento, se necessário. Observe que isso substituirá as configurações de gateway de rede local existentes. Use o nome existente do seu gateway de rede local ao modificar para que as configurações substituirá. Se não estiver, você criará um novo gateway de rede local, não modificando existente.

        New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
        -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
        -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName

3. Crie a conexão. Neste exemplo, estamos configurando um tipo de conexão IPsec. Quando você recria sua conexão, use o tipo de conexão que está especificado para sua configuração. Para tipos de conexão adicionais, consulte a página de [cmdlet do PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .  Para obter o nome de VirtualNetworkGateway, você pode executar o `Get-AzureRmVirtualNetworkGateway` cmdlet.

    Defina as variáveis:

        $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName

    Crie a conexão:
    
        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
        -Location "West US" `
        -VirtualNetworkGateway1 $vnetgw `
        -LocalNetworkGateway2 $local `
        -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

