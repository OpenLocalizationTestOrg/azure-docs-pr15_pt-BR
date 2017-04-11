<properties 
   pageTitle="Exibir logs de diagnósticos para análise de Lucerne de dados do Azure | Microsoft Azure" 
   description="Noções básicas de como configurar e acessar logs de diagnóstico para análise de Lucerne de dados do Azure " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="Blackmist" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="08/11/2016"
   ms.author="larryfr"/>

# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Acesse os logs de diagnósticos para análise de Lucerne de dados do Azure

Saiba mais sobre como habilitar o log de diagnóstico para a sua conta de dados Lucerne Analytics e como exibir os logs coletados para sua conta.

Organizações podem habilitar o log de diagnóstico para sua conta do Azure dados Lucerne Analytics coletar trilhas de auditoria de acesso de dados. Esses logs fornecem informações como:

* Uma lista de usuários que acessado os dados.
* Frequência os dados são acessados.
* A quantidade de dados é armazenado na conta.

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).
- **Ativar sua assinatura do Azure** para dados Lucerne Analytics Public Preview. Consulte [as instruções](data-lake-analytics-get-started-portal.md#signup).
- **Conta de análise de Lucerne de dados do azure**. Siga as instruções contidas em [Introdução ao Azure dados Lucerne análise usando o portal do Azure](data-lake-analytics-get-started-portal.md).

## <a name="enable-logging"></a>Habilitar o log

1. Entrar novo [portal Azure](https://portal.azure.com).

2. Abra sua conta de dados Lucerne Analytics, da lâmina sua conta dados Lucerne análise, clique em **configurações**e, em seguida, clique em **Configurações de diagnóstico**.

3. Na lâmina **Diagnóstico** , faça as seguintes alterações para configurar o log de diagnóstico.

    ![Habilitar o log de diagnóstico] (./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Ativar logs de diagnóstico")

    * Definir **Status** como **em** para habilitar o log de diagnóstico.
    * Você pode optar por armazenar/processo os dados de duas maneiras diferentes.
        * Selecione **Exportar para o evento Hub** transmitir dados de log a um Hub de evento do Azure. Use esta opção se você tiver um pipeline de processamento downstream para analisar os logs de entrada em tempo real. Se você selecionar essa opção, você deve fornecer os detalhes para o Hub de evento do Azure que você deseja usar.
        * Selecione **Exportar para conta de armazenamento** para armazenar logs a uma conta de armazenamento do Azure. Use esta opção se você quiser arquivar os dados. Se você selecionar essa opção, você deve fornecer uma conta de armazenamento do Azure para salvar os logs.
    * Especifique se você deseja obter os logs de auditoria, logs de solicitação ou ambos.
    * Especifique o número de dias para o qual os dados devem ser mantidos.
    * Clique em **Salvar**.

Depois que você tiver ativado as definições de diagnóstico, você pode assistir os logs na guia **Logs de diagnóstico** .

## <a name="view-logs"></a>Exibir logs

Há duas maneiras de exibir os dados de log para sua conta de dados Lucerne Analytics.

* Das configurações de conta da análise de Lucerne de dados
* A conta de armazenamento do Azure onde os dados são armazenados

### <a name="using-the-data-lake-analytics-settings-view"></a>Usando o modo de exibição de dados Lucerne Analytics configurações

1. Da lâmina de **configurações** de conta seus dados Lucerne análise, clique em **Logs de diagnóstico**.

    ![Log de diagnóstico de exibição] (./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs.png "Exibir logs de diagnóstico") 

2. Na lâmina **Logs de diagnóstico** , você deve ver os logs categorizados por **Logs de auditoria** e **Logs de solicitação**.
    * Logs de solicitação capturam todas as solicitações de API feitas na conta de dados Lucerne Analytics.
    * Logs de auditoria são semelhantes às solicitar Logs, mas oferece uma divisão muito mais detalhada das operações estão sendo executadas na conta de dados Lucerne Analytics. Por exemplo, uma chamada de API de carregamento único em logs de solicitação pode resultar em várias operações de "Acrescentar" nos logs de auditoria.

3. Clique no link de **Download** para uma entrada de log baixar os logs.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>A conta de armazenamento do Azure que contém dados de log

1. Abra a lâmina de conta de armazenamento do Azure associada a análise de dados Lucerne para registrar em log e, em seguida, clique em Blobs. A lâmina de **serviço Blob** lista dois contêineres.

    ![Log de diagnóstico de exibição] (./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs-storage-account.png "Exibir logs de diagnóstico")

    * O contêiner **ideias de logs de auditoria** contém os logs de auditoria.
    * O contêiner **ideias de logs de solicitações** contém os logs de solicitação.

2. Dentro desses contêineres, os logs são armazenados em estrutura a seguir.

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json
    
    > [AZURE.NOTE] O `##` entradas no caminho contêm o ano, mês, dia e hora em que o log foi criado. Dados Lucerne Analytics cria cada hora, de um arquivo e então `m=` sempre contém um valor de `00`.

    Como exemplo, o caminho completo para um log de auditoria pode ser:
    
        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    Da mesma forma, o caminho completo para um log de solicitação poderia ser:
    
        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Estrutura de log

Os logs de auditoria e solicitação estão em formato JSON. Nesta seção, vamos examinar a estrutura do JSON para solicitação e logs de auditoria.

### <a name="request-logs"></a>Logs de solicitação

Eis um exemplo de entrada no log de solicitação formatada em JSON. Cada blob tem um objeto de raiz chamado **registros** que contém uma matriz de objetos de log.

    {
    "records": 
      [     
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Solicitar o esquema de log

| Nome            | Tipo   | Descrição                                                                    |
|-----------------|--------|--------------------------------------------------------------------------------|
| tempo            | Cadeia de caracteres | O carimbo de hora (no UTC) do log                                              |
| identificação de recurso      | Cadeia de caracteres | A identificação do recurso que levaram operação colocar em                            |
| categoria        | Cadeia de caracteres | A categoria de log. Por exemplo, **solicitações**.                                   |
| operationName   | Cadeia de caracteres | Nome da operação que está conectada. Por exemplo, GetAggregatedJobHistory.              |
| resultType      | Cadeia de caracteres | O status da operação, por exemplo, 200.                                 |
| callerIpAddress | Cadeia de caracteres | O endereço IP do cliente que está fazendo a solicitação                                |
| correlationId   | Cadeia de caracteres | A id do log. Este valor pode ser usado para agrupar um conjunto de entradas de log relacionados |
| identidade        | Objeto | A identidade que gerou o log                                            |
| Propriedades      | JSON   | Consulte a próxima seção (esquema de propriedades do log de solicitação) para obter detalhes |

#### <a name="request-log-properties-schema"></a>Esquema de propriedades do log de solicitação

| Nome                 | Tipo   | Descrição                                               |
|----------------------|--------|-----------------------------------------------------------|
| HttpMethod           | Cadeia de caracteres | O método HTTP usado para a operação. Por exemplo, obter. |
| Caminho                 | Cadeia de caracteres | O caminho a operação foi executado em                   |
| RequestContentLength | int    | O comprimento de conteúdo da solicitação HTTP                    |
| ClientRequestId      | Cadeia de caracteres | A Id que identifica exclusivamente essa solicitação              |
| Hora de início            | Cadeia de caracteres | A hora em que o servidor recebeu a solicitação         |
| Hora de término              | Cadeia de caracteres | A hora em que o servidor enviou uma resposta              |

### <a name="audit-logs"></a>Logs de auditoria

Eis um exemplo de entrada no log de auditoria formatados como JSON. Cada blob tem um objeto de raiz chamado **registros** que contém uma matriz de objetos de log

    {
    "records": 
      [     
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job", 
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Esquema de log de auditoria

| Nome            | Tipo   | Descrição                                                                    |
|-----------------|--------|--------------------------------------------------------------------------------|
| tempo            | Cadeia de caracteres | O carimbo de hora (no UTC) do log                                              |
| identificação de recurso      | Cadeia de caracteres | A identificação do recurso que levaram operação colocar em                            |
| categoria        | Cadeia de caracteres | A categoria de log. Por exemplo, **auditoria**.                                      |
| operationName   | Cadeia de caracteres | Nome da operação que está conectada. Por exemplo, JobSubmitted.              |
| resultType | Cadeia de caracteres | Um substatus para o status do trabalho (operationName). |
| resultSignature | Cadeia de caracteres | Detalhes adicionais sobre o status do trabalho (operationName). |
| identidade      | Cadeia de caracteres | O usuário que solicitado a operação. Por exemplo, susan@contoso.com.                                 |
| Propriedades      | JSON   | Consulte a próxima seção (esquema de propriedades do log de auditoria) para obter detalhes |

> [AZURE.NOTE] __resultType__ __resultSignature__ fornecem informações sobre o resultado de uma operação e conter apenas um valor se uma operação foi concluída. Por exemplo, eles contêm um valor quando __operationName__ contém um valor de __JobStarted__ ou __JobEnded__.

#### <a name="audit-log-properties-schema"></a>Esquema de propriedades do log de auditoria

| Nome       | Tipo   | Descrição                              |
|------------|--------|------------------------------------------|
| JobId | Cadeia de caracteres | A identificação atribuída ao trabalho  |
| Nome_do_trabalho | Cadeia de caracteres | O nome fornecido para o trabalho |
| JobRunTime | Cadeia de caracteres | O tempo de execução usado para processar o trabalho |
| SubmitTime | Cadeia de caracteres | A hora (no UTC) que o trabalho foi enviado |
| Hora de início | Cadeia de caracteres | A hora em que o trabalho foi iniciado executando após o envio (no UTC). |
| Hora de término | Cadeia de caracteres | A hora em que o trabalho foi concluído. |
| Paralelismo | Cadeia de caracteres | O número de unidades de dados Lucerne Analytics solicitado para este trabalho durante o envio. |

> [AZURE.NOTE] __SubmitTime__, __hora de início__, __hora de término__ __paralelismo__ fornecem informações sobre uma operação e conter apenas um valor se uma operação foi iniciado ou concluídas. Por exemplo, __SubmitTime__ contém um valor após __operationName__ indica __JobSubmitted__.

## <a name="process-the-log-data"></a>Processar os dados de log

Análise de Lucerne dados Azure fornece uma amostra processar e analisar os dados de log. Você pode encontrar a amostra em [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 


## <a name="next-steps"></a>Próximas etapas

- [Visão geral dos dados Azure Lucerne Analytics](data-lake-analytics-overview.md)


