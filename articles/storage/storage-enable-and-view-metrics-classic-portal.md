<properties 
    pageTitle="Habilitando métricas de armazenamento no portal do Azure | Microsoft Azure" 
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

# <a name="enabling-storage-metrics-and-viewing-metrics-data"></a>Habilitando métricas de armazenamento e exibição de dados de métricas

[AZURE.INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Visão geral

Por padrão, métricas de armazenamento não está habilitada para os serviços de armazenamento. Você pode habilitar o monitoramento usando o [Portal de clássico do Azure](https://manage.windowsazure.com), Windows PowerShell, ou programaticamente por meio de uma API de armazenamento.

Quando você habilita métricas de armazenamento, você deve escolher um período de retenção para os dados: esse período determina quanto tempo o armazenamento de serviço mantém as avaliações e encargos você para o espaço necessário para armazená-los. Normalmente, você deve usar um período de retenção mais curto para minutos métricas de métricas por hora devido o espaço extra significativo necessário para métricas de minuto. Você deve escolher um período de retenção, de forma que você tem tempo suficiente para analisar os dados e baixar qualquer métricas que você deseja manter para fins de relatórios de análise off-line ou. Lembre-se de que você também será cobrado para baixar dados de métricas de sua conta de armazenamento.

## <a name="how-to-enable-storage-metrics-using-the-azure-classic-portal"></a>Como habilitar métricas de armazenamento usando o Portal de clássico do Azure

No [Portal de clássico do Azure](https://manage.windowsazure.com), você pode usar a página Configurar para uma conta de armazenamento para controle métricas de armazenamento. Para monitoramento, você pode definir um período de retenção e de um nível em dias para cada um dos blobs, tabelas e filas. Em cada caso, o nível é um destes procedimentos:

- Desligado — Sem medições são coletadas.

- Mínimo — Métricas de armazenamento coleta um conjunto básico de métricas como entrada/saída, disponibilidade, latência e porcentagens de sucesso, que são agregadas para os serviços do Blob, tabela e fila.

- Detalhado — Métricas de armazenamento coleta um conjunto completo de métricas que incluem as mesmas métricas para cada operação de API, além das métricas de nível de serviço de armazenamento. Métricas detalhadas habilitar análise detalhada dos problemas que ocorrem durante as operações do aplicativo.

Observe que o Portal de clássico do Azure não atualmente permitem configurar métricas de minuto na sua conta de armazenamento; Você deve habilitar o minutos métricas usando o PowerShell ou programaticamente.


## <a name="how-to-enable-storage-metrics-using-powershell"></a>Como habilitar métricas de armazenamento usando o PowerShell

Você pode usar o PowerShell no seu computador local para configurar métricas de armazenamento em sua conta de armazenamento usando o cmdlet Get-AzureStorageServiceMetricsProperty para recuperar as configurações atuais do PowerShell do Azure e o cmdlet Set-AzureStorageServiceMetricsProperty para alterar as configurações atuais.

Os cmdlets que controlam métricas de armazenamento use os parâmetros a seguir:

- Valores possíveis MetricsType são horas e minutos.

- Valores possíveis ServiceType são Blob, fila e tabela.

- Valores possíveis MetricsLevel são nenhum (equivalente para desativá-lo no Portal de clássico do Azure), o serviço (equivalente ao mínimo no Portal de clássico do Azure) e ServiceAndApi (equivalente ao detalhado no Portal de clássico do Azure).

Por exemplo, o seguinte comando alterna em métricas de minuto para o serviço de blob na sua conta de armazenamento padrão com a período de retenção definido para cinco dias:

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

Quando você configurou métricas de armazenamento para monitorar a sua conta de armazenamento, ele registra as métricas em um conjunto de tabelas conhecidos em sua conta de armazenamento. Você pode usar a página de Monitor para sua conta de armazenamento no Portal de clássico do Azure para exibir as métricas por hora conforme eles se tornam disponíveis em um gráfico. Nesta página no Portal de clássico do Azure, você pode:

- Selecione quais métricas Plotar no gráfico (a opção de métricas disponíveis dependem se você escolheu monitoramento detalhado ou mínima do serviço na página Configurar).


- Selecione o intervalo de tempo para as métricas exibidas no gráfico.


- Escolha usar uma escala absoluta ou relativa para plotar as métricas.


- Configure alertas de email para notificá-lo quando uma métrica específico alcança um determinado valor.


Se você quiser para baixar as métricas para armazenamento de longo prazo ou analisá-los localmente, você precisará usar uma ferramenta ou escrever um código para ler as tabelas. Você deve baixar as métricas minutos para análise. As tabelas não aparece se você lista todas as tabelas em sua conta de armazenamento, mas você pode acessá-los diretamente por nome. Muitas ferramentas de navegação de armazenamento de terceiros estejam cientes dessas tabelas e permitem que você vê-los diretamente (consulte o postagem de blog [Gerenciadores de armazenamento do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) para obter uma lista de ferramentas disponíveis).

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

Considere configurar alertas no Portal de clássico do Azure na página do Monitor para que métricas de armazenamento automaticamente pode notificá-lo de quaisquer alterações importantes no comportamento dos seus serviços de armazenamento. Se você usar uma ferramenta de Gerenciador de armazenamento para baixar esses dados de métricas em um formato delimitado, você pode usar o Microsoft Excel para analisar os dados. Consulte o blog postagens [Gerenciadores de armazenamento do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) para uma lista de ferramentas do explorer de armazenamento disponível.



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

## <a name="next-steps"></a>Próximas etapas:
[Habilitando a análise de armazenamento log e acessar dados de Log](https://msdn.microsoft.com/library/dn782840.aspx)
 