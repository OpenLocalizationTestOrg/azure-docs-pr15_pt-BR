<properties
    pageTitle="Maximizar o uso de nó lote com tarefas paralelas | Microsoft Azure"
    description="Aumentar a eficiência e custos mais baixos, usando menos nós de computação e tarefas em execução simultâneas em cada nó em um pool de lote do Azure"
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

# <a name="maximize-azure-batch-compute-resource-usage-with-concurrent-node-tasks"></a>Maximizar o uso de recursos de computação Azure lote com tarefas de nó simultâneas

Executando mais de uma tarefa simultaneamente em cada nó de computação no pool de lote do Azure, você pode maximizar o uso de recursos em um número menor de nós no pool. Para algumas cargas de trabalho, isso pode resultar em períodos de trabalho menores e menor custo.

Enquanto alguns cenários beneficiam dedicar todos os recursos de um nó a uma única tarefa, várias situações beneficiam permitindo várias tarefas compartilhar esses recursos:

 - **Minimizar a transferência de dados** quando as tarefas são capazes de compartilhar dados. Neste cenário, você pode reduzir consideravelmente copiando dados compartilhada para um número menor de nós e executar tarefas em paralelo em cada nó encargos de transferência de dados. Isso se aplica especialmente se os dados sejam copiados para cada nó devem ser transferidos entre regiões geográficas.

 - **Uso de memória maximização** quando tarefas exigem uma grande quantidade de memória, mas somente durante curtos períodos de tempo e horários variáveis durante a execução. Você pode utilizar nós de computação menos, mas maiores, com mais de memória eficiente lidar com tais picos. Esses nós teria várias tarefas em execução em paralelo em cada nó, mas cada tarefa seria aproveitar memória bastante de nós em momentos diferentes.

 - **Atenuar limites de número de nó** quando a comunicação entre os nós é necessária dentro de um pool. Atualmente, os pools configurados para comunicação entre nós são limitados ao 50 nós de computação. Se cada nó em tal um pool é capaz de executar tarefas em paralelo, um número maior de tarefas pode ser executado simultaneamente.

 - **Duplicar um local cluster de computação**, como quando você primeiro move um ambiente de computação para Azure. Se sua solução local atual executa várias tarefas por nó de computação, você pode aumentar o número máximo de tarefas de nó espelhar mais de perto dessa configuração.

## <a name="example-scenario"></a>Cenário de exemplo

Como um exemplo para ilustrar os benefícios de execução de tarefa paralela, digamos que seu aplicativo de tarefa tem requisitos de CPU e memória, como [padrão\_D1](../cloud-services/cloud-services-sizes-specs.md#general-purpose-d) nós são suficientes. Mas, para concluir o trabalho no tempo necessário, 1.000 de nós são necessárias.

Em vez de usar padrão\_nós D1 que têm 1 núcleo de CPU, você poderia usar [padrão\_D14](../cloud-services/cloud-services-sizes-specs.md#memory-intensive-d) nós que têm 16 cores e habilitar a execução de tarefa paralela. Portanto, *16 vezes menos nós* poderia ser usado, em vez de 1.000 nós, 63 somente seria necessário. Além disso, se forem necessários para cada nó arquivos grandes de aplicativo ou dados de referência, a eficiência e a duração da tarefa novamente aperfeiçoadas desde que os dados são copiados para somente 16 nós.

## <a name="enable-parallel-task-execution"></a>Habilitar a execução de tarefa paralela

Você pode configurar nós de computação para execução de tarefa paralela no nível do pool. Com a biblioteca de lote .NET, defina o [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] propriedade quando você cria um pool. Se você estiver usando a API REST lote, definir a [maxTasksPerNode] [ rest_addpool] elemento no corpo da solicitação durante a criação de pool.

Azure lote permite que você defina máximos tarefas por nó até quatro vezes (x 4) o número de cores de nó. Por exemplo, se o pool estiver configurado conosco de dimensionar "Grande" (quatro cores), em seguida, `maxTasksPerNode` pode estar definida como 16. Para obter detalhes sobre o número de cores para cada um dos tamanhos de nó, consulte [tamanhos para serviços de nuvem](../cloud-services/cloud-services-sizes-specs.md). Para obter mais informações sobre os limites de serviço, consulte [cotas e limites para o serviço de lote do Azure](batch-quota-limit.md).

> [AZURE.TIP] Certifique-se de levar em consideração o `maxTasksPerNode` valor quando você construir uma [fórmula de escala automática] [ enable_autoscaling] para o pool. Por exemplo, uma fórmula que avalia `$RunningTasks` podem ser afetados drasticamente por um aumento de tarefas por nó. Para obter mais informações, consulte [automaticamente escala calcular nós em um pool de lote do Azure](batch-automatic-scaling.md) .

## <a name="distribution-of-tasks"></a>Distribuição de tarefas

Quando os nós de computação em um pool podem executar tarefas simultaneamente, é importante especificar como você deseja que as tarefas sejam distribuídos entre os nós no pool.

Usando o [CloudPool.TaskSchedulingPolicy] [ task_schedule] propriedade, você pode especificar que as tarefas devem ser atribuídas uniformemente em todos os nós no pool ("disseminação"). Ou você pode especificar que quantas tarefas possível devem ser atribuídas a cada nó antes de tarefas atribuídas a outro nó no pool ("remessa").

Como um exemplo de como esse recurso é útil, considere o pool de [padrão\_D14](../cloud-services/cloud-services-sizes-specs.md#memory-intensive-d) nós (no exemplo acima) que está configurado com uma [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] valor de 16. Se o [CloudPool.TaskSchedulingPolicy] [ task_schedule] está configurado com uma [ComputeNodeFillType] [ fill_type] do *pacote*, ele seria maximizar o uso de todas as cores de 16 de cada nó e permitir que um [pool de autoscaling](batch-automatic-scaling.md) aparar nós não utilizados do pool (nós sem qualquer tarefas atribuídas). Isso minimiza o uso do recurso e economiza dinheiro.

## <a name="batch-net-example"></a>Exemplo de .NET em lotes

Este [Lote .NET] [ api_net] trecho de código da API mostra uma solicitação para criar um pool que contém quatro nós grande com um máximo de quatro tarefas por nó. Especifica uma política que irá preencher cada nó com tarefas antes atribuindo tarefas para outro nó no pool de agendamento de tarefas. Para obter mais informações sobre como adicionar pools usando a API do .NET lote, consulte [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicated: 4
        virtualMachineSize: "large",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Exemplo de restante em lotes

Este [Lote restante] [ api_rest] API trecho mostra uma solicitação para criar um pool que contém dois nós grandes com um máximo de quatro tarefas por nó. Para obter mais informações sobre como adicionar pools usando a API REST, consulte [Adicionar um pool a uma conta][rest_addpool].

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicated":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [AZURE.NOTE] Você pode definir o `maxTasksPerNode` elemento e [MaxTasksPerComputeNode] [ maxtasks_net] propriedade somente em tempo de criação de pool. Eles não podem ser modificados após um pool já foi criado.

## <a name="code-sample"></a>Amostra de código

O [ParallelNodeTasks] [ parallel_tasks_sample] projeto no GitHub ilustra o uso da [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] propriedade.

Este aplicativo de console c# usa o [Lote .NET] [ api_net] biblioteca para criar um pool com um ou mais nós de computação. Ele executa um número configurável das tarefas em nós para simular carga variável. Saída do aplicativo especifica quais nós executado cada tarefa. O aplicativo também fornece um resumo dos parâmetros de trabalho e duração. A parte de resumo da saída do dois execuções diferentes do aplicativo de exemplo é exibido abaixo.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A primeira execução do aplicativo de exemplo mostra que com um único nó no pool e a configuração padrão de uma tarefa por nó, a duração da tarefa é mais de 30 minutos.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

A segunda execução do exemplo mostra uma diminuição significativa no duração da tarefa. Isso ocorre porque o pool foi configurado com quatro tarefas por nó, que permite a execução de tarefa paralela concluir o trabalho de quase um trimestre do tempo.

> [AZURE.NOTE] As durações de trabalho nos resumos acima não inclui a hora de criação de pool. Cada um dos trabalhos acima foi enviada para pools criados anteriormente cujos nós de computação estavam no estado *ocioso* na hora de envio.

## <a name="next-steps"></a>Próximas etapas

### <a name="batch-explorer-heat-map"></a>Mapa de calor Explorer em lotes

O [Azure lote Explorer][batch_explorer], um dos [aplicativos de exemplo]do Azure lote[github_samples], contém um recurso de *Mapa de calor* que fornece visualização da execução de tarefa. Quando você estiver executando o [ParallelTasks] [ parallel_tasks_sample] aplicativo de exemplo, você pode usar o recurso de mapa de calor para visualizar facilmente a execução de tarefas em paralelo em cada nó.

![Mapa de calor Explorer em lotes][1]

*Lote Explorer mapa de calor um pool de quatro nós, com cada nó quatro tarefas em execução no momento*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png
