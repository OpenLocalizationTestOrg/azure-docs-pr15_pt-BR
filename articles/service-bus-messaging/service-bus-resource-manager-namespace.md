<properties
    pageTitle="Criar um namespace barramento de serviço usando um modelo do Gerenciador de recursos | Microsoft Azure"
    description="Usar o Gerenciador de recursos do Azure modelo para criar um namespace barramento de serviço"
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
    ms.date="10/04/2016"
    ms.author="sethm;shvija"/>

# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Criar um namespace barramento de serviço usando um modelo do Gerenciador de recursos do Azure

Este artigo descreve como usar um modelo do Gerenciador de recursos do Azure que cria um namespace barramento de serviço do tipo **Messaging** com uma SKU padrão/Basic. O artigo também define os parâmetros que são especificados para a execução da implantação. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender suas necessidades.

Para obter mais informações sobre a criação de modelos, consulte [modelos de coautoria Gerenciador de recursos do Azure][].

Para o modelo completo, consulte o [modelo de namespace barramento de serviço][] no GitHub.

>[AZURE.NOTE] Os modelos de Gerenciador de recursos do Azure a seguir estão disponíveis para download e implantação. 
>
>-    [Criar um namespace Hubs de evento com um grupo de Hub de evento e consumidor](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>-    [Criar um namespace barramento de serviço com fila](service-bus-resource-manager-namespace-queue.md)
>-    [Criar um namespace barramento de serviço com o tópico e assinatura](service-bus-resource-manager-namespace-topic.md)
>-    [Criar um namespace barramento de serviço com fila e a autorização da regra](service-bus-resource-manager-namespace-auth-rule.md)
>
>Para verificar os modelos mais recentes, visite a Galeria de [Modelos de início rápido do Azure][] e procure por barramento de serviço.

## <a name="what-will-you-deploy"></a>O que você implantará?

Com este modelo, você irá implantar um namespace barramento de serviço com um [básico, padrão ou Premium](https://azure.microsoft.com/pricing/details/service-bus/) SKU.

Para executar a implantação automaticamente, clique no botão a seguir:

[![Implantar para o Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Gerenciador de recursos do Azure, você define parâmetros para valores que você deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada `Parameters` que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam com base no projeto que você está implantando ou baseado no ambiente do qual que você está implantando. Não defina parâmetros para valores que sempre permanecerá o mesmo. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

Esse modelo define os parâmetros a seguir.

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

### <a name="servicebussku"></a>serviceBusSKU

O nome da barramento de serviço [SKU](https://azure.microsoft.com/pricing/details/service-bus/) para criar.

```
"serviceBusSku": { 
    "type": "string", 
    "allowedValues": [ 
        "Basic", 
        "Standard",
        "Premium" 
    ], 
    "defaultValue": "Standard", 
    "metadata": { 
        "description": "The messaging tier for service Bus namespace" 
    } 

```

O modelo define os valores que são permitidos para este parâmetro (Basic, padrão ou Premium) e atribui um valor padrão (padrão) se nenhum valor for especificado.

Para obter mais informações sobre preços do barramento de serviço, consulte [barramento de serviço preços e cobrança][].

### <a name="servicebusapiversion"></a>serviceBusApiVersion

A versão da API do barramento de serviço do modelo.

```
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2015-08-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       } 
```

## <a name="resources-to-deploy"></a>Recursos para implantação

### <a name="service-bus-namespace"></a>Namespace do barramento de serviço

Cria um namespace padrão de barramento de serviço do tipo **mensagens**.

```
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "properties": {
        }
    }
]
```

## <a name="commands-to-run-deployment"></a>Comandos para executar implantação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### <a name="azure-cli"></a>CLI Azure

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## <a name="next-steps"></a>Próximas etapas

Agora que você criou e implantou recursos usando o Gerenciador de recursos do Azure, Aprenda a gerenciar esses recursos lendo estes artigos:

- [Gerenciar barramento de serviço com o PowerShell](service-bus-powershell-how-to-provision.md)
- [Gerenciar recursos de barramento de serviço com o Explorador de barramento de serviço](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Criação de modelos do Gerenciador de recursos do Azure]: ../resource-group-authoring-templates.md
  [Modelo de namespace barramento de serviço]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
  [Modelos de início rápido Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Barramento de serviço preços e cobrança]: https://azure.microsoft.com/documentation/articles/service-bus-pricing-billing/
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
