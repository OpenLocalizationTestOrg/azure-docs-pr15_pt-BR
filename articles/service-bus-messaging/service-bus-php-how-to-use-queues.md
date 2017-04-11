<properties 
    pageTitle="Como usar filas de barramento de serviço com PHP | Microsoft Azure" 
    description="Saiba como usar filas de barramento de serviço no Azure. Exemplos de código escritos em PHP." 
    services="service-bus" 
    documentationCenter="php" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>Como usar filas de barramento de serviço

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este guia mostra como usar filas de barramento de serviço. Os exemplos são gravados no PHP e usam o [SDK do Azure para PHP](../php-download-sdk.md). Os cenários cobertos incluem **Criando filas**, **Enviar e receber mensagens**e **excluindo filas**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-php-application"></a>Criar um aplicativo PHP

O único requisito para criar um aplicativo PHP que acessa o serviço do Azure Blob é de referência de classes no [SDK do Azure para PHP](../php-download-sdk.md) de dentro do seu código. Você pode usar as ferramentas de desenvolvimento para criar o aplicativo, ou o bloco de notas.

> [AZURE.NOTE] Sua instalação do PHP também deve ter a [extensão de OpenSSL](http://php.net/openssl) instalado e ativado.

Neste guia, você usará os recursos de serviço que podem ser chamados de dentro de um aplicativo PHP localmente ou em código em execução dentro de uma função web Azure, uma função de trabalho ou um site.

## <a name="get-the-azure-client-libraries"></a>Obter o Azure cliente bibliotecas

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar barramento de serviço

Para usar a fila de barramento de serviço APIs, faça o seguinte:

1. Fazer referência ao arquivo de carregador usando o [require_once] [ require_once] instrução.
2. Fazer referência a quaisquer classes que você pode usar.

O exemplo a seguir mostra como incluir o arquivo de carregador e fazer referência à classe **ServicesBuilder** .

> [AZURE.NOTE] Este exemplo (e outros exemplos neste artigo) supõe que você instalou as bibliotecas de cliente do PHP para Azure via compositor. Se você instalou as bibliotecas manualmente ou como um pacote de PEREIRAS, você deve fazer referência o arquivo de carregador **WindowsAzure.php** .

```
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Nos exemplos a seguir, o `require_once` instrução sempre será mostrada, mas somente as classes necessárias para executar o exemplo referenciadas.

## <a name="set-up-a-service-bus-connection"></a>Configurar uma conexão de barramento de serviço

Para criar uma instância de um cliente de barramento de serviço, primeiro você deve ter uma cadeia de conexão válida neste formato:

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

Onde o **ponto de extremidade** é geralmente do formato `[yourNamespace].servicebus.windows.net`.

Para criar qualquer cliente de serviço Azure, você deve usar a classe **ServicesBuilder** . É possível:

* Passe a cadeia de conexão diretamente para ele.
* Use o **CloudConfigurationManager (CCM)** para verificar várias origens externas para a cadeia de conexão:
    * Por padrão, ele vem com suporte para uma fonte externa - variáveis de ambiente
    * Você pode adicionar novas fontes estendendo a classe de **ConnectionStringSource**

Para os exemplos descritos aqui, a cadeia de conexão será passada diretamente.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="how-to-create-a-queue"></a>Como: criar uma fila

Você pode executar operações de gerenciamento de filas de barramento de serviço por meio da classe **ServiceBusRestProxy** . Um objeto de **ServiceBusRestProxy** é construído por meio do método de fábrica **ServicesBuilder::createServiceBusService** com uma cadeia de conexão apropriado que encapsula as permissões do token para gerenciá-lo.

O exemplo a seguir mostra como criar uma instância de um **ServiceBusRestProxy** e chame **ServiceBusRestProxy -> createQueue** para criar uma fila denominada `myqueue` dentro de uma `MySBNamespace` namespace do serviço:

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {
    $queueInfo = new QueueInfo("myqueue");
        
    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [AZURE.NOTE] Você pode usar o `listQueues` método `ServiceBusRestProxy` objetos para verificar se já existe uma fila com um nome especificado dentro de um namespace.

## <a name="how-to-send-messages-to-a-queue"></a>Como: enviar mensagens para uma fila

Para enviar uma mensagem para uma fila de barramento de serviço, seu aplicativo chama o método **ServiceBusRestProxy -> sendQueueMessage** . O código a seguir mostra como enviar uma mensagem para o `myqueue` fila criada anteriormente dentro do `MySBNamespace` namespace do serviço.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");
    
    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Mensagens enviadas para (e recebidas de) filas de barramento de serviço são instâncias da classe **BrokeredMessage** . Objetos de **BrokeredMessage** têm um conjunto de métodos padrão (como **getLabel**, **getTimeToLive**, **setLabel**e **setTimeToLive**) e as propriedades que são usadas para manter as propriedades personalizadas específicas do aplicativo e um corpo de dados de aplicativo aleatório.

Filas de barramento de serviço oferecem suporte a um tamanho máximo de mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB no [nível Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizados, pode ter um tamanho máximo de 64 KB. Não há nenhum limite o número de mensagens mantido em uma fila, mas há um limite no tamanho total das mensagens apresentadas por uma fila. Esse limite superior no tamanho da fila é 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Como receber mensagens de uma fila

A melhor maneira de receber mensagens de uma fila é usar um método **ServiceBusRestProxy -> receiveQueueMessage** . As mensagens podem ser recebidas em dois modos diferentes: **ReceiveAndDelete** (padrão) e **PeekLock**.

Quando receber usando o modo de **ReceiveAndDelete** , é uma operação de captura de único - ou seja, quando barramento de serviço recebe uma solicitação de leitura para uma mensagem em uma fila, ele marca a mensagem como sendo consumida e retorna para o aplicativo. Modo de **ReceiveAndDelete** é o modelo mais simples e funciona melhor para cenários em que um aplicativo pode suportar não processando uma mensagem em caso de uma falha. Para entender isso, considere um cenário em que o consumidor emite a solicitação de recebimento e então trava antes de processá-lo. Porque barramento de serviço será marcou a mensagem como sendo consumida, em seguida, quando o aplicativo é reiniciado e começa consumindo mensagens novamente, ele será ter perdido a mensagem que foi consumida antes da falha.

No modo de **PeekLock** , você receber uma mensagem se torna uma operação de dois estágios, que possibilita aos aplicativos de suporte que não é possível suportar mensagens ausentes. Quando barramento de serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, bloqueia-lo para impedir que outros consumidores recebê-lo e retorna para o aplicativo. Depois que o aplicativo termina de processar a mensagem (ou armazena confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento passando a mensagem recebida para **ServiceBusRestProxy -> deleteMessage**. Quando barramento de serviço vê a **deleteMessage** chamar, ele será marcar a mensagem como sendo consumida e removê-lo da fila.

O exemplo a seguir mostra como uma mensagem pode ser recebida e processada usando o modo de **PeekLock** (não o modo padrão).

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();
        
    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";
        
    /*---------------------------
        Process message here.
    ----------------------------*/
        
    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/windowsazure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como: manipular falhas do aplicativo e mensagens ilegíveis

Barramento de serviço fornece funcionalidade para ajudá-lo a recuperar normalmente de erros em seu aplicativo ou dificuldades processamento de uma mensagem. Se um aplicativo receptor não é possível processar a mensagem por algum motivo, ele pode chamar o método **unlockMessage** na mensagem recebida (em vez do método de **deleteMessage** ). Isso fará com que o barramento de serviço desbloquear a mensagem dentro da fila e disponibilizá-lo a ser recebida novamente, pelo mesmo aplicativo consumindo ou por outro aplicativo consumindo.

Há também um tempo limite associado a uma mensagem bloqueada dentro da fila e se o aplicativo falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se o aplicativo trava), e em seguida, barramento de serviço será desbloquear a mensagem automaticamente e disponibilizá-lo a ser recebida novamente.

Se o aplicativo trava após processamento da mensagem, mas antes da solicitação de **deleteMessage** é emitida, em seguida, a mensagem será entregue novamente para o aplicativo quando ele for reiniciado. Isso normalmente é chamado **Pelo menos uma vez processamento**; ou seja, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser entregue novamente. Se o cenário não pode suportar processamento duplicado, em seguida, adicionar lógica adicional para aplicativos para controlar a entrega de mensagem duplicada é recomendável. Isso geralmente é feito usando o método **getMessageId** da mensagem, que permanece constante entre tentativas de entrega.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos de filas de barramento de serviço, consulte [filas, tópicos e assinaturas][] para obter mais informações.

Para obter mais informações, consulte também o [PHP Developer Center](/develop/php/).

[Filas, tópicos e assinaturas]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once

 
