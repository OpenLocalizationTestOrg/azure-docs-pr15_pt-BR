<properties
    pageTitle="Habilitando métricas de armazenamento no Portal do Azure | Microsoft Azure"
    description="Como habilitar métricas de armazenamento para os serviços do Blob, fila, tabela e arquivo"
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Habilitando métricas de armazenamento do Azure e exibir dados de métricas

[AZURE.INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Visão geral

Por padrão, métricas de armazenamento não está habilitada para os serviços de armazenamento. Você pode habilitar o monitoramento por meio do [Portal do Azure](https://portal.azure.com) ou o Windows PowerShell, ou por programação via biblioteca de armazenamento do cliente.

Quando você habilita métricas de armazenamento, você deve escolher um período de retenção para os dados: esse período determina quanto tempo o armazenamento de serviço mantém as avaliações e encargos você para o espaço necessário para armazená-los. Normalmente, você deve usar um período de retenção mais curto para minutos métricas de métricas por hora devido o espaço extra significativo necessário para métricas de minuto. Você deve escolher um período de retenção, de forma que você tem tempo suficiente para analisar os dados e baixar qualquer métricas que você deseja manter para fins de relatórios de análise off-line ou. Lembre-se de que você também será cobrado para baixar dados de métricas de sua conta de armazenamento.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Como habilitar métricas usando o Portal do Azure

Siga estas etapas para habilitar métricas no [Portal do Azure](https://portal.azure.com):

1. Navegue até sua conta de armazenamento.
1. Abra a lâmina **configurações** e selecione **Diagnóstico**.
1. Certifique-se de que o **Status** está definido como **ativado**.
1. Selecione as métricas para os serviços que você deseja monitorar.
2. Especifica uma política de retenção para indicar quanto tempo para reter métricas e dados de log.

Observe que o [Portal Azure](https://portal.azure.com) não atualmente permitem configurar métricas de minuto na sua conta de armazenamento; Você deve habilitar o minutos métricas usando o PowerShell ou programaticamente.

## <a name="how-to-enable-metrics-using-powershell"></a>Como habilitar métricas usando o PowerShell

Você pode usar o PowerShell no seu computador local para configurar métricas de armazenamento em sua conta de armazenamento usando o cmdlet Get-AzureStorageServiceMetricsProperty para recuperar as configurações atuais do PowerShell do Azure e o cmdlet Set-AzureStorageServiceMetricsProperty para alterar as configurações atuais.

Os cmdlets que controlam métricas de armazenamento use os parâmetros a seguir:

- MetricsType: valores possíveis são horas e minutos.

- ServiceType: valores possíveis são Blob, fila e tabela.

- MetricsLevel: valores possíveis são nenhuma, o serviço e ServiceAndApi.

Por exemplo, o seguinte comando alterna em métricas de minuto para o serviço de Blob na sua conta de armazenamento padrão com a período de retenção definido para cinco dias:

`Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`

O comando a seguir recupera os atual por hora métricas nível e retenção dias para o serviço de blob na sua conta de armazenamento padrão:

`Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob`

Para obter informações sobre como configurar cmdlets do PowerShell do Azure para trabalhar com sua assinatura do Azure e como selecionar a conta de armazenamento padrão para usar, consulte: [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Como habilitar métricas de armazenamento por programação

O trecho c# a seguir mostra como habilitar o log para o serviço de Blob usando a biblioteca de cliente de armazenamento para .NET e métricas:

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Enable Storage Analytics logging and set retention policy to 10 days.
    ServiceProperties properties = new ServiceProperties();
    properties.Logging.LoggingOperations = LoggingOperations.All;
    properties.Logging.RetentionDays = 10;
    properties.Logging.Version = "1.0";

    // Configure service properties for metrics. Both metrics and logging must be set at the same time.
    properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.HourMetrics.RetentionDays = 10;
    properties.HourMetrics.Version = "1.0";

    properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.MinuteMetrics.RetentionDays = 10;
    properties.MinuteMetrics.Version = "1.0";

    // Set the default service version to be used for anonymous requests.
    properties.DefaultServiceVersion = "2015-04-05";

    // Set the service properties.
    blobClient.SetServiceProperties(properties);


## <a name="viewing-storage-metrics"></a>Exibindo métricas de armazenamento

Depois de configurar métricas de armazenamento Analytics para monitorar a sua conta de armazenamento, a análise de armazenamento registra as métricas em um conjunto de tabelas conhecidos em sua conta de armazenamento. Você pode configurar gráficos para exibir métricas por hora no [Portal do Azure](https://portal.azure.com):

1. Navegue até sua conta de armazenamento no [Portal do Azure](https://portal.azure.com).
2. Na seção **monitoramento** , clique em **Adicionar blocos** para adicionar um novo gráfico. Na **Galeria de peças**, selecione a métrica que você deseja exibir e arraste-a para a seção de **monitoramento** .
3. Para editar quais medidas são exibidas em um gráfico, clique no link **Editar** . Você pode adicionar ou remover métricas individuais, marcando ou desmarcando-los.
4. Clique em **Salvar** quando terminar de métricas de edição.

Se você quiser para baixar as métricas para armazenamento de longo prazo ou analisá-los localmente, você precisará:

- Use uma ferramenta que está ciente dessas tabelas e permitirá que você visualize e baixe-os.
- Escreva um aplicativo personalizado ou script para ler e armazenar as tabelas.

Muitas ferramentas de navegação de armazenamento de terceiros estão cientes dessas tabelas e permitem que você vê-los diretamente.
Consulte [Gerenciadores de armazenamento do Azure](storage-explorers.md) para obter uma lista de ferramentas disponíveis.

> [AZURE.NOTE] Começando com a versão 0.8.0 do [Microsoft Azure armazenamento Explorer] (http://storageexplorer.com/), você agora poderá exibir e baixar as tabelas de métricas de análise.

Para acessar as tabelas de análise programaticamente, observe que as tabelas de análise não aparecem se você listar todas as tabelas em sua conta de armazenamento. Você pode acessá-los diretamente por nome ou usar a [API CloudAnalyticsClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) na biblioteca de cliente .NET para os nomes de tabela de consulta.

### <a name="hourly-metrics"></a>Métricas por hora
- $MetricsHourPrimaryTransactionsBlob
- $MetricsHourPrimaryTransactionsTable
- $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Métricas de minuto
- $MetricsMinutePrimaryTransactionsBlob
- $MetricsMinutePrimaryTransactionsTable
- $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Capacidade
- $MetricsCapacityBlob

Você pode obter detalhes completos dos esquemas dessas tabelas no [Esquema de tabela de métricas de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343264.aspx). As linhas de amostra abaixo mostram apenas um subconjunto das colunas disponíveis, mas ilustram alguns recursos importantes da maneira como métricas de armazenamento salva essas métricas:

| PartitionKey  |       RowKey       |                    Carimbo de hora | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Disponibilidade | AverageE2ELatency | AverageServerLatency | PercentSuccess |
|---------------|:------------------:|-----------------------------:|---------------|-----------------------|--------------|-------------|--------------|-------------------|----------------------|----------------|
| 20140522T1100 |      usuário; Todos os      | 2014-05-22T11:01:16.7650250Z | 7             | 7                     | 4003         | 46801       | 100          | 104.4286          | 6.857143             | 100            |
| 20140522T1100 | usuário; QueryEntities | 2014-05-22T11:01:16.7640250Z | 5             | 5                     | 2694         | 45951       | 100          | 143.8             | 7.8                  | 100            |
| 20140522T1100 |  usuário; QueryEntity  | 2014-05-22T11:01:16.7650250Z | 1             | 1                     | 538          | 633         | 100          | 3                 | 3                    | 100            |
| 20140522T1100 | usuário; UpdateEntity  | 2014-05-22T11:01:16.7650250Z | 1             | 1                     | 771          | 217         | 100          | 9                 | 6                    | 100               |

Dados de métricas minuto neste exemplo, a chave da partição usa o horário em resolução de minuto. A chave de linha identifica o tipo de informação armazenada na linha e isso é composto por duas partes de informações, o tipo de acesso e o tipo de solicitação:

- O tipo de acesso é usuário ou sistema, onde usuário se refere a todas as solicitações de usuário para o serviço de armazenamento e sistema refere-se a solicitações feitas por análise de armazenamento.

- O tipo de solicitação é tudo que nesse caso é uma linha de resumo ou identifica a API específica como QueryEntity ou UpdateEntity.


Os dados de exemplo acima mostram todos os registros por um único minuto (começando em 11:00 AM), para que o número de solicitações de QueryEntities mais o número de QueryEntity solicita mais o número de UpdateEntity solicitações adicionar até sete, que é o total mostrado na fileira de usuário: All. Da mesma forma, você pode obter a latência de ponta a ponta média 104.4286 na fileira de usuário: todas as Calculando ((143.8 * 5) + 3 + 9) / 7.

Considere configurar alertas no [Portal do Azure](https://portal.azure.com) na página do Monitor para que métricas de armazenamento automaticamente pode notificá-lo de quaisquer alterações importantes no comportamento dos seus serviços de armazenamento. Se você usar uma ferramenta de Gerenciador de armazenamento para baixar esses dados de métricas em um formato delimitado, você pode usar o Microsoft Excel para analisar os dados. Consulte o blog postagens [Gerenciadores de armazenamento do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) para uma lista de ferramentas do explorer de armazenamento disponível.



## <a name="accessing-metrics-data-programmatically"></a>Acessando dados de métricas programaticamente

A listagem a seguir mostra o exemplo código c# que acessa as minutos métricas para um intervalo de minutos e exibe os resultados em uma janela de console. Ele usa a biblioteca de armazenamento do Azure versão 4 que inclui a classe de CloudAnalyticsClient que simplifica acessando as tabelas de métricas de armazenamento.

    private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
    {
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");

    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
    Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
    var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
    var t = analyticsClient.GetMinuteMetricsTable(service);
    var opContext = new OperationContext();
    var query =
    from entity in metricsQuery
    // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
    // because they are calculated fields in the MetricsEntity class.
    // The PartitionKey identifies the DataTime of the metrics.
    where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0
    select entity;

    // Filter on "user" transactions after fetching the metrics from Table Storage.
    // (StartsWith is not supported using LINQ with Azure table storage)
    var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
    var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
    Console.WriteLine(resultString);
    }
    }

    private static string MetricsString(MetricsEntity entity, OperationContext opContext)
    {
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
    string.Format("Time: {0}, ", entity.Time) +
    string.Format("AccessType: {0}, ", entity.AccessType) +
    string.Format("TransactionType: {0}, ", entity.TransactionType) +
    string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;

    }




## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>Quais encargos você provoca quando você habilita métricas de armazenamento?

Grave solicitações para criar entidades de tabela para métricas cobradas as taxas padrão aplicável a todas as operações de armazenamento do Azure.

Ler e excluir solicitações por um cliente para dados de métricas também são faturáveis com taxas padrão. Se você tiver configurado uma política de retenção de dados, você não é cobrados quando o armazenamento do Azure exclui dados de métricas antigos. No entanto, se você excluir dados de análise, sua conta é cobrada para as operações de exclusão.

A capacidade usada pelas tabelas de métricas também é faturável: você pode usar o seguinte para estimar a quantidade de capacidade usada para armazenar dados de métricas:

- Se cada hora um serviço utiliza cada API em cada serviço, aproximadamente 148KB de dados está armazenado cada hora nas tabelas de transações métricas se você tiver habilitado o serviço e o nível de API resumo.

- Se cada hora um serviço utiliza cada API em cada serviço, aproximadamente 12KB de dados está armazenado cada hora nas tabelas de transações métricas se você tiver habilitado o nível de serviço apenas resumo.

- A tabela de capacidade para blobs tem duas linhas adicionadas a cada dia (desde o usuário tiver optado para logs): isso significa que todos os dias o tamanho desta tabela aumenta por até aproximadamente 300 bytes.

## <a name="next-steps"></a>Avançar-etapas:
[Permitindo o armazenamento de registro em log e acessar dados de Log](https://msdn.microsoft.com/library/dn782840.aspx)
