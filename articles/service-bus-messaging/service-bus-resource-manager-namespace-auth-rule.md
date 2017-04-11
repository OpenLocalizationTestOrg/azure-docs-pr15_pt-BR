<properties
    pageTitle="Criar uma regra de autorização de barramento de serviço usando um modelo do Gerenciador de recursos do Azure | Microsoft Azure"
    description="Criar uma regra de autorização de barramento de serviço para namespace e fila usando o modelo do Gerenciador de recursos do Azure"
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

# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Criar uma regra de autorização de barramento de serviço para namespace e fila usando um modelo do Gerenciador de recursos do Azure

Este artigo mostra como usar um modelo do Gerenciador de recursos do Azure que cria uma [regra de autorização](service-bus-authentication-and-authorization.md#shared-access-signature-authentication) para um namespace de barramento de serviço e fila. Você aprenderá como definir quais recursos são implantados e como definir parâmetros que são especificadas quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender suas necessidades.

Para obter mais informações sobre a criação de modelos, consulte [modelos de coautoria Gerenciador de recursos do Azure][].

Para o modelo completo, consulte o [modelo de regra de auth barramento de serviço][] no GitHub.

>[AZURE.NOTE] Os modelos de Gerenciador de recursos do Azure a seguir estão disponíveis para download e implantação.
>
>-    [Criar um namespace Hubs de evento com um grupo de Hub de evento e consumidor](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>-    [Criar um namespace barramento de serviço com fila](service-bus-resource-manager-namespace-queue.md)
>-    [Criar um namespace barramento de serviço com o tópico e assinatura](service-bus-resource-manager-namespace-topic.md)
>-    [Criar um namespace barramento de serviço](service-bus-resource-manager-namespace.md)
>
>Para verificar os modelos mais recentes, visite a Galeria de [Modelos de início rápido do Azure][] e procure por "Barramento de serviço".

## <a name="what-will-you-deploy"></a>O que você implantará?

Com este modelo, você implantará uma regra de autorização de barramento de serviço para um namespace e mensagens entidade (neste caso, fila).

Este modelo usa [Assinatura de acesso compartilhado (SAS)](service-bus-sas-overview.md) para autenticação. SAS permite que aplicativos autenticar barramento de serviço usando uma tecla de acesso configurada no namespace ou na entidade mensagens (fila ou tópico) com o qual direitos específicos estão associados. Você pode usar essa chave para gerar um token SAS que os clientes podem usar para autenticar barramento de serviço.

Para executar a implantação automaticamente, clique no botão a seguir:

[![Implantar para o Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Gerenciador de recursos do Azure, você define parâmetros para valores que você deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada `Parameters` que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam com base no projeto que você está implantando ou baseado no ambiente do qual que você está implantando. Não defina parâmetros para valores que sempre permanecerá o mesmo. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

O modelo define os parâmetros a seguir.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

O nome do namespace barramento de serviço para criar.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>namespaceAuthorizationRuleName 

O nome da regra de autorização para o namespace.

```
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName

O nome da fila no namespace barramento de serviço.

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

Cria um namespace padrão de barramento de serviço do tipo **Messaging**e uma regra de autorização de barramento de serviço para namespace e entidade.

```
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "StandardSku",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

## <a name="commands-to-run-deployment"></a>Comandos para executar implantação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Próximas etapas

Agora que você criou e implantou recursos usando o Gerenciador de recursos do Azure, Aprenda a gerenciar esses recursos exibindo estes artigos:

- [Gerenciar barramento de serviço com o PowerShell](service-bus-powershell-how-to-provision.md)
- [Gerenciar recursos de barramento de serviço com o Explorador de barramento de serviço](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)
- [Autorização e autenticação de barramento de serviço](service-bus-authentication-and-authorization.md)

  [Criação de modelos do Gerenciador de recursos do Azure]: ../resource-group-authoring-templates.md
  [Modelos de início rápido Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Modelo de regra de auth barramento de serviço]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
