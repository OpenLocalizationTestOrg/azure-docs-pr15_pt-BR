<properties
    pageTitle="Introdução ao blob storage e Visual Studio conectados serviços (serviços de nuvem) | Microsoft Azure"
    description="Como começar a usar o armazenamento de Blob do Azure em um projeto de serviço de nuvem no Visual Studio depois de conectar a uma conta de armazenamento usando o Visual Studio conectado serviços"
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

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introdução ao armazenamento de Blob do Azure e Visual Studio conectado serviços (projetos de serviços de nuvem)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Visão geral

Este artigo descreve como começar a usar o armazenamento de Blob do Azure depois que você criou ou referenciado uma conta de armazenamento do Azure usando a caixa de diálogo **Adicionar serviços conectados** de Visual Studio em um projeto de serviços de nuvem do Visual Studio. Mostraremos como acessar e criar contêineres de blob e como executar tarefas comuns como carregar, listando e baixando blobs. Os exemplos são escritos em C\# e usar a [Biblioteca do cliente de armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Armazenamento de Blob do Azure é um serviço para armazenar grandes quantidades de dados não estruturados que podem ser acessadas a partir de qualquer lugar do mundo via HTTP ou HTTPS. Um único blob pode ser de qualquer tamanho. BLOBs podem ser coisas como imagens, arquivos de áudio e vídeo, dados processados e arquivos de documentos.

Assim como arquivos live em pastas, blobs de armazenamento ao vivo em contêineres. Após ter criado um armazenamento, você criar um ou mais contêineres no armazenamento. Por exemplo, em um armazenamento chamado "Scrapbook", você pode criar contêineres no armazenamento chamado "imagens" para armazenar imagens e outro chamado "áudio" para armazenar arquivos de áudio. Depois de criar os contêineres, você pode carregar arquivos de blob individuais para eles.

- Para obter mais informações sobre manipular programaticamente blobs, consulte [Introdução ao armazenamento de Blob do Azure usando .NET](storage-dotnet-how-to-use-blobs.md).
- Para obter informações gerais sobre o armazenamento do Azure, consulte a [documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/).
- Para obter informações gerais sobre os serviços de nuvem do Azure, consulte a [documentação de serviços de nuvem](https://azure.microsoft.com/documentation/services/cloud-services/).
- Para obter mais informações sobre a programação de aplicativos ASP.NET, consulte [ASP.NET](http://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Contêineres de blob do Access no código

Para acessar programaticamente blobs em projetos de serviço de nuvem, você precisa adicionar os itens a seguir, se elas não já está presentes.

1. Adicione as seguintes declarações de namespace de código na parte superior de qualquer arquivo c# na qual você deseja acessar programaticamente o armazenamento do Azure.

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Obtenha um objeto de **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a sua cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço Azure.

        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

3. Obtenha um objeto de **CloudBlobClient** para fazer referência a um contêiner existente na sua conta de armazenamento.

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

4. Obtenha um objeto de **CloudBlobContainer** para fazer referência a um contêiner de blob específico.

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [AZURE.NOTE] Use todo o código mostrado no procedimento anterior frente ao código mostrado nas seções a seguir.

## <a name="create-a-container-in-code"></a>Criar um contêiner no código

> [AZURE.NOTE] Algumas APIs que executam chamadas check-out para o armazenamento do Azure no ASP.NET são assíncronas. Para obter mais informações, consulte [programação assíncrona com assíncrono e espera](http://msdn.microsoft.com/library/hh191443.aspx) . O código no exemplo a seguir pressupõe que você está usando métodos de programação assíncrono.

Para criar um contêiner em sua conta de armazenamento, tudo o que você precisa fazer é adicionar uma chamada para **CreateIfNotExistsAsync** como o seguinte código:

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


Para disponibilizar os arquivos dentro do contêiner para todos, você pode definir o contêiner para ser público usando o código a seguir.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Qualquer pessoa na Internet pode ver blobs em um contêiner de público, mas você pode modificar ou excluí-las somente se você tiver a chave de acesso apropriado.

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob em um contêiner

Armazenamento do Azure suporta blobs de bloco e blobs de página. Na maioria dos casos, o blob de bloco é o tipo recomendado para usar.

Para carregar um arquivo para um blob de bloco, obtenha uma referência de contêiner e usá-lo para obter uma referência de blob de bloco. Quando você tiver uma referência de blob, você pode carregar qualquer fluxo de dados a ele chamando o método **UploadFromStream** . Esta operação cria o blob se anteriormente não existe ou substitui-la, se ela existir. O exemplo a seguir mostra como carregar um blob em um contêiner e pressupõe que o contêiner já foi criado.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Para listar os blobs em um contêiner, primeiro obtenha uma referência de contêiner. Em seguida, você pode usar o método de **ListBlobs** do contêiner para recuperar o blobs e/ou diretórios dentro dela. Para acessar o conjunto sofisticado de métodos e propriedades para um retornado **IListBlobItem**, você deve convertê-lo a um objeto **CloudBlockBlob**, **CloudPageBlob**ou **CloudBlobDirectory** . Se o tipo for desconhecido, você pode usar uma verificação de tipo para determinar qual convertê-la para. O código a seguir demonstra como recuperar e o URI de cada item no contêiner de **fotos** de saída:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
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

Como mostrado no exemplo de código anterior, o serviço de blob tem o conceito de diretórios dentro de contêineres, também. Isso é para que você pode organizar seus blobs em uma estrutura mais semelhante de pasta. Por exemplo, considere o seguinte conjunto de blobs bloco em um contêiner chamado **fotos**:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Quando você chamar **ListBlobs** no contêiner (como o exemplo anterior), a coleção retornada contém objetos de **CloudBlobDirectory** e **CloudBlockBlob** que representa os diretórios e blobs contidos no nível superior. Aqui está a saída resultante:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Opcionalmente, você pode definir o parâmetro **UseFlatBlobListing** do método **ListBlobs** como **true**. Isso resulta em cada blob sendo retornada como um **CloudBlockBlob**, independentemente de diretório. Aqui está a chamada para **ListBlobs**:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

e aqui estão os resultados:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Para obter mais informações, consulte [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx).

## <a name="download-blobs"></a>Baixar blobs

Para baixar blobs, primeiro recuperar uma referência de blob e então chamar o método **DownloadToStream** . O exemplo a seguir usa o método **DownloadToStream** para transferir o conteúdo blob a um objeto de fluxo que você pode manter para um arquivo local.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Você também pode usar o método **DownloadToStream** para baixar o conteúdo de um blob como uma cadeia de texto.

    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Excluir blobs

Para excluir um blob, primeiro obter uma referência de blob e, em seguida, chame o método **Delete** .

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Listar blobs em páginas assíncrona

Se estiver listando um grande número de blobs ou que você deseja controlar o número de resultados que retornar em uma operação de listagem, você pode listar blobs nas páginas de resultados. Este exemplo mostra como retornar resultados em páginas de forma assíncrona, para que a execução não está bloqueada aguardando para retornar um grande conjunto de resultados.

Este exemplo mostra um blob plano listando, mas você também pode executar uma listagem hierárquica, definindo o parâmetro **useFlatBlobListing** do método **ListBlobsSegmentedAsync** como **false**.

Como o método de exemplo chama um método assíncrono, ela deve ser precedida com a palavra-chave **assíncrono** e ele deve retornar um objeto de **tarefa** . A palavra-chave espera especificada para o método de **ListBlobsSegmentedAsync** suspende a execução do método amostra até que a tarefa de listagem seja concluída.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        // List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        // When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            // or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
