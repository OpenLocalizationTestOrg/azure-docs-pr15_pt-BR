<properties 
   pageTitle="Exibir logs de diagnóstico para armazenamento de Lucerne de dados do Azure | Microsoft Azure" 
   description="Noções básicas de como configurar e acessar logs de diagnóstico para armazenamento de Lucerne de dados do Azure " 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>Acesse os logs de diagnósticos para armazenamento de Lucerne de dados do Azure

Saiba mais sobre como habilitar o log de diagnóstico para a sua conta de armazenamento de Lucerne de dados e como exibir os logs coletados para sua conta.

Organizações podem habilitar o log de diagnóstico para sua conta de armazenamento de Lucerne de dados do Azure coletar trilhas de auditoria de acesso de dados que fornece informações como lista de usuários acessando os dados, frequência os dados são acessados, a quantidade de dados está armazenado de conta, etc.

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).

- **Conta de armazenamento de Lucerne de dados do azure**. Siga as instruções contidas em [Introdução ao armazenamento de Lucerne de dados do Azure usando o Portal do Azure](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>Habilitar o log de diagnóstico para a sua conta de armazenamento de Lucerne de dados

1. Entrar novo [Portal Azure](https://portal.azure.com).

2. Abra sua conta de armazenamento de Lucerne de dados e em seu blade de conta de armazenamento de Lucerne de dados, clique em **configurações**e, em seguida, clique em **Configurações de diagnóstico**.

3. Na lâmina **Diagnóstico** , faça as seguintes alterações para configurar o log de diagnóstico.

    ![Habilitar o log de diagnóstico] (./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Ativar logs de diagnóstico")

    * Definir **Status** como **em** para habilitar o log de diagnóstico.
    * Você pode optar por armazenar/processo os dados de duas maneiras diferentes.
        * Selecione a opção para **Exportar para o evento Hub** transmitir dados de log a um Hub de evento do Azure. Provavelmente você usará esta opção se você tiver um pipeline de processamento downstream para analisar os logs de entrada em tempo real. Se você selecionar essa opção, você deve fornecer os detalhes para o Hub de evento do Azure que você deseja usar.
        * Selecione a opção **Exportar para conta de armazenamento** para armazenar logs a uma conta de armazenamento do Azure. Você use esta opção se você quiser arquivar os dados que serão processados em lotes em uma data posterior. Se você selecionar essa opção, você deve fornecer uma conta de armazenamento do Azure para salvar os logs.
    * Especifique se você deseja obter os logs de auditoria, logs de solicitação ou ambos.
    * Especifique o número de dias para o qual os dados devem ser mantidos.
    * Clique em **Salvar**.

Depois que você tiver ativado as definições de diagnóstico, você pode assistir os logs na guia **Logs de diagnóstico** .

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>Exibir logs de diagnóstico para a sua conta de armazenamento de Lucerne de dados

Há duas maneiras de exibir os dados de log para sua conta de armazenamento de Lucerne de dados.

* No modo de configurações de conta de armazenamento de Lucerne de dados
* A conta de armazenamento do Azure onde os dados são armazenados

### <a name="using-the-data-lake-store-settings-view"></a>Usando o modo de exibição de dados Lucerne as configurações do repositório

1. Da lâmina de **configurações** de conta seu armazenamento de Lucerne de dados, clique em **Logs de diagnóstico**.

    ![Log de diagnóstico de exibição] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Exibir logs de diagnóstico") 

2. Na lâmina **Logs de diagnóstico** , você deve ver os logs categorizados por **Logs de auditoria** e **Logs de solicitação**.
    * Logs de solicitação capturam todas as solicitações de API feitas na conta do repositório de Lucerne de dados.
    * Logs de auditoria são semelhantes às solicitar Logs, mas oferece uma divisão muito mais detalhada das operações estão sendo executadas na conta do repositório de Lucerne de dados. Por exemplo, uma chamada de API de carregamento único em logs de solicitação pode resultar em várias operações de "Acrescentar" nos logs de auditoria.

3. Clique no link **Baixar** contra cada entrada de log para baixar os logs.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>A conta de armazenamento do Azure que contém dados de log

1. Abra a lâmina de conta de armazenamento do Azure associada ao armazenamento de Lucerne de dados para o registro em log e, em seguida, clique em Blobs. A lâmina de **serviço Blob** lista dois contêineres.

    ![Log de diagnóstico de exibição] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Exibir logs de diagnóstico")

    * O contêiner **ideias de logs de auditoria** contém os logs de auditoria.
    * O contêiner **solicitações de logs de ideias** contém os logs de solicitação.

2. Dentro desses contêineres, os logs são armazenados em estrutura a seguir.

    ![Log de diagnóstico de exibição] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Exibir logs de diagnóstico")

    Como exemplo, o caminho completo para um log de auditoria pode ser`https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

    Similary, o caminho completo para um log de solicitação poderia ser`https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Compreender a estrutura dos dados log

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
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
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
| operationName   | Cadeia de caracteres | Nome da operação que está conectada. Por exemplo, getfilestatus.              |
| resultType      | Cadeia de caracteres | O status da operação, por exemplo, 200.                                 |
| callerIpAddress | Cadeia de caracteres | O endereço IP do cliente que está fazendo a solicitação                                |
| correlationId   | Cadeia de caracteres | A identificação do log que pode usada para agrupar um conjunto de entradas de log relacionados |
| identidade        | Objeto | A identidade que gerou o log                                            |
| Propriedades      | JSON   | Veja abaixo para obter detalhes                                                          |

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
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
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
| operationName   | Cadeia de caracteres | Nome da operação que está conectada. Por exemplo, getfilestatus.              |
| resultType      | Cadeia de caracteres | O status da operação, por exemplo, 200.                                 |
| correlationId   | Cadeia de caracteres | A identificação do log que pode usada para agrupar um conjunto de entradas de log relacionados |
| identidade        | Objeto | A identidade que gerou o log                                            |
| Propriedades      | JSON   | Veja abaixo para obter detalhes                                                          |

#### <a name="audit-log-properties-schema"></a>Esquema de propriedades do log de auditoria

| Nome       | Tipo   | Descrição                              |
|------------|--------|------------------------------------------|
| StreamName | Cadeia de caracteres | O caminho a operação foi executado em  |


## <a name="samples-to-process-the-log-data"></a>Exemplos para processar os dados de log

Armazenamento de Lucerne de dados do Azure fornece uma amostra processar e analisar os dados de log. Você pode encontrar a amostra em [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 


## <a name="see-also"></a>Consulte também

- [Visão geral do Azure Lucerne de repositório de dados](data-lake-store-overview.md)
- [Proteger os dados no repositório de Lucerne de dados](data-lake-store-secure-data.md)

