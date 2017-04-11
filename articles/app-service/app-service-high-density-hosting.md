<properties
    pageTitle="Alta densidade de hospedagem no serviço de aplicativo do Azure | Microsoft Azure"
    description="Alta densidade de hospedagem no serviço de aplicativo do Azure"
    authors="btardif"
    manager="wpickett"
    editor=""
    services="app-service\web"
    documentationCenter=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="byvinyal"/>

# <a name="high-density-hosting-on-azure-app-service"></a>Alta densidade de hospedagem no serviço de aplicativo do Azure

Ao usar o serviço de aplicativo, seu aplicativo é desassociado da capacidade alocada por dois conceitos:

- **o aplicativo:** Representa o aplicativo e sua configuração de tempo de execução. Por exemplo, ele inclui a versão do .NET runtime deve ser carregados, as configurações do aplicativo, etc.

- **o plano de serviço de aplicativo:** Define as características da capacidade, o conjunto de recursos disponíveis e a localidade do aplicativo. Por exemplo, características podem ser grande máquina (quatro cores), quatro instâncias, os recursos Premium do Leste EUA.

Um aplicativo sempre está vinculado a um plano de serviço de aplicativo, mas um plano de serviço de aplicativo pode fornecer a capacidade de um ou mais aplicativos.

Como resultado, a plataforma fornece flexibilidade para isolar um único aplicativo ou tiver vários aplicativos compartilhar recursos compartilhando um plano de serviço de aplicativo.

No entanto, quando vários aplicativos compartilham um plano de serviço de aplicativo, uma instância do que o aplicativo é executado em cada instância desse plano de serviço de aplicativo.

## <a name="per-app-scaling"></a>Por aplicativo dimensionamento
*Por aplicativo dimensionamento* é um recurso que pode ser habilitado no nível de plano de serviço de aplicativo e, em seguida, usado por aplicativo.

Por aplicativo dimensionamento escalas de um aplicativo independentemente do plano de serviço de aplicativo que hospeda-lo. Dessa forma, um plano de serviço de aplicativo pode ser configurado para fornecer 10 instâncias, mas um aplicativo pode ser definido para dimensionar a apenas 5-las.

O modelo de Gerenciador de recursos do Azure a seguir cria um plano de serviço de aplicativo que está dimensionado para 10 instâncias e um aplicativo que está configurado para usar por aplicativo dimensionamento e dimensionar a apenas 5 instâncias.

O plano de serviço de aplicativo está definindo a propriedade **por site dimensionamento** para true ( `"perSiteScaling": true`). O aplicativo está definindo o **número de trabalhadores** usar para 5 (`"properties": { "numberOfWorkers": "5" }`).

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters":{
            "appServicePlanName": { "type": "string" },
            "appName": { "type": "string" }
         },
        "resources": [
        {
            "comments": "App Service Plan with per site perSiteScaling = true",
            "type": "Microsoft.Web/serverFarms",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "size": "S1",
                "family": "S",
                "capacity": 10
                },
            "name": "[parameters('appServicePlanName')]",
            "apiVersion": "2015-08-01",
            "location": "West US",
            "properties": {
                "name": "[parameters('appServicePlanName')]",
                "perSiteScaling": true
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[parameters('appName')]",
            "apiVersion": "2015-08-01-preview",
            "location": "West US",
            "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
            "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
            "resources": [ {
                    "comments": "",
                    "type": "config",
                    "name": "web",
                    "apiVersion": "2015-08-01",
                    "location": "West US",
                    "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                    "properties": { "numberOfWorkers": "5" }
             } ]
         }]
    }


## <a name="recommended-configuration-for-high-density-hosting"></a>Configuração recomendada para a hospedagem de alta densidade

Por aplicativo dimensionamento é um recurso que está habilitado em ambientes de serviço de aplicativo e regiões Azure públicos. No entanto, a estratégia recomendada é usar ambientes de serviço de aplicativo para aproveitar seus recursos avançados e os pools maiores de capacidade.  

Siga estas etapas para configurar alta densidade hospedando para seus aplicativos:

1. Configurar o ambiente de serviço de aplicativo e escolha um pool de trabalhador dedicado para o cenário de hospedagem de alta densidade.

1. Criar um plano de serviço de aplicativo único e dimensioná-lo para usar toda a capacidade disponível no pool de trabalho.

1. Defina o sinalizador de dimensionamento por site como true no plano de serviço de aplicativo.

1. Novos sites são criados e atribuídos a esse plano de serviço de aplicativo com a propriedade de **numberOfWorkers** definida como **1**. Usando essa configuração produz a maior densidade possível neste pool de trabalho.

1. O número de trabalhadores pode ser configurado independentemente por site conceder recursos adicionais conforme necessário. Por exemplo, um site de uso intenso pode definir **numberOfWorkers** para **3** para ter mais capacidade de processamento para esse aplicativo, enquanto sites de baixo uso defina **numberOfWorkers** como **1**.
