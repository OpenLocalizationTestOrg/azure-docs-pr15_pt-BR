<properties
    pageTitle="Criar um namespace barramento de serviço com o tópico, assinatura e regra usando um modelo do Gerenciador de recursos do Azure | Microsoft Azure"
    description="Criar um namespace barramento de serviço com o tópico, assinatura e regra usando o modelo do Gerenciador de recursos do Azure"
    services="service-bus"
    documentationCenter=".net"
    authors="ShubhaVijayasarathy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="ShubhaVijayasarathy"/>

# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Criar um namespace barramento de serviço com o tópico, assinatura e regra usando um modelo do Gerenciador de recursos do Azure

Este artigo mostra como usar um modelo do Gerenciador de recursos do Azure que cria um namespace barramento de serviço com um tópico, assinatura e regra (filtro). Você saiba como definir quais recursos são implantados e como definir parâmetros que são especificadas quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender suas necessidades

Para obter mais informações sobre a criação de modelos, consulte [modelos de coautoria Gerenciador de recursos do Azure][].

Para obter mais informações sobre prática e padrões em convenções de nomenclatura de recursos do Azure, consulte [Convenções de nomenclatura de recursos do Azure][].

Para o modelo completo, consulte o modelo de [barramento de serviço do namespace com o tópico, a assinatura e a regra][] .

>[AZURE.NOTE] Os modelos de Gerenciador de recursos do Azure a seguir estão disponíveis para download e implantação.
>
>-    [Criar um namespace barramento de serviço com fila e a autorização da regra](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Criar um namespace barramento de serviço com fila](service-bus-resource-manager-namespace-queue.md)
>-    [Criar um namespace barramento de serviço](service-bus-resource-manager-namespace.md)
>-    [Criar um namespace barramento de serviço com o tópico e assinatura](service-bus-resource-manager-namespace-topic.md)
>
>Para verificar os modelos mais recentes, visite a Galeria de [Modelos de início rápido do Azure][] e procure por barramento de serviço.

## <a name="what-will-you-deploy"></a>O que você implantará?

Com este modelo, você implantar um namespace barramento de serviço com tópico, assinatura e regra (filtro).

[Tópicos de barramento de serviço e assinaturas](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) fornecer um formulário de um-para-muitos de comunicação, em um padrão de *publicação/assinatura* . Ao usar tópicos e assinaturas, componentes de um aplicativo distribuído não comunicarem diretamente, em vez disso, trocar mensagens por meio de tópico que atua como um intermediário. Uma assinatura a um tópico é semelhante a uma fila virtual que recebe cópias das mensagens que foram enviadas para o tópico. Um filtro em assinatura permite especificar quais mensagens enviadas para um tópico deverá aparecer dentro de uma assinatura de tópico específico.

## <a name="what-are-rules-filters"></a>Quais são as regras (filtros)?

Em muitos cenários, as mensagens com características específicas devem ser processadas de maneiras diferentes. Para permitir isso, você pode configurar assinaturas para localizar as mensagens que têm desejado propriedades e, em seguida, executam certas modificações a essas propriedades. Enquanto assinaturas de barramento de serviço ver todas as mensagens enviadas para o tópico, você só pode copiar um subconjunto dessas mensagens na fila de assinatura virtual. Isso é feito usando filtros de assinatura. Para saber mais sobre rules(filters), consulte [assinaturas, tópicos e filas de barramento de serviço][].

Para executar a implantação automaticamente, clique no botão a seguir:

[![Implantar para o Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Gerenciador de recursos do Azure, você deve definir parâmetros para os valores que você deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada `Parameters` que contém todos os valores de parâmetro. Você deve definir um parâmetro para esses valores que variam com base no projeto que você está implantando ou baseado no ambiente do qual que você está implantando. Não defina parâmetros para valores que permanecem sempre a mesma. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

O modelo define os parâmetros a seguir:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

O nome do namespace barramento de serviço para criar.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

O nome do tópico criado no namespace barramento de serviço.

```
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

O nome da assinatura criada no namespace barramento de serviço.

```
"serviceBusSubscriptionName": {
"type": "string"
}
```
### <a name="servicebusrulename"></a>serviceBusRuleName

O nome do rule(filter) criado no namespace barramento de serviço.

```
   "serviceBusRuleName": {
   "type": "string",
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

Cria um namespace padrão de barramento de serviço do tipo **Messaging**, com o tópico e regras e assinatura.

```
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filter": {
                            "sqlExpression": "StoreName = 'Store1'"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Comandos para executar implantação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Próximas etapas

Agora que você criou e implantou recursos usando o Gerenciador de recursos do Azure, Aprenda a gerenciar esses recursos exibindo estes artigos:

- [Gerenciar barramento de serviço do Azure usando automação do Azure](service-bus-automation-manage.md)
- [Gerenciar barramento de serviço com o PowerShell](service-bus-powershell-how-to-provision.md)
- [Gerenciar recursos de barramento de serviço com o Explorador de barramento de serviço](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Criação de modelos do Gerenciador de recursos do Azure]: ../resource-group-authoring-templates.md
  [Modelos de início rápido Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Recursos Azure convenções de nomenclatura]: https://azure.microsoft.com/en-us/documentation/articles/guidance-naming-conventions/
  [Namespace do barramento de serviço com tópico, assinatura e regra]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
  [Assinaturas, tópicos e filas de barramento de serviço]:service-bus-queues-topics-subscriptions.md
  
