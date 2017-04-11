<properties
    pageTitle="Como usar o armazenamento de arquivos de Java | Microsoft Azure"
    description="Aprenda a usar o serviço do Azure arquivo para carregar, baixar, lista e excluir arquivos. Amostras escritas em Java."
    services="storage"
    documentationCenter="java"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn" />

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-file-storage-from-java"></a>Como usar o armazenamento de arquivos de Java

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>Visão geral

Neste guia você aprenderá como realizar operações básicas sobre o serviço de armazenamento de arquivo do Microsoft Azure. Por meio de exemplos escritos em Java, você aprenderá a criar compartilhamentos e diretórios, carregar, lista e excluir arquivos. Se você estiver começando a usar o serviço de armazenamento de arquivos do Microsoft Azure, percorrer os conceitos nas seções a seguir será muito útil compreender as amostras.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Criar um aplicativo Java

Para criar os exemplos, você precisará Java Development Kit (JDK) e a [] [SDK do Azure armazenamento para Java]. Você também deve ter criado uma conta de armazenamento do Azure.

## <a name="setup-your-application-to-use-file-storage"></a>Configurar seu aplicativo para usar o armazenamento de arquivos

Para usar o armazenamento do Azure APIs, adicione a seguinte instrução na parte superior do arquivo Java onde você pretende acessar o serviço de armazenamento do.

    // Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.file.*;

## <a name="setup-an-azure-storage-connection-string"></a>Configuração de uma cadeia de caracteres de conexão de armazenamento do Azure

Para usar o armazenamento de arquivos, você precisa se conectar à sua conta de armazenamento do Azure. A primeira etapa seria configurar uma cadeia de conexão que usaremos para se conectar à sua conta de armazenamento. Vamos definir uma variável estática para fazê-lo.

    // Configure the connection-string with your values
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account_name;" +
        "AccountKey=your_storage_account_key";

> [AZURE.NOTE] Substitua your_storage_account_name e your_storage_account_key com os valores reais para a sua conta de armazenamento.

## <a name="connecting-to-an-azure-storage-account"></a>Conectando a uma conta de armazenamento do Azure

Para se conectar à sua conta de armazenamento, você precisa usar o objeto **CloudStorageAccount** , passando uma cadeia de conexão para seu método de **Analisar** .

    // Use the CloudStorageAccount object to connect to your storage account
    try {
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
    } catch (InvalidKeyException invalidKey) {
        // Handle the exception
    }

**CloudStorageAccount.parse** gera um InvalidKeyException, portanto você precisará colocá-lo dentro de um bloco try/catch.

## <a name="how-to-create-a-share"></a>Como: criar um compartilhamento

Todos os arquivos e pastas em armazenamento de arquivo residem em um contêiner chamado de **compartilhamento**. Sua conta de armazenamento pode ter tantos compartilhamentos como permite que sua capacidade de conta. Para obter acesso a um compartilhamento e seu conteúdo, você precisa usar um cliente de armazenamento do arquivo.

    // Create the file storage client.
    CloudFileClient fileClient = storageAccount.createCloudFileClient();

Usando o cliente de armazenamento do arquivo, em seguida, você pode obter uma referência a um compartilhamento.

    // Get a reference to the file share
    CloudFileShare share = fileClient.getShareReference("sampleshare");

Para realmente criar o compartilhamento, use o método **createIfNotExists** do objeto CloudFileShare.

    if (share.createIfNotExists()) {
        System.out.println("New share created");
    }

Neste ponto, **compartilhar** armazena uma referência a um compartilhamento chamado **sampleshare**.

## <a name="how-to-upload-a-file"></a>Como: carregar um arquivo

Um compartilhamento de armazenamento do Azure arquivo contém pelo menos, um diretório raiz onde podem residem os arquivos. Nesta seção, você aprenderá como carregar um arquivo de armazenamento local para o diretório raiz de um compartilhamento.

A primeira etapa no carregamento de um arquivo é obter uma referência para o diretório onde ele deve residem. Você pode fazer isso chamando o método **getRootDirectoryReference** do objeto compartilhado.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

Agora que você tem uma referência para o diretório raiz do compartilhamento, você pode carregar um arquivo nele usando o seguinte código.

    // Define the path to a local file.
    final String filePath = "C:\\temp\\Readme.txt";

    CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
    cloudFile.uploadFromFile(filePath);

## <a name="how-to-create-a-directory"></a>Como: criar um diretório

Você também pode organizar armazenamento colocando arquivos dentro subdiretórios em vez de ter todos eles no diretório raiz. O serviço de armazenamento de arquivos Azure permite criar tantos diretórios quanto sua conta permitir. O código a seguir irá criar uma subpasta chamada **sampledir** sob o diretório raiz.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    //Get a reference to the sampledir directory
    CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

    if (sampleDir.createIfNotExists()) {
        System.out.println("sampledir created");
    } else {
        System.out.println("sampledir already exists");
    }

## <a name="how-to-list-files-and-directories-in-a-share"></a>Como: arquivos e pastas em um compartilhamento de lista

Obtendo uma lista de arquivos e pastas dentro de um compartilhamento é feito facilmente chamando **listFilesAndDirectories** em uma referência de CloudFileDirectory. O método retorna uma lista de objetos ListFileItem que você pode iteramos sobre. Como exemplo, o código a seguir lista arquivos e pastas dentro do diretório raiz.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
        System.out.println(fileItem.getUri());
    }


## <a name="how-to-download-a-file"></a>Como: baixar um arquivo

Uma das operações mais frequentes que você executará contra o armazenamento de arquivos é baixar arquivos. No exemplo a seguir, o código downloads SampleFile.txt e exibe seu conteúdo.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    //Get a reference to the directory that contains the file
    CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

    //Get a reference to the file you want to download
    CloudFile file = sampleDir.getFileReference("SampleFile.txt");

    //Write the contents of the file to the console.
    System.out.println(file.downloadText());

## <a name="how-to-delete-a-file"></a>Como: excluir um arquivo

Outra operação de armazenamento de arquivo comum é exclusão do arquivo. O código a seguir exclui um arquivo denominado SampleFile.txt armazenados dentro de um diretório chamado **sampledir**.


    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    // Get a reference to the directory where the file to be deleted is in
    CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

    String filename = "SampleFile.txt"
    CloudFile file;

    file = containerDir.getFileReference(filename)
    if ( file.deleteIfExists() ) {
        System.out.println(filename + " was deleted");
    }


## <a name="how-to-delete-a-directory"></a>Como: excluir um diretório

Exclusão de um diretório é uma tarefa relativamente simple, embora ele deve ser observado que você não pode excluir uma pasta que contém ainda arquivos ou outros diretórios.

    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    // Get a reference to the directory you want to delete
    CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

    // Delete the directory
    if ( containerDir.deleteIfExists() ) {
        System.out.println("Directory deleted");
    }


## <a name="how-to-delete-a-share"></a>Como: excluir um compartilhamento

Excluir um compartilhamento é feito chamando o método de **deleteIfExists** em um objeto de CloudFileShare. Aqui está o código de exemplo que faz isso.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the file client.
       CloudFileClient fileClient = storageAccount.createCloudFileClient();

       // Get a reference to the file share
       CloudFileShare share = fileClient.getShareReference("sampleshare");

       if (share.deleteIfExists()) {
           System.out.println("sampleshare deleted");
       }
    } catch (Exception e) {
        e.printStackTrace();
    }

## <a name="next-steps"></a>Próximas etapas

Se você gostaria de saber mais sobre outros APIs de armazenamento Azure, siga esses links.

- [Central de desenvolvedores de Java](http://azure.microsoft.com/develop/java/)
- [Armazenamento do Azure SDK para Java](https://github.com/azure/azure-storage-java)
- [Armazenamento do Azure SDK para Android](https://github.com/azure/azure-storage-android)
- [Referência SDK do cliente de armazenamento do Azure](http://dl.windowsazure.com/storage/javadoc/)
- [Serviços de armazenamento do Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog da equipe de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- [Transferir dados com o utilitário de AzCopy de linha de comando](storage-use-azcopy.md)
