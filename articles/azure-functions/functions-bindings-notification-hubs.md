<properties
    pageTitle="Vinculação de Hub de notificação de funções Azure | Microsoft Azure"
    description="Compreenda como usar associação de Hub de notificação do Azure em funções do Azure."
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
    ms.date="10/27/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-notification-hub-output-binding"></a>Ligação de saída do Azure Hub de notificação de funções

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e ligações de Hub de notificação do Azure código em funções do Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Suas funções podem enviar notificações por push usando um Hub de notificação do Azure configurado com algumas linhas de código. No entanto, o Hub de notificação do Azure devem ser configurado para a plataforma notificações de serviços (PNS) que você deseja usar. Para obter mais informações sobre como configurar um Hub de notificação do Azure e desenvolver uma aplicativos cliente que registre-se para receber notificações, consulte [Introdução ao Hubs de notificação](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) e clique em sua plataforma de cliente de destino na parte superior.

As notificações de que enviar podem ser notificações nativas ou notificações de modelo. Notificações nativas direcionar uma plataforma de notificação específicos conforme configurado no `platform` propriedade da associação saída. Uma notificação de modelo pode ser usada para várias plataformas de destino.   

## <a name="notification-hub-output-binding-properties"></a>Propriedades de vinculação de saída de hub de notificação

O arquivo function.json fornece as seguintes propriedades:

- `name`: Nome variável usado no código de função da mensagem de notificação hub.
- `type`: deve estar definida como *"notificationHub"*.
- `tagExpression`: Expressões de marca permite que você especificar que as notificações sejam entregues em um conjunto de dispositivos que se inscreveram para receber notificações que correspondem à expressão de marca.  Para obter mais informações, consulte [expressões de roteamento e marca](../notification-hubs/notification-hubs-tags-segment-push-message.md).
- `hubName`: Nome do recurso hub notificação no portal do Azure.
- `connection`: Essa cadeia de caracteres de conexão deve ser uma cadeia de conexão de **Configuração do aplicativo** defina o valor de *DefaultFullSharedAccessSignature* para seu hub de notificação.
- `direction`: deve estar definida como *"-out"*. 
- `platform`: A propriedade de plataforma indica a plataforma de notificação seus destinos de notificação. Deve ser um dos seguintes valores:
    - `template`: Isso é a plataforma padrão, se a propriedade de plataforma é omitida a vinculação de saída. Notificações de modelo podem ser usadas para destinar qualquer plataforma configurada no Hub de notificação do Azure. Para obter mais informações sobre como usar modelos em geral para enviar notificações de entre plataformas com um Hub de notificação do Azure, consulte [modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).
    - `apns`: Serviço de notificações de envio Apple. Para obter mais informações sobre como configurar o hub de notificação para APNS e recebimento de notificação em um aplicativo de cliente, consulte [Enviar as notificações por push para o iOS com Hubs de notificação do Azure](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md) 
    - `adm`: [Dispositivo Amazon de mensagens](https://developer.amazon.com/device-messaging). Para obter mais informações sobre como configurar o hub de notificação para ADM e receber a notificação em um aplicativo de dispositivos Kindle, consulte o [Guia de Introdução com Hubs de notificação para aplicativos de dispositivos Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md) 
    - `gcm`: [Mensagens de nuvem do Google](https://developers.google.com/cloud-messaging/). Firebase nuvem de mensagens, que é a nova versão do GCM, também é suportada. Para obter mais informações sobre como configurar o hub de notificação para GCM/FCM e recebimento de notificação em um aplicativo cliente Android, consulte [Enviar as notificações por push para o Android com Hubs de notificação do Azure](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)
    - `wns`: [Serviços de notificação do Windows Push](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) direcionamento plataformas Windows. Também há suporte para Windows Phone 8.1 e posterior WNS. Para obter mais informações sobre como configurar o hub de notificação para WNS e receber a notificação no aplicativo plataforma de Windows Universal (UWP), consulte [Introdução ao notificação Hubs para Windows Universal plataforma de aplicativos](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
    - `mpns`: [Serviço de notificações Push da Microsoft](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx). Essa plataforma suporta Windows Phone 8 e plataformas mais antigas do Windows Phone. Para obter mais informações sobre como configurar o hub de notificação para MPNS e recebimento de notificação em um aplicativo do Windows Phone, consulte [notificações de envio de envio com Hubs de notificação do Azure no Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)
 
Function.json de exemplo:

    {
      "bindings": [
        {
          "name": "notification",
          "type": "notificationHub",
          "tagExpression": "",
          "hubName": "my-notification-hub",
          "connection": "MyHubConnectionString",
          "platform": "gcm",
          "direction": "out"
        }
      ],
      "disabled": false
    }

## <a name="notification-hub-connection-string-setup"></a>Instalação de cadeia de conexão de hub de notificação

Para usar uma saída de hub de notificação de vinculação, você deve configurar a cadeia de conexão para o hub. Isso pode ser feito na guia *integrar* selecionando seu hub de notificação ou criar um novo. 

Você também pode adicionar manualmente uma cadeia de conexão para um hub existente adicionando uma cadeia de conexão para o *DefaultFullSharedAccessSignature* ao seu hub de notificação. Essa cadeia de caracteres de conexão fornece sua permissão de acesso de função para enviar mensagens de notificação. O valor de cadeia de conexão *DefaultFullSharedAccessSignature* pode ser acessado a partir do botão de **teclas** na lâmina principal de seu recurso de hub de notificação no portal do Azure. Para adicionar manualmente uma cadeia de conexão para o seu hub, use as seguintes etapas: 

1. No **aplicativo de função** blade do portal do Azure, clique em **configurações do aplicativo de função > vá para configurações de serviço de aplicativo**.

2. Na lâmina **configurações** , clique em **Configurações do aplicativo**.

3. Role até a seção de **cadeias de caracteres de Conexão** e adicionar uma entrada nomeada para o valor de *DefaultFullSharedAccessSignature* para seu hub de notificação. Altere o tipo de **escala**.
4. Fazer referência a seu nome de cadeia de conexão nas ligações de saída. Semelhante ao **MyHubConnectionString** usadas no exemplo acima.

## <a name="native-notification-examples"></a>Exemplos de notificação nativo

#### <a name="apns-native-notifications-with-c-queue-triggers"></a>Notificações de nativo APNS com c# fila disparadores

Este exemplo mostra como usar tipos definidos na [Biblioteca de Hubs de notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar uma notificação de APNS nativa. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The JSON format for a native APNS notification is ...
        // { "aps": { "alert": "notification message" }}  

        log.Info($"Sending APNS notification of a new user");   
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                            user.name + ")\" }}";
        log.Info($"{apnsNotificationPayload}");
        await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
    }

#### <a name="gcm-native-notifications-with-c-queue-triggers"></a>Notificações de nativo GCM com c# fila disparadores

Este exemplo mostra como usar tipos definidos na [Biblioteca de Hubs de notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar uma notificação de GCM nativa. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The JSON format for a native GCM notification is ...
        // { "data": { "message": "notification message" }}  

        log.Info($"Sending GCM notification of a new user");    
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                            user.name + ")\" }}";
        log.Info($"{gcmNotificationPayload}");
        await notification.AddAsync(new GcmNotification(gcmNotificationPayload));       
    }

#### <a name="wns-native-notifications-with-c-queue-triggers"></a>Notificações de nativo WNS com c# fila disparadores

Este exemplo mostra como usar tipos definidos na [Biblioteca de Hubs de notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar uma notificação do sistema WNS nativa. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The XML format for a native WNS toast notification is ...
        // <?xml version="1.0" encoding="utf-8"?>
        // <toast>
        //   <visual>
        //     <binding template="ToastText01">
        //       <text id="1">notification message</text>
        //     </binding>
        //   </visual>
        // </toast>

        log.Info($"Sending WNS toast notification of a new user");  
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                        "<toast><visual><binding template=\"ToastText01\">" +
                                            "<text id=\"1\">" + 
                                                "A new user wants to be added (" + user.name + ")" + 
                                            "</text>" +
                                        "</binding></visual></toast>";

        log.Info($"{wnsNotificationPayload}");
        await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));       
    }


## <a name="template-notification-examples"></a>Exemplos de modelo de notificação

#### <a name="template-example-for-nodejs-timer-triggers"></a>Exemplo de modelo para disparadores de timer Node 

Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém `location` e `message`.

    module.exports = function (context, myTimer) {
        var timeStamp = new Date().toISOString();
       
        if(myTimer.isPastDue)
        {
            context.log('Node.js is running late!');
        }
        context.log('Node.js timer trigger function ran!', timeStamp);  
        context.bindings.notification = {
            location: "Redmond",
            message: "Hello from Node!"
        };
        context.done();
    };

#### <a name="template-example-for-f-timer-triggers"></a>Exemplo de modelo para F # timer disparadores

Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém `location` e `message`.

    let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
        notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]


#### <a name="template-example-using-an-out-parameter"></a>Exemplo de modelo usando um parâmetro de saída 

Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém um `message` espaço reservado no modelo.

    using System;
    using System.Threading.Tasks;
    using System.Collections.Generic;
     
    public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
    }
     
    private static IDictionary<string, string> GetTemplateProperties(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["message"] = message;
        return templateProperties;
    }

#### <a name="template-example-with-asynchronous-function"></a>Exemplo de modelo com a função assíncrona

Se você estiver usando o código assíncrono, parâmetros de saída não são permitidos. Nesse caso use `IAsyncCollector` para retornar sua notificação de modelo. O código a seguir é um exemplo assíncrono do código acima. 

    using System;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        log.Info($"Sending Template Notification to Notification Hub");
        await notification.AddAsync(GetTemplateProperties(myQueueItem));    
    }
    
    private static IDictionary<string, string> GetTemplateProperties(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["user"] = "A new user wants to be added : " + message;
        return templateProperties;
    }

#### <a name="template-example-using-json"></a>Exemplo de modelo usando JSON 

Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém um `message` espaço reservado no modelo usando uma cadeia de caracteres JSON válida.

    using System;
     
    public static void Run(string myQueueItem,  out string notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
    }


#### <a name="template-example-using-notification-hubs-library-types"></a>Exemplo de modelo usando tipos de bibliotecas de Hubs de notificação

Este exemplo mostra como usar tipos definidos na [Biblioteca de Hubs de notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 


    #r "Microsoft.Azure.NotificationHubs"

    using System;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
     
    public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
    {
       log.Info($"C# Queue trigger function processed: {myQueueItem}");
       notification = GetTemplateNotification(myQueueItem);
    }

    private static TemplateNotification GetTemplateNotification(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["message"] = message;
        return new TemplateNotification(templateProperties);
    }

## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
