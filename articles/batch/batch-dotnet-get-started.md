<properties
    pageTitle="Tutorial - começar com a biblioteca do Azure lote .NET | Microsoft Azure"
    description="Aprenda os conceitos básicos do lote do Azure e como desenvolver para o serviço de lote com um cenário de exemplo."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="08/15/2016"
    ms.author="marsma"/>

# <a name="get-started-with-the-azure-batch-library-for-net"></a>Começar a usar a biblioteca de lote do Azure para .NET

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Aprenda as Noções básicas do [Azure lote] [ azure_batch] e o [Lote .NET] [ net_api] biblioteca neste artigo discutir um aplicativo de amostra c# passo a passo. Vamos examinar como este aplicativo de exemplo utiliza o serviço de lote para processar uma carga de trabalho paralela na nuvem, bem como ele interage com [O armazenamento do Azure](../storage/storage-introduction.md) para recuperação e transferência de arquivo. Você aprenderá técnicas de fluxo de trabalho do aplicativo em lotes comuns. Você também obter uma compreensão base dos componentes principais do lote, como trabalhos, tarefas, pools e nós de computadores.

![Fluxo de trabalho do lote solução (basic)][11]<br/>

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tenha um conhecimento de trabalho do c# e do Visual Studio. Ele também pressupõe que você seja capaz de satisfazer os requisitos de criação de conta que são especificados abaixo do Azure e os serviços de armazenamento e lote.

### <a name="accounts"></a>Contas

- **Conta do Microsoft Azure**: se você ainda não tiver uma assinatura do Azure, [Crie uma conta gratuita do Azure][azure_free_account].
- **Conta de lote**: depois que você tiver uma assinatura do Azure, [Crie uma conta de lote do Azure](batch-account-create-portal.md).
- **Conta de armazenamento**: consulte [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) no [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md).

> [AZURE.IMPORTANT] Lote atualmente oferece suporte *apenas* o tipo de conta de armazenamento de **finalidade geral** , conforme descrito na etapa 5 # [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) em [contas de armazenamento do Azure sobre](../storage/storage-create-storage-account.md).

### <a name="visual-studio"></a>O Visual Studio

Você deve ter o **Visual Studio 2015** para construir o projeto de amostra. Você pode encontrar versões de avaliação e gratuitas do Visual Studio a [Visão geral dos produtos do Visual Studio 2015][visual_studio].

### <a name="dotnettutorial-code-sample"></a>Exemplo de código de *DotNetTutorial*

O [DotNetTutorial] [ github_dotnettutorial] amostra é um dos exemplos de código muitos encontrados nos [exemplos de lote azure] [ github_samples] repositório no GitHub. Você pode baixar o exemplo clicando no botão **Baixar ZIP** na home page do repositório ou clicando o [azure-lote-amostras-master.zip] [ github_samples_zip] link de download direto. Depois de extrair o conteúdo do arquivo ZIP, você encontrará a solução na seguinte pasta:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### <a name="azure-batch-explorer-optional"></a>Azure Explorer lote (opcional)

O [Azure lote Explorer] [ github_batchexplorer] é um utilitário gratuito que está incluído nos [exemplos de lote azure] [ github_samples] repositório no GitHub. Embora não seja necessário para concluir este tutorial, ele pode ser útil durante o desenvolvimento e depuração de suas soluções de lote.

## <a name="dotnettutorial-sample-project-overview"></a>Visão geral do projeto de amostra DotNetTutorial

O exemplo de código *DotNetTutorial* é uma solução de 2015 do Visual Studio que consiste em dois projetos: **DotNetTutorial** e **TaskApplication**.

- **DotNetTutorial** é o aplicativo cliente que interage com os serviços de armazenamento e lote para executar uma carga de trabalho paralela em calcular nós (máquinas virtuais). DotNetTutorial é executado na sua estação de trabalho local.

- **TaskApplication** é o programa que é executado em nós de computação no Azure para realizar o trabalho real. No exemplo, `TaskApplication.exe` analisa o texto em um arquivo baixado do armazenamento do Azure (o arquivo de entrada). Em seguida, ela produz um arquivo de texto (o arquivo de saída) que contém uma lista das três palavras principais que aparecem no arquivo de entrada. Após criar o arquivo de saída, TaskApplication carrega o arquivo ao armazenamento do Azure. Isso disponibiliza para o aplicativo cliente para download. TaskApplication é executado em paralelo em vários nós de computação no serviço em lotes.

O diagrama a seguir ilustra as operações principais que são executadas pelo aplicativo cliente, *DotNetTutorial*e o aplicativo que é executado pelas tarefas, *TaskApplication*. Este fluxo de trabalho básico é típico de muitas soluções de computação que são criadas com o lote. Enquanto não Demonstre cada recurso disponível no serviço de lote, quase todos os cenários de lote incluem processos semelhantes.

![Fluxo de trabalho de exemplo em lotes][8]<br/>

[**Etapa 1.**](#step-1-create-storage-containers) Crie **contêineres** no armazenamento de Blob do Azure.<br/>
[**Etapa 2.**](#step-2-upload-task-application-and-data-files) Carregar arquivos de aplicativo de tarefa e arquivos de entrada de email do Outlook.<br/>
[**Etapa 3.**](#step-3-create-batch-pool) Crie um lote de **pool**.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** O pool **StartTask** baixa os arquivos binários da tarefa (TaskApplication) para nós ao entrarem o pool.<br/>
[**Etapa 4.**](#step-4-create-batch-job) Crie um lote de **trabalho**.<br/>
[**Etapa 5.**](#step-5-add-tasks-to-job) Adicione **tarefas** ao trabalho.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** As tarefas são agendadas para executar em nós.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Cada tarefa downloads de seus dados de entrada do armazenamento do Azure e começa a execução.<br/>
[**Etapa 6.**](#step-6-monitor-tasks) Monitorar tarefas.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Como as tarefas são concluídas, elas carregue seus dados de saída ao armazenamento do Azure.<br/>
[**Etapa 7.**](#step-7-download-task-output) Baixe a saída de tarefa do armazenamento.

Conforme mencionado, não todas as soluções do lote executa estas etapas exatas e podem incluir muitos mais, mas o aplicativo de exemplo *DotNetTutorial* demonstra processos comuns encontrados em uma solução de lote.

## <a name="build-the-dotnettutorial-sample-project"></a>Criar o projeto de amostra *DotNetTutorial*

Antes de poder executar com êxito o exemplo, você deverá especificar lote e armazenamento de credenciais de conta do projeto de *DotNetTutorial* `Program.cs` arquivo. Se você não tiver feito isso, abra a solução no Visual Studio clicando duas vezes na `DotNetTutorial.sln` arquivo de solução. Ou abri-lo de dentro do Visual Studio usando o **arquivo > Abrir > projeto/solução** menu.

Abrir `Program.cs` dentro do projeto *DotNetTutorial* . Adicione suas credenciais conforme especificado na parte superior do arquivo:

```
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

> [AZURE.IMPORTANT] Conforme mencionado acima, atualmente especifique as credenciais para uma conta de armazenamento de **finalidade geral** no armazenamento do Azure. Seus aplicativos de lote usarem o armazenamento de blob dentro da conta de armazenamento de **finalidade geral** . Não especifique as credenciais para uma conta de armazenamento que foi criado, selecionando o tipo de conta de *armazenamento de Blob* .

Você pode encontrar suas credenciais de conta de armazenamento e lote dentro a lâmina de conta de cada serviço no [portal do Azure][azure_portal]:

![Credenciais no portal de lote][9]
![credenciais de armazenamento no portal][10]<br/>

Agora que você atualizou o projeto com suas credenciais, clique com botão direito a solução no Solution Explorer e clique em **Criar solução**. Confirme a restauração de quaisquer pacotes NuGet, se você for solicitado.

> [AZURE.TIP] Se os pacotes NuGet não são restaurados automaticamente ou se você vir erros sobre uma falha ao restaurar os pacotes, certifique-se de que você tenha o [NuGet Package Manager] [ nuget_packagemgr] instalado. Em seguida, habilite o download de pacotes ausentes. Consulte [Habilitando pacote restaurar durante construir] [ nuget_restore] para habilitar o download do pacote.

Nas seções a seguir, podemos dividir o aplicativo de exemplo para as etapas que ele executa para processar uma carga de trabalho no serviço em lotes e aborde essas etapas em detalhes. Recomendamos que faça referência a solução aberta no Visual Studio enquanto você progredir o restante deste artigo, desde que não cada linha de código na amostra é discutida.

Navegue até o topo da `MainAsync` método do projeto de *DotNetTutorial* `Program.cs` arquivo para começar a etapa 1. Cada etapa abaixo e em seguida, aproximadamente segue a progressão das chamadas de método em `MainAsync`.

## <a name="step-1-create-storage-containers"></a>Etapa 1: Criar contêineres de armazenamento

![Criar contêineres em armazenamento do Azure][1]
<br/>

Lote inclui suporte interno para interagir com o armazenamento do Azure. Contêineres na sua conta de armazenamento fornecerá os arquivos necessários às tarefas que são executadas em sua conta do lote. Os contêineres também fornecem um local para armazenar os dados de saída que produzem as tarefas. A primeira coisa que o aplicativo de cliente *DotNetTutorial* faz é criar três contêineres no [Armazenamento de Blob do Azure](../storage/storage-introduction.md):

- **aplicativo**: este contêiner armazenará o aplicativo executar as tarefas, bem como qualquer uma de suas dependências, como DLLs.
- **entrada**: tarefas serão baixadas os arquivos de dados para processar a partir do contêiner de *entrada* .
- **saída**: quando tarefas concluir processamento de arquivo de entrada, elas serão carregadas os resultados ao contêiner de *saída* .

Para interagir com uma conta de armazenamento e criar contêineres, podemos usar a [Biblioteca de cliente de armazenamento do Azure para .NET][net_api_storage]. Vamos criar uma referência para a conta com [CloudStorageAccount][net_cloudstorageaccount]e de que criar um [CloudBlobClient][net_cloudblobclient]:

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to
// blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Usamos o `blobClient` referência em todo o aplicativo e passá-lo como um parâmetro para vários métodos. Um exemplo disto é no bloco de código imediatamente posterior acima, onde chamamos `CreateContainerIfNotExistAsync` realmente criar os contêineres.

```csharp
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```csharp
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container =
            blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

Depois que os contêineres foram criados, o aplicativo agora pode carregar os arquivos que serão usados pelas tarefas.

> [AZURE.TIP] [Como usar o armazenamento de Blob do .NET](../storage/storage-dotnet-how-to-use-blobs.md) fornece uma boa visão geral de trabalhar com blobs e contêineres de armazenamento do Azure. Ele deve estar na parte superior da sua lista de leitura como começar a trabalhar com lote.

## <a name="step-2-upload-task-application-and-data-files"></a>Etapa 2: Carregar arquivos de aplicativos e dados de tarefa

![Carregar arquivos de entrada (dados) e aplicativo de tarefa para contêineres][2]
<br/>

Na operação de carregamento de arquivo, *DotNetTutorial* primeiro define coleções dos caminhos de arquivo de **entrada** de **aplicativos** e como elas existem no computador local. Em seguida, ele carrega esses arquivos para os contêineres que você criou na etapa anterior.

```
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

Há dois métodos no `Program.cs` que estão envolvidos no processo de carregamento:

- `UploadFilesToContainerAsync`: Este método retorna uma coleção de [ResourceFile] [ net_resourcefile] objetos (discutidos abaixo) e internamente chamadas `UploadFileToContainerAsync` para carregar cada arquivo que é passado no parâmetro *filePaths* .
- `UploadFileToContainerAsync`: Este é o método que realmente executa o carregamento de arquivo e cria o [ResourceFile] [ net_resourcefile] objetos. Depois de carregar o arquivo, ele obtém uma assinatura de acesso compartilhado (SAS) para o arquivo e retorna um objeto de ResourceFile que o representa. Acesso assinaturas também são discutidas abaixo compartilhado.

```
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath, FileMode.Open);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### <a name="resourcefiles"></a>ResourceFiles

Uma [ResourceFile] [ net_resourcefile] fornece tarefas em lote com a URL para um arquivo em armazenamento do Azure que são baixadas para um nó de computação antes dessa tarefa é executada. O [ResourceFile.BlobSource] [ net_resourcefile_blobsource] propriedade especifica a URL completa do arquivo conforme ela existe no armazenamento do Azure. A URL também pode incluir uma assinatura de acesso compartilhado (SAS) que fornece acesso seguro para o arquivo. A maioria dos tipos de tarefas em lote .NET incluem uma propriedade de *ResourceFiles* , incluindo:

- [CloudTask][net_task]
- [StartTask][net_pool_starttask]
- [JobPreparationTask][net_jobpreptask]
- [JobReleaseTask][net_jobreltask]

O aplicativo de exemplo DotNetTutorial não usa os tipos de tarefa JobPreparationTask ou JobReleaseTask, mas você pode ler mais sobre eles em [nós de computadores de tarefas de conclusão e preparação de trabalho de executar em lote do Azure](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Assinatura de acesso compartilhado (SAS)

Acesso compartilhado assinaturas são cadeias de caracteres que — quando incluídos como parte de uma URL — fornecer acesso seguro a contêineres e blobs no armazenamento do Azure. O aplicativo usa blob e contêiner compartilhados de DotNetTutorial acessar URLs de assinatura e demonstra como obter essas cadeias de caracteres de assinatura de acesso compartilhado do serviço de armazenamento.

- **Blob compartilhados assinaturas de acesso**: StartTask do pool em DotNetTutorial usa assinaturas de acesso do blob compartilhado ao baixar os binários do aplicativo e arquivos de dados de entrada do armazenamento (consulte a etapa 3 abaixo). O `UploadFileToContainerAsync` método do DotNetTutorial `Program.cs` contém o código que obtém a assinatura de acesso compartilhado de cada blob. Ela faz isso chamando [CloudBlob.GetSharedAccessSignature][net_sas_blob].

- **Contêiner compartilhados assinaturas de acesso**: que cada tarefa terminar seu trabalho no nó de computadores, ele carrega o seu arquivo de saída para o contêiner de *saída* no armazenamento do Azure. Para fazer isso, TaskApplication usa uma assinatura de acesso compartilhado de contêiner que fornece acesso de gravação para o contêiner como parte do caminho quando ele carrega o arquivo. Obtendo a assinatura de acesso compartilhado contêiner é feito de maneira semelhante como quando obter o blob compartilhado assinatura de acesso. DotNetTutorial, você verá que a `GetContainerSasUrl` método auxiliar chama [CloudBlobContainer.GetSharedAccessSignature] [ net_sas_container] para fazê-lo. Você vai ler mais sobre como TaskApplication usa o contêiner compartilhados assinatura de acesso em "etapa 6: monitorar tarefas."

> [AZURE.TIP] Confira a série de duas partes sobre assinaturas de acesso compartilhado, [parte 1: Noções básicas sobre o modelo de assinatura (SAS) acesso compartilhado](../storage/storage-dotnet-shared-access-signature-part-1.md) e [parte 2: criar e usar uma assinatura de acesso compartilhado (SAS) com o armazenamento de Blob](../storage/storage-dotnet-shared-access-signature-part-2.md), para saber mais sobre como fornecer acesso seguro a dados em sua conta de armazenamento.

## <a name="step-3-create-batch-pool"></a>Etapa 3: Criar o pool de lote

![Criar um pool de lote][3]
<br/>

Um lote **pool** é uma coleção de nós de computação (máquinas virtuais) no qual o lote executa tarefas de um trabalho.

Depois que ele carrega os aplicativo e arquivos de dados para a conta de armazenamento, *DotNetTutorial* começa sua interação com o serviço de lote usando a biblioteca de lote .NET. Para fazer isso, uma [BatchClient] [ net_batchclient] é criado pela primeira vez:

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

Em seguida, um pool de nós de computação é criado na conta lote com uma chamada para `CreatePoolAsync`. `CreatePoolAsync`usa o [BatchClient.PoolOperations.CreatePool] [ net_pool_create] método para criar um pool no serviço em lotes.

```csharp
private static async Task CreatePoolAsync(
    BatchClient batchClient,
    string poolId,
    IList<ResourceFile> resourceFiles)
{
    Console.WriteLine("Creating pool [{0}]...", poolId);

    // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(),
    // no pool is actually created in the Batch service. This CloudPool instance is
    // therefore considered "unbound," and we can modify its properties.
    CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicated: 3,           // 3 compute nodes
            virtualMachineSize: "small",  // single-core, 1.75 GB memory, 224 GB disk
            cloudServiceConfiguration:
                new CloudServiceConfiguration(osFamily: "4")); // Win Server 2012 R2

    // Create and assign the StartTask that will be executed when compute nodes join
    // the pool. In this case, we copy the StartTask's resource files (that will be
    // automatically downloaded to the node by the StartTask) into the shared
    // directory that all tasks will have access to.
    pool.StartTask = new StartTask
    {
        // Specify a command line for the StartTask that copies the task application
        // files to the node's shared directory. Every compute node in a Batch pool
        // is configured with several pre-defined environment variables that you can
        // reference by using commands or applications run by tasks.

        // Since a successful execution of robocopy can return a non-zero exit code
        // (e.g. 1 when one or more files were successfully copied) we need to
        // manually exit with a 0 for Batch to recognize StartTask execution success.
        CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
        ResourceFiles = resourceFiles,
        WaitForSuccess = true
    };

    await pool.CommitAsync();
}
```

Quando você cria um pool com [CreatePool][net_pool_create], especificar várias parâmetros como o número de nós de computação, o [tamanho de nós](../cloud-services/cloud-services-sizes-specs.md)e sistema operacional de nós. *DotNetTutorial*, podemos usar [CloudServiceConfiguration] [ net_cloudserviceconfiguration] para especificar o Windows Server 2012 R2 dos [Serviços](../cloud-services/cloud-services-guestos-update-matrix.md)de nuvem. No entanto, especificando uma [VirtualMachineConfiguration] [ net_virtualmachineconfiguration] em vez disso, você pode criar pools de nós criado a partir de imagens do Marketplace, que inclui imagens do Windows e Linux — consulte [nós em lote Azure pools de computadores provisionar Linux](batch-linux-nodes.md) para obter mais informações.

> [AZURE.IMPORTANT] Cobrado para recursos de computação no lote. Para minimizar os custos, você pode reduzir `targetDedicated` para 1 antes de executar a amostra.

Junto com essas propriedades de nó físico, você também pode especificar um [StartTask] [ net_pool_starttask] para o pool. O StartTask é executado em cada nó conforme o nó ingressa o pool e cada vez que um nó seja reiniciado. O StartTask é especialmente útil para instalar aplicativos em nós de computação antes da execução de tarefas. Por exemplo, se suas tarefas processam dados usando scripts Python, você poderia usar um StartTask instalar Python nos nós de computação.

Neste aplicativo de exemplo, o StartTask copia os arquivos que baixa de armazenamento (que são especificados usando o [StartTask][net_starttask].[ ResourceFiles] [ net_starttask_resourcefiles] propriedade) do diretório de trabalho StartTask à pasta compartilhada que *todas as* tarefas em execução no nó podem acessar. Essencialmente, isso copia `TaskApplication.exe` e suas dependências à pasta compartilhada em cada nó como o nó ingressará pool, para que todas as tarefas que executar no nó possam acessá-lo.

> [AZURE.TIP] O recurso de **pacotes de aplicativos** do Azure lote fornece outra maneira de obter seu aplicativo para os nós de computação em um pool. Consulte [implantação do aplicativo com pacotes de aplicativos do Azure lote](batch-application-packages.md) para obter detalhes.

Também notável no trecho de código acima é o uso de duas variáveis de ambiente na propriedade de *linha de comando* da StartTask: `%AZ_BATCH_TASK_WORKING_DIR%` e `%AZ_BATCH_NODE_SHARED_DIR%`. Cada nó de computação dentro de um pool de lote é configurado automaticamente com diversas variáveis de ambiente que são específicas ao lote. Qualquer processo que é executado por uma tarefa tem acesso a essas variáveis de ambiente.

> [AZURE.TIP] Para saber mais sobre o ambiente variáveis que estão disponíveis em nós de computação em um pool de lote e informações em pastas de trabalho de tarefa, consulte as seções de [configurações de ambiente para tarefas](batch-api-basics.md#environment-settings-for-tasks) e [arquivos e pastas](batch-api-basics.md#files-and-directories) na [Visão geral do recurso de lote para desenvolvedores](batch-api-basics.md).

## <a name="step-4-create-batch-job"></a>Etapa 4: Criar trabalho em lotes

![Criar trabalho em lotes][4]<br/>

Um lote de **trabalho** é um conjunto de tarefas e está associado a um pool de nós de computação. As tarefas em um trabalho executar em nós de computação do pool associado.

Você pode usar um trabalho não apenas para organizar e controlar tarefas em cargas de trabalho relacionadas, mas também para impor determinadas restrições, como o tempo de execução máximo para o trabalho (e por extensão, suas tarefas), além de prioridade de trabalho em relação a outros trabalhos na conta do lote. No entanto, neste exemplo, o trabalho está associado apenas com o pool criado na etapa 3 de #. Sem propriedades adicionais são configuradas.

Todos os trabalhos de lote estão associados um determinado pool. Esta associação indica quais nós tarefas do trabalho executará em. Você pode especificar isso usando o [CloudJob.PoolInformation] [ net_job_poolinfo] propriedade, conforme mostrado no trecho de código abaixo.

```csharp
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

Agora que um trabalho tiver sido criado, as tarefas são adicionadas para realizar o trabalho.

## <a name="step-5-add-tasks-to-job"></a>Etapa 5: Adicionar tarefas ao trabalho

![Adicionar tarefas ao trabalho][5]<br/>
*(1) tarefas são adicionadas ao trabalho, (2) as tarefas são agendadas para executar em nós e (3) as tarefas baixar os arquivos de dados para processar*

Em lotes **tarefas** são as unidades individuais de trabalho que são executados em nós de computação. Uma tarefa tem uma linha de comando e executa os scripts ou executáveis que você especificar nessa linha de comando.

Para realizar o trabalho realmente, tarefas devem ser adicionadas a um trabalho. Cada [CloudTask] [ net_task] é configurado usando uma propriedade de linha de comando e [ResourceFiles] [ net_task_resourcefiles] (como com StartTask do pool) que a tarefa baixa a nó antes de sua linha de comando é executada automaticamente. No projeto de amostra de *DotNetTutorial* , cada tarefa processa somente um arquivo. Portanto, sua coleção de ResourceFiles contém um único elemento.

```csharp
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [AZURE.IMPORTANT] Quando acessam variáveis de ambiente como `%AZ_BATCH_NODE_SHARED_DIR%` ou executar um aplicativo não encontrado no nó `PATH`, linhas de comando de tarefa devem ser prefixadas com `cmd /c`. Isso explicitamente executará o intérprete de comandos e instruí-la para terminar após executar o comando. Esse requisito é desnecessário se suas tarefas executar um aplicativo no nó `PATH` (como *robocopy.exe* ou *powershell.exe*) e sem variáveis de ambiente são usadas.

Dentro do `foreach` loop no trecho de código acima, você pode ver que a linha de comando para a tarefa é criada que são passados três argumentos de linha de comando para *TaskApplication.exe*:

1. O **primeiro argumento** é o caminho do arquivo para processar. Este é o caminho local para o arquivo conforme ela existe no nó. Quando o ResourceFile objeto no `UploadFileToContainerAsync` foi criado pela primeira vez acima, o nome de arquivo foi usado para essa propriedade (como um parâmetro para o construtor de ResourceFile). Isso indica que o arquivo pode ser encontrado no mesmo diretório como *TaskApplication.exe*.

2. O **segundo argumento** Especifica que as palavras de *N* superiores devem ser escritas para o arquivo de saída. No exemplo, isso é embutida para que as três palavras superiores são gravadas ao arquivo de saída.

3. O **terceiro argumento** é a assinatura de acesso compartilhado (SAS) que fornece acesso de gravação ao contêiner de **saída** no armazenamento do Azure. *TaskApplication.exe* usa esta URL de assinatura de acesso compartilhado quando ele carrega o arquivo de saída para o armazenamento do Azure. Você pode encontrar o código para isso na `UploadFileToContainer` método do projeto de TaskApplication `Program.cs` arquivo:

```csharp
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath, FileMode.Open);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## <a name="step-6-monitor-tasks"></a>Etapa 6: Tarefas de Monitor

![Monitorar tarefas][6]<br/>
*O aplicativo cliente (1) monitora as tarefas de conclusão e status de sucesso e (2) as tarefas carregar dados de resultado ao armazenamento do Azure*

Quando tarefas são adicionadas a um trabalho, eles são automaticamente na fila e agendados para execução em nós de computação dentro do pool associados ao trabalho. Com base nas configurações que você especificar, o lote manipula fila de todas as tarefas, agendamento, repetindo e outras tarefas de administração de tarefa para você. Há muitas abordagens para monitorar a execução da tarefa. DotNetTutorial mostra um exemplo simples que relatórios somente em falha de conclusão e tarefas ou sucesso estados.

Dentro do `MonitorTasks` método do DotNetTutorial `Program.cs`, há três conceitos lote .NET que garantem discussão. Elas estão listadas abaixo na ordem de aparência:

1. **ODATADetailLevel**: especificando [ODATADetailLevel] [ net_odatadetaillevel] na lista de operações (como obter uma lista de tarefas de uma tarefa) é essencial garantir o desempenho do aplicativo de lote. Adicione [o serviço do Azure lote de consulta com eficiência](batch-efficient-list-queries.md) à sua lista de leitura se você planeja fazem qualquer tipo de status monitoramento dentro dos seus aplicativos de lote.

2. **TaskStateMonitor**: [TaskStateMonitor] [ net_taskstatemonitor] fornece aplicativos do lote .NET com utilitários helper para monitorar estados de tarefa. Em `MonitorTasks`, *DotNetTutorial* aguarda para todas as tarefas alcançar [TaskState.Completed] [ net_taskstate] dentro de um limite de tempo. Em seguida, ele termina o trabalho.

3. **TerminateJobAsync**: encerrando um trabalho com [JobOperations.TerminateJobAsync] [ net_joboperations_terminatejob] (ou o bloqueio JobOperations.TerminateJob) marca trabalho como concluída. É essencial para fazê-lo a se a sua solução de lote usa um [JobReleaseTask][net_jobreltask]. Este é um tipo especial de tarefa, que é descrito na [preparação e conclusão de tarefas](batch-job-prep-release.md).

O `MonitorTasks` método de *DotNetTutorial* `Program.cs` aparece abaixo:

```csharp
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to  specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...",
        timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor
        = batchClient.Utilities.CreateTaskStateMonitor();

    try
    {
        await taskStateMonitor.WhenAll(tasks, TaskState.Completed, timeout);
    }
    catch (TimeoutException)
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);
        Console.WriteLine(failureMessage);
        return false;
    }

    await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

    // All tasks have reached the "Completed" state, however, this does not
    // guarantee all tasks completed successfully. Here we further check each task's
    // ExecutionInfo property to ensure that it did not encounter a scheduling error
    // or return a non-zero exit code.

    // Update the detail level to populate only the task id and executionInfo
    // properties. We refresh the tasks below, and need only this information for
    // each task.
    detail.SelectClause = "id, executionInfo";

    foreach (CloudTask task in tasks)
    {
        // Populate the task's properties with the latest info from the
        // Batch service
        await task.RefreshAsync(detail);

        if (task.ExecutionInformation.SchedulingError != null)
        {
            // A scheduling error indicates a problem starting the task on the node.
            // It is important to note that the task's state can be "Completed," yet
            // still have encountered a scheduling error.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] encountered a scheduling error: {1}",
                task.Id,
                task.ExecutionInformation.SchedulingError.Message);
        }
        else if (task.ExecutionInformation.ExitCode != 0)
        {
            // A non-zero exit code may indicate that the application executed by
            // the task encountered an error during execution. As not every
            // application returns non-zero on failure by default (e.g. robocopy),
            // your implementation of error checking may differ from this example.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## <a name="step-7-download-task-output"></a>Etapa 7: Baixar saída de tarefa

![Baixar saída tarefas de armazenamento][7]<br/>

Agora que o trabalho for concluído, a saída das tarefas pode ser baixada do armazenamento do Azure. Isso é feito com uma chamada para `DownloadBlobsFromContainerAsync` na *DotNetTutorial* `Program.cs`:

```csharp
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [AZURE.NOTE] A chamada para `DownloadBlobsFromContainerAsync` o *DotNetTutorial* aplicativo especifica que os arquivos devem ser baixados para sua `%TEMP%` pasta. Fique à vontade para modificar esse local de saída.

## <a name="step-8-delete-containers"></a>Etapa 8: Contêineres de excluir

Porque cobrado para dados que reside no armazenamento do Azure, sempre é uma boa ideia para remover qualquer bolhas que não são mais necessários para seus trabalhos em lotes. Do DotNetTutorial `Program.cs`, isso é feito com três chamadas para o método auxiliar `DeleteContainerAsync`:

```csharp
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

O próprio método simplesmente obtém uma referência para o contêiner e chama [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]:

```csharp
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Etapa 9: Excluir o trabalho e o pool

Na etapa final, o usuário será solicitado a excluir o trabalho e o pool que foram criados pelo aplicativo DotNetTutorial. Embora você não é cobrados para trabalhos e tarefas, *são* cobrados para nós de computação. Portanto, recomendamos que você alocar nós conforme necessário. Excluir pools não utilizados pode ser parte do processo de manutenção.

Do BatchClient [JobOperations] [ net_joboperations] e [PoolOperations] [ net_pooloperations] têm correspondente métodos de exclusão, que são chamados se o usuário confirmar exclusão:

```csharp
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [AZURE.IMPORTANT] Tenha em mente que cobrado para recursos de computação — excluir pools não utilizados minimizará custo. Além disso, esteja ciente de que a exclusão de um pool exclui todos os nós de computação dentro desse pool e que os dados em nós serão irrecuperáveis depois que o pool é excluído.

## <a name="run-the-dotnettutorial-sample"></a>Executar a amostra de *DotNetTutorial*

Quando você executa o aplicativo de exemplo, a saída do console será semelhante à seguinte. Durante a execução, você terá uma pausa em `Awaiting task completion, timeout in 00:30:00...` enquanto nós de computação do pool são iniciadas. Usar o [portal do Azure] [ azure_portal] para monitorar seu pool, calcular nós, trabalho e tarefas durante e após a execução. Usar o [portal do Azure] [ azure_portal] ou do [Gerenciador de armazenamento do Azure] [ storage_explorers] para exibir os recursos de armazenamento (contêineres e blobs) que são criados pelo aplicativo.

Tempo de execução típico é **aproximadamente 5 minutos** quando você executar o aplicativo em sua configuração padrão.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Próximas etapas

Fique à vontade para fazer alterações *DotNetTutorial* e *TaskApplication* para experimentar com cenários de computação diferentes. Por exemplo, tente adicionar um atraso de execução para *TaskApplication*, como com [Sleep][net_thread_sleep], para simular tarefas de longa execução e monitorá-los no portal. Tente adicionar mais tarefas ou ajustando o número de nós de computação. Adicionar lógica para verificar e permite o uso de um pool existente para acelerar o tempo de execução (*Dica*: fazer check-out `ArticleHelpers.cs` na [Microsoft.Azure.Batch.Samples.Common] [ github_samples_common] projeto nas [amostras de lote azure][github_samples]).

Agora que você estiver familiarizado com o fluxo de trabalho básico de uma solução de lote, é hora de se aprofundar nos recursos adicionais do serviço do lote.

- Leia a [Visão geral do recurso de lote para desenvolvedores](batch-api-basics.md), que é recomendável para todos os novos usuários de lote.
- Iniciar nos outros artigos de desenvolvimento do lote em **desenvolvimento profundamente** o [caminho de aprendizagem do lote][batch_learning_path].
- Fazer check-out de uma implementação diferente de processamento a carga de trabalho de "palavras primeiras N" usando o lote na [TopNWords] [ github_topnwords] amostra.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://docs.nuget.org/consume/installing-nuget
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Criar contêineres em armazenamento do Azure"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Carregar arquivos de entrada (dados) e aplicativo de tarefa para contêineres"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Criar pool de lote"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Criar trabalho em lotes"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Adicionar tarefas ao trabalho"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Monitorar tarefas"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Baixar saída tarefas de armazenamento"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Fluxo de trabalho do lote solução (diagrama completo)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Credenciais de lote no Portal"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Credenciais de armazenamento no Portal"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Fluxo de trabalho do lote solução (diagrama mínimo)"
