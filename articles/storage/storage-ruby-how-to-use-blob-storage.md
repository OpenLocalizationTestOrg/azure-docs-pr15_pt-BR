<properties
    pageTitle="Como usar o armazenamento de Blob (armazenamento de objeto) do Ruby | Microsoft Azure"
    description="Armazene dados não estruturados na nuvem com o armazenamento de Blob do Azure (armazenamento de objeto)."
    services="storage"
    documentationCenter="ruby"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>


# <a name="how-to-use-blob-storage-from-ruby"></a>Como usar o armazenamento de Blob do Ruby

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Visão geral

Armazenamento de Blob do Azure é um serviço que armazena os dados não estruturados na nuvem como objetos/blobs. Armazenamento de blob pode armazenar qualquer tipo de texto ou dados binários, como um documento, arquivo de mídia ou instalador do aplicativo. Armazenamento de blob também é conhecido como armazenamento do objeto.

Este guia mostrará como executar cenários comuns usando o armazenamento de Blob. Os exemplos são gravados usando a API de Ruby. Os cenários cobertos incluem blobs **Carregando, listando, baixando** e **Excluindo** .

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Criar um aplicativo Ruby

Crie um aplicativo Ruby. Para obter instruções, consulte [Ruby no aplicativo da Web de Rails em uma máquina virtual do Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)

## <a name="configure-your-application-to-access-storage"></a>Configurar seu aplicativo para acessar o armazenamento

Para usar o armazenamento do Azure, você precisa baixar e usar o pacote de azure Ruby, que inclui um conjunto de bibliotecas de conveniência que se comunicar com os serviços de armazenamento restante.

### <a name="use-rubygems-to-obtain-the-package"></a>Use RubyGems para obter o pacote

1. Use uma interface de linha de comando como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).

2. Digite "azure de instalação de gem" na janela de comando para instalar o gem e as dependências.

### <a name="import-the-package"></a>Importar o pacote

Usando o editor de texto favorito, adicione o seguinte à parte superior do arquivo Ruby onde você pretende usar o armazenamento:

    require "azure"

## <a name="setup-an-azure-storage-connection"></a>Configurar uma Conexão de armazenamento do Azure

O módulo azure lerá as variáveis de ambiente **AZURE\_armazenamento\_conta** e **AZURE\_armazenamento\_ACCESS_KEY** para informações necessárias para conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, você deve especificar as informações da conta antes de usar **Azure::Blob::BlobService** com o seguinte código:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"


Para obter esses valores de um clássico ou conta de armazenamento do Gerenciador de recursos no portal do Azure:

1. Faça logon no [portal do Azure](https://portal.azure.com).
2. Navegue até a conta de armazenamento que você deseja usar.
3. Na lâmina configurações à direita, clique em **Teclas de acesso**.
4. A lâmina de teclas de acesso que aparece, você verá a tecla de acesso 1 e 2 de tecla de acesso. Você pode usar qualquer um dos dois. 
5. Clique no ícone de cópia para copiar a chave para a área de transferência. 

Para obter esses valores de uma conta de armazenamento clássico no portal do Azure clássico:

1. Faça logon no [portal do Azure clássico](https://manage.windowsazure.com).
2. Navegue até a conta de armazenamento que você deseja usar.
3. Clique em **Gerenciar teclas de acesso** na parte inferior do painel de navegação.
4. Na caixa de diálogo pop, você verá o nome da conta de armazenamento, a chave primária de acesso e a chave de acesso secundário. Para chave de acesso, você pode usar um principal ou o secundário. 
5. Clique no ícone de cópia para copiar a chave para a área de transferência.

## <a name="create-a-container"></a>Criar um contêiner

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

O objeto **Azure::Blob::BlobService** permite que você trabalhar com contêineres e blobs. Para criar um contêiner, use o **criar\_container()** método.

O exemplo de código a seguir cria um contêiner ou imprima o erro se houver alguma.

    azure_blob_service = Azure::Blob::BlobService.new
    begin
      container = azure_blob_service.create_container("test-container")
    rescue
      puts $!
    end

Se você quiser fazer os arquivos no contêiner de público, você pode definir permissões do contêiner.

Você pode modificar apenas o <strong>criar\_container()</strong> chamada para passar o **: público\_acesso\_nível** opção:

    container = azure_blob_service.create_container("test-container",
      :public_access_level => "<public access level>")


Os valores válidos para o **: público\_acesso\_nível** opção são:

* **blob:** Especifica público completa acesso de leitura para dados de contêiner e blob. Clientes podem enumerar blobs dentro do contêiner via solicitação anônima, mas não é possível enumerar contêineres dentro da conta de armazenamento.

* **contêiner:** Especifica o público acesso de leitura para blobs. Dados de blob dentro deste recipiente podem ser lidos por meio de solicitação anônima, mas dados do contêiner não estão disponíveis. Clientes não é possível enumerar blobs dentro do contêiner via solicitação anônima.

Como alternativa, você pode modificar o nível de acesso público de um contêiner usando **definir\_contêiner\_acl()** método para especificar o nível de acesso público.

O exemplo de código a seguir altera o nível de acesso público ao **contêiner**:

    azure_blob_service.set_container_acl('test-container', "container")

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob em um contêiner

Para carregar o conteúdo em um blob, use o **criar\_bloco\_blob()** método para criar o blob, use um arquivo ou sequência como o conteúdo do blob.

O código a seguir carrega o arquivo **test.png** como um novo blob denominado "imagem-blob" no contêiner.

    content = File.open("test.png", "rb") { |file| file.read }
    blob = azure_blob_service.create_block_blob(container.name,
      "image-blob", content)
    puts blob.name

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Para listar os contêineres, use o método de **list_containers()** .
Para listar os blobs dentro de um contêiner, use **lista\_blobs()** método.

Isso produz as urls de todos os blobs em todos os contêineres para a conta.

    containers = azure_blob_service.list_containers()
    containers.each do |container|
      blobs = azure_blob_service.list_blobs(container.name)
      blobs.each do |blob|
        puts blob.name
      end
    end

## <a name="download-blobs"></a>Baixar blobs

Para baixar blobs, use o **obter\_blob()** método para recuperar o conteúdo.

O exemplo de código a seguir demonstra usando **obter\_blob()** para baixar o conteúdo de "imagem-blob" e gravar para um arquivo local.

    blob, content = azure_blob_service.get_blob(container.name,"image-blob")
    File.open("download.png","wb") {|f| f.write(content)}

## <a name="delete-a-blob"></a>Excluir um Blob
Finalmente, para excluir um blob, use o **Excluir\_blob()** método. O exemplo de código a seguir demonstra como excluir um blob.

    azure_blob_service.delete_blob(container.name, "image-blob")

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre tarefas mais complexas de armazenamento, siga estes links:

- [Blog da equipe de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Repositório de [SDK do Azure para Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) no GitHub
- [Transferir dados com o utilitário de AzCopy de linha de comando](storage-use-azcopy.md)
