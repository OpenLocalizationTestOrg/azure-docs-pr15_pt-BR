<properties
    pageTitle="Enviando notificações de envio com Hubs de notificação do Azure e Node"
    description="Saiba como usar Hubs de notificação para enviar as notificações de envio de um aplicativo de Node."
    keywords="notificação, push notifications,node.js push, ios envio por push"
    services="notification-hubs"
    documentationCenter="nodejs"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Enviando notificações de envio com Hubs de notificação do Azure e Node
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

##<a name="overview"></a>Visão geral

> [AZURE.IMPORTANT] Para concluir este tutorial, você deve ter uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Este guia mostrará como enviar as notificações por push com a Ajuda de Hubs de notificação do Azure diretamente a partir de um aplicativo de Node. 

Os cenários cobertos incluem enviando notificações por push para aplicativos nas seguintes plataformas:

* Android
* iOS
* Windows Phone
* Plataforma Windows universal 

Para obter mais informações sobre hubs de notificação, consulte a seção [Próximas etapas](#next) .

##<a name="what-are-notification-hubs"></a>O que são notificação Hubs?

Azure Hubs de notificação fornecem uma infraestrutura de fácil de usar, várias plataformas e escalável para enviar notificações por push para dispositivos móveis. Para obter detalhes sobre a infraestrutura de serviço, consulte a página de [Hubs de notificação do Azure](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx) .

##<a name="create-a-nodejs-application"></a>Criar um aplicativo de Node

A primeira etapa neste tutorial está criando um novo aplicativo Node em branco. Para obter instruções sobre como criar um aplicativo de Node, consulte [criar e implantar um aplicativo de Node ao Site do Azure][nodejswebsite], [Serviço de nuvem Node] [ Node.js Cloud Service] usando o Windows PowerShell ou [Site com WebMatrix].

##<a name="configure-your-application-to-use-notification-hubs"></a>Configurar seu aplicativo para usar Hubs de notificação

Para usar Hubs de notificação do Azure, você precisa baixar e usar o Node [pacote azure](https://www.npmjs.com/package/azure), que inclui um conjunto interno de bibliotecas de auxiliar que se comunicar com os serviços de restante de notificação de envio.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Use o Gerenciador de pacote de nó (NPM) para obter o pacote

1.  Use uma interface de linha como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Linux) e navegue até a pasta onde você criou seu aplicativo em branco.

2.  Digite **npm instalar azure sb** na janela de comando.

3.  Você pode executar manualmente o comando **ls** ou **dir** para verificar se um **nó\_módulos** pasta foi criada. Dentro dessa pasta, localize o pacote do **azure** , que contém as bibliotecas que você precisa para acessar o Hub de notificação.

>[AZURE.NOTE] Você pode saber mais sobre como instalar NPM em oficial [NPM blog](http://blog.npmjs.org/post/85484771375/how-to-install-npm). 

### <a name="import-the-module"></a>Importar o módulo

Usando um editor de texto, adicione o seguinte à parte superior do arquivo **Server. js** do aplicativo:

    var azure = require('azure');

### <a name="setup-an-azure-notification-hub-connection"></a>Configurar uma conexão de Hub de notificação do Azure

O objeto **NotificationHubService** permite que você trabalhe com hubs de notificação. O código a seguir cria um objeto de **NotificationHubService** para o hub de nofication denominado **hubname**. Adicione-o na parte superior do arquivo **Server. js** , após a instrução para importar o módulo azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

O valor de **connectionstring** de conexão pode ser obtido a partir do [Portal do Azure] , executando as seguintes etapas:

1. No painel de navegação esquerdo, clique em **Procurar**.

2. Selecione **Hubs de notificação**e, em seguida, localize o hub que você deseja usar para a amostra. Você pode se referir a [guia de Introdução do Windows Store tutorial](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) se precisar de ajuda para criar um novo Hub de notificação.

3. Selecione **configurações**.

4. Clique em **políticas de acesso**. Você verá as duas cadeias de caracteres de conexão de acesso completo e compartilhados.

![Portal Azure - Hubs de notificação](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [AZURE.NOTE] Você também pode recuperar a cadeia de conexão usando o cmdlet **Get-AzureSbNamespace** fornecido pelo [PowerShell do Azure](../powershell-install-configure.md) ou o comando **Mostrar sb azure namespace** com a [Interface de linha do Azure (Azure comando)](../xplat-cli-install.md).

##<a name="general-architecture"></a>Arquitetura geral

O objeto **NotificationHubService** expõe as seguintes instâncias de objeto para enviar notificações por push para aplicativos e dispositivos específicos:

* **Android** - usar o objeto **GcmService** , que está disponível em **notificationHubService.gcm**
* **iOS** - usar o objeto **ApnsService** , que pode ser acessado em **notificationHubService.apns**
* **Windows Phone** - usar o objeto **MpnsService** , que está disponível em **notificationHubService.mpns**
* **Universal plataforma Windows** - use o objeto **WnsService** , que está disponível em **notificationHubService.wns**

### <a name="how-to-send-push-notifications-to-android-applications"></a>Como: enviar as notificações por push para aplicativos Android

O objeto **GcmService** fornece um método **Enviar** que pode ser usado para enviar notificações por push para aplicativos Android. O método **Enviar** aceita os seguintes parâmetros:

* **Marcas** - o identificador de marca. Se nenhuma marca for fornecida, a notificação será enviada para os clientes de al.
* **Carga** - JSON ou carga de cadeia de caracteres bruto da mensagem.
* **Retorno de chamada** - a função de retorno de chamada.

Para obter mais informações sobre o formato de carga, consulte a seção de **carga** do documento do [Servidor de GCM implementando](http://developer.android.com/google/gcm/server.html#payload) .

O código a seguir usa a instância de **GcmService** exposta pela **NotificationHubService** para enviar uma notificação por push para todos os clientes registrados.

    var payload = {
      data: {
        message: 'Hello!'
      }
    };
    notificationHubService.gcm.send(null, payload, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Como: enviar as notificações por push para aplicativos do iOS

Mesmo como com aplicativos Android descrito acima, o objeto de **ApnsService** fornece um método **Enviar** que pode ser usado para enviar notificações por push para aplicativos do iOS. O método **Enviar** aceita os seguintes parâmetros:

* **Marcas** - o identificador de marca. Se nenhuma marca for fornecida, a notificação será enviada para todos os clientes.
* **Carga** - JSON da mensagem ou carga de cadeia de caracteres.
* **Retorno de chamada** - a função de retorno de chamada.

Para obter mais informações no formato de carga, consulte a seção de **Carga de notificação** do documento [Local e o guia de programação de notificação por Push](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) .

O código a seguir usa a instância de **ApnsService** exposta pela **NotificationHubService** para enviar uma mensagem de alerta para todos os clientes:

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
        // notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Como: enviar as notificações por push para aplicativos do Windows Phone

O objeto **MpnsService** fornece um método **Enviar** que pode ser usado para enviar notificações por push para aplicativos do Windows Phone. O método **Enviar** aceita os seguintes parâmetros:

* **Marcas** - o identificador de marca. Se nenhuma marca for fornecida, a notificação será enviada para todos os clientes.
* **Carga** - carga XML da mensagem.
* **TargetName**  -  `toast` para notificações do sistema. `token`para notificações de peças.
* **NotificationClass** - a prioridade da notificação. Consulte a seção de **Elementos de cabeçalho HTTP** do documento [notificações de um servidor de envio](http://msdn.microsoft.com/library/hh221551.aspx) para valores válidos.
* **Opções** - cabeçalhos de solicitação opcionais.
* **Retorno de chamada** - a função de retorno de chamada.

Para obter uma lista de opções válidas **TargetName**, **NotificationClass** e cabeçalho, confira a página de [notificações de um servidor de envio](http://msdn.microsoft.com/library/hh221551.aspx) .

O código de exemplo a seguir usa a instância de **MpnsService** exposta pela **NotificationHubService** para enviar uma notificação de envio do sistema:

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Como: enviar as notificações por push para aplicativos de plataforma de Windows Universal (UWP)

O objeto **WnsService** fornece um método **Enviar** que pode ser usado para enviar notificações por push para aplicativos de plataforma Universal do Windows.  O método **Enviar** aceita os seguintes parâmetros:

* **Marcas** - o identificador de marca. Se nenhuma marca for fornecida, a notificação será enviada para todos os clientes registrados.
* **Carga** - a carga da mensagem XML.
* **Tipo** - o tipo de notificação.
* **Opções** - cabeçalhos de solicitação opcionais.
* **Retorno de chamada** - a função de retorno de chamada.

Para obter uma lista de tipos válidos e cabeçalhos de solicitação, consulte [cabeçalhos de solicitação e resposta de serviço de notificação por Push](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

O código a seguir usa a instância de **WnsService** exposta pela **NotificationHubService** para enviar uma notificação de envio do sistema para um aplicativo UWP:

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
        // notification sent
      }
    });

## <a name="next-steps"></a>Próximas etapas

Os trechos de exemplo acima permitem que você crie facilmente a infraestrutura de serviço para entregar notificações por push para uma ampla variedade de dispositivos. Agora que você aprendeu as Noções básicas do uso Hubs de notificação com Node, siga estes links para saber mais sobre como você pode estender esses recursos ainda mais.

-   Consulte a referência MSDN para [Hubs de notificação Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx).
-   Visite o repositório de [SDK do Azure para nó] no GitHub por mais exemplos e detalhes de implementação.

  [SDK do Azure para nó]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Next Steps]: #nextsteps
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #Create_a_Nodejs_Application
  [Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: Create a Topic]: #How_to_Create_a_Topic
  [How to: Create Subscriptions]: #How_to_Create_Subscriptions
  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Azure Classic Portal]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Site da Web com WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
  [Portal do Azure]: https://portal.azure.com
