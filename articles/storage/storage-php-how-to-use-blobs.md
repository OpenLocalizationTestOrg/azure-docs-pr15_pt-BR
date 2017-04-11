<properties
    pageTitle="Como usar o armazenamento de blob (armazenamento de objeto) do PHP | Microsoft Azure"
    description="Armazene dados não estruturados na nuvem com o armazenamento de Blob do Azure (armazenamento de objeto)."
    documentationCenter="php"
    services="storage"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="how-to-use-blob-storage-from-php"></a>Como usar o armazenamento de blob do PHP

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Visão geral

Armazenamento de Blob do Azure é um serviço que armazena os dados não estruturados na nuvem como objetos/blobs. Armazenamento de blob pode armazenar qualquer tipo de texto ou dados binários, como um documento, arquivo de mídia ou instalador do aplicativo. Armazenamento de blob também é conhecido como armazenamento do objeto.

Este guia mostra como executar cenários comuns usando o serviço de blob do Microsoft Azure. Os exemplos são gravados no PHP e usar o [SDK do Azure para PHP] [download]. Os cenários cobertos incluem **Carregando**, **Listando**, **baixando**e **Excluindo** blobs. Para obter mais informações sobre blobs, consulte a seção [próximas etapas](#next-steps) .

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Criar um aplicativo PHP

O único requisito para criar um aplicativo PHP que acessa o serviço de blob do Microsoft Azure é de referência de classes no SDK do Azure para PHP de dentro do seu código. Você pode usar as ferramentas de desenvolvimento para criar seu aplicativo, incluindo o bloco de notas.

Este guia, use os recursos de serviço, que podem ser chamados dentro de um aplicativo PHP localmente ou no código em execução dentro de uma função web Azure, uma função de trabalho ou um site.

## <a name="get-the-azure-client-libraries"></a>Obtenha as bibliotecas de cliente Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-blob-service"></a>Configurar seu aplicativo para acessar o serviço de blob

Para usar o serviço de blob do Microsoft Azure APIs, você precisa:

1. Fazer referência ao arquivo de carregador usando a instrução [require_once] e
2. Fazer referência a quaisquer classes que você pode usar.

O exemplo a seguir mostra como incluir o arquivo de carregador e fazer referência à classe **ServicesBuilder** .

> [AZURE.NOTE] Este exemplo (e outros exemplos neste artigo) pressupõem que você instalou as bibliotecas de cliente do PHP para Azure via compositor. Se você instalou as bibliotecas manualmente, você precisará fazer referência a `WindowsAzure.php` arquivo de carregador automático.

    require_once 'vendor/autoload.php';
    use WindowsAzure\Common\ServicesBuilder;


Nos exemplos a seguir, o `require_once` instrução será mostrada sempre, mas somente as classes necessárias para executar o exemplo referenciadas.

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma conexão de armazenamento do Azure

Para criar uma instância de um cliente de serviço do Azure blob, primeiro você deve ter uma cadeia de conexão válida. O formato para a cadeia de conexão de serviço do blob é:

Para acessar um serviço live:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Para acessar o emulador de armazenamento:

    UseDevelopmentStorage=true


Para criar qualquer cliente de serviço Azure, você precisa usar a classe **ServicesBuilder** . É possível:

* Passar a cadeia de conexão diretamente para ele ou
* Use o **CloudConfigurationManager (CCM)** para verificar várias origens externas para a cadeia de conexão:
    * Por padrão, ele vem com suporte para uma fonte externa - variáveis de ambiente.
    * Você pode adicionar novas fontes estendendo a classe **ConnectionStringSource** .

Para os exemplos descritos aqui, a cadeia de conexão será passada diretamente.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## <a name="create-a-container"></a>Criar um contêiner

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Um objeto de **BlobRestProxy** permite que você crie um contêiner de blob com o método **createContainer** . Ao criar um contêiner, você pode definir opções no contêiner, mas fazer isso não é necessário. (O exemplo a seguir mostra como definir a lista de controle de acesso do contêiner (ACL) e metadados de contêiner.)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Blob\Models\CreateContainerOptions;
    use MicrosoftAzure\Storage\Blob\Models\PublicAccessType;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    // OPTIONAL: Set public access policy and metadata.
    // Create container options object.
    $createContainerOptions = new CreateContainerOptions();

    // Set public access policy. Possible values are
    // PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
    // CONTAINER_AND_BLOBS:
    // Specifies full public read access for container and blob data.
    // proxys can enumerate blobs within the container via anonymous
    // request, but cannot enumerate containers within the storage account.
    //
    // BLOBS_ONLY:
    // Specifies public read access for blobs. Blob data within this
    // container can be read via anonymous request, but container data is not
    // available. proxys cannot enumerate blobs within the container via
    // anonymous request.
    // If this value is not specified in the request, container data is
    // private to the account owner.
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    try {
        // Create container.
        $blobRestProxy->createContainer("mycontainer", $createContainerOptions);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Chamando **setPublicAccess (PublicAccessType::CONTAINER\_AND\_BLOBS)** torna os contêiner e blob dados acessível via solicitações anônimas. Chamar **setPublicAccess(PublicAccessType::BLOBS_ONLY)** torna somente os dados de blob acessível via solicitações anônimas. Para obter mais informações sobre o contêiner ACLs, consulte [Definir contêiner ACL (API REST)][container-acl].

Para obter mais informações sobre códigos de erro de serviço do Blob, consulte [Códigos de erro de serviço do Blob][error-codes].

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob em um contêiner

Para carregar um arquivo como um blob, use o método **BlobRestProxy -> createBlockBlob** . Esta operação cria o blob se ele não existe, ou substitui-lo em caso afirmativo. O exemplo de código a seguir pressupõe que o contêiner já foi criado e usa [fopen] [ fopen] para abrir o arquivo como um fluxo.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    $content = fopen("c:\myfile.txt", "r");
    $blob_name = "myblob";

    try {
        //Upload blob
        $blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Observe que o exemplo anterior carrega um blob como um fluxo. No entanto, um blob pode também ser carregado como uma cadeia de caracteres usando, por exemplo, o [arquivo\_obter\_conteúdo] [ file_get_contents] função. Para fazer isso usando o exemplo anterior, altere `$content = fopen("c:\myfile.txt", "r");` para `$content = file_get_contents("c:\myfile.txt");`.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Para listar os blobs em um contêiner, use o método **BlobRestProxy -> listBlobs** com um **foreach** loop para repetir o resultado. O código a seguir exibe o nome de cada blob como saída em um contêiner e exibe seu URI para o navegador.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // List blobs.
        $blob_list = $blobRestProxy->listBlobs("mycontainer");
        $blobs = $blob_list->getBlobs();

        foreach($blobs as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }


## <a name="download-a-blob"></a>Baixe um blob

Para baixar um blob, chame o método **BlobRestProxy -> getBlob** , em seguida, chame o método **getContentStream** no objeto **GetBlobResult** resultante.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Get blob.
        $blob = $blobRestProxy->getBlob("mycontainer", "myblob");
        fpassthru($blob->getContentStream());
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Observe que o exemplo acima obtém um blob como um recurso de fluxo (o comportamento padrão). Entretanto, você pode usar o [fluxo\_obter\_conteúdo] [ stream-get-contents] função converter fluxo retornado em uma cadeia de caracteres.

## <a name="delete-a-blob"></a>Excluir um blob

Para excluir um blob, passe o nome do contêiner e blob para **BlobRestProxy -> deleteBlob**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete blob.
        $blobRestProxy->deleteBlob("mycontainer", "myblob");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="delete-a-blob-container"></a>Excluir um contêiner de blob

Finalmente, para excluir um contêiner de blob, passe o nome de contêiner para **BlobRestProxy -> deleteContainer**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete container.
        $blobRestProxy->deleteContainer("mycontainer");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu as Noções básicas do serviço do Azure blob, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

- Visite o [blog da equipe de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Consulte o [exemplo de blob do bloco PHP](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php).
- Consulte o [exemplo de blob de página PHP](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php).
- [Transferir dados com o utilitário de AzCopy de linha de comando](storage-use-azcopy.md)
 
Para obter mais informações, consulte também o [PHP Developer Center](/develop/php/).


[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents
