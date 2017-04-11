<properties 
    pageTitle="Como usar AMQP 1.0 com a API do barramento de serviço Java | Microsoft Azure" 
    description="Como usar o Java Message Service (JMS) com barramento de serviço do Azure e avançadas mensagem fila Protodol (AMQP) 1.0." 
    services="service-bus" 
    documentationCenter="java" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Como usar a API Java Message Service (JMS) com barramento de serviço e AMQP 1.0

O Advanced mensagem fila Protocol (AMQP) 1.0 é um protocolo eficiente, confiável e nível de fios de mensagens que você pode usar para criar aplicativos de mensagens robustos, entre plataformas.

Suporte para AMQP 1.0 no carregador de serviço significa que você pode usar na fila e publicar/assinar intermediário recursos de mensagens de um intervalo de plataformas usando um protocolo binário eficiente. Além disso, você pode criar aplicativos compostos dos componentes criados usando uma mistura de idiomas, estruturas e sistemas operacionais.

Este artigo explica como usar o barramento de serviço (filas e publicar/assinar tópicos) os recursos de mensagens de aplicativos de Java usando o populares Java Message Service (JMS) API padrão. Não há um [artigo companion](service-bus-dotnet-advanced-message-queuing.md) que explica como fazer a mesma usando a API do serviço barramento .NET. Você pode usar essas duas guias juntos para saber mais sobre mensagens entre plataformas usando AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Introdução ao barramento de serviço

Este guia pressupõe que você já tem um namespace de barramento de serviço contendo uma fila denominada **Fila1**. Se você não fizer isso, você pode [criar a fila e namespace](service-bus-create-namespace-portal.md) usando o [portal do Azure](https://portal.azure.com). Para obter mais informações sobre como criar filas e namespaces barramento de serviço, consulte [como usar filas de barramento de serviço](service-bus-dotnet-get-started-with-queues.md).

> [AZURE.NOTE] Tópicos e filas particionadas também suportam AMQP. Para obter mais informações, consulte [Partitioned mensagens entidades](service-bus-partitioning.md) e [1.0 AMQP suporte para barramento de serviço particionado tópicos e filas](service-bus-partitioned-queues-and-topics-amqp-overview.md).

## <a name="downloading-the-amqp-10-jms-client-library"></a>Download da biblioteca de cliente AMQP 1.0 JMS

Para obter informações sobre onde baixar a versão mais recente da biblioteca cliente Apache Qpid JMS AMQP 1.0, visite [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

Adicione os seguintes quatro arquivos JAR do arquivamento de distribuição do Apache Qpid JMS AMQP 1.0 para o caminho de classe Java ao criar e executar aplicativos JMS com barramento de serviço:

- geronimo-jms\_1.1\_especificações 1.0.jar
- qpid-amqp-1-0-Client-[Version].jar
- qpid-amqp-1-0-Client-JMS-[Version].jar
- qpid-amqp-1-0-Common-[Version].jar

## <a name="coding-java-applications"></a>Codificação de aplicativos Java

### <a name="java-naming-and-directory-interface-jndi"></a>Interface de diretório (JNDI) e Java de nomenclatura

JMS usa o Java nomeação e a Interface de diretório (JNDI) para criar uma separação entre nomes de lógicos e física. Dois tipos de objetos são resolvidos usando JNDI: ConnectionFactory e destino. JNDI usa um modelo de provedor no qual você pode conectar serviços de diretório diferentes para lidar com tarefas de resolução de nome. O Apache Qpid JMS AMQP 1.0 biblioteca vem com um simples propriedades provedor JNDI baseados em arquivos que é configurado usando um arquivo de propriedades dos seguintes Formatar:

```
# servicebus.properties - sample JNDI configuration
    
# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    
# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="configure-the-connectionfactory"></a>Configurar o ConnectionFactory

A entrada usada para definir um **ConnectionFactory** no provedor de JNDI de Qpid propriedades do arquivo é do seguinte formato:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Onde **[jndi_name]** e **[URL de conexão]** têm os seguintes significados:

- **[jndi_name]**: O nome lógico da ConnectionFactory. Este é o nome que será resolvido no aplicativo Java usando o método IntialContext.lookup() JNDI.
- **[URL de conexão]**: um URL que fornece a biblioteca JMS com as informações necessárias para o corretor AMQP.

O formato do **URL de conexão** é da seguinte maneira:

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
Onde **[namespace]**, **[SASPolicyName]** e **[SASPolicyKey]** têm os seguintes significados:

- **[namespace]**: namespace o barramento de serviço.
- **[SASPolicyName]**: nome de diretiva de assinatura de acesso compartilhado a fila.
- **[SASPolicyKey]**: chave de política de assinatura de acesso compartilhado a fila.

> [AZURE.NOTE] Você deve codificar a URL a senha manualmente. Um utilitário de codificação de URL útil está disponível em [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

#### <a name="configure-destinations"></a>Configurar os destinos

A entrada usada para definir um destino no provedor Qpid propriedades de arquivo JNDI é dos seguintes formatos:

```
queue.[jndi_name] = [physical_name]
```

ou

```
topic.[jndi_name] = [physical_name]
```

Onde **[jndi\_nome]** e **[físico\_nome]** têm os seguintes significados:

- **[jndi_name]**: O nome lógico de destino. Este é o nome que será resolvido no aplicativo Java usando o método IntialContext.lookup() JNDI.
- **[physical_name]**: O nome da entidade barramento de serviço no qual o aplicativo envia ou recebe mensagens.

> [AZURE.NOTE] Ao receber de uma assinatura de tópico barramento de serviço, o nome físico especificado em JNDI deve ser o nome do tópico. O nome da assinatura é fornecido quando a assinatura durável é criada no código do aplicativo JMS. O [Guia do desenvolvedor do serviço barramento AMQP 1.0](service-bus-amqp-dotnet.md) fornece mais detalhes sobre como trabalhar com assinaturas do tópico de barramento de serviço do JMS.

### <a name="write-the-jms-application"></a>Escreva o aplicativo JMS

Existem APIs ou opções necessárias ao usar JMS com barramento de serviço especial. No entanto, há algumas restrições que serão abordadas posteriormente. Como com qualquer aplicativo JMS, a primeira coisa requerido é configuração do ambiente JNDI, sejam capazes de resolver um **ConnectionFactory** e destinos.

#### <a name="configure-the-jndi-initialcontext"></a>Configurar o InitialContext JNDI

O ambiente JNDI está configurado, passando uma tabela de hash das informações de configuração para o construtor da classe javax.naming.InitialContext. Os dois necessários elementos da tabela de hash são o nome da classe de fábrica contexto inicial e a URL do provedor. O código a seguir mostra como configurar o ambiente de JNDI para usar o arquivo de propriedades de Qpid com base no provedor JNDI com um arquivo de propriedades chamado **servicebus.properties**.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Um aplicativo de JMS simples usando uma fila de barramento de serviço

O programa de exemplo a seguir envia TextMessages JMS para uma fila de barramento de serviço com o nome JNDI lógico da fila e recebe as mensagens de volta.

```
// SimpleSenderReceiver.java
    
import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Hashtable;
import java.util.Random;
    
public class SimpleSenderReceiver implements MessageListener {
    private static boolean runReceiver = true;
    private Connection connection;
    private Session sendSession;
    private Session receiveSession;
    private MessageProducer sender;
    private MessageConsumer receiver;
    private static Random randomGenerator = new Random();
    
    public SimpleSenderReceiver() throws Exception {
        // Configure JNDI environment
        Hashtable<String, String> env = new Hashtable<String, String>();
        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                   "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
        env.put(Context.PROVIDER_URL, "servicebus.properties");
        Context context = new InitialContext(env);
    
        // Look up ConnectionFactory and Queue
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        Destination queue = (Destination) context.lookup("QUEUE");
    
        // Create Connection
        connection = cf.createConnection();
    
        // Create sender-side Session and MessageProducer
        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        sender = sendSession.createProducer(queue);
    
        if (runReceiver) {
            // Create receiver-side Session, MessageConsumer,and MessageListener
            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            receiver = receiveSession.createConsumer(queue);
            receiver.setMessageListener(this);
            connection.start();
        }
    }
    
    public static void main(String[] args) {
        try {
    
            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                runReceiver = false;
            }
    
            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));
    
            while (true) {
                String s = commandLine.readLine();
                if (s.equalsIgnoreCase("exit")) {
                    simpleSenderReceiver.close();
                    System.exit(0);
                } else {
                    simpleSenderReceiver.sendMessage();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    
    private void sendMessage() throws JMSException {
        TextMessage message = sendSession.createTextMessage();
        message.setText("Test AMQP message from JMS");
        long randomMessageID = randomGenerator.nextLong() >>>1;
        message.setJMSMessageID("ID:" + randomMessageID);
        sender.send(message);
        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
    }
    
    public void close() throws JMSException {
        connection.close();
    }
    
    public void onMessage(Message message) {
        try {
            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
            message.acknowledge();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}   
```

### <a name="run-the-application"></a>Executar o aplicativo

Executando o aplicativo gera saída do formulário:

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.
    
Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318
    
Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483
    
Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Entre plataformas de mensagens entre JMS e .NET

Este guia mostrava como enviar e receber mensagens para e do barramento de serviço usando JMS. Entretanto, um dos principais benefícios do AMQP 1.0 é que ele permite que aplicativos são criados de componentes escritos em idiomas diferentes, com mensagens trocadas confiável e fidelidade total.

Usando o aplicativo de JMS de exemplo descrito acima e um aplicativo .NET semelhante tirado de um guia associado, [como usar AMQP 1.0 com a API do .NET serviço barramento .NET](service-bus-dotnet-advanced-message-queuing.md), você pode trocar mensagens entre .NET e Java. 

Para obter mais informações sobre os detalhes de mensagens usando o barramento de serviço e AMQP 1.0 em plataformas cruzadas, consulte o [Guia do desenvolvedor do serviço barramento AMQP 1.0](service-bus-amqp-dotnet.md).

### <a name="jms-to-net"></a>JMS para .NET

Para demonstrar JMS para .NET de mensagens:

* Inicie o aplicativo de exemplo .NET sem quaisquer argumentos de linha de comando.
* Inicie o aplicativo de amostra Java com o argumento de linha de comando "sendonly". Nesse modo, o aplicativo não receberá mensagens da fila, ele enviará somente.
* Pressione **Enter** algumas vezes no console do aplicativo Java, que fará com que as mensagens sejam enviadas.
* Essas mensagens são recebidas pelo aplicativo .NET.

#### <a name="output-from-jms-application"></a>Saída do aplicativo de JMS

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>Saída do aplicativo .NET

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>.NET para JMS

Para demonstrar .NET para JMS de mensagens:

* Inicie o aplicativo de amostra do .NET com o argumento de linha de comando "sendonly". Nesse modo, o aplicativo não receberá mensagens da fila, ele enviará somente.
* Inicie o aplicativo de amostra Java sem quaisquer argumentos de linha de comando.
* Pressione **Enter** algumas vezes no console de aplicativo do .NET, o que fará com que as mensagens sejam enviadas.
* Essas mensagens são recebidas pelo aplicativo Java.

#### <a name="output-from-net-application"></a>Saída do aplicativo .NET

```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3  
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>Saída do aplicativo de JMS

```
> java SimpleSenderReceiver 
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>Restrições e os recursos sem suporte

As seguintes restrições existem ao usar JMS sobre AMQP 1.0 com barramento de serviço, ou seja:

* Somente uma **MessageProducer** ou **MessageConsumer** é permitido por **sessão**. Se você precisar criar várias **MessageProducers** ou **MessageConsumers** em um aplicativo, crie uma **sessão** de dedicado para cada um deles.
* Assinaturas de tópico voláteis não são suportadas no momento.
* Atualmente, não há suporte para **MessageSelectors** .
* Destinos temporários, ou seja, **TemporaryQueue**, **TemporaryTopic** não têm suporte, além de **QueueRequestor** e **TopicRequestor** APIs que usá-los.
* Sessões realizadas e transações distribuídas não são suportadas.

## <a name="summary"></a>Resumo

Este guia prático mostrava como usar barramento de serviço orientado recursos de mensagens (filas e publicar/assinar tópicos) do Java usando a API de JMS populares e AMQP 1.0.

Você também pode usar o serviço barramento AMQP 1.0 de outros idiomas, incluindo .NET, C, Python e PHP. Componentes criados usando essas diferentes idiomas podem trocar mensagens confiável e no total fidelidade usando o 1.0 AMQP suporte no carregador de serviço. Para obter mais informações, consulte o [Guia do desenvolvedor do serviço barramento AMQP 1.0](service-bus-amqp-dotnet.md).

## <a name="next-steps"></a>Próximas etapas

* [Suporte de AMQP 1.0 no carregador de serviço do Azure](service-bus-amqp-overview.md)
* [Como usar AMQP 1.0 com a API do serviço barramento .NET](service-bus-dotnet-advanced-message-queuing.md)
* [Barramento AMQP 1.0 de serviço Guia do desenvolvedor](service-bus-amqp-dotnet.md)
* [Como usar filas de barramento de serviço](service-bus-dotnet-get-started-with-queues.md)
* [Central de desenvolvedores de Java](/develop/java/).



 
