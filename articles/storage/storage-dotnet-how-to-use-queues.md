<properties
    pageTitle="Introdução ao armazenamento de fila do Azure usando .NET | Microsoft Azure"
    description="Filas Azure fornecem um sistema de mensagens confiável e assíncrono, entre componentes de aplicativo. Nuvem mensagens habilita os componentes do aplicativo para dimensionar de maneira independente."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/12/2016"
    ms.author="robinsh"/>

# <a name="get-started-with-azure-queue-storage-using-net"></a>Introdução ao armazenamento de fila do Azure usando .NET

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Visão geral

Armazenamento do Azure fila fornece nuvem de mensagens entre os componentes de aplicativo. Criar aplicativos para escala, componentes de aplicativo geralmente são desassociados, para que eles possam ser redimensionados independentemente. O armazenamento de fila oferece mensagens assíncrona para comunicação entre componentes de aplicativo, se eles estiverem executando na nuvem, na área de trabalho, em um servidor local ou em um dispositivo móvel. Armazenamento de fila também dá suporte a gerenciamento de tarefas assíncronas e criação de fluxos de trabalho de processo.

### <a name="about-this-tutorial"></a>Sobre este tutorial

Este tutorial mostra como escrever código .NET para alguns cenários comuns usando o armazenamento de fila do Azure. Cenários cobertos incluem criando e excluindo filas e adicionando, ler e excluir mensagens de fila.

**Tempo estimado para concluir:** 45 minutos

**Prerequisities:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Biblioteca de cliente de armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Gerenciador de configuração do Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Uma [conta de armazenamento do Azure](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Adicionar declarações de namespace

Adicione o seguinte `using` instruções para o topo da `program.cs` arquivo:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Queue; // Namespace for Queue storage types

### <a name="parse-the-connection-string"></a>Analisará a cadeia de conexão

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>Crie o cliente de serviço de fila

A classe **CloudQueueClient** permite recuperar filas armazenadas em armazenamento de fila. Aqui está uma maneira de criar o cliente de serviço:

    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Agora você está pronto para escrever código que lê e grava dados ao armazenamento de fila.

## <a name="create-a-queue"></a>Criar uma fila

Este exemplo mostra como criar uma fila se ele ainda não existir:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a container.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## <a name="insert-a-message-into-a-queue"></a>Inserir uma mensagem em uma fila

Para inserir uma mensagem em uma fila existente, primeiro crie um novo **CloudQueueMessage**. Em seguida, chame o método **AddMessage** . Um **CloudQueueMessage** pode ser criadas de uma cadeia de caracteres (no formato UTF-8) ou uma matriz de **bytes** . Aqui está o código que cria uma fila (se ela não existir) e insere a mensagem 'Olá, mundo':

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

## <a name="peek-at-the-next-message"></a>Inspecionar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-lo da fila chamando o método **PeekMessage** .

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

    // Display message.
    Console.WriteLine(peekedMessage.AsString);

## <a name="change-the-contents-of-a-queued-message"></a>Alterar o conteúdo de uma mensagem na fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representa uma tarefa de trabalho, você pode usar esse recurso para atualizar o status da tarefa. O código a seguir atualiza a mensagem de fila com novo conteúdo e define o tempo limite de visibilidade para estender outro 60 segundos. Isso salva o estado de trabalho associado à mensagem e fornece o cliente minuto outro para continuar trabalhando na mensagem. Você pode usar essa técnica para controlar fluxos de trabalho de várias etapas em mensagens de fila sem precisar recomeçar desde o começo se uma etapa de processamento falhar devido a falha de hardware ou software. Normalmente, você prefere manter uma contagem de repetição também e se a mensagem for repetida mais de *n* vezes, você deve excluí-lo. Isso protege contra uma mensagem que dispara um erro de aplicativo cada vez que ela é processada.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.");
    queue.UpdateMessage(message,
        TimeSpan.FromSeconds(60.0),  // Make it visible for another 60 seconds.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

## <a name="de-queue-the-next-message"></a>Retirar a próxima mensagem da fila

O código do seu filas eliminação de uma mensagem de uma fila em duas etapas. Quando você chamar **GetMessage**, você recebe a próxima mensagem na fila. Uma mensagem retornada de **GetMessage** fica invisível para qualquer outro código ler mensagens dessa fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para concluir a remoção a mensagem da fila, você também deve chamar **DeleteMessage**. Esse processo de duas etapas de remoção de uma mensagem garante que, se seu código falha processar uma mensagem devido a falha de hardware ou software, outra instância do seu código pode receberá a mesma mensagem e tente novamente. Seu código chama **DeleteMessage** direita após a mensagem foi processada.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Usar padrão esperar assíncrono com APIs comuns de armazenamento de fila

Este exemplo mostra como usar o padrão de esperar assíncrono com APIs comuns de armazenamento de fila. O exemplo chama a versão assíncrona de cada um dos métodos determinados, conforme indicado pelo sufixo *assíncrono* de cada método. Quando um método assíncrono é usado, o assíncrono-aguardar padrão suspende a execução local até que a chamada seja concluída. Esse comportamento permite que o segmento atual para outro trabalho, o que ajuda a evitar gargalos de desempenho e melhora a capacidade de resposta geral do seu aplicativo. Para obter mais detalhes sobre como usar o padrão de espera assíncrono no .NET consulte [assíncrono e espera (c# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create the queue if it doesn't already exist
    if(await queue.CreateIfNotExistsAsync())
    {
        Console.WriteLine("Queue '{0}' Created", queue.Name);
    }
    else
    {
        Console.WriteLine("Queue '{0}' Exists", queue.Name);
    }

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await queue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await queue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Aproveite as opções adicionais para eliminação filas de mensagens

Há duas maneiras que você pode personalizar a recuperação de mensagens de uma fila.
Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir o tempo limite invisibility maior ou menor, permitindo a seu código mais ou menos tempo para processar totalmente cada mensagem. O exemplo de código a seguir usa o método **GetMessages** para receber mensagens de 20 em uma chamada. Em seguida, ele processa cada mensagem usando um loop **foreach** . Ele também define o tempo limite invisibility para cinco minutos para cada mensagem. Observe que o inicia de 5 minutos para todas as mensagens ao mesmo tempo, após ter de 5 minutos transmitido desde que a chamada para **GetMessages**, quaisquer mensagens que não foram excluídos ficará visível novamente.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>Obtenha o comprimento da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O método **FetchAttributes** solicita o serviço de fila para recuperar os atributos de fila, incluindo a contagem de mensagens. A propriedade **ApproximateMessageCount** retorna o último valor recuperado pelo método **FetchAttributes** , sem chamar o serviço de fila.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Fetch the queue attributes.
    queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="delete-a-queue"></a>Excluir uma fila

Para excluir uma fila e todas as mensagens contidas nele, chame o método **Delete** no objeto fila.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu as Noções básicas do armazenamento de fila, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

- Exiba a documentação de referência de serviço de fila para obter detalhes completos sobre APIs disponíveis:
    - [Biblioteca de cliente de armazenamento para referência .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [Referência de API REST](http://msdn.microsoft.com/library/azure/dd179355)
- Aprenda a simplificar o código que você escreve para trabalhar com o armazenamento do Azure usando o [SDK do Azure WebJobs](../app-service-web/websites-dotnet-webjobs-sdk.md).
- Exiba guias de recurso mais para saber sobre as opções adicionais para armazenar dados no Azure.
    - [Introdução ao armazenamento de tabela do Azure usando .NET](storage-dotnet-how-to-use-tables.md) para armazenar dados estruturados.
    - [Introdução ao armazenamento de Blob do Azure usando .NET](storage-dotnet-how-to-use-blobs.md) para armazenar dados não estruturados.
    - [Conectar ao banco de dados SQL usando .NET (c#)](../sql-database/sql-database-develop-dotnet-simple.md) para armazenar dados relacionais.

  [Download and install the Azure SDK for .NET]: /develop/net/
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Creating a Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
