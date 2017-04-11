<properties
   pageTitle="Implantar recursos com API REST e modelo | Microsoft Azure"
   description="Use o Gerenciador de recursos do Azure e REST API de Gerenciador de recursos para implantar um recursos no Azure. Os recursos são definidos em um modelo do Gerenciador de recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Implantar recursos com modelos do Gerenciador de recursos e REST API de Gerenciador de recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI Azure](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

Este artigo explica como usar a REST API Gerenciador de recursos com os modelos do Gerenciador de recursos para implantar seus recursos no Azure.  

> [AZURE.TIP] Para obter ajuda com um erro de depuração durante a implantação, consulte:
>
> - [Operações de implantação do modo de exibição com a API REST](resource-manager-troubleshoot-deployments-rest.md) para saber mais sobre a obtenção de informações que ajuda você a solucionar o erro
> - [Solucionar erros comuns ao implantar recursos para Azure com o Gerenciador de recursos do Azure](resource-manager-common-deployment-errors.md) para saber como resolver erros comuns de implantação

Seu modelo pode ser um arquivo local ou um arquivo externo que está disponível por meio de um URI. Quando seu modelo reside em uma conta de armazenamento, você pode restringir o acesso ao modelo e fornecer um token de assinatura (SAS) acesso compartilhado durante a implantação.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>Implantar com a API REST
1. Defina [cabeçalhos e parâmetros comuns](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common), incluindo tokens de autenticação.
2. Se você não tiver um grupo de recursos existentes, crie um grupo de recursos. Forneça sua id de assinatura, o nome do novo grupo de recursos e local que você precisa para sua solução. Para obter mais informações, consulte [criar um grupo de recursos](https://msdn.microsoft.com/library/azure/dn790525.aspx).

        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
   
3. Valide sua implantação antes de executar-executando a operação de [Validar uma implantação do modelo](https://msdn.microsoft.com/library/azure/dn790547.aspx) . Ao testar a implantação, forneça parâmetros exatamente como você faria ao executar a implantação (mostrada na próxima etapa).

3. Crie uma implantação. Forneça sua id de assinatura, o nome do grupo de recursos para implantar, o nome da implantação e um link para seu modelo. Para obter informações sobre o arquivo de modelo, consulte o [arquivo de parâmetro](#parameter-file). Para obter mais informações sobre a API REST para criar um grupo de recursos, consulte [criar uma implantação do modelo](https://msdn.microsoft.com/library/azure/dn790564.aspx). Observe que o **modo** é definido como **Incremental**. Para executar uma implantação completa, defina o **modo** como **concluída**. Tenha cuidado ao usar o modo completo, como você pode excluir acidentalmente recursos que não estão em seu modelo.
    
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
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
              }
            }
          }
   
      Se você quiser fazer logon conteúdo de resposta, o conteúdo de solicitação, ou ambas, incluem **debugSetting** na solicitação.

        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }

      Você pode configurar sua conta de armazenamento para usar um token de assinatura (SAS) acesso compartilhado. Para obter mais informações, consulte [Delegando acesso com uma assinatura de acesso compartilhado](https://msdn.microsoft.com/library/ee395415.aspx).

4. Obter o status da implantação do modelo. Para obter mais informações, consulte [obter informações sobre uma implantação do modelo](https://msdn.microsoft.com/library/azure/dn790565.aspx).

          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Próximas etapas
- Para um exemplo de implantação de recursos por meio da biblioteca de cliente .NET, consulte [implantar recursos usando bibliotecas .NET e um modelo](virtual-machines/virtual-machines-windows-csharp-template.md).
- Para definir parâmetros no modelo, consulte [modelos de criação de páginas](resource-group-authoring-templates.md#parameters).
- Para obter orientações sobre como implantar sua solução para diferentes ambientes, consulte [ambientes de desenvolvimento e teste no Microsoft Azure](solution-dev-test-environments.md).
- Para obter detalhes sobre como usar uma referência de KeyVault para passar valores seguros, consulte [passar valores seguros durante a implantação](resource-manager-keyvault-parameter.md).
