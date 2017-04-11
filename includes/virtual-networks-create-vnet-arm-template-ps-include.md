## <a name="deploy-the-arm-template-by-using-powershell"></a>Implantar o modelo ARM usando o PowerShell

Para implantar o modelo ARM que você baixou usando o PowerShell, siga as etapas abaixo.

1. Se você nunca usou o PowerShell do Azure, veja [como instalar e configurar o Azure PowerShell](../articles/powershell-install-configure.md) e siga as instruções totalmente ao final para entrar no Azure e selecione sua assinatura.

3. Se necessário, execute o **`New-AzureRmResourceGroup`** cmdlet para criar um novo grupo de recursos. O comando a seguir cria um grupo de recursos denominado *TestRG* na região *Centro dos EUA* azure. Para obter mais informações sobre os grupos de recursos, visite [Visão geral do Gerenciador de recursos do Azure](../articles/resource-group-overview.md).

        New-AzureRmResourceGroup -Name TestRG -Location centralus
        
    Aqui está a saída esperada para o comando acima:

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

4. Executar o **`New-AzureRmResourceGroupDeployment`** cmdlet para implantar o novo VNet usando o modelo e o parâmetro arquivos que você baixou e modificado acima.

        New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
            -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
            
    Aqui está a saída esperada para o comando acima:
        
        DeploymentName    : TestVNetDeployment
        ResourceGroupName : TestRG
        ProvisioningState : Succeeded
        Timestamp         : 8/14/2015 9:40:00 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            location         String                     Central US
                            vnetName         String                     TestVNet
                            addressPrefix    String                     192.168.0.0/16
                            subnet1Prefix    String                     192.168.1.0/24
                            subnet1Name      String                     FrontEnd
                            subnet2Prefix    String                     192.168.2.0/24
                            subnet2Name      String                     BackEnd
        
        Outputs           :

5. Executar o **`Get-AzureRmVirtualNetwork`** cmdlet para exibir as propriedades de VNet a nova, conforme mostrado abaixo.


        Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
        
    Aqui está a saída esperada para o comando acima:
        
        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]