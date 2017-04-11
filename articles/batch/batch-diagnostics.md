<properties
   pageTitle="Log de diagnóstico lote Azure | Microsoft Azure"
   description="Gravar e analisar os eventos de log de diagnóstico para recursos de conta do Azure lote como pools e tarefas."
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="10/12/2016"
   ms.author="marsma"/>

# <a name="azure-batch-diagnostic-logging"></a>Log de diagnóstico lote Azure

Como com muitos serviços Azure, o serviço de lote emite eventos de log para determinados recursos durante o ciclo de vida do recurso. Você pode ativar logs de diagnóstico do Azure lote para registrar eventos para recursos como pools e tarefas e, em seguida, use os logs para avaliação de diagnóstico e monitoramento. Eventos como pool de criar, excluir pool, início da tarefa, tarefa concluída e outras pessoas estão incluídas em logs de diagnóstico do lote.

>[AZURE.NOTE] Este artigo discute o log de eventos para próprios recursos de conta lote, não de trabalho e dados de saída da tarefa. Para obter detalhes sobre como armazenar os dados de saída de seus trabalhos e tarefas, consulte [saída persistir Azure lote de trabalho e tarefa](batch-task-output.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Conta do Azure em lotes](batch-account-create-portal.md)

* [Conta de armazenamento Azure](../storage/storage-create-storage-account.md#create-a-storage-account)

  Para manter os logs de diagnóstico de lote, você deve criar uma conta de armazenamento do Azure onde Azure armazenará os logs. Você especifica essa conta de armazenamento quando você [Habilitar o log de diagnóstico](#enable-diagnostic-logging) para a sua conta de lote. A conta de armazenamento que você especificar quando você habilita o conjunto de log não é a mesma que uma conta de armazenamento vinculado referida nos artigos [pacotes de aplicativos](batch-application-packages.md) e [persistência de saída de tarefa](batch-task-output.md) .

  >[AZURE.WARNING] Você é **cobrado** para os dados armazenados em sua conta de armazenamento do Azure. Isso inclui os logs de diagnóstico abordados neste artigo. Tenha isto em mente ao criar sua [política de retenção de log](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md).

## <a name="enable-diagnostic-logging"></a>Habilitar o log de diagnóstico

Log de diagnóstico não está habilitado por padrão para a sua conta de lote. Você deve ativar explicitamente o log de diagnóstico para cada conta de lote que você deseja monitorar:

[Como habilitar a coleção de Logs de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs)

Recomendamos que você leia o artigo de [Visão geral do Azure Logs de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) completo para obter uma compreensão de não apenas como habilitar registro em log, mas as categorias de log compatíveis com os vários serviços do Azure. Por exemplo, lote Azure atualmente oferece suporte a uma categoria de log: **Logs de serviço**.

## <a name="service-logs"></a>Logs de serviço

Logs de serviço de lote Azure contêm os eventos emitidos pelo serviço Azure lote durante o ciclo de vida de um recurso de lote como um pool ou uma tarefa. Cada evento emitido por lote é armazenado na conta de armazenamento especificada no formato JSON. Por exemplo, este é o corpo de uma amostra **pool criar evento**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Cada corpo de evento reside em um arquivo de .json na conta de armazenamento do Azure especificados. Se quiser acessar os logs diretamente, você talvez queira examinar o [esquema de Logs de diagnóstico na conta de armazenamento](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

## <a name="service-log-events"></a>Eventos de Log do serviço

O serviço de lote atualmente emite os seguintes eventos de Log do serviço. Esta lista pode não estar completa, desde que eventos adicionais podem ter sido adicionados desde a última vez em que este artigo foi atualizado.

| **Eventos de Log do serviço** |
| ------------------ |
| [Criar pool][pool_create] |
| [Início de exclusão do pool][pool_delete_start] |
| [Excluir pool concluída][pool_delete_complete] |
| [Início de redimensionamento do pool][pool_resize_start] |
| [Redimensionamento de pool concluída][pool_resize_complete] |
| [Início da tarefa][task_start] |
| [Tarefa concluída][task_complete] |
| [Falhas de tarefa][task_fail] |

## <a name="next-steps"></a>Próximas etapas

Além de armazenar eventos de log de diagnóstico em uma conta de armazenamento do Azure, você também pode transmitir eventos de Log de serviço de lote a um [Hub de evento do Azure](../event-hubs/event-hubs-what-is-event-hubs.md)e enviá-los para [Análise de Log do Azure](../log-analytics/log-analytics-overview.md).

* [Transmitir Azure Logs de diagnóstico para Hubs de evento](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)

  Transmita eventos de diagnóstico de lote para o serviço de ingresso dados altamente escaláveis, Hubs de evento. Hubs de evento podem inclusão milhões de eventos por segundo, que você pode transformar e armazenar usando qualquer provedor de análise em tempo real.

* [Analisar os logs de diagnóstico Azure usando a análise de Log](../log-analytics/log-analytics-azure-storage-json.md)

  Envie os logs de diagnósticos para análise de Log onde você pode analisá-los no portal do pacote de gerenciamento de operações (OMS) ou exportá-los para análise no Power BI ou no Excel.

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx
