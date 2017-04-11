## <a name="how-to-create-a-vnet-using-powershell"></a>Como criar um VNet usando o PowerShell
Para criar um VNet usando o PowerShell, siga as etapas abaixo.

1. Se você nunca usou o PowerShell do Azure, veja [como instalar e configurar o Azure PowerShell](../articles/powershell-install-configure.md) e siga as instruções totalmente ao final para entrar no Azure e selecione sua assinatura.
    
2. Se necessário, crie um novo grupo de recursos, conforme mostrado abaixo. Para o nosso cenário, crie um grupo de recursos denominado *TestRG*. Para obter mais informações sobre os grupos de recursos, visite [Visão geral do Gerenciador de recursos do Azure](../articles/resource-group-overview.md).

        New-AzureRmResourceGroup -Name TestRG -Location centralus

    Saída esperada:
    
        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG   

3. Crie um novo VNet chamado *TestVNet*, conforme mostrado abaixo.

        New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
            -AddressPrefix 192.168.0.0/16 -Location centralus   
        
    Saída esperada:

        Name                : TestVNet
        ResourceGroupName   : TestRG
        Location            : centralus
        Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState       : Succeeded
        Tags                    : 
        AddressSpace            : {
                                   "AddressPrefixes": [
                                     "192.168.0.0/16"
                                   ]
                                 }
        DhcpOptions             : {}
        Subnets                 : []
        VirtualNetworkPeerings  : []

4. Armazene o objeto de rede virtual em uma variável, conforme mostrado abaixo.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    
    >[AZURE.TIP] Você pode combinar as etapas 3 e 4 executando **$vnet = New-AzureRmVirtualNetwork - ResourceGroupName TestRG-nome TestVNet - AddressPrefix 192.168.0.0/16-local centralus**.

5. Adicione uma sub-rede à nova variável VNet, conforme mostrado abaixo.

        Add-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd `
            -VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
        
    Saída esperada:

        Name                : TestVNet
        ResourceGroupName   : TestRG
        Location            : centralus
        Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState   : Succeeded
        Tags                :
        AddressSpace        : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions         : {}
        Subnets             : [
                                  {
                                    "Name": "FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24"
                                  }
                                ]
        VirtualNetworkPeerings  : []

6. Repita a etapa 5 acima para cada sub-rede que você deseja criar. O comando a seguir cria a sub-rede de *back-end* para nosso cenário.

        Add-AzureRmVirtualNetworkSubnetConfig -Name BackEnd `
            -VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24

7. Embora você criar sub-redes, eles só existem atualmente na variável local usada para recuperar o VNet que você criar na etapa 4 acima. Para salvar as alterações do Azure, execute o cmdlet **Set-AzureRmVirtualNetwork** , conforme mostrado abaixo.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet 
        
    Saída esperada:

        Name                : TestVNet
        ResourceGroupName   : TestRG
        Location            : centralus
        Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState   : Succeeded
        Tags                :
        AddressSpace        : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions         : {
                                  "DnsServers": []
                                }
        Subnets             : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  },
                                  {
                                    "Name": "BackEnd",
                                    "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                    "AddressPrefix": "192.168.2.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  }
                                ]
        VirtualNetworkPeerings : []
