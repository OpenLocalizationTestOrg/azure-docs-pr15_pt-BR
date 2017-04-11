<properties
    pageTitle="Use ações de escala automática para enviar email e webhook notificações de alerta. | Microsoft Azure"
    description="Veja como usar ações de escala automática para chamar URLs web ou enviar notificações por email no Monitor do Azure. "
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="ashwink"/>

# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Usar ações de escala automática para enviar as notificações de alerta de email e webhook no Monitor do Azure

Este artigo mostra como configurar o disparadores para que você possa chamar URLs de web específico ou enviar emails com base em ações de escala automática no Azure.  

## <a name="webhooks"></a>Webhooks
Webhooks permitem que você rotear as notificações de alerta do Azure para outros sistemas para notificações de processamento posterior ou personalizados. Por exemplo, roteamento o alerta para serviços que podem lidar com uma solicitação de entrada da web para enviar que SMS, bugs de log, notificar uma equipe usando o bate-papo ou mensagens de serviços, etc. O URI webhook deve ser um ponto de extremidade HTTP ou HTTPS válido.

## <a name="email"></a>Email
Emails podem ser enviados para qualquer endereço de email válido. Os administradores e colaboradores da assinatura onde a regra está sendo executado também será notificado.


## <a name="cloud-services-and-web-apps"></a>Serviços de nuvem e Web Apps
Você pode optar por usar-in do portal do Azure para serviços de nuvem e Farms de servidor (Web Apps).

- Escolha a **escala por** métrica.

![escala por](./media/insights-autoscale-to-webhook-email/insights-autoscale-scale-by.png)

## <a name="virtual-machine-scale-sets"></a>Conjuntos de escala de máquina virtual
Para mais recente máquinas virtuais criados com o recurso Gerenciador (conjuntos de escala de máquina Virtual), você pode configurar isso usando modelos API REST, Gerenciador de recursos, PowerShell e CLI. Uma interface de portal ainda não está disponível.
Ao usar o modelo de API REST ou Gerenciador de recursos, inclua o elemento de notificações com as seguintes opções.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```
|Campo                              |Obrigatório? |Descrição|
|---                                |---        |---|
|operação                          |Sim        |o valor deve ser "Escala"|
|sendToSubscriptionAdministrator    |Sim        |valor deve ser "true" ou "false"|
|sendToSubscriptionCoAdministrators |Sim        |valor deve ser "true" ou "false"|
|customEmails                       |Sim        |valor pode ser [] null ou uma matriz de cadeia de caracteres de emails|
|webhooks                           |Sim        |valor pode ser Uri nulo ou válido|
|serviceUri                         |Sim        |um https válido Uri|
|Propriedades                         |Sim        |valor deve ser {vazia ou pode conter pares chave-valor|


## <a name="authentication-in-webhooks"></a>Autenticação em webhooks
Há duas formas URI de autenticação:

1. Autenticação de token-base, onde você salvar o URI webhook com uma identificação de token como um parâmetro de consulta. Por exemplo, https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
2. Autenticação básica, em que você usa uma ID de usuário e senha. Por exemplo,https://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## <a name="autoscale-notification-webhook-payload-schema"></a>Esquema de carga de webhook de notificação de escala automática
Quando a notificação de escala automática é gerada, os metadados a seguir é incluído na carga webhook:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


|Campo  |Obrigatório?|    Descrição|
|---|---|---|
|status |Sim    |O status que indica que uma ação de escala automática foi gerada|
|operação| Sim |Para um aumento de instâncias, ele será "Escala-Out" e uma queda no instâncias, será "Escala em"|
|contexto|   Sim |O contexto de ação de escala automática|
|carimbo de hora| Sim |Carimbo de hora quando a ação de escala automática foi acionada|
|ID |Sim|   ID do gerente de recursos da configuração de escala automática|
|nome   |Sim|   O nome da configuração de escala automática|
|detalhes|   Sim |Explicação da ação que o serviço de escala automática levou e a alteração na contagem instância|
|subscriptionId|    Sim |ID da assinatura do recurso destino que está sendo dimensionado|
|resourceGroupName| Sim|    Nome do grupo de recursos do recurso destino que está sendo dimensionado|
|resourceName   |Sim|   Nome do recurso destino que está sendo dimensionado|
|tipo de recurso   |Sim|   Os três valores compatíveis: "microsoft.classiccompute/domainnames/slots/roles" - funções de serviço de nuvem, "microsoft.compute/virtualmachinescalesets" - conjuntos de escala de máquina Virtual e "Microsoft.Web/serverfarms" - Web App|
|identificação de recurso |Sim|Identificação do gerente de recursos do recurso de destino que está sendo dimensionado|
|portalLink |Sim    |Link do portal Azure à página de resumo do recurso destino|
|oldCapacity|   Sim |(Antigo) instância contagem atual quando escala automática levou uma ação de escala|
|newCapacity|   Sim |A nova contagem de instância que o recurso de escala de escala automática|
|Propriedades|    Não| Opcional. Conjunto de < chave, valor > pares (por exemplo, dicionário < String, String >). O campo propriedades é opcional. Em uma interface de usuário personalizada ou um fluxo de trabalho de aplicativo com base em lógica, você pode inserir chaves e valores que podem ser passados usando a carga. Uma maneira alternativa para passar propriedades personalizadas para a chamada de saída webhook é usar o webhook URI próprio (como parâmetros de consulta)|
