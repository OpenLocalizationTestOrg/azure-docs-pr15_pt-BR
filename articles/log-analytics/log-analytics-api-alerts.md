<properties
   pageTitle="API de REST alerta de análise de log"
   description="A API REST alerta do Analytics de Log permite que você criar e gerenciar alertas no pacote de gerenciamento de operações (OMS).  Este artigo fornece detalhes da API e vários exemplos para executar operações diferentes."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="log-analytics-alert-rest-api"></a>Análise de log alerta API REST

A API REST alerta do Analytics de Log permite que você criar e gerenciar alertas no pacote de gerenciamento de operações (OMS).  Este artigo fornece detalhes da API e vários exemplos para executar operações diferentes.

A API REST de pesquisa de análise de Log é RESTful e podem ser acessada através da REST API do Azure Gerenciador de recursos. Neste documento você encontrará exemplos onde a API é acessada a partir de uma linha de comando do PowerShell usando [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de fonte aberta que simplifica a chamar a API do Gerenciador de recursos do Azure. O uso de ARMClient e PowerShell é uma das várias opções para acessar a API de pesquisa de análise de Log. Com essas ferramentas, você pode utilizar a API do Gerenciador de recursos do Azure RESTful para fazer chamadas para espaços de trabalho do OMS e executar comandos de pesquisa dentro deles. A API produzirá resultados de pesquisa para você no formato JSON, permitindo que você use os resultados da pesquisa de muitas maneiras diferentes programaticamente.

## <a name="prerequisites"></a>Pré-requisitos
Atualmente, os alertas só podem ser criadas com uma pesquisa salva em análise de Log.  Você pode fazer referência à [API de REST de pesquisa de Log](log-analytics-log-search-api.md) para obter mais informações.

## <a name="schedules"></a>Agendas
Uma pesquisa salva pode ter um ou mais agendamentos. O cronograma define com que frequência a pesquisa é executado e o intervalo de tempo em que o critério é identificado.
Cronogramas têm as propriedades da tabela a seguir.

| Propriedade  | Descrição |
|:--|:--|
| Intervalo | Com que frequência a pesquisa é executada. Medido em minutos. |
| QueryTimeSpan | O intervalo de tempo na qual o critério é avaliada. Deve ser igual a ou maior que o intervalo. Medido em minutos. |
| Versão | A versão da API sendo usada.  Atualmente, isso sempre deve ser definido como 1. |

Por exemplo, considere uma consulta de eventos com um intervalo de 15 minutos e um período de tempo de 30 minutos. Nesse caso, a consulta seria executada a cada 15 minutos, e um alerta seria acionado se os critérios de continuação resolver para true sobre um intervalo de 30 minutos.

### <a name="retrieving-schedules"></a>Recuperando agendas
Use o método Get para recuperar todos os agendamentos para uma pesquisa salva.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Use o método obter com uma identificação de cronograma para recuperar uma agenda específica para uma pesquisa salva.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

A seguir é uma resposta de exemplo de um cronograma.

    {
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
        "Interval": 15,
        "QueryTimeSpan": 15
    }

### <a name="creating-a-schedule"></a>Criando uma agenda
Use o método de colocar com uma identificação de agenda exclusiva para criar uma nova agenda.  Observe que duas agendas não podem ter o mesmo ID mesmo que eles estejam associados diferentes pesquisas salvas.  Quando você cria um cronograma no console de OMS, um GUID é criado para a ID de agenda.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Editando um cronograma
Use o método de colocar com um ID de agenda existente para a mesma pesquisa salva para modificar o cronograma.  O corpo da solicitação deve incluir a etag da agenda.

    $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Excluir agendas
Use o método Delete com uma identificação de cronograma para excluir uma agenda.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Ações
Um cronograma pode ter várias ações. Uma ação pode definir um ou mais processos para executar como enviar um email ou iniciando um runbook ou ele pode definir um limite que determina quando os resultados de uma pesquisa corresponder alguns critérios.  Algumas ações definirá ambos para que os processos são executados quando o limite é atendido.

Todas as ações têm as propriedades da tabela a seguir.  Diferentes tipos de alertas têm diferentes propriedades adicionais que estão descritas abaixo.

| Propriedade | Descrição |
|:--|:--|
| Tipo | Tipo de ação.  Atualmente, os valores possíveis são alerta e Webhook. |
| Nome | Nome de exibição para o alerta. |
| Versão | A versão da API sendo usada.  Atualmente, isso sempre deve ser definido como 1. |

### <a name="retrieving-actions"></a>Recuperando ações
Use o método Get para recuperar todas as ações de um cronograma.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Use o método obter com a ID de ação para recuperar uma ação específica para um cronograma.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Criando ou editando ações
Use o método de colocar com uma identificação de ação exclusiva para a agenda para criar uma nova ação.  Quando você cria uma ação no console de OMS, um GUID é para a ID de ação.

Use o método de colocar com um ID de ação existente para a mesma pesquisa salva para modificar o cronograma.  O corpo da solicitação deve incluir a etag da agenda.

O formato de solicitação para criar uma nova ação varia por tipo de ação para que esses exemplos são fornecidos nas seções a seguir.

### <a name="deleting-actions"></a>Excluindo ações
Use o método Delete com a ID de ação para excluir uma ação.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Ações de alerta
Um cronograma deve ter somente uma ação de alerta.  Ações de alerta tem uma ou mais das seções na tabela a seguir.  Cada é descrito mais detalhadamente abaixo.

| Seção | Descrição |
|:--|:--|
| Limite | Critérios para quando a ação é executada. |  
| EmailNotification | Envie um email para vários destinatários. |
| Correção | Inicie um runbook no Azure automação para tentar corrigir o problema identificado. |

#### <a name="thresholds"></a>Limites
Uma ação de alerta deve ter apenas um limite.  Quando os resultados de uma pesquisa salva corresponderem o limite em uma ação associada que a pesquisa, os outros processos na ação são executados.  Uma ação também pode conter apenas um limite para que ele pode ser usado com ações de outros tipos que não contêm limites.

Limites têm as propriedades da tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| Operador | Operador de comparação limite. <br> gt = maior que <br> lt = menor que |
| Valor | Valor para o limite. |

Por exemplo, considere uma consulta de eventos com um intervalo de 15 minutos, um período de tempo de 30 minutos e um limite de maior que 10. Nesse caso, a consulta seria executada a cada 15 minutos, e um alerta seria acionado se ele retornado 10 eventos que foram criados em um intervalo de 30 minutos.

A seguir é uma resposta de amostra para uma ação com apenas um limite.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Use o método de colocar com uma identificação de ação exclusivo para criar uma nova ação de limite de um cronograma.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Use o método de colocar com um ID de ação existente para modificar uma ação de limite para um cronograma.  O corpo da solicitação deve incluir a etag da ação.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="email-notification"></a>Notificação por email
Notificações por email enviar email para um ou mais destinatários.  Eles incluem as propriedades da tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| Destinatários | Lista de endereços de email. |
| Assunto | O assunto do email. |
| Anexo | Atualmente, anexos não são suportados para que sempre terá um valor "Nenhum". |

A seguir é uma resposta de amostra para uma ação de notificação de email com um limite.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Use o método de colocar com uma identificação de ação exclusivo para criar uma nova ação de email de um cronograma.  O exemplo a seguir cria uma notificação por email com um limite para que o email é enviado quando os resultados da pesquisa salva excederem o limite.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

Use o método de colocar com um ID de ação existente para modificar uma ação de email de um cronograma.  O corpo da solicitação deve incluir a etag da ação.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

#### <a name="remediation-actions"></a>Ações de correção
Correções iniciar um runbook no Azure automação que tenta corrigir o problema identificado pelo alerta.  Você deve criar uma webhook para runbook usado em uma ação de correção e especifique o URI na propriedade WebhookUri.  Quando você cria essa ação usando o console OMS, um novo webhook é criada automaticamente para runbook.

Correções incluem as propriedades da tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| RunbookName | Nome do runbook. Este deve corresponder um runbook publicado na conta de automação configurada na solução automação em seu espaço de trabalho do OMS. |
| WebhookUri | URI da webhook.
| Expiração | A data de vencimento e a hora do webhook.  Se o webhook não tiverem uma expiração, isso pode ser qualquer data futura válida. |

A seguir é uma resposta de amostra para uma ação de correção com um limite.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Use o método de colocar com uma identificação de ação exclusivo para criar uma nova ação de correção de um cronograma.  O exemplo a seguir cria uma correção com um limite para que runbook é iniciado quando os resultados da pesquisa salva excedem o limite.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Use o método de colocar com um ID de ação existente para modificar uma ação de correção para um cronograma.  O corpo da solicitação deve incluir a etag da ação.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Exemplo
A seguir está um exemplo completo para criar um novo alerta de email.  Isso cria uma nova programação junto com uma ação contendo um limite e email.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $workspaceId    = "MyWorkspace"
    $searchId       = "51cf0bd9-5c74-6bcb-927e-d1e9080b934e"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule?api-version=2015-03-20 $scheduleJson

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/mythreshold?api-version=2015-03-20 $thresholdJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/myemailaction?api-version=2015-03-20 $emailJson

### <a name="webhook-actions"></a>Ações de Webhook
Ações de Webhook iniciar um processo chamando uma URL e, opcionalmente, fornecendo uma carga sejam enviadas.  Eles são semelhantes às ações de correção, exceto que eles são destinados webhooks que podem invocar processos diferente de automação do Azure runbooks.  Eles também fornecem a opção adicional de fornecer uma carga ser entregue para o processo remoto.

Ações de Webhook não tem um limite, mas em vez disso, devem ser adicionadas a uma agenda que tem uma ação de alerta com um limite.  Você pode adicionar várias ações de Webhook que tudo funcionará quando o limite é atendido.

Ações de Webhook incluem as propriedades da tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| WebhookUri | O assunto do email. |
| CustomPayload | Carga personalizada sejam enviadas para a webhook.  O formato dependerá o que o webhook está esperando. |

A seguir é uma resposta de amostra para ação de webhook e uma ação de alerta associada com um limite.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

#### <a name="create-or-edit-a-webhook-action"></a>Criar ou editar uma ação de webhook
Use o método de colocar com uma identificação de ação exclusivo para criar uma nova ação webhook de um cronograma.  O exemplo a seguir cria uma ação de Webhook e uma ação de alerta com um limite para que o webhook será acionada quando os resultados da pesquisa salva excedem o limite.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Use o método de colocar com um ID de ação existente para modificar uma ação de webhook para um cronograma.  O corpo da solicitação deve incluir a etag da ação.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

## <a name="next-steps"></a>Próximas etapas

- Use a [API REST para realizar pesquisas de log](log-analytics-log-search-api.md) da análise de Log.
