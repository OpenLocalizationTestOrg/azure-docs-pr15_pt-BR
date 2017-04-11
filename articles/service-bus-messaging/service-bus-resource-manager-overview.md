<properties
    pageTitle="Criar recursos de barramento de serviço usando modelos do Gerenciador de recursos do Azure | Microsoft Azure"
    description="Usar modelos do Gerenciador de recursos do Azure para automatizar a criação de recursos de barramento de serviço"
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
    ms.author="sethm"/>

# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Criar recursos de barramento de serviço usando modelos do Gerenciador de recursos do Azure

Este artigo descreve como criar e implantar recursos de barramento de serviço e Hubs de evento usando modelos, PowerShell e o provedor de recursos de barramento de serviço do Gerenciador de recursos do Azure.

Azure modelos do Gerenciador de recursos ajudam você a definir os recursos para implantar uma solução e para especificar parâmetros e variáveis que permitem valores para diferentes ambientes de entrada. O modelo consiste em JSON e expressões que você pode usar para construir valores para a sua implantação. Para obter informações detalhadas sobre como escrever modelos do Gerenciador de recursos do Azure e uma discussão do formato de modelo, consulte [modelos de coautoria Gerenciador de recursos do Azure](../resource-group-authoring-templates.md). 

>[AZURE.NOTE] Os exemplos deste artigo mostram como usar o Gerenciador de recursos do Azure para criar um namespace barramento de serviço e a entidade de mensagens (fila). Para outros exemplos de modelo, visite a [Galeria de modelos de início rápido do Azure][] e procure por "Barramento de serviço".

## <a name="service-bus-and-event-hubs-resource-manager-templates"></a>Modelos de barramento de serviço e Gerenciador de recursos de Hubs de evento

Esses modelos de barramento de serviço e Gerenciador de recursos do evento Hubs Azure estão disponíveis para download e implantação. Clique nos seguintes links para obter detalhes sobre cada um deles, com links para os modelos no GitHub: 

- [Criar um namespace barramento de serviço](service-bus-resource-manager-namespace.md)
- [Criar um namespace barramento de serviço com fila](service-bus-resource-manager-namespace-queue.md)
- [Criar um namespace barramento de serviço com o tópico e assinatura](service-bus-resource-manager-namespace-topic.md)
- [Criar um namespace barramento de serviço com fila e a autorização da regra](service-bus-resource-manager-namespace-auth-rule.md)
- [Criar um namespace Hubs de evento com um grupo de Hub de evento e consumidor](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)

## <a name="deploy-with-powershell"></a>Implantar com PowerShell

O procedimento a seguir descreve como usar o PowerShell para implantar um modelo de Gerenciador de recursos do Azure que cria um namespace **padrão** de barramento de serviço de nível e uma fila dentro desse namespace. Esse exemplo é baseado no modelo de [criar um namespace barramento de serviço com fila](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) . O fluxo de trabalho aproximado é da seguinte maneira:

1. Instale o PowerShell.
2. Crie o modelo e (opcionalmente) um arquivo de parâmetro.
2. No PowerShell, faça logon em sua conta do Azure.
3. Crie um novo grupo de recursos se não existir.
4. Teste a implantação.
5. Se desejar, defina o modo de implantação.
6. Implante o modelo.

Para obter informações completas sobre como implantar modelos de Gerenciador de recursos do Azure, consulte [recursos de implantar com modelos do Gerenciador de recursos do Azure][].

### <a name="install-powershell"></a>Instale o PowerShell

Instale o Azure PowerShell seguindo as instruções em [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

### <a name="create-a-template"></a>Criar um modelo

Clonar ou copie o modelo de [201 servicebus-criar-fila](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) do GitHub:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusQueueName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Queue"
            }
        },
        "serviceBusApiVersion": {
            "type": "string",
            "defaultValue": "2015-08-01",
            "metadata": {
                "description": "Service Bus ApiVersion used by the template"
            }
        }
    },
    "variables": {
        "location": "[resourceGroup().location]",
        "sbVersion": "[parameters('serviceBusApiVersion')]",
        "defaultSASKeyName": "RootManageSharedAccessKey",
        "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]"
    },
    "resources": [{
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
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }],
    "outputs": {
        "NamespaceConnectionString": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
        },
        "SharedAccessPolicyPrimaryKey": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
        }
    }
}
```

### <a name="create-a-parameters-file-optional"></a>Criar um arquivo de parâmetros (opcional)

Para usar um arquivo de parâmetros opcionais, copie o arquivo [201 servicebus-criar-fila](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) . Substitua o valor de `serviceBusNamespaceName` com o nome do namespace barramento de serviço que você deseja criar nesta implantação e substitua o valor de `serviceBusQueueName` com o nome da fila que você deseja criar. 

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2015-08-01"
        }
    }
}
```

Para obter mais informações, consulte o tópico do [arquivo de parâmetro](../resource-group-template-deploy.md#parameter-file) .

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Faça logon no Azure e defina a assinatura do Azure

A partir de um prompt do PowerShell, execute o seguinte comando:

```
Login-AzureRmAccount
```

Você será solicitado a fazer logon para sua conta do Azure. Depois de fazer logon, execute o seguinte comando para exibir suas assinaturas disponíveis.

```
Get-AzureRMSubscription
```

Esse comando retorna uma lista de assinaturas do Azure disponíveis. Escolha uma assinatura para a sessão atual, execute o comando a seguir. Substituir `<YourSubscriptionId>` com o GUID para a assinatura Azure que você deseja usar.

```
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Definir o grupo de recursos

Se você não tiver um grupo de recursos existentes, crie um novo grupo de recursos com o comando **New-AzureRmResourceGroup** . Fornece o nome do grupo de recursos e local que você deseja usar. Por exemplo:

```
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Se bem-sucedida, será exibido um resumo do grupo de recursos novos.

```
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Testar a implantação

Validar sua implantação executando o `Test-AzureRmResourceGroupDeployment` cmdlet. Ao testar a implantação, forneça parâmetros exatamente como você faria ao executar a implantação.

```
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Criar a implantação

Para criar a nova implantação, execute o `New-AzureRmResourceGroupDeployment` comando e fornecer os parâmetros necessários quando solicitado. Os parâmetros incluem um nome para sua implantação, o nome do seu grupo de recursos e o caminho ou a URL para o arquivo de modelo. Se o parâmetro de **modo** não for especificado, o valor padrão de **Incremental** será usado. Para obter mais informações, consulte [implantações completas e incrementais](../resource-group-template-deploy.md#incremental-and-complete-deployments).

O comando a seguir solicita os três parâmetros na janela do PowerShell:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Para especificar um arquivo de parâmetros em vez disso, use o comando a seguir.

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Você também pode usar parâmetros de embutida quando você executa o cmdlet de implantação. O comando é da seguinte maneira:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Para executar uma implantação [completa](../resource-group-template-deploy.md#incremental-and-complete-deployments) , defina o parâmetro **Mode** como **concluída**:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

### <a name="verify-the-deployment"></a>Verifique se a implantação

Se os recursos são implantados com êxito, um resumo da implantação é exibido na janela do PowerShell:

```
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2016 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2015-08-01

```

## <a name="next-steps"></a>Próximas etapas

Agora você viu os comandos para implantar um modelo do Gerenciador de recursos do Azure e fluxo de trabalho básico. Para obter informações mais detalhadas, visite os links a seguir:

- [Visão geral do Gerenciador de recursos Azure][]
- [Implantar recursos com modelos do Gerenciador de recursos do Azure][]
- [Criação de modelos](../resource-group-authoring-templates.md)


[Visão geral do Gerenciador de recursos Azure]: ../resource-group-overview.md
[Implantar recursos com modelos do Gerenciador de recursos do Azure]: ../resource-group-template-deploy.md
[Galeria de modelos de início rápido Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus