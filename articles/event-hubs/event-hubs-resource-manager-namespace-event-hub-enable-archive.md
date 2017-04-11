<properties
    pageTitle="Criar um namespace Hubs de evento com Hub de evento e habilitar o arquivamento usando um modelo do Gerenciador de recursos do Azure | Microsoft Azure"
    description="Criar um namespace Hubs de evento com Hub de evento e habilitar o arquivamento usando o modelo do Gerenciador de recursos do Azure"
    services="event-hubs"
    documentationCenter=".net"
    authors="ShubhaVijayasarathy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="09/14/2016"
    ms.author="ShubhaVijayasarathy"/>

# <a name="create-an-event-hubs-namespace-with-event-hub-and-enable-archive-using-an-azure-resource-manager-template"></a>Criar um namespace Hubs de evento com Hub de evento e habilitar o arquivamento usando um modelo do Gerenciador de recursos do Azure

Este artigo mostra como usar um modelo do Gerenciador de recursos do Azure que cria um namespace Hubs de evento com um Hub de evento e permite que o arquivo morto em seu Hub de evento. Você saiba como definir quais recursos são implantados e como definir parâmetros que são especificadas quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender suas necessidades

Para obter mais informações sobre a criação de modelos, consulte [modelos de coautoria Gerenciador de recursos do Azure][].

Para obter mais informações sobre prática e padrões em recursos do Azure convenções de nomenclatura, consulte [Convenções de nomenclatura de recursos do Azure][].

Para o modelo completo, consulte o [Hub de evento e habilitar o modelo de arquivo morto][] no GitHub.

>[AZURE.NOTE]
>Para verificar os modelos mais recentes, visite a Galeria de [Modelos de início rápido do Azure][] e procure por Hubs de evento.

## <a name="what-you-deploy"></a>O que você implanta?

Com este modelo, você implantar um namespace Hubs de evento com um Hub de evento e habilitar o arquivo morto.

[Hubs de evento](../event-hubs/event-hubs-what-is-event-hubs.md) for um evento de serviço usado para fornecer o evento e telemetria ingresso ao Azure grande, em escala com alta confiabilidade e baixa latência de processamento. Arquivo morto de Hubs de evento permitirá que você fornecer automaticamente os dados de streaming em seu Hubs de evento ao armazenamento de Blob do Azure de sua escolha dentro de um tempo especificado ou o intervalo de tamanho de sua escolha.

Para executar a implantação automaticamente, clique no botão a seguir:

[![Implantar para o Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-archive%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Gerenciador de recursos do Azure, você define parâmetros para valores que você deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada `Parameters` que contém todos os valores de parâmetro. Você deve definir um parâmetro para esses valores que variam com base no projeto que você está implantando ou baseado no ambiente do qual que você está implantando. Não defina parâmetros para valores que permanecem sempre a mesma. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

O modelo define os parâmetros a seguir.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

O nome do namespace Hubs de evento para criar.

```
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

O nome do Hub evento criado em eventos Hubs namespace.

```
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the Event Hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

O número de dias que você quer que as mensagens mantidas no seu Hub de evento. 

```
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in Event Hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

O número de partições que desejar no seu Hub de evento.

```
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="archiveenabled"></a>archiveEnabled

Habilite o arquivamento em seu Hub de evento.

```
"archiveEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Archive for your Event Hub"
    }
 }
```
### <a name="archiveencodingformat"></a>archiveEncodingFormat

O formato de codificação que você especificar para serializar os dados do evento.

```
"archiveEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format Archive serializes the EventData"
    }
}
```

### <a name="archivetime"></a>archiveTime

O intervalo de tempo em que o arquivo morto inicia o arquivamento dos dados no armazenamento de blob do Microsoft Azure.

```
"archiveTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"the time window in seconds for the archival"
    }
}
```

### <a name="archivesize"></a>archiveSize

O intervalo de tamanho no qual o arquivo morto começa arquivamento dos dados no armazenamento de blob do Microsoft Azure.

```
"archiveSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"the size window in bytes for archival"
    }
}
```

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

O arquivo morto requer uma id de recurso com a conta de armazenamento, para habilitar o arquivo morto para o seu armazenamento do Azure desejado.

```
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Account resource id where you want the blobs be archived"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

O contêiner de blob onde deseja que seus dados de evento ser arquivados.

```
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Container that you want the blobs archived in"
    }
}
```


### <a name="apiversion"></a>apiVersion

A versão da API do modelo.

```
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

## <a name="resources-to-deploy"></a>Recursos para implantação

Cria um namespace do tipo **EventHubs**, com um Hub de evento e permite que o arquivo morto.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
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
                  "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]",
                  "MessageRetentionInDays":"[parameters('messageRetentionInDays')]",
                  "PartitionCount":"[parameters('partitionCount')]",
                  "ArchiveDescription":{
                        "enabled":"[parameters('archiveEnabled')]",
                        "encoding":"[parameters('archiveEncodingFormat')]",
                        "intervalInSeconds":"[parameters('archiveTime')]",
                        "sizeLimitInBytes":"[parameters('archiveSize')]",
                        "destination":{
                            "name":"EventHubArchive.AzureBlockBlob",
                            "properties":{
                                "StorageAccountResourceId":"[parameters('destinationStorageAccountResourceId')]",
                                "BlobContainer":"[parameters('blobContainerName')]"
                            }
                        } 
                  }
                  
               }
               
            }
         ]
      }
   ]
```

## <a name="commands-to-run-deployment"></a>Comandos para executar implantação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json
```

## <a name="azure-cli"></a>CLI Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json][]
```

[Criação de modelos do Gerenciador de recursos do Azure]: ../resource-group-authoring-templates.md
[Modelos de início rápido Azure]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event Hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-eventhubs-create-namespace-and-enable-archive/
[Recursos Azure convenções de nomenclatura]: https://azure.microsoft.com/en-us/documentation/articles/guidance-naming-conventions/
[Hub de evento e habilitar o modelo de arquivo morto]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-archive
