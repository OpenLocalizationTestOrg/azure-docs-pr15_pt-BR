<properties
    pageTitle="Como usar o armazenamento de fila (C++) | Microsoft Azure"
    description="Saiba como usar o serviço de armazenamento de fila no Azure. Amostras são escritas em C++."
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

# <a name="how-to-use-queue-storage-from-c"></a>Como usar o armazenamento de fila do C++  

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Visão geral
Este guia mostrará como executar cenários comuns usando o serviço de armazenamento de fila do Azure. Os exemplos estão escritos em C++ e usam a [Biblioteca de cliente de armazenamento do Azure para C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Os cenários cobertos incluem **inserindo**, **inspecionar**, **Obtendo**e **Excluindo** mensagens da fila, bem como **criar e excluir filas**.

>[AZURE.NOTE] Este guia se destina a biblioteca de cliente de armazenamento do Azure para C++ versão 1.0.0 e acima. A versão recomendada é a biblioteca de cliente do armazenamento 2.2.0, que está disponível por meio do [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](http://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Criar um aplicativo de C++  
Neste guia, você usará os recursos de armazenamento que podem ser executados dentro de um aplicativo de C++.

Para fazer isso, você precisará instalar a biblioteca de cliente de armazenamento do Azure para C++ e criar uma conta de armazenamento do Azure em sua assinatura do Azure.

Para instalar a biblioteca de cliente de armazenamento do Azure para C++, você pode usar os seguintes métodos:

-   **Linux:** Siga as instruções fornecidas na página da [Biblioteca de cliente de armazenamento do Azure para C++ Leiame](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .
-   **Windows:** No Visual Studio, clique em **Ferramentas > Gerenciador de pacotes do NuGet > Package Manager Console**. Digite o seguinte comando no [console de NuGet Package Manager](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e pressione **ENTER**.

        Install-Package wastorage

## <a name="configure-your-application-to-access-queue-storage"></a>Configurar seu aplicativo para acessar o armazenamento de fila
Adicione que o seguinte incluir instruções para a parte superior do arquivo C++ onde você deseja usar o armazenamento do Azure APIs para acessar filas:  

    #include "was/storage_account.h"
    #include "was/queue.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de caracteres de conexão de armazenamento do Azure

Um cliente de armazenamento do Azure usa uma cadeia de conexão de armazenamento para armazenar pontos de extremidade e as credenciais para acessar os serviços de gerenciamento de dados. Quando em execução em um aplicativo cliente, você deve fornecer a cadeia de conexão de armazenamento no formato a seguir, usando o nome da sua conta de armazenamento e a tecla de acesso de armazenamento para a conta de armazenamento listada no [Portal do Azure](https://portal.azure.com) para os valores *AccountName* e *AccountKey* . Para obter informações sobre contas de armazenamento e as teclas de acesso, consulte [Sobre contas de armazenamento do Azure](storage-create-storage-account.md). Este exemplo mostra como você pode declarar um campo estático para armazenar a cadeia de conexão:  

    // Define the connection-string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Para testar seu aplicativo em seu computador local do Windows, você pode usar o Microsoft Azure [emulador de armazenamento](storage-use-emulator.md) que está instalado com o [SDK do Azure](https://azure.microsoft.com/downloads/). Emulador de armazenamento é um utilitário que simula os serviços de Blob, fila e tabela disponíveis no Azure em sua máquina de desenvolvimento local. O exemplo a seguir mostra como você pode declarar um campo estático para armazenar a cadeia de conexão para seu emulador de armazenamento local:  

    // Define the connection-string with Azure Storage Emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Para iniciar o emulador de armazenamento do Azure, selecione o botão **Iniciar** ou pressione a tecla do **Windows** . Comece a digitar **Emulador de armazenamento do Azure**e selecione **Emulador de armazenamento do Microsoft Azure** na lista de aplicativos.

Os exemplos a seguir presumem que você usou um destes dois métodos para obter a cadeia de conexão de armazenamento.

## <a name="retrieve-your-connection-string"></a>Recuperar sua cadeia de caracteres de conexão
Você pode usar a classe **cloud_storage_account** para representar suas informações de conta de armazenamento. Para recuperar informações de sua conta de armazenamento da cadeia de conexão de armazenamento, você pode usar o método de **Analisar** .

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

## <a name="how-to-create-a-queue"></a>Como: criar uma fila
Um objeto de **cloud_queue_client** permite que você obtenha objetos de referência para filas. O código a seguir cria um objeto **cloud_queue_client** .

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create a queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

Use o objeto **cloud_queue_client** para obter uma referência à fila que você deseja usar. Você pode criar fila se ele não existir.

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Create the queue if it doesn't already exist.
    queue.create_if_not_exists();  

## <a name="how-to-insert-a-message-into-a-queue"></a>Como: inserir uma mensagem em uma fila
Para inserir uma mensagem em uma fila existente, primeiro crie um novo **cloud_queue_message**. Em seguida, chame o método de **add_message** . Um **cloud_queue_message** podem ser criadas de uma cadeia de caracteres ou uma matriz de **bytes** . Aqui está o código que cria uma fila (se ela não existir) e insere a mensagem 'Olá, mundo':

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Create the queue if it doesn't already exist.
    queue.create_if_not_exists();

    // Create a message and add it to the queue.
    azure::storage::cloud_queue_message message1(U("Hello, World"));
    queue.add_message(message1);  

## <a name="how-to-peek-at-the-next-message"></a>Como: inspecionar a próxima mensagem
Você pode inspecionar a mensagem na frente de uma fila sem removê-lo da fila chamando o método **peek_message** .

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Peek at the next message.
    azure::storage::cloud_queue_message peeked_message = queue.peek_message();

    // Output the message content.
    std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: alterar o conteúdo de uma mensagem na fila
Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representa uma tarefa de trabalho, você pode usar esse recurso para atualizar o status da tarefa. O código a seguir atualiza a mensagem de fila com novo conteúdo e define o tempo limite de visibilidade para estender outro 60 segundos. Isso salva o estado de trabalho associado à mensagem e fornece o cliente minuto outro para continuar trabalhando na mensagem. Você pode usar essa técnica para controlar fluxos de trabalho de várias etapas em mensagens de fila sem precisar recomeçar desde o começo se uma etapa de processamento falhar devido a falha de hardware ou software. Normalmente, você prefere manter uma contagem de repetição também e se a mensagem for repetida mais de n vezes, você deve excluí-lo. Isso protege contra uma mensagem que dispara um erro de aplicativo cada vez que ela é processada.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_conection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Get the message from the queue and update the message contents.
    // The visibility timeout "0" means make it visible immediately.
    // The visibility timeout "60" means the client can get another minute to continue
    // working on the message.
    azure::storage::cloud_queue_message changed_message = queue.get_message();

    changed_message.set_content(U("Changed message"));
    queue.update_message(changed_message, std::chrono::seconds(60), true);

    // Output the message content.
    std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  

## <a name="how-to-de-queue-the-next-message"></a>Como: retirar a próxima mensagem da fila
O código do seu filas eliminação de uma mensagem de uma fila em duas etapas. Quando você chamar **get_message**, você recebe a próxima mensagem na fila. Uma mensagem retornada de **get_message** fica invisível para qualquer outro código ler mensagens dessa fila. Para concluir a remoção a mensagem da fila, você também deve chamar **delete_message**. Esse processo de duas etapas de remoção de uma mensagem garante que, se seu código falha processar uma mensagem devido a falha de hardware ou software, outra instância do seu código pode receberá a mesma mensagem e tente novamente. Seu código chama **delete_message** direita após a mensagem foi processada.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Get the next message.
    azure::storage::cloud_queue_message dequeued_message = queue.get_message();
    std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

    // Delete the message.
    queue.delete_message(dequeued_message);

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Como: utilizar opções adicionais para eliminação filas de mensagens
Há duas maneiras que você pode personalizar a recuperação de mensagens de uma fila. Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir o tempo limite invisibility maior ou menor, permitindo a seu código mais ou menos tempo para processar totalmente cada mensagem. O exemplo de código a seguir usa o método **get_messages** para receber mensagens de 20 em uma chamada. Em seguida, ele processa cada mensagem usando **um loop** . Ele também define o tempo limite invisibility para cinco minutos para cada mensagem. Observe que o inicia de 5 minutos para todas as mensagens ao mesmo tempo, após ter de 5 minutos transmitido desde que a chamada para **get_messages**, quaisquer mensagens que não foram excluídos ficará visível novamente.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
    // 5 minutes (300 seconds).
    azure::storage::queue_request_options options;
    azure::storage::operation_context context;

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

    for (auto it = messages.cbegin(); it != messages.cend(); ++it)
    {
        // Display the contents of the message.
        std::wcout << U("Get: ") << it->content_as_string() << std::endl;
    }

## <a name="how-to-get-the-queue-length"></a>Como: obter o comprimento da fila
Você pode obter uma estimativa do número de mensagens em uma fila. O método **download_attributes** solicita o serviço de fila para recuperar os atributos de fila, incluindo a contagem de mensagens. O método **approximate_message_count** obtém o número aproximado de mensagens na fila.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Fetch the queue attributes.
    queue.download_attributes();

    // Retrieve the cached approximate message count.
    int cachedMessageCount = queue.approximate_message_count();

    // Display number of messages.
    std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  

## <a name="how-to-delete-a-queue"></a>Como: excluir uma fila
Para excluir uma fila e todas as mensagens contidas nele, chame o método **delete_queue_if_exists** no objeto fila.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // If the queue exists and delete it.
    queue.delete_queue_if_exists();  

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu as Noções básicas do armazenamento de fila, siga estes links para saber mais sobre o armazenamento do Azure.

-   [Como usar o armazenamento de Blob do C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Como usar o armazenamento de tabela do C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Recursos de armazenamento do Azure de lista em C++](storage-c-plus-plus-enumeration.md)
-   [Biblioteca de cliente de armazenamento para referência de C++](http://azure.github.io/azure-storage-cpp)
-   [Documentação de armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)

