<properties
    pageTitle="Dependências no Azure lote de tarefas | Microsoft Azure"
    description="Criar tarefas que dependem da conclusão bem-sucedida de outras tarefas de processamento MapReduce estilo e dados de grandes semelhantes cargas de trabalho em lote do Azure."
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
    ms.date="09/28/2016"
    ms.author="marsma" />

# <a name="task-dependencies-in-azure-batch"></a>Dependências de tarefas no lote do Azure

O recurso de dependências de tarefas do Azure lote é uma boa opção se você quiser processar:

- Cargas de trabalho de estilo MapReduce na nuvem.
- Trabalhos cujas tarefas de processamento de dados podem ser expresso como um gráfico direcionado acíclica (Dag mão).
- Qualquer outro trabalho nas quais tarefas downstream dependem da saída de tarefas upstream.

Dependências de tarefas em lotes permitem que você crie tarefas agendadas para execução em nós de computação somente após a conclusão bem-sucedida de uma ou mais tarefas. Por exemplo, você pode criar um trabalho que renderiza cada quadro de um filme 3D com tarefas paralelas separadas. O final de tarefas – a "tarefa de mesclagem –" mescla as quadros renderizados no filme completo somente depois que todos os quadros tiveram sido processados com êxito.

Você pode criar tarefas que dependem de outras tarefas em um relacionamento de um para um ou um-para-muitos. Você pode até criar uma dependência de intervalo em que uma tarefa depende da conclusão bem-sucedida de um grupo de tarefas em um intervalo específico de IDs de tarefa. Você pode combinar essas três cenários básicos para criar relações de muitos-para-muitos.

## <a name="task-dependencies-with-batch-net"></a>Dependências de tarefas com o lote .NET

Neste artigo, vamos abordar como configurar dependências entre tarefas usando o [Lote .NET] [ net_msdn] biblioteca. Primeiro mostraremos como [Habilitar dependência entre tarefas](#enable-task-dependencies) nos seus trabalhos e, em seguida, demonstre como [Configurar uma tarefa com dependências](#create-dependent-tasks). Finalmente, vamos abordar os [cenários de dependência](#dependency-scenarios) lote compatível com.

## <a name="enable-task-dependencies"></a>Habilitar dependências entre tarefas

Para usar dependências entre tarefas em seu aplicativo de lote, você deve primeiro saber o serviço de lote que o trabalho usa dependências entre tarefas. No lote .NET, ativá-lo em seu [CloudJob] [ net_cloudjob] definindo seus [UsesTaskDependencies] [ net_usestaskdependencies] propriedade `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

No trecho de código anterior, "batchClient" é uma instância do [BatchClient] [ net_batchclient] classe.

## <a name="create-dependent-tasks"></a>Criar tarefas dependentes

Para criar uma tarefa dependente da conclusão bem-sucedida de uma ou mais tarefas, você informa ao lote que a tarefa "depende" outras tarefas. No lote .NET, configure o [CloudTask][net_cloudtask]. [DependsOn] [net_dependson] propriedade com uma instância do [TaskDependencies] [ net_taskdependencies] classe:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Este trecho de código cria uma tarefa com a ID de "Flores" que será agendado para ser executado em um nó de computação somente depois que as tarefas com IDs de "Chuva" e "DOM" foram concluídas com êxito.

 > [AZURE.NOTE] Uma tarefa é considerada concluído quando ela está no estado **concluído** e seu **código de saída** é `0`. No lote .NET, isso significa que um [CloudTask][net_cloudtask]. [Estado] [net_taskstate] valor da propriedade `Completed` e da CloudTask [TaskExecutionInformation][net_taskexecutioninformation]. [Código_de_saída] [net_exitcode] valor da propriedade é `0`.

## <a name="dependency-scenarios"></a>Cenários de dependência

Há três cenários de dependência de tarefas básicas que você pode usar no lote Azure: um para um, um-para-muitos e ID da tarefa de intervalo dependência. Elas podem ser combinadas para fornecer um cenário de quarto, muitos-para-muitos.

 Cenário&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Exemplo | |
 :-------------------: | ------------------- | -------------------
 [Um para um](#one-to-one) | *taskB* depende *TarefaUma* <p/> *taskB* não ser agendado para execução até *TarefaUma* foi concluída com êxito | ![Diagrama: dependências de tarefas individual][1]
 [Um-para-muitos](#one-to-many) | *taskC* depende *TarefaUma* e *taskB* <p/> *taskC* não ser agendado para execução até *TarefaUma* e *taskB* tem concluído com êxito | ![Diagrama: dependências de tarefas de um-para-muitos][2]
 [Intervalo de ID da tarefa](#task-id-range) | *taskD* depende de uma variedade de tarefas <p/> *taskD* não ser agendado para execução até que as tarefas com IDs de *1* a *10* tem concluído com êxito | ![Diagrama: Dependência de intervalo de id de tarefa][3]

>[AZURE.TIP] Você pode criar relações de **muitos-para-muitos** , como onde tarefas C, D, E e F cada dependem de tarefas A e B. Isso é útil, por exemplo, em cenários de pré-processamento paralelizados onde suas tarefas downstream dependem da saída de várias tarefas upstream.

### <a name="one-to-one"></a>Um para um

Para criar uma tarefa que tem uma dependência em da conclusão bem-sucedida de uma outra tarefa, você fornecer um ID de única tarefa para o [TaskDependencies][net_taskdependencies]. [OnId] [net_onid] método estático ao preencher o [DependsOn] [ net_dependson] propriedade de [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Um-para-muitos

Para criar uma tarefa que tem uma dependência em da conclusão bem-sucedida de várias tarefas, que você fornece um conjunto de tarefas IDs a [TaskDependencies][net_taskdependencies]. [OnIds] [net_onids] método estático ao preencher o [DependsOn] [ net_dependson] propriedade de [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

### <a name="task-id-range"></a>Intervalo de ID da tarefa

Para criar uma tarefa que tem uma dependência em da conclusão bem-sucedida de um grupo de tarefas cuja lie IDs dentro de um intervalo, você fornecerá o primeiro e último IDs de tarefas no intervalo à [TaskDependencies][net_taskdependencies]. [OnIdRange] [net_onidrange] método estático ao preencher o [DependsOn] [ net_dependson] propriedade de [CloudTask][net_cloudtask].

>[AZURE.IMPORTANT] Quando você usa intervalos de ID de tarefa para as suas dependências, a tarefa IDs no intervalo *deve* ser representações de cadeia de caracteres de valores inteiros. Além disso, cada tarefa no intervalo deve concluída com êxito para a tarefa dependente ser agendado para execução.

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="code-sample"></a>Amostra de código

O [TaskDependencies] [ github_taskdependencies] projeto de amostra é um dos [exemplos de código do Azure lote] [ github_samples] no GitHub. Essa solução Visual Studio 2015 demonstra como habilitar dependências de tarefas em um trabalho, criar tarefas que dependem de outras tarefas e executar essas tarefas em um pool de nós de computação.

## <a name="next-steps"></a>Próximas etapas

### <a name="application-deployment"></a>Implantação do aplicativo

O recurso de [pacotes de aplicativos](batch-application-packages.md) do lote fornece uma maneira fácil para ambos implantar e versão os aplicativos que suas tarefas executar em nós de computadores.

### <a name="installing-applications-and-staging-data"></a>Instalar aplicativos e dados de teste

Confira os [aplicativos de instalação e dados de preparação no lote Calcular nós] [ forum_post] postar no Fórum do Azure lote para uma visão geral dos vários métodos para preparar sua nós para executar tarefas. Esta postagem escrito por um dos membros da equipe lote do Azure, é uma boa introdução sobre as diferentes maneiras de obter os arquivos (incluindo os aplicativos e dados de entrada de tarefa) em seus nós de computação.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagrama: dependência de um para um"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagrama: dependência de um-para-muitos"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagrama: dependência de intervalo de id de tarefa"
