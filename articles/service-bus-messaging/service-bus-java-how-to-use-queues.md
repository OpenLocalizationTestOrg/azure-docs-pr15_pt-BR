<properties
    pageTitle="Como usar filas de barramento de serviço com Java | Microsoft Azure"
    description="Saiba como usar filas de barramento de serviço no Azure. Exemplos de código escritos em Java."
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"
    manager="timlt"
    />

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>Como usar filas de barramento de serviço

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este artigo descreve como usar filas de barramento de serviço. Os exemplos são gravados em Java e usam o [SDK do Azure para Java][]. Os cenários cobertos incluem **Criando filas**, **Enviar e receber mensagens**e **excluindo filas**.

## <a name="what-are-service-bus-queues"></a>O que são filas de barramento do serviço?

Filas de barramento de serviço oferecem suporte a um modelo de comunicação **intermediário de mensagens** . Ao usar filas, componentes de um aplicativo distribuído não comunicarem diretamente; em vez disso, eles trocam mensagens por meio de uma fila, que atua como um intermediário (agente). Um produtor de mensagem (remetente) mãos desativar uma mensagem na fila e, em seguida, continua seu processamento.
Forma assíncrona, um consumidor de mensagem (receptor) obtém a mensagem da fila e a processa. O produtor não tem de esperar uma resposta do consumidor para continuar a processar e ainda mais enviar mensagens. Filas oferecem **primeiro em, PEPS (primeiro)** entrega de mensagens para uma ou mais consumidores concorrência. Ou seja, as mensagens são normalmente recebidas e processadas pelos receptores na ordem em que eles foram adicionados à fila, e cada mensagem é recebida e processada pelo consumidor de apenas uma mensagem.

![QueueConcepts](./media/service-bus-java-how-to-use-queues/sb-queues-08.png)

Filas de barramento de serviço são uma tecnologia de uso geral que pode ser usada para uma ampla variedade de situações:

- Comunicação entre funções da web e trabalhador em um aplicativo do Azure de vários níveis.
- Comunicação entre aplicativos no local e o Azure hospedado aplicativos em uma solução híbrida.
- Comunicação entre componentes de um aplicativo distribuído em execução no local em organizações diferentes ou departamentos de uma organização.

Usando filas permite dimensionar seus aplicativos com mais facilidade e habilite resiliência em sua arquitetura.

## <a name="create-a-service-namespace"></a>Criar um namespace de serviço

Para começar a usar filas de barramento de serviço no Azure, você deve primeiro criar um namespace. Um namespace fornece um contêiner de escopo para endereçar recursos de barramento de serviço dentro de seu aplicativo.

Para criar um namespace:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar barramento de serviço

Verifique se que você instalou o [SDK do Azure para Java][] antes de criar esse exemplo. Se você estiver usando o Eclipse, você pode instalar o [Kit de ferramentas do Azure para Eclipse][] que inclui o SDK do Azure para Java. Em seguida, você pode adicionar as **Bibliotecas do Microsoft Azure para Java** ao seu projeto:

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Adicione o seguinte `import` instruções para a parte superior do arquivo Java:

```
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## <a name="create-a-queue"></a>Criar uma fila

Operações de gerenciamento de filas de barramento de serviço podem ser executadas por meio da classe **ServiceBusContract** . Um objeto de **ServiceBusContract** é construído com uma configuração apropriada que encapsula o token SAS com permissões para gerenciá-lo, e a classe de **ServiceBusContract** é o único ponto de comunicação com o Azure.

A classe **ServiceBusService** fornece métodos para criar, enumerar e excluir filas. O exemplo a seguir mostra como um objeto de **ServiceBusService** pode ser usado para criar uma fila denominada "TestQueue", com um namespace chamado "HowToSample":

```
Configuration config =
    ServiceBusConfiguration.configureWithSASAuthentication(
            "HowToSample",
            "RootManageSharedAccessKey",
            "SAS_key_value",
            ".servicebus.windows.net"
            );

ServiceBusContract service = ServiceBusService.create(config);
QueueInfo queueInfo = new QueueInfo("TestQueue");
try
{
    CreateQueueResult result = service.createQueue(queueInfo);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Existem métodos em **QueueInfo** que permitem propriedades da fila para ser ajustado (por exemplo: para definir o valor padrão para vida (útil TTL) a serem aplicadas a mensagens enviadas para a fila). O exemplo a seguir mostra como criar uma fila denominada `TestQueue` com um tamanho máximo de 5GB:

````
long maxSizeInMegabytes = 5120;
QueueInfo queueInfo = new QueueInfo("TestQueue");
queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateQueueResult result = service.createQueue(queueInfo);
````

Observe que você pode usar o método **listQueues** em objetos de **ServiceBusContract** para verificar se já existe uma fila com um nome especificado dentro de um namespace de serviço.

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila

Para enviar uma mensagem para uma fila de barramento de serviço, o seu aplicativo obtém um objeto **ServiceBusContract** . O código a seguir mostra como enviar uma mensagem o `TestQueue` fila previamente criada no `HowToSample` namespace:

```
try
{
    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendQueueMessage("TestQueue", message);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

As mensagens enviadas para e recebidos de barramento de serviço filas são instâncias da classe [BrokeredMessage][] . Objetos de [BrokeredMessage][] têm um conjunto de propriedades padrão (como [rótulo](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), um dicionário que é usado para armazenar propriedades específicas do aplicativo personalizado e um corpo de dados de aplicativo aleatório. Um aplicativo pode definir o corpo da mensagem, passando qualquer objeto serializável no construtor do [BrokeredMessage][]e o serializador adequado será usado para serializar o objeto. Como alternativa, você pode fornecer um java **. IO. InputStream** objeto.

O exemplo a seguir demonstra como enviar cinco testar mensagens para o `TestQueue` **MessageSender** obtivemos no trecho de código anterior:

```
for (int i=0; i<5; i++)
{
     // Create message, passing a string message for the body.
     BrokeredMessage message = new BrokeredMessage("Test message " + i);
     // Set an additional app-specific property.
     message.setProperty("MyProperty", i);
     // Send message to the queue
     service.sendQueueMessage("TestQueue", message);
}
```

Filas de barramento de serviço oferecem suporte a um tamanho máximo de mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB no [nível Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizados, pode ter um tamanho máximo de 64 KB. Não há nenhum limite o número de mensagens mantido em uma fila, mas há um limite no tamanho total das mensagens apresentadas por uma fila. Este tamanho de fila é definido em tempo de criação, com um limite de 5 GB.

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

A principal maneira para receber mensagens de uma fila é usar um objeto **ServiceBusContract** . Mensagens recebidas podem trabalhar em dois modos diferentes: **ReceiveAndDelete** e **PeekLock**.

Quando receber usando o modo de **ReceiveAndDelete** , é uma operação de captura de único - ou seja, quando barramento de serviço recebe uma solicitação de leitura para uma mensagem em uma fila, ele marca a mensagem como sendo consumida e retorna para o aplicativo. Modo de **ReceiveAndDelete** (que é o modo padrão) é o modelo mais simples e funciona melhor para cenários em que um aplicativo pode suportar não processando uma mensagem em caso de uma falha. Para entender isso, considere um cenário em que o consumidor emite a solicitação de recebimento e então trava antes de processá-lo.
Porque barramento de serviço será marcou a mensagem como sendo consumida, em seguida, quando o aplicativo é reiniciado e começa consumindo mensagens novamente, ele será ter perdido a mensagem que foi consumida antes da falha.

Modo de **PeekLock** , receber torna-se uma operação de dois estágios, que possibilita aos aplicativos de suporte que não é possível suportar mensagens ausentes. Quando barramento de serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, bloqueia, para evitar que outros consumidores recebê-lo e retorna para o aplicativo. Após o aplicativo termina de processar a mensagem (ou armazena confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento ligando para **Excluir** na mensagem recebida. Quando barramento de serviço vê a chamada **Excluir** , ele será marcar a mensagem como sendo consumida e removê-lo da fila.

O exemplo a seguir demonstra como mensagens podem ser recebidas e processadas usando o modo de **PeekLock** (não o modo padrão). O exemplo a seguir faz um loop infinito e processa mensagens quando chegarem em nosso "TestQueue":

```
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
         ReceiveQueueMessageResult resultQM =
                service.receiveQueueMessage("TestQueue", opts);
        BrokeredMessage message = resultQM.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the queue message.
            System.out.print("From queue: ");
            byte[] b = new byte[200];
            String s = null;
            int numRead = message.getBody().read(b);
            while (-1 != numRead)
            {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
            }
            System.out.println();
            System.out.println("Custom Property: " +
                message.getProperty("MyProperty"));
            // Remove message from queue.
            System.out.println("Deleting this message.");
            //service.deleteMessage(message);
        }  
        else  
        {
            System.out.println("Finishing up - no more messages.");
            break;
            // Added to handle no more messages.
            // Could instead wait for more messages to be added.
        }
    }
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
catch (Exception e) {
    System.out.print("Generic exception encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como lidar com mensagens ilegíveis e travamentos de aplicativos

Barramento de serviço fornece funcionalidade para ajudá-lo a recuperar normalmente de erros em seu aplicativo ou dificuldades processamento de uma mensagem. Se um aplicativo receptor não é possível processar a mensagem por algum motivo, ele pode chamar o método **unlockMessage** na mensagem recebida (em vez do método de **deleteMessage** ). Isso fará com que o barramento de serviço desbloquear a mensagem dentro da fila e disponibilizá-lo a ser recebida novamente, pelo mesmo aplicativo consumindo ou por outro aplicativo consumindo.

Há também um tempo limite associado a uma mensagem bloqueada dentro da fila e se o aplicativo falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se o aplicativo trava), e em seguida, barramento de serviço será desbloquear a mensagem automaticamente e disponibilizá-lo a ser recebida novamente.

Se o aplicativo trava após processamento da mensagem, mas antes da solicitação de **deleteMessage** é emitida, em seguida, a mensagem será entregue novamente para o aplicativo quando ele for reiniciado. Isso normalmente é chamado **Pelo menos uma vez processamento**, ou seja, cada mensagem será processada pelo menos uma vez mas em determinadas situações a mesma mensagem pode ser entregue novamente. Se o cenário não pode suportar processamento duplicado, os desenvolvedores de aplicativos devem adicionar lógica adicional para seu aplicativo para controlar a entrega de mensagem duplicada. Isso geralmente é feito usando o método **getMessageId** da mensagem, que permanecerá constante entre tentativas de entrega.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos de filas de barramento de serviço, consulte [filas, tópicos e assinaturas][] para obter mais informações.

Para obter mais informações, consulte o [Java Developer Center](/develop/java/).


  [Azure SDK para Java]: http://azure.microsoft.com/develop/java/
  [Kit de ferramentas de Azure para Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Filas, tópicos e assinaturas]: service-bus-queues-topics-subscriptions.md
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

