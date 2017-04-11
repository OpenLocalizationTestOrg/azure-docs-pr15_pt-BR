<properties
    pageTitle="Transmitir Azure Logs de diagnóstico para Hubs de evento | Microsoft Azure"
    description="Saiba como transmitir Logs de diagnóstico do Azure para Hubs de evento."
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
    ms.date="08/08/2016"
    ms.author="johnkem"/>

# <a name="stream-azure-diagnostic-logs-to-event-hubs"></a>Transmitir Azure Logs de diagnóstico para Hubs de evento

**[Logs de diagnóstico do Azure](monitoring-overview-of-diagnostic-logs.md)** pode ser transmitido em quase em tempo real para qualquer aplicativo usando a opção de "Exportar para o evento Hubs" incorporada no Portal ou habilitando a Id de regra de barramento de serviço em uma configuração de diagnóstico por meio do Azure Cmdlets do PowerShell ou CLI do Azure.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>O que você pode fazer com os Logs de diagnóstico e Hubs de evento
Aqui estão algumas maneiras que você pode usar o recurso de fluxo para Logs de diagnóstico:

- **Logs de fluxo para festa 3º log e sistemas de telemetria** – ao longo do tempo, Hubs de evento streaming se tornará o mecanismo de soluções de análise de log e conduza seus Logs de diagnóstico em SIEMs de terceiros.

- **Integridade do serviço de modo de exibição por streaming "caminho quente" dados PowerBI** – usando Hubs de evento, a análise de fluxo e PowerBI, você pode transformar facilmente seus dados de diagnóstico em quase em tempo real obtenção de informações em seus serviços Azure. [Este artigo documentação fornece uma visão geral de como configurar um evento Hubs, processar dados com a análise de fluxo e usar PowerBI como uma saída](../stream-analytics/stream-analytics-power-bi-dashboard.md). Eis algumas dicas para obter configurado com os Logs de diagnóstico:
    - Os Hubs de evento para uma categoria de Logs de diagnóstico é criado automaticamente quando você marcar a opção no portal ou ativá-lo através do PowerShell, para que você deseja selecionar os Hubs de evento no namespace barramento de serviço com o nome que começa com "ideias-"
    - Aqui está um exemplo de consulta de análise de fluxo que você pode usar para simplesmente analisar todos os dados de log da uma tabela de PowerBI:

```
SELECT
records.ArrayValue.[Properties you want to track]
INTO
[OutputSourceName – the PowerBI source]
FROM
[InputSourceName] AS e
CROSS APPLY GetArrayElements(e.records) AS records
```

- **Criar uma plataforma de log e telemetria personalizada** – se você já tiver uma plataforma de telemetria personalizados ou está pensando sobre a criação de um, altamente escaláveis publicação assinatura natureza do evento Hubs permite flexibilidade inclusão logs de diagnóstico. [Guia do consulte Dan Rosanova usando Hubs de evento em uma plataforma de telemetria escala global aqui](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Habilitar o streaming de Logs de diagnóstico
Você pode habilitar o streaming de Logs de diagnóstico programaticamente, por meio do portal do, ou usar a [API do Azure Monitor REST](https://msdn.microsoft.com/library/azure/dn931943.aspx). De qualquer forma, você escolher um Namespace de barramento de serviço e Hubs um evento é criada no namespace para cada categoria de log que habilitar. Uma **Categoria de Log** de diagnóstico é um tipo de log que um recurso pode coletar. Você pode selecionar quais categorias de log que você gostaria de coletar para um determinado recurso no Portal do Azure sob a lâmina de diagnóstico.

![Categorias de log no Portal](./media/monitoring-stream-diagnostic-logs-to-event-hubs/log-categories.png)

> [AZURE.WARNING] Habilitando e streaming logs de diagnóstico de computação [requer um conjunto diferente de etapas](../event-hubs/event-hubs-streaming-azure-diags-data.md)de recursos (por exemplo, VMs ou tecidos do serviço).

### <a name="via-powershell-cmdlets"></a>Por meio de Cmdlets do PowerShell
Para habilitar o streaming por meio do [Azure Cmdlets do PowerShell](insights-powershell-samples.md), você pode usar o `Set-AzureRmDiagnosticSetting` cmdlet com esses parâmetros:

```
Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

O serviço de regra é uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`, por exemplo, `/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{service bus namespace}/authorizationrules/RootManageSharedAccessKey`.


### <a name="via-azure-cli"></a>Via CLI Azure
Para habilitar o streaming por meio da [CLI do Azure](insights-cli-samples.md), você pode usar o `insights diagnostic set` comando assim:

```
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

Use o mesmo formato ID de regra de barramento de serviço conforme explicado para o Cmdlet do PowerShell.

###<a name="via-azure-portal"></a>Por meio do Portal do Azure
Para habilitar o streaming por meio do Portal do Azure, navegue até as configurações do diagnóstico de um recurso e selecione 'Exportar para o Hub de evento'.

![Exportar para Hubs de evento no Portal](./media/monitoring-stream-diagnostic-logs-to-event-hubs/portal-export.png)

Para configurá-lo, selecione um Namespace de barramento de serviço existente. Namespace selecionado será onde os Hubs de evento é criado (se esta for sua primeira logs de diagnóstico de tempo streaming) ou transmitidos para (se já houver recursos que são streaming categoria log para este namespace), e a política define as permissões que tem o mecanismo de streaming. Hoje, o fluxo para um evento Hubs requer permissões de gerenciar, ler e enviar. Você pode criar ou modificar políticas de acesso do serviço barramento Namespace compartilhadas no portal do clássico sob a guia "Configurar" para seu Namespace de barramento de serviço. Para atualizar uma dessas configurações de diagnóstico, o cliente deve ter a permissão de ListKey na regra de autorização de barramento de serviço.

##<a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Como posso consumir os dados do log de eventos Hubs?
Eis os dados de saída de amostra dos Hubs evento:

```
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| Nome do elemento | Descrição                                            |
|--------------|--------------------------------------------------------|
|registros       | Uma matriz de todos os eventos de log nessa carga.            |
|tempo          | Hora em que o evento ocorreu.                      |
|categoria      | Categoria de log para este evento.                           |
|identificação de recurso    | Identificação do recurso do recurso que gerou esse evento. |
|operationName | Nome da operação.                                 |
|nível         | Opcional. Indica o nível de log de eventos.               |
|Propriedades    | Propriedades do evento.                               |


Você pode exibir uma lista de todos os provedores de recursos com suporte a streaming hub de evento [aqui](monitoring-overview-of-diagnostic-logs.md).

##<a name="next-steps"></a>Próximas etapas
- [Leia mais sobre Logs de diagnóstico do Azure](monitoring-overview-of-diagnostic-logs.md)
- [Começar com Hubs de evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
