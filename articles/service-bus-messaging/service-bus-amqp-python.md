<properties 
    pageTitle="Serviço barramento e Python com AMQP 1.0 | Microsoft Azure"
    description="Usando o barramento de serviço do Python com AMQP."
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

# <a name="using-service-bus-from-python-with-amqp-10"></a>Usando o barramento de serviço do Python com AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Massa do próton Python é uma associação de idioma Python em massa do próton-C; Isto é, Python massa do próton é implementado como uma embalagem em torno de um mecanismo implementado em C.

## <a name="download-the-proton-client-library"></a>Baixar a biblioteca de cliente de massa do próton

Você pode baixar massa do próton-C e suas ligações associadas (incluindo Python) do [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). O download está em forma de código fonte. Para criar o código, siga as instruções contidas dentro do pacote baixado.

Observe que no momento da redação deste artigo, o suporte a SSL em massa do próton-C está disponível somente para sistemas operacionais Linux. Como barramento de serviço do Azure requer o uso de SSL, massa do próton-C (e as ligações de idioma) só podem ser usadas para acessar o barramento de serviço do Linux neste momento. Trabalho para habilitar massa do próton-C com SSL no Windows está em andamento então verifique frequentemente para atualizações.

## <a name="working-with-service-bus-queues-topics-and-subscriptions-from-python"></a>Trabalhando com filas, tópicos e assinaturas do Python barramento de serviço

O código a seguir mostra como enviar e receber mensagens de um serviço de barramento entidade de mensagens.

### <a name="send-messages-using-proton-python"></a>Enviar mensagens usando Python massa do próton

O código a seguir mostra como enviar uma mensagem para um serviço de barramento entidade de mensagens.

```
messenger = Messenger()
message = Message()
message.address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"

message.body = u"This is a text string"
messenger.put(message)
messenger.send()
```

### <a name="receive-messages-using-proton-python"></a>Receber mensagens usando o Python massa do próton

O código a seguir mostra como receber uma mensagem de um serviço de barramento entidade de mensagens.

```
messenger = Messenger()
address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"
messenger.subscribe(address)

messenger.start()
messenger.recv(1)
message = Message()
if messenger.incoming:
      messenger.get(message)
messenger.stop()
```

## <a name="messaging-between-net-and-proton-python"></a>Mensagens entre .NET e em massa do próton Python

### <a name="application-properties"></a>Propriedades de aplicativo

#### <a name="proton-python-to-service-bus-net-apis"></a>Massa do próton-Python para barramento .NET APIs de serviço

Propriedades de aplicativo dos seguintes tipos de suporte de mensagens de massa do próton Python: **int**, **long**, **flutuação**, **uuid**, **bool**, **cadeia de caracteres**. O código de Python a seguir mostra como definir propriedades em uma mensagem usando cada um desses tipos de propriedade.

```
message.properties[u"TestString"] = u"This is a string"    
message.properties[u"TestInt"] = 1
message.properties[u"TestLong"] = 1000L
message.properties[u"TestFloat"] = 1.5    
message.properties[u"TestGuid"] = uuid.uuid1()    
```

Na API serviço barramento .NET, propriedades do aplicativo de mensagem são carregadas no conjunto de **Propriedades** de [BrokeredMessage][]. O código a seguir mostra como ler as propriedades de aplicativo de uma mensagem recebida de um cliente de Python.

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

A tabela a seguir mapeia os tipos de propriedade Python para os tipos de propriedade .NET.

| Tipo de propriedade de Python | Tipo de propriedade de .NET |
|----------------------|--------------------|
| int                  | int                |
| flutuação                | duplo             |
| Longas                 | Int64              |
| UUID                 | GUID               |
| bool                 | bool               |
| cadeia de caracteres               | cadeia de caracteres             |

#### <a name="service-bus-net-apis-to-proton-python"></a>Barramento de serviço APIs do .NET em massa do próton Python

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
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

O código de Python a seguir mostra como ler as propriedades de aplicativo de uma mensagem recebida de um cliente de serviço barramento .NET.

```
if message.properties != None:
   for k,v in message.properties.items():         
         print "--   %s : %s (%s)" % (k, str(v), type(v))         
```

A tabela a seguir mapeia os tipos de propriedade .NET para os tipos de propriedade Python.

| Tipo de propriedade de .NET | Tipo de propriedade de Python | Anotações                                                                                                                                                               |
|--------------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte               | int                  | -                                                                                                                                                                     |
| SByte              | int                  | -                                                                                                                                                                     |
| CARACT               | CARACT                 | Classe de massa do próton Python                                                                                                                                                 |
| curto              | int                  | -                                                                                                                                                                     |
| UShort             | int                  | -                                                                                                                                                                     |
| int                | int                  | -                                                                                                                                                                     |
| uint               | int                  | -                                                                                                                                                                     |
| Longas               | int                  | -                                                                                                                                                                     |
| ULong              | Longas                 | Classe de massa do próton Python                                                                                                                                                 |
| flutuação              | flutuação                | -                                                                                                                                                                     |
| duplo             | flutuação                | -                                                                                                                                                                     |
| decimal            | Cadeia de caracteres               | Decimal não é suportado atualmente com massa do próton.                                                                                                                     |
| bool               | bool                 | -                                                                                                                                                                     |
| GUID               | UUID                 | Classe de massa do próton Python                                                                                                                                                 |
| cadeia de caracteres             | cadeia de caracteres               | -                                                                                                                                                                     |
| DateTime           | carimbo de hora            | Classe de massa do próton Python                                                                                                                                                 |
| DateTimeOffset     | DescribedType        | DateTimeOffset.UtcTicks mapeado para o tipo de AMQP:<type name=”datetime-offset” class=restricted source=”long”><descriptor name=”com.microsoft:datetime-offset” /></type> |
| Período de tempo           | DescribedType        | Timespan.Ticks mapeado para o tipo de AMQP:<type name=”timespan” class=restricted source=”long”><descriptor name=”com.microsoft:timespan” /></type>                        |
| URI                | DescribedType        | Uri.AbsoluteUri mapeado para o tipo de AMQP:<type name=”uri” class=restricted source=”string”><descriptor name=”com.microsoft:uri” /></type>                               |

### <a name="standard-properties"></a>Propriedades padrão

As tabelas a seguir mostram o mapeamento entre as propriedades de mensagem padrão de massa do próton Python e as propriedades de mensagem padrão [BrokeredMessage][] .

#### <a name="proton-python-to-service-bus-net-apis"></a>Massa do próton-Python para barramento .NET APIs de serviço

| Massa do próton Python        | Barramento de serviço do .NET         | Anotações                                                     |
|----------------------|--------------------------|-----------------------------------------------------------|
| durável              | n/d                      | Barramento de serviço só oferece suporte a mensagens duráveis.               |
| prioridade             | n/d                      | Barramento de serviço só oferece suporte a prioridade de uma única mensagem.      |
| TTL                  | Message.TimeToLive       | Conversão, TTL de massa do próton Python é definida em milissegundos. |
| primeiro\_comprador      | n/d                      | -                                                           |
| entrega\_contagem      | n/d                      | -                                                           |
| ID                   | Message.MessageID        | -                                                           |
| usuário\_id             | n/d                      | -                                                           |
| endereço              | Message.To               | -                                                           |
| Assunto              | Message.Label            | -                                                           |
| resposta\_para            | Message.ReplyTo          | -                                                           |
| correlação\_id      | Message.CorrelationID    | -                                                           |
| conteúdo\_tipo        | Message.ContentType      | -                                                           |
| conteúdo\_codificação    | n/d                      | -                                                           |
| expiração\_tempo         | n/d                      | -                                                           |
| criação\_tempo       | n/d                      | -                                                           |
| grupo\_id            | Message.SessionId        | -                                                           |
| grupo\_sequência      | n/d                      | -                                                           |
| resposta\_para\_grupo\_id | Message.ReplyToSessionId | -                                                           |
| formato               | n/d                      | -                                                           |

| Barramento de serviço do .NET        | Massa do próton                       | Anotações                                                     |
|-------------------------|------------------------------|-----------------------------------------------------------|
| ContentType             | Message.Content\_tipo        | -                                                           |
| CorrelationId           | Message.Correlation\_id      | -                                                           |
| EnqueuedTimeUtc         | n/d                          | -                                                           |
| Rótulo                   | Message.Subject              | -                                                           |
| MessageId               | Message.ID                   | -                                                           |
| ReplyTo                 | Message.Reply\_para            | -                                                           |
| ReplyToSessionId        | Message.Reply\_para\_grupo\_id | -                                                           |
| ScheduledEnqueueTimeUtc | n/d                          | -                                                           |
| Identificação de sessão               | Message.Group\_id            | -                                                           |
| TimeToLive              | Message.TTL                  | Conversão, TTL de massa do próton Python é definida em milissegundos. |
| Para                      | Message.Address              | -                                                           |

## <a name="next-steps"></a>Próximas etapas

Pronto para saber mais? Visite os links a seguir:

- [Visão geral do serviço barramento AMQP]
- [AMQP no carregador de serviço para o Windows Server]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP no carregador de serviço para o Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

[Visão geral do serviço barramento AMQP]: service-bus-amqp-overview.md
