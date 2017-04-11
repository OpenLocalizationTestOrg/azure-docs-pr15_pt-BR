<properties
    pageTitle="Criar um namespace Hubs de evento com Hub de evento e consumidor grupo usando um modelo do Gerenciador de recursos do Azure | Microsoft Azure"
    description="Criar um namespace Hubs de evento com Hub de evento e grupo consumidor usando o modelo do Gerenciador de recursos do Azure"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/31/2016"
    ms.author="sethm;shvija"/>

# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Criar um namespace Hubs de evento com Hub de evento e consumidor grupo usando um modelo do Gerenciador de recursos do Azure

Este artigo mostra como usar um modelo do Gerenciador de recursos do Azure que cria um namespace Hubs de evento com um Hub de evento e um grupo de consumidor. Você aprenderá como definir quais recursos são implantados e como definir parâmetros que são especificadas quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender suas necessidades

Para obter mais informações sobre a criação de modelos, consulte [modelos de coautoria Gerenciador de recursos do Azure][].

Para o modelo completo, consulte o [Hub de evento e o modelo de grupo do consumidor][] no GitHub.

>[AZURE.NOTE]
>Para verificar os modelos mais recentes, visite a Galeria de [Modelos de início rápido do Azure][] e procure por Hubs de evento.

## <a name="what-will-you-deploy"></a>O que você implantará?

Com este modelo, você irá implantar um namespace Hubs de evento com um Hub de evento e um grupo de consumidor.

[Hubs de evento](../event-hubs/event-hubs-what-is-event-hubs.md) for um evento de serviço usado para fornecer o evento e telemetria ingresso ao Azure grande, em escala com alta confiabilidade e baixa latência de processamento.

Para executar a implantação automaticamente, clique no botão a seguir:

[![Implantar para o Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Gerenciador de recursos do Azure, você define parâmetros para valores que você deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada `Parameters` que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam com base no projeto que você está implantando ou baseado no ambiente do qual que você está implantando. Não defina parâmetros para valores que sempre permanecerá o mesmo. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

O modelo define os parâmetros a seguir.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

O nome do namespace Hubs de evento para criar.

```
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName

O nome do Hub evento criado em eventos Hubs namespace.

```
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName

O nome do grupo de consumidor criado para o Hub de evento.

```
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion

A versão da API do modelo.

```
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Recursos para implantação

Cria um namespace do tipo **EventHubs**, com um Hub de evento e um grupo de consumidor.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="commands-to-run-deployment"></a>Comandos para executar implantação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>CLI Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

[Criação de modelos do Gerenciador de recursos do Azure]: ../resource-group-authoring-templates.md
[Modelos de início rápido Azure]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Modelo de grupo de Hub e consumidores de evento]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
