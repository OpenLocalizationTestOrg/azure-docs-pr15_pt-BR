### <a name="noconnection"></a>Como adicionar ou remover prefixos - nenhuma conexão de gateway

- **Para adicionar** prefixos de endereço adicionais para um local de rede gateway que você criou, mas que não ainda tem uma conexão de gateway, use o exemplo abaixo. Certifique-se de alterar os valores para o seu próprio.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

- **Para remover** um prefixo de endereço de um gateway de rede local que não tem uma conexão de VPN, use o exemplo a seguir. Deixe os prefixos que você não precisa mais. Neste exemplo, nós já não precisa prefixar 20.0.0.0/24 (do exemplo anterior), portanto atualizaremos local gateway de rede e excluir esse prefixo.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="withconnection"></a>Como adicionar ou remover prefixos - existentes conexão de gateway

Se você criou sua conexão de gateway e deseja adicionar ou remover os prefixos de endereço IP contidos no seu gateway de rede local, você precisará fazer as seguintes etapas na ordem. Isso resultará em algum tempo de inatividade para sua conexão de VPN. Ao atualizar seu prefixos, você deverá primeiro remover a conexão, modificar os prefixos e, em seguida, criar uma nova conexão. Nos exemplos a seguir, certifique-se de alterar os valores para o seu próprio.

>[AZURE.IMPORTANT] Não exclua o gateway VPN. Se você fizer isso, você precisará voltar as etapas para criá-lo novamente, bem como reconfigurar o roteador local com as novas configurações.
 
1. Remova a conexão.

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName

2. Modifique os prefixos de endereço do seu gateway de rede local.

    Defina a variável para o LocalNetworkGateway.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName

    Modifique os prefixos.

        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

4. Crie a conexão. Neste exemplo, estamos configurando um tipo de conexão IPsec. Quando você recria sua conexão, use o tipo de conexão que está especificado para sua configuração. Para tipos de conexão adicionais, consulte a página de [cmdlet do PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .

    Defina a variável para o VirtualNetworkGateway.

        $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName

    Crie a conexão. Observe que este exemplo usa a variável $local definido por você na etapa anterior.


        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName -Location 'West US' `
        -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
        -ConnectionType IPsec `
        -RoutingWeight 10 -SharedKey 'abc123'
