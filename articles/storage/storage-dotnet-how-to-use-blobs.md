<properties
    pageTitle="Introdução ao armazenamento de Blob do Azure (armazenamento de objeto) usando .NET | Microsoft Azure"
    description="Armazene dados não estruturados na nuvem com o armazenamento de Blob do Azure (armazenamento de objeto)."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="get-started-with-azure-blob-storage-using-net"></a>Introdução ao armazenamento de Blob do Azure usando .NET

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Visão geral

Armazenamento de Blob do Azure é um serviço que armazena os dados não estruturados na nuvem como objetos/blobs. Armazenamento de blob pode armazenar qualquer tipo de texto ou dados binários, como um documento, arquivo de mídia ou instalador do aplicativo. Armazenamento de blob também é conhecido como armazenamento do objeto.

### <a name="about-this-tutorial"></a>Sobre este tutorial

Este tutorial mostra como escrever código .NET para alguns cenários comuns usando o armazenamento de Blob do Azure. Cenários cobertos incluem carregando, listando, baixando e excluindo blobs.

**Tempo estimado para concluir:** 45 minutos

**Prerequisities:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Biblioteca de cliente de armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Gerenciador de configuração do Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Uma [conta de armazenamento do Azure](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Mais exemplos

Para usar o armazenamento de Blob de exemplos adicionais, consulte [Introdução ao armazenamento de Blob do Azure no .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/). Você pode baixar o aplicativo de amostra e executá-lo ou procure o código no GitHub.


[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Adicionar declarações de namespace

Adicione o seguinte `using` declarações na parte superior da `program.cs` arquivo:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types

### <a name="parse-the-connection-string"></a>Analisará a cadeia de conexão

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-blob-service-client"></a>Crie o cliente de serviço de Blob

A classe **CloudBlobClient** permite que você recupere contêineres e blobs armazenados em armazenamento de Blob. Aqui está uma maneira de criar o cliente de serviço:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

Agora você está pronto para escrever código que lê e grava dados ao armazenamento de Blob.

## <a name="create-a-container"></a>Criar um contêiner

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Este exemplo mostra como criar um contêiner, se ele ainda não existir:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

Por padrão, o novo contêiner é particular, indicando que você deve especificar a chave de acesso de armazenamento para baixar blobs desse recipiente. Se você quiser disponibilizar os arquivos dentro do contêiner para todos, você pode definir o contêiner pública usando o seguinte código:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

Qualquer pessoa na Internet pode ver blobs em um contêiner de público, mas você pode modificar ou excluí-las somente se você tiver a tecla de acesso de conta apropriada ou uma assinatura de acesso compartilhado.

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob em um contêiner

Armazenamento de Blob do Azure suporta blobs de bloco e blobs de página.  Na maioria dos casos, o blob de bloco é o tipo recomendado para usar.

Para carregar um arquivo para um blob de bloco, obtenha uma referência de contêiner e usá-lo para obter uma referência de blob de bloco. Quando você tiver uma referência de blob, você pode carregar qualquer fluxo de dados a ele chamando o método **UploadFromStream** . Essa operação criará o blob se anteriormente não existir ou substituí-lo, se ela existir.

O exemplo a seguir mostra como carregar um blob em um contêiner e pressupõe que o contêiner já foi criado.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Para listar os blobs em um contêiner, primeiro obtenha uma referência de contêiner. Em seguida, você pode usar o método de **ListBlobs** do contêiner para recuperar o blobs e/ou diretórios dentro dela. Para acessar o conjunto sofisticado de métodos e propriedades para um retornado **IListBlobItem**, você deve convertê-lo a um objeto **CloudBlockBlob**, **CloudPageBlob**ou **CloudBlobDirectory** .  Se o tipo for desconhecido, você pode usar uma verificação de tipo para determinar qual convertê-la para.  O seguinte código demonstra como recuperar e o URI de cada item de saída a `photos` contêiner:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

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

Como mostrado acima, você pode nomear blobs com informações de caminho em seus nomes. Isso cria uma estrutura de diretório virtual que você pode organizar e desviar como faria com um sistema de arquivos tradicional. Observe que a estrutura de diretório só é virtual - os somente os recursos disponíveis no armazenamento de Blob são contêineres e blobs. No entanto, a biblioteca de cliente do armazenamento oferece um objeto de **CloudBlobDirectory** para se referir a um diretório virtual e simplificar o processo de trabalhar com bolhas que são organizadas dessa maneira.

Por exemplo, considere o seguinte conjunto de blobs bloco em um contêiner chamado `photos`:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Quando você chamar **ListBlobs** no contêiner 'fotos' (como em exemplo acima), uma listagem hierárquica é retornada. Ele contém objetos **CloudBlobDirectory** e **CloudBlockBlob** , representando os diretórios e blobs no contêiner, respectivamente. A saída resultante aparência:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Opcionalmente, você pode definir o parâmetro **UseFlatBlobListing** do método **ListBlobs** como **true**. Nesse caso, cada blob no contêiner é retornado como um objeto de **CloudBlockBlob** . A chamada para **ListBlobs** para retornar uma listagem plana tem esta aparência:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

e os resultados ter esta aparência:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## <a name="download-blobs"></a>Baixar blobs

Para baixar blobs, primeiro recuperar uma referência de blob e então chamar o método **DownloadToStream** . O exemplo a seguir usa o método **DownloadToStream** para transferir o conteúdo blob a um objeto de fluxo que você pode manter para um arquivo local.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Você também pode usar o método **DownloadToStream** para baixar o conteúdo de um blob como uma cadeia de texto.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Excluir blobs

Para excluir um blob, primeiro obter uma referência de blob e, em seguida, chame o método **Delete** nele.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Listar blobs em páginas assíncrona

Se estiver listando um grande número de blobs ou que você deseja controlar o número de resultados que retornar em uma operação de listagem, você pode listar blobs nas páginas de resultados. Este exemplo mostra como retornar resultados em páginas de forma assíncrona, para que a execução não está bloqueada aguardando para retornar um grande conjunto de resultados.

Este exemplo mostra um blob plano listando, mas você também pode executar uma listagem hierárquica, definindo a `useFlatBlobListing` parâmetro do método **ListBlobsSegmentedAsync** para `false`.

Porque o método de exemplo chama um método assíncrono, ela deve ser precedida com a `async` palavra-chave e ele devem retornar um objeto de **tarefa** . A palavra-chave espera especificada para o método de **ListBlobsSegmentedAsync** suspende a execução do método amostra até que a tarefa de listagem seja concluída.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
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

## <a name="writing-to-an-append-blob"></a>Gravar um blob de acréscimo

Um blob de acréscimo é um novo tipo de blob, introduzido com versão 5. x da biblioteca de cliente de armazenamento do Azure para .NET. Um blob de acréscimo é otimizado para operações de acréscimo, como log. Como um blob de bloco, um blob de acréscimo é composto dos blocos, mas quando você adiciona um novo bloco para um blob de acréscimo, sempre é acrescentado ao final do blob. Você não pode atualizar ou excluir um bloco existente em um blob de acréscimo. O bloco de IDs para um blob de acréscimo não estão expostos como elas são para um blob de bloco.

Cada bloco em um blob de acréscimo pode ser um tamanho diferente, até no máximo 4 MB, e um blob de acréscimo pode incluir um máximo de 50.000 blocos. O tamanho máximo de um blob de acréscimo, portanto, é um pouco mais de 195 GB (4 MB X 50.000 blocos).

O exemplo a seguir cria um novo blob de acréscimo e acrescenta alguns dados, simular uma operação de log simples.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

    //Create the container if it does not already exist.
    container.CreateIfNotExists();

    //Get a reference to an append blob.
    CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

    //Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
    //You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
    appendBlob.CreateOrReplace();

    int numBlocks = 10;

    //Generate an array of random bytes.
    Random rnd = new Random();
    byte[] bytes = new byte[numBlocks];
    rnd.NextBytes(bytes);

    //Simulate a logging operation by writing text data and byte data to the end of the append blob.
    for (int i = 0; i < numBlocks; i++)
    {
        appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
            DateTime.UtcNow, bytes[i], Environment.NewLine));
    }

    //Read the append blob to the console window.
    Console.WriteLine(appendBlob.DownloadText());

Consulte [Compreendendo bloco Blobs, Blobs de página e acrescentar Blobs](https://msdn.microsoft.com/library/azure/ee691964.aspx) para obter mais informações sobre as diferenças entre os três tipos de blobs.

## <a name="managing-security-for-blobs"></a>Gerenciar a segurança de blobs

Por padrão, o armazenamento do Azure mantém seus dados seguros limitando o acesso ao proprietário da conta, que está em posse das teclas de acesso de conta. Quando você precisa compartilhar dados blob em sua conta de armazenamento, é importante para fazê-lo sem comprometer a segurança de suas chaves de acesso de conta. Além disso, você pode criptografar dados de blob para garantir que ele é seguro indo durante a transmissão e o armazenamento do Azure.

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### <a name="controlling-access-to-blob-data"></a>Controlar o acesso aos dados blob

Por padrão, os dados de blob em sua conta de armazenamento são acessíveis somente para o proprietário da conta de armazenamento. Autenticação de solicitações contra armazenamento de Blob requer a tecla de acesso de conta por padrão. No entanto, você talvez queira disponibilizar determinados dados blob para outros usuários. Você tem duas opções:

- **Acesso anônimo:** Você pode disponibilizar um contêiner ou seus blobs publicamente para acesso anônimo. Consulte [Gerenciar acesso anônimo de leitura para contêineres e blobs](storage-manage-access-to-resources.md) para obter mais informações.
- **Shared access assinaturas:** Você pode fornecer clientes com uma assinatura de acesso compartilhado (SAS), que fornece acesso delegado a um recurso em sua conta de armazenamento, com permissões especificadas e durante um intervalo que você especificar. Consulte [Usando compartilhados acesso assinaturas (SAS)](storage-dotnet-shared-access-signature-part-1.md) para obter mais informações.

### <a name="encrypting-blob-data"></a>Criptografar dados blob

Armazenamento do Azure oferece suporte para criptografia de dados de blob no cliente e no servidor:

- **Criptografia do lado do cliente:** A biblioteca de cliente de armazenamento para .NET dá suporte à criptografia de dados em aplicativos cliente antes de carregar ao armazenamento do Azure e descriptografar dados durante o download do cliente. A biblioteca também oferece suporte a integração com o Azure chave cofre para gerenciamento de chaves de conta de armazenamento. Consulte [Criptografia do lado do cliente com .NET para armazenamento do Microsoft Azure](storage-client-side-encryption.md) para obter mais informações. Consulte também [Tutorial: criptografar e descriptografar blobs no armazenamento do Microsoft Azure usando o Azure chave cofre](storage-encrypt-decrypt-blobs-key-vault.md).
- **Criptografia de servidor**: armazenamento Azure agora dá suporte à criptografia de servidor. Consulte [criptografia de serviço de armazenamento do Azure para dados inativos (visualização)](storage-service-encryption.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu as Noções básicas do armazenamento de Blob, siga estes links para saber mais.

### <a name="microsoft-azure-storage-explorer"></a>Gerenciador de armazenamento do Microsoft Azure
- [Microsoft Azure armazenamento Explorer (MASE)](../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo gratuito, da Microsoft que permite que você trabalhe visualmente com dados de armazenamento do Azure no Windows, OS X e Linux.

### <a name="blob-storage-samples"></a>Exemplos de armazenamento de blob

- [Introdução ao armazenamento de blob do Microsoft Azure no .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### <a name="blob-storage-reference"></a>Referência de armazenamento de blob

- [Biblioteca de cliente de armazenamento para referência .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
- [Referência de API REST](http://msdn.microsoft.com/library/azure/dd179355)

### <a name="conceptual-guides"></a>Guias de conceituais

- [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md)
- [Introdução ao armazenamento de arquivos para .NET](storage-dotnet-how-to-use-files.md)
- [Como usar o armazenamento de blob do Microsoft Azure com o SDK do WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355
