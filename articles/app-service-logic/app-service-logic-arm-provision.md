<properties 
    pageTitle="Criar um aplicativo de lógica usando modelos do Gerenciador de recursos do Azure no serviço de aplicativo do Azure | Microsoft Azure" 
    description="Use um modelo do Gerenciador de recursos do Azure para implantar um aplicativo de lógica vazia para definir fluxos de trabalho." 
    services="logic-apps" 
    documentationCenter="" 
    authors="MSFTMan" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/25/2016" 
    ms.author="deonhe"/>

# <a name="create-a-logic-app-using-a-template"></a>Criar um aplicativo de lógica usando um modelo

Use um modelo do Gerenciador de recursos do Azure para criar um aplicativo de lógica vazio que pode ser usado para definir fluxos de trabalho. Você pode definir quais recursos são implantados e como definir parâmetros especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender suas necessidades.

Para obter mais detalhes sobre as propriedades de aplicativo lógica, consulte [API de gerenciamento de fluxo de trabalho de aplicativo de lógica](https://msdn.microsoft.com/library/azure/mt643788.aspx). 

Para obter exemplos da definição do próprio, consulte [definições de aplicativo de lógica de autor](app-service-logic-author-definitions.md). 

Para obter mais informações sobre a criação de modelos, consulte [Criação de modelos do Azure Gerenciador de recursos](../resource-group-authoring-templates.md).

Para o modelo completo, consulte o [modelo de aplicativo de lógica](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## <a name="what-you-will-deploy"></a>O que você implantará

Com este modelo, você implantar um aplicativo de lógica.

Para executar a implantação automaticamente, selecione o botão a seguir:  

[![Implantar para o Azure](media/app-service-logic-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### <a name="testuri"></a>testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
      }
    
## <a name="resources-to-deploy"></a>Recursos para implantação

### <a name="logic-app"></a>Aplicativo de lógica

Cria o aplicativo de lógica.

Os modelos usa um valor de parâmetro para o nome do aplicativo de lógica. Ele define o local do aplicativo lógica no mesmo local como o grupo de recursos. 

Essa definição de determinado executa uma vez por hora e ping no local especificado no parâmetro **testUri** . 

    {
      "type": "Microsoft.Logic/workflows",
      "apiVersion": "2016-06-01",
      "name": "[parameters('logicAppName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "LogicApp"
      },
      "properties": {
        "definition": {
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      }
    }


## <a name="commands-to-run-deployment"></a>Comandos para executar implantação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>CLI Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup


 
