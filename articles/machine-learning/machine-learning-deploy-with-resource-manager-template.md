<properties
    pageTitle="Implantar o espaço de trabalho usando o modelo do Azure Gerenciador de recursos de aprendizado de máquina | Microsoft Azure"
    description="Como implantar um espaço de trabalho para aprendizado de máquina do Azure usando o modelo do Gerenciador de recursos do Azure"
    services="machine-learning"
    documentationCenter=""
    authors="ahgyger"
    manager="haining"
    editor="garye"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="ahgyger"/>
# <a name="deploy-machine-learning-workspace-using-azure-resource-manager"></a>Implantar o espaço de trabalho usando o Azure Gerenciador de recursos de aprendizado de máquina

## <a name="introduction"></a>Introdução
Usando um Gerenciador de recursos do Azure modelo de implantação economiza tempo, dando a você uma maneira scalable de implantar componentes interconectados com uma validação e mecanismo de repetição. Para configurar espaços de trabalho de aprendizado de máquina Azure, por exemplo, é necessário primeiro configurar uma conta de armazenamento do Azure e implantar o seu espaço de trabalho. Imagine isso manualmente para centenas de espaços de trabalho. Uma alternativa mais fácil é usar um modelo do Gerenciador de recursos do Azure para implantar um espaço de trabalho de aprendizado de máquina do Azure e todas as suas dependências. Este artigo o guiará durante este processo passo a passo. Para uma ótima visão geral do Gerenciador de recursos do Azure, consulte [Visão geral do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Passo a passo: criar um espaço de trabalho de aprendizado de máquina
Podemos irá criar um grupo de recursos do Azure e implantar uma nova conta de armazenamento do Azure e um novo Azure máquina aprendizagem espaço de trabalho usando um modelo do Gerenciador de recursos. Uma vez concluída a implantação, podemos será impresso informações importantes sobre os espaços de trabalho que foram criados (a chave primária, o workspaceID e a URL para o espaço de trabalho).

### <a name="create-an-azure-resource-manager-template"></a>Criar um modelo do Gerenciador de recursos do Azure
Um espaço de trabalho de aprendizado de máquina requer uma conta de armazenamento do Azure para armazenar o conjunto de dados vinculado a ele.
O modelo a seguir usa o nome do grupo de recursos para gerar o nome da conta de armazenamento e o nome do espaço de trabalho.  Além disso, ele também usa o nome da conta de armazenamento como uma propriedade ao criar o espaço de trabalho.

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Salve este modelo como arquivo de mlworkspace.json em c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Implantar o grupo de recursos, com base no modelo
* Abrir o PowerShell
* Instalar módulos do Gerenciador de recursos do Azure e gerenciamento de serviços do Azure  

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Estas etapas Baixe e instale os módulos necessários para concluir as etapas restantes. Isso só precisa ser feito uma vez no ambiente de onde você está executando os comandos do PowerShell.   

* Autenticar Azure  

```
# Authenticate (enter your credentials in the pop-up window)
Add-AzureRmAccount
```
Esta etapa deve ser repetido para cada sessão. Uma vez autenticado, suas informações de assinatura devem ser exibidas.

![Conta do Azure][1]

Agora que temos acesso ao Azure, podemos criar o grupo de recursos.

* Criar um grupo de recursos

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Verifique se o grupo de recursos está provisionado corretamente. **ProvisioningState** deve ser "bem-sucedida."
O nome do grupo de recursos é usado pelo modelo para gerar o nome da conta de armazenamento. O nome da conta de armazenamento deve estar entre 3 e 24 caracteres e use números e apenas letras minúsculas.

![Grupo de recursos][2]

* Usando a implantação do grupo de recursos, implante um novo espaço de trabalho de aprendizado de máquina.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Depois que a implantação for concluída, é simples tenha acesso às propriedades do espaço de trabalho que você implantado. Por exemplo, você pode acessar o Token de chave primária.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Outra maneira de recuperar tokens de espaço de trabalho existente é usar o comando de chamar AzureRmResourceAction. Por exemplo, você pode listar os tokens primárias e secundários de todos os espaços de trabalho.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
Após o espaço de trabalho está provisionado, você também pode automatizar muitas tarefas de Studio de aprendizado de máquina do Azure usando o [Módulo do PowerShell para aprendizado de máquina do Azure](http://aka.ms/amlps).

## <a name="next-steps"></a>Próximas etapas 
* Saiba mais sobre [Modelos de Gerenciador de recursos de criação do Azure](../resource-group-authoring-templates.md). 
* Dê uma olhada no [Repositório de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates). 
* Assista a este vídeo sobre [O Gerenciador de recursos do Azure](https://channel9.msdn.com/Events/Ignite/2015/C9-39). 
 
<!--Image references-->
[1]: ../media/machine-learning-deploy-with-resource-manager-template/azuresubscription.png
[2]: ../media/machine-learning-deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->
