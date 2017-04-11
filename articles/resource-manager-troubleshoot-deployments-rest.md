<properties
   pageTitle="Exibir operações de implantação com a API REST | Microsoft Azure"
   description="Descreve como usar a API do restante do Azure Gerenciador de recursos para detectar problemas de implantação do Gerenciador de recursos."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-resource-manager-rest-api"></a>Operações de implantação do modo de exibição com a API de REST do Gerenciador de recursos do Azure

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Se você recebeu um erro ao implantar recursos Azure, você talvez queira ver mais detalhes sobre as operações de implantação que foram executadas. A API REST fornece operações que permitem localizar os erros e determinar possíveis correções.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Você pode evitar alguns erros validando seu modelo e infraestrutura antes da implantação. Você também pode registrar solicitação adicional e informações de resposta durante a implantação que pode ser úteis mais tarde para solução de problemas. Para saber sobre Validando e informações de solicitação e resposta de registro, consulte [implantar um grupo de recursos com o modelo do Gerenciador de recursos do Azure](resource-group-template-deploy-rest.md).

## <a name="troubleshoot-with-rest-api"></a>Solucionar problemas com a API REST

1. Implante seus recursos com a operação de [criação de uma implantação do modelo](https://msdn.microsoft.com/library/azure/dn790564.aspx) . Para manter informações que podem ser úteis para depuração, defina a propriedade **debugSetting** na solicitação JSON para **requestContent** e/ou **responseContent**. 

        PUT https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",      
              },
              "debugSetting": {
                "detailLevel": "requestContent, responseContent"
              }
            }
          }

    Por padrão, o valor de **debugSetting** é definido como **Nenhum**. Ao especificar o valor de **debugSetting** , considere cuidadosamente o tipo de informação que você está passando em durante a implantação. Informações de log sobre a solicitação ou resposta, você pode potencialmente expor dados confidenciais que são recuperados por meio de operações de implantação. 

2. Obtenha informações sobre uma implantação com a operação de [obter informações sobre uma implantação do modelo](https://msdn.microsoft.com/library/azure/dn790565.aspx) .

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}

    Na resposta, observe em particular os elementos **provisioningState** , **correlationId** e **erro** . **CorrelationId** é usado para controlar eventos relacionados e pode ser útil ao trabalhar com o suporte técnico para solucionar um problema.
    
        { 
          ...
          "properties": {
            "provisioningState":"Failed",
            "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
            ...
            "error":{
              "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
              "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
            }  
          }
        }

3. Obtenha informações sobre operações de implantação com a operação de [todas as operações de implantação do modelo de lista](https://msdn.microsoft.com/library/azure/dn790518.aspx) . 

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}

    A resposta irá incluir informações de solicitação e/ou resposta com base no que você especificou na propriedade **debugSetting** durante a implantação.
    
        {
          ...
          "properties": 
          {
            ...
            "request":{
              "content":{
                "location":"West US",
                "properties":{
                  "accountType": "Standard_LRS"
                }
              }
            },
            "response":{
              "content":{
                "error":{
                  "message":"Conflict","code":"Conflict"
                }
              }
            }
          }
        }

4. Obtenha eventos dos logs de auditoria para a implantação com a operação de [lista os eventos de gerenciamento de uma assinatura](https://msdn.microsoft.com/library/azure/dn931934.aspx) .

        GET https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version={api-version}&$filter={filter-expression}&$select={comma-separated-property-names}


## <a name="next-steps"></a>Próximas etapas

- Para obter ajuda com resolução de erros de implantação específica, consulte [resolver erros comuns ao implantar recursos para Azure com o Gerenciador de recursos do Azure](resource-manager-common-deployment-errors.md).
- Para saber sobre como usar os logs de auditoria para monitorar a outros tipos de ações, consulte [operações com o Gerenciador de recursos de auditoria](resource-group-audit.md).
- Para validar sua implantação antes de executá-lo, consulte [implantar um grupo de recursos com o modelo do Gerenciador de recursos do Azure](resource-group-template-deploy.md).
