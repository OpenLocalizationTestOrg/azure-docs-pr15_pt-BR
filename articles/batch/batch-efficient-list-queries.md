<properties
    pageTitle="Consultas de lista eficiente no lote Azure | Microsoft Azure"
    description="Aumentar o desempenho filtrando suas consultas quando solicitando informações sobre recursos de lote como pools, trabalhos, tarefas e nós de computadores."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/25/2016"
    ms.author="marsma" />

# <a name="query-the-azure-batch-service-efficiently"></a>Consultar o serviço do Azure lote com eficiência

Aqui você aprenderá a aumentar o desempenho do seu aplicativo do Azure lote, reduzindo a quantidade de dados que são retornados pelo serviço consulta trabalhos, tarefas, e calcular nós com o [Lote .NET] [ api_net] biblioteca.

Praticamente todos os aplicativos de lote precisará executar algum tipo de operação de monitoramento ou outro que consulta o serviço de lote, muitas vezes em intervalos regulares. Por exemplo, para determinar se há quaisquer tarefas enfileiradas restante em um trabalho, você deve obter dados em cada tarefa no trabalho. Para determinar o status de nós em seu pool, você deve obter dados em cada nó no pool. Este artigo explica como executar essas consultas a maneira mais eficiente.

## <a name="meet-the-detaillevel"></a>Atender a DetailLevel

Em um aplicativo do lote de produção, entidades como trabalhos, tarefas e nós de computação podem numerar milhares. Quando você solicita informações sobre esses recursos, uma potencialmente grande quantidade de dados deve "cross conexão" do serviço em lotes ao seu aplicativo em cada consulta. Limitando o número de itens e o tipo de informação que será retornado por uma consulta, você pode aumentar a velocidade de suas consultas e, portanto, o desempenho do seu aplicativo.

Este [Lote .NET] [ api_net] trecho de código da API lista *cada* tarefa que está associada uma tarefa, juntamente com *todas* as propriedades de cada tarefa:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Você pode executar uma consulta de lista muito mais eficiente, no entanto, aplicando um "nível de detalhes" à sua consulta. Você pode fazer isso, fornecendo um [ODATADetailLevel] [ odata] objeto para o [JobOperations.ListTasks] [ net_list_tasks] método. Este trecho de código retorna apenas o ID, linha de comando e propriedades de informações de nó de computação de tarefas concluídas:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Neste cenário de exemplo, se houver milhares de tarefas no trabalho, os resultados da segunda consulta serão retornados normalmente muito mais rápido do que o primeiro. Obter mais informações sobre como usar ODATADetailLevel quando você lista itens com a API do .NET lote estão incluída [abaixo](#efficient-querying-in-batch-net).

> [AZURE.IMPORTANT]
> É altamente recomendável que você *sempre* fornecer um objeto de ODATADetailLevel a suas chamadas de lista de API .NET para garantir a máxima eficiência e desempenho de seu aplicativo. Especificando um nível de detalhe, você pode ajudar a reduzir os tempos de resposta do serviço de lote, melhorar a utilização de rede e minimizar o uso da memória por aplicativos clientes.

## <a name="filter-select-and-expand"></a>Filtrar, selecione e expanda

O [Lote .NET] [ api_net] e [Lote restante] [ api_rest] APIs fornecem a capacidade de reduzir o número de itens que são retornados em uma lista, bem como a quantidade de informações que são retornadas para cada. Você pode fazer isso, especificando o **filtro**, **Selecione**e **expanda cadeias de caracteres** ao executar consultas de lista.

### <a name="filter"></a>Filtro
A cadeia de caracteres de filtro é uma expressão que reduz o número de itens que são retornados. Por exemplo, somente as tarefas em execução para um trabalho de lista ou somente nós de computação que estão prontos para executar tarefas de lista.

- A cadeia de caracteres de filtro consiste em uma ou mais expressões, com uma expressão que consiste em um nome de propriedade, o operador e o valor. As propriedades que podem ser especificadas são específicas para cada tipo de entidade que você consulta, como são os operadores que têm suporte para cada propriedade.
- Várias expressões podem ser combinadas usando os operadores lógicos `and` e `or`.
- Este exemplo filtrar listas de cadeia de caracteres somente a execução "renderizar" tarefas: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Selecione
A cadeia de caracteres select limita os valores de propriedade retornadas para cada item. Você especificar uma lista de nomes de propriedade, e somente os valores de propriedade são retornados para os itens nos resultados da consulta.

- A cadeia de caracteres select consiste em uma lista separada por vírgulas de nomes de propriedade. Você pode especificar qualquer uma das propriedades para o tipo de entidade que você está consultando.
- Essa cadeia de caracteres de seleção de exemplo especifica que apenas três valores de propriedade devem ser retornados para cada tarefa: `id, state, stateTransitionTime`.

### <a name="expand"></a>Expandir
A cadeia de caracteres de expansão reduz o número de chamadas de API que são necessárias para obter determinadas informações. Quando você usa uma cadeia de caracteres de expansão, mais informações sobre cada item podem ser obtidas com uma única chamada de API. Em vez de primeiro obter a lista de entidades, em seguida, solicitando informações para cada item na lista, você pode usar uma cadeia de caracteres de expansão para obter as mesmas informações em uma única chamada de API. Menos chamadas de API significa melhor desempenho.

- Semelhante à cadeia de caracteres select, os controles de cadeia de caracteres de expansão se determinados dados estão incluídos nos resultados da consulta de lista.
- A cadeia de caracteres de expansão é suportada apenas quando ele é usado na listagem trabalhos, agendas de trabalho, tarefas e pools. Atualmente, ele suporta apenas informações estatísticas.
- Quando todas as propriedades são necessárias e nenhuma sequência select for especificada, a cadeia de caracteres de expansão *deve* ser usado para obter informações de estatísticas. Se uma cadeia de caracteres select é usada para obter um subconjunto de propriedades, em seguida, `stats` pode ser especificado na cadeia de seleção e a cadeia de caracteres de expansão não precisa ser especificado.
- Este exemplo expandir cadeia Especifica que informações estatísticas devem ser retornadas para cada item na lista: `stats`.

> [AZURE.NOTE] Ao construir qualquer um dos tipos de cadeia de caracteres de três consulta (filtrar, selecione e expanda), você deve garantir que os nomes de propriedade e caso coincidir com seus correspondentes de elemento API REST. Por exemplo, ao trabalhar com a classe .NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) , especifique o **estado** em vez de **estado**, embora a propriedade .NET é [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Consulte as tabelas abaixo para mapeamentos de propriedade entre o .NET e as APIs REST.

### <a name="rules-for-filter-select-and-expand-strings"></a>Regras de filtro, selecione e expanda cadeias de caracteres

- Nomes de propriedades no filtro, selecione e expanda cadeias deverá aparecer como funcionam no [Lote restante] [ api_rest] API – mesmo quando você usa o [Lote .NET] [ api_net] ou uma das SDK do lote.
- Todos os nomes de propriedade diferenciam maiusculas de minúsculas, mas valores de propriedade diferenciam maiusculas de minúsculas.
- Data/hora cadeias de caracteres pode ser um dos dois formatos e deve ser precedidas com `DateTime`.

  - Exemplo de formato W3C-DTF:`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - Exemplo de formato RFC 1123:`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- Cadeias de caracteres Boolianas estão `true` ou `false`.
- Se uma propriedade inválida ou operador for especificada, um `400 (Bad Request)` resultará em erro.

## <a name="efficient-querying-in-batch-net"></a>Eficiente consultando no lote .NET

Dentro do [Lote .NET] [ api_net] API, o [ODATADetailLevel] [ odata] classe é usada para fornecimento de filtro, selecione e expanda cadeias de caracteres para operações de lista. A classe ODataDetailLevel tem três propriedades de cadeia de caracteres pública que podem ser especificadas no construtor ou definir diretamente no objeto. Você então passar o objeto ODataDetailLevel como um parâmetro para as diversas operações de lista como [ListPools][net_list_pools], [ListJobs][net_list_jobs]e [ListTasks][net_list_tasks].

- [ODATADetailLevel][odata]. [FilterClause] [ odata_filter]: Limitar o número de itens que são retornados.
- [ODATADetailLevel][odata]. [SelectClause] [ odata_select]: Especificar quais valores de propriedade são retornados com cada item.
- [ODATADetailLevel][odata]. [ExpandClause] [ odata_expand]: Recuperar dados para todos os itens em uma única API chamada em vez de chamadas separadas para cada item.

O trecho de código a seguir usa a API de .NET lote com eficiência consultar o serviço de lote para as estatísticas de um conjunto específico de pools. Neste cenário, o usuário de lote tem pools de produção e de teste. O pool de teste IDs são precedidos com "teste" e o grupo de produção IDs são precedidos com "produto". No trecho, *myBatchClient* é uma instância corretamente inicializada da classe [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) .

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [AZURE.TIP] Uma instância de [ODATADetailLevel] [ odata] que está configurado com selecionar e expandir cláusulas também podem ser passadas para apropriado métodos Get, como [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), para limitar a quantidade de dados que serão retornados.

## <a name="batch-rest-to-net-api-mappings"></a>Lote restante para mapeamentos de API .NET

Nomes de propriedade no filtro, selecione e expanda cadeias de caracteres *deve* refletir seus equivalentes de API REST, tanto no nome e o caso. As tabelas a seguir fornecem mapeamentos entre os equivalentes de .NET e API REST.

### <a name="mappings-for-filter-strings"></a>Mapeamentos de cadeias de caracteres de filtro

- **Métodos de lista do .NET**: cada um dos métodos API .NET nesta coluna aceita uma [ODATADetailLevel] [ odata] objeto como um parâmetro.
- **Solicitações de lista REST**: página de cada API REST vinculada nesta coluna contém uma tabela que especifica as propriedades e operações permitidos em cadeias de caracteres de *filtro* . Você usará esses nomes de propriedade e operações quando você construir uma [ODATADetailLevel.FilterClause] [ odata_filter] cadeia de caracteres.

| Métodos de lista do .NET | Solicitações de lista REST |
|---|---|
| [CertificateOperations.ListCertificates][net_list_certs] | [Listar os certificados em uma conta][rest_list_certs]
| [CloudTask.ListNodeFiles][net_list_task_files] | [Listar os arquivos associados a uma tarefa][rest_list_task_files]
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] | [O status da preparação do trabalho e tarefas de lançamento do trabalho para um trabalho de lista][rest_list_jobprep_status]
| [JobOperations.ListJobs][net_list_jobs] | [Listar os trabalhos em uma conta][rest_list_jobs]
| [JobOperations.ListNodeFiles][net_list_nodefiles] | [Listar os arquivos em um nó][rest_list_nodefiles]
| [JobOperations.ListTasks][net_list_tasks] | [Lista as tarefas associadas a um trabalho][rest_list_tasks]
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] | [Listar as cronogramas de trabalho em uma conta][rest_list_job_schedules]
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] | [Listar os trabalhos associados a um plano de trabalho][rest_list_schedule_jobs]
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] | [Listar os nós de computação em um pool][rest_list_compute_nodes]
| [PoolOperations.ListPools][net_list_pools] | [Listar os pools em uma conta][rest_list_pools]

### <a name="mappings-for-select-strings"></a>Mapeamentos para selecionados cadeias de caracteres

- **Tipos de lote .NET**: tipos de API do lote .NET.
- **API REST entidades**: cada página nesta coluna contém uma ou mais tabelas que os nomes de propriedade da API REST para o tipo de lista. Esses nomes de propriedade são usadas quando você construir *Selecione* cadeias de caracteres. Você usará esses mesmos nomes de propriedade quando você construir uma [ODATADetailLevel.SelectClause] [ odata_select] cadeia de caracteres.

| Tipos de .NET em lotes | Entidades API REST |
|---|---|
| [Certificado][net_cert] | [Obter informações sobre um certificado][rest_get_cert] |
| [CloudJob][net_job] | [Obter informações sobre um trabalho][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [Obter informações sobre um cronograma de trabalho][rest_get_schedule] |
| [ComputeNode][net_node] | [Obter informações sobre um nó][rest_get_node] |
| [CloudPool][net_pool] | [Obter informações sobre um pool][rest_get_pool] |
| [CloudTask][net_task] | [Obter informações sobre uma tarefa][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Exemplo: construir uma cadeia de caracteres de filtro

Quando você construir uma cadeia de caracteres de filtro para [ODATADetailLevel.FilterClause][odata_filter], consulte a tabela acima em "Mapeamentos cadeias de filtro de" a página de documentação de localizar a API REST que corresponde à operação de lista que você deseja realizar. Você encontrará as propriedades filtráveis e os operadores suportados na primeira tabela multirow nessa página. Se você deseja recuperar todas as tarefas cujo código de saída era diferente de zero, por exemplo, esta linha na [lista de tarefas associadas a um trabalho] [ rest_list_tasks] Especifica a cadeia de caracteres de propriedade aplicável e operadores permitidos:

| Propriedade | Operações permitidas | Tipo |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

Portanto, a cadeia de caracteres de filtro para listar todas as tarefas com um código de saída zero seria:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Exemplo: construir uma cadeia de caracteres select

Construir [ODATADetailLevel.SelectClause][odata_select], consulte a tabela acima em "Mapeamentos para selecionados cadeias de caracteres" e navegue até a página de API REST que corresponde ao tipo de entidade que estiver listando. Você encontrará as propriedades selecionáveis e os operadores suportados na primeira tabela multirow nessa página. Se você deseja recuperar somente a identificação e a linha de comando para cada tarefa em uma lista, por exemplo, você encontrará essas linhas na tabela aplicável [obter informações sobre uma tarefa][rest_get_task]:

| Propriedade | Tipo | Anotações |
| :--- | :--- | :--- |
| `id` | `String` | `The ID of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

A cadeia de caracteres de seleção para incluir apenas a identificação e a linha de comando com cada tarefa listada seria:

`id, commandLine`

## <a name="code-samples"></a>Exemplos de código

### <a name="efficient-list-queries-code-sample"></a>Amostra de código de consultas com uso eficiente de lista

Confira o [EfficientListQueries] [ efficient_query_sample] projeto de amostra no GitHub para ver a consulta de lista como eficaz pode afetar o desempenho em um aplicativo. Este aplicativo de console c# cria e adiciona um grande número de tarefas em um trabalho. Em seguida, ela faz várias chamadas para o [JobOperations.ListTasks] [ net_list_tasks] método e passa [ODATADetailLevel] [ odata] objetos que estão configurados com valores de propriedade diferentes para variar a quantidade de dados a ser retornado. Ele produz um resultado similar ao seguinte:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Conforme mostrado nos tempos decorridos, você pode reduzir significativamente os tempos de resposta de consulta limitando as propriedades e o número de itens que são retornados. Você pode encontrar esse e outros projetos de amostra as [amostras de lote azure] [ github_samples] repositório no GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Amostra de biblioteca e o código de BatchMetrics

Além da amostra de código EfficientListQueries acima, você pode encontrar o [BatchMetrics] [ batch_metrics] projeto nas [amostras de lote azure] [ github_samples] GitHub repositório. O projeto de amostra BatchMetrics demonstra como monitorar o andamento do trabalho de lote do Azure usando a API do lote com eficiência.

O [BatchMetrics] [ batch_metrics] exemplo inclui um projeto de biblioteca de classes .NET que você pode incorporar em seus próprios projetos e um programa de linha de comando simple para exercício e demonstram o uso da biblioteca.

O aplicativo de exemplo no projeto demonstra as seguintes operações:

1. Selecionando atributos específicos para baixar somente as propriedades que necessárias
2. Filtrando tempos de transição de estado para baixar somente alterações desde a última consulta

Por exemplo, o seguinte método aparece na biblioteca de BatchMetrics. Ela retorna um ODATADetailLevel que especifica que somente os `id` e `state` propriedades devem ser obtidas para as entidades que são consultadas. Ela também especifica que somente entidades cujo estado foi alterado desde especificado `DateTime` parâmetro deve ser retornado.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Próximas etapas

### <a name="parallel-node-tasks"></a>Tarefas de nó paralelas

[Maximizar Azure lote Calcular uso de recursos com tarefas de nó simultâneas](batch-parallel-node-tasks.md) é outro artigo relacionado ao lote aplicativo desempenho. Alguns tipos de cargas de trabalho podem se beneficiar de executar tarefas em paralelo no maiores – mas menos – nós de computadores. Confira o [cenário de exemplo](batch-parallel-node-tasks.md#example-scenario) no artigo para obter detalhes sobre esse cenário.

### <a name="batch-forum"></a>Fórum do lote

[Fórum do Azure lote] [ forum] no MSDN é um ótimo lugar para discutir lote e faça perguntas sobre o serviço. Vá diretamente para postagens "adesivas" úteis e envie suas perguntas conforme eles surgem enquanto você cria soluções lote.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
