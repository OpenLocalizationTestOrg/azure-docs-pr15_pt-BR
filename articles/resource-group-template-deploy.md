<properties
   pageTitle="Implantar recursos com PowerShell e modelo | Microsoft Azure"
   description="Use o Gerenciador de recursos do Azure e Azure PowerShell para implantar um recursos no Azure. Os recursos são definidos em um modelo do Gerenciador de recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Implantar recursos com modelos do Gerenciador de recursos e PowerShell do Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI Azure](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

Este tópico explica como usar o PowerShell do Azure com os modelos do Gerenciador de recursos para implantar seus recursos no Azure.  

> [AZURE.TIP] Para obter ajuda com um erro de depuração durante a implantação, consulte:
>
> - [Operações de implantação do modo de exibição com o PowerShell do Azure](resource-manager-troubleshoot-deployments-powershell.md) para saber mais sobre a obtenção de informações que ajuda você a solucionar o erro
> - [Solucionar erros comuns ao implantar recursos para Azure com o Gerenciador de recursos do Azure](resource-manager-common-deployment-errors.md) para saber como resolver erros comuns de implantação

Seu modelo pode ser um arquivo local ou um arquivo externo que está disponível por meio de um URI. Quando seu modelo reside em uma conta de armazenamento, você pode restringir o acesso ao modelo e fornecer um token de assinatura (SAS) acesso compartilhado durante a implantação.

## <a name="quick-steps-to-deployment"></a>Etapas rápidas para implantação

Este artigo descreve todas as diferentes opções disponíveis para você durante a implantação. No entanto, muitas vezes você só precisará de dois comandos simples. Para rapidamente começar com implantação, use os seguintes comandos:

    New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
    New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

Para saber mais sobre as opções de implantação que pode ser mais adequado para seu cenário, continue lendo este artigo.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-powershell"></a>Implantar com PowerShell

1. Faça logon em sua conta do Azure.

        Add-AzureRmAccount

     Um resumo da sua conta será retornado.

        Environment : AzureCloud
        Account     : someone@example.com
        ...

2. Se você tiver várias assinaturas, forneça a ID da assinatura que você deseja usar para implantação com o comando **Set-AzureRmContext** . 

        Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

3. Normalmente, ao implantar um novo modelo, você deseja criar um grupo de recursos para conter os recursos. Se você tiver um grupo de recursos existente que você deseja implantar, você pode ignorar esta etapa e usar esse grupo de recursos. 

     Para criar um grupo de recursos, forneça um nome e local para seu grupo de recursos. Você precisa fornecer um local para o grupo de recursos, porque o grupo de recursos armazena metadados sobre os recursos. Por razões de conformidade, talvez você queira especificar onde os metadados está armazenado. Em geral, recomendamos que você especifique um local onde a maioria dos seus recursos residirão. Usar o mesmo local pode simplificar o seu modelo.

        New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
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

4. Antes de executar sua implantação, você pode validar suas configurações de implantação. O cmdlet **Test-AzureRmResourceGroupDeployment** permite localizar problemas antes de criar recursos reais. O exemplo a seguir mostra como validar uma implantação.

        Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

5. Para implantar recursos em seu grupo de recursos, execute o comando **New-AzureRmResourceGroupDeployment** e forneça os parâmetros necessários. Os parâmetros incluem um nome para sua implantação, o nome do seu grupo de recursos, o caminho ou a URL para o modelo que você criou e outros parâmetros necessários para seu cenário. Se o parâmetro de **modo** não for especificado, o valor padrão de **Incremental** será usado. Para executar uma implantação completa, defina o **modo** como **concluída**. Tenha cuidado ao usar o modo completo, como você pode excluir acidentalmente recursos que não estão em seu modelo.

     Para implantar um modelo local, use o parâmetro **TemplateFile** :

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

     Para implantar um modelo externo, use o parâmetro **TemplateUri** :

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate>
   
     Você tem as seguintes opções para fornecer valores de parâmetro: 
   
     1. Use parâmetros de embutida.

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"

     2. Use um objeto de parâmetro.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters

     3. Use um arquivo de parâmetro local. Para obter informações sobre o arquivo de modelo, consulte o [arquivo de parâmetro](#parameter-file).

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

     4. Use um arquivo de parâmetro externo. Para obter informações sobre o arquivo de modelo, consulte o [arquivo de parâmetro](#parameter-file). 

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri <LinkToParameterFile>

        Quando você usa um arquivo de parâmetro externo, você não pode passar outros valores em linha ou de um arquivo local. Para obter mais informações, consulte [a precedência de parâmetro](#parameter-precendence).

     Depois que os recursos foram implantados, você verá um resumo da implantação.

        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleResourceGroup
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2015 7:00:27 PM
        Mode              : Incremental
        ...

     Se seu modelo inclui um parâmetro com o mesmo nome de um dos parâmetros no comando do PowerShell, você precisará fornecer um valor para esse parâmetro. O parâmetro no modelo de incluirá o sufixo **FromTemplate**. Por exemplo, um parâmetro denominada **ResourceGroupName** no seu modelo apresenta um conflito com o parâmetro **ResourceGroupName** no cmdlet [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) . Você será solicitado a fornecer um valor para **ResourceGroupNameFromTemplate**. Em geral, você deve evitar essa confusão nomeando não parâmetros com o mesmo nome usado para operações de implantação de parâmetros.

6. Se você quiser registrar informações adicionais sobre a implantação que pode ajudar a solucionar problemas de erros de implantação, use o parâmetro **DeploymentDebugLogLevel** . Você pode especificar que o conteúdo de solicitação, conteúdo de resposta ou ambos estar conectado com a operação de implantação.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
        
     Para obter mais informações sobre como usar esse conteúdo depuração para implantações de solução de problemas, consulte [implantações de grupo de recursos de solução de problemas com Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md).

## <a name="deploy-template-from-storage-with-sas-token"></a>Implantar o modelo de armazenamento com token SAS

Você pode adicionar seus modelos para uma conta de armazenamento e vinculá-los durante a implantação com um token SAS.

> [AZURE.IMPORTANT] Seguindo as etapas abaixo, o blob que contém o modelo é acessível para somente o proprietário da conta. No entanto, quando você cria um token SAS para o blob, o blob é acessível para qualquer pessoa com esse URI. Se outro usuário interceptar o URI, esse usuário é possível acessar o modelo. Usar um token SAS é uma boa maneira de limitar o acesso aos seus modelos, mas você não deve incluir dados confidenciais como senhas diretamente no modelo.

### <a name="add-private-template-to-storage-account"></a>Adicionar modelo particular à conta de armazenamento

As etapas a seguir configuram uma conta de armazenamento para os modelos:

1. Crie um grupo de recursos.

        New-AzureRmResourceGroup -Name ManageGroup -Location "West US"

2. Crie uma conta de armazenamento. O nome da conta de armazenamento deve ser exclusivo entre Azure, portanto, forneça seu próprio nome da conta.

        New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"

3. Defina a conta de armazenamento atual.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

4. Crie um contêiner. A permissão é definida para **Desativar** o que significa que o contêiner somente é acessível para o proprietário.

        New-AzureStorageContainer -Name templates -Permission Off
        
5. Adicione seu modelo ao contêiner.

        Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
        
### <a name="provide-sas-token-during-deployment"></a>Fornecer token SAS durante a implantação

Para implantar um modelo particular em uma conta de armazenamento, recuperar um token SAS e incluí-los no URI para o modelo.

1. Se você tiver alterado a conta de armazenamento atual, defina a conta de armazenamento atual para aquele que contém seus modelos.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

2. Crie um token SAS com permissões de leitura e uma hora de expiração para limitar o acesso. Recupere o URI completo do modelo incluindo o token SAS.

        $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

3. Implante o modelo, fornecendo o URI que inclui o token SAS.

        New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri

Para obter um exemplo do uso de um token SAS com modelos vinculados, consulte [usando modelos vinculados com o Gerenciador de recursos do Azure](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="parameter-precedence"></a>Precedência de parâmetro

Você pode usar um arquivo de parâmetro local na mesma operação implantação e parâmetros de embutido. Por exemplo, você pode especificar alguns valores no arquivo de parâmetro local e adicionar outro valores embutido durante a implantação. Se você fornecer valores para um parâmetro no arquivo de parâmetro local tanto embutido, o valor de embutida tem precedência.

No entanto, você não pode usar parâmetros de embutido com um arquivo de parâmetro externo. Quando você especifica um arquivo de parâmetro no parâmetro **TemplateParameterUri** , todos os parâmetros de embutido são ignorados. Você deve fornecer todos os valores de parâmetro no arquivo externo. Se seu modelo inclui um valor confidencial que você não pode incluir no arquivo de parâmetros, adicione esse valor para um cofre chave e referenciar o cofre chave no seu arquivo de parâmetro externo ou fornecer dinamicamente todas as embutidas de valores de parâmetro.

Para obter detalhes sobre como usar uma referência de KeyVault para passar valores seguros, consulte [passar valores seguros durante a implantação](resource-manager-keyvault-parameter.md).

## <a name="next-steps"></a>Próximas etapas
- Para um exemplo de implantação de recursos por meio da biblioteca de cliente .NET, consulte [implantar recursos usando bibliotecas .NET e um modelo](virtual-machines/virtual-machines-windows-csharp-template.md).
- Para definir parâmetros no modelo, consulte [modelos de criação de páginas](resource-group-authoring-templates.md#parameters).
- Para obter orientações sobre como implantar sua solução para diferentes ambientes, consulte [ambientes de desenvolvimento e teste no Microsoft Azure](solution-dev-test-environments.md).

