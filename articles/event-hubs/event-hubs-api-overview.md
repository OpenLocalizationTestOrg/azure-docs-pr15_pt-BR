<properties 
    pageTitle="Visão geral das APIs Hubs evento Azure | Microsoft Azure"
    description="Um resumo de algumas das cliente do evento Hubs .NET chave APIs."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/16/2016"
    ms.author="sethm" />

# <a name="event-hubs-api-overview"></a>Visão geral de Hubs API do evento

Este artigo resume alguns dos principais APIs do cliente de evento Hubs .NET. Há duas categorias: gerenciamento e APIs do tempo de execução. Tempo de execução APIs consistem em todas as operações necessárias para enviar e receber uma mensagem. Operações de gerenciamento permitem que você gerencie um estado de entidade Hubs de eventos por criar, atualizar e excluir entidades.

Cenários de monitoramento abrangem gerenciamento e o tempo de execução. Para a documentação de referência detalhadas sobre as APIs .NET, consulte as referências de [Serviço barramento .NET](https://msdn.microsoft.com/library/azure/mt419900.aspx) e [API de EventProcessorHost](https://msdn.microsoft.com/library/azure/mt445521.aspx) .

## <a name="management-apis"></a>APIs de gerenciamento

Para executar as seguintes operações de gerenciamento, você deve ter permissões de **Gerenciar** no namespace Hubs de evento:

### <a name="create"></a>Criar

```
// Create the Event Hub
EventHubDescription ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
namespaceManager.CreateEventHubAsync(ehd).Wait();
```

### <a name="update"></a>Atualização

```
EventHubDescription ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
string ruleName = "myeventhubmanagerule";
string ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
namespaceManager.UpdateEventHubAsync(ehd).Wait();
```

### <a name="delete"></a>Excluir

```
namespaceManager.DeleteEventHubAsync("Event Hub name").Wait();
```

## <a name="run-time-apis"></a>APIs de tempo de execução

### <a name="create-publisher"></a>Criar o publisher

```
// EventHubClient model (uses implicit factory instance, so all links on same connection)
EventHubClient eventHubClient = EventHubClient.Create("Event Hub name");
```

### <a name="publish-message"></a>Publicar mensagem

```
// Create the device/temperature metric
MetricEvent info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
EventData data = new EventData(new byte[10]); // Byte array
EventData data = new EventData(Stream); // Stream 
EventData data = new EventData(info, serializer) //Object and serializer 
    {
       PartitionKey = info.DeviceId.ToString()
    };

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>Criar consumidor

```
// Create the Event Hubs client
EventHubClient eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
EventHubConsumerGroup defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index);

// From one day ago
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));
                        
// From specific offset, -1 means oldest
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>Consumir mensagem

```
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)
                                    
// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>Host de processador de evento APIs

Essas APIs fornecem resiliência para processos de trabalho que pode ficar indisponível, distribuindo fragmentos entre trabalhadores disponíveis.

```
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

string eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
string blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

EventHubDescription eventHubDescription = new EventHubDescription(EventHubName);
EventProcessorHost host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
            host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
host.UnregisterEventProcessorAsync().Wait();   
```

A interface de [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx) é definida da seguinte maneira:

```
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            Process messages here
        }
        
        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }


    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre cenários de Hubs de evento, visite esses links:

- [O que é Azure evento Hubs?](event-hubs-what-is-event-hubs.md)
- [Visão geral de Hubs de evento](event-hubs-overview.md)
- [Guia de programação de Hubs de evento](event-hubs-programming-guide.md)
- [Exemplos de código de Hubs de evento](http://code.msdn.microsoft.com/site/search?query=event hub&f[0].Value=event hubs&f[0].Type=SearchText&ac=5)

As referências de API .NET estão aqui:

- [Referências barramento de serviço e a API do evento Hubs .NET](https://msdn.microsoft.com/library/azure/mt419900.aspx)
- [Host de processador de evento Referência API](https://msdn.microsoft.com/library/azure/mt445521.aspx)
