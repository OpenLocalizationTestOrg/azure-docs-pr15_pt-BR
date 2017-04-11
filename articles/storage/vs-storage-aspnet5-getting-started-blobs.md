<properties
    pageTitle="Introdução ao blob storage e Visual Studio conectados serviços (ASP.NET 5) | Microsoft Azure"
    description="Como começar a usar o armazenamento de Blob do Azure em um projeto do Visual Studio ASP.NET 5 após ter criado uma conta de armazenamento usando os serviços do Visual Studio conectado"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-5"></a>Introdução ao Azure Blob storage e Visual Studio conectados serviços (ASP.NET 5)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

##<a name="overview"></a>Visão geral

Este artigo descreve como começar a usar o armazenamento de Blob do Azure no Visual Studio depois que você tenha criado ou referenciado uma conta de armazenamento do Azure em um projeto do ASP.NET 5 usando a caixa de diálogo do Visual Studio adicionar conectado serviços.

Armazenamento de Blob do Azure é um serviço para armazenar grandes quantidades de dados não estruturados que podem ser acessadas a partir de qualquer lugar do mundo via HTTP ou HTTPS. Um único blob pode ser de qualquer tamanho. BLOBs podem ser coisas como imagens, arquivos de áudio e vídeo, dados processados e arquivos de documentos. Este artigo descreve como começar a usar o armazenamento de blob depois de criar uma conta de armazenamento do Azure usando a caixa de diálogo **Adicionar serviços conectados** de Visual Studio em um projeto do ASP.NET 5.

Assim como arquivos live em pastas, blobs de armazenamento ao vivo em contêineres. Após ter criado um armazenamento, você criar um ou mais contêineres no armazenamento. Por exemplo, em um armazenamento chamado "Scrapbook", você pode criar contêineres no armazenamento chamado "imagens" para armazenar imagens e outro chamado "áudio" para armazenar arquivos de áudio. Depois de criar os contêineres, você pode carregar arquivos de blob individuais para eles. Consulte [Introdução ao armazenamento de Blob do Azure usando .NET](storage-dotnet-how-to-use-blobs.md) para saber mais sobre manipular programaticamente blobs.

##<a name="access-blob-containers-in-code"></a>Contêineres de blob do Access no código

Para acessar programaticamente blobs em projetos ASP.NET 5, você precisa adicionar os itens a seguir, se elas não já está presentes.

1. Adicione as seguintes declarações de namespace de código na parte superior de qualquer arquivo c# na qual você deseja acessar programaticamente o armazenamento do Azure.

        using Microsoft.Extensions.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Extensions.Logging.LogLevel;

2. Obtenha um objeto de **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a sua cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **Observação:** Use todo o código acima frente ao código nas seções a seguir.


3. Use um objeto de **CloudBlobClient** para obter uma referência de **CloudBlobContainer** a um contêiner existente na sua conta de armazenamento.

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");



##<a name="create-a-container-in-code"></a>Criar um contêiner no código

Você também pode usar o **CloudBlobClient** para criar um contêiner na sua conta de armazenamento. Tudo o que você precisa fazer é adicionar uma chamada para **CreateIfNotExistsAsync** como o seguinte código:

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “my-new-container.”
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


**Observação:** As APIs que executam chamadas para o armazenamento do Azure no ASP.NET 5 são assíncronas. Para obter mais informações, consulte [programação assíncrona com assíncrono e espera](http://msdn.microsoft.com/library/hh191443.aspx) . O código abaixo presume métodos de programação assíncrono estão sendo usados.

Para disponibilizar os arquivos dentro do contêiner para todos, você pode definir o contêiner para ser público usando o código a seguir.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

##<a name="upload-a-blob-into-a-container"></a>Carregar um blob em um contêiner

Para carregar um arquivo blob em um contêiner, obtenha uma referência de contêiner e usá-lo para obter uma referência de blob. Depois de ter uma referência de blob, você pode carregar qualquer fluxo de dados a ele chamando o método **UploadFromStreamAsync** . Esta operação cria o blob se ele não ainda estiver lá, ou substitui-la, se ela existir. O exemplo a seguir mostra como carregar um blob em um contêiner e pressupõe que o contêiner já foi criado.

    // Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

##<a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner
Para listar os blobs em um contêiner, primeiro obtenha uma referência de contêiner. Você pode chamar método de **ListBlobsSegmentedAsync** do contêiner para recuperar o blobs e/ou diretórios dentro dela. Para acessar o conjunto sofisticado de métodos e propriedades para um retornado **IListBlobItem**, você deve convertê-lo a um objeto **CloudBlockBlob**, **CloudPageBlob**ou **CloudBlobDirectory** . Se você não souber o tipo de blob, você pode usar uma verificação de tipo para determinar qual convertê-la para. O código a seguir demonstra como recuperar e o URI de cada item em um contêiner de saída.

    BlobContinuationToken token = null;
    do
    {
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
        token = resultSegment.ContinuationToken;

        foreach (IListBlobItem item in resultSegment.Results)
        {
            if (item.GetType() == typeof(CloudBlockBlob))
            {
                CloudBlockBlob blob = (CloudBlockBlob)item;
                Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
            }

            else if (item.GetType() == typeof(CloudPageBlob))
            {
                CloudPageBlob pageBlob = (CloudPageBlob)item;

                Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
            }

            else if (item.GetType() == typeof(CloudBlobDirectory))
            {
                CloudBlobDirectory directory = (CloudBlobDirectory)item;

                Console.WriteLine("Directory: {0}", directory.Uri);
            }
        }
    } while (token != null);

Existem-outras maneiras de listar o conteúdo de um contêiner de blob. Para obter mais informações, consulte [Introdução ao armazenamento de Blob do Azure usando .NET](storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container) .

##<a name="download-a-blob"></a>Baixe um blob
Para baixar um blob, primeiro obtenha uma referência para o blob e então chamar o método **DownloadToStreamAsync** . O exemplo a seguir usa o método **DownloadToStreamAsync** para transferir o conteúdo blob a um objeto de fluxo que você pode salvar como um arquivo local.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named “myfile”.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        await blockBlob.DownloadToStreamAsync(fileStream);
    }

Existem outras maneiras de salvar blobs como arquivos. Para obter mais informações, consulte [Introdução ao armazenamento de Blob do Azure usando .NET](storage-dotnet-how-to-use-blobs.md#download-blobs) .

##<a name="delete-a-blob"></a>Excluir um blob
Para excluir um blob, primeiro obtenha uma referência para o blob e então chamar o método de **DeleteAsync** nele.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    await blockBlob.DeleteAsync();

## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
