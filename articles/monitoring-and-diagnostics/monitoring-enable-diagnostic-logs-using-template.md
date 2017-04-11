<properties
    pageTitle="Habilitar automaticamente as configurações de diagnóstico usando um modelo do Gerenciador de recursos | Microsoft Azure"
    description="Saiba como usar um modelo do Gerenciador de recursos para criar definições de diagnóstico que ajudarão a transmitir sua logs de diagnóstico para Hubs de evento ou armazená-los em uma conta de armazenamento."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Habilitar automaticamente as configurações de diagnóstico na criação de recursos usando um modelo do Gerenciador de recursos
Neste artigo, vamos mostrar como você pode usar um [modelo do Gerenciador de recursos do Azure](../resource-group-authoring-templates.md) para configurar definições de diagnóstico em um recurso quando ele é criado. Isso permite que você iniciar o streaming seus Logs de diagnóstico e métricas para Hubs de evento, arquivá-los em uma conta de armazenamento ou enviando-os para a análise de Log quando um recurso é criado automaticamente.

O método para ativar Logs de diagnóstico usando um modelo do Gerenciador de recursos depende do tipo de recurso.

- Recursos de **Computação não** (por exemplo, grupos de segurança de rede, lógica de aplicativos, automação) usam [configurações de diagnóstico descrito neste artigo](./monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).
- **Calcular** Recursos (WAD/LAD baseado) usam o [arquivo de configuração de WAD/LAD descrito neste artigo](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

Neste artigo, vamos descrevem como configurar diagnóstico usando qualquer um dos métodos.

As etapas básicas são da seguinte maneira:

1. Crie um modelo como um arquivo JSON que descreve como criar o recurso e habilitar o diagnóstico.
2. [Implantar o modelo usando qualquer método de implantação](../resource-group-template-deploy.md).

Abaixo fornecemos um exemplo do arquivo JSON de modelo, que você precisa gerar para não-computação e recursos de computação.

## <a name="non-compute-resource-template"></a>Modelo de recursos de computação não
Para obter recursos não computação, você precisará fazer duas coisas:

1. Adicione parâmetros ao blob de parâmetros para o nome da conta de armazenamento, ID de regra de barramento de serviço e/ou a ID de espaço de trabalho de análise de Log de OMS (Ativando arquivamento de Logs de diagnóstico em uma conta de armazenamento, streaming de logs de eventos Hubs e/ou enviar logs para análise de Log).

    ```json
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. Da matriz de recursos do recurso para o qual você deseja habilitar Logs de diagnóstico, adicionar um recurso do tipo `[resource namespace]/providers/diagnosticSettings`.

    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
          "workspaceId": "[parameters('workspaceId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ]
        }
      }
    ]
    ```

O blob de propriedades para a configuração de diagnóstico segue [o formato descrito neste artigo](https://msdn.microsoft.com/library/azure/dn931931.aspx).

Aqui está um exemplo completo que cria um grupo de segurança de rede e ativa streaming Hubs de evento e armazenamento em uma conta de armazenamento.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nsgName": {
      "type": "string",
      "metadata": {
        "description": "Name of the NSG that will be created."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('nsgName')]",
      "apiVersion": "2016-03-30",
      "location": "westus",
      "properties": {
        "securityRules": []
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "NetworkSecurityGroupEvent",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              },
              {
                "category": "NetworkSecurityGroupRuleCounter",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>Calcular o modelo de recurso
Para habilitar o diagnóstico em um recurso de computação, por exemplo um cluster de máquina Virtual ou tecidos do serviço, você precisa:

1. Adicione a extensão de diagnóstico do Azure para a definição de recurso de máquina virtual.
2. Especifica um hub de conta e/ou o evento de armazenamento como um parâmetro.
3. Adicione o conteúdo do arquivo WADCfg XML na caixa da propriedade XMLCfg, escape todos os caracteres XML corretamente.

> [AZURE.WARNING] A última etapa pode ser difícil. [Consulte este artigo](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables) para obter um exemplo que divide o esquema de configuração de diagnóstico em variáveis que são escape e formatados corretamente.

O processo inteiro, incluindo exemplos, é descrito [neste documento](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).


## <a name="next-steps"></a>Próximas etapas
- [Leia mais sobre Logs de diagnóstico do Azure](./monitoring-overview-of-diagnostic-logs.md)
- [Transmitir Azure Logs de diagnóstico para Hubs de evento](./monitoring-stream-diagnostic-logs-to-event-hubs.md)
