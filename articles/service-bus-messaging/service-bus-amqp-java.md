<properties 
    pageTitle="Serviço barramento e Java com AMQP 1.0 | Microsoft Azure"
    description="Usando o barramento de serviço do Java com AMQP"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="use-service-bus-from-java-with-amqp-10"></a>Usar barramento de serviço do Java com AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

O Java Message Service (JMS) é uma API padrão para trabalhar com middleware orientado para mensagens na plataforma Java. Microsoft que Azure barramento de serviço foi testado com o 1.0 AMQP de acordo com a biblioteca de cliente JMS desenvolvida pelo project Apache Qpid. Esta biblioteca dá suporte a API de 1.1 JMS completo e pode ser usada com qualquer serviço de mensagens compatível com AMQP 1.0. Este cenário também é suportado no [Barramento de serviço para o Windows Server](https://msdn.microsoft.com/library/dn282144.aspx) (local barramento de serviço). Para obter mais informações, consulte [AMQP no barramento de serviço para o Windows Server][].

## <a name="download-the-apache-qpid-amqp-10-jms-client-library"></a>Baixar a biblioteca de cliente Apache Qpid AMQP 1.0 JMS

Para obter informações sobre como baixar a versão mais recente da biblioteca cliente Apache Qpid JMS AMQP 1.0, visite [http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html](http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html).

Adicione os seguintes quatro arquivos JAR do arquivamento de distribuição do Apache Qpid JMS AMQP 1.0 para o caminho de classe Java ao criar e executar aplicativos JMS com barramento de serviço:

-   geronimo-jms\_1.1\_especificações-[versão]. jar

-   qpid-amqp-1-0-Client-[Version].jar

-   qpid-amqp-1-0-Client-JMS-[Version].jar

-   qpid-amqp-1-0-Common-[Version].jar

## <a name="work-with-service-bus-queues-topics-and-subscriptions-from-jms"></a>Trabalhar com filas, tópicos e assinaturas de JMS barramento de serviço

### <a name="java-naming-and-directory-interface-jndi"></a>Interface de diretório (JNDI) e Java de nomenclatura

JMS usa o Java nomeação e a Interface de diretório (JNDI) para criar uma separação entre nomes de lógicos e física. Dois tipos de objetos são resolvidos usando JNDI: **ConnectionFactory** e **destino**. JNDI usa um modelo de provedor no qual você pode conectar serviços de diretório diferentes para lidar com tarefas de resolução de nome. A biblioteca Apache Qpid JMS AMQP 1.0 vem com uma propriedades simples provedor de JNDI baseados em arquivos que é configurado usando um arquivo de texto.

O provedor de JNDI Qpid propriedades do arquivo é configurado usando um arquivo de propriedades dos seguintes formatos:

```
# servicebus.properties – sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCONNECTIONFACTORY = amqps://[username]:[password]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
topic.TOPIC = topic1
queue.QUEUE = queue1
```

#### <a name="configure-the-connection-factory"></a>Configurar a fábrica de conexão

A entrada usada para definir um **ConnectionFactory** no provedor de JNDI de Qpid propriedades do arquivo é do seguinte formato:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Onde `[jndi\_name]` e `[ConnectionURL]` têm os seguintes significados:

| Nome            | Significado                                                                                                                                    |   |   |   |   |
|-----------------|--------------------------------------------------------------------------------------------------------------------------------------------|---|---|---|---|
| `[jndi\_name]`    | O nome lógico da fábrica conexão. Esse nome é resolvido no aplicativo Java usando o JNDI `IntialContext.lookup()` método. |   |   |   |   |
| `[ConnectionURL]` | Uma URL que fornece a biblioteca JMS com as informações necessárias para o corretor AMQP.                                                      |   |   |   |   |

O formato da conexão URL é da seguinte maneira:

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```

Onde `[namespace]`, `[username]`, e `[password]` têm os seguintes significados:

| Nome          | Significado                                                                        |   |   |   |   |
|---------------|--------------------------------------------------------------------------------|---|---|---|---|
| `[namespace]` | O namespace de barramento de serviço obtido do [portal do Azure][].                      |   |   |   |   |
| `[username]`  | O nome de chave de serviço barramento SAS obtido do [portal do Azure][].                    |   |   |   |   |
| `[password]`  | Formulário codificada como URL da chave de serviço barramento SAS obtido do [portal do Azure][]. |   |   |   |   |

> [AZURE.NOTE] Você deve codificar a URL a senha manualmente. Um utilitário de codificação de URL útil está disponível em [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

Por exemplo, se as informações obtidas no portal do é da seguinte maneira:

| Namespace:   | Test.ServiceBus.Windows.NET                  |
|--------------|----------------------------------------------|
| Nome do emissor: | RootManageSharedAccessKey                                        |
| Chave do emissor:  | abcdefg |

Em seguida, para definir um objeto de **ConnectionFactory** chamado `SBCONNECTIONFACTORY`, a cadeia de caracteres de configuração seria assim:

```
connectionfactory.SBCONNECTIONFACTORY = amqps://RootManageSharedAccessKey:abcdefg@test.servicebus.windows.net
```

#### <a name="configure-destinations"></a>Configurar os destinos

A entrada que define um destino no provedor Qpid propriedades de arquivo JNDI é dos seguintes formatos:

```
queue.[jndi_name] = [physical_name]
topic.[jndi_name] = [physical_name]
```

Onde `[jndi\_name]` e `[physical\_name]` têm os seguintes significados:

| Nome              | Significado                                                                                                                                  |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| `[jndi\_name]`    | O nome lógico de destino. Este é nome é resolvido no aplicativo Java usando o JNDI `IntialContext.lookup()` método. |
| `[physical\name]` | O nome da entidade barramento de serviço no qual o aplicativo envia ou recebe mensagens.                                                  |

Observe o seguinte:

- O `[physical\name]` valor pode ser uma fila de barramento de serviço ou tópico.
- Ao receber de uma assinatura de tópico barramento de serviço, o nome físico especificado em JNDI deve ser o nome do tópico. O nome da assinatura é fornecido quando a assinatura durável é criada no código do aplicativo JMS.
- Também é possível tratar uma assinatura de tópico barramento de serviço como uma fila JMS. Há várias vantagens dessa abordagem: o mesmo código receptor pode ser usado para filas tópico assinaturas e todas as informações de endereço (os nomes de tópico e assinatura) são exteriorizadas no arquivo de propriedades.
- Para tratar uma assinatura de tópico barramento de serviço como uma fila JMS, a entrada no arquivo de propriedades deve estar no formato: `queue.[jndi\_name] = [topic\_name]/Subscriptions/[subscription\_name]`. |

Para definir um destino JMS lógico denominado "Tópico" que mapeia para um tópico de barramento de serviço chamado "tópico1", a entrada no arquivo de propriedades seria assim:

```
topic.TOPIC = topic1
```

### <a name="send-messages-using-jms"></a>Enviar mensagens usando JMS

O código a seguir mostra como enviar uma mensagem para um tópico de barramento de serviço. Será considerado que `SBCONNECTIONFACTORY` e `TOPIC` são definidos em um arquivo de configuração de **servicebus.properties** conforme descrito na seção anterior.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env); 
 
ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
MessageProducer producer = session.createProducer(topic);
TextMessage message = session.createTextMessage("This is a text string"); 
producer.send(message);
```

### <a name="receive-messages-using-jms"></a>Receber mensagens usando JMS

O código a seguir mostra `how` para receber uma mensagem de uma assinatura de tópico barramento de serviço. Será considerado que `SBCONNECTIONFACTORY` e tópico são definidos em um arquivo de configuração de **servicebus.properties** conforme descrito na seção anterior. Pressupõe-se também que o nome de assinatura é `subscription1`.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
TopicSubscriber subscriber = session.createDurableSubscriber(topic, "subscription1");
connection.start();
Message message = messageConsumer.receive();
```

### <a name="guidelines-for-building-robust-applications"></a>Diretrizes para criar aplicativos robustos

A especificação de JMS define como o contrato de exceção os métodos de API e o código do aplicativo deve ser escrito para lidar com essas exceções. Aqui estão alguns outros pontos a serem considerados em relação ao tratamento de exceção:

-   Registre um **ExceptionListener** com a conexão JMS usando **connection.setExceptionListener**. Isso permite que um cliente ser notificado sobre um problema assíncrona. Esta notificação é particularmente importante para conexões que apenas consumam mensagens, que não teriam nenhuma outra maneira de saber que sua conexão falhou. O **ExceptionListener** é chamado se há um problema com subjacente AMQP conexão, sessão ou link. Nessa situação, o programa aplicativo deve recriar a **Conexão JMS** **sessão**, **MessageProducer** e **MessageConsumer** objetos do zero.

-   Para verificar que uma mensagem foi com êxito enviada de um **MessageProducer** para uma entidade de barramento de serviço, certifique-se de que o aplicativo foi configurado com o **qpid.sync\_publicar** conjunto de propriedades do sistema. Você pode fazer isso iniciando o programa com a **-Dqpid.sync\_publicar = true** opção de máquina virtual Java definida na linha de comando ao iniciar o aplicativo. Definir esta opção configura a biblioteca não retornar da chamada enviar até que foi recebida confirmação de que a mensagem foi aceita pelo barramento de serviço. Se ocorrer um problema durante a operação de envio, um **JMSException** é gerado. Existem duas causas possíveis: 
    1. Se o problema for devido a barramento de serviço rejeitando a mensagem específica que está sendo enviada, em seguida, uma exceção de **MessageRejectedException** será gerada. Esse erro é transitório, ou devido a algum problema com a mensagem. O curso recomendado de ação é fazer várias tentativas repetir a operação com alguma lógica retirada. Se o problema persistir, a mensagem deve ficar abandonada com um erro conectado localmente. Não é necessário recriar os objetos de **Conexão JMS**, **sessão**ou **MessageProducer** nessa situação. 
    2. Se o problema for devido a barramento de serviço fechando o Link AMQP, em seguida, uma exceção de **InvalidDestinationException** será gerada. Isso pode ser devido a um problema temporário ou devido a entidade de mensagem que está sendo excluído. Nos dois casos, os objetos de **Conexão JMS**, **sessão**e **MessageProducer** devem ser recriados. Se a condição de erro foi temporária, essa operação eventualmente será bem-sucedido. Se a entidade tiver sido excluída, a falha será permanente.

## <a name="messaging-between-net-and-jms"></a>Mensagens entre .NET e JMS

### <a name="message-bodies"></a>Corpos de mensagens

JMS define cinco tipos diferentes de mensagens: **BytesMessage**, **MapMessage**, **ObjectMessage**, **StreamMessage**e **TextMessage**. A API do serviço barramento .NET tem um tipo de mensagem única, [BrokeredMessage][].

#### <a name="jms-to-service-bus-net-api"></a>JMS para barramento de serviço API .NET

As seções a seguir mostram como consumir mensagens de cada um dos tipos de mensagem JMS do .NET. Um exemplo de **ObjectMessage** não foi incluído, como o corpo de uma **ObjectMessage** contém um objeto serializável na linguagem de programação Java, que não é interpretable por um aplicativo .NET.

##### <a name="bytesmessage"></a>BytesMessage

O código a seguir mostra como consumir o corpo de um objeto **BytesMessage** usando as APIs de .NET de barramento de serviço.

```
Stream stream = message.GetBody<Stream>();
int streamLength = (int)stream.Length;

byte[] byteArray = new byte[streamLength];
stream.Read(byteArray, 0, streamLength);

Console.WriteLine("Length = " + streamLength);
for (int i = 0; i < stream.Length; i++)
{
  Console.Write("[" + (sbyte) byteArray[i] + "]");
}
```

##### <a name="mapmessage"></a>MapMessage

O código a seguir mostra como consumir o corpo de um objeto **MapMessage** usando as APIs de .NET de barramento de serviço. Este código percorre os elementos do mapa, exibindo o nome e o valor de cada elemento.

```
Dictionary<String, Object> dictionary = message.GetBody<Dictionary<String, Object>>();

foreach (String mapItemName in dictionary.Keys)
{
  Object mapItemValue = null;
  if (dictionary.TryGetValue(mapItemName, out mapItemValue))
  {
    Console.WriteLine(mapItemName + ":" + mapItemValue);
  }
}
```

##### <a name="streammessage"></a>StreamMessage

O código a seguir mostra como consumir o corpo de um objeto **StreamMessage** usando as APIs de .NET de barramento de serviço. Este código lista cada um dos itens do fluxo, juntamente com seus tipos.

```
List<Object> list = message.GetBody<List<Object>>();

foreach (Object item in list)
{
  Console.WriteLine(item + " (" + item.GetType() + ")");
}
```

##### <a name="textmessage"></a>TextMessage

O código a seguir mostra como consumir o corpo de um objeto **TextMessage** usando as APIs de .NET de barramento de serviço. Este código exibe a cadeia de texto contida no corpo da mensagem.

```
Console.WriteLine("Text: " + message.GetBody<String>());
```

#### <a name="service-bus-net-apis-to-jms"></a>Serviço barramento APIs do .NET JMS

As seções a seguir mostram como um aplicativo .NET pode criar uma mensagem que é recebida no JMS em cada uma dos diferentes tipos de mensagem JMS. Um exemplo de **ObjectMessage** não foi incluído, como o corpo de uma **ObjectMessage** contém um objeto serializável na linguagem de programação Java, que não é interpretable por um aplicativo .NET.

##### <a name="bytesmessage"></a>BytesMessage

O código a seguir mostra como criar um objeto de [BrokeredMessage][] no .NET recebido por um cliente JMS como um **BytesMessage**.

```
byte[] bytes = { 33, 12, 45, 33, 12, 45, 33, 12, 45, 33, 12, 45 };
message = new BrokeredMessage(bytes);
```

##### <a name="streammessage"></a>StreamMessage

O código a seguir mostra como criar um objeto de [BrokeredMessage][] no .NET recebido por um cliente JMS como um **StreamMessage**.

```
List<Object> list = new List<Object>();
list.Add("String 1");
list.Add("String 2");
list.Add("String 3");
list.Add((double)3.14159);
message = new BrokeredMessage(list);
```

##### <a name="textmessage"></a>TextMessage

O código a seguir mostra como consumir corpo de uma **TextMessage** usando a API do serviço barramento .NET. Este código exibe a cadeia de texto contida no corpo da mensagem.

```
message = new BrokeredMessage("this is a text string");
```

### <a name="application-properties"></a>Propriedades de aplicativo

####<a name="jms-to-service-bus-net-apis"></a>JMS para barramento .NET APIs de serviço

Propriedades de aplicativo dos seguintes tipos de suporte de mensagens JMS: **booliana**, **bytes**, **curto**, **int**, **long**, **flutuação**, **duplas**e **cadeia de caracteres**. O código de Java a seguir mostra como definir propriedades em uma mensagem usando cada um desses tipos de propriedade.

```
message.setBooleanProperty("TestBoolean", true); 
message.setByteProperty("TestByte", (byte) 33); 
message.setDoubleProperty("TestDouble", 3.14159D); 
message.setFloatProperty("TestFloat", 3.13159F); 
message.setIntProperty("TestInt", 100); 
message.setStringProperty("TestString", "Service Bus");
```

No .NET APIs do barramento de serviço, propriedades do aplicativo de mensagem são carregadas no conjunto de **Propriedades** de [BrokeredMessage][]. O código a seguir mostra como ler as propriedades de aplicativo de uma mensagem recebida de um cliente JMS.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}
```

A tabela a seguir mostra como os tipos de propriedade JMS mapeiam para os tipos de propriedade .NET.

| Tipo de propriedade JMS | Tipo de propriedade de .NET |
|-------------------|--------------------|
| Byte              | SByte              |
| Número inteiro           | int                |
| Sobrepor             | flutuação              |
| Duplo            | duplo             |
| Booliano           | bool               |
| Cadeia de caracteres            | cadeia de caracteres             |

O tipo de [BrokeredMessage][] suporta propriedades de aplicativo dos seguintes tipos: **bytes**, **sbyte**, **caract**, **curto**, **ushort**, **int**, **uint**, **long**, **ulong**, **Flutuar**, **duplo**, **decimal**, **bool**, **Guid**, **cadeia de caracteres**, **Uri**, **DateTime**, **DateTimeOffset**e **período de tempo**. O código de .NET a seguir mostra como definir propriedades em um objeto de [BrokeredMessage][] usar cada um desses tipos de propriedade.

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

O código de Java a seguir mostra como ler as propriedades de aplicativo de uma mensagem recebida de um cliente de serviço barramento .NET.

```
Enumeration propertyNames = message.getPropertyNames(); 
while (propertyNames.hasMoreElements()) 
{ 
  String name = (String) propertyNames.nextElement(); 
  Object value = message.getObjectProperty(name); 
  System.out.println(name + ": " + value + " (" + value.getClass() + ")"); 
}
```

A tabela a seguir mostra como os tipos de propriedade .NET mapeiam para os tipos de propriedade JMS.

| Tipo de propriedade de .NET | Tipo de propriedade JMS | Anotações                                                                                                                                                               |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte               | UnsignedByte      | -                                                                                                                                                                      |
| SByte              | Byte              | -                                                                                                                                                                     |
| CARACT               | Caractere         | -                                                                                                                                                                     |
| curto              | Curto             | -                                                                                                                                                                     |
| UShort             | UnsignedShort     | -                                                                                                                                                                     |
| int                | Número inteiro           | -                                                                                                                                                                     |
| uint               | UnsignedInteger   | -                                                                                                                                                                     |
| Longas               | Longas              | -                                                                                                                                                                     |
| ULong              | UnsignedLong      | -                                                                                                                                                                     |
| flutuação              | Sobrepor             | -                                                                                                                                                                     |
| duplo             | Duplo            | -                                                                                                                                                                     |
| decimal            | BigDecimal        | -                                                                                                                                                                     |
| bool               | Booliano           | -                                                                                                                                                                     |
| GUID               | UUID              | -                                                                                                                                                                     |
| cadeia de caracteres             | Cadeia de caracteres            | -                                                                                                                                                                     |
| DateTime           | Data              | -                                                                                                                                                                     |
| DateTimeOffset     | DescribedType     | DateTimeOffset.UtcTicks mapeado para o tipo de AMQP:<type name=”datetime-offset” class=restricted source=”long”><descriptor name=”com.microsoft:datetime-offset” /></type> |
| Período de tempo           | DescribedType     | Timespan.Ticks mapeado para o tipo de AMQP:<type name=”timespan” class=restricted source=”long”><descriptor name=”com.microsoft:timespan” /></type>                        |
| URI                | DescribedType     | Uri.AbsoluteUri mapeado para o tipo de AMQP:<type name=”uri” class=restricted source=”string”><descriptor name=”com.microsoft:uri” /></type>                               |

### <a name="standard-headers"></a>Cabeçalhos padrão

As tabelas a seguir mostram como os cabeçalhos padrão JMS e as propriedades padrão de [BrokeredMessage][] são mapeados usando AMQP 1.0.

#### <a name="jms-to-service-bus-net-apis"></a>JMS para barramento .NET APIs de serviço

| JMS              | Barramento de serviço do .NET               | Anotações                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|------------------|--------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| JMSCorrelationID | Message.CorrelationID          | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| JMSDeliveryMode  | Não disponível no momento        | Barramento de serviço só oferece suporte a mensagens duráveis; Por exemplo, DeliveryMode.PERSISTENT, independentemente de qual for especificado.                                                                                                                                                                                                                                                                                                                                                         |
| JMSDestination   | Message.To                     | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| JMSExpiration    | Mensagem. TimeToLive            | Conversão                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| JMSMessageID     | Message.MessageID              | Por padrão, JMSMessageID é codificado no formato binário na mensagem AMQP. No recebimento de id da mensagem binário, a biblioteca de cliente .NET converte uma representação de cadeia de caracteres com base nos valores unicode dos bytes. Para alternar a biblioteca JMS para usar as ids de mensagem de cadeia de caracteres, acrescentar a "messageid binário = false" cadeia de caracteres para os parâmetros de consulta do URL JNDI. Por exemplo: “amqps://[username]:[password]@[namespace].servicebus.windows.net? messageid binário = false ". |
| JMSPriority      | Não disponível no momento        | Barramento de serviço não dá suporte a prioridade da mensagem.                                                                                                                                                                                                                                                                                                                                                                                                                             |
| JMSRedelivered   | Não disponível no momento        | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| JMSReplyTo       | Mensagem. ReplyTo               | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| JMSTimestamp     | Message.EnqueuedTimeUtc        | Conversão                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| JMSType          | Recém-adicionada ["jms-tipo"] | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |

#### <a name="service-bus-net-apis-to-jms"></a>Serviço barramento APIs do .NET JMS

| Barramento de serviço do .NET        | JMS              | Anotações                   |
|-------------------------|------------------|-------------------------|
| ContentType             | -                  | Não disponível no momento |
| CorrelationId           | JMSCorrelationID | -                         |
| EnqueuedTimeUtc         | JMSTimestamp     | Conversão              |
| Rótulo                   | n/d              | Não disponível no momento |
| MessageId               | JMSMessageID     | -                         |
| ReplyTo                 | JMSReplyTo       | -                         |
| ReplyToSessionId        | n/d              | Não disponível no momento |
| ScheduledEnqueueTimeUtc | n/d              | Não disponível no momento |
| Identificação de sessão               | n/d              | Não disponível no momento |
| TimeToLive              | JMSExpiration    | Conversão              |
| Para                      | JMSDestination   | -                         |

## <a name="unsupported-features-and-restrictions"></a>Restrições e os recursos sem suporte

As seguintes restrições existirem quando usando JMS AMQP 1.0 com barramento de serviço:

-   Somente uma **MessageProducer** ou **MessageConsumer** é permitido por sessão. Se você quiser criar vários objetos **MessageProducer** ou **MessageConsumer** em um aplicativo, crie sessões dedicadas para cada um deles.

-   Assinaturas de tópico voláteis não são suportadas no momento.

-   Objetos de **MessageSelector** não são suportados.

-   Destinos temporários; Por exemplo, **TemporaryQueue** ou **TemporaryTopic**, não são suportados, juntamente com o **QueueRequestor** e **TopicRequestor** APIs que usá-los.

-   Sessões realizadas não são suportadas.

-   Transações distribuídas não são suportadas.

## <a name="next-steps"></a>Próximas etapas

Pronto para saber mais? Visite os links a seguir:

- [Visão geral do serviço barramento AMQP]
- [AMQP no carregador de serviço para o Windows Server]

[AMQP no carregador de serviço para o Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

[Visão geral do serviço barramento AMQP]: service-bus-amqp-overview.md
[Portal do Azure]: https://portal.azure.com
