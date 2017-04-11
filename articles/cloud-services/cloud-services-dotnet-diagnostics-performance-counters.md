<properties
   pageTitle="Use contadores de desempenho no diagnóstico do Azure | Microsoft Azure"
   description="Use contadores de desempenho em serviços de nuvem Azure ou máquina virtual para localizar gargalos e ajustar o desempenho."
   services="cloud-services"
   documentationCenter=".net"
   authors="rboucher"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/29/2016"
   ms.author="robb" />

# <a name="create-and-use-performance-counters-in-an-azure-application"></a>Criar e usar contadores de desempenho em um aplicativo do Azure

Este artigo descreve os benefícios do e como colocar contadores de desempenho em seu aplicativo do Azure. Você pode usá-los para coletar dados, Encontre afunilamentos e ajustar o desempenho do sistema e do aplicativo.

Contadores de desempenho disponíveis para o Windows Server, IIS e ASP.NET também podem ser coletados e usados para determinar a integridade de funções da web Azure, funções de trabalho e máquinas virtuais. Você também pode criar e usar contadores de desempenho personalizados.  

Você pode examinar dados de contador de desempenho
1. Diretamente no host de aplicativo com a ferramenta de desempenho acessada usando a área de trabalho remota
2. Com o System Center Operations Manager usando o pacote de gerenciamento do Azure
3. Com outras ferramentas de monitoramento que acessam os dados de diagnósticos transferido para o armazenamento do Azure. Para obter mais informações, consulte [Store e exibir dados de diagnóstico no armazenamento do Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx) .  

Para obter mais informações sobre como monitorar o desempenho do seu aplicativo no [Azure portal clássico](http://manage.azure.com/), veja [como os serviços de nuvem do Monitor](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/).

Para obter orientações detalhadas adicionais sobre como criar um log e estratégia de rastreamento e usando o diagnóstico e outras técnicas para solucionar problemas e otimizar aplicativos do Azure, consulte [Solução de problemas de práticas recomendadas para desenvolvimento de aplicativos do Azure](https://msdn.microsoft.com/library/azure/hh771389.aspx).


## <a name="enable-performance-counter-monitoring"></a>Habilitar o monitoramento de contador de desempenho

Disco de desempenho não estão ativados por padrão. O aplicativo ou uma tarefa de inicialização deve modificar a configuração padrão do agente de diagnóstico para incluir os contadores de desempenho específicos que você deseja monitorar para cada instância de função.

### <a name="performance-counters-available-for-microsoft-azure"></a>Contadores de desempenho disponíveis para o Microsoft Azure

Azure fornece um subconjunto dos contadores de desempenho disponíveis para o Windows Server, IIS e a pilha do ASP.NET. A tabela a seguir lista alguns dos contadores de desempenho de interesse particular para aplicativos do Azure.

|Categoria de contador: Objeto (instância)|Nome do contador      |Referência|
|---|---|---|
|Exceções do .NET CLR (_Global_)|# Exceções iniciadas / s   |Contadores de desempenho de exceção|
|Memória do .NET CLR (_Global_)    |% Time no GC            |Contadores de desempenho de memória|
|ASP.NET                      |Reiniciar o aplicativo    |Contadores de desempenho do ASP.NET|
|ASP.NET                      |Tempo de execução de solicitação  |Contadores de desempenho do ASP.NET|
|ASP.NET                      |Solicitações desconectadas   |Contadores de desempenho do ASP.NET|
|ASP.NET                      |Processo de trabalho é reiniciado |Contadores de desempenho do ASP.NET|
|Aplicativos do ASP.NET (__Total__)|Total de solicitações        |Contadores de desempenho do ASP.NET|
|Aplicativos do ASP.NET (__Total__)|Solicitações/s          |Contadores de desempenho do ASP.NET|
|V4.0.30319 do ASP.NET           |Tempo de execução de solicitação  |Contadores de desempenho do ASP.NET|
|V4.0.30319 do ASP.NET           |Tempo de espera da solicitação       |Contadores de desempenho do ASP.NET|
|V4.0.30319 do ASP.NET           |Solicitações atual        |Contadores de desempenho do ASP.NET|
|V4.0.30319 do ASP.NET           |Solicitações na fila         |Contadores de desempenho do ASP.NET|
|V4.0.30319 do ASP.NET           |Solicitações rejeitadas       |Contadores de desempenho do ASP.NET|
|Memória                       |MBytes disponíveis        |Contadores de desempenho de memória|
|Memória                       |Bytes confirmados         |Contadores de desempenho de memória|
|( Total)            |% Tempo de processador        |Contadores de desempenho do ASP.NET|
|TCPv4                        |Falhas de Conexão     |Objeto TCP|
|TCPv4                        |Conexões estabelecidas |Objeto TCP|
|TCPv4                        |Conexões redefinidas       |Objeto TCP|
|TCPv4                        |Segmentos enviados/s       |Objeto TCP|
|Interface(*) de rede         |Bytes recebidos/s      |Objeto de Interface de rede|
|Interface(*) de rede         |Bytes enviados/s          |Objeto de Interface de rede|
|Interface de rede ( 2 adaptador de rede de barramento de máquina Virtual do Microsoft)|Bytes recebidos/s|Objeto de Interface de rede|
|Interface de rede ( 2 adaptador de rede de barramento de máquina Virtual do Microsoft)|Bytes enviados/s|Objeto de Interface de rede|
|Interface de rede ( 2 adaptador de rede de barramento de máquina Virtual do Microsoft)|Total de bytes/s|Objeto de Interface de rede|

## <a name="create-and-add-custom-performance-counters-to-your-application"></a>Criar e adicionar contadores de desempenho personalizados para seu aplicativo

Azure tem suporte para criar e modificar contadores de desempenho personalizados para funções da web e funções de trabalho. Os contadores podem ser usados para controlar e monitorar o comportamento específico do aplicativo. Você pode criar e excluir categorias de contador de desempenho personalizados e especificadores de uma tarefa de inicialização, função web ou função de trabalho com permissões de elevado.

>[AZURE.NOTE] Código que faz alterações em contadores personalizados deve ter elevado as permissões para executar. Se o código estiver em uma função da web ou a função de trabalho, a função deve incluir a marca <Runtime executionContext="elevated" /> no arquivo ServiceDefinition.csdef para a função inicializar corretamente.

Você pode enviar dados de contador de desempenho personalizados para armazenamento do Azure usando o agente de diagnóstico.

Os dados do contador de desempenho padrão são gerados pelos processos Azure. Dados do contador de desempenho personalizados devem ser criados pelo seu aplicativo de função de função ou colega da web. Consulte [Tipos de contador de desempenho](https://msdn.microsoft.com/library/z573042h.aspx) para obter informações sobre os tipos de dados que podem ser armazenados em contadores de desempenho personalizado. Consulte [PerformanceCounters amostra](http://code.msdn.microsoft.com/azure/) para obter um exemplo que cria e conjuntos de dados do contador de desempenho personalizadas em uma função da web.

## <a name="store-and-view-performance-counter-data"></a>Armazenar e exibir dados de contador de desempenho

Azure armazena dados de contador de desempenho com outras informações de diagnóstico. Esses dados estão disponíveis para monitoramento remoto enquanto a instância de função está em execução usando o acesso remoto a área de trabalho para exibir ferramentas como o Monitor de desempenho. Para manter os dados fora da instância de função, o agente de diagnóstico deve transferir os dados para armazenamento do Azure. O limite de tamanho do cache contador de dados de desempenho pode ser configurado no agente de diagnóstico ou ele pode ser configurado para ser parte de um limite de compartilhado para todos os dados de diagnósticos. Para obter mais informações sobre como configurar o tamanho de buffer, consulte [OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx) e [DirectoriesBufferConfiguration](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx). Consulte [armazenar e exibir dados de diagnóstico no armazenamento do Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx) para obter uma visão geral de configurar o agente de diagnóstico para transferir dados para uma conta de armazenamento.

Cada instância de contador de desempenho configurado é registrada em uma taxa de amostragem especificado, e os dados de amostrados são transferidos para a conta de armazenamento por uma solicitação de transferência agendada ou uma solicitação de transferência de sob demanda. Transferências automáticas podem ser agendadas sempre que uma vez por minuto. Dados de contador de desempenho transferidos pelo agente de diagnóstico são armazenados em uma tabela, WADPerformanceCountersTable, na conta de armazenamento. Esta tabela pode ser acessada e consultada com métodos de armazenamento do Azure padrão API. Consulte o [Exemplo do Microsoft Azure PerformanceCounters](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9) para obter um exemplo de consulta e exibir dados de contador de desempenho da tabela WADPerformanceCountersTable.

>[AZURE.NOTE] Dependendo da frequência de transferência do agente de diagnóstico e latência de fila, os dados de contador de desempenho mais recentes na conta de armazenamento podem ser vários minutos desatualizada.

## <a name="enable-performance-counters-using-diagnostics-configuration-file"></a>Ativar contadores de desempenho usando arquivo de configuração de diagnóstico

Use o procedimento a seguir para ativar contadores de desempenho em seu aplicativo do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Esta seção pressupõe que você tenha importado o monitor de diagnóstico para seu aplicativo e adicionado o arquivo de configuração de diagnóstico à sua solução do Visual Studio (diagnostics.wadcfg no SDK 2,4 e abaixo ou diagnostics.wadcfgx no SDK 2.5 e acima). Consulte as etapas 1 e 2 no [Diagnóstico habilitando nos serviços de nuvem do Azure e máquinas virtuais](./cloud-services-dotnet-diagnostics.md)) para obter mais informações.

## <a name="step-1-collect-and-store-data-from-performance-counters"></a>Etapa 1: Coletar e armazenar dados de contadores de desempenho

Depois de ter adicionado o arquivo de diagnóstico para sua solução do Visual Studio, você pode configurar o conjunto e o armazenamento de dados de contador de desempenho em um aplicativo do Azure. Isso é feito adicionando contadores de desempenho para o arquivo de diagnóstico. Dados de diagnóstico, incluindo contadores de desempenho, primeiro são coletados na instância. Os dados, em seguida, são mantidos para a tabela de WADPerformanceCountersTable no serviço de tabela do Azure, então você também precisa especificar a conta de armazenamento em seu aplicativo. Se você estiver testando o seu aplicativo localmente no emulador calcular, você também pode armazenar dados de diagnóstico localmente no emulador armazenamento. Antes de armazenar dados de diagnóstico você deve primeiro acesse o [portal clássico Azure](http://manage.windowsazure.com/) e crie uma conta de armazenamento. Uma prática recomendada é localizar sua conta de armazenamento no mesmo geográfica-local como seu aplicativo do Azure para evitar pagando custos de largura de banda externo e para reduzir a latência.

### <a name="add-performance-counters-to-the-diagnostics-file"></a>Adicione contadores de desempenho para o arquivo de diagnóstico

Existem muitos contadores que você pode usar. O exemplo a seguir mostra vários contadores de desempenho que são recomendados para web e o monitoramento de função de operador.

Abra o arquivo de diagnóstico (diagnostics.wadcfg no SDK 2,4 e abaixo ou diagnostics.wadcfgx no SDK 2.5 e acima) e adicione o seguinte ao elemento DiagnosticMonitorConfiguration:

```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
       <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
    </PerformanceCounters>
```

O atributo bufferQuotaInMB, que especifica a quantidade máxima de armazenamento de sistema de arquivos que está disponível para o tipo de conjunto de dados (Azure logs, logs do IIS, etc.). O padrão é 0. Quando a cota é alcançada, os dados mais antigos são excluídos como novos dados são adicionados. A soma de todas as propriedades de bufferQuotaInMB deve ser maior que o valor do atributo OverallQuotaInMB. Para uma discussão mais detalhada de determinar a quantidade de armazenamento será necessária para o conjunto de dados de diagnóstico, consulte a seção configuração WAD de [Solução de problemas de práticas recomendadas para desenvolvimento de aplicativos do Azure](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx).

O atributo scheduledTransferPeriod, que especifica o intervalo entre agendada transferências de dados, arredondado para cima até o minuto mais próximo. Nos exemplos a seguir, ela é definida como PT30M (30 minutos). Definindo o período de transferência para um valor pequeno, como 1 minuto, afetará negativamente o desempenho do seu aplicativo em produção, mas pode ser útil para diagnóstico vendo funcionando rapidamente quando você está testando. O período de transferência agendada deve ser pequeno o suficiente para garantir que os dados de diagnóstico não são substituídos na instância, mas grande o suficiente para que ele não afetará o desempenho do seu aplicativo.

O atributo counterSpecifier Especifica o contador de desempenho para coletar. O atributo sampleRate Especifica a taxa em que o contador de desempenho deve ser de amostra, nesse caso 30 segundos.

Depois de adicionar os contadores de desempenho que você deseja coletar, salve as alterações no arquivo de diagnóstico. Em seguida, você precisa especificar a conta de armazenamento que os dados de diagnóstico serão persistentes para.

### <a name="specify-the-storage-account"></a>Especificar a conta de armazenamento

Para manter suas informações de diagnóstico para sua conta de armazenamento do Azure, você deve especificar uma cadeia de conexão no seu arquivo de configuração (ServiceConfiguration) do serviço.

Azure SDK 2.5 a conta de armazenamento pode ser especificada no arquivo diagnostics.wadcfgx.

>[AZURE.NOTE] Estas instruções se aplicam somente ao Azure SDK 2,4 e abaixo. Azure SDK 2.5 a conta de armazenamento pode ser especificada no arquivo diagnostics.wadcfgx.

Para definir as cadeias de caracteres de conexão:

1. Abra o arquivo de ServiceConfiguration.Cloud.cscfg usando o editor de texto favorito e defina a cadeia de conexão para o seu armazenamento. Os valores *AccountName* e *AccountKey* são encontrados no portal de clássico do Azure no painel de conta de armazenamento, em gerenciamento de chaves.

    ```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. Salve o arquivo de ServiceConfiguration.Cloud.cscfg.

3. Abra o arquivo ServiceConfiguration.Local.cscfg e verifique se seja UseDevelopmentStorage está definida como true.

    ```
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
Agora que as cadeias de caracteres de conexão estão definidas, seu aplicativo irá persistir dados de diagnóstico para sua conta de armazenamento quando seu aplicativo é implantado.
4. Salve e crie seu projeto e implantar seu aplicativo.

## <a name="step-2-optional-create-custom-performance-counters"></a>Etapa 2: Criar (opcional) personalizado contadores

Além os contadores de desempenho predefinidos, você pode adicionar seus próprios contadores de desempenho personalizados para monitorar a funções da web ou de trabalho. Contadores de desempenho personalizados podem ser usados para controlar e monitorar o comportamento específico do aplicativo e podem ser criados ou excluídos em uma tarefa de inicialização, a função web ou a função de trabalho com permissões de elevado.

O agente de diagnóstico do Azure atualiza a contador configuração de desempenho do arquivo .wadcfg após o início de um minuto.  Se você criar contadores de desempenho personalizados no método OnStart e suas tarefas de inicialização levar mais de um minuto para executar, seus contadores de desempenho personalizados terá não foi criados quando o agente de diagnóstico do Azure tenta carregá-los.  Neste cenário, você verá que o diagnóstico do Azure corretamente captura todos os dados de diagnóstico, exceto seus contadores de desempenho personalizados.  Para resolver esse problema, crie os contadores de desempenho em uma tarefa de inicialização ou mover algumas das suas estimativas de inicialização funcionam para o método OnStart depois de criar os contadores de desempenho.

Execute as seguintes etapas para criar um contador chamado "\MyCustomCounterCategory\MyButton1Counter" de desempenho personalizado simple:

1. Abra o arquivo de definição de serviço (CSDEF) para seu aplicativo.
2. Adicione o elemento de tempo de execução para o WebRole ou elemento de WorkerRole para permitir a execução com privilégios elevados:

    ```
    <runtime executioncontext="elevated"/>
    ```
3. Salve o arquivo.
4. Abra o arquivo de diagnóstico (diagnostics.wadcfg no SDK 2,4 e abaixo ou diagnostics.wadcfgx no SDK 2.5 e acima) e adicione o seguinte para o DiagnosticMonitorConfiguration 

    ```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
     <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. Salve o arquivo.
6. Crie a categoria de contador de desempenho personalizados no método OnStart de sua função, antes de invocar base. OnStart. O exemplo c# a seguir cria uma categoria personalizada, se ele ainda não existir:

    ```
    public override bool OnStart()
    {
    if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
    {
       CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

       // add a counter tracking user button1 clicks
       CounterCreationData operationTotal1 = new CounterCreationData();
       operationTotal1.CounterName = "MyButton1Counter";
       operationTotal1.CounterHelp = "My Custom Counter for Button1";
       operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
       counterCollection.Add(operationTotal1);

       PerformanceCounterCategory.Create(
         "MyCustomCounterCategory",
         "My Custom Counter Category",
         PerformanceCounterCategoryType.SingleInstance, counterCollection);

       Trace.WriteLine("Custom counter category created.");
    }
    else{
       Trace.WriteLine("Custom counter category already exists.");
    }

    return base.OnStart();
    }
    ```
7. Atualize os contadores dentro de seu aplicativo. O exemplo a seguir atualiza um contador de desempenho personalizados Button1_Click eventos:

    ```
    protected void Button1_Click(object sender, EventArgs e)
        {
         PerformanceCounter button1Counter = new PerformanceCounter(
           "MyCustomCounterCategory",
           "MyButton1Counter",
           string.Empty,
           false);
         button1Counter.Increment();
        this.Button1.Text = "Button 1 count: " +
           button1Counter.RawValue.ToString();
        }
    ```
8. Salve o arquivo.  

Dados do contador de desempenho personalizados agora serão coletados pelo monitor de diagnóstico do Azure.

## <a name="step-3-query-performance-counter-data"></a>Etapa 3: Consultar dados de contador de desempenho

Depois que o aplicativo é implantado e executar o monitor de diagnóstico começará a coletar contadores de desempenho e manter esses dados para o armazenamento do Azure. Use ferramentas como Server Explorer no Visual Studio, [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/)ou no [Gerenciador de diagnóstico do Azure](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) por Cerebrata para exibir os dados de contadores de desempenho na tabela WADPerformanceCountersTable. Você pode também programaticamente consultar o serviço de tabela usando [c#](../storage/storage-dotnet-how-to-use-tables.d), [Java](../storage/storage-java-how-to-use-table-storage.md), [Node](../storage/storage-nodejs-how-to-use-table-storage.md), [Python](../storage/storage-python-how-to-use-table-storage.md), [Ruby](../storage/storage-ruby-how-to-use-table-storage.md)ou [PHP](../storage/storage-php-how-to-use-table-storage.md).

O exemplo c# a seguir mostra uma consulta simples a tabela de WADPerformanceCountersTable e salva os dados de diagnóstico para um arquivo CSV. Depois que os contadores de desempenho são salvos para um arquivo CSV, você pode usar os recursos de gráficas no Microsoft Excel ou alguma outra ferramenta para visualizar os dados. Certifique-se de adicionar uma referência a Microsoft.WindowsAzure.Storage.dll, que está incluído no SDK do Azure para .NET outubro de 2012 e posterior. O conjunto está instalado no diretório de programa Files%\Microsoft SDKs\Microsoft Azure.NET SDK\version-num\ref\ %.

```
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ...

    // Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
    // you store your connection string using the Microsoft Azure service configuration
    // system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
    // to retrieve your storage connection string.  If you're not using Cloud Services, it's
    // recommended that you store the connection string in your web.config or app.config file.
    // Use the ConfigurationManager type to retrieve your storage connection string.

    string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

    // Get a reference to the storage account using the connection string.  You can also use the development
    // storage account (Storage Emulator) for local debugging.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    //CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
    CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

    // Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
    TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
       .Where(
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
          TableOperators.And,
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
          TableOperators.And,
          TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
          )
       )
    );

    // Execute the table query.
    IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

    // Process the query results and build a CSV file.
    StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

    foreach (PerformanceCountersEntity entity in result)
    {
       sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
          + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
    }

    StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
    sw.Write(sb.ToString());
    sw.Close();
```

Mapa de entidades para c# objetos usando uma classe personalizada derivada de **TableEntity**. O código a seguir define uma classe de entidade que representa um contador de desempenho na tabela **WADPerformanceCountersTable** .


    public class PerformanceCountersEntity : TableEntity
    {
       public long EventTickCount { get; set; }
       public string DeploymentId { get; set; }
       public string Role { get; set; }
       public string RoleInstance { get; set; }
       public string CounterName { get; set; }
       public double CounterValue { get; set; }
    }


## <a name="next-steps"></a>Próximas etapas
[Artigos adicionais para o modo de exibição no diagnóstico do Azure] (.. / azure-diagnostics.md)
