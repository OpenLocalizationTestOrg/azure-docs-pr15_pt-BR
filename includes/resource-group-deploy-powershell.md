## <a name="how-to-deploy-with-powershell"></a>Como implantar com PowerShell

1. Faça login em sua conta do Azure.

          Add-AzureAccount

   Depois de fornecer suas credenciais, o comando retorna informações sobre sua conta.

          Id                             Type       ...
          --                             ----    
          example@contoso.com            User       ...   

2. Se você tiver várias assinaturas, forneça a identificação de assinatura que você deseja usar para implantação. 

          Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Alterne para o módulo do Gerenciador de recursos do Azure.

          Switch-AzureMode AzureResourceManager

4. Se você não tiver um grupo de recursos existentes, crie um novo grupo de recursos. Fornece o nome do grupo de recursos e local que você precisa para sua solução.

        New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"

   Um resumo do novo grupo de recursos é retornado.

        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Para criar uma nova implantação para seu grupo de recursos, execute o comando **New-AzureResourceGroupDeployment** e forneça os parâmetros necessários. Os parâmetros incluirá um nome para sua implantação, o nome do seu grupo de recursos, o caminho ou a URL para o modelo que você criou e outros parâmetros necessários para seu cenário. 
   
   Você tem as seguintes opções para fornecer valores de parâmetro: 
   
   - Use parâmetros de embutida.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

   - Use um objeto de parâmetro.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

   - Usando um arquivo de parâmetro.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

  Quando o grupo de recursos tiver sido implantado, você verá um resumo da implantação.

             DeploymentName    : ExampleDeployment
             ResourceGroupName : ExampleResourceGroup
             ProvisioningState : Succeeded
             Timestamp         : 4/14/2015 7:00:27 PM
             Mode              : Incremental
             ...

6. Para obter informações sobre falhas de implantação.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. Para obter informações detalhadas sobre falhas de implantação.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput
