<properties 
    pageTitle="Serviço barramento e PHP com AMQP 1.0 | Microsoft Azure"
    description="Usando o barramento de serviço do PHP com AMQP."
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

# <a name="using-service-bus-from-php-with-amqp-10"></a>Usando o barramento de serviço do PHP com AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Massa do próton PHP é uma associação de idioma PHP em massa do próton-C; Isto é, PHP massa do próton é implementado como uma embalagem em torno de um mecanismo implementado em C.

## <a name="downloading-the-proton-client-library"></a>Download da biblioteca de cliente de massa do próton

Você pode baixar massa do próton-C e suas ligações associadas (incluindo PHP) do [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). O download está em forma de código fonte. Para criar o código, siga as instruções contidas no pacote baixado.

> [AZURE.IMPORTANT] No momento da redação deste artigo, o suporte a SSL em massa do próton-C só está disponível para sistemas operacionais Linux. Como barramento de serviço do Azure requer o uso de SSL, massa do próton-C (e as ligações de idioma) só podem ser usadas para acessar o barramento de serviço do Linux neste momento. Trabalho para habilitar massa do próton-C com SSL no Windows está em andamento, então verifique frequentemente para atualizações.

## <a name="working-with-service-bus-queues-topics-and-subscriptions-from-php"></a>Trabalhando com filas, tópicos e assinaturas do PHP barramento de serviço

O código a seguir mostra como enviar e receber mensagens de um serviço de barramento entidade de mensagens.

### <a name="sending-messages-using-proton-php"></a>Enviar mensagens usando PHP massa do próton

O código a seguir mostra como enviar uma mensagem para um serviço de barramento entidade de mensagens.

```
$messenger = new Messenger();
$message = new Message();
$message->address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";

$message->body = "This is a text string";
$messenger->put($message);
$messenger->send();
```

### <a name="receiving-messages-using-proton-php"></a>Recebimento de mensagens usando PHP massa do próton

O código a seguir mostra como receber uma mensagem de um serviço de barramento entidade de mensagens.

```
$messenger = new Messenger();
$address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";
$messenger->subscribe($address);

$messenger->start();
$messenger->recv(1);

if($messenger->incoming())
{
   $message = new Message();
   $messenger->get($message);      
}

$messenger->stop();
```

## <a name="messaging-between-net-and-proton-php"></a>Mensagens entre .NET e massa do próton-PHP

### <a name="application-properties"></a>Propriedades de aplicativo

#### <a name="protonphp-to-service-bus-net-apis"></a>ProtonPHP para barramento .NET APIs de serviço

Propriedades de aplicativo dos seguintes tipos de suporte de mensagens de massa do próton PHP: **inteiro**, **duplo**, **booliano**, **cadeia de caracteres**e **objeto**. O código PHP a seguir mostra como definir propriedades em uma mensagem usando cada um desses tipos de propriedade.

```
$message->properties["TestInt"] = 1;    
$message->properties["TestDouble"] = 1.5;      
$message->properties["TestBoolean"] = False;
$message->properties["TestString"] = "Service Bus";    
$message->properties["TestObject"] = new UUID("1234123412341234");   
```

No .NET APIs do barramento de serviço, propriedades do aplicativo de mensagem são carregadas no conjunto de **Propriedades** de [BrokeredMessage][]. O código a seguir mostra como ler as propriedades de aplicativo de uma mensagem recebida de um cliente do PHP.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}if (message.Properties.Keys.Count > 0)
{
foreach (string name in message.Properties.Keys)
{
  Object value = message.Properties[name];
  Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
}
Console.WriteLine();
}
```

A tabela a seguir mapeia os tipos de propriedade PHP para os tipos de propriedade .NET.

| Tipo de propriedade PHP | Tipo de propriedade de .NET |
|-------------------|--------------------|
| número inteiro           | int                |
| duplo            | duplo             |
| booliano           | bool               |
| cadeia de caracteres            | cadeia de caracteres             |
| objeto            | Objeto             |

#### <a name="service-bus-net-apis-to-php"></a>Barramento de serviço APIs do .NET ao PHP

O tipo de [BrokeredMessage][] suporta propriedades de aplicativo dos seguintes tipos: **byte**, **sbyte**, **caract**, **curto**, **ushort**, **int**, **uint**, **longos**, **ulong**, **flutuação**, **double**, **decimal**, **bool**, **Guid**, **cadeia de caracteres**, **Uri**, **DateTime**, **DateTimeOffset**e **período de tempo**. O código de .NET a seguir mostra como definir propriedades em um objeto de [BrokeredMessage][] usar cada um desses tipos de propriedade.

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

O código PHP a seguir mostra como ler as propriedades de aplicativo de uma mensagem recebida de um cliente de serviço barramento .NET.

```
if ($message->properties != null)
{
  foreach($message->properties as $key => $value)
  {
    printf("-- %s : %s (%s) \n", $key, $value, gettype($value));                       
  }         
}
```

A tabela a seguir mapeia os tipos de propriedade .NET para os tipos de propriedade PHP.

| Tipo de propriedade de .NET | Tipo de propriedade PHP | Anotações                                                                                                                                                               |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte               | número inteiro           | -                                                                                                                                                                     |
| SByte              | número inteiro           | -                                                                                                                                                                     |
| CARACT               | CARACT              | Classe de massa do próton PHP                                                                                                                                                    |
| curto              | número inteiro           | -                                                                                                                                                                     |
| UShort             | número inteiro           | -                                                                                                                                                                     |
| int                | número inteiro           | -                                                                                                                                                                     |
| uint               | Número inteiro           | -                                                                                                                                                                     |
| Longas               | número inteiro           | -                                                                                                                                                                     |
| ULong              | número inteiro           | -                                                                                                                                                                     |
| flutuação              | duplo            | -                                                                                                                                                                     |
| duplo             | duplo            | -                                                                                                                                                                     |
| decimal            | cadeia de caracteres            | Decimal não é suportado atualmente com massa do próton.                                                                                                                     |
| bool               | booliano           | -                                                                                                                                                                     |
| GUID               | UUID              | Classe de massa do próton PHP                                                                                                                                                    |
| cadeia de caracteres             | cadeia de caracteres            | -                                                                                                                                                                     |
| DateTime           | número inteiro           | -                                                                                                                                                                     |
| DateTimeOffset     | DescribedType     | DateTimeOffset.UtcTicks mapeado para o tipo de AMQP:<type name="datetime-offset" class=restricted source="long"><descriptor name="com.microsoft:datetime-offset" /></type> |
| Período de tempo           | DescribedType     | Timespan.Ticks mapeado para o tipo de AMQP:<type name="timespan" class=restricted source="long"><descriptor name="com.microsoft:timespan" /></type>                        |
| URI                | DescribedType     | Uri.AbsoluteUri mapeado para o tipo de AMQP:<type name="uri" class=restricted source="string"><descriptor name="com.microsoft:uri" /></type>                               |

### <a name="standard-properties"></a>Propriedades padrão

As tabelas a seguir mostram o mapeamento entre as propriedades de mensagem padrão de massa do próton PHP e as propriedades de mensagem padrão [BrokeredMessage][] .

| Massa do próton PHP           | Barramento de serviço do .NET         | Anotações                                                    |
|----------------------|--------------------------|----------------------------------------------------------|
| Durável              | n/d                      | Barramento de serviço só oferece suporte a mensagens duráveis.          |
| Prioridade             | n/d                      | Barramento de serviço só oferece suporte a prioridade de uma única mensagem. |
| TTL                  | Message.TimeToLive       | Conversão, TTL de massa do próton PHP é definida em milissegundos.   |
| primeiro\_comprador      | -                          | -                                                          |
| entrega\_contagem      | -                          | -                                                          |
| ID                   | Message.Id               | -                                                          |
| usuário\_id             | -                          | -                                                          |
| Endereço              | Message.To               | -                                                          |
| Assunto              | Message.Label            | -                                                          |
| resposta\_para            | Message.ReplyTo          | -                                                          |
| correlação\_id      | Message.CorrelationId    | -                                                          |
| conteúdo\_tipo        | Message.ContentType      | -                                                          |
| conteúdo\_codificação    | n/d                      | -                                                          |
| expiração\_tempo         | Message.ExpiresAtUTC     | -                                                          |
| criação\_tempo       | n/d                      | -                                                          |
| grupo\_id            | Message.SessionId        | -                                                          |
| grupo\_sequência      | -                          | -                                                          |
| resposta\_para\_grupo\_id | Message.ReplyToSessionId | -                                                          |
| Formato               | n/d                      | -

#### <a name="service-bus-net-apis-to-proton-php"></a>Barramento de serviço APIs do .NET em massa do próton PHP

| Barramento de serviço do .NET        | Massa do próton PHP                                             | Anotações                                                  |
|-------------------------|--------------------------------------------------------|--------------------------------------------------------|
| ContentType             | Mensagem -\>conteúdo\_tipo                                | -                                                        |
| CorrelationId           | Mensagem -\>correlação\_id                              | -                                                        |
| EnqueuedTimeUtc         | Mensagem -\>anotações [x-opt-enfileirados-hora]             | -                                                        |
| Rótulo                   | Mensagem -\>assunto                                      | -                                                        |
| MessageId               | Mensagem -\>id                                           | -                                                        |
| ReplyTo                 | Mensagem -\>resposta\_para                                    | -                                                        |
| ReplyToSessionId        | Mensagem -\>resposta\_para\_grupo\_id                         | -                                                        |
| ScheduledEnqueueTimeUtc | Mensagem -\>anotações ["x-opt-agendado-fila-hora"] | -                                                        |
| Identificação de sessão               | Mensagem -\>grupo\_id                                    | -                                                        |
| TimeToLive              | Mensagem -\>ttl                                          | Conversão, TTL de massa do próton PHP é definida em milissegundos. |
| Para                      | Mensagem -\>endereço                                      | -                                                        |

## <a name="next-steps"></a>Próximas etapas

Pronto para saber mais? Visite os links a seguir:

- [Visão geral do serviço barramento AMQP]
- [AMQP no carregador de serviço para o Windows Server]


[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP no carregador de serviço para o Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[Visão geral do serviço barramento AMQP]: service-bus-amqp-overview.md
