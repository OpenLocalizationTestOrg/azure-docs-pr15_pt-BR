<properties 
    pageTitle="Provisionar um Cache relacionada | Microsoft Azure" 
    description="Use o modelo do Gerenciador de recursos do Azure para implantar um Cache do Azure relacionada." 
    services="app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="web" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/27/2016" 
    ms.author="sdanie"/>

# <a name="create-a-redis-cache-using-a-template"></a>Criar um Cache relacionada usando um modelo

Neste tópico, você saiba como criar um modelo do Gerenciador de recursos do Azure que implanta um Cache do Azure relacionada. O cache pode ser usado com uma conta de armazenamento existente para manter os dados de diagnóstico. Você também Saiba como definir quais recursos são implantados e como definir parâmetros que são especificadas quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender suas necessidades.

Atualmente, configurações de diagnóstico são compartilhadas para todos os caches na mesma região para uma assinatura. Atualizar um cache na região afeta todos os outros caches na região.

Para obter mais informações sobre a criação de modelos, consulte [Criação de modelos do Azure Gerenciador de recursos](../resource-group-authoring-templates.md).

Para o modelo completo, consulte o [modelo de Cache relacionada](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

>[AZURE.NOTE] Modelos do Gerenciador de recursos para o novo [nível de Premium](cache-premium-tier-intro.md) estão disponíveis. 
>
>-    [Criar um Cache de relacionada Premium com agrupamento](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
>-    [Criar Premium relacionada Cache com persistência de dados](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
>-    [Criar Premium relacionada Cache com VNet e agrupamento opcional](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
>
>Para verificar os modelos mais recentes, consulte [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/) e pesquise por `Redis Cache`.

## <a name="what-you-will-deploy"></a>O que você implantará

Neste modelo, você irá implantar um Cache relacionada Azure que usa uma conta existente do armazenamento de dados de diagnóstico.

Para executar a implantação automaticamente, clique no botão a seguir:

[![Implantar para o Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Gerenciador de recursos do Azure, você define parâmetros para valores que você deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada parâmetros que contém todos os valores de parâmetro.
Você deve definir um parâmetro para esses valores que variam com base no projeto que você está implantando ou baseado no ambiente do qual que você está implantando. Não defina parâmetros para valores que permanecem sempre a mesma. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados. 


[AZURE.INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation

O local do Cache relacionada. Para melhor desempenho, use o mesmo local como o aplicativo para ser usado com o cache.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName

O nome da conta de armazenamento existente para usar para diagnósticos. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort

Um valor booliano que indica se deseja permitir acesso por meio de portas não SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus

Um valor que indica se o diagnóstico está habilitado. Usar ou desative.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }
    
## <a name="resources-to-deploy"></a>Recursos para implantação

### <a name="redis-cache"></a>Relacionada Cache

Cria o Cache relacionada Azure.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-07-01",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }



## <a name="commands-to-run-deployment"></a>Comandos para executar implantação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)] 

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache -redisCacheLocation "West US"

### <a name="azure-cli"></a>CLI Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup


