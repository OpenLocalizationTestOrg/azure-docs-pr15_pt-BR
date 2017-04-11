<properties 
    pageTitle="Como usar o barramento de serviço do Azure com o SDK do WebJobs" 
    description="Saiba como usar filas de barramento de serviço do Azure e tópicos com o SDK do WebJobs." 
    services="app-service\web, service-bus" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-service-bus-with-the-webjobs-sdk"></a>Como usar o barramento de serviço do Azure com o SDK do WebJobs

## <a name="overview"></a>Visão geral

Este guia fornece c# exemplos de código que mostram como acionar um processo quando uma mensagem de barramento de serviço do Azure for recebida. Os exemplos de código usam [WebJobs SDK](websites-dotnet-webjobs-sdk.md) versão 1. x.

Guia supõe que você sabe [como criar um projeto de WebJob no Visual Studio com cadeias de caracteres de conexão que apontam para sua conta de armazenamento](websites-dotnet-webjobs-sdk-get-started.md).

Os trechos de código mostram somente funções, não o código que cria o `JobHost` objeto como neste exemplo:

```
public class Program
{
   public static void Main()
   {
      JobHostConfiguration config = new JobHostConfiguration();
      config.UseServiceBus();
      JobHost host = new JobHost(config);
      host.RunAndBlock();
   }
}
```

Um [exemplo de código completo do barramento de serviço](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Program.cs) está no repositório do azure-webjobs-amostras de sdk no GitHub.com.

## <a id="prerequisites"></a>Pré-requisitos

Para trabalhar com barramento de serviço, você precisa instalar o pacote de NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) além de outros pacotes WebJobs SDK. 

Você também precisa definir a cadeia de conexão AzureWebJobsServiceBus além as cadeias de caracteres de conexão de armazenamento.  Você pode fazer isso no `connectionStrings` seção do arquivo App, conforme mostrado no exemplo a seguir:

        <connectionStrings>
            <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsServiceBus" connectionString="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[yourKey]"/>
        </connectionStrings>

Para um projeto de exemplo que inclui a configuração de cadeia de conexão do barramento de serviço no arquivo App, consulte [exemplo de barramento de serviço](https://github.com/Azure/azure-webjobs-sdk-samples/tree/master/BasicSamples/ServiceBus). 

As cadeias de caracteres de conexão também podem ser definidas no ambiente do tempo de execução Azure, que então substitui as configurações de App quando o WebJob é executado no Azure; Para obter mais informações, consulte [Introdução ao SDK do WebJobs](websites-dotnet-webjobs-sdk-get-started.md#configure-the-web-app-to-use-your-azure-sql-database-and-storage-account).

## <a id="trigger"></a>Como disparar uma função quando for recebida uma mensagem de fila do barramento de serviço

Para escrever uma função que o SDK WebJobs chama quando for recebida uma mensagem de fila, use o `ServiceBusTrigger` atributo. O construtor de atributo leva um parâmetro que especifica o nome da fila para pesquisar.

### <a name="how-servicebustrigger-works"></a>Como funciona o ServiceBusTrigger

O SDK recebe uma mensagem na `PeekLock` modo e chamadas `Complete` na mensagem se a função for concluído com êxito ou chamadas `Abandon` se a função falhar. Se a função é executado mais do que o `PeekLock` tempo limite, o bloqueio será renovada automaticamente.

Barramento de serviço faz sua própria manipulação de fila suspeita que não pode ser controlada ou configurada pelo SDK do WebJobs. 

### <a name="string-queue-message"></a>Mensagem de fila de cadeia de caracteres

O exemplo de código a seguir lê uma mensagem de fila que contém uma cadeia de caracteres e grava a cadeia de caracteres para o painel de WebJobs SDK.

        public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
            TextWriter logger)
        {
            logger.WriteLine(message);
        }

**Observação:** Se você estiver criando as mensagens de fila em um aplicativo que não usa o SDK do WebJobs, verifique se definir [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) como "texto/sem formatação".

### <a name="poco-queue-message"></a>Mensagem de fila POCO

O SDK desserializará automaticamente uma mensagem de fila que contenha JSON para um POCO [(simples objeto CLR antigo](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) tipo. O exemplo de código a seguir lê uma mensagem de fila que contenha um `BlobInformation` objeto que tem uma `BlobName` propriedade:

        public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
            TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

Para exemplos de código mostrando como usar propriedades da POCO para trabalhar com blobs e tabelas na mesma função, consulte a [versão de filas de armazenamento deste artigo](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#pocoblobs).

Se seu código que cria a mensagem de fila não usar o SDK do WebJobs, use o código semelhante ao seguinte exemplo:

        var client = QueueClient.CreateFromConnectionString(ConfigurationManager.ConnectionStrings["AzureWebJobsServiceBus"].ConnectionString, "blobadded");
        BlobInformation blobInformation = new BlobInformation () ;
        var message = new BrokeredMessage(blobInformation);
        client.Send(message);

### <a name="types-servicebustrigger-works-with"></a>Tipos de ServiceBusTrigger funciona com

Além disso `string` e tipos POCO, você pode usar o `ServiceBusTrigger` atributo com uma matriz de bytes ou um `BrokeredMessage` objeto.

## <a id="create"></a>Como criar mensagens de fila de barramento de serviço

Escrever uma função que cria um novo uso de mensagem de fila a `ServiceBus` atributo e passe o nome da fila para o construtor de atributo. 


### <a name="create-a-single-queue-message-in-a-non-async-function"></a>Criar uma mensagem de fila única em uma função não assíncrono

O exemplo de código a seguir usa um parâmetro de saída para criar uma nova mensagem na fila denominada "outputqueue" com o mesmo conteúdo que a mensagem recebida na fila denominada "inputqueue".

        public static void CreateQueueMessage(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] out string outputQueueMessage)
        {
            outputQueueMessage = queueMessage;
        }

O parâmetro de saída para a criação de uma mensagem única fila pode ser qualquer um dos seguintes tipos:

* `string`
* `byte[]`
* `BrokeredMessage`
* Um tipo POCO serializável que você definir. Serializado automaticamente como JSON.

Para os parâmetros de tipo POCO, uma mensagem de fila sempre é criada quando terminar a função; Se o parâmetro for nulo, o SDK cria uma mensagem de fila que retornará nula quando a mensagem for recebida e desserializada. Para os outros tipos, se o parâmetro é nulo nenhuma mensagem de fila é criada.

### <a name="create-multiple-queue-messages-or-in-async-functions"></a>Criar várias mensagens de fila ou em funções assíncronas

Para criar várias mensagens, use o `ServiceBus` atributo com `ICollector<T>` ou `IAsyncCollector<T>`, conforme mostrado no exemplo de código a seguir:

        public static void CreateQueueMessages(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

Cada mensagem de fila é criada imediatamente quando o `Add` método é chamado.

## <a id="topics"></a>Como trabalhar com tópicos do barramento de serviço

Para escrever uma função que o SDK chama quando for recebida uma mensagem sobre um tópico de barramento de serviço, use o `ServiceBusTrigger` atributo com o construtor que usa o nome de tópico e assinatura, conforme mostrado no exemplo de código a seguir:

        public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
            TextWriter logger)
        {
            logger.WriteLine("Topic message: " + message);
        }

Para criar uma mensagem sobre um tópico, use o `ServiceBus` atributo com um nome de tópico da mesma maneira que você pode usá-lo com um nome de fila.

## <a name="features-added-in-release-11"></a>Recursos adicionados na versão 1.1

Os seguintes recursos foram adicionados na versão 1.1:

* Permitir personalização profunda de processamento por meio de mensagens `ServiceBusConfiguration.MessagingProvider`.
* `MessagingProvider`suporta a personalização do barramento serviço `MessagingFactory` e `NamespaceManager`.
* A `MessageProcessor` padrão de estratégia permite especificar um processador por fila/tópico.
* Concorrência de processamento de mensagem é suportada por padrão. 
* Fácil personalização de `OnMessageOptions` via `ServiceBusConfiguration.MessageOptions`.
* Permitir [AccessRights](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Functions.cs#L71) sejam especificadas na `ServiceBusTriggerAttribute` / `ServiceBusAttribute` (para cenários em que você pode não ter gerenciam direitos). 

## <a id="queues"></a>Tópicos relacionados cobertos pelo artigo de instruções de filas de armazenamento

Para obter informações sobre cenários de WebJobs SDK não específicos barramento de serviço, veja [como usar o armazenamento de fila Azure com o SDK do WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Os tópicos abordados nesse artigo incluem o seguinte:

* Funções assíncronas
* Várias instâncias
* Desligamento
* Usar atributos de WebJobs SDK no corpo de uma função
* Definir as cadeias de caracteres de conexão do SDK no código
* Definir valores para WebJobs SDK parâmetros do construtor no código
* Disparar uma função manualmente
* Gravar logs

## <a id="nextsteps"></a>Próximas etapas

Este guia forneceu exemplos de código que mostram como lidar com cenários comuns para trabalhar com barramento de serviço do Azure. Para obter mais informações sobre como usar WebJobs do Azure e o SDK do WebJobs, consulte [Azure WebJobs recomendados recursos](http://go.microsoft.com/fwlink/?linkid=390226).
 
