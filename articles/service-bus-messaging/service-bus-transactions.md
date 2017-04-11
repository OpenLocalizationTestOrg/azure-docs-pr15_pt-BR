<properties 
    pageTitle="Transações de barramento de serviço | Microsoft Azure" 
    description="Visão geral de transações atômicas barramento de serviço do Azure e enviar via" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="10/04/2016"
    ms.author="clemensv;sethm"/>

# <a name="overview-of-service-bus-transaction-processing"></a>Visão geral do processamento de transação de barramento de serviço

Este artigo discute os recursos de transação do barramento de serviço do Azure. Muita discussão é ilustrada pelas [Transações atômica com amostra de barramento de serviço](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions). Este artigo está limitado a uma visão geral do processamento de transações e o recurso *Enviar* no carregador de serviço, enquanto a amostra de transações atômica é mais amplo e mais complexas no escopo.

## <a name="transactions-in-service-bus"></a>Transações no carregador de serviço

Uma [transação](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions#what-are-transactions) agrupa duas ou mais operações juntos em um *escopo de execução*. Por natureza, uma transação deve garantir que todas as operações pertencentes a um grupo específico de operações tenham êxito ou falharem conjuntamente. Neste aspecto transações atuam como uma unidade, que geralmente é conhecida como *atomicidade*. 

Barramento de serviço é um agente de mensagem transações e garante a integridade de transações para todas as operações internas contra armazena sua mensagem. Todas as transferências de mensagens dentro de barramento de serviço, como mover mensagens para uma [fila de inatividade](service-bus-dead-letter-queues.md) ou [encaminhamento automático](service-bus-auto-forwarding.md) de mensagens entre entidades, são transações. Assim, se barramento de serviço aceita uma mensagem, ele já foi armazenado e rotulado com um número de sequência. Daí em seguida diante, qualquer transferências de mensagem dentro de barramento de serviço são operações coordenadas entre entidades e nenhuma delas causará perda (sucesso de origem e destino falha) ou à duplicação (Falha de origem e destino é bem sucedida) da mensagem.

Barramento de serviço oferece suporte a operações de agrupamento contra mensagens jurídica (fila, tópico, assinatura) dentro do escopo de uma transação. Por exemplo, você pode enviar várias mensagens para uma fila de dentro de um escopo de transação e as mensagens só será confirmadas log da fila quando a transação é concluída com êxito.

## <a name="operations-within-a-transaction-scope"></a>Operações em um escopo de transação 

As operações que podem ser executadas em um escopo de transação são da seguinte maneira:

- ** [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx), [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx), [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx)**: enviar, SendAsync, SendBatch, SendBatchAsync 

- **[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)**: concluída, CompleteAsync, abandonar, AbandonAsync, mensagens mortas, DeadletterAsync, adiar, DeferAsync, RenewLock, RenewLockAsync 

Receber operações não são incluídas, porque ele será considerado que o aplicativo adquire mensagens usando o modo de [ReceiveMode.PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , dentro de alguns recebem loop ou com um [OnMessage](https://msdn.microsoft.com/library/azure/dn369601.aspx) retorno de chamada e só então abre um escopo de transação para processar a mensagem.

A disposição da mensagem (concluída, abandonar, aceitas, adiar), que ocorre dentro do escopo e dependentes ativado, o resultado geral da transação.

## <a name="transfers-and-send-via"></a>Transferências e "Enviar"

Para habilitar transações transferência de dados de uma fila para um processador e, em seguida, para outra fila, o serviço barramento suporta *transferências*. Em uma operação de transferência, um remetente primeiro envia uma mensagem para uma "fila de transferência" e a fila de transferência imediatamente move a mensagem para a fila de destino pretendido usando a mesma implementação de transferência robustos que depende da capacidade de encaminhar automaticamente. A mensagem nunca é confirmada log da fila transferência de forma que ele fique visível para os consumidores da fila transferência.

O poder desse recurso transações fica evidente quando da própria fila de transferência é a fonte de mensagens de entrada do remetente. Em outras palavras, barramento de serviço pode transferir a mensagem para a fila de destino "via" fila de transferência, ao realizar uma concluída (ou adiar, ou aceitas) operação na mensagem de entrada, em uma operação atômica. 

### <a name="see-it-in-code"></a>Vê-la no código

Para configurar esses transferências, você criar um remetente da mensagem que se destina a fila de destino via fila de transferência. Você também terá um receptor que recebe mensagens dessa fila mesmo. Por exemplo:

```
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

Em seguida, uma transação simple usa esses elementos, como no exemplo seguinte:

```
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir para obter mais informações sobre filas de barramento de serviço:

- [Encadeamento entidades de barramento de serviço com encaminhamento automático](service-bus-auto-forwarding.md)
- [Amostra de encaminhar automaticamente](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AutoForward)
- [Transações atômicas com amostra de barramento de serviço](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions)
- [Azure filas e barramento de serviço filas comparadas](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Como usar filas de barramento de serviço](service-bus-dotnet-get-started-with-queues.md)