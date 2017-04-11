<properties
    pageTitle="Como usar o armazenamento de fila do Java | Microsoft Azure"
    description="Aprenda a usar o serviço de fila do Azure para criar e excluir filas e inserir, obter e excluir mensagens. Amostras escritas em Java."
    services="storage"
    documentationCenter="java"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-queue-storage-from-java"></a>Como usar o armazenamento de filas de Java

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Visão geral

Este guia mostrará como executar cenários comuns usando o serviço de armazenamento de fila do Azure. Os exemplos são gravados em Java e usam o [SDK de armazenamento do Azure para Java][]. Os cenários cobertos incluem **inserindo**, **inspecionar**, **Obtendo**e **Excluindo** mensagens da fila, bem como **criar** e **Excluir** filas. Para obter mais informações sobre filas, consulte a seção [próximas etapas](#Next-Steps) .

Observação: Um SDK está disponível para os desenvolvedores que usam o armazenamento do Azure em dispositivos Android. Para obter mais informações, consulte o [SDK de armazenamento do Azure para Android][].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Criar um aplicativo Java

Neste guia, você usará os recursos de armazenamento que podem ser executados dentro de um aplicativo Java localmente ou no código em execução dentro de uma função da web ou a função de trabalho no Azure.

Para fazer isso, você precisará instalar o Kit de desenvolvimento de Java (JDK) e crie uma conta de armazenamento do Azure em sua assinatura do Azure. Depois que você tiver feito isso, você precisa verificar se o seu sistema de desenvolvimento atende os requisitos mínimos e as dependências que são listadas no repositório [SDK de armazenamento do Azure para Java][] no GitHub. Se seu sistema atenda a esses requisitos, você pode seguir as instruções para baixar e instalar as bibliotecas de armazenamento do Azure para Java em seu sistema desse repositório. Quando tiver concluído essas tarefas, você poderá criar um aplicativo Java que usa os exemplos deste artigo.

## <a name="configure-your-application-to-access-queue-storage"></a>Configurar seu aplicativo para acessar o armazenamento de fila

Adicione as seguintes instruções de importação para a parte superior do arquivo Java onde você deseja usar o armazenamento do Azure APIs para acessar filas:

    // Include the following imports to use queue APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.queue.*;

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

## <a name="how-to-create-a-queue"></a>Como: criar uma fila

Um objeto de **CloudQueueClient** permite que você obtenha objetos de referência para filas. O código a seguir cria um objeto **CloudQueueClient** . (Anotação: existem maneiras adicionais para criar objetos **CloudStorageAccount** ; para obter mais informações, consulte **CloudStorageAccount** na [Referência de SDK do Azure armazenamento cliente].)

Use o objeto **CloudQueueClient** para obter uma referência à fila que você deseja usar. Você pode criar fila se ele não existir.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the queue client.
       CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

       // Retrieve a reference to a queue.
       CloudQueue queue = queueClient.getQueueReference("myqueue");

       // Create the queue if it doesn't already exist.
       queue.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-add-a-message-to-a-queue"></a>Como: adicionar uma mensagem para uma fila

Para inserir uma mensagem em uma fila existente, primeiro crie um novo **CloudQueueMessage**. Em seguida, chame o método **addMessage** . Um **CloudQueueMessage** pode ser criadas de uma cadeia de caracteres (no formato UTF-8) ou uma matriz de bytes. Aqui está o código que cria uma fila (se ela não existir) e insere a mensagem "Olá, mundo".

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Create the queue if it doesn't already exist.
        queue.createIfNotExists();

        // Create a message and add it to the queue.
        CloudQueueMessage message = new CloudQueueMessage("Hello, World");
        queue.addMessage(message);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-peek-at-the-next-message"></a>Como: inspecionar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-lo da fila chamando **peekMessage**.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Peek at the next message.
        CloudQueueMessage peekedMessage = queue.peekMessage();

        // Output the message value.
        if (peekedMessage != null)
        {
          System.out.println(peekedMessage.getMessageContentAsString());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: alterar o conteúdo de uma mensagem na fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representa uma tarefa de trabalho, você pode usar esse recurso para atualizar o status da tarefa. O código a seguir atualiza a mensagem de fila com novo conteúdo e define o tempo limite de visibilidade para estender outro 60 segundos. Isso salva o estado de trabalho associado à mensagem e fornece o cliente minuto outro para continuar trabalhando na mensagem. Você pode usar essa técnica para controlar fluxos de trabalho de várias etapas em mensagens de fila sem precisar recomeçar desde o começo se uma etapa de processamento falhar devido a falha de hardware ou software. Normalmente, você prefere manter uma contagem de repetição também e se a mensagem for repetida mais de *n* vezes, você deve excluí-lo. Isso protege contra uma mensagem que dispara um erro de aplicativo cada vez que ela é processada.

As seguintes pesquisas de amostra de código por meio de fila de mensagens, localiza a primeira mensagem que corresponde a "Olá, mundo" para o conteúdo, e em seguida, modifica o conteúdo da mensagem e sai.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // The maximum number of messages that can be retrieved is 32.
        final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

        // Loop through the messages in the queue.
        for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
        {
            // Check for a specific string.
            if (message.getMessageContentAsString().equals("Hello, World"))
            {
                // Modify the content of the first matching message.
                message.setMessageContent("Updated contents.");
                // Set it to be visible in 30 seconds.
                EnumSet<MessageUpdateFields> updateFields =
                    EnumSet.of(MessageUpdateFields.CONTENT,
                    MessageUpdateFields.VISIBILITY);
                // Update the message.
                queue.updateMessage(message, 30, updateFields, null, null);
                break;
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Como alternativa, o código de exemplo a seguir atualiza apenas a primeira mensagem visível na fila.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve the first visible message in the queue.
        CloudQueueMessage message = queue.retrieveMessage();

        if (message != null)
        {
            // Modify the message content.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 60 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 60, updateFields, null, null);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-get-the-queue-length"></a>Como: obter o comprimento da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O método **downloadAttributes** solicita o serviço de fila para vários valores atuais, incluindo uma contagem de quantas mensagens estão em uma fila. A contagem só está aproximada porque mensagens podem ser adicionadas ou removidas após o serviço de fila responde à sua solicitação. O método **getApproximateMessageCount** retorna o último valor recuperado pela chamada para **downloadAttributes**, sem chamar o serviço de fila.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

       // Download the approximate message count from the server.
        queue.downloadAttributes();

        // Retrieve the newly cached approximate message count.
        long cachedMessageCount = queue.getApproximateMessageCount();

        // Display the queue length.
        System.out.println(String.format("Queue length: %d", cachedMessageCount));
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-dequeue-the-next-message"></a>Como: retirar a próxima mensagem

Seu código desenfileira uma mensagem de uma fila em duas etapas. Quando você chamar **retrieveMessage**, você recebe a próxima mensagem na fila. Uma mensagem retornada de **retrieveMessage** fica invisível para qualquer outro código ler mensagens dessa fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para concluir a remoção a mensagem da fila, você também deve chamar **deleteMessage**. Esse processo de duas etapas de remoção de uma mensagem garante que, se seu código falha processar uma mensagem devido a falha de hardware ou software, outra instância do seu código pode receberá a mesma mensagem e tente novamente. Seu código chama **deleteMessage** direita após a mensagem foi processada.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve the first visible message in the queue.
        CloudQueueMessage retrievedMessage = queue.retrieveMessage();

        if (retrievedMessage != null)
        {
            // Process the message in less than 30 seconds, and then delete the message.
            queue.deleteMessage(retrievedMessage);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }


## <a name="additional-options-for-dequeuing-messages"></a>Opções adicionais para mensagens de desenfileiramento

Há duas maneiras que você pode personalizar a recuperação de mensagens de uma fila. Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir o tempo limite invisibility maior ou menor, permitindo a seu código mais ou menos tempo para processar totalmente cada mensagem.

O exemplo de código a seguir usa o método **retrieveMessages** para receber mensagens de 20 em uma chamada. Em seguida, ele processa cada mensagem usando **um loop** . Ele também define o tempo limite invisibility para cinco minutos (300 segundos) para cada mensagem. Observe que os cinco minutos inicia para todas as mensagens ao mesmo tempo, portanto quando cinco minutos passaram desde a chamada para **retrieveMessages**, quaisquer mensagens que não foram excluídas ficará visíveis novamente.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
        for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
            // Do processing for all messages in less than 5 minutes,
            // deleting each message after processing.
            queue.deleteMessage(message);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-list-the-queues"></a>Como: filas de lista

Para obter uma lista das filas atuais, ligue para o método **CloudQueueClient.listQueues()** , que retornará uma coleção de objetos **CloudQueue** .

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient =
            storageAccount.createCloudQueueClient();

        // Loop through the collection of queues.
        for (CloudQueue queue : queueClient.listQueues())
        {
            // Output each queue name.
            System.out.println(queue.getName());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-a-queue"></a>Como: excluir uma fila

Para excluir uma fila e todas as mensagens contidas nele, chame o método **deleteIfExists** no objeto **CloudQueue** .

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Delete the queue if it exists.
        queue.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu as Noções básicas do armazenamento de fila, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

- [Armazenamento do Azure SDK para Java][]
- [Referência SDK do cliente de armazenamento do Azure][]
- [Serviços de armazenamento do Azure API REST][]
- [Blog da equipe de armazenamento do Azure][]

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Armazenamento do Azure SDK para Java]: https://github.com/azure/azure-storage-java
[Armazenamento do Azure SDK para Android]: https://github.com/azure/azure-storage-android
[Referência SDK do cliente de armazenamento do Azure]: http://dl.windowsazure.com/storage/javadoc/
[Serviços de armazenamento do Azure API REST]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Blog da equipe de armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
