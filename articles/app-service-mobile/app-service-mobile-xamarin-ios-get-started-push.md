<properties
    pageTitle="Adicionar notificações por push para o seu aplicativo de Xamarin.iOS com o serviço de aplicativo do Azure"
    description="Saiba como usar o serviço de aplicativo do Azure para enviar notificações por push para seu aplicativo Xamarin.iOS"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinios-app"></a>Adicionar notificações por push ao seu App Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Visão geral

Neste tutorial, você adicionar notificações por push para o projeto [Xamarin.iOS rápido iniciar](app-service-mobile-xamarin-ios-get-started.md) para que uma notificação de envio é enviada para o dispositivo sempre que um registro é inserido.

Se você não usa o projeto de servidor de início rápido baixado, você precisará do pacote de extensão de notificação de envio. Consulte [trabalhar com o servidor de back-end do .NET SDK para aplicativos do Azure móvel](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

##<a name="prerequisites"></a>Pré-requisitos

* Conclua o tutorial de [início rápido do Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) .

* Um dispositivo iOS física. Notificações por Push não são suportadas pelo simulator iOS.

##<a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrar o aplicativo para notificações por push no portal do desenvolvedor da Apple

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

##<a name="configure-your-mobile-app-to-send-push-notifications"></a>Configurar seu aplicativo de celular para enviar notificações por push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a name="update-the-server-project-to-send-push-notifications"></a>Atualizar o projeto de servidor para enviar as notificações por push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="configure-your-xamarinios-project"></a>Configurar seu projeto de Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

##<a name="add-push-notifications-to-your-app"></a>Adicionar notificações por push para o seu aplicativo

1. No **QSTodoService**, adicione a propriedade a seguir para que **AppDelegate** pode adquirir o cliente móvel:

            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }

1. Adicione o seguinte `using` instrução na parte superior do arquivo **AppDelegate.cs** .

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

2. Em **AppDelegate**, substitua o evento **FinishedLaunching** :

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. No mesmo arquivo, substitua o evento **RegisteredForRemoteNotifications** . Nesse código estiver registrando uma notificação de modelo simples que serão enviados em todas as plataformas suportadas pelo servidor.

    Para obter mais informações sobre modelos com Hubs de notificação, consulte [modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).


        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }


4. Em seguida, substitua o evento **DidReceivedRemoteNotification** :

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

Seu aplicativo agora é atualizado para oferecer suporte a notificações por push.

## <a name="test"></a>Teste as notificações por push em seu aplicativo

1. Pressione o botão **Executar** para criar o projeto e iniciar o aplicativo em um dispositivo capaz de iOS, clique em **Okey** para aceitar as notificações por push.

    > [AZURE.NOTE] Explicitamente você deve aceitar as notificações por push de seu aplicativo. Esta solicitação ocorre apenas na primeira vez que o aplicativo é executado.

2. No aplicativo, digite uma tarefa e, em seguida, clique no sinal de adição (**+**) ícone.

3. Verifique se uma notificação for recebida e clique **Okey** para descartar a notificação.

4. Repita a etapa 2 e imediatamente feche o aplicativo e verifique se uma notificação é mostrada.

Este tutorial foi concluído com êxito.

<!-- Images. -->

<!-- URLs. -->



