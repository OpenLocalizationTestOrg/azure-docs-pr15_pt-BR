<properties
    pageTitle="Azure disparadores de barramento de serviço de funções e ligações | Microsoft Azure"
    description="Compreenda como usar disparadores barramento de serviço do Azure e ligações em funções do Azure."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="funções de Azure, funções, processamento de eventos, computação dinâmico, arquitetura sem servidor"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande; glenga"/>

# <a name="azure-functions-service-bus-triggers-and-bindings-for-queues-and-topics"></a>Azure disparadores de barramento de serviço de funções e ligações para tópicos e filas

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e código barramento de serviço do Azure disparadores e ligações em funções do Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="sbtrigger"></a>Gatilho de fila ou tópico barramento de serviço

#### <a name="functionjson"></a>Function.JSON

O arquivo de *function.json* Especifica as seguintes propriedades.

- `name`: O nome da variável utilizado no código de função fila ou tópico ou a mensagem fila ou tópico. 
- `queueName`: Para fila dispare somente, o nome da fila para pesquisar.
- `topicName`: Para tópico dispare somente, o nome do tópico para pesquisar.
- `subscriptionName`: Para tópico dispare somente, o nome da assinatura.
- `connection`: O nome da configuração do aplicativo que contém uma cadeia de conexão do barramento de serviço. A cadeia de conexão deve ser para um namespace barramento de serviço, não limitado a uma fila específica ou tópico. Se a cadeia de conexão não tiver gerenciar direitos, defina o `accessRights` propriedade. Se você deixar `connection` vazio, no disparador ou ligação funcionarão com a cadeia de conexão de serviço barramento padrão para o aplicativo de função, que é especificada pela configuração do aplicativo AzureWebJobsServiceBus.
- `accessRights`: Especifica os direitos de acesso disponíveis para a cadeia de conexão. Valor padrão é `manage`. Defina como `listen` se você estiver usando uma cadeia de conexão que não oferece gerenciar permissões. Caso contrário, as funções runtime tente e falhas fazer operações que exigem gerenciam direitos.
- `type`: Deve ser definida como *serviceBusTrigger*.
- `direction`: Deve ser definida como *no*. 

Exemplo *function.json* para um disparador de fila do barramento de serviço:

```json
{
  "bindings": [
    {
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "name": "myQueueItem",
      "type": "serviceBusTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="c-code-example-that-processes-a-service-bus-queue-message"></a>Código exemplo c# que processa uma mensagem de fila do barramento de serviço

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### <a name="f-code-example-that-processes-a-service-bus-queue-message"></a>F # exemplo de código que processa uma mensagem de fila do barramento de serviço

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

#### <a name="nodejs-code-example-that-processes-a-service-bus-queue-message"></a>Exemplo de código Node que processa uma mensagem de fila do barramento de serviço

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

#### <a name="supported-types"></a>Tipos de compatíveis

A mensagem de fila do barramento de serviço pode ser desserializada a qualquer um dos seguintes tipos:

* Objeto (a partir do JSON)
* cadeia de caracteres
* matriz de bytes 
* `BrokeredMessage`(C#) 

#### <a id="sbpeeklock"></a>Comportamento de PeekLock

O tempo de execução de funções recebe uma mensagem na `PeekLock` modo e chamadas `Complete` na mensagem se a função for concluído com êxito ou chamadas `Abandon` se a função falhar. Se a função é executado mais do que o `PeekLock` tempo limite, o bloqueio será renovada automaticamente.

#### <a id="sbpoison"></a>Manipulação de mensagem suspeita

Barramento de serviço faz sua própria mensagem suspeita que não pode ser controlado ou configurado na configuração de funções do Azure ou código de tratamento. 

#### <a id="sbsinglethread"></a>Único-threading

Por padrão as funções runtime processa várias mensagens de fila simultaneamente. Para direcionar o tempo de execução para processar apenas uma única fila ou mensagem de tópico por vez, defina `serviceBus.maxConcurrrentCalls` para 1 no arquivo *host.json* . Para obter informações sobre o arquivo de *host.json* , consulte [Estrutura da pasta](functions-reference.md#folder-structure) no artigo de referência do desenvolvedor e [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) no wiki do repositório WebJobs.Script.

## <a id="sboutput"></a>Fila de barramento de serviço ou tópico ligação de saída

#### <a name="functionjson"></a>Function.JSON

O arquivo de *function.json* Especifica as seguintes propriedades.

- `name`: O nome da variável usado em código de função da fila ou mensagem de fila. 
- `queueName`: Para fila dispare somente, o nome da fila para pesquisar.
- `topicName`: Para tópico dispare somente, o nome do tópico para pesquisar.
- `subscriptionName`: Para tópico dispare somente, o nome da assinatura.
- `connection`: Mesmo barramento de serviço do disparador.
- `accessRights`: Especifica os direitos de acesso disponíveis para a cadeia de conexão. Valor padrão é `manage`. Defina como `send` se você estiver usando uma cadeia de conexão que não oferece gerenciar permissões. Caso contrário, as funções runtime tente e falhas fazer operações que exigem gerenciam direitos, como Criando filas.
- `type`: Deve ser definida como *serviceBus*.
- `direction`: Deve ser definida como *check-out*. 

Exemplo *function.json* para usando um gatilho de timer para escrever mensagens de fila de barramento de serviço:

```JSON
{
  "bindings": [
    {
      "schedule": "0/15 * * * * *",
      "name": "myTimer",
      "runsOnStartup": true,
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "outputSbQueue",
      "type": "serviceBus",
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="supported-types"></a>Tipos de compatíveis

Funções Azure podem criar uma mensagem de fila do barramento de serviço de qualquer um dos seguintes tipos.

* Objeto (sempre cria uma mensagem JSON, cria a mensagem com um objeto nulo se o valor for nulo quando terminar a função)
* cadeia de caracteres (cria uma mensagem se o valor for não nulas quando terminar a função)
* matriz de bytes (funciona como cadeia de caracteres) 
* `BrokeredMessage`(C#, funciona como cadeia de caracteres)

Para criar várias mensagens em uma função c#, você pode usar `ICollector<T>` ou `IAsyncCollector<T>`. Uma mensagem é criada quando você chamar o `Add` método.

#### <a name="c-code-examples-that-create-service-bus-queue-messages"></a>C# exemplos de código que criar mensagens de fila de barramento de serviço

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### <a name="f-code-example-that-creates-a-service-bus-queue-message"></a>F # exemplo de código que cria uma mensagem de fila do barramento de serviço

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

#### <a name="nodejs-code-example-that-creates-a-service-bus-queue-message"></a>Exemplo de código de Node que cria uma mensagem de fila do barramento de serviço

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
