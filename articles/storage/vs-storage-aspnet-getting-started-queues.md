<properties
    pageTitle="Introdução ao armazenamento de fila e Visual Studio conectado serviços (ASP.NET) | Microsoft Azure"
    description="Como começar a usar o armazenamento do Azure fila em um projeto do ASP.NET no Visual Studio depois de conectar a uma conta de armazenamento usando o Visual Studio conectado serviços"
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
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services"></a>Introdução ao Azure fila de armazenamento e o Visual Studio conectado serviços

[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Visão geral

Este artigo descreve como começar a usar o armazenamento do Azure fila no Visual Studio depois que você tenha criado ou referenciado uma conta de armazenamento do Azure em um projeto do ASP.NET usando a caixa de diálogo do Visual Studio **Adicionar serviços conectados** .

Mostraremos como criar e acessar uma fila do Azure em sua conta de armazenamento. Também mostraremos como executar operações de fila básicas, como adição, modificação, lendo e Removendo mensagens de fila. Os exemplos são gravados no código c# e usam a [Biblioteca do cliente de armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx). Para obter mais informações sobre ASP.NET, consulte [ASP.NET](http://www.asp.net).

Armazenamento de fila Azure é um serviço para armazenar grandes números de mensagens que podem ser acessadas a partir de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma mensagem de única fila pode ter até 64 KB em tamanho e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento.

## <a name="access-queues-in-code"></a>Filas de acesso no código

Para acessar filas em projetos do ASP.NET, você precisa incluir os seguintes itens para qualquer arquivo de origem c# que acessam o armazenamento de fila do Azure.

1. Verifique se que as declarações de namespace na parte superior do arquivo c# incluem essas instruções **usando** .

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenha um objeto de **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a sua cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenha um objeto de **CloudQueueClient** para fazer referência os objetos de fila em sua conta de armazenamento.  

        // Create the CloudQueueClient object for this storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenha um objeto de **CloudQueue** para fazer referência a uma fila específica.

        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**Observação** Use todo o código acima frente ao código dos exemplos a seguir.

## <a name="create-a-queue-in-code"></a>Criar uma fila no código

Para criar uma fila Azure no código, basta adicione uma chamada para **CreateIfNotExists** para o código acima.

    // Create the messageQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem para uma fila

Para inserir uma mensagem em uma fila existente, criar um novo objeto de **CloudQueueMessage** , em seguida, chame o método **AddMessage** .

Um objeto de **CloudQueueMessage** pode ser criado de uma cadeia de caracteres (no formato UTF-8) ou uma matriz de bytes.

Aqui está um exemplo que insere a mensagem 'Olá, mundo'.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Ler uma mensagem em uma fila

Você pode inspecionar a mensagem na frente de uma fila sem removê-lo da fila chamando o método PeekMessage().

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Ler e remover uma mensagem em uma fila

Seu código pode remover (eliminação na fila) uma mensagem de uma fila em duas etapas.
1. Chame getMessage () para obter a próxima mensagem na fila. Uma mensagem retornada de getMessage () fica invisível para qualquer outro código ler mensagens dessa fila. Por padrão, essa mensagem permanece invisível por 30 segundos.
2.  Para concluir a remoção a mensagem da fila, ligue para **DeleteMessage**.

Esse processo de duas etapas de remoção de uma mensagem garante que, se seu código falha processar uma mensagem devido a falha de hardware ou software, outra instância do seu código pode receberá a mesma mensagem e tente novamente. O código a seguir chama **DeleteMessage** direita após a mensagem foi processada.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-for-de-queuing-messages"></a>Usar opções adicionais para eliminação filas de mensagens

Há duas maneiras que você pode personalizar a recuperação de mensagens de uma fila.
Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir o tempo limite invisibility maior ou menor, permitindo a seu código mais ou menos tempo para processar totalmente cada mensagem. O exemplo de código a seguir usa o método **GetMessages** para receber mensagens de 20 em uma chamada. Em seguida, ele processa cada mensagem usando um loop **foreach** . Ele também define o tempo limite invisibility para cinco minutos para cada mensagem. Observe que o inicia de 5 minutos para todas as mensagens ao mesmo tempo, após ter de 5 minutos transmitido desde que a chamada para **GetMessages**, quaisquer mensagens que não foram excluídos ficará visível novamente.

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

Você pode obter uma estimativa do número de mensagens em uma fila. O método **FetchAttributes** solicita o queueservice para recuperar os atributos de fila, incluindo a contagem de mensagens. A propriedade **ApproximateMethodCount** retorna o último valor recuperado pelo método **FetchAttributes** , sem chamar o queueservice.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-async-await-pattern-with-common-queueapis"></a>Usar padrão esperar assíncrono com queueAPIs comuns

Este exemplo mostra como usar o padrão de esperar assíncrono com queueAPIs comuns. O exemplo chama a versão assíncrona de cada um dos métodos determinados, isso pode ser visto pela correção de POST assíncrono de cada método. Quando um método assíncrono é usado o assíncrono-aguardar padrão suspende a execução local até que a chamada seja concluída. Esse comportamento permite que o segmento atual para outro trabalho que ajuda a evitar gargalos de desempenho e melhora a capacidade de resposta geral do seu aplicativo. Para obter mais detalhes sobre como usar o padrão de espera assíncrono no .NET Consulte [assíncrono e espera (c# e Visual Basic)] (https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>Excluir uma fila

Para excluir uma fila e todas as mensagens contidas nele, chame o método **Delete** no objeto fila.

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]