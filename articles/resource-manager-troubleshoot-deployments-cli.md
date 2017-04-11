<properties
   pageTitle="Exibir operações de implantação com CLI Azure | Microsoft Azure"
   description="Descreve como usar a CLI do Azure para detectar problemas de implantação do Gerenciador de recursos."
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
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-cli"></a>Operações de implantação do modo de exibição com CLI do Azure

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Se você recebeu um erro ao implantar recursos Azure, você talvez queira ver mais detalhes sobre as operações de implantação que foram executadas. Azure CLI fornece comandos que permitem localizar os erros e determinar possíveis correções.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Você pode evitar alguns erros validando seu modelo e infraestrutura antes da implantação. Você também pode registrar solicitação adicional e informações de resposta durante a implantação que pode ser úteis mais tarde para solução de problemas. Para saber sobre Validando e informações de solicitação e resposta de registro, consulte [implantar um grupo de recursos com o modelo do Gerenciador de recursos do Azure](resource-group-template-deploy-cli.md).

## <a name="use-audit-logs-to-troubleshoot"></a>Usar logs de auditoria para solucionar problemas

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver erros para uma implantação, use as seguintes etapas:

1. Para ver os logs de auditoria, execute o comando **Mostrar log de grupo azure** . Você pode incluir o **– implantação de última** opção para recuperar somente o log para a implantação mais recente.

        azure group log show ExampleGroup --last-deployment

2. O comando **Mostrar log de grupo azure** retorna muitas informações. Para solução de problemas, você geralmente deseja se concentrar em operações que falhou. O script a seguir usa a opção de **– json** e o utilitário JSON [jq](https://stedolan.github.io/jq/) para pesquisar o log de falhas de implantação.

        azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'
        
        {
        "claims": {
          "aud": "https://management.core.windows.net/",
          "iss": "https://sts.windows.net/<guid>/",
          "iat": "1442510510",
          "nbf": "1442510510",
          "exp": "1442514410",
          "ver": "1.0",
          "http://schemas.microsoft.com/identity/claims/tenantid": "{guid}",
          "http://schemas.microsoft.com/identity/claims/objectidentifier": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
          "puid": "XXXXXXXXXXXXXXXX",
          "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",
          "altsecid": "1:example.com:XXXXXXXXXXX",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string="">",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
          "name": "Tom FitzMacken",
          "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
          "groups": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
          "wids": "{guid}",
          "appid": "{guid}",
          "appidacr": "0",
          "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
          "http://schemas.microsoft.com/claims/authnclassreference": "1",
          "ipaddr": "000.000.000.000"
        },
        "properties": {
          "statusCode": "Conflict",
          "statusMessage": "{\"Code\":\"Conflict\",\"Message\":\"Website with given name mysite already exists.\",\"Target\":null,\"Details\":[{\"Message\":\"Website with given name
            mysite already exists.\"},{\"Code\":\"Conflict\"},{\"ErrorEntity\":{\"Code\":\"Conflict\",\"Message\":\"Website with given name mysite already exists.\",\"ExtendedCode\":
            \"54001\",\"MessageTemplate\":\"Website with given name {0} already exists.\",\"Parameters\":[\"mysite\"],\"InnerErrors\":null}}],\"Innererror\":null}"
        },
        ...

    Você pode ver **Propriedades** inclui informações em json sobre a operação que falhou.

    Você pode usar o **– detalhado** e opções de **-vv** para ver mais informações dos logs.  Use o **– detalhado** opção para exibir as etapas as operações dar uma olhada em `stdout`. Para um histórico de solicitação completa, use a opção **- vv** . As mensagens geralmente fornecem dicas importantes sobre a causa de qualquer falhas.

3. Para focalizar a mensagem de status para as entradas falhas, use o seguinte comando:

        azure group log show ExampleGroup --json | jq -r ".[] | select(.status.value == \"Failed\") | .properties.statusMessage"


## <a name="use-deployment-operations-to-troubleshoot"></a>Use operações de implantação para solucionar problemas

1. Obtenha o status geral de uma implantação com o comando **Mostrar de implantação do azure grupo** . No exemplo abaixo a implantação falhou.

        azure group deployment show --resource-group ExampleGroup --name ExampleDeployment
        
        info:    Executing command group deployment show
        + Getting deployments
        data:    DeploymentName     : ExampleDeployment
        data:    ResourceGroupName  : ExampleGroup
        data:    ProvisioningState  : Failed
        data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
        data:    Mode               : Incremental
        data:    Name             Type    Value
        data:    ---------------  ------  ------------
        data:    siteName         String  ExampleSite
        data:    hostingPlanName  String  ExamplePlan
        data:    siteLocation     String  West US
        data:    sku              String  Free
        data:    workerSize       String  0
        info:    group deployment show command OK

2. Para ver a mensagem para operações com falha para uma implantação, use:

        azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json  | jq ".[] | select(.properties.provisioningState == \"Failed\") | .properties.statusMessage.Message"


## <a name="next-steps"></a>Próximas etapas

- Para obter ajuda com resolução de erros de implantação específica, consulte [resolver erros comuns ao implantar recursos para Azure com o Gerenciador de recursos do Azure](resource-manager-common-deployment-errors.md).
- Para saber sobre como usar os logs de auditoria para monitorar a outros tipos de ações, consulte [operações com o Gerenciador de recursos de auditoria](resource-group-audit.md).
- Para validar sua implantação antes de executá-lo, consulte [implantar um grupo de recursos com o modelo do Gerenciador de recursos do Azure](resource-group-template-deploy.md).
