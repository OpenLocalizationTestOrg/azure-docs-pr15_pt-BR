<properties
    pageTitle="Como usar o armazenamento de tabela do Azure do Ruby | Microsoft Azure"
    description="Armazene dados estruturados na nuvem usando o armazenamento de tabela do Azure, um armazenamento de dados NoSQL."
    services="storage"
    documentationCenter="ruby"
    authors="tamram"
    manager="carmonm"
    editor=""/>
<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-azure-table-storage-from-ruby"></a>Como usar o armazenamento de tabela do Azure do Ruby

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Visão geral

Este guia mostra como executar cenários comuns usando o serviço de tabela do Azure. Os exemplos são gravados usando a API de Ruby. Os cenários cobertos incluem **Criando e excluindo uma tabela, inserindo e consultar entidades em uma tabela**.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Criar um aplicativo Ruby

Para obter instruções sobre como criar um aplicativo Ruby, consulte [Ruby no aplicativo da Web de Rails em uma máquina virtual do Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).


## <a name="configure-your-application-to-access-storage"></a>Configurar seu aplicativo para acessar o armazenamento

Para usar o armazenamento do Azure, você precisa baixar e usar o pacote de azure Ruby que inclui um conjunto de bibliotecas de conveniência que se comunicar com os serviços de armazenamento restante.

### <a name="use-rubygems-to-obtain-the-package"></a>Use RubyGems para obter o pacote

1. Use uma interface de linha de comando como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).

2. Digite **gem instalar azure** na janela de comando para instalar o gem e as dependências.

### <a name="import-the-package"></a>Importar o pacote

Usar o editor de texto favorito, adicione o seguinte à parte superior do arquivo Ruby onde você pretende usar o armazenamento:

    require "azure"

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma conexão de armazenamento do Azure

O módulo azure lerá as variáveis de ambiente **AZURE\_armazenamento\_conta** e **AZURE\_armazenamento\_acesso\_chave** para informações necessárias para conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, você deve especificar as informações da conta antes de usar **Azure::TableService** com o seguinte código:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

Para obter esses valores de um clássico ou conta de armazenamento do Gerenciador de recursos no Portal do Azure:

1. Faça logon [Portal do Azure](https://portal.azure.com).
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

## <a name="create-a-table"></a>Criar uma tabela

O objeto **Azure::TableService** permite que você trabalhar com tabelas e entidades. Para criar uma tabela, use o **criar\_table()** método. O exemplo a seguir cria uma tabela ou imprima o erro se houver alguma.

    azure_table_service = Azure::TableService.new
    begin
      azure_table_service.create_table("testtable")
    rescue
      puts $!
    end

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade, primeiro crie um objeto de hash que define as propriedades de entidade. Observe que, para cada entidade que você deverá especificar um **PartitionKey** e **RowKey**. Estes são os identificadores exclusivos de suas entidades e valores que podem ser consultados mais rápidas que as outras propriedades. Armazenamento do Azure usa **PartitionKey** para distribuir automaticamente entidades da tabela sobre muitos de nós de armazenamento. Entidades com a mesma **PartitionKey** são armazenadas no mesmo nó. A **RowKey** é a ID exclusiva da entidade dentro da partição pertence.

    entity = { "content" => "test entity",
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.insert_entity("testtable", entity)

## <a name="update-an-entity"></a>Atualizar uma entidade

Há vários métodos disponíveis para atualizar uma entidade existente:

* **atualizar\_entity():** Atualize uma entidade existente, substituindo-lo.
* **direta\_entity():** Atualiza uma entidade existente mesclando novos valores de propriedade para a entidade existente.
* **Inserir\_ou\_direta\_entity():** Atualiza uma entidade existente, substituindo. Se nenhuma entidade existir, um novo será inserido:
* **Inserir\_ou\_substituir\_entity():** Atualiza uma entidade existente mesclando novos valores de propriedade para a entidade existente. Se nenhuma entidade existir, um novo será inserido.

O exemplo a seguir demonstra atualizando uma entidade usando **atualizar\_entity()**:

    entity = { "content" => "test entity with updated content",
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.update_entity("testtable", entity)

Com **atualizar\_entity()** e **direta\_entity()**, se a entidade que você está atualizando não existir, em seguida, a operação de atualização falhará. Portanto, se você quiser armazenar uma entidade, independentemente se ele já existe, você deve usar **Inserir\_ou\_substituir\_entity()** ou **Inserir\_ou\_direta\_entity()**.

## <a name="work-with-groups-of-entities"></a>Trabalhar com grupos de entidades

Às vezes, faz sentido para submeter várias operações juntos em um lote para garantir atômica processamento pelo servidor. Para fazer isso, primeiro crie um objeto de **lote** e use o **executar\_batch()** método em **TableService**. O exemplo a seguir demonstra enviando duas entidades com RowKey 2 e 3 em um lote. Observe que ela só funciona para entidades com o mesmo PartitionKey.

    azure_table_service = Azure::TableService.new
    batch = Azure::Storage::Table::Batch.new("testtable",
      "test-partition-key") do
      insert "2", { "content" => "new content 2" }
      insert "3", { "content" => "new content 3" }
    end
    results = azure_table_service.execute_batch(batch)

## <a name="query-for-an-entity"></a>Consulta para uma entidade

Para consultar uma entidade em uma tabela, use o **obter\_entity()** método, passando o nome de tabela, **PartitionKey** e **RowKey**.

    result = azure_table_service.get_entity("testtable", "test-partition-key",
      "1")

## <a name="query-a-set-of-entities"></a>Consulta de um conjunto de entidades

Para consultar um conjunto de entidades em uma tabela, crie um objeto de hash de consulta e use o **consulta\_entities()** método. O exemplo a seguir demonstra obtendo todas as entidades com a mesma **PartitionKey**:

    query = { :filter => "PartitionKey eq 'test-partition-key'" }
    result, token = azure_table_service.query_entities("testtable", query)

> [AZURE.NOTE] Se o conjunto de resultados é muito grande para uma única consulta retornar, um token de continuação será retornado que você pode usar para recuperar páginas subsequentes.

## <a name="query-a-subset-of-entity-properties"></a>Um subconjunto das propriedades de entidade da consulta

Uma consulta a uma tabela pode recuperar apenas algumas propriedades de uma entidade. Essa técnica, chamada de "projeção", reduz a largura de banda e pode melhorar o desempenho de consulta, especialmente para entidades grandes. Use a cláusula select e passar os nomes das propriedades que você gostaria de recuperar dados para o cliente.

    query = { :filter => "PartitionKey eq 'test-partition-key'",
      :select => ["content"] }
    result, token = azure_table_service.query_entities("testtable", query)

## <a name="delete-an-entity"></a>Excluir uma entidade

Para excluir uma entidade, use o **Excluir\_entity()** método. Você precisa passar na nome a tabela que contém a entidade, o PartitionKey e RowKey da entidade.

        azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## <a name="delete-a-table"></a>Excluir uma tabela

Para excluir uma tabela, use o **Excluir\_table()** método e passar na nome de tabela que você deseja excluir.

        azure_table_service.delete_table("testtable")

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre tarefas mais complexas de armazenamento, siga estes links:

- [Blog da equipe de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Repositório de [SDK do Azure para Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) no GitHub
