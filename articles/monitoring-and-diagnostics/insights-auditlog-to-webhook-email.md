<properties
    pageTitle="Configurar um webhook sobre alertas de Log de atividade do Azure | Microsoft Azure"
    description="Veja como usar alertas de Log da atividade para chamar webhooks. "
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
    ms.date="10/20/2016"
    ms.author="ashwink"/>

# <a name="configure-a-webhook-on-an-azure-activity-log-alerts"></a>Configurar um webhook em um alerta do Azure Log de atividade

Webhooks permitem que você rotear uma notificação de alerta Azure para outros sistemas para ações de processamento posterior ou personalizados. Você pode usar um webhook em um alerta para roteá-lo para serviços que enviar SMS, registrar bugs, notifique uma equipe por meio de serviços de bate-papo/mensagens ou fazer qualquer número de outras ações. Este artigo descreve como configurar um webhook em um alerta de registro de atividade do Azure e aparência a carga para a POSTAGEM HTTP um webhook. Para obter informações sobre a configuração e o esquema para um alerta de métrica Azure, [Consulte esta página em vez disso](insights-webhooks-alerts.md). Você também pode definir um alerta de Log da atividade para enviar emails quando ativado.

>[AZURE.NOTE] Este recurso está atualmente no modo de visualização, portanto esperar o desempenho e a qualidade de variável.

Você pode configurar um alerta de Log de atividade usando os [Cmdlets do PowerShell do Azure](insights-powershell-samples.md#create-alert-rules), [Entre plataformas CLI](insights-cli-samples.md#work-with-alerts)ou [API do Azure Monitor REST](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>Autenticação do webhook
TThe webhook pode autenticar usando um destes métodos:

1. **Autorização baseada em token** - o webhook URI é salvo com uma identificação de token, ex. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.  **Autorização básica** - o webhook URI é salvo com um nome de usuário e senha, por exemplo. `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Esquema de carga
A operação de POSTAGEM contém a seguinte carga JSON e esquema para todos os alertas baseados em Log de atividade. Este esquema é semelhante à usada por alertas baseados em métrica.

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

|Nome do elemento       |Descrição|
|---                |---|
|status             |Usado para alertas de métrica. Sempre definido como "ativado" para alertas de Log de atividade.|
|contexto            |Contexto do evento.|
|resourceProviderName|O provedor de recursos do recurso afetado.|
|conditionType      |Sempre "evento".|
|nome               |Nome da regra de alerta.|
|ID                 |Identificação do recurso do alerta.|
|Descrição        |Descrição do alerta conforme definido durante a criação do alerta.|
|subscriptionId     |ID do Azure assinatura.|
|carimbo de hora          |Hora em que o evento foi gerado pelo serviço do Azure que processada a solicitação.|
|identificação de recurso         |Identificação do recurso do recurso afetado.|
|resourceGroupName  |Nome do grupo de recursos para o recurso afetado|
|Propriedades         |Conjunto de `<Key, Value>` pares (isto é, `Dictionary<String, String>`) que inclui detalhes sobre o evento.|
|evento              |Elemento que contém metadados sobre o evento.|
|autorização      |As propriedades RBAC do evento. Eles geralmente incluem a ação de"", "função" e "escopo".|
|categoria           |Categoria do evento. Valores com suporte incluem: administrativas, alerta, segurança, ServiceHealth, recomendação.|
|chamador             |Endereço de email do usuário que executou a operação, a declaração UPN ou a declaração SPN com base na disponibilidade. Pode ser nulo certas para chamadas do sistema.|
|correlationId      |Geralmente um GUID no formato de cadeia de caracteres. Eventos com correlationId pertencem a mesma ação maior e geralmente compartilham um correlationId.|
|eventDescription   |Descrição de texto estático do evento.|
|eventDataId        |Identificador exclusivo para o evento.|
|eventSource        |Nome do serviço Azure ou infraestrutura que gerou o evento.|
|httpRequest        |Geralmente inclui "clientRequestId", "clientIpAddress" e "método" (por exemplo, coloque método HTTP).|
|nível              |Um dos seguintes valores: "Crítico", "Erro", "Aviso", "Informativo" e "Detalhado."|
|operationId        |Geralmente um GUID compartilhado entre os eventos correspondente a única operação.|
|operationName      |Nome da operação.|
|Propriedades         |Propriedades do evento.|
|status             |Cadeia de caracteres. Status da operação. Valores comuns incluem: "Iniciado", "Em andamento", "Êxito", "Falha", "Ativo", "Resolvido".|
|subStatus          |Geralmente inclui o código de status HTTP da chamada REST correspondente. Ele também pode conter outras cadeias de caracteres descrevendo um substatus. Os valores de substatus comum incluem: Okey (código de Status HTTP: 200) criados (código de Status HTTP: 201), aceito (código de Status HTTP: 202), não conteúdo (código de Status HTTP: 204), solicitação incorreta (código de Status HTTP: 400), não encontrado (código de Status HTTP: 404), conflito (código de Status HTTP: 409), erro interno do servidor (código de Status HTTP: 500), serviço indisponível (código de Status HTTP: 503), tempo limite do Gateway (código de Status HTTP: 504)|

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre o Log de atividade](monitoring-overview-activity-logs.md)
- [Executar scripts de automação do Azure (Runbooks) no Azure alertas](http://go.microsoft.com/fwlink/?LinkId=627081)
- [Use o aplicativo de lógica para enviar um SMS via Twilio de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Este exemplo é para alertas de métrica, mas pode ser modificado para trabalhar com um alerta de registro de atividades.
- [Use o aplicativo de lógica para enviar uma mensagem de margem de atraso de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Este exemplo é para alertas de métrica, mas pode ser modificado para trabalhar com um alerta de registro de atividades.
- [Use o aplicativo de lógica para enviar uma mensagem para uma fila do Azure de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Este exemplo é para alertas de métrica, mas pode ser modificado para trabalhar com um alerta de registro de atividades.
