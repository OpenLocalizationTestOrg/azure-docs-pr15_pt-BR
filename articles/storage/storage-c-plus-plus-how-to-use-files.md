<properties
    pageTitle="Como usar o armazenamento de arquivos do C++ | Microsoft Azure"
    description="Armazenar dados de arquivo na nuvem com o armazenamento de arquivos do Azure."
    services="storage"
    documentationCenter=".net"
    authors="seguler"
    manager="jahogg"
    editor="tysonn" />

<tags ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="seguler" />

# <a name="how-to-use-file-storage-from-c"></a>Como usar o armazenamento de arquivos do C++

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]
[AZURE.INCLUDE [storage-file-overview-include](../../includes/storage-file-overview-include.md)]

## <a name="about-this-tutorial"></a>Sobre este tutorial

Neste tutorial, você aprenderá a executar operações básicas sobre o serviço de armazenamento de arquivo do Microsoft Azure. Por meio de exemplos escritos em C++, você aprenderá a criar compartilhamentos e diretórios, carregar, lista e excluir arquivos. Se você estiver começando a usar o serviço de armazenamento de arquivos do Microsoft Azure, percorrer os conceitos nas seções a seguir será muito útil compreender as amostras.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Criar um aplicativo de C++

Para criar os exemplos, você precisará instalar a biblioteca de cliente de armazenamento do Azure 2.4.0 para C++. Você também deve ter criado uma conta de armazenamento do Azure.

Para instalar o cliente de armazenamento do Azure 2.4.0 para C++, você pode usar um dos seguintes métodos:

-   **Linux:** Siga as instruções fornecidas na página da [Biblioteca de cliente de armazenamento do Azure para C++ Leiame](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .

-   **Windows:** No Visual Studio, clique em **ferramentas &gt; NuGet Package Manager &gt; Package Manager Console**. Digite o seguinte comando no [console de NuGet Package Manager](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e pressione **ENTER**.

    Pacote de instalação wastorage

## <a name="set-up-your-application-to-use-file-storage"></a>Configurar seu aplicativo para usar o armazenamento de arquivos

Adicione que o seguinte incluir instruções para a parte superior do arquivo C++ onde você deseja usar o armazenamento do Azure APIs para acessar arquivos:

    #include "was/storage_account.h"
    #include "was/file.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de caracteres de conexão de armazenamento do Azure

Para usar o armazenamento de arquivos, você precisa se conectar à sua conta de armazenamento do Azure. A primeira etapa seria configurar uma cadeia de conexão que usaremos para se conectar à sua conta de armazenamento. Vamos definir uma variável estática para fazê-lo.

    // Define the connection-string with your values.
    const utility::string_t 
    storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

## <a name="connecting-to-an-azure-storage-account"></a>Conectando a uma conta de armazenamento do Azure

Você pode usar a classe **cloud_storage_account** para representar suas informações de conta de armazenamento. Para recuperar informações de sua conta de armazenamento da cadeia de conexão de armazenamento, você pode usar o método de **Analisar** .

    // Retrieve storage account from connection string. 
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);

## <a name="how-to-create-a-share"></a>Como: criar um compartilhamento

Todos os arquivos e pastas em armazenamento de arquivo residem em um contêiner chamado de **compartilhamento**. Sua conta de armazenamento pode ter quantos compartilhamentos como permite que sua capacidade de conta. Para obter acesso a um compartilhamento e seu conteúdo, você precisa usar um cliente de armazenamento do arquivo.

    // Create the file storage client.
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();

Usando o cliente de armazenamento do arquivo, em seguida, você pode obter uma referência a um compartilhamento.

    // Get a reference to the file share
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));

Para criar o compartilhamento, use o método **create_if_not_exists** do objeto **cloud_file_share** .

    if (share.create_if_not_exists()) { 
        std::wcout << U("New share created") << std::endl;  
    }

Neste ponto, **compartilhar** armazena uma referência a um compartilhamento chamado **Meu compartilhamento de amostra**.

## <a name="how-to-upload-a-file"></a>Como: carregar um arquivo

No mínimo, um compartilhamento de armazenamento de arquivo do Azure contém um diretório raiz onde podem residem os arquivos. Nesta seção, você aprenderá como carregar um arquivo de armazenamento local para o diretório raiz de um compartilhamento.

A primeira etapa no carregamento de um arquivo é obter uma referência para o diretório onde ele deve residem. Você pode fazer isso chamando o método **get_root_directory_reference** do objeto compartilhado.

    //Get a reference to the root directory for the share.
    azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();

Agora que você tem uma referência para o diretório raiz do compartilhamento, você pode carregar um arquivo nele. Este exemplo carrega de um arquivo de texto e de um fluxo.

    // Upload a file from a stream.
    concurrency::streams::istream input_stream = 
      concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

    azure::storage::cloud_file file1 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
    file1.upload_from_stream(input_stream);

    // Upload some files from text.
    azure::storage::cloud_file file2 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
    file2.upload_text(_XPLATSTR("more text"));

    // Upload a file from a file.
    azure::storage::cloud_file file4 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    file4.upload_from_file(_XPLATSTR("DataFile.txt"));  

## <a name="how-to-create-a-directory"></a>Como: criar um diretório

Você também pode organizar armazenamento colocando arquivos dentro subdiretórios em vez de ter todos eles no diretório raiz. O serviço de armazenamento de arquivos Azure permite criar tantos diretórios quanto sua conta permitir. O código a seguir criará um diretório chamado **minha pasta de amostra** sob o diretório raiz bem como uma subpasta chamada **Meu subpasta de amostra**.
    
    // Retrieve a reference to a directory
    azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));
    
    // Return value is true if the share did not exist and was successfully created.
    directory.create_if_not_exists();
    
    // Create a subdirectory.
    azure::storage::cloud_file_directory subdirectory = 
      directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
    subdirectory.create_if_not_exists();

## <a name="how-to-list-files-and-directories-in-a-share"></a>Como: arquivos e pastas em um compartilhamento de lista

Obtendo uma lista de arquivos e pastas dentro de um compartilhamento é feito facilmente chamando **list_files_and_directories** em uma referência de **cloud_file_directory** . Para acessar o conjunto sofisticado de métodos e propriedades para um retornado **list_file_and_directory_item**, você deve chamar o método **list_file_and_directory_item.as_file** para obter um objeto de **cloud_file** ou o método **list_file_and_directory_item.as_directory** para obter um objeto **cloud_file_directory** .

O código a seguir demonstra como recuperar e o URI de cada item na pasta raiz do compartilhamento de saída.

    //Get a reference to the root directory for the share.
    azure::storage::cloud_file_directory root_dir = 
      share.get_root_directory_reference();
    
    // Output URI of each item.
    azure::storage::list_file_and_diretory_result_iterator end_of_results;
    
    for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
    {
        if(it->is_directory())
        {
            ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
        }
        else if (it->is_file())
        {
            ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
        }       
    }
    

## <a name="how-to-download-a-file"></a>Como: baixar um arquivo

Para baixar arquivos, primeiro recuperar uma referência de arquivo e, em seguida, chame o método de **download_to_stream** para transferir o conteúdo do arquivo para um objeto de fluxo que você pode manter para um arquivo local. Como alternativa, você pode usar o método **download_to_file** para baixar o conteúdo de um arquivo para um arquivo local. Você pode usar o método **download_text** para baixar o conteúdo de um arquivo como uma cadeia de texto.

O exemplo a seguir usa os métodos **download_to_stream** e **download_text** para demonstrar baixar os arquivos que foram criados nas seções anteriores.

    // Download as text
    azure::storage::cloud_file text_file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
    utility::string_t text = text_file.download_text();
    ucout << "File Text: " << text << std::endl;
    
    // Download as a stream.
    azure::storage::cloud_file stream_file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    
    concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
    concurrency::streams::ostream output_stream(buffer);
    stream_file.download_to_stream(output_stream);
    std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
    std::vector<unsigned char>& data = buffer.collection();
    outfile.write((char *)&data[0], buffer.size());
    outfile.close();

## <a name="how-to-delete-a-file"></a>Como: excluir um arquivo

Outra operação de armazenamento de arquivo comum é exclusão do arquivo. O código a seguir exclui um arquivo denominado meu-amostra-arquivo-3 armazenados sob o diretório raiz.

    // Get a reference to the root directory for the share. 
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    azure::storage::cloud_file_directory root_dir = 
      share.get_root_directory_reference();
    
    azure::storage::cloud_file file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    
    file.delete_file_if_exists();

## <a name="how-to-delete-a-directory"></a>Como: excluir um diretório

Exclusão de um diretório é uma tarefa simples, embora ele deve ser observado que você não pode excluir uma pasta que contém ainda arquivos ou outros diretórios.
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    // Get a reference to the directory.
    azure::storage::cloud_file_directory directory = 
      share.get_directory_reference(_XPLATSTR("my-sample-directory"));
    
    // Get a reference to the subdirectory you want to delete.
    azure::storage::cloud_file_directory sub_directory =
      directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
    
    // Delete the subdirectory and the sample directory.
    sub_directory.delete_directory_if_exists();
    
    directory.delete_directory_if_exists();

## <a name="how-to-delete-a-share"></a>Como: excluir um compartilhamento

Excluir um compartilhamento é feito chamando o método de **delete_if_exists** em um objeto de cloud_file_share. Aqui está o código de exemplo que faz isso.
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    // delete the share if exists
    share.delete_share_if_exists();

## <a name="set-the-maximum-size-for-a-file-share"></a>Definir o tamanho máximo de um compartilhamento de arquivo

Você pode definir a cota (ou o tamanho máximo) para um compartilhamento de arquivo, em gigabytes. Você também pode verificar a quantidade de dados atualmente está armazenado em compartilhar.

Definindo a cota de um compartilhamento, você pode limitar o tamanho total dos arquivos armazenados no compartilhamento. Se o tamanho total dos arquivos no compartilhamento exceder a cota definida no compartilhamento, em seguida, os clientes poderão aumentar o tamanho dos arquivos existentes ou criar novos arquivos, a menos que esses arquivos estão vazios.

O exemplo a seguir mostra como verificar o uso atual para um compartilhamento e como definir a cota para o compartilhamento.

    // Parse the connection string for the storage account.
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the file client.
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    if (share.exists())
    {
        std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();
    
        //This line sets the quota to 2560GB
        share.resize(2560);
    
        std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();
    
    }

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Gerar uma assinatura de acesso compartilhado para um arquivo ou compartilhamento de arquivos

Você pode gerar uma assinatura de acesso compartilhado (SAS) para um compartilhamento de arquivo ou para um arquivo individual. Você também pode criar uma política de acesso compartilhado em um compartilhamento de arquivo para gerenciar assinaturas de acesso compartilhado. Criar uma política de acesso compartilhado é recomendado, pois fornece um meio de revogar as associações de segurança, se ele deve ser comprometido.

O exemplo a seguir cria uma política de acesso compartilhado em um compartilhamento e, em seguida, utiliza essa política para fornecer as restrições uma Associações em um arquivo no compartilhamento de.

    // Parse the connection string for the storage account.
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the file client and get a reference to the share
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();
    
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    if (share.exists())
    {
        // Create and assign a policy
        utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

        azure::storage::file_shared_access_policy sharedPolicy = 
          azure::storage::file_shared_access_policy();

        //set permissions to expire in 90 minutes
        sharedPolicy.set_expiry(utility::datetime::utc_now() + 
          utility::datetime::from_minutes(90));

        //give read and write permissions
        sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

        //set permissions for the share
        azure::storage::file_share_permissions permissions; 

        //retrieve the current list of shared access policies
        azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;
        
        //add the new shared policy
        policies.insert(std::make_pair(policy_name, sharedPolicy));

        //save the updated policy list
        permissions.set_policies(policies);
        share.upload_permissions(permissions);

        //Retrieve the root directory and file references
        azure::storage::cloud_file_directory root_dir = 
          share.get_root_directory_reference();
        azure::storage::cloud_file file = 
          root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

        // Generate a SAS for a file in the share 
        //  and associate this access policy with it.       
        utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);
        
        // Create a new CloudFile object from the SAS, and write some text to the file.     
        azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
        utility::string_t text = _XPLATSTR("My sample content");        
        file_with_sas.upload_text(text);        
        
        //Download and print URL with SAS.
        utility::string_t downloaded_text = file_with_sas.download_text();      
        ucout << downloaded_text << std::endl;      
        ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;
    
    }

Para obter mais informações sobre como criar e usar assinaturas de acesso compartilhado, consulte [Assinaturas usando de acesso compartilhado (SAS)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o armazenamento do Azure, explore estes recursos:

-   [Biblioteca de cliente de armazenamento para C++](https://github.com/Azure/azure-storage-cpp)

-   [Explorador de armazenamento do Azure](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)

-   [Documentação de armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
