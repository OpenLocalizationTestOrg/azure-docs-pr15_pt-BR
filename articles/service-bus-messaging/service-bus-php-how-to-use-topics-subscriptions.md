<properties 
    pageTitle="Como usar tópicos de barramento de serviço com PHP | Microsoft Azure" 
    description="Saiba como usar tópicos de barramento de serviço com PHP no Azure." 
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
    ms.date="10/14/2016" 
    ms.author="sethm"/>


# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Como usar assinaturas e tópicos de barramento de serviço

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo mostra como usar assinaturas e tópicos de barramento de serviço. Os exemplos são gravados no PHP e usam o [SDK do Azure para PHP](../php-download-sdk.md). Os cenários cobertos incluem **Criando tópicos e assinaturas**, **Criando filtros de assinatura**, **enviando mensagens para um tópico**, **receber mensagens de uma assinatura**e **Excluindo tópicos e assinaturas**.

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-php-application"></a>Criar um aplicativo PHP

O único requisito para criar um aplicativo PHP que acessa o serviço do Azure Blob é fazer referência classes no [SDK do Azure para PHP](../php-download-sdk.md) de dentro do seu código. Você pode usar as ferramentas de desenvolvimento para criar o aplicativo, ou o bloco de notas.

> [AZURE.NOTE] Sua instalação do PHP também deve ter a [extensão de OpenSSL](http://php.net/openssl) instalado e ativado.

Este artigo descreve como usar os recursos de serviço que podem ser chamados dentro de um aplicativo PHP localmente ou em código em execução dentro de uma função web Azure, uma função de trabalho ou um site.

## <a name="get-the-azure-client-libraries"></a>Obter o Azure cliente bibliotecas

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar barramento de serviço

Para usar as APIs de barramento de serviço:

1. Fazer referência ao arquivo de carregador usando o [require_once] [ require-once] instrução.
2. Fazer referência a quaisquer classes que você pode usar.

O exemplo a seguir mostra como incluir o arquivo de carregador e fazer referência à classe **ServiceBusService** .

> [AZURE.NOTE] Este exemplo (e outros exemplos neste artigo) supõe que você instalou as bibliotecas de cliente do PHP para Azure via compositor. Se você instalou as bibliotecas manualmente ou como um pacote de PEREIRAS, você deve fazer referência o arquivo de carregador **WindowsAzure.php** .

```
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Nos exemplos a seguir, o `require_once` instrução sempre será mostrada, mas somente as classes necessárias para executar o exemplo referenciadas.

## <a name="set-up-a-service-bus-connection"></a>Configurar uma conexão de barramento de serviço

Para criar uma instância de um cliente de barramento de serviço primeiro você deve ter uma cadeia de conexão válida neste formato:

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

Onde `Endpoint` normalmente é do formato `https://[yourNamespace].servicebus.windows.net`.

Para criar qualquer cliente de serviço Azure, você deve usar a classe **ServicesBuilder** . É possível:

* Passe a cadeia de conexão diretamente para ele.
* Use o **CloudConfigurationManager (CCM)** para verificar várias origens externas para a cadeia de conexão:
    * Por padrão, ele vem com suporte para uma fonte externa - variáveis de ambiente.
    * Você pode adicionar novas fontes estendendo a classe **ConnectionStringSource** .

Para os exemplos descritos aqui, a cadeia de conexão é passada diretamente.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
    
$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Criar um tópico

Você pode executar operações de gerenciamento de tópicos de barramento de serviço por meio da classe **ServiceBusRestProxy** . Um objeto de **ServiceBusRestProxy** é construído por meio do método de fábrica **ServicesBuilder::createServiceBusService** com uma cadeia de conexão apropriado que encapsula as permissões do token para gerenciá-lo.

O exemplo a seguir mostra como criar uma instância de um **ServiceBusRestProxy** e chame **ServiceBusRestProxy -> createTopic** para criar um tópico chamado `mytopic` dentro de uma `MySBNamespace` namespace:

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;
    
// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {       
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
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

> [AZURE.NOTE] Você pode usar o `listTopics` método `ServiceBusRestProxy` objetos para verificar se já existe um tópico com um nome especificado dentro de um namespace de serviço.

## <a name="create-a-subscription"></a>Criar uma assinatura

Assinaturas de tópico também são criadas com o método **ServiceBusRestProxy -> createSubscription** . As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens passadas para fila virtual da assinatura.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma assinatura com o filtro padrão (MatchAll)

O filtro de **MatchAll** é o padrão que é usado se nenhum filtro for especificado quando uma nova assinatura é criada. Quando o filtro de **MatchAll** é usado, publicadas para o tópico de todas as mensagens são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma inscrição chamada 'mysubscription' e usa o filtro de **MatchAll** padrão.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

### <a name="create-subscriptions-with-filters"></a>Criar assinaturas com filtros

Você também pode configurar filtros que permitem especificar quais mensagens enviadas para um tópico devem aparecer dentro de uma assinatura de tópico específico. O tipo mais flexível de filtro compatível com assinaturas é o **SqlFilter**, que implementa um subconjunto de SQL92. Filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para saber mais sobre SqlFilters, consulte [SqlFilter.SqlExpression propriedade][sqlfilter].

> [AZURE.NOTE] Cada regra em uma assinatura processa mensagens recebidas independentemente, adicionando suas mensagens de resultado para a assinatura. Além disso, cada nova assinatura tem um objeto de **regra** padrão com um filtro que adiciona todas as mensagens do tópico para a assinatura. Para receber apenas as mensagens que o filtro de correspondência, você deve remover a regra padrão. Você pode remover a regra padrão usando o `ServiceBusRestProxy->deleteRule` método.

O exemplo a seguir cria uma inscrição chamada **HighMessages** com um **SqlFilter** que seleciona somente as mensagens que têm uma propriedade personalizada de **MessageNumber** maior que 3 (consulte a [Enviar mensagens para um tópico](#send-messages-to-a-topic) para obter informações sobre como adicionar propriedades personalizadas às mensagens):

```
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Observe que este código requer o uso de um namespace adicional: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Da mesma forma, o exemplo a seguir cria uma inscrição chamada **LowMessages** com um **SqlFilter** que seleciona somente as mensagens que têm uma **MessageNumber** propriedade menor ou igual a 3:

```
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Agora, quando uma mensagem é enviada para o `mytopic` tópico, ele sempre foi entregue para receptores assinados o `mysubscription` assinatura e seletivamente entregue para receptores assinados o `HighMessages` e `LowMessages` assinaturas (dependendo o conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

Para enviar uma mensagem para um tópico de barramento de serviço, seu aplicativo chama o método **ServiceBusRestProxy -> sendTopicMessage** . O código a seguir mostra como enviar uma mensagem para o `mytopic` tópico criado anteriormente dentro do `MySBNamespace` namespace do serviço.

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
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

As mensagens enviadas para os tópicos de barramento de serviço são instâncias da classe **BrokeredMessage** . Objetos de **BrokeredMessage** têm um conjunto de propriedades padrão e métodos (como **getLabel**, **getTimeToLive**, **setLabel**e **setTimeToLive**), bem como propriedades que podem ser usadas para armazenar as propriedades personalizadas específicas do aplicativo. O exemplo a seguir mostra como enviar mensagens de teste de 5 para o `mytopic` tópico criado anteriormente. O método **DefinirPropriedade** é usado para adicionar uma propriedade personalizada (`MessageNumber`) a cada mensagem. Observe que o `MessageNumber` valor da propriedade varia em cada mensagem (você pode usar esse valor para determinar quais assinaturas recebam-lo, conforme mostrado na seção [criar uma assinatura](#create-a-subscription) ):

```
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);
            
    // Set custom property.
    $message->setProperty("MessageNumber", $i);
            
    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Tópicos de barramento de serviço oferece suporte a um tamanho máximo de mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB no [nível Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizados, pode ter um tamanho máximo de 64 KB. Não há nenhum limite o número de mensagens contidos em um tópico, mas há um limite no tamanho total das mensagens mantidas por um tópico. Esse limite superior no tamanho do tópico é 5 GB. Para obter mais informações sobre cotas, consulte [cotas de barramento de serviço][].

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura

A melhor maneira de receber mensagens de uma assinatura é usar um método **ServiceBusRestProxy -> receiveSubscriptionMessage** . Mensagens recebidas podem trabalhar em dois modos diferentes: **ReceiveAndDelete** (padrão) e **PeekLock**.

Quando receber usando o modo de **ReceiveAndDelete** , é uma operação de captura de único; Isto é, quando barramento de serviço recebe uma solicitação de leitura para uma mensagem em uma assinatura, ele marca a mensagem como sendo consumida e retorna para o aplicativo. Modo de **ReceiveAndDelete** é o modelo mais simples e funciona melhor para cenários em que um aplicativo pode suportar não processando uma mensagem em caso de uma falha. Para entender isso, considere um cenário em que o consumidor emite a solicitação de recebimento e então trava antes de processá-lo. Porque barramento de serviço será marcou a mensagem como sendo consumida, em seguida, quando o aplicativo é reiniciado e começa consumindo mensagens novamente, ele será ter perdido a mensagem que foi consumida antes da falha.

No modo de **PeekLock** , você receber uma mensagem se torna uma operação de dois estágios, que possibilita aos aplicativos de suporte que não é possível suportar mensagens ausentes. Quando barramento de serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, bloqueia, para evitar que outros consumidores recebê-lo e retorna para o aplicativo. Depois que o aplicativo termina de processar a mensagem (ou armazena confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento passando a mensagem recebida para **ServiceBusRestProxy -> deleteMessage**. Quando barramento de serviço vê a **deleteMessage** chamar, ele será marcar a mensagem como sendo consumida e removê-lo da fila.

O exemplo a seguir mostra como receber e processar uma mensagem usando o modo de **PeekLock** (não o modo padrão). 

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();
    
    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";
        
    /*---------------------------
        Process message here.
    ----------------------------*/
        
    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como: manipular falhas do aplicativo e mensagens ilegíveis

Barramento de serviço fornece funcionalidade para ajudá-lo a recuperar normalmente de erros em seu aplicativo ou dificuldades processamento de uma mensagem. Se um aplicativo receptor não é possível processar a mensagem por algum motivo, ele pode chamar o método **unlockMessage** na mensagem recebida (em vez do método de **deleteMessage** ). Isso fará com que o barramento de serviço desbloquear a mensagem dentro da fila e disponibilizá-lo a ser recebida novamente, pelo mesmo aplicativo consumindo ou por outro aplicativo consumindo.

Há também um tempo limite associado a uma mensagem bloqueada dentro da fila e se o aplicativo falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se o aplicativo trava), e em seguida, barramento de serviço será desbloquear a mensagem automaticamente e disponibilizá-lo a ser recebida novamente.

Se o aplicativo trava após processamento da mensagem, mas antes da solicitação de **deleteMessage** é emitida, em seguida, a mensagem será entregue novamente para o aplicativo quando ele for reiniciado. Isso normalmente é chamado **Pelo menos uma vez processamento**; ou seja, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser entregue novamente. Se o cenário não pode suportar processamento duplicado, os desenvolvedores de aplicativos devem adicionar lógica adicional para aplicativos para controlar a entrega de mensagem duplicada. Isso geralmente é feito usando o método **getMessageId** da mensagem, que permanece constante entre tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Excluir tópicos e assinaturas

Para excluir um tópico ou uma assinatura, use o **ServiceBusRestProxy -> deleteTopic** ou os métodos de **ServiceBusRestProxy -> deleteSubscripton** , respectivamente. Observe que a exclusão de um tópico também exclui qualquer assinaturas que são registradas com o tópico.

O exemplo a seguir mostra como excluir um tópico chamado `mytopic` e suas assinaturas registradas.

```
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {       
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Usando o método de **deleteSubscription** , você pode excluir uma assinatura de maneira independente:

```
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos de filas de barramento de serviço, consulte [filas, tópicos e assinaturas][] para obter mais informações.

[Filas, tópicos e assinaturas]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[require-once]: http://php.net/require_once
[Cotas de barramento de serviço]: service-bus-quotas.md
