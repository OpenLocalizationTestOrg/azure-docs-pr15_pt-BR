<properties
    pageTitle="Vinculação de funções Twilio Azure | Microsoft Azure"
    description="Compreenda como usar ligações Twilio com funções do Azure."
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
    ms.date="10/20/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-twilio-output-binding"></a>Ligação de saída do Azure Twilio de funções

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e usar ligações Twilio com funções do Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Azure suporta funções Twilio saída ligações para habilitar suas funções enviar mensagens de texto SMS com algumas linhas de código e uma conta de [Twilio](https://www.twilio.com/) . 
 

## <a name="functionjson-for-azure-notification-hub-output-binding"></a>ligação de saída Function.JSON para Hub de notificação do Azure

O arquivo function.json fornece as seguintes propriedades:

- `name`: Nome variável usada no código de função para a mensagem de texto SMS Twilio.
- `type`: deve estar definida como *"twilioSms"*.
- `accountSid`: Este valor deve ser definido para o nome da configuração do aplicativo que detém o Sid de conta Twilio.
- `authToken`: Este valor deve ser definido para o nome da configuração do aplicativo que detém o token de autenticação Twilio.
- `to`: Este valor é definido para o número de telefone que o texto SMS é enviado para.
- `from`: Este valor é definido para o número de telefone que o texto SMS é enviado da.
- `direction`: deve estar definida como *"-out"*.
- `body`: Este valor pode ser usado para codificar a mensagem de texto SMS se você não precisa defini-la dinamicamente no código de sua função. 

 
Function.json de exemplo:

    {
      "type": "twilioSms",
      "name": "message",
      "accountSid": "TwilioAccountSid",
      "authToken": "TwilioAuthToken",
      "to": "+1704XXXXXXX",
      "from": "+1425XXXXXXX",
      "direction": "out",
      "body": "Azure Functions Testing"
    }


## <a name="example-c-queue-trigger-with-twilio-output-binding"></a>Ligação de saída do disparador do exemplo c# fila com Twilio

#### <a name="synchronous"></a>Síncrono

Este exemplo síncrono de código para um gatilho de fila do armazenamento do Azure usa um parâmetro de saída para enviar uma mensagem de texto a um cliente que fez uma ordem.

    #r "Newtonsoft.Json"
    #r "Twilio.Api"

    using System;
    using Newtonsoft.Json;
    using Twilio;

    public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        dynamic order = JsonConvert.DeserializeObject(myQueueItem);
        string msg = "Hello " + order.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the SMSMessage variable.
        message = new SMSMessage();

        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        message.Body = msg;
        message.To = order.mobileNumber;
    }

#### <a name="asynchronous"></a>Assíncrona

Este exemplo assíncrona de código para um gatilho de fila do armazenamento do Azure envia uma mensagem de texto para um cliente que fez uma ordem.

    #r "Newtonsoft.Json"
    #r "Twilio.Api"
     
    using System;
    using Newtonsoft.Json;
    using Twilio;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        dynamic order = JsonConvert.DeserializeObject(myQueueItem);
        string msg = "Hello " + order.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the SMSMessage variable.
        SMSMessage smsText = new SMSMessage();

        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        smsText.Body = msg;
        smsText.To = order.mobileNumber;
        
        await message.AddAsync(smsText);
    }


## <a name="example-nodejs-queue-trigger-with-twilio-output-binding"></a>Ligação de saída do disparador de fila do exemplo Node com Twilio

Este exemplo Node envia uma mensagem de texto para um cliente que fez uma ordem.

    module.exports = function (context, myQueueItem) {
        context.log('Node.js queue trigger function processed work item', myQueueItem);
    
        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        var msg = "Hello " + myQueueItem.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the message binding.
        context.bindings.message = {};
    
        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        context.bindings.message = {
            body : msg,
            to : myQueueItem.mobileNumber
        };
    
        context.done();
    };

## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
