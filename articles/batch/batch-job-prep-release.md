<properties
    pageTitle="Preparação e limpeza no lote de trabalho | Microsoft Azure"
    description="Usar tarefas de preparação de nível de trabalho para minimizar a transferência de dados para nós de computação do Azure lote e, em seguida, solte a tarefas de limpeza de nó na conclusão do trabalho."
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
    ms.date="09/16/2016"
    ms.author="marsma" />

# <a name="run-job-preparation-and-completion-tasks-on-azure-batch-compute-nodes"></a>Executar tarefas de conclusão e preparação do trabalho em nós de computação de lote do Azure

 Um trabalho de lote do Azure geralmente requer alguma forma de instalação antes de suas tarefas são executadas e posteriores à manutenção de trabalho quando suas tarefas são concluídas. Talvez seja necessário baixar dados de entrada de tarefas comuns no seu nós de computação, ou carregar dados de saída de tarefas ao armazenamento do Azure após concluir o trabalho. Você pode usar as tarefas de **preparação de trabalho** e **Solte de trabalho** para executar essas operações.

## <a name="what-are-job-preparation-and-release-tasks"></a>O que são preparação de trabalho e tarefas de lançamento?

Antes de executar tarefas de uma tarefa, a tarefa de preparação de trabalho é executado em todos os nós de computação agendados para ser executado pelo menos uma tarefa. Depois que o trabalho é concluído, a tarefa de lançamento de trabalho é executado em cada nó no pool que executadas pelo menos uma tarefa. Como com tarefas de lote normal, você pode especificar uma linha de comando a ser chamado quando uma tarefa de preparação ou lançamento de trabalho é executada.

Tarefas de preparação e lançamento oferecem recursos familiares de tarefa do lote como o download do arquivo ([arquivos de recurso][net_job_prep_resourcefiles]), elevados execução, variáveis de ambiente personalizado, duração máxima de execução, contagem de repetição e tempo de retenção de arquivo.

Nas seções a seguir, você vai aprender como usar o [JobPreparationTask] [ net_job_prep] e [JobReleaseTask] [ net_job_release] classes encontradas no [Lote .NET] [ api_net] biblioteca.

> [AZURE.TIP] Tarefas de preparação e lançamento de trabalho são especialmente útil em ambientes "compartilhado pool", que um pool de nós de computação persiste entre as execuções de trabalho e é usado pelo número de trabalhos.

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Quando usar preparação de trabalho e tarefas de lançamento

Preparação do trabalho e tarefas de lançamento de trabalho são uma boa opção nas seguintes situações:

**Baixar dados de tarefas comuns**

Trabalhos em lotes geralmente exigem um conjunto comum de dados como entrada para tarefas de trabalho. Por exemplo, em cálculos de análise de risco diária, dados de mercado são específico de trabalho, ainda comuns para todas as tarefas no trabalho. Esses dados de mercado, geralmente vários gigabytes de tamanho, devem ser baixados para cada nó de computação apenas uma vez para que qualquer tarefa que é executada no nó pode usá-lo. Use uma **tarefa de preparação do trabalho** para baixar esses dados para cada nó antes da execução do trabalho fica outras tarefas.

**Excluir saída de trabalho e tarefa**

Em um ambiente de "compartilhado pool", onde nós de computação de um pool não estão encerrados entre trabalhos, talvez você precise excluir dados de trabalho entre as execuções. Talvez seja necessário economizar espaço em disco em nós ou satisfazer políticas de segurança da sua organização. Use uma **tarefa de lançamento do trabalho** para excluir dados que foi baixados por uma tarefa de preparação do trabalho ou gerados durante a execução da tarefa.

**Retenção de registros**

Talvez você queira manter uma cópia dos arquivos de log que suas tarefas geram ou talvez falhar arquivos de despejo que podem ser gerados por aplicativos falhas. Usar uma **tarefa de lançamento do trabalho** nesses casos para compactar e carregar esses dados para um [Armazenamento do Azure] [ azure_storage] conta.

>[AZURE.TIP] Outra maneira de manter logs e outros trabalhos e tarefas de saída de dados são usar a biblioteca de [Convenções de arquivo de lote do Azure](batch-task-output.md) .

## <a name="job-preparation-task"></a>Tarefa de preparação do trabalho

Antes da execução de tarefas de uma tarefa, o lote executa a tarefa de preparação do trabalho em cada nó de computação que está agendada para executar uma tarefa. Por padrão, o serviço de lote aguarda a tarefa de preparação do trabalho ser concluída antes de executar as tarefas agendadas para executar no nó. No entanto, você pode configurar o serviço não espere. Se o nó for reiniciado, a tarefa de preparação de trabalho é executado novamente, mas você também pode desativar esse comportamento.

A tarefa de preparação de trabalho é executada apenas em nós que estão agendados para executar uma tarefa. Isso impede a execução desnecessária de uma tarefa de preparação no caso de um nó não está atribuído a uma tarefa. Isso pode ocorrer quando o número de tarefas para um trabalho é menor que o número de nós em um pool. Ele também se aplica quando [a execução de tarefas simultâneas](batch-parallel-node-tasks.md) está habilitado, deixando alguns nós ocioso se a contagem de tarefa for menor do que as tarefas de simultâneas possíveis totais. Não executando a tarefa de preparação do trabalho em nós ociosos, você pode passar menos dinheiro em encargos de transferência de dados.

> [AZURE.NOTE] [JobPreparationTask] [ net_job_prep_cloudjob] difere [CloudPool.StartTask] [ pool_starttask] no JobPreparationTask executa no início de cada trabalho, enquanto StartTask executa somente quando um nó de computação primeiro une um pool ou reiniciar.

## <a name="job-release-task"></a>Tarefa de lançamento do trabalho

Depois que um trabalho está marcado como concluída, a tarefa de lançamento de trabalho é executada em cada nó no pool que executadas pelo menos uma tarefa. Você marcar uma tarefa como concluída por emitir uma solicitação de fim. Em seguida, o serviço de lote define o estado de trabalho para *Encerrar*, encerra quaisquer tarefas ativas ou executando associadas ao trabalho e executa a tarefa de lançamento do trabalho. O trabalho depois passa para o estado *concluído* .

> [AZURE.NOTE] Exclusão de trabalho também executa a tarefa de lançamento do trabalho. No entanto, se um trabalho já foi encerrado, a tarefa de lançamento não é executada uma segunda vez se o trabalho posteriormente é excluído.

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Preparação do trabalho e tarefas com .NET em lotes de lançamento

Para usar uma tarefa de preparação do trabalho, atribua um [JobPreparationTask] [ net_job_prep] objeto [CloudJob.JobPreparationTask do seu trabalho] [ net_job_prep_cloudjob] propriedade. Da mesma forma, inicializar uma [JobReleaseTask] [ net_job_release] e atribuí-la a [CloudJob.JobReleaseTask do seu trabalho] [ net_job_prep_cloudjob] propriedade para definir a tarefa de lançamento do trabalho.

Neste trecho de código, `myBatchClient` é uma instância de [BatchClient][net_batch_client], e `myPool` é um pool existente dentro da conta de lote.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobPreparationTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Conforme mencionado anteriormente, a tarefa de lançamento é executada quando um trabalho é encerrado ou excluído. Encerrar um trabalho com [JobOperations.TerminateJobAsync][net_job_terminate]. Excluir um trabalho com [JobOperations.DeleteJobAsync][net_job_delete]. Você normalmente encerra ou excluir um trabalho quando suas tarefas são concluídas, ou quando você atingiu um tempo limite que você definiu.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsy("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Exemplo de código no GitHub

Para verificar a preparação do trabalho e solte tarefas em ação, confira o [JobPrepRelease] [ job_prep_release_sample] projeto de amostra no GitHub. Este aplicativo de console faz o seguinte:

1. Cria um pool com dois nós "pequenas".
2. Cria um trabalho com tarefas padrão, versão e preparação de trabalho.
3. Executa a tarefa de preparação do trabalho, que grava primeiro a identificação de nó para um arquivo de texto no diretório "compartilhado" de um nó.
4. Executa uma tarefa em cada nó que grava sua ID da tarefa para o mesmo arquivo de texto.
5. Depois que todas as tarefas estão concluídas (ou o tempo limite é atingido), imprime o conteúdo do arquivo de texto de cada nó no console.
6. Quando o trabalho for concluído, executa a tarefa de lançamento do trabalho para excluir o arquivo do nó.
6. Imprime os códigos de saída as tarefas de preparação e lançamento de trabalho para cada nó no qual elas executadas.
7. Execução de pausas para permitir que a confirmação da exclusão de trabalho e/ou pool.

Saída do aplicativo de exemplo é semelhante ao seguinte:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

>[AZURE.NOTE] Devido a hora de criação e começar a variável de nós em um novo pool (alguns nós estão prontos para tarefas antes de outras pessoas), você poderá ver uma saída diferente. Especificamente, porque as tarefas concluir rapidamente, um de nós do pool pode executar todas as tarefas do trabalho. Nesse caso, você observará que o trabalho de preparação e tarefas de lançamento não existem para o nó que não executadas nenhuma tarefa.

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Inspecionar preparação de trabalho e tarefas de lançamento no portal do Azure

Quando você executa o aplicativo de exemplo, você pode usar o [portal do Azure] [ portal] para exibir as propriedades de trabalho e suas tarefas ou até mesmo baixar o arquivo de texto compartilhado que é modificado por tarefas do trabalho.

Captura de tela abaixo mostra a **lâmina de tarefas de preparação** no portal do Azure após uma execução do aplicativo de exemplo. Navegue para as propriedades de *JobPrepReleaseSampleJob* depois de suas tarefas concluídas (mas antes de excluir seu trabalho e pool) e clique em **preparação tarefas** ou **lançamento** para exibir suas propriedades.

![Propriedades de preparação do trabalho no portal do Azure][1]

## <a name="next-steps"></a>Próximas etapas

### <a name="application-packages"></a>Pacotes de aplicativos

Além da tarefa de preparação do trabalho, você também pode usar o recurso de [pacotes de aplicativos](batch-application-packages.md) do lote para preparar nós de computação para execução da tarefa. Esse recurso é especialmente útil para implantar aplicativos que não exigem executando um instalador, aplicativos que contêm muitos (100 +) arquivos ou aplicativos que exigem o controle de versão estrita.

### <a name="installing-applications-and-staging-data"></a>Instalar aplicativos e dados de teste

Esta postagem no fórum MSDN fornece uma visão geral dos vários métodos de preparar os nós para execução de tarefas:

[Nós de computadores de instalação de aplicativos e dados no lote de teste][forum_post]

Ele escrito por um dos membros da equipe Azure lote, discute várias técnicas que você pode usar para implantar aplicativos e dados para nós de computadores.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

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

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
