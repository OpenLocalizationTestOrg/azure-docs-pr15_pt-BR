<properties 
    pageTitle="Implantar um aplicativo web que esteja vinculado a um repositório GitHub" 
    description="Use um modelo do Gerenciador de recursos do Azure para implantar um aplicativo web que contenha um projeto de um repositório GitHub." 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/27/2016" 
    ms.author="cephalin"/>

# <a name="deploy-a-web-app-linked-to-a-github-repository"></a>Implantar um aplicativo web vinculado a um repositório GitHub

Neste tópico, você aprenderá a criar um modelo do Gerenciador de recursos do Azure que implante um aplicativo web que esteja vinculado a um projeto em um repositório GitHub. Você aprenderá como definir quais recursos são implantados e como definir parâmetros que são especificadas quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender suas necessidades.

Para obter mais informações sobre a criação de modelos, consulte [Criação de modelos do Azure Gerenciador de recursos](../resource-group-authoring-templates.md).

Para o modelo completo, consulte [Web App vinculado ao modelo de GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.json).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="what-you-will-deploy"></a>O que você implantará

Com este modelo, você irá implantar um aplicativo web que contém o código de um projeto em GitHub.

Para executar a implantação automaticamente, clique no botão a seguir:

[![Implantar para o Azure](./media/app-service-web-arm-from-github-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-github-deploy%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### <a name="repourl"></a>repoURL

A URL do repositório de GitHub que contém o projeto para implantar. Este parâmetro contém um valor padrão, mas esse valor destina-se apenas a mostrar como fornecer a URL para o repositório. Você pode usar esse valor ao testar o modelo, mas você desejará fornecer a URL do seu próprio repositório ao trabalhar com o modelo.

    "repoURL": {
        "type": "string",
        "defaultValue": "https://github.com/davidebbo-test/Mvc52Application.git"
    }

### <a name="branch"></a>ramificação

A ramificação do repositório para usar quando implantar o aplicativo. O valor padrão é mestre, mas você pode fornecer o nome de qualquer ramificação no repositório que você deseja implantar.

    "branch": {
        "type": "string",
        "defaultValue": "master"
    }
    
## <a name="resources-to-deploy"></a>Recursos para implantação

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="web-app"></a>Aplicativo Web

Cria o aplicativo da web que esteja vinculado ao projeto no GitHub. 

Você especificar o nome do aplicativo web por meio do parâmetro de **nome do site** e o local do aplicativo web por meio do parâmetro **siteLocation** . No elemento **dependsOn** , o modelo define o aplicativo web como dependentes do serviço de plano de hospedagem. Porque ele é dependente no plano de hospedagem, o web app não é criado até que o plano de hospedagem terminou sendo criado. O elemento de **dependsOn** só é usado para especificar a ordem de implantação. Se você não marcar o aplicativo web como depende do plano de hospedagem, Azure recurso gerenciado tentará criar os dois recursos ao mesmo tempo, e você poderá receber um erro se o aplicativo web é criado antes do plano de hospedagem.

O aplicativo web também tem um recurso de filho que é definido na seção **recursos** abaixo. Este recurso filho define o controle de origem para o projeto implantado com o aplicativo web. Neste modelo, o controle de origem está vinculado a um determinado repositório GitHub. Repositório GitHub é definido com o código **"RepoUrl": "https://github.com/davidebbo-test/Mvc52Application.git"** você pode embutir a URL do repositório quando você deseja criar um modelo que implanta repetidamente um único projeto exigindo o número mínimo de parâmetros.
Em vez de embutir a URL do repositório, você pode adicionar um parâmetro para a URL do repositório e usar esse valor para a propriedade de **RepoUrl** .

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('repoURL')]",
            "branch": "[parameters('branch')]",
            "IsManualIntegration": true
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>Comandos para executar implantação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>CLI Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json


 
