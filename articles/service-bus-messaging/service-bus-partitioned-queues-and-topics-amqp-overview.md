<properties 
    pageTitle="Suporte a AMQP 1.0 barramento de serviço particionado tópicos e filas | Microsoft Azure" 
    description="Saiba como usar os tópicos e filas avançadas mensagem fila Protocol (AMQP) 1.0 com barramento de serviço particionado." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="hillaryc" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="hillaryc;sethm"/>

# <a name="amqp-10-support-for-service-bus-partitioned-queues-and-topics"></a>Suporte a AMQP 1.0 tópicos e filas do barramento de serviço particionado 

Barramento de serviço do Azure agora suporta o avançado mensagem fila protocolo (**AMQP**) 1.0 para barramento de serviço **tópicos e filas particionado.**

**AMQP** é um protocolo de filas de mensagem de padrão aberto que permite que você desenvolva aplicativos de plataforma cruzada usando diferentes linguagens de programação. Para obter informações gerais sobre AMQP suporte no carregador de serviço, consulte [AMQP 1.0 no carregador de serviço de suporte](service-bus-amqp-overview.md).

**Tópicos e filas particionados**, também conhecido como *entidades particionadas*, oferecem maior disponibilidade, confiabilidade e produtividade de tópicos e filas não particionados convencionais. Para obter mais informações sobre entidades particionadas, consulte [Particionada entidades de mensagens](service-bus-partitioning.md).

A adição de AMQP 1.0 como um protocolo para se comunicar com tópicos e filas particionadas permite que você crie aplicativos interoperabilidade que podem aproveitar a maior disponibilidade, confiabilidade e em todo oferecidas por entidades barramento de serviço particionado.

Para um nível de fios AMQP 1.0 protocolo guia detalhada, que explica como o barramento de serviço implementa e especificação técnica OASIS AMQP se baseia, consulte o [guia de protocolo AMQP 1.0 no barramento de serviço do Azure e Hubs de evento](service-bus-amqp-protocol-guide.md).    

## <a name="use-amqp-with-partitioned-queues"></a>Usar AMQP com filas particionadas

Filas são úteis para cenários que exigem a separação temporal, redistribuição de carga, balanceamento de carga e acoplamento. Com uma fila, fornecedores enviam mensagens à fila e consumidores de recebem mensagens de fila, em situações em que uma mensagem só pode ser recebida uma vez. Um bom exemplo disso é um sistema de estoque no qual terminais de ponto de venda publicar dados a uma fila em vez de diretamente para o sistema de gerenciamento de estoque. O sistema de gerenciamento de estoque, em seguida, consome dados a qualquer momento para gerenciar reabastecimento de estoque. Isso tem várias vantagens, incluindo o sistema de gerenciamento de estoque não precisar ser online em todos os momentos. Para obter mais detalhes sobre filas de barramento de serviço, consulte [criar aplicativos que usam filas de barramento de serviço](service-bus-create-queues.md). 

Uma fila particionada ainda mais aumenta a disponibilidade, a confiabilidade e produtividade para aplicativos, como essas filas são particionadas por vários agentes de mensagens e armazena mensagens.     

### <a name="create-partitioned-queues"></a>Criar filas particionadas

Você pode criar uma fila particionada usando o [portal de clássico Azure][] e o SDK do barramento de serviço. Para criar uma fila particionada, defina a propriedade [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) como **true** na instância [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx) . O código a seguir mostra como criar uma fila particionada usando o SDK do barramento de serviço. 
 
```
// Create partitioned queue
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var queueDescription = new QueueDescription("myQueue");
queueDescription.EnablePartitioning = true;
nm.CreateQueue(queueDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Enviar e receber mensagens usando AMQP

Você pode enviar mensagens para e receber mensagens de uma fila particionada usando AMQP como protocolo, definindo a propriedade de [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) para [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) , conforme mostrado no código a seguir.  

```
// Sending and receiving messages to and from a queue
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
var queueClient = QueueClient.CreateFromConnectionString(amqpConnectionString, "myQueue");

BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
queueClient.Send(message);

var receivedMessage = queueClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="use-amqp-with-partitioned-topics"></a>Usar AMQP com tópicos particionados

Tópicos são conceitualmente semelhantes a filas, mas tópicos podem direcionar uma cópia da mesma mensagem para várias *assinaturas*. Em um tópico, fornecedores enviam mensagens para o tópico e consumidores de recebem mensagens de assinaturas. O cenário de ponto de venda de sistema de estoque, terminais publicar dados para o tópico. O sistema de gerenciamento de estoque, em seguida, recebe mensagens de uma assinatura. Além disso, um sistema de monitoramento pode receber a mesma mensagem de uma assinatura diferente. Para obter mais detalhes sobre tópicos de barramento de serviço e assinaturas, consulte [criar aplicativos que usam assinaturas e tópicos de barramento de serviço](service-bus-create-topics-subscriptions.md). 

Como com filas, ainda mais tópicos particionados aumentam a disponibilidade, a confiabilidade e produtividade para aplicativos, como esses tópicos e suas assinaturas são particionadas por vários agentes de mensagens e armazena mensagens. 

### <a name="create-partitioned-topics"></a>Criar tópicos particionados

Você pode criar um tópico particionado usando o [portal de clássico Azure][] e o SDK do barramento de serviço. Para criar um tópico particionado, defina a propriedade [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx) como **true** na instância [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) . O código a seguir mostra como criar um tópico particionado usando o SDK do barramento de serviço.
    
```
// Create partitioned topic
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var topicDescription = new TopicDescription("myTopic");
topicDescription.EnablePartitioning = true;
nm.CreateTopic(topicDescription);

var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
nm.CreateSubscription(subscriptionDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Enviar e receber mensagens usando AMQP

Você pode enviar mensagens para e receber mensagens de uma assinatura de tópico particionado usando AMQP como um protocolo, definindo a propriedade de [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) para [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx), conforme mostrado no código a seguir.  

```
// Sending and receiving messages to a topic and from a subscription
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
    
var topicClient = TopicClient.CreateFromConnectionString(amqpConnectionString, "myTopic");
BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
topicClient.Send(message);
    
var subcriptionClient = SubscriptionClient.CreateFromConnectionString(amqpConnectionString, "myTopic", "mySubscription");
var receivedMessage = subcriptionClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="next-steps"></a>Próximas etapas

Consulte as seguintes informações adicionais para saber mais sobre particionadas entidades mensagens bem como AMQP.

*    [Particionado entidades de mensagens](service-bus-partitioning.md)
*    [OASIS Advanced Message fila Protocol (AMQP) versão 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Suporte de AMQP 1.0 no carregador de serviço](service-bus-amqp-overview.md)
*    [1.0 AMQP guia de protocolo barramento de serviço do Azure e Hubs de evento](service-bus-amqp-protocol-guide.md)
*    [Como usar a API Java Message Service (JMS) com barramento de serviço e AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Como usar AMQP 1.0 com a API do serviço barramento .NET](service-bus-dotnet-advanced-message-queuing.md)

[Azure portal clássico]: http://manage.windowsazure.com
