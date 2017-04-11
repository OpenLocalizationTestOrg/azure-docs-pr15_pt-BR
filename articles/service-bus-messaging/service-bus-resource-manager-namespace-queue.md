<properties
    pageTitle="Criar um namespace barramento de serviço com fila usando um modelo do Gerenciador de recursos do Azure | Microsoft Azure"
    description="Criar um namespace barramento de serviço e uma fila usando o modelo do Gerenciador de recursos do Azure"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/14/2016"
    ms.author="sethm;shvija"/>

# <a name="create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Criar um namespace barramento de serviço e uma fila usando um modelo do Gerenciador de recursos do Azure

Este artigo mostra como usar um modelo do Gerenciador de recursos do Azure que cria um namespace barramento de serviço e uma fila. Você aprenderá como definir quais recursos são implantados e como definir parâmetros que são especificadas quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender suas necessidades.

Para obter mais informações sobre a criação de modelos, consulte [modelos de coautoria Gerenciador de recursos do Azure][].

Para o modelo completo, consulte o [modelo de namespace e fila do barramento de serviço][] no GitHub.

>[AZURE.NOTE] Os modelos de Gerenciador de recursos do Azure a seguir estão disponíveis para download e implantação.
>
>-    [Criar um namespace barramento de serviço com fila e a autorização da regra](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Criar um namespace barramento de serviço com o tópico e assinatura](service-bus-resource-manager-namespace-topic.md)
>-    [Criar um namespace barramento de serviço](service-bus-resource-manager-namespace.md)
>-    [Criar um namespace Hubs de evento com um grupo de Hub de evento e consumidor](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>
>Para verificar os modelos mais recentes, visite a Galeria de [Modelos de início rápido do Azure][] e procure por "Barramento de serviço".

## <a name="what-will-you-deploy"></a>O que você implantará?

Com este modelo, você irá implantar um namespace barramento de serviço com uma fila.

[Filas de barramento de serviço](service-bus-queues-topics-subscriptions.md#queues) oferecem primeiro em, entrega de mensagens de PEPS (primeiro) para um ou mais consumidores concorrência.

Para executar a implantação automaticamente, clique no botão a seguir:

[![Implantar para o Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Gerenciador de recursos do Azure, você define parâmetros para valores que você deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada `Parameters` que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam com base no projeto que você está implantando ou baseado no ambiente do qual que você está implantando. Não defina parâmetros para valores que sempre permanecerá o mesmo. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

O modelo define os parâmetros a seguir.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

O nome do namespace barramento de serviço para criar.

```
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName

O nome da fila criada no namespace barramento de serviço.

```
"serviceBusQueueName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

A versão da API do barramento de serviço do modelo.

```
"serviceBusApiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Recursos para implantação

Cria um namespace padrão de barramento de serviço do tipo **mensagens**, com uma fila.

```
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]",
            }
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Comandos para executar implantação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Próximas etapas

Agora que você criou e implantou recursos usando o Gerenciador de recursos do Azure, Aprenda a gerenciar esses recursos exibindo estes artigos:

- [Gerenciar barramento de serviço com o PowerShell](service-bus-powershell-how-to-provision.md)
- [Gerenciar recursos de barramento de serviço com o Explorador de barramento de serviço](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Criação de modelos do Gerenciador de recursos do Azure]: ../resource-group-authoring-templates.md
  [Modelo de namespace e fila do barramento de serviço]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
  [Modelos de início rápido Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
