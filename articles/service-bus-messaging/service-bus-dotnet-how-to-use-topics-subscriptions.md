<properties
    pageTitle="Use os tópicos de barramento de serviço com .NET | Microsoft Azure"
    description="Saiba como usar tópicos de barramento de serviço e assinaturas com .NET no Azure. Exemplos de código são gravados para aplicativos .NET."
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
    ms.topic="get-started-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Como usar assinaturas e tópicos de barramento de serviço

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como usar assinaturas e tópicos de barramento de serviço. Os exemplos são gravados em c# e usam as APIs do .NET. Os cenários cobertos incluem a criação de tópicos e assinaturas, criando filtros de assinatura, enviando mensagens a um tópico, receber mensagens de uma assinatura e excluindo tópicos e assinaturas. Para obter mais informações sobre tópicos e assinaturas, consulte a seção [próximas etapas](#next-steps) .

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="configure-the-application-to-use-service-bus"></a>Configurar o aplicativo para usar barramento de serviço

Quando você cria um aplicativo que usa o barramento de serviço, você deve adicionar uma referência para o conjunto de barramento de serviço e inclui os namespaces correspondentes. A maneira mais fácil de fazer isso é baixar o pacote de [NuGet](https://www.nuget.org) apropriado.

## <a name="get-the-service-bus-nuget-package"></a>Obter o pacote de serviço barramento NuGet

O [pacote de serviço barramento NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) é a maneira mais fácil para obter a API do barramento de serviço e configurar o aplicativo com todas as dependências de barramento de serviço necessárias. Para instalar o pacote de serviço barramento NuGet em seu projeto, faça o seguinte:

1.  No Solution Explorer, clique com botão direito **referências**, clique em **Gerenciar pacotes do NuGet**.
2.  Pesquise "Barramento de serviço" e selecione o item de **Barramento de serviço do Microsoft Azure** . Clique em **instalar** para concluir a instalação, feche a caixa de diálogo a seguir:

    ![][7]

Agora você está pronto para escrever código para barramento de serviço.

## <a name="create-a-service-bus-connection-string"></a>Criar uma cadeia de conexão do barramento de serviço

Barramento de serviço usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais. Você pode colocar sua cadeia de conexão em um arquivo de configuração, em vez de embuti-la:

- Ao usar os serviços do Azure, é recomendável que você armazene sua cadeia de caracteres de conexão usando o sistema de configuração do serviço Azure (arquivos .csdef e .cscfg).
- Ao usar sites Azure ou máquinas virtuais do Azure, é recomendável que você armazene sua cadeia de caracteres de conexão usando o sistema de configuração do .NET (por exemplo, o arquivo Web. config).

Em ambos os casos, você pode recuperar sua cadeia de caracteres de conexão usando o `CloudConfigurationManager.GetSetting` método, conforme mostrado neste artigo.

### <a name="configure-your-connection-string"></a>Configurar sua cadeia de conexão

O mecanismo de configuração de serviço permite que você dinamicamente altere configurações do [portal do Azure][] sem reimplantar o aplicativo. Por exemplo, adicione uma `Setting` de etiqueta para o seu arquivo de definição (**.csdef**) de serviço, conforme mostrado no exemplo a seguir.

```
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```

Depois que você especifica valores no arquivo de configuração (.cscfg) do serviço.

```
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Use o nome de chave de assinatura de acesso compartilhado (SAS) e chaves valores recuperados a partir do portal, conforme descrito anteriormente.

### <a name="configure-your-connection-string-when-using-azure-websites-or-azure-virtual-machines"></a>Configurar sua cadeia de conexão ao usar sites Azure ou máquinas virtuais do Azure

Ao usar sites ou máquinas virtuais, é recomendável que você use o sistema de configuração do .NET (por exemplo, Web. config). Armazenar a cadeia de conexão usando o `<appSettings>` elemento.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Use o nome de SAS e valores-chave que você recuperou do [portal do Azure][], conforme descrito anteriormente.

## <a name="create-a-topic"></a>Criar um tópico

Você pode executar operações de gerenciamento de tópicos do barramento de serviço e assinaturas usando a classe de [Gerenciador de namespace](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Essa classe fornece métodos para criar, enumerar e excluir tópicos.

O exemplo a seguir cria um `NamespaceManager` objeto usando o Azure `CloudConfigurationManager` classe com uma cadeia de conexão que consiste o endereço base de um namespace de barramento de serviço e as associações de segurança apropriadas credenciais com permissões para gerenciá-lo. Essa cadeia de caracteres de conexão é da seguinte forma:

```
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Use o exemplo a seguir, fornecidas as definições de configuração na seção anterior.

```
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

Há sobrecargas do método [CreateTopic](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createtopic.aspx) que permitem definir as propriedades do tópico; Por exemplo, para definir o padrão para vida útil (TTL) valor a ser aplicado a mensagens enviadas para o tópico. Essas configurações são aplicadas usando a classe [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) . O exemplo a seguir mostra como criar um tópico chamado **TestTopic** com um tamanho máximo de 5 GB e uma mensagem de padrão TTL de 1 minuto.

```
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [AZURE.NOTE] Você pode usar o método [TopicExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.topicexists.aspx) em [Gerenciador de namespace](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) objetos para verificar se já existe um tópico com um nome especificado dentro de um namespace.

## <a name="create-a-subscription"></a>Criar uma assinatura

Você também pode criar assinaturas de tópico usando a classe de [Gerenciador de namespace](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens passadas para fila virtual da assinatura.

> [AZURE.IMPORTANT] Em ordem para as mensagens sejam recebidas por uma assinatura, você deve criar essa assinatura antes de enviar quaisquer mensagens para o tópico. Se não houver nenhuma inscrição para um tópico, o tópico descarta essas mensagens.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma assinatura com o filtro padrão (MatchAll)

Se nenhum filtro for especificado quando uma nova assinatura for criada, o filtro de **MatchAll** é o filtro padrão que é usado. Quando você usa o filtro de **MatchAll** , todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada "AllMessages" e usa o filtro de **MatchAll** padrão.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### <a name="create-subscriptions-with-filters"></a>Criar assinaturas com filtros

Você também pode configurar filtros que permitem especificar quais mensagens enviadas para um tópico devem aparecer dentro de uma assinatura de tópico específico.

O tipo mais flexível de filtro compatível com assinaturas é a classe [SqlFilter][] , que implementa um subconjunto de SQL92. Filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para saber mais sobre as expressões que podem ser usadas com um filtro SQL, consulte a sintaxe [SqlFilter.SqlExpression][] .

O exemplo a seguir cria uma inscrição chamada **HighMessages** com um objeto de [SqlFilter][] que seleciona somente as mensagens que têm uma propriedade personalizada de **MessageNumber** maior que 3.

```
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

Da mesma forma, o exemplo a seguir cria uma inscrição chamada **LowMessages** com um [SqlFilter][] que seleciona somente as mensagens que têm uma **MessageNumber** propriedade menor ou igual a 3.

```
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

Agora quando uma mensagem é enviada para `TestTopic`, sempre é fornecida para receptores inscrito para a assinatura de tópico **AllMessages** e entregue seletivamente para receptores assinados as assinaturas de tópico **HighMessages** e **LowMessages** (dependendo do conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

Para enviar uma mensagem para um tópico de barramento de serviço, o seu aplicativo cria um objeto de [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) usando a cadeia de conexão.

O seguinte código demonstra como criar um objeto de [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) para o tópico **TestTopic** criado anteriormente usando o [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx) chamada API.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

As mensagens enviadas para os tópicos de barramento de serviço são instâncias da classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Objetos de [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) têm um conjunto de propriedades padrão (como [rótulo](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), um dicionário que é usado para armazenar as propriedades personalizadas específicas do aplicativo e um corpo de dados de aplicativo aleatório. Um aplicativo pode definir o corpo da mensagem, passando qualquer objeto serializável para o construtor do objeto [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) e apropriado **DataContractSerializer** é usada para serializar o objeto. Como alternativa, **System.IO.Stream** pode ser fornecido.

O exemplo a seguir demonstra como enviar mensagens de teste cinco ao objeto [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) **TestTopic** obtido no exemplo de código anterior. Observe que o valor da propriedade [MessageNumber](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx) de cada mensagem varia dependendo a iteração do loop (Isso determina quais assinaturas recebam-lo).

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageNumber"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Tópicos de barramento de serviço oferece suporte a um tamanho máximo de mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB no [nível Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizados, pode ter um tamanho máximo de 64 KB. Não há nenhum limite o número de mensagens contidos em um tópico, mas há um limite no tamanho total das mensagens mantidas por um tópico. Tamanho este tópico é definido em tempo de criação, com um limite de 5 GB. Se a partição estiver habilitada, o limite superior é maior. Para obter mais informações, consulte [Partitioned mensagens entidades](service-bus-partitioning.md).

## <a name="how-to-receive-messages-from-a-subscription"></a>Como receber mensagens de uma assinatura

A maneira recomendada para receber mensagens de uma assinatura é usar um objeto [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) . Objetos de [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) podem trabalhar em dois modos diferentes: [ *ReceiveAndDelete* e *PeekLock*](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Quando receber usando o modo de **ReceiveAndDelete** , é uma operação de captura de único; Isto é, quando barramento de serviço recebe uma solicitação de leitura para uma mensagem em uma assinatura, ele marca a mensagem como sendo consumida e retorna para o aplicativo. Modo de **ReceiveAndDelete** é o modelo mais simples e funciona melhor para cenários em que um aplicativo pode suportar não processando uma mensagem em caso de uma falha. Para entender isso, considere um cenário em que o consumidor emite a solicitação de recebimento e então trava antes de processá-lo. Porque barramento de serviço tiver marcado a mensagem como consumida, quando o aplicativo é reiniciado e começa consumindo mensagens novamente, a mensagem que foi consumida antes da falha ter perdido a ele.

No modo de **PeekLock** (que é o modo padrão), o processo de recebimento se torna uma operação de dois estágios, que possibilita aos aplicativos de suporte que não é possível suportar mensagens ausentes. Quando barramento de serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, bloqueia, para evitar que outros consumidores recebê-lo e retorna para o aplicativo. Depois que o aplicativo termina de processar a mensagem (ou armazena confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento chamando [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) na mensagem recebida. Quando barramento de serviço vê **concluída** a chamada, ele marca a mensagem como sendo consumida e remove-o da assinatura.

O exemplo a seguir demonstra como mensagens podem ser recebidas e processadas usando o modo de **PeekLock** padrão. Para especificar um valor diferente de [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , você pode usar outra sobrecarga para [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx). Este exemplo usa o retorno de chamada [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) para processar mensagens quando chegarem na assinatura de **HighMessages** .

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

Este exemplo configura o retorno de chamada [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) usando um objeto [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx) . [Preenchimento automático](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) está definido como **false** para habilitar o controle manual de quando ligar [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) a mensagem recebida. [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) é definido como 1 minuto, o que faz com que o cliente de esperar até um minuto antes de encerrar o recurso de renovação automática e o cliente faz uma nova chamada para verificar mensagens. Este valor da propriedade reduz o número de vezes que o cliente faz chamadas paga que não recuperar mensagens.

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como lidar com mensagens ilegíveis e travamentos de aplicativos

Barramento de serviço fornece funcionalidade para ajudá-lo a recuperar normalmente de erros em seu aplicativo ou dificuldades processamento de uma mensagem. Se um aplicativo de recebimento não for possível processar a mensagem por algum motivo, ele pode chamar o método [abandonar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) na mensagem recebida (em vez do método de [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) ). Isso faz com que o barramento de serviço desbloquear a mensagem na assinatura e disponibilizá-lo a ser recebida novamente, pelo mesmo aplicativo consumindo ou por outro aplicativo consumindo.

Há também um tempo limite associado a uma mensagem bloqueada na assinatura, e se o aplicativo falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se o aplicativo trava), e em seguida, serviço desbloqueie a mensagem automaticamente e disponibiliza a ser recebida novamente.

Se o aplicativo trava após processamento da mensagem, mas antes da solicitação [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) é emitida, a mensagem será entregue novamente para o aplicativo quando ele for reiniciado. Isso normalmente é chamado *pelo menos uma vez processamento*; ou seja, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser entregue novamente. Se o cenário não pode suportar processamento duplicado, os desenvolvedores de aplicativos devem adicionar lógica adicional para seu aplicativo para controlar a entrega de mensagem duplicada. Isso geralmente é obtido usando a propriedade [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) da mensagem, que permanece constante entre tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Excluir tópicos e assinaturas

O exemplo a seguir demonstra como excluir o tópico **TestTopic** do namespace de serviço **HowToSample** .

```
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

Excluir um tópico também exclui qualquer assinaturas que são registradas com o tópico. Assinaturas também podem ser excluídas independentemente. O código a seguir demonstra como excluir uma inscrição chamada **HighMessages** do tópico **TestTopic** .

```
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os fundamentos de tópicos do barramento de serviço e assinaturas, siga estes links para saber mais.

-   [Filas, tópicos e assinaturas][].
-   [Exemplo de filtros de tópico][]
-   Referência de API do [SqlFilter][].
-   Criar um aplicativo de trabalho que envia e recebe mensagens de e para uma fila de barramento de serviço: [barramento de serviço orientado mensagens tutorial de .NET][].
-   Exemplos de barramento de serviço: Baixe de [amostras Azure][] ou consulte [Visão geral](service-bus-samples.md).

  [Portal do Azure]: https://portal.azure.com

  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

  [Filas, tópicos e assinaturas]: service-bus-queues-topics-subscriptions.md
  [Exemplo de filtros de tópico]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Tutorial do barramento de serviço orientado mensagens .NET]: service-bus-brokered-tutorial-dotnet.md
  [Exemplos do Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
