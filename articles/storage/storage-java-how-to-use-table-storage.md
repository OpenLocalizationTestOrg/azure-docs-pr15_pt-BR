<properties
    pageTitle="Como usar o armazenamento de tabela do Java | Microsoft Azure"
    description="Armazene dados estruturados na nuvem usando o armazenamento de tabela do Azure, um armazenamento de dados NoSQL."
    services="storage"
    documentationCenter="java"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-java"></a>Como usar o armazenamento de tabela do Java

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Visão geral

Este guia mostrará como executar cenários comuns usando o serviço de armazenamento de tabela do Azure. Os exemplos são gravados em Java e usam o [SDK de armazenamento do Azure para Java][]. Os cenários cobertos incluem tabelas **Criando**, **Listando**e **Excluindo** , bem como **Inserir**, **consultando**, **modificando**e **Excluindo** entidades em uma tabela. Para obter mais informações sobre tabelas, consulte a seção [próximas etapas](#Next-Steps) .

Observação: Um SDK está disponível para os desenvolvedores que usam o armazenamento do Azure em dispositivos Android. Para obter mais informações, consulte o [SDK de armazenamento do Azure para Android][].

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Criar um aplicativo Java

Neste guia, você usará os recursos de armazenamento que podem ser executados dentro de um aplicativo Java localmente ou no código em execução dentro de uma função da web ou a função de trabalho no Azure.

Para fazer isso, você precisará instalar o Kit de desenvolvimento de Java (JDK) e crie uma conta de armazenamento do Azure em sua assinatura do Azure. Depois que você tiver feito isso, você precisa verificar se o seu sistema de desenvolvimento atende os requisitos mínimos e as dependências que são listadas no repositório [SDK de armazenamento do Azure para Java][] no GitHub. Se seu sistema atenda a esses requisitos, você pode seguir as instruções para baixar e instalar as bibliotecas de armazenamento do Azure para Java em seu sistema desse repositório. Quando tiver concluído essas tarefas, você poderá criar um aplicativo Java que usa os exemplos deste artigo.

## <a name="configure-your-application-to-access-table-storage"></a>Configurar seu aplicativo para acessar o armazenamento de tabela

Adicione as seguintes instruções de importação para a parte superior do arquivo Java onde você deseja usar o armazenamento do Microsoft Azure APIs para acessar tabelas:

    // Include the following imports to use table APIs
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.table.*;
    import com.microsoft.azure.storage.table.TableQuery.*;

## <a name="setup-an-azure-storage-connection-string"></a>Configuração de uma cadeia de caracteres de conexão de armazenamento do Azure

Um cliente de armazenamento do Azure usa uma cadeia de conexão de armazenamento para armazenar pontos de extremidade e as credenciais para acessar os serviços de gerenciamento de dados. Quando em execução em um aplicativo cliente, você deve fornecer a cadeia de conexão de armazenamento no formato a seguir, usando o nome da sua conta de armazenamento e a chave primária de acesso para a conta de armazenamento listada no [Portal do Azure](https://portal.azure.com) para os valores *AccountName* e *AccountKey* . Este exemplo mostra como você pode declarar um campo estático para armazenar a cadeia de conexão:

    // Define the connection-string with your values.
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

Em um aplicativo em execução dentro de uma função no Microsoft Azure, essa cadeia de caracteres pode ser armazenada no arquivo de configuração do serviço, *ServiceConfiguration*e pode ser acessada com uma chamada para o método **RoleEnvironment.getConfigurationSettings** . Aqui está um exemplo de Obtendo a cadeia de conexão de um elemento de **configuração** chamado *StorageConnectionString* no arquivo de configuração de serviço:

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Os exemplos a seguir presumem que você usou um destes dois métodos para obter a cadeia de conexão de armazenamento.

## <a name="how-to-create-a-table"></a>Como: criar uma tabela

Um objeto de **CloudTableClient** permite que você obtenha objetos de referência para tabelas e entidades. O código a seguir cria um objeto de **CloudTableClient** e usa para criar um novo objeto **CloudTable** que representa uma tabela chamada "pessoas". (Anotação: existem maneiras adicionais para criar objetos **CloudStorageAccount** ; para obter mais informações, consulte **CloudStorageAccount** na [Referência de SDK do Azure armazenamento cliente].)

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the table client.
       CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create the table if it doesn't exist.
       String tableName = "people";
       CloudTable cloudTable = tableClient.getTableReference(tableName);
       cloudTable.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-list-the-tables"></a>Como: listar as tabelas

Para obter uma lista de tabelas, chame o método **CloudTableClient.listTables()** para recuperar uma lista iterable dos nomes de tabela.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Loop through the collection of table names.
        for (String table : tableClient.listTables())
        {
          // Output each table name.
          System.out.println(table);
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-add-an-entity-to-a-table"></a>Como: adicionar uma entidade a uma tabela

Mapa de entidades para objetos de Java usando uma classe personalizada implementar **TableEntity**. Para sua conveniência, a classe **TableServiceEntity** implementa **TableEntity** e usa reflexão para mapear propriedades para métodos getter e setter nomeados para as propriedades. Para adicionar uma entidade a uma tabela, primeiro crie uma classe que define as propriedades de sua entidade. O código a seguir define uma classe de entidade que usa o nome do cliente como a chave de linha e o sobrenome como a chave da partição. Juntos, partição da entidade e a chave de linha identificam a entidade na tabela. Entidades com a mesma chave de partição podem ser consultadas mais rápido do que aqueles com chaves de partição diferente.

    public class CustomerEntity extends TableServiceEntity {
        public CustomerEntity(String lastName, String firstName) {
            this.partitionKey = lastName;
            this.rowKey = firstName;
        }

        public CustomerEntity() { }

        String email;
        String phoneNumber;

        public String getEmail() {
            return this.email;
        }

        public void setEmail(String email) {
            this.email = email;
        }

        public String getPhoneNumber() {
            return this.phoneNumber;
        }

        public void setPhoneNumber(String phoneNumber) {
            this.phoneNumber = phoneNumber;
        }
    }

Operações de tabela que envolvem entidades exigem um objeto **TableOperation** . Este objeto define a operação a ser executada em uma entidade, que pode ser executada com um objeto **CloudTable** . O código a seguir cria uma nova instância da classe **CustomerEntity** com alguns dados de cliente sejam armazenados. O código próximo chama **TableOperation.insertOrReplace** para criar um objeto de **TableOperation** para inserir uma entidade em uma tabela e associa a nova **CustomerEntity** com ele. Finalmente, o código chama o método **Executar** no objeto **CloudTable** , especificando a tabela de "pessoas" e a nova **TableOperation**, que envia uma solicitação para o serviço de armazenamento para inserir a nova entidade de cliente na tabela de "pessoas" ou substituir a entidade se ele já existir.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a new customer entity.
        CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
        customer1.setEmail("Walter@contoso.com");
        customer1.setPhoneNumber("425-555-0101");

        // Create an operation to add the new customer to the people table.
        TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

        // Submit the operation to the table service.
        cloudTable.execute(insertCustomer1);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-insert-a-batch-of-entities"></a>Como: inserir um lote de entidades

Você pode inserir um lote de entidades para o serviço de tabela em uma operação de gravação. O código a seguir cria um objeto de **TableBatchOperation** , em seguida, adiciona que três Inserir operações-lo. Cada operação de inserção é adicionada criando um novo objeto de entidade, definindo seus valores e, em seguida, chamar o método **insert** no objeto **TableBatchOperation** para associar a entidade uma nova operação de inserir. Em seguida, o código chama **Executar** no objeto **CloudTable** , especificando a tabela de "pessoas" e o objeto **TableBatchOperation** , que envia o lote de operações de tabela para o serviço de armazenamento em uma única solicitação.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Define a batch operation.
        TableBatchOperation batchOperation = new TableBatchOperation();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a customer entity to add to the table.
        CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
        customer.setEmail("Jeff@contoso.com");
        customer.setPhoneNumber("425-555-0104");
        batchOperation.insertOrReplace(customer);

       // Create another customer entity to add to the table.
       CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
       customer2.setEmail("Ben@contoso.com");
       customer2.setPhoneNumber("425-555-0102");
       batchOperation.insertOrReplace(customer2);

       // Create a third customer entity to add to the table.
       CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
       customer3.setEmail("Denise@contoso.com");
       customer3.setPhoneNumber("425-555-0103");
       batchOperation.insertOrReplace(customer3);

       // Execute the batch of operations on the "people" table.
       cloudTable.execute(batchOperation);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Algumas informações sobre as operações de lote:

- Você pode executar até 100 insert, excluir, mesclar, substituir, inserir ou mesclar e inserir ou substituir operações em qualquer combinação em um único lote.
- Uma operação em lotes pode ter uma operação de recuperar, se for a única operação no lote.
- Todas as entidades em uma operação de único lote devem ter a mesma chave de partição.
- Uma operação de lote está limitada a uma carga de dados de 4MB.

## <a name="how-to-retrieve-all-entities-in-a-partition"></a>Como: recuperar todas as entidades em uma partição

Para consultar uma tabela para entidades em uma partição, você pode usar um **TableQuery**. Chame **TableQuery.from** para criar uma consulta em uma tabela específica que retorna um tipo de resultado especificado. O código a seguir especifica um filtro para entidades onde 'Smith' é a chave da partição. **TableQuery.generateFilterCondition** é um método auxiliar para criar filtros para consultas. Chame **onde** a referência retornada pelo método **TableQuery.from** para aplicar o filtro à consulta. Quando a consulta é executada com uma chamada para **Executar** no objeto **CloudTable** , ela retorna um **iterador** com o tipo de resultado de **CustomerEntity** especificado. Você pode usar o **iterador** retornado em um loop for each consumir os resultados. Este código imprime os campos de cada entidade nos resultados da consulta no console.

    try
    {
        // Define constants for filters.
        final String PARTITION_KEY = "PartitionKey";
        final String ROW_KEY = "RowKey";
        final String TIMESTAMP = "Timestamp";

        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create a cloud table object for the table.
       CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a filter condition where the partition key is "Smith".
        String partitionFilter = TableQuery.generateFilterCondition(
           PARTITION_KEY,
           QueryComparisons.EQUAL,
           "Smith");

       // Specify a partition query, using "Smith" as the partition key filter.
       TableQuery<CustomerEntity> partitionQuery =
           TableQuery.from(CustomerEntity.class)
           .where(partitionFilter);

        // Loop through the results, displaying information about the entity.
        for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-retrieve-a-range-of-entities-in-a-partition"></a>Como: recuperar um intervalo de entidades em uma partição

Se você não quiser todas as entidades em uma partição de consulta, você pode especificar um intervalo usando operadores de comparação em um filtro. O código a seguir combina dois filtros para obter todas as entidades na partição "Antonio" onde a chave de linha (nome) começa com uma letra até 'E' do alfabeto. Imprime os resultados da consulta. Se você usar as entidades adicionadas à tabela no lote Inserir seção deste guia, apenas duas entidades são retornadas desta vez (Manuel e Denise Smith); Jeff Smith não está incluído.

    try
    {
        // Define constants for filters.
        final String PARTITION_KEY = "PartitionKey";
        final String ROW_KEY = "RowKey";
        final String TIMESTAMP = "Timestamp";

        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the table client.
       CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create a cloud table object for the table.
       CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a filter condition where the partition key is "Smith".
        String partitionFilter = TableQuery.generateFilterCondition(
           PARTITION_KEY,
           QueryComparisons.EQUAL,
           "Smith");

        // Create a filter condition where the row key is less than the letter "E".
        String rowFilter = TableQuery.generateFilterCondition(
           ROW_KEY,
           QueryComparisons.LESS_THAN,
           "E");

        // Combine the two conditions into a filter expression.
        String combinedFilter = TableQuery.combineFilters(partitionFilter,
            Operators.AND, rowFilter);

        // Specify a range query, using "Smith" as the partition key,
        // with the row key being up to the letter "E".
        TableQuery<CustomerEntity> rangeQuery =
           TableQuery.from(CustomerEntity.class)
           .where(combinedFilter);

        // Loop through the results, displaying information about the entity
        for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-retrieve-a-single-entity"></a>Como: recuperar uma única entidade

Você pode escrever uma consulta para recuperar uma entidade única e específica. O código a seguir chama **TableOperation.retrieve** com linha e chave da partição parâmetros-chave para especificar o cliente "Jeff Smith", em vez de criar um **TableQuery** e usar filtros para fazer a mesma coisa. Quando executada, a operação de recuperar retorna apenas uma entidade, em vez de um conjunto. O método **getResultAsType** converterá o resultado para o tipo do destino de atribuição, um objeto de **CustomerEntity** . Se este tipo não é compatível com o tipo especificado para a consulta, será lançada uma exceção. Um valor nulo será retornado se nenhuma entidade tem uma partição exata e a chave de linha correspondem. Especificando teclas partição e de linha em uma consulta é a maneira mais rápida de recuperar uma única entidade do serviço de tabela.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
        TableOperation retrieveSmithJeff =
           TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

       // Submit the operation to the table service and get the specific entity.
       CustomerEntity specificEntity =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Output the entity.
        if (specificEntity != null)
        {
            System.out.println(specificEntity.getPartitionKey() +
                " " + specificEntity.getRowKey() +
                "\t" + specificEntity.getEmail() +
                "\t" + specificEntity.getPhoneNumber());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-modify-an-entity"></a>Como: modificar uma entidade

Para modificar uma entidade, recuperá-la a partir do serviço de tabela, faça as alterações no objeto de entidade e salvar as alterações de volta para o serviço de tabela com uma operação de substituir ou direta. O código a seguir altera o número de telefone de um cliente existente. Em vez de chamar **TableOperation.insert** como fizemos para inserir, esse código chama **TableOperation.replace**. O método **CloudTable.execute** chama o serviço de tabela e a entidade é substituída, a menos que outro aplicativo modificaram no tempo desde que este aplicativo recuperá-la. Quando isso acontece, uma exceção é lançada e a entidade deve ser recuperada, modificada e salvo novamente. Esse padrão de repetição de concorrência otimista é comum em um sistema de armazenamento distribuído.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
        TableOperation retrieveSmithJeff =
           TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Submit the operation to the table service and get the specific entity.
        CustomerEntity specificEntity =
          cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Specify a new phone number.
        specificEntity.setPhoneNumber("425-555-0105");

        // Create an operation to replace the entity.
        TableOperation replaceEntity = TableOperation.replace(specificEntity);

        // Submit the operation to the table service.
        cloudTable.execute(replaceEntity);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-query-a-subset-of-entity-properties"></a>Como: consultar um subconjunto das propriedades de entidade

Uma consulta a uma tabela pode recuperar apenas algumas propriedades de uma entidade. Essa técnica, chamada projeção, reduz a largura de banda e pode melhorar o desempenho de consulta, especialmente para entidades grandes. A consulta no código a seguir usa o método **select** para retornar somente os endereços de email de entidades na tabela. Os resultados são projetados em uma coleção de **cadeia de caracteres** com a Ajuda de um **EntityResolver**, que faz a conversão de tipo nas entidades retornada do servidor. Você pode saber mais sobre projeção no [tabelas do Azure: apresentando Upsert e projeção de consulta][]. Observe que projeção não é suportada no emulador armazenamento local, para que esse código é executado somente quando usando uma conta do serviço de tabela.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Define a projection query that retrieves only the Email property
        TableQuery<CustomerEntity> projectionQuery =
           TableQuery.from(CustomerEntity.class)
           .select(new String[] {"Email"});

        // Define a Entity resolver to project the entity to the Email value.
        EntityResolver<String> emailResolver = new EntityResolver<String>() {
            @Override
            public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
                return properties.get("Email").getValueAsString();
            }
        };

        // Loop through the results, displaying the Email values.
        for (String projectedString :
            cloudTable.execute(projectionQuery, emailResolver)) {
                System.out.println(projectedString);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-insert-or-replace-an-entity"></a>Como: inserir ou substituir uma entidade

Muitas vezes você deseja adicionar uma entidade a uma tabela sem saber se ele já existe na tabela. Uma operação de inserir ou substituir permite que você faça uma única solicitação que irá inserir a entidade se ele não existir ou substituir a existente em caso afirmativo. O código a seguir com base em exemplos anteriores, insere ou substitui a entidade para "Walter Harp". Depois de criar uma nova entidade, este código chama o método de **TableOperation.insertOrReplace** . Este código depois telefona para **Executar** no objeto **CloudTable** com a tabela e inserir ou substituir operação de tabela como os parâmetros. Para atualizar apenas parte de uma entidade, o método **TableOperation.insertOrMerge** pode ser usado. Observe que inserir-ou-replace não é suportado no emulador armazenamento local, para que esse código é executado somente quando usando uma conta do serviço de tabela. Saiba mais sobre como inserir ou substituir e inserir ou mesclagem-neste [tabelas do Azure: apresentando Upsert e projeção de consulta][].

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a new customer entity.
        CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
        customer5.setEmail("Walter@contoso.com");
        customer5.setPhoneNumber("425-555-0106");

        // Create an operation to add the new customer to the people table.
        TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

        // Submit the operation to the table service.
        cloudTable.execute(insertCustomer5);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-an-entity"></a>Como: excluir uma entidade

Você pode facilmente excluir uma entidade após você ter recuperou-lo. Depois que a entidade é recuperada, ligue para **TableOperation.delete** com a entidade excluir. Ligue para **Executar** no objeto **CloudTable** . O seguinte código recupera e exclui uma entidade de cliente.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
        TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
        CustomerEntity entitySmithJeff =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Create an operation to delete the entity.
        TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

        // Submit the delete operation to the table service.
        cloudTable.execute(deleteSmithJeff);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-a-table"></a>Como: excluir uma tabela

Finalmente, o código a seguir exclui uma tabela de uma conta de armazenamento. Uma tabela que foi excluída não estará disponível para ser recriadas para um período de tempo após a exclusão, normalmente menos de quarenta segundos.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Delete the table and all its data if it exists.
        CloudTable cloudTable = new CloudTable("people",tableClient);
        cloudTable.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu as Noções básicas do armazenamento de tabela, siga estes links para aprender a realizar tarefas de armazenamento mais complexas.

- [Armazenamento do Azure SDK para Java][]
- [Referência SDK do cliente de armazenamento do Azure][]
- [API REST de armazenamento do Azure][]
- [Blog da equipe de armazenamento do Azure][]

Para obter mais informações, consulte também o [Java Developer Center](/develop/java/).


[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Armazenamento do Azure SDK para Java]: https://github.com/azure/azure-storage-java
[Armazenamento do Azure SDK para Android]: https://github.com/azure/azure-storage-android
[Referência SDK do cliente de armazenamento do Azure]: http://dl.windowsazure.com/storage/javadoc/
[API REST de armazenamento do Azure]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Blog da equipe de armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Tabelas do Azure: Apresentando Upsert e projeção de consulta]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
