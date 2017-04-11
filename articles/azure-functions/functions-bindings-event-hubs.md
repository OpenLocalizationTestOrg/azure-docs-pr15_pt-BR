<properties
    pageTitle="Azure ligações de funções evento Hub | Microsoft Azure"
    description="Compreenda como usar ligações Hub de evento do Azure em funções do Azure."
    services="functions"
    documentationCenter="na"
    authors="wesmc7777"
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
    ms.date="10/17/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-event-hub-bindings"></a>Azure ligações Hub de evento de funções

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e ligações do [Azure evento Hub](../event-hubs/event-hubs-overview.md) de código para funções do Azure. Funções Azure suportam ligações disparador e saída para Hubs de evento do Azure.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Se você estiver começando a usar o Azure Hubs de evento, consulte [Visão geral do Hub de evento do Azure](../event-hubs/event-hubs-overview.md).

## <a name="azure-event-hub-trigger-binding"></a>Hub de evento Azure disparar ligação

Um gatilho de Hub de evento do Azure pode ser usado para responder a um evento enviado para um fluxo de evento do hub de evento. Você deve ter acesso de leitura para o hub de evento para configurar uma associação de disparador.

#### <a name="functionjson-for-event-hub-trigger-binding"></a>Function.JSON para evento Hub disparar ligação

O arquivo de *function.json* para um gatilho de Hub de evento do Azure Especifica as seguintes propriedades:

- `type`: Deve ser definida como *eventHubTrigger*.
- `name`: O nome da variável usado no código de função para a mensagem de hub do evento. 
- `direction`: Deve ser definida como *no*. 
- `path`: O nome do hub do evento.
- `consumerGroup`: Isso é uma propriedade opcional usada para configurar o [grupo de consumidor](../event-hubs-overview.md#consumer-groups) usado para assinar eventos no hub. Se omitido, o `$Default` consumidor grupo é usado. 
- `connection`: O nome da configuração do aplicativo que contém a cadeia de conexão para o namespace que o hub de evento reside em. Copie essa cadeia de caracteres de conexão clicando no botão de **Informações de Conexão** para o namespace, não o hub de evento em si.  Essa cadeia de caracteres de conexão deve ter pelo menos permissões de leitura para ativar o disparador.

        {
          "bindings": [
            {
              "type": "eventHubTrigger",
              "name": "myEventHubMessage",
              "direction": "in",
              "path": "MyEventHub",
              "connection": "myEventHubReadConnectionString"
            }
          ],
          "disabled": false
        }

#### <a name="azure-event-hub-trigger-c-example"></a>Exemplo de disparador c# do Azure Hub de evento
 
Usando o function.json de exemplo acima, o corpo da mensagem do evento será registrado de usando o função código c# abaixo:
 
    using System;
    
    public static void Run(string myEventHubMessage, TraceWriter log)
    {
        log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
    }

#### <a name="azure-event-hub-trigger-f-example"></a>Exemplo de disparador F # do Azure Hub de evento

Usando o function.json de exemplo acima, o corpo da mensagem do evento será registrado de usando o F # função código abaixo:

    let Run(myEventHubMessage: string, log: TraceWriter) =
        log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)

#### <a name="azure-event-hub-trigger-nodejs-example"></a>Exemplo de Node disparadores de Azure Hub de evento
 
Usando o function.json de exemplo acima, o corpo da mensagem do evento será registrado de usando o código de função node abaixo:
 
    module.exports = function (context, myEventHubMessage) {
        context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
        context.done();
    };


## <a name="azure-event-hub-output-binding"></a>Ligação de saída do Azure Hub de evento

Um Hub de evento do Azure ligação de saída é usada para gravar eventos para um fluxo de evento do hub de evento. Você deve ter permissão de enviar a um hub de evento para escrever eventos para ele. 

#### <a name="functionjson-for-event-hub-output-binding"></a>ligação de saída de Function.JSON para Hub de evento

O arquivo de *function.json* para um Hub de evento do Azure saída vinculação especifica as seguintes propriedades:

- `type`: Deve ser definida como *eventHub*.
- `name`: O nome da variável usado no código de função para a mensagem de hub do evento. 
- `path`: O nome do hub do evento.
- `connection`: O nome da configuração do aplicativo que contém a cadeia de conexão para o namespace que o hub de evento reside em. Copie essa cadeia de caracteres de conexão clicando no botão de **Informações de Conexão** para o namespace, não o hub de evento em si.  Essa cadeia de caracteres de conexão deve ter permissões de enviar para enviar a mensagem para o fluxo de evento Hub.
- `direction`: Deve ser definida como *check-out*. 

        {
          "type": "eventHub",
          "name": "outputEventHubMessage",
          "path": "myeventhub",
          "connection": "MyEventHubSend",
          "direction": "out"
        }


#### <a name="azure-event-hub-c-code-example-for-output-binding"></a>Azure evento Hub código exemplo c# para vinculação de saída
 
O seguinte exemplo função código c# demonstra gravar um evento em um fluxo de evento do Hub de evento. Este exemplo representa ligação mostrada acima de saída do Hub do evento aplicado a um gatilho de timer c#.  
 
    using System;
    
    public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
    {
        String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    
        log.Verbose(msg);   
        
        outputEventHubMessage = msg;
    }

#### <a name="azure-event-hub-f-code-example-for-output-binding"></a>Azure exemplo de código evento Hub F # para vinculação de saída

O F # exemplo função código a seguir demonstra gravar um evento em um fluxo de evento do Hub de evento. Este exemplo representa ligação mostrada acima de saída do Hub do evento aplicado a um gatilho de timer c#.

    let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
        let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
        log.Verbose(msg);
        outputEventHubMessage <- msg;

#### <a name="azure-event-hub-nodejs-code-example-for-output-binding"></a>Exemplo de código de evento Hub Node Azure para vinculação de saída
 
O seguinte código de função de exemplo Node demonstra gravar um evento em um fluxo de evento do Hub de evento. Este exemplo representa ligação mostrada acima de saída do Hub do evento aplicado a um gatilho de timer Node.  
 
    module.exports = function (context, myTimer) {
        var timeStamp = new Date().toISOString();
        
        if(myTimer.isPastDue)
        {
            context.log('TimerTriggerNodeJS1 is running late!');
        }

        context.log('TimerTriggerNodeJS1 function ran!', timeStamp);   
        
        context.bindings.outputEventHubMessage = "TimerTriggerNodeJS1 ran at : " + timeStamp;
    
        context.done();
    };

## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
