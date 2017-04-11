<properties
    pageTitle="Como usar o armazenamento de blob (armazenamento de objeto) do C++ | Microsoft Azure"
    description="Armazene dados não estruturados na nuvem com o armazenamento de Blob do Azure (armazenamento de objeto)."
    services="storage"
    documentationCenter=".net"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="how-to-use-blob-storage-from-c"></a>Como usar o armazenamento de Blob do C++  

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Visão geral

Armazenamento de Blob do Azure é um serviço que armazena os dados não estruturados na nuvem como objetos/blobs. Armazenamento de blob pode armazenar qualquer tipo de texto ou dados binários, como um documento, arquivo de mídia ou instalador do aplicativo. Armazenamento de blob também é conhecido como armazenamento do objeto.

Este guia irá demonstrar como executar cenários comuns usando o serviço de armazenamento de Blob do Azure. Os exemplos estão escritos em C++ e usam a [Biblioteca de cliente de armazenamento do Azure para C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Os cenários cobertos incluem **Carregando**, **Listando**, **baixando**e **Excluindo** blobs.  

>[AZURE.NOTE] Este guia se destina a biblioteca de cliente de armazenamento do Azure para C++ versão 1.0.0 e acima. A versão recomendada é a biblioteca de cliente do armazenamento 2.2.0, que está disponível por meio do [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp).

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Criar um aplicativo de C++
Neste guia, você usará os recursos de armazenamento que podem ser executados dentro de um aplicativo de C++.  

Para fazer isso, você precisará instalar a biblioteca de cliente de armazenamento do Azure para C++ e criar uma conta de armazenamento do Azure em sua assinatura do Azure.   

Para instalar a biblioteca de cliente de armazenamento do Azure para C++, você pode usar os seguintes métodos:

-   **Linux:** Siga as instruções fornecidas na página da [Biblioteca de cliente de armazenamento do Azure para C++ Leiame](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .  
-   **Windows:** No Visual Studio, clique em **Ferramentas > Gerenciador de pacotes do NuGet > Package Manager Console**. Digite o seguinte comando no [console de NuGet Package Manager](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e pressione **ENTER**.  

        Install-Package wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Configurar seu aplicativo para acessar o armazenamento de Blob  
Adicione que o seguinte incluir instruções para a parte superior do arquivo C++ onde você deseja usar o armazenamento do Azure APIs para acessar blobs:  

    #include "was/storage_account.h"
    #include "was/blob.h"

## <a name="setup-an-azure-storage-connection-string"></a>Configuração de uma cadeia de caracteres de conexão de armazenamento do Azure
Um cliente de armazenamento do Azure usa uma cadeia de conexão de armazenamento para armazenar pontos de extremidade e as credenciais para acessar os serviços de gerenciamento de dados. Quando em execução em um aplicativo cliente, você deve fornecer a cadeia de conexão de armazenamento no formato a seguir, usando o nome da sua conta de armazenamento e a tecla de acesso de armazenamento para a conta de armazenamento listada no [Portal do Azure](https://portal.azure.com) para os valores *AccountName* e *AccountKey* . Para obter informações sobre contas de armazenamento e as teclas de acesso, consulte [Sobre contas de armazenamento do Azure](storage-create-storage-account.md). Este exemplo mostra como você pode declarar um campo estático para armazenar a cadeia de conexão:  

    // Define the connection-string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Para testar seu aplicativo em seu computador local do Windows, você pode usar o Microsoft Azure [emulador de armazenamento](storage-use-emulator.md) que está instalado com o [SDK do Azure](https://azure.microsoft.com/downloads/). Emulador de armazenamento é um utilitário que simula os serviços de Blob, fila e tabela disponíveis no Azure em sua máquina de desenvolvimento local. O exemplo a seguir mostra como você pode declarar um campo estático para armazenar a cadeia de conexão para seu emulador de armazenamento local:

    // Define the connection-string with Azure Storage Emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Para iniciar o emulador de armazenamento do Azure, selecione o botão **Iniciar** ou pressione a tecla do **Windows** . Comece a digitar **Emulador de armazenamento do Azure**e selecione **Emulador de armazenamento do Microsoft Azure** na lista de aplicativos.  

Os exemplos a seguir presumem que você usou um destes dois métodos para obter a cadeia de conexão de armazenamento.  

## <a name="retrieve-your-connection-string"></a>Recuperar sua cadeia de caracteres de conexão
Você pode usar a classe **cloud_storage_account** para representar suas informações de conta de armazenamento. Para recuperar informações de sua conta de armazenamento da cadeia de conexão de armazenamento, você pode usar o método de **Analisar** .  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

Em seguida, obtenha uma referência a uma classe **cloud_blob_client** que permite a você recuperar objetos que representam contêineres e blobs armazenados dentro do serviço de armazenamento de Blob. O código a seguir cria um objeto de **cloud_blob_client** usando o objeto de conta de armazenamento que nós recuperados acima:  

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  

## <a name="how-to-create-a-container"></a>Como: criar um contêiner

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Este exemplo mostra como criar um contêiner, se ele ainda não existir:  

    try
    {
        // Retrieve storage account from connection string.
        azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

        // Create the blob client.
        azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

        // Retrieve a reference to a container.
        azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

        // Create the container if it doesn't already exist.
        container.create_if_not_exists();
    }
    catch (const std::exception& e)
    {
        std::wcout << U("Error: ") << e.what() << std::endl;
    }  

Por padrão, o novo contêiner é particular e especifique a chave de acesso de armazenamento para baixar blobs desse recipiente. Se você quiser disponibilizar os arquivos (blobs) dentro do contêiner para todos, você pode definir o contêiner pública usando o seguinte código:  

    // Make the blob container publicly accessible.
    azure::storage::blob_container_permissions permissions;
    permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
    container.upload_permissions(permissions);  

Qualquer pessoa na Internet pode ver blobs em um contêiner de público, mas você pode modificar ou excluí-las somente se você tiver a chave de acesso apropriado.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Como: carregar um blob em um contêiner
Armazenamento de Blob do Azure suporta blobs de bloco e blobs de página. Na maioria dos casos, o blob de bloco é o tipo recomendado para usar.  

Para carregar um arquivo para um blob de bloco, obtenha uma referência de contêiner e usá-lo para obter uma referência de blob de bloco. Quando você tiver uma referência de blob, você pode carregar qualquer fluxo de dados a ele chamando o método **upload_from_stream** . Essa operação criará o blob se anteriormente não existir ou substituí-lo, se ela existir. O exemplo a seguir mostra como carregar um blob em um contêiner e pressupõe que o contêiner já foi criado.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Create or overwrite the "my-blob-1" blob with contents from a local file.
    concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
    blockBlob.upload_from_stream(input_stream);
    input_stream.close().wait();

    // Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
    // Retrieve a reference to a blob named "my-blob-2".
    azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
    blob2.upload_text(U("more text"));

    // Retrieve a reference to a blob named "my-blob-3".
    azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
    blob3.upload_text(U("other text"));  

Como alternativa, você pode usar o método **upload_from_file** para carregar um arquivo em um blob de bloco.

## <a name="how-to-list-the-blobs-in-a-container"></a>Como: os blobs em um contêiner de lista
Para listar os blobs em um contêiner, primeiro obtenha uma referência de contêiner. Em seguida, você pode usar o método de **list_blobs** do contêiner para recuperar o blobs e/ou diretórios dentro dela. Para acessar o conjunto sofisticado de métodos e propriedades para um retornado **list_blob_item**, você deve chamar o método **list_blob_item.as_blob** para obter um objeto de **cloud_blob** ou o método **list_blob.as_directory** para obter um objeto cloud_blob_directory. O código a seguir demonstra como recuperar e o URI de cada item no contêiner de **Meu contêiner de amostra** de saída:

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Output URI of each item.
    azure::storage::list_blob_item_iterator end_of_results;
    for (auto it = container.list_blobs(); it != end_of_results; ++it)
    {
        if (it->is_blob())
        {
            std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
        }
        else
        {
            std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
        }
    }

Para obter mais detalhes sobre como listar operações, consulte [Recursos de armazenamento do Azure lista em C++](storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Como: baixar blobs
Para baixar blobs, primeiro recuperar uma referência de blob e então chamar o método **download_to_stream** . O exemplo a seguir usa o método **download_to_stream** para transferir o conteúdo blob a um objeto de fluxo que você pode manter para um arquivo local.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Save blob contents to a file.
    concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
    concurrency::streams::ostream output_stream(buffer);
    blockBlob.download_to_stream(output_stream);

    std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
    std::vector<unsigned char>& data = buffer.collection();

    outfile.write((char *)&data[0], buffer.size());
    outfile.close();  

Como alternativa, você pode usar o método **download_to_file** para baixar o conteúdo de um blob para um arquivo.
Além disso, você também pode usar o método **download_text** para baixar o conteúdo de um blob como uma cadeia de texto.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-2".
    azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

    // Download the contents of a blog as a text string.
    utility::string_t text = text_blob.download_text();

## <a name="how-to-delete-blobs"></a>Como: excluir blobs
Para excluir um blob, primeiro obter uma referência de blob e, em seguida, chamar o método **delete_blob** nele.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Delete the blob.
    blockBlob.delete_blob();

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu as Noções básicas do armazenamento de blob, siga estes links para saber mais sobre o armazenamento do Azure.  

-   [Como usar o armazenamento de fila do C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Como usar o armazenamento de tabela do C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Recursos de armazenamento do Azure de lista em C++](storage-c-plus-plus-enumeration.md)
-   [Biblioteca de cliente de armazenamento para referência de C++](http://azure.github.io/azure-storage-cpp)
-   [Documentação de armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md)
