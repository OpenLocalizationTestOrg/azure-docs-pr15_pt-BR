<properties
    pageTitle="Como usar o armazenamento de tabela (C++) | Microsoft Azure"
    description="Armazene dados estruturados na nuvem usando o armazenamento de tabela do Azure, um armazenamento de dados NoSQL."
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

# <a name="how-to-use-table-storage-from-c"></a>Como usar o armazenamento de tabela do C++

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Visão geral  
Este guia mostrará como executar cenários comuns usando o serviço de armazenamento de tabela do Azure. Os exemplos estão escritos em C++ e usam a [Biblioteca de cliente de armazenamento do Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Os cenários cobertos incluem **Criando e excluindo uma tabela** e **como trabalhar com entidades de tabela**.

>[AZURE.NOTE] Este guia se destina a biblioteca de cliente de armazenamento do Azure para C++ versão 1.0.0 e acima. A versão recomendada é a biblioteca de cliente do armazenamento 2.2.0, que está disponível por meio do [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## <a name="create-a-c-application"></a>Criar um aplicativo de C++  
Neste guia, você usará os recursos de armazenamento que podem ser executados dentro de um aplicativo de C++. Para fazer isso, você precisará instalar a biblioteca de cliente de armazenamento do Azure para C++ e criar uma conta de armazenamento do Azure em sua assinatura do Azure.  

Para instalar a biblioteca de cliente de armazenamento do Azure para C++, você pode usar os seguintes métodos:

-   **Linux:** Siga as instruções fornecidas na página de [Biblioteca de cliente de armazenamento do Azure para C++ Leiame](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .  
-   **Windows:** No Visual Studio, clique em **Ferramentas > Gerenciador de pacotes do NuGet > Package Manager Console**. Digite o seguinte comando no [console de NuGet Package Manager](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e pressione Enter.  

        Install-Package wastorage

## <a name="configure-your-application-to-access-table-storage"></a>Configurar seu aplicativo para acessar o armazenamento de tabela  
Adicione que o seguinte incluir instruções para a parte superior do arquivo C++ onde você deseja usar o armazenamento do Azure APIs para acessar tabelas:  

    #include "was/storage_account.h"
    #include "was/table.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de caracteres de conexão de armazenamento do Azure  
Um cliente de armazenamento do Azure usa uma cadeia de conexão de armazenamento para armazenar pontos de extremidade e as credenciais para acessar os serviços de gerenciamento de dados. Durante a execução de um aplicativo cliente, você deve fornecer a cadeia de conexão de armazenamento no formato a seguir. Use o nome da sua conta de armazenamento e a tecla de acesso de armazenamento para a conta de armazenamento listada no [Portal do Azure](https://portal.azure.com) para os valores *AccountName* e *AccountKey* . Para obter informações sobre contas de armazenamento e as teclas de acesso, consulte [contas de armazenamento do Azure sobre](storage-create-storage-account.md). Este exemplo mostra como você pode declarar um campo estático para armazenar a cadeia de conexão:  

    // Define the connection string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Para testar seu aplicativo em seu computador local baseado no Windows, você pode usar o Azure [emulador de armazenamento](storage-use-emulator.md) que está instalado com o [SDK do Azure](https://azure.microsoft.com/downloads/). Emulador de armazenamento é um utilitário que simula os serviços do Azure Blob, fila e tabela disponíveis em sua máquina de desenvolvimento local. O exemplo a seguir mostra como você pode declarar um campo estático para armazenar a cadeia de conexão para seu emulador de armazenamento local:  

    // Define the connection string with Azure storage emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Para iniciar o emulador de armazenamento do Azure, clique no botão **Iniciar** ou pressione a tecla do Windows. Comece a digitar **Emulador de armazenamento do Azure**e selecione **Emulador de armazenamento do Microsoft Azure** na lista de aplicativos.  

Os exemplos a seguir presumem que você usou um destes dois métodos para obter a cadeia de conexão de armazenamento.  

## <a name="retrieve-your-connection-string"></a>Recuperar sua cadeia de caracteres de conexão  
Você pode usar a classe **cloud_storage_account** para representar suas informações de conta de armazenamento. Para recuperar informações de sua conta de armazenamento da cadeia de conexão de armazenamento, você pode usar o método de análise.

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

Em seguida, obtenha uma referência a uma classe **cloud_table_client** , como permite que você obtenha objetos de referência para tabelas e entidades armazenadas dentro do serviço de armazenamento de tabela. O código a seguir cria um objeto **cloud_table_client** usando o objeto de conta de armazenamento que nós recuperados acima:  

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

## <a name="create-a-table"></a>Criar uma tabela
Um objeto de **cloud_table_client** permite que você obtenha objetos de referência para tabelas e entidades. O código a seguir cria um objeto de **cloud_table_client** e usa para criar uma nova tabela.

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Retrieve a reference to a table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table if it doesn't exist.
    table.create_if_not_exists();  

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
Para adicionar uma entidade a uma tabela, crie um novo objeto de **table_entity** e passe para **table_operation::insert_entity**. O código a seguir usa o nome do cliente como a chave de linha e o sobrenome como a chave de partição. Juntos, partição da entidade e a chave de linha identificam a entidade na tabela. Entidades com a mesma chave de partição podem ser consultadas mais rápido do que aqueles com chaves de partição diferentes, mas usando as teclas de partição diverso permite maior escalabilidade de operação em paralelo. Para obter mais informações, consulte a [lista de verificação de escalabilidade e desempenho de armazenamento do Microsoft Azure](storage-performance-checklist.md).

O código a seguir cria uma nova instância do **table_entity** com alguns dados de cliente sejam armazenados. O código próximo chama **table_operation::insert_entity** para criar um objeto de **table_operation** para inserir uma entidade em uma tabela e associa a nova entidade de tabela com ele. Finalmente, o código chama o método execute no objeto **cloud_table** . E o novo **table_operation** envia uma solicitação para o serviço de tabela para inserir a nova entidade de cliente na tabela de "pessoas".  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Retrieve a reference to a table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table if it doesn't exist.
    table.create_if_not_exists();

    // Create a new customer entity.
    azure::storage::table_entity customer1(U("Harp"), U("Walter"));

    azure::storage::table_entity::properties_type& properties = customer1.properties();
    properties.reserve(2);
    properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

    properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

    // Create the table operation that inserts the customer entity.
    azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

    // Execute the insert operation.
    azure::storage::table_result insert_result = table.execute(insert_operation);

## <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades
Você pode inserir um lote de entidades para o serviço de tabela em uma operação de gravação. O código a seguir cria um objeto de **table_batch_operation** e adiciona que três Inserir operações-lo. Cada operação de inserção é adicionada criando um novo objeto de entidade, definindo seus valores e, em seguida, chamar o método insert no objeto **table_batch_operation** para associar a entidade uma nova operação de inserir. Em seguida, **cloud_table.execute** é chamado para executar a operação.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Define a batch operation.
    azure::storage::table_batch_operation batch_operation;

    // Create a customer entity and add it to the table.
    azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

    azure::storage::table_entity::properties_type& properties1 = customer1.properties();
    properties1.reserve(2);
    properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
    properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

    // Create another customer entity and add it to the table.
    azure::storage::table_entity customer2(U("Smith"), U("Ben"));

    azure::storage::table_entity::properties_type& properties2 = customer2.properties();
    properties2.reserve(2);
    properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
    properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

    // Create a third customer entity to add to the table.
    azure::storage::table_entity customer3(U("Smith"), U("Denise"));

    azure::storage::table_entity::properties_type& properties3 = customer3.properties();
    properties3.reserve(2);
    properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
    properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

    // Add customer entities to the batch insert operation.
    batch_operation.insert_or_replace_entity(customer1);
    batch_operation.insert_or_replace_entity(customer2);
    batch_operation.insert_or_replace_entity(customer3);

    // Execute the batch operation.
    std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);

Algumas informações sobre as operações de lote:  

-   Você pode executar até 100 inserir, excluir, mala direta, substituir, inserir-ou-mesclar e inserir ou substituir as operações em qualquer combinação em um único lote.  
-   Uma operação em lotes pode ter uma operação de recuperar, se for a única operação no lote.  
-   Todas as entidades em uma operação de único lote devem ter a mesma chave de partição.  
-   Uma operação de lote está limitada a uma carga de dados de 4 MB.  

## <a name="retrieve-all-entities-in-a-partition"></a>Recuperar todas as entidades em uma partição
Para consultar uma tabela para todas as entidades em uma partição, use um objeto de **table_query** . O exemplo de código a seguir especifica um filtro para entidades onde 'Smith' é a chave da partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta no console.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    azure::storage::table_query query;

    query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Print the fields for each customer.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        const azure::storage::table_entity::properties_type& properties = it->properties();

        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
            << U(", Property1: ") << properties.at(U("Email")).string_value()
            << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
    }  

A consulta neste exemplo traz todas as entidades que correspondem aos critérios de filtro. Se você tiver tabelas grandes e precisa baixar as entidades de tabela com frequência, recomendamos que você armazenar seus dados em bolhas de armazenamento do Azure em vez disso.

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Recuperar um intervalo de entidades em uma partição
Se você não quiser todas as entidades em uma partição de consulta, você pode especificar um intervalo combinando o filtro de chave de partição com um filtro de chave de linha. O exemplo de código a seguir usa dois filtros para obter todas as entidades na partição 'Smith' onde a chave de linha (nome) começa com uma letra anteriores ao 'E' do alfabeto e imprime os resultados da consulta.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table query.
    azure::storage::table_query query;

    query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
        azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
        azure::storage::query_comparison_operator::equal, U("Smith")),
        azure::storage::query_logical_operator::op_and,
        azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Loop through the results, displaying information about the entity.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        const azure::storage::table_entity::properties_type& properties = it->properties();

        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
            << U(", Property1: ") << properties.at(U("Email")).string_value()
            << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
    }  

## <a name="retrieve-a-single-entity"></a>Recuperar uma única entidade
Você pode escrever uma consulta para recuperar uma entidade única e específica. O código a seguir usa **table_operation::retrieve_entity** para especificar o cliente 'Jeff Smith'. Esse método retorna apenas uma entidade, em vez de um conjunto e o valor retornado está em **table_result**. Especificando teclas partição e de linha em uma consulta é a maneira mais rápida de recuperar uma única entidade do serviço de tabela.  

    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Output the entity.
    azure::storage::table_entity entity = retrieve_result.entity();
    const azure::storage::table_entity::properties_type& properties = entity.properties();

    std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;

## <a name="replace-an-entity"></a>Substituir uma entidade
Para substituir uma entidade, recuperá-la a partir do serviço de tabela, modifique o objeto de entidade e salve as alterações de volta para o serviço de tabela. O código a seguir altera telefone e endereço de um cliente existente de email. Em vez de chamar **table_operation::insert_entity**, este código usa **table_operation::replace_entity**. Isso faz com que a entidade a ser totalmente substituído no servidor, a menos que a entidade no servidor foi alterado desde que foi recuperada, caso em que a operação falhará. Essa falha é para impedir que seu aplicativo substitua inadvertidamente uma alteração feita entre a recuperação e atualização por outro componente do seu aplicativo. A manipulação correta dessa falha é recuperar a entidade novamente, faça suas alterações (se ainda válida) e execute outra operação de **table_operation::replace_entity** . A próxima seção mostrará como substituir esse comportamento.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Replace an entity.
    azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
    azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
    properties_to_replace.reserve(2);

    // Specify a new phone number.
    properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

    // Specify a new email address.
    properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

    // Create an operation to replace the entity.
    azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

    // Submit the operation to the Table service.
    azure::storage::table_result replace_result = table.execute(replace_operation);

## <a name="insert-or-replace-an-entity"></a>Inserir ou substituir uma entidade
operações de **table_operation::replace_entity** falhará se a entidade foi alterada desde que foi recuperada do servidor. Além disso, você deve recuperar a entidade do servidor primeiro em ordem para **table_operation::replace_entity** seja bem-sucedido. Às vezes, no entanto, você não sabe se a entidade existe no servidor e os valores atuais armazenados nele são sem importância — sua atualização deve substituir todas elas. Para fazer isso, você usaria uma operação de **table_operation::insert_or_replace_entity** . Essa operação insere a entidade se ele não existe, ou substitui em caso afirmativo, independentemente de quando a última atualização foi feita. No exemplo de código a seguir, a entidade de cliente para Jeff Smith ainda é recuperada, mas ele é salvo de volta o servidor via **table_operation::insert_or_replace_entity**. Todas as atualizações feitas a entidade entre a recuperação e a operação de atualização serão substituídas.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Insert-or-replace an entity.
    azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
    azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

    properties_to_insert_or_replace.reserve(2);

    // Specify a phone number.
    properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

    // Specify an email address.
    properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

    // Create an operation to insert-or-replace the entity.
    azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

    // Submit the operation to the Table service.
    azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);

## <a name="query-a-subset-of-entity-properties"></a>Um subconjunto das propriedades de entidade da consulta  
Uma consulta a uma tabela pode recuperar apenas algumas propriedades de uma entidade. A consulta no código a seguir usa o método **table_query::set_select_columns** para retornar somente os endereços de email de entidades na tabela.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Define the query, and select only the Email property.
    azure::storage::table_query query;
    std::vector<utility::string_t> columns;

    columns.push_back(U("Email"));
    query.set_select_columns(columns);

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Display the results.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

        const azure::storage::table_entity::properties_type& properties = it->properties();
        for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
        {
            std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
        }

        std::wcout << std::endl;
    }

>[AZURE.NOTE] Consultar algumas propriedades de uma entidade é uma operação mais eficiente que recuperar todas as propriedades.

## <a name="delete-an-entity"></a>Excluir uma entidade
Você pode facilmente excluir uma entidade após você ter recuperou-lo. Depois que a entidade é recuperada, ligue para **table_operation::delete_entity** com a entidade excluir. Chame o método **cloud_table.execute** . O seguinte código recupera e exclui uma entidade com uma chave de partição de "Antonio" e uma chave de linha de "Jeff".  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Create an operation to delete the entity.
    azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

    // Submit the delete operation to the Table service.
    azure::storage::table_result delete_result = table.execute(delete_operation);  

## <a name="delete-a-table"></a>Excluir uma tabela
Finalmente, o exemplo de código a seguir exclui uma tabela de uma conta de armazenamento. Uma tabela que tenha sido excluída estará disponível para ser recriada para um período de tempo após a exclusão.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Create an operation to delete the entity.
    azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

    // Submit the delete operation to the Table service.
    azure::storage::table_result delete_result = table.execute(delete_operation);

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu as Noções básicas do armazenamento de tabela, siga estes links para saber mais sobre o armazenamento do Azure:  

-   [Como usar o armazenamento de Blob do C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Como usar o armazenamento de fila do C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Listar os recursos de armazenamento do Azure em C++](storage-c-plus-plus-enumeration.md)
-   [Biblioteca de cliente de armazenamento para referência de C++](http://azure.github.io/azure-storage-cpp)
-   [Azure documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/)
