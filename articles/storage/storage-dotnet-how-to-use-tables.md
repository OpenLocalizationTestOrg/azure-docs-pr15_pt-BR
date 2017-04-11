<properties
    pageTitle="Introdução ao armazenamento de tabela do Azure usando .NET | Microsoft Azure"
    description="Armazene dados estruturados na nuvem usando o armazenamento de tabela do Azure, um armazenamento de dados NoSQL."
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


# <a name="get-started-with-azure-table-storage-using-net"></a>Introdução ao armazenamento de tabela do Azure usando .NET

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Visão geral

Armazenamento de tabela do Azure é um serviço que armazena dados estruturados de NoSQL na nuvem. Armazenamento de tabela é um armazenamento de chave/atributo com um design schemaless. Como o armazenamento de tabela é schemaless, é fácil adaptar seus dados conforme as necessidades dos seus evolve de aplicativo. Acesso a dados é rápido e econômico para todos os tipos de aplicativos. Armazenamento de tabela normalmente é significativamente menor no custo SQL tradicional para semelhantes volumes de dados.

Você pode usar o armazenamento de tabela para armazenar flexíveis conjuntos de dados, como dados de usuário para aplicativos web, catálogos de endereços, informações de dispositivo e qualquer outro tipo de metadados que requer o seu serviço. Você pode armazenar qualquer número de entidades em uma tabela e uma conta de armazenamento pode conter qualquer número de tabelas, para cima até o limite de capacidade da conta de armazenamento.

### <a name="about-this-tutorial"></a>Sobre este tutorial

Este tutorial mostra como escrever código .NET para alguns cenários comuns usando o armazenamento de tabela do Azure, incluindo a criação e a exclusão de uma tabela e inserindo, atualizando, excluindo e consultando dados de tabela.

**Tempo estimado para concluir:** 45 minutos

**Prerequisities:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Biblioteca de cliente de armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Gerenciador de configuração do Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Uma [conta de armazenamento do Azure](storage-create-storage-account.md#create-a-storage-account)

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Mais exemplos

Para exemplos adicionais usando o armazenamento de tabela, consulte [Introdução ao armazenamento de tabela do Azure no .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/). Você pode baixar o aplicativo de amostra e executá-lo ou procure o código no GitHub.


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Adicionar declarações de namespace

Adicione o seguinte `using` instruções para o topo da `program.cs` arquivo:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Table; // Namespace for Table storage types

### <a name="parse-the-connection-string"></a>Analisará a cadeia de conexão

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Crie o cliente de serviço de tabela

A classe **CloudTableClient** permite recuperar tabelas e entidades armazenadas em armazenamento de tabela. Aqui está uma maneira de criar o cliente de serviço:

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

Agora você está pronto para escrever código que lê e grava dados ao armazenamento de tabela.

## <a name="create-a-table"></a>Criar uma tabela

Este exemplo mostra como criar uma tabela se ele ainda não existir:

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Retrieve a reference to the table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the table if it doesn't exist.
    table.CreateIfNotExists();

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Entidades mapeiam para C\# objetos usando uma classe personalizada derivam de **TableEntity**. Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de sua entidade. O código a seguir define uma classe de entidade que usa o nome do cliente como a chave de linha e o sobrenome como a chave da partição. Juntos, partição da entidade e a chave de linha identificam a entidade na tabela. Entidades com a mesma chave de partição podem ser consultadas mais rápido do que aqueles com chaves de partição diferentes, mas usando as teclas de partição diverso permite maior escalabilidade de operações paralelas.  Para qualquer propriedade que deve ser armazenada no serviço de tabela, a propriedade deve ser uma propriedade pública de um tipo suportado que expõe ambos `get` e `set`.
Além disso, o tipo de entidade *deve* expor um construtor sem parâmetros.

    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

Operações de tabela que envolvem entidades são executadas através do objeto de **CloudTable** que você criou anteriormente na seção "Criar uma tabela". A operação a ser executada é representada por um objeto **TableOperation** .  O exemplo de código a seguir mostra a criação do objeto **CloudTable** e, em seguida, um objeto de **CustomerEntity** .  Para preparar a operação, um objeto **TableOperation** é criado para inserir a entidade de cliente na tabela.  Por fim, a operação é executada chamando **CloudTable.Execute**.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation object that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

## <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades

Você pode inserir um lote de entidades em uma tabela em uma operação de gravação. Algumas outras observações sobre operações em lotes:

-  Você pode executar atualizações, exclui e insere na mesma operação único lote.
-  Uma operação de único lote pode incluir até 100 entidades.
-  Todas as entidades em uma operação de único lote devem ter a mesma chave de partição.
-  Embora seja possível executar uma consulta como uma operação em lotes, ele deve ser a única operação no lote.

<!-- -->
O exemplo de código a seguir cria dois objetos de entidade e adiciona cada **TableBatchOperation** usando o método **Insert** . Em seguida, **CloudTable.Execute** é chamado para executar a operação.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity and add it to the table.
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";
    customer1.PhoneNumber = "425-555-0104";

    // Create another customer entity and add it to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";

    // Add both customer entities to the batch insert operation.
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);

    // Execute the batch operation.
    table.ExecuteBatch(batchOperation);

## <a name="retrieve-all-entities-in-a-partition"></a>Recuperar todas as entidades em uma partição

Para consultar uma tabela para todas as entidades em uma partição, use um objeto de **TableQuery** .
O exemplo de código a seguir especifica um filtro para entidades onde 'Smith' é a chave da partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta no console.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Recuperar um intervalo de entidades em uma partição

Se você não quiser todas as entidades em uma partição de consulta, você pode especificar um intervalo combinando o filtro de chave de partição com um filtro de chave de linha. O exemplo de código a seguir usa dois filtros para obter todas as entidades na partição 'Smith' onde a chave de linha (nome) começa com uma letra anteriores ao 'E' do alfabeto e imprime os resultados da consulta.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the table query.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Loop through the results, displaying information about the entity.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-a-single-entity"></a>Recuperar uma única entidade

Você pode escrever uma consulta para recuperar uma entidade única e específica. O código a seguir usa **TableOperation** para especificar o cliente 'Ben Smith'.
Esse método retorna apenas uma entidade em vez de um conjunto e o valor retornado no **TableResult.Result** é um objeto **CustomerEntity** .
Especificando teclas partição e de linha em uma consulta é a maneira mais rápida de recuperar uma única entidade do serviço de tabela.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="replace-an-entity"></a>Substituir uma entidade

Para atualizar uma entidade, recuperá-la a partir do serviço de tabela, modifique o objeto de entidade e salve as alterações de volta para o serviço de tabela. O código a seguir altera o número de telefone de um cliente existente. Em vez de chamar **Inserir**, este código usa **Substituir**. Isso faz com que a entidade a ser totalmente substituído no servidor, a menos que a entidade no servidor foi alterado desde que foi recuperada, caso em que a operação falhará.  Essa falha é para impedir que seu aplicativo substitua inadvertidamente uma alteração feita entre a recuperação e atualização por outro componente do seu aplicativo.  A manipulação correta dessa falha é recuperar a entidade novamente, faça suas alterações (se ainda válida) e execute outra operação **Substituir** .  A próxima seção mostrará como substituir esse comportamento.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-0105";

       // Create the Replace TableOperation.
       TableOperation updateOperation = TableOperation.Replace(updateEntity);

       // Execute the operation.
       table.Execute(updateOperation);

       Console.WriteLine("Entity updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="insert-or-replace-an-entity"></a>Inserir ou substituir uma entidade

**Substituir** operações falhará se a entidade foi alterada desde que foi recuperada do servidor.  Além disso, você deve recuperar a entidade do servidor primeiro em ordem para a operação de **Substituir** seja bem-sucedido.
Às vezes, no entanto, você não sabe se a entidade existe no servidor e os valores atuais armazenados nele são sem importância. Sua atualização deve substituir todas elas.  Para fazer isso, você usaria uma operação de **InsertOrReplace** .  Essa operação insere a entidade se ele não existe, ou substitui em caso afirmativo, independentemente de quando a última atualização foi feita.  No exemplo de código a seguir, a entidade de cliente para Ben Smith ainda é recuperada, mas ele é salvo de volta o servidor via **InsertOrReplace**.  Todas as atualizações feitas a entidade entre as operações de atualização e a recuperação serão substituídas.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-1234";

       // Create the InsertOrReplace TableOperation.
       TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

       // Execute the operation.
       table.Execute(insertOrReplaceOperation);

       Console.WriteLine("Entity was updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="query-a-subset-of-entity-properties"></a>Um subconjunto das propriedades de entidade da consulta

Uma consulta de tabela pode recuperar apenas algumas propriedades de uma entidade em vez de todas as propriedades de entidade. Essa técnica, chamada projeção, reduz a largura de banda e pode melhorar o desempenho de consulta, especialmente para entidades grandes. A consulta no código a seguir retorna apenas os endereços de email de entidades na tabela. Isso é feito usando uma consulta de **DynamicTableEntity** e também **EntityResolver**. Você pode aprender mais sobre projeção na [postagem de blog de projeção de consulta e apresentando Upsert][]. Observe que projeção não é suportada no emulador armazenamento local, para que esse código é executado somente quando você estiver usando uma conta do serviço de tabela.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and select only the Email property.
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

## <a name="delete-an-entity"></a>Excluir uma entidade

Você pode facilmente excluir uma entidade após você ter recuperou, usando o mesmo padrão mostrado para atualizar uma entidade.  O seguinte código recupera e exclui uma entidade de cliente.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       table.Execute(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Could not retrieve the entity.");

## <a name="delete-a-table"></a>Excluir uma tabela

Finalmente, o exemplo de código a seguir exclui uma tabela de uma conta de armazenamento. Uma tabela que tenha sido excluída estará disponível para ser recriada para um período de tempo após a exclusão.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

## <a name="retrieve-entities-in-pages-asynchronously"></a>Recuperar entidades em páginas assíncrona

Se você está lendo um grande número de entidades, e você quiser processo/exibir entidades como eles são recuperados em vez de esperar todos eles retornar, você pode recuperar entidades usando uma consulta segmentada. Este exemplo mostra como retornar resultados em páginas usando o padrão de esperar assíncrono para que a execução não está bloqueada enquanto aguarda para um grande conjunto de resultados para retornar. Para obter mais detalhes sobre como usar o padrão de espera assíncrono no .NET, consulte [programação assíncrona com assíncrono e espera (c# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

    // Initialize a default TableQuery to retrieve all the entities in the table.
    TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

    // Initialize the continuation token to null to start from the beginning of the table.
    TableContinuationToken continuationToken = null;

    do
    {
        // Retrieve a segment (up to 1,000 entities).
        TableQuerySegment<CustomerEntity> tableQueryResult =
            await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

        // Assign the new continuation token to tell the service where to
        // continue on the next iteration (or null if it has reached the end).
        continuationToken = tableQueryResult.ContinuationToken;

        // Print the number of rows retrieved.
        Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

    // Loop until a null continuation token is received, indicating the end of the table.
    } while(continuationToken != null);

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu as Noções básicas do armazenamento de tabela, siga estes links para saber mais sobre tarefas mais complexas de armazenamento:

- Ver mais exemplos de armazenamento de tabela na [Introdução ao armazenamento de tabela do Azure no .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
- Exiba a documentação de referência do serviço de tabela para obter detalhes completos sobre APIs disponíveis:
    - [Biblioteca de cliente de armazenamento para referência .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [Referência de API REST](http://msdn.microsoft.com/library/azure/dd179355)
- Aprenda a simplificar o código que você escreve para trabalhar com o armazenamento do Azure usando o [SDK do Azure WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-get-started.md)
- Exiba guias de recurso mais para saber sobre as opções adicionais para armazenar dados no Azure.
    - [Introdução ao armazenamento de Blob do Azure usando .NET](storage-dotnet-how-to-use-blobs.md) para armazenar dados não estruturados.
    - [Conectar ao banco de dados SQL usando .NET (c#)](../sql-database/sql-database-develop-dotnet-simple.md) para armazenar dados relacionais.

  [Download and install the Azure SDK for .NET]: /develop/net/
  [Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png

  [Postagem de blog apresentando Upsert e projeção de consulta]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [.NET Client Library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Azure Storage Team blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configure Azure Storage connection strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [How to: Programmatically access Table storage]: #tablestorage
