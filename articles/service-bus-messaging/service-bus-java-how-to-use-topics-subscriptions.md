<properties
    pageTitle="Como usar tópicos de barramento de serviço com Java | Microsoft Azure"
    description="Saiba como usar tópicos de barramento de serviço e assinaturas no Azure. Exemplos de código são gravados para aplicativos Java."
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Como usar assinaturas e tópicos de barramento de serviço

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este guia descreve como usar assinaturas e tópicos de barramento de serviço. Os exemplos são gravados em Java e usam o [SDK do Azure para Java][]. Os cenários cobertos incluem **Criando tópicos e assinaturas**, **Criando filtros de assinatura**, **enviando mensagens para um tópico**, **receber mensagens de uma assinatura**e **Excluindo tópicos e assinaturas**.

## <a name="what-are-service-bus-topics-and-subscriptions"></a>O que são assinaturas e tópicos de barramento de serviço?

Tópicos de barramento de serviço e assinaturas suportam a uma *publicação/assinatura* modelo de comunicação de mensagens. Ao usar tópicos e assinaturas, componentes de um aplicativo distribuído não comunicarem diretamente; em vez disso, eles trocam mensagens por meio de um tópico, que atua como um intermediário.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Em contraste com filas de barramento de serviço, em que cada mensagem é processada por um único consumidor, tópicos e assinaturas fornecem um formulário de "um-para-muitos" de comunicação, usando um padrão publicar/assinar. É possível registrar várias assinaturas a um tópico. Quando uma mensagem é enviada para um tópico, ele é então disponibilizado para cada assinatura alça/processo de maneira independente.

Uma assinatura a um tópico é semelhante a uma fila virtual que recebe cópias das mensagens que foram enviadas para o tópico. Opcionalmente, você pode registrar regras de filtro para um tópico numa base por assinatura, que permite filtrar/restringir quais mensagens a um tópico são recebidas pelo quais assinaturas de tópico.

Tópicos de barramento de serviço e assinaturas permitem que você dimensionar para processar um grande número de mensagens em um grande número de usuários e aplicativos.

## <a name="create-a-service-namespace"></a>Criar um namespace de serviço

Para começar a usar os tópicos de barramento de serviço e assinaturas no Azure, você deve primeiro criar um namespace de serviço. Um namespace fornece um contêiner de escopo para endereçar recursos de barramento de serviço dentro de seu aplicativo.

Para criar um namespace:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar barramento de serviço

Verifique se que você instalou o [SDK do Azure para Java][] antes de criar esse exemplo. Se você estiver usando o Eclipse, você pode instalar o [Kit de ferramentas do Azure para Eclipse][] que inclui o SDK do Azure para Java. Em seguida, você pode adicionar as **Bibliotecas do Microsoft Azure para Java** ao seu projeto:

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Adicione as seguintes instruções de importação para a parte superior do arquivo Java:

```
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

Adicione as bibliotecas do Azure para Java ao seu caminho de criação e incluí-lo em seu conjunto de implantação do projeto.

## <a name="create-a-topic"></a>Criar um tópico

Operações de gerenciamento de tópicos do barramento de serviço podem ser executadas por meio da classe **ServiceBusContract** . Um objeto de **ServiceBusContract** é construído com uma configuração apropriada que encapsula o token SAS com permissões para gerenciá-lo, e a classe de **ServiceBusContract** é o único ponto de comunicação com o Azure.

A classe **ServiceBusService** fornece métodos para criar, enumerar e excluir tópicos. O exemplo a seguir mostra como um objeto de **ServiceBusService** pode ser usado para criar um tópico chamado `TestTopic`, com um namespace chamado `HowToSample`:

    Configuration config =
        ServiceBusConfiguration.configureWithSASAuthentication(
          "HowToSample",
          "RootManageSharedAccessKey",
          "SAS_key_value",
          ".servicebus.windows.net"
          );

    ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
    try  
    {
        CreateTopicResult result = service.createTopic(topicInfo);
    }
    catch (ServiceException e) {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Existem métodos em **TopicInfo** que permitem propriedades do tópico a ser definida (por exemplo: para definir o valor padrão para vida (útil TTL) a serem aplicadas a mensagens enviadas para o tópico). O exemplo a seguir mostra como criar um tópico chamado `TestTopic` com um tamanho máximo de 5 GB:

    long maxSizeInMegabytes = 5120;  
    TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateTopicResult result = service.createTopic(topicInfo);

Observe que você pode usar o método **listTopics** em objetos de **ServiceBusContract** para verificar se já existe um tópico com um nome especificado dentro de um namespace de serviço.

## <a name="create-subscriptions"></a>Criar assinaturas

Assinaturas para tópicos também são criadas com a classe de **ServiceBusService** . As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens passadas para fila virtual da assinatura.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma assinatura com o filtro padrão (MatchAll)

O filtro de **MatchAll** é o padrão que é usado se nenhum filtro for especificado quando uma nova assinatura é criada. Quando o filtro de **MatchAll** é usado, publicadas para o tópico de todas as mensagens são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada "AllMessages" e usa o filtro de **MatchAll** padrão.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result =
        service.createSubscription("TestTopic", subInfo);

### <a name="create-subscriptions-with-filters"></a>Criar assinaturas com filtros

Você também pode criar filtros que permitem a escopo que as mensagens enviadas para um tópico deverão ser mostrada dentro de uma assinatura de tópico específico.

O tipo mais flexível de filtro compatível com assinaturas é o [SqlFilter][], que implementa um subconjunto de SQL92. Filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, examine a sintaxe [SqlFilter.SqlExpression][] .

O exemplo a seguir cria uma inscrição chamada `HighMessages` com um objeto de [SqlFilter][] que seleciona somente as mensagens que têm uma propriedade personalizada de **MessageNumber** maior que 3:

```
// Create a "HighMessages" filtered subscription  
SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "HighMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "HighMessages", "$Default");
```

Da mesma forma, o exemplo a seguir cria uma inscrição chamada `LowMessages` com um objeto de [SqlFilter][] que somente seleciona mensagens que tenham uma **MessageNumber** propriedade menor ou igual a 3:

```
// Create a "LowMessages" filtered subscription
SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "LowMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "LowMessages", "$Default");
```

Quando uma mensagem agora é enviada para `TestTopic`, ele sempre sejam entregues em receptores assinados o `AllMessages` assinatura e seletivamente entregue para receptores assinados o `HighMessages` e `LowMessages` assinaturas (dependendo o conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

Para enviar uma mensagem para um tópico de barramento de serviço, o seu aplicativo obtém um objeto **ServiceBusContract** . O seguinte código demonstra como enviar uma mensagem o `TestTopic` tópico criado anteriormente dentro do `HowToSample` namespace:

```
BrokeredMessage message = new BrokeredMessage("MyMessage");
service.sendTopicMessage("TestTopic", message);
```

As mensagens enviadas para os tópicos de barramento de serviço são instâncias da classe [BrokeredMessage][] . [BrokeredMessage][] *os objetos têm um conjunto de métodos padrão (como * *setLabel* * e * *TimeToLive**), um dicionário que é usado para armazenar as propriedades personalizadas específicas do aplicativo e um corpo de dados de aplicativo aleatório. Um aplicativo pode definir o corpo da mensagem, passando qualquer objeto serializável no construtor do [BrokeredMessage][]e o apropriado * *DataContractSerializer* * será usado para serializar o objeto. Como alternativa, uma * *java.io.InputStream** pode ser fornecido.

O exemplo a seguir demonstra como enviar cinco testar mensagens para o `TestTopic` **MessageSender** obtivemos no trecho de código acima.
Observe como o valor da propriedade **MessageNumber** de cada mensagem varia a iteração do loop (isso determinará quais assinaturas recebam-lo):

```
for (int i=0; i<5; i++)  {
// Create message, passing a string message for the body
BrokeredMessage message = new BrokeredMessage("Test message " + i);
// Set some additional custom app-specific property
message.setProperty("MessageNumber", i);
// Send message to the topic
service.sendTopicMessage("TestTopic", message);
}
```

Tópicos de barramento de serviço oferece suporte a um tamanho máximo de mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB no [nível Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizados, pode ter um tamanho máximo de 64 KB. Não há nenhum limite o número de mensagens contidos em um tópico, mas há um limite no tamanho total das mensagens mantidas por um tópico. Tamanho este tópico é definido em tempo de criação, com um limite de 5 GB.

## <a name="how-to-receive-messages-from-a-subscription"></a>Como receber mensagens de uma assinatura

Para receber mensagens de uma assinatura, use um objeto de **ServiceBusContract** . Mensagens recebidas podem trabalhar em dois modos diferentes: **ReceiveAndDelete** e **PeekLock**.

Quando receber usando o modo de **ReceiveAndDelete** , é uma operação de captura de único - ou seja, quando barramento de serviço recebe uma solicitação de leitura para uma mensagem, ele marca a mensagem como sendo consumida e retorna para o aplicativo. Modo de **ReceiveAndDelete** é o modelo mais simples e funciona melhor para cenários em que um aplicativo pode suportar não processando uma mensagem em caso de uma falha. Para entender isso, considere um cenário em que o consumidor emite a solicitação de recebimento e então trava antes de processá-lo. Porque barramento de serviço será marcou a mensagem como sendo consumida, em seguida, quando o aplicativo é reiniciado e começa consumindo mensagens novamente, ele será ter perdido a mensagem que foi consumida antes da falha.

Modo de **PeekLock** , receber torna-se uma operação de dois estágios, que possibilita aos aplicativos de suporte que não é possível suportar mensagens ausentes. Quando barramento de serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, bloqueia, para evitar que outros consumidores recebê-lo e retorna para o aplicativo. Após o aplicativo termina de processar a mensagem (ou armazena confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento ligando para **Excluir** na mensagem recebida. Quando barramento de serviço vê a chamada **Excluir** , ele será marcar a mensagem como sendo consumida e removê-lo do tópico.

O exemplo a seguir demonstra como mensagens podem ser recebidas e processadas usando o modo de **PeekLock** (não o modo padrão). O exemplo a seguir executa um loop e processa mensagens na assinatura "HighMessages" e, em seguida, sai quando não existem mais mensagens (como alternativa, pode ser definida aguardar para novas mensagens).

```
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
        ReceiveSubscriptionMessageResult  resultSubMsg =
            service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
        BrokeredMessage message = resultSubMsg.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the topic message.
            System.out.print("From topic: ");
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
                message.getProperty("MessageNumber"));
            // Delete message.
            System.out.println("Deleting this message.");
            service.deleteMessage(message);
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

Barramento de serviço fornece funcionalidade para ajudá-lo a recuperar normalmente de erros em seu aplicativo ou dificuldades processamento de uma mensagem. Se um aplicativo receptor não é possível processar a mensagem por algum motivo, ele pode chamar o método **unlockMessage** na mensagem recebida (em vez do método de **deleteMessage** ). Isso fará com que o barramento de serviço desbloquear a mensagem dentro do tópico e disponibilizá-lo a ser recebida novamente, pelo mesmo aplicativo consumindo ou por outro aplicativo consumindo.

Há também um tempo limite associado a uma mensagem bloqueada dentro do tópico, e se o aplicativo falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se o aplicativo trava), então barramento de serviço será desbloquear a mensagem automaticamente e disponibilizá-lo a ser recebida novamente.

Se o aplicativo trava após processamento da mensagem, mas antes da solicitação de **deleteMessage** é emitida, em seguida, a mensagem será entregue novamente para o aplicativo quando ele for reiniciado. Isso normalmente é chamado **Pelo menos uma vez processamento**, ou seja, cada mensagem será processada pelo menos uma vez mas em determinadas situações a mesma mensagem pode ser entregue novamente. Se o cenário não pode suportar processamento duplicado, os desenvolvedores de aplicativos devem adicionar lógica adicional para seu aplicativo para controlar a entrega de mensagem duplicada. Isso geralmente é feito usando o método **getMessageId** da mensagem, que permanecerá constante entre tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Excluir tópicos e assinaturas

A principal maneira de excluir tópicos e assinaturas é usar um objeto **ServiceBusContract** . Excluir um tópico também excluirá qualquer assinaturas que são registradas com o tópico. Assinaturas também podem ser excluídas independentemente.

```
// Delete subscriptions
service.deleteSubscription("TestTopic", "AllMessages");
service.deleteSubscription("TestTopic", "HighMessages");
service.deleteSubscription("TestTopic", "LowMessages");

// Delete a topic
service.deleteTopic("TestTopic");
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos de filas de barramento de serviço, consulte [assinaturas, tópicos e filas de barramento de serviço][] para obter mais informações.

  [Azure SDK para Java]: http://azure.microsoft.com/develop/java/
  [Kit de ferramentas de Azure para Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Azure classic portal]: http://manage.windowsazure.com/
  [Assinaturas, tópicos e filas de barramento de serviço]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx 
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  
  [0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
  [2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
  [3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
