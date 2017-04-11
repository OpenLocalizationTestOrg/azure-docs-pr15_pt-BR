<properties
    pageTitle="Persistência no Azure lote de saída de trabalho e tarefa | Microsoft Azure"
    description="Saiba como usar o armazenamento do Azure como um armazenamento durável para seu trabalho e tarefa em lotes de saída e habilitar a exibição essa saída persistente no portal do Azure."
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
    ms.date="09/07/2016"
    ms.author="marsma" />

# <a name="persist-azure-batch-job-and-task-output"></a>Se mantiver saída de trabalho e tarefas de lote do Azure

As tarefas que você executa no lote geralmente produzem saída que deve ser armazenada e recuperada posteriormente por outras tarefas no trabalho, o aplicativo cliente que executado o trabalho ou ambos. Esta saída pode ser arquivos criados pelo processamento de entrada de dados ou associados a execução da tarefa de arquivos de log. Este artigo apresenta uma biblioteca de classes .NET que usa uma técnica baseado em convenções para cumprir tal saída de tarefas ao armazenamento de Blob do Azure, tornando-o disponível mesmo depois de excluir seu pools, trabalhos e nós de computadores.

Usando a técnica neste artigo, você também pode exibir a saída de tarefa em **logs de salvo** no [portal do Azure]e **arquivos de saída de Saved** [portal].

![Arquivos de saída salvo e seletores de logs salvos no portal][1]

>[AZURE.NOTE] As [Convenções de arquivo do Azure lote] [ nuget_package] biblioteca de classes .NET abordada neste artigo está na visualização. Alguns dos recursos descritos aqui podem ser alteradas antes da disponibilidade geral.

## <a name="task-output-considerations"></a>Considerações de saída de tarefa

Quando você cria sua solução de lote, você deve considerar diversos fatores relacionados a saídas de trabalho e tarefa.

* **Calcular o tempo de vida de nó**: calcular nós são geralmente temporárias, especialmente em pools habilitados em escala automática. Saídas as tarefas que são executadas em um nó estão disponíveis somente enquanto o nó existe e apenas dentro do período de retenção de arquivo que você definiu para a tarefa. Para garantir que a saída da tarefa é preservada, suas tarefas, portanto, devem carregue seus arquivos de saída para um armazenamento durável, por exemplo, o armazenamento do Azure.

* **Armazenamento de saída**: para persistir dados de saída de tarefa para armazenamento durável, você pode usar o [SDK de armazenamento do Azure](../storage/storage-dotnet-how-to-use-blobs.md) em seu código de tarefa para carregar a saída de tarefa em um contêiner de armazenamento de Blob. Se você implementar um contêiner e a convenção de nomenclatura de arquivo, seu aplicativo cliente ou outras tarefas no trabalho podem, em seguida, localize e Baixe esta saída com base na convenção.

* **Recuperação de saída**: você pode recuperar saída tarefas diretamente a partir dos nós de computação em seu pool ou armazenamento do Azure se suas tarefas se mantiver sua saída. Para recuperar a saída de uma tarefa diretamente de um nó de computação, é necessário o nome do arquivo e seu local de saída no nó. Se você se mantiver saída ao armazenamento do Azure, tarefas downstream ou seu aplicativo cliente deve ter o caminho completo para o arquivo no armazenamento do Azure para baixá-lo usando o SDK de armazenamento do Azure.

* **Exibindo saída**: quando você navegar para uma tarefa em lote no portal do Azure e selecione **arquivos no nó**, você verá com todos os arquivos associados com a tarefa, não apenas os arquivos de saída que você está interessado. Novamente, os arquivos em nós de computação estão disponíveis somente enquanto o nó existe e apenas dentro do período de retenção de arquivo que você definiu para a tarefa. Para exibir a saída de tarefa que você já persistentes ao armazenamento do Azure no portal do ou um aplicativo como o [Gerenciador de armazenamento do Azure][storage_explorer], você deve saber seu local e navegue até o arquivo diretamente.

## <a name="help-for-persisted-output"></a>Ajuda para saída persistente

Para ajudá-lo mais facilmente persistirem trabalho e tarefas de saída, a equipe de lote tem definidos e implementado um conjunto de convenções de nomenclatura, além de uma biblioteca de classes .NET, as [Convenções de arquivo do Azure lote] [ nuget_package] biblioteca, que você pode usar em seus aplicativos de lote. Além disso, o portal do Azure está ciente dessas convenções de nomenclatura para que você possa localizar facilmente os arquivos que você armazenou usando a biblioteca.

## <a name="using-the-file-conventions-library"></a>Usando a biblioteca de convenções de arquivo

[Convenções de arquivo do Azure lote] [ nuget_package] é uma biblioteca de classe .NET que seus aplicativos .NET lote podem usar para armazenar e recuperar saídas de tarefa para e do armazenamento do Azure facilmente. Destina-se a uso em código de tarefa e de cliente--no código de tarefa para arquivos persistir e em código do cliente para listar e recuperá-las. Suas tarefas também podem usar a biblioteca para recuperar saídas das tarefas upstream, como em um cenário de [dependências entre tarefas](batch-task-dependencies.md) .

A biblioteca de convenções cuida de garantir que a tarefa e contêineres de armazenamento saída arquivos são nomeados de acordo com a convenção e são carregados no lugar certo quando persistentes ao armazenamento do Azure. Quando você recupera saídas, você pode localizar facilmente as saídas de um determinado trabalho ou uma tarefa por listando ou recuperando saídas por ID e finalidade, em vez de precisar saber nomes de arquivos ou onde ele existe no armazenamento.

Por exemplo, você pode usar a biblioteca "Listar todos os arquivos intermediários para tarefa 7" ou "Obtenha-me a visualização em miniatura para trabalho *mymovie*," sem precisar saber os nomes de arquivo ou local dentro de sua conta de armazenamento.

### <a name="get-the-library"></a>Obtenha a biblioteca

Você pode obter a biblioteca, que contém novas classes e estende a [CloudJob] [ net_cloudjob] e [CloudTask] [ net_cloudtask] classes com novos métodos, do [NuGet][nuget_package]. Você pode adicioná-lo ao seu projeto do Visual Studio usando o [Gerenciador de pacotes do NuGet biblioteca][nuget_manager].

>[AZURE.TIP] Você pode encontrar o [código-fonte] [ github_file_conventions] para a biblioteca de convenções de arquivo do Azure lote no GitHub no Microsoft Azure SDK do repositório de .NET.

## <a name="requirement-linked-storage-account"></a>Requisito: conta de armazenamento vinculado

Para armazenar saídas armazenamento durável usando a biblioteca de convenções de arquivo e exibi-los no portal do Azure, você deve [vincular uma conta de armazenamento do Azure](batch-application-packages.md#link-a-storage-account) à sua conta do lote. Se você ainda não começou, vincule uma conta de armazenamento à sua conta de lote usando o portal do Azure:

**Conta de lote** blade > **configurações** > **Conta de armazenamento** > **Conta de armazenamento** (nenhum) > Selecionar uma conta de armazenamento em sua assinatura

Para uma orientação sobre mais detalhada sobre como vincular uma conta de armazenamento, consulte [implantação do aplicativo com pacotes de aplicativos do Azure lote](batch-application-packages.md).

## <a name="persist-output"></a>Se mantiver saída

Há duas ações principais para executar quando salvar a saída de trabalho e tarefa com a biblioteca de convenções de arquivo: criar o contêiner de armazenamento e salvar a saída para o contêiner.

>[AZURE.WARNING] Como todas as saídas de trabalho e tarefa são armazenadas no mesmo contêiner, [limites de otimização de armazenamento](../storage/storage-performance-checklist.md#blobs) podem ser impostas se um grande número de tarefas tentar se mantiver arquivos ao mesmo tempo.

### <a name="create-storage-container"></a>Criar contêiner de armazenamento

Antes de suas tarefas começam saída persistente para armazenamento, você deve criar um contêiner de armazenamento de blob à qual eles carregará sua saída. Fazer isso chamando [CloudJob][net_cloudjob]. [PrepareOutputStorageAsync] [net_prepareoutputasync]. Esse método de extensão leva uma [CloudStorageAccount] [ net_cloudstorageaccount] objeto como um parâmetro e cria um contêiner chamado de forma que seu conteúdo é detectável pelo portal do Azure e os métodos de recuperação abordados posteriormente neste artigo.

Normalmente, você coloque esse código no seu aplicativo cliente - o aplicativo que cria seu pools, trabalhos e tarefas.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Armazenar saídas de tarefa

Agora que você já preparado um contêiner no armazenamento de blob, tarefas podem salvar saída ao contêiner usando o [TaskOutputStorage] [ net_taskoutputstorage] classe encontrada na biblioteca de convenções do arquivo.

No seu código de tarefa, primeiro crie um [TaskOutputStorage] [ net_taskoutputstorage] objeto, em seguida, quando a tarefa foi concluída seu trabalho, chame o [TaskOutputStorage][net_taskoutputstorage]. [SaveAsync] [net_saveasync] método para salvar sua saída ao armazenamento do Azure.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

O parâmetro "tipo de saída" categoriza os arquivos persistentes. Há quatro predefinidos [TaskOutputKind] [ net_taskoutputkind] tipos: "TaskOutput", "TaskPreview", "TaskLog" e "TaskIntermediate". Você também pode definir tipos personalizados se eles seria útil em seu fluxo de trabalho.

Esses tipos de saída permitem especificar qual tipo de saídas para listar quando você mais tarde consulta lote saídas persistentes de uma determinada tarefa. Em outras palavras, quando você lista saídas para uma tarefa, você pode filtrar a lista em um dos tipos de saída. Por exemplo, "Enviar-me a saída de *visualização* para tarefa *109*." Mais na listagem e a recuperação de saídas aparece na [recuperar saída](#retrieve-output) posteriormente neste artigo.

>[AZURE.TIP] O tipo de saída também designa onde no portal do Azure um determinado arquivo aparece: *TaskOutput*-arquivos categorizados aparecem em "Arquivos de saída de tarefa" e *TaskLog* arquivos aparecem em "Logs de tarefas".

### <a name="store-job-outputs"></a>Armazenar saídas de trabalho

Além de armazenar saídas de tarefa, você pode armazenar saídas associadas a todo o trabalho. Por exemplo, a tarefa de mesclagem de um trabalho de processamento de filme, você pode persistir o filme totalmente renderizado como uma saída de trabalho. Quando seu trabalho for concluído, seu aplicativo cliente pode simplesmente listar e recuperar saídas para o trabalho e não é necessário consultar as tarefas individuais.

Armazenar a saída de trabalho chamando a [JobOutputStorage][net_joboutputstorage]. [SaveAsync] [net_joboutputstorage_saveasync] método, e especifique o [JobOutputKind] [ net_joboutputkind] e nome de arquivo:

```
CloudJob job = await batchClient.JobOperations.GetJobAsync(jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Como com TaskOutputKind para saídas de tarefa, você usa o [JobOutputKind] [ net_joboutputkind] parâmetro para categorizar um trabalho do persistentes arquivos. Este parâmetro permite consulta posterior para (lista) um tipo específico de saída. O JobOutputKind inclui tipos de saída e visualização e suporta Criando tipos personalizados.

### <a name="store-task-logs"></a>Armazenar logs de tarefa

Além de manter um arquivo para armazenamento durável quando uma tarefa ou tarefa for concluída, talvez seja necessário para manter os arquivos que são atualizados durante a execução de uma tarefa, arquivos de log ou `stdout.txt` e `stderr.txt`, por exemplo. Para essa finalidade, a biblioteca de convenções de arquivo do Azure lote fornece a [TaskOutputStorage][net_taskoutputstorage]. [SaveTrackedAsync] [net_savetrackedasync] método. Com [SaveTrackedAsync][net_savetrackedasync], você pode controlar atualizações em um arquivo no nó (em um intervalo que você especificar) e manter as atualizações ao armazenamento do Azure.

Trecho de código a seguir, podemos usar [SaveTrackedAsync] [ net_savetrackedasync] atualizar `stdout.txt` em armazenamento do Azure cada 15 segundos durante a execução da tarefa:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
    await Task.Delay(stdoutFlushDelay);
}
```

`Code to process data and produce output file(s)`é simplesmente um espaço reservado para o código que sua tarefa normalmente deve executar. Por exemplo, você pode ter código que baixa dados do armazenamento do Azure e executa transformação ou cálculo nele. A parte importante esse trecho está demonstrando como você pode quebrar esse código em uma `using` bloco para atualizar periodicamente um arquivo com [SaveTrackedAsync][net_savetrackedasync].

O `Task.Delay` for necessária no fim deste `using` bloco para garantir que o agente de nó tem tempo para liberar o conteúdo de saída padrão para o arquivo de stdout.txt no nó (o agente de nó é um programa que é executado em cada nó no pool e fornece a interface de comando e controle entre o nó e o serviço de lote). Sem este atraso, é possível perder os últimos segundos de saída. Esse atraso não pode ser necessário para todos os arquivos.

>[AZURE.NOTE] Quando você habilita o rastreamento com SaveTrackedAsync de arquivos, somente *acrescenta* o arquivo controladas são mantidas ao armazenamento do Azure. Use esse método somente para arquivos de log não girando ou outros arquivos que são anexados a, ou seja, os dados de rastreamento só é adicionado ao final do arquivo quando ele for atualizado.

## <a name="retrieve-output"></a>Recuperar saída

Quando você recupera a saída persistente usando a biblioteca de convenções de arquivo do Azure lote, você pode fazer isso de maneira centrados em tarefas e em trabalho. Você pode solicitar a saída para determinada tarefa ou trabalho sem precisar saber seu caminho no armazenamento de blob ou até mesmo seu nome de arquivo. Você pode simplesmente dizer "Dê os arquivos de saída para tarefa *109*."

O trecho de código a seguir itera em todas as tarefas de um trabalho, imprime algumas informações sobre os arquivos de saída para a tarefa e, em seguida, baixa seus arquivos do armazenamento de.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (TaskOutputStorage output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="task-outputs-and-the-azure-portal"></a>Tarefas saídas e o portal do Azure

O portal do Azure Exibe logs que são persistentes e saídas de tarefa para uma conta de armazenamento do Azure vinculada usando as convenções de nomenclatura encontradas no [Azure lote arquivo convenções Leiame][github_file_conventions_readme]. Você pode implementar essas convenções em um idioma de sua escolha, ou você pode usar a biblioteca de convenções de arquivo em seus aplicativos .NET.

### <a name="enable-portal-display"></a>Habilitar a exibição do portal

Para habilitar a exibição de suas saídas no portal do, você deve satisfazer os seguintes requisitos:

 1. [Vincular uma conta de armazenamento do Azure](#requirement-linked-storage-account) para sua conta de lote.
 2. Seguem as convenções de nomenclatura predefinidas para contêineres de armazenamento e arquivos quando persistir saídas. Você pode encontrar a definição dessas convenções na biblioteca de convenções de arquivo [Leiame][github_file_conventions_readme]. Se você usar as [Convenções de arquivo do Azure lote] [ nuget_package] biblioteca para manter sua saída, esse requisito é satisfeita.

### <a name="view-outputs-in-the-portal"></a>Saídas de modo de exibição no portal

Para exibir logs e saídas de tarefas no portal do Azure, navegue até a tarefa cuja saída você está interessado, em seguida, clique em **arquivos de saída Saved** ou **registros salvos**. Esta imagem mostra os **arquivos de saída salvo** da tarefa com a ID "007":

![Tarefa saídas blade no portal do Azure][2]

## <a name="code-sample"></a>Amostra de código

O [PersistOutputs] [ github_persistoutputs] projeto de amostra é um dos [exemplos de código do Azure lote] [ github_samples] no GitHub. Essa solução Visual Studio 2015 demonstra como usar a biblioteca de convenções de arquivo de lote do Azure para persistir saída de tarefa para armazenamento durável. Para executar o exemplo, siga estas etapas:

1. Abra o projeto no **Visual Studio 2015**.
2. Adicione suas **credenciais de conta** de lote e armazenamento para **AccountSettings.settings** no projeto Microsoft.Azure.Batch.Samples.Common.
3. **Construir** (mas não são executadas) a solução. Restaure quaisquer pacotes NuGet se for solicitado.
4. Use o portal do Azure para carregar um [pacote de aplicativos](batch-application-packages.md) para **PersistOutputsTask**. Incluir o `PersistOutputsTask.exe` e seus conjuntos de módulos dependentes no pacote zip, defina a ID do aplicativo para "PersistOutputsTask" e a versão do pacote de aplicativo "1.0".
5. **Iniciar** projeto (Executar) o **PersistOutputs** .

## <a name="next-steps"></a>Próximas etapas

### <a name="application-deployment"></a>Implantação do aplicativo

O recurso de [pacotes de aplicativos](batch-application-packages.md) do lote fornece uma maneira fácil para ambos implantar e versão os aplicativos que suas tarefas executar em nós de computadores.

### <a name="installing-applications-and-staging-data"></a>Instalar aplicativos e dados de teste

Confira os [aplicativos de instalação e dados de preparação no lote Calcular nós] [ forum_post] postar no Fórum do Azure lote para uma visão geral dos vários métodos para preparar sua nós para execução de tarefas. Esta postagem escrito por um dos membros da equipe lote do Azure, é uma boa introdução sobre as diferentes maneiras de obter os arquivos (incluindo os aplicativos e dados de entrada de tarefa) em seus nós de computação e algumas considerações especiais para cada método.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Arquivos de saída salvo e seletores de logs salvos no portal"
[2]: ./media/batch-task-output/task-output-02.png "Tarefa saídas blade no portal do Azure"