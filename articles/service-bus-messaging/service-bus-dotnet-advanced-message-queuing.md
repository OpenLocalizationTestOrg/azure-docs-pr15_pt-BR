<properties 
    pageTitle="Como usar AMQP 1.0 com a API do barramento de serviço .NET | Microsoft Azure" 
    description="Saiba como usar avançadas mensagem fila Protodol (AMQP) 1.0 com a API de barramento de serviço do Azure .NET." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-amqp-10-with-the-service-bus-net-api"></a>Como usar AMQP 1.0 com a API do serviço barramento .NET

O Advanced mensagem fila Protocol (AMQP) 1.0 é um protocolo eficiente, confiável e nível de fios de mensagens que você pode usar para construir robusta, entre plataformas, aplicativos de mensagens.

Suporte para AMQP 1.0 no carregador de serviço significa que você pode usar na fila e publicar/assinar intermediário recursos de mensagens de um intervalo de plataformas usando um protocolo binário eficiente. Além disso, você pode criar aplicativos compostos dos componentes criados usando uma mistura de idiomas, estruturas e sistemas operacionais.

Este artigo explica como usar os barramento de serviço orientado recursos de mensagens (filas e publicar/assinar tópicos) de aplicativos .NET usando a API do serviço barramento .NET. Não há um [artigo companion](service-bus-java-how-to-use-jms-api-amqp.md) que explica como fazer a mesma usando a API Java Message Service (JMS) padrão. Você pode usar essas duas guias juntos para saber mais sobre mensagens entre plataformas usando AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Introdução ao barramento de serviço

Este artigo pressupõe que você já tem um namespace de barramento de serviço contendo uma fila denominada "Fila1." Se você não fizer isso, você pode criar a fila usando o [portal do Azure][]e namespace. Para obter mais informações sobre como criar filas e namespaces barramento de serviço, consulte [Introdução ao filas de barramento de serviço](service-bus-dotnet-get-started-with-queues.md#1-create-a-namespace-using-the-Azure-portal).

## <a name="download-the-service-bus-sdk"></a>Baixe o SDK do barramento de serviço

Suporte de AMQP 1.0 está disponível no serviço barramento SDK versão 2.1 ou posterior. Você pode baixar os bits mais recentes do NuGet em [http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/).

## <a name="code-net-applications"></a>Aplicativos do .NET de código

Por padrão, a biblioteca de cliente .NET de barramento de serviço se comunica com o serviço de barramento de serviço usando um protocolo de baseado em SOAP dedicado. Para usar AMQP 1.0 em vez de padrão protocolo requer configuração explícita na cadeia de caracteres de conexão de serviço barramento conforme descrito na próxima seção. Diferente dessa alteração, o código do aplicativo permanece basicamente inalterado quando usando AMQP 1.0.

Na versão atual, há alguns recursos de API que não têm suporte ao usar AMQP. Esses recursos sem suporte são listados posteriormente na seção [restrições e os recursos sem suporte](#unsupported-features-and-restrictions). Algumas das definições de configuração avançada também têm um significado diferente ao usar AMQP. Nenhuma dessas configurações são usadas neste artigo, mas mais detalhes estão disponíveis na [Visão geral de AMQP de barramento de serviço](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences).

### <a name="configure-via-appconfig"></a>Configurar via App

É uma prática recomendada que aplicativos usam o arquivo de configuração de App para armazenar configurações. Para aplicativos de barramento de serviço, você pode usar o App armazenar barramento de serviço **ConnectionString**. Este aplicativo de exemplo também usa o App para armazenar o nome do serviço barramento mensagens entidade que ele usa.

Um exemplo de arquivo de App é mostrado aqui:

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
            <add key="EntityName" value="queue1" />
    </appSettings>
</configuration>
```

### <a name="configure-the-service-bus-connection-string"></a>Configurar a cadeia de conexão do barramento de serviço

O valor da configuração **Microsoft.ServiceBus.ConnectionString** é a cadeia de conexão do barramento de serviço é usada para configurar a conexão para barramento de serviço. O formato é da seguinte maneira:

```
Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp
```

Onde `[namespace]` e `[SAS key]` são obtidos a partir do [portal do Azure][]. Para obter mais informações, consulte [como usar filas de barramento de serviço] [].

Ao usar AMQP, a cadeia de conexão é acrescentada com `;TransportType=Amqp`, que informa que a biblioteca de cliente para tornar sua conexão ao serviço barramento usando AMQP 1.0.

### <a name="configure-the-entity-name"></a>Configurar o nome de entidade

Este aplicativo exemplo usa a `EntityName` configuração na seção **appSettings** do arquivo App para configurar o nome da fila com a qual o aplicativo de troca de mensagens.

### <a name="a-simple-net-application-using-a-service-bus-queue"></a>Um aplicativo de .NET simples usando uma fila de barramento de serviço

O exemplo a seguir envia e recebe mensagens de e uma fila de barramento de serviço.

```
// SimpleSenderReceiver.cs
    
using System;
using System.Configuration;
using System.Threading;
using Microsoft.ServiceBus.Messaging;
    
namespace SimpleSenderReceiver
{
    class SimpleSenderReceiver
    {
        private static string connectionString;
        private static string entityName;
        private static Boolean runReceiver = true;
        private MessagingFactory factory;
        private MessageSender sender;
        private MessageReceiver receiver;
        private MessageListener messageListener;
        private Thread listenerThread;
    
        static void Main(string[] args)
        {
            try
            {
                if ((args.Length > 0) && args[0].ToLower().Equals("sendonly"))
                    runReceiver = false;
                    
                string ConnectionStringKey = "Microsoft.ServiceBus.ConnectionString";
                string entityNameKey = "EntityName";
                entityName = ConfigurationManager.AppSettings[entityNameKey];
                connectionString = ConfigurationManager.AppSettings[ConnectionStringKey];
                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
    
                Console.WriteLine("Press [enter] to send a message. " +
                    "Type 'exit' + [enter] to quit.");
    
                while (true)
                {
                    string s = Console.ReadLine();
                    if (s.ToLower().Equals("exit"))
                    {
                        simpleSenderReceiver.Close();
                        System.Environment.Exit(0);
                    }
                    else
                        simpleSenderReceiver.SendMessage();
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Caught exception: " + e.Message);
            }
        }
    
        public SimpleSenderReceiver()
        {
            factory = MessagingFactory.CreateFromConnectionString(connectionString);
            sender = factory.CreateMessageSender(entityName);
    
            if (runReceiver)
            {
                receiver = factory.CreateMessageReceiver(entityName);
                messageListener = new MessageListener(receiver);
                listenerThread = new Thread(messageListener.Listen);
                listenerThread.Start();
            }
        }
    
        public void Close()
        {
            messageListener.RequestStop();
            listenerThread.Join();
            factory.Close();
        }
    
        private void SendMessage()
        {
            BrokeredMessage message = new BrokeredMessage("Test AMQP message from .NET");
            sender.Send(message);
            Console.WriteLine("Sent message with MessageID = " 
                + message.MessageId);
        }

    }
    
    public class MessageListener
    {
        private MessageReceiver messageReceiver;
        public MessageListener(MessageReceiver receiver)
        {
            messageReceiver = receiver;
        }
    
        public void Listen()
        {
            while (!_shouldStop)
            {
                try
                {
                    BrokeredMessage message = 
                        messageReceiver.Receive(new TimeSpan(0, 0, 10));
                    if (message != null)
                    {
                        Console.WriteLine("Received message with MessageID = " + 
                            message.MessageId);
                        message.Complete();
                    }
                }
                catch (Exception e)
                {
                    Console.WriteLine("Caught exception: " + e.Message);
                    break;
                }
            }
        }
    
        public void RequestStop()
        {
            _shouldStop = true;
        }
    
        private volatile bool _shouldStop;
    }
}
```

### <a name="run-the-application"></a>Executar o aplicativo

Executando o aplicativo gera saída do formulário:

```
> SimpleSenderReceiver.exe
Press [enter] to send a message. Type 'exit' + [enter] to quit.
    
Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
    
Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
Received message with MessageID = d00e2e088f06416da7956b58310f7a06
    
Received message with MessageID = f27f79ec124548c196fd0db8544bca49
Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Entre plataformas de mensagens entre JMS e .NET

Este tópico mostrava como enviar mensagens para barramento de serviço usando .NET e também como receber essas mensagens usando .NET. Entretanto, um dos principais benefícios do AMQP 1.0 é que ele permite que aplicativos são criados de componentes escritos em idiomas diferentes, com mensagens trocadas confiável e fidelidade total.

Usando o aplicativo de .NET de amostra descrito acima e um aplicativo Java semelhante tirado de um guia associado, [como usar a API com barramento de serviço & AMQP 1.0 Java Message Service (JMS)](service-bus-java-how-to-use-jms-api-amqp.md), é possível a troca de mensagens entre .NET e Java. 

Para obter mais informações sobre os detalhes de mensagens usando o barramento de serviço e AMQP 1.0 em plataformas cruzadas, consulte [Visão geral do serviço barramento AMQP 1.0](service-bus-amqp-overview.md).

### <a name="jms-to-net"></a>JMS para .NET

Para demonstrar JMS para .NET de mensagens:

* Inicie o aplicativo de exemplo .NET sem quaisquer argumentos de linha de comando.
* Inicie o aplicativo de amostra Java com o argumento de linha de comando "sendonly". Nesse modo, o aplicativo não receberá mensagens da fila, ele enviará somente.
* Pressione **Enter** algumas vezes no console do aplicativo Java, que fará com que as mensagens sejam enviadas.
* Essas mensagens são recebidas pelo aplicativo .NET.

### <a name="output-from-jms-application"></a>Saída do aplicativo de JMS

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

### <a name="output-from-net-application"></a>Saída do aplicativo .NET

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

## <a name="net-to-jms"></a>.NET para JMS

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

Os seguintes recursos da API do barramento de serviço do .NET não são suportados no momento ao usar AMQP:

 * Transações
 * Enviar por destino de transferência

Para obter mais informações, consulte [os recursos sem suporte, restrições e diferenças de comportamento](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences).

## <a name="summary"></a>Resumo

Este artigo mostrava como acessar os recursos de mensagens de barramento de serviço orientado (filas e publicar/assinar tópicos) do .NET usando AMQP 1.0 e a API do serviço barramento .NET.

Você também pode usar o serviço barramento AMQP 1.0 de outros idiomas incluindo Java, C, Python e PHP. Componentes criados usando estas linguagens podem trocar mensagens confiavelmente e fidelidade total usando o AMQP 1.0 no carregador de serviço. Para obter mais informações, consulte [Visão geral de AMQP de barramento de serviço](service-bus-amqp-dotnet.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você leu uma visão geral do barramento de serviço e AMQP com .NET, consulte os links a seguir para obter mais informações.

* [Suporte de AMQP 1.0 no carregador de serviço do Azure](service-bus-amqp-overview.md)
* [Como usar a API Java Message Service (JMS) com barramento de serviço & AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
* [Como usar filas de barramento de serviço](service-bus-dotnet-get-started-with-queues.md)
 
[Portal do Azure]: https://portal.azure.com
