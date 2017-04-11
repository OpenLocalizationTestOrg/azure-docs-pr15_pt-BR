<properties
    pageTitle="Introdução ao armazenamento de fila e Visual Studio conectado serviços (ASP.NET 5) | Microsoft Azure"
    description="Como começar a usar o armazenamento do Azure fila em um projeto de 5 do ASP.NET no Visual Studio"
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

# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-5"></a>Introdução ao armazenamento de fila e Visual Studio conectado serviços (ASP.NET 5)

[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

##<a name="overview"></a>Visão geral

Este artigo descreve como começar a usar o armazenamento do Azure fila no Visual Studio depois que você tenha criado ou referenciado uma conta de armazenamento do Azure em um projeto do ASP.NET 5 usando a caixa de diálogo do Visual Studio **Adicionar serviços conectados** . A operação de **Adicionar serviços conectados** instala os pacotes NuGet apropriados para acessar o armazenamento do Azure em seu projeto e adiciona a cadeia de conexão da conta de armazenamento para seus arquivos de configuração do projeto.

Armazenamento de fila Azure é um serviço para armazenar grandes números de mensagens que podem ser acessadas a partir de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma mensagem de única fila pode ter até 64 quilobytes (KB), e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento.

Para começar, primeiro é necessário criar uma fila Azure em sua conta de armazenamento. Mostraremos como criar uma fila no código. Também mostraremos como realizar operações de fila básicas, como adição, modificação, lendo e Removendo mensagens de fila. Os exemplos são escritos em C\# código e usar a biblioteca de cliente de armazenamento do Azure para .NET. Para obter mais informações sobre ASP.NET, consulte [ASP.NET](http://www.asp.net).

**Observação:** Algumas das APIs que executam chamadas para o armazenamento do Azure no ASP.NET 5 são assíncronas. Para obter mais informações, consulte [programação assíncrona com assíncrono e espera](http://msdn.microsoft.com/library/hh191443.aspx) . O código abaixo presume métodos de programação assíncrono estão sendo usados.

- Consulte [Introdução ao armazenamento de fila do Azure usando .NET](storage-dotnet-how-to-use-queues.md) para saber mais sobre manipular programaticamente filas.
- Consulte a [documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o armazenamento do Azure.
- Consulte a [documentação de serviços de nuvem](https://azure.microsoft.com/documentation/services/cloud-services/) para obter informações gerais sobre os serviços de nuvem Azure.
- Consulte [ASP.NET](http://www.asp.net) para obter mais informações sobre a programação de aplicativos ASP.NET.





##<a name="access-queues-in-code"></a>Filas de acesso no código

Para acessar filas em projetos ASP.NET 5, você precisa incluir os seguintes itens para qualquer arquivo de origem c# que acessa armazenamento de fila Azure.

1. Verifique se que as declarações de namespace na parte superior do arquivo c# incluem essas instruções **usando** .

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Obtenha um objeto de **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a sua cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenha um objeto de **CloudQueueClient** para fazer referência os objetos de fila em sua conta de armazenamento.  

        // Create the CloudQueueClient object for the storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenha um objeto de **CloudQueue** para fazer referência a uma fila específica.

        // Get a reference to the CloudQueue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**Observação:** Use todo o código acima frente ao código dos exemplos a seguir.

###<a name="create-a-queue-in-code"></a>Criar uma fila no código

Para criar a fila Azure no código, basta adicione uma chamada para **CreateIfNotExistsAsync**.

    // Create the CloudQueue if it does not exist.
    await messageQueue.CreateIfNotExistsAsync();

##<a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem para uma fila

Para inserir uma mensagem em uma fila existente, criar um novo objeto de **CloudQueueMessage** , em seguida, chame o método **AddMessageAsync** .

Um objeto de **CloudQueueMessage** pode ser criado de uma cadeia de caracteres (no formato UTF-8) ou uma matriz de bytes.

Aqui está um exemplo que insere a mensagem 'Olá, mundo'.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    await messageQueue.AddMessageAsync(message);

##<a name="read-a-message-in-a-queue"></a>Ler uma mensagem em uma fila

Você pode inspecionar a mensagem na frente de uma fila sem removê-lo da fila chamando o método **PeekMessageAsync** .

    // Peek the next message in the queue. 
    CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();


##<a name="read-and-remove-a-message-in-a-queue"></a>Ler e remover uma mensagem em uma fila

Seu código pode remover (retirar) uma mensagem de uma fila em duas etapas.
1. Ligue para **GetMessageAsync** para obter a próxima mensagem na fila. Uma mensagem retornada de **GetMessageAsync** fica invisível para qualquer outro código ler mensagens dessa fila. Por padrão, essa mensagem permanece invisível por 30 segundos.
2.  Para concluir a remoção a mensagem da fila, ligue para **DeleteMessageAsync**.

Esse processo de duas etapas de remoção de uma mensagem garante que, se seu código falha processar uma mensagem devido a falha de hardware ou software, outra instância do seu código pode receberá a mesma mensagem e tente novamente. O código a seguir chama **DeleteMessageAsync** direita após a mensagem foi processada.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

    // Process the message in less than 30 seconds.

    // Then delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);

## <a name="leverage-additional-options-for-dequeuing-messages"></a>Aproveite as opções adicionais para mensagens de desenfileiramento

Há duas maneiras que você pode personalizar a recuperação de mensagens de uma fila.
Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir o tempo limite invisibility maior ou menor, permitindo a seu código mais ou menos tempo para processar totalmente cada mensagem. O exemplo de código a seguir usa o método **GetMessages** para receber mensagens de 20 em uma chamada. Em seguida, ele processa cada mensagem usando um loop **foreach** . Ele também define o tempo limite invisibility para 5 minutos para cada mensagem. Observe que o início de 5 minutos para todas as mensagens ao mesmo tempo, transmitido após ter de 5 minutos após a chamada para **GetMessages**, quaisquer mensagens que não foram excluídos ficam visíveis novamente.

    // Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
    //   delete each message after processing.

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>Obtenha o comprimento da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O método **FetchAttributes** solicita o serviço de fila para recuperar os atributos de fila, incluindo a contagem de mensagens. A propriedade **ApproximateMethodCount** retorna o último valor recuperado pelo método **FetchAttributes** , sem chamar o serviço de fila.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display the number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Usar os padrões de esperar assíncrono com fila comuns APIs

Este exemplo mostra como usar o padrão de esperar assíncrono com fila comuns APIs. O exemplo chama a versão assíncrona de cada um dos métodos determinados. Isso pode ser visto pela correção de POST assíncrono de cada método. Quando um método assíncrono é usado, o padrão de esperar assíncrono suspende execução local até que a chamada seja concluída. Esse comportamento permite que o segmento atual para outro trabalho que ajuda a evitar gargalos de desempenho e melhora a capacidade de resposta geral do seu aplicativo. Para obter mais detalhes sobre como usar o padrão de espera assíncrono no .NET, consulte [assíncrono e espera (c# e Visual Basic)] (https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to add to the queue.
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message.
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");
## <a name="delete-a-queue"></a>Excluir uma fila

Para excluir uma fila e todas as mensagens contidas nele, chame o método **Delete** no objeto fila.

    // Delete the queue.
    messageQueue.Delete();


##<a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
