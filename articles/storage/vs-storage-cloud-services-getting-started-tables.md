<properties
    pageTitle="Introdução ao armazenamento de tabela e o Visual Studio conectado serviços (serviços de nuvem) | Microsoft Azure"
    description="Como começar a usar o armazenamento de tabela do Azure em um projeto de serviço de nuvem no Visual Studio depois de conectar a uma conta de armazenamento usando o Visual Studio conectado serviços"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introdução ao armazenamento de tabela do Microsoft Azure e Visual Studio conectado serviços (projetos de serviços de nuvem)

[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

##<a name="overview"></a>Visão geral

Este artigo descreve como começar a usar o armazenamento de tabela Azure no Visual Studio depois que você tenha criado ou referenciado uma conta de armazenamento do Azure em um projeto de serviços de nuvem usando a caixa de diálogo do Visual Studio **Adicionar serviços conectados** . A operação de **Adicionar serviços conectados** instala os pacotes NuGet apropriados para acessar o armazenamento do Azure em seu projeto e adiciona a cadeia de conexão da conta de armazenamento para seus arquivos de configuração do projeto.

O serviço de armazenamento de tabela do Azure permite armazenar grandes quantidades de dados estruturados. O serviço é um armazenamento de dados NoSQL que aceita chamadas autenticadas de dentro e fora da nuvem Azure. Tabelas do Azure são ideais para armazenar dados estruturados e não-relacionais.

Para começar, primeiro é necessário criar uma tabela em sua conta de armazenamento. Mostraremos como criar uma tabela do Azure no código e também como realizar operações de entidades, como adicionar, modificar, ler e entidades de tabela de leitura e de tabela básica. Os exemplos são escritos em C\# código e usar a [biblioteca de cliente de armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**Observação:** Algumas das APIs que executam chamadas check-out para o armazenamento do Azure são assíncronas. Para obter mais informações, consulte [programação assíncrona com assíncrono e espera](http://msdn.microsoft.com/library/hh191443.aspx) . O código abaixo presume métodos de programação assíncrono estão sendo usados.

- Consulte [Introdução ao armazenamento de tabela do Azure usando .NET](storage-dotnet-how-to-use-tables.md) para saber mais sobre programaticamente manipulação de tabelas.
- Consulte a [documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o armazenamento do Azure.
- Consulte a [documentação de serviços de nuvem](https://azure.microsoft.com/documentation/services/cloud-services/) para obter informações gerais sobre os serviços de nuvem Azure.
- Consulte [ASP.NET](http://www.asp.net) para obter mais informações sobre a programação de aplicativos ASP.NET.

## <a name="access-tables-in-code"></a>Tabelas do Access no código

Para acessar tabelas em projetos de serviço de nuvem, você precisa incluir itens a seguir para quaisquer arquivos de origem c# que acessam o armazenamento de tabela do Microsoft Azure.

1. Verifique se que as declarações de namespace na parte superior do arquivo c# incluem essas instruções **usando** .

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Obtenha um objeto de **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a cadeia de conexão de armazenamento e informações de conta de armazenamento de configuração do serviço Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
> [AZURE.NOTE]  Use todo o código acima frente ao código dos exemplos a seguir.

3. Obtenha um objeto de **CloudTableClient** para fazer referência os objetos de tabela em sua conta de armazenamento.

         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. Obtenha um objeto de referência **CloudTable** fazer referência a uma tabela específica e entidades.

        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Criar uma tabela no código

Para criar a tabela Azure, basta adicionar uma chamada para **CreateIfNotExistsAsync** para o depois de obter um objeto **CloudTable** conforme descrito na seção "Tabelas no código de acesso".

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de sua entidade. O código a seguir define uma classe de entidade chamada **CustomerEntity** que usa o nome do cliente como a chave de linha e o sobrenome como a chave de partição.

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

Operações de tabela que envolvem entidades são feitas usando o objeto **CloudTable** que você criou anteriormente em "acessar tabelas no código". O objeto **TableOperation** representa a operação a ser feito. O exemplo de código a seguir mostra como criar um objeto de **CloudTable** e um objeto de **CustomerEntity** . Para preparar a operação, um **TableOperation** é criado para inserir a entidade de cliente na tabela. Por fim, a operação é executada chamando **CloudTable.ExecuteAsync**.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades

Você pode inserir várias entidades em uma tabela em uma operação de gravação única. O exemplo de código a seguir cria dois objetos de entidade ("Jeff Smith" e "Ben Smith"), adiciona-los a um objeto de **TableBatchOperation** usando o método de inserir e, em seguida, inicia a operação chamando **CloudTable.ExecuteBatchAsync**.

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
    await peopleTable.ExecuteBatchAsync(batchOperation);

## <a name="get-all-of-the-entities-in-a-partition"></a>Obter todas as entidades em uma partição

Para consultar uma tabela para todas as entidades em uma partição, use um objeto de **TableQuery** . O exemplo de código a seguir especifica um filtro para entidades onde 'Smith' é a chave da partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta no console.

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity entity in resultSegment.Results)
        {
            Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
        }
    } while (token != null);

    return View();


## <a name="get-a-single-entity"></a>Obtenha uma única entidade

Você pode escrever uma consulta para obter uma entidade única e específica. O código a seguir usa um objeto de **TableOperation** para especificar um cliente chamado 'Ben Smith'. Esse método retorna apenas uma entidade, em vez de um conjunto e o valor retornado no **TableResult.Result** é um objeto **CustomerEntity** . Especificando teclas partição e de linha em uma consulta é a maneira mais rápida de recuperar uma única entidade do serviço de **tabela** .

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>Excluir uma entidade
Você pode excluir uma entidade quando o encontrar. O código a seguir procura por uma entidade de cliente denominada "Manuel Oliveira" e se ele o encontrar, exclui-lo.

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation and then execute it.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       await peopleTable.ExecuteAsync(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Couldn't delete the entity.");

## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
