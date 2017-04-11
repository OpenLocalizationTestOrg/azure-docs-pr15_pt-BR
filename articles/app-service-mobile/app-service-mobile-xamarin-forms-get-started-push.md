<properties
    pageTitle="Adicionar notificações por push para o seu aplicativo de Xamarin.Forms | Microsoft Azure"
    description="Aprenda a usar os serviços do Azure para enviar notificações por push de várias plataformas para seus aplicativos de Xamarin.Forms."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Adicionar notificações por push para o seu aplicativo de Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Visão geral

Neste tutorial, você adicionar push notificações para todos os projetos resultaram de [Xamarin.Forms rápido iniciar](app-service-mobile-xamarin-forms-get-started.md) para que uma notificação de envio é enviada para todos os clientes de plataforma cruzada sempre que um registro é inserido.

Se você não usa o projeto de servidor de início rápido baixado, você precisará do pacote de extensão de notificação de envio. Consulte [trabalhar com o servidor de back-end do .NET SDK para aplicativos do Azure móvel](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

##<a name="prerequisites"></a>Pré-requisitos

* Para iOS, você precisará de uma [associação de programa de desenvolvedor do Apple](https://developer.apple.com/programs/ios/) e um dispositivo iOS física porque o [iOS simulator não oferece suporte para notificações por push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

##<a name="configure-hub"></a>Configurar um Hub de notificação

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a name="update-the-server-project-to-send-push-notifications"></a>Atualizar o projeto de servidor para enviar as notificações por push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##<a name="optional-configure-and-run-the-android-project"></a>(Opcional) Configurar e executar o projeto Android

Conclua esta seção para ativar as notificações por push para o projeto de Droid de Xamarin.Forms para Android.


###<a name="enable-firebase-cloud-messaging-fcm"></a>Habilitar nuvem Firebase (FCM) de mensagens

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

###<a name="configure-the-mobile-app-backend-to-send-push-requests-using-fcm"></a>Configurar o back-end do aplicativo Mobile para enviar solicitações de envio usando FCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

###<a name="add-push-notifications-to-the-android-project"></a>Adicionar notificações por push para o projeto Android

Com o back-end configurado com FCM, podemos adicionar componentes códigos ao cliente para registrar FCM, registrar para notificações de envio com Hubs de notificação do Azure por meio do aplicativo móvel back-end e receber notificações.

1. No projeto **Droid** , a pasta **componentes** de atalho, clique em **Obter mais componentes …**, pesquise o componente do **Cliente de mensagens de nuvem do Google** e adicioná-lo ao projeto. Este componente dá suporte a notificações por push para um projeto de Xamarin Android.


2. Abra o arquivo de projeto MainActivity.cs e adicione o seguinte usando instrução na parte superior do arquivo:

        using Gcm.Client;

3. Adicione o seguinte código para o método de **OnCreate** após a chamada para **LoadApplication**:

        try
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
        }
        catch (Java.Net.MalformedURLException)
        {
            CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
        }
        catch (Exception e)
        {
            CreateAndShowDialog(e.Message, "Error");
        }


4. Adicione um novo método de auxiliar **CreateAndShowDialog** , da seguinte maneira:

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage (message);
            builder.SetTitle (title);
            builder.Create().Show ();
        }


5. Adicione o seguinte código à classe **MainActivity** :

        // Create a new instance field for this activity.
        static MainActivity instance = null;

        // Return the current activity instance.
        public static MainActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }

    Isso expõe a instância atual do **MainActivity** , portanto, pode ser executado no thread da interface do usuário principal.

6. Inicializar o `instance`, variável no início do método **OnCreate** , da seguinte maneira.

        // Set the current instance of MainActivity.
        instance = this;

2. Adicionar um novo arquivo de classe ao projeto **Droid** chamado `GcmService.cs`e verifique se as seguintes instruções **using** estão presentes na parte superior do arquivo:

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V4.App;
        using Android.Util;
        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Text;


9. Adicione as solicitações de permissão a seguir na parte superior do arquivo, após as instruções **using** e antes da declaração de **namespace** .

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]

10. Adicione a seguinte definição de classe ao namespace. 

        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
        }

    >[AZURE.NOTE]Substitua **< PROJECT_NUMBER >** pelo seu número de projeto que você anotou anteriormente.   

11. Substitua a classe **GcmService** vazia com o seguinte código, que usa o receptor de difusão novato:

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }


12. Adicione o seguinte código à classe **GcmService** que substitui o manipulador de eventos **OnRegistered** e implementa um método de **registrar** .

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

            MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

        Note that this code uses the `messageParam` parameter in the template registration. 

13. Adicione o seguinte código que implementa **OnMessage**: 

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

    Isso manipula notificações recebidas e enviá-los para o gerente de notificação seja exibida.

14. **GcmServiceBase** também requer que você implementar os métodos de manipulador **OnUnRegistered** e **AoOcorrerErro** , que pode ser feito da seguinte maneira:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

Agora, você está pronto teste as notificações por push no aplicativo em execução em um dispositivo Android ou o emulador.

###<a name="test-push-notifications-in-your-android-app"></a>Teste as notificações por push em seu aplicativo Android

As duas primeiras etapas são necessárias somente durante o teste em um emulador.

1. Certifique-se de que você está implantando ou depuração em um dispositivo virtual que tenha Google APIs definida como destino, conforme mostrado abaixo no Gerenciador de dispositivo Android Virtual (AVD).

2. Adicionar uma conta do Google para o dispositivo Android clicando em **aplicativos** > **configurações** > **Adicionar conta**, em seguida, siga os prompts para usar Adicionar uma conta existente do Google para o dispositivo para criar um novo.

1. No Visual Studio ou Xamarin Studio, clique com botão direito no projeto **Droid** e clique em **Definir como projeto de inicialização**.

2. Pressione o botão **Executar** para criar o projeto e iniciar o aplicativo em seu dispositivo Android ou emulador.

3. No aplicativo, digite uma tarefa e, em seguida, clique no sinal de adição (**+**) ícone.

4. Verifique se que uma notificação é recebida quando um item é adicionado.


##<a name="optional-configure-and-run-the-ios-project"></a>(Opcional) Configurar e executar o projeto do iOS

Esta seção é para execução do projeto de iOS Xamarin para dispositivos iOS. Se não estiver trabalhando com dispositivos iOS, você pode pular esta seção.

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]


####<a name="configure-the-notification-hub-for-apns"></a>Configurar o hub de notificação para APNS

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Em seguida, você configurará a configuração do projeto iOS no Xamarin Studio ou Visual Studio.

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####<a name="add-push-notifications-to-your-ios-app"></a>Adicionar notificações por push para o seu aplicativo iOS

1. No projeto **iOS** , abra AppDelegate.cs adicionar a seguinte instrução **usando** à parte superior do arquivo de código.

        using Newtonsoft.Json.Linq;

4. Na classe **AppDelegate** , adicione uma substituição para o evento **RegisteredForRemoteNotifications** ao registrar notificações de:

        public override void RegisteredForRemoteNotifications(UIApplication application, 
            NSData deviceToken)
        {
            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. No **AppDelegate**, também adicione a seguinte substituição **DidReceivedRemoteNotification** manipulador de eventos:

        public override void DidReceiveRemoteNotification(UIApplication application, 
            NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

    Este método manipula notificações recebidas enquanto o aplicativo é executado.

2. Na classe **AppDelegate** , adicione o seguinte código para o método **FinishedLaunching** : 

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    Isso permite suporte para notificações de remotos e solicitações de envio por push registro.

Seu aplicativo agora é atualizado para oferecer suporte a notificações por push.

####<a name="test-push-notifications-in-your-ios-app"></a>Teste as notificações por push em seu aplicativo iOS

1. Clique com botão direito do projeto do iOS e clique em **Definir como projeto StartPp**.

2. Pressione o botão **Executar** ou **F5** no Visual Studio para criar o projeto e iniciar o aplicativo em um dispositivo iOS, clique em **Okey** para aceitar as notificações por push.

    > [AZURE.NOTE] Explicitamente você deve aceitar as notificações por push de seu aplicativo. Esta solicitação ocorre apenas na primeira vez que o aplicativo é executado.

3. No aplicativo, digite uma tarefa e, em seguida, clique no sinal de adição (**+**) ícone.

4. Verifique se uma notificação for recebida e clique **Okey** para descartar a notificação.


##<a name="optional-configure-and-run-the-windows-projects"></a>(Opcional) Configurar e executar os projetos do Windows

Esta seção é para executar a Xamarin.Forms WinApp e WinPhone81 projetos para dispositivos Windows. Estas etapas também oferecem suporte a projetos de plataforma de Windows Universal (UWP). Você pode pular esta seção se você não estiver trabalhando com dispositivos Windows.


####<a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrar seu aplicativo do Windows para notificações por push WNS

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####<a name="configure-the-notification-hub-for-wns"></a>Configurar o hub de notificação para WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####<a name="add-push-notifications-to-your-windows-app"></a>Adicionar notificações por push para o seu aplicativo do Windows

1. No Visual Studio, abra **App.xaml.cs** em um projeto do Windows e adicione as seguintes instruções de **uso** .

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    Substituir `<your_TodoItemManager_portable_class_namespace>` com namespace do seu projeto portátil que contém o `TodoItemManager` classe.
 

2. Em App.xaml.cs adicione o seguinte método de **InitNotificationsAsync** : 

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = 
                "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyWNS},
                {"headers", headers} // Needed for WNS.
            };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
                .RegisterAsync(channel.Uri, templates);
        }

    Este método obtém o canal de notificação por push e registra um modelo para receber notificações do modelo de seu hub de notificação. Uma notificação de modelo que suporta *messageParam* serão entregues para este cliente.

3. Em App.xaml.cs, atualizar a definição de método de manipulador de eventos **OnLaunched** adicionando o `async` modificadora, em seguida, adicione a seguinte linha de código no final do método: 

        await InitNotificationsAsync();

    Isso garante a que o registro de notificação de envio é criado ou atualizado sempre que o aplicativo é iniciado. É importante fazer isso para garantir que o canal de envio WNS sempre está ativo.  

4. Na Solution Explorer para Visual Studio, abra o arquivo **Package.appxmanifest** e defina **Capaz de sistema** para **Sim** em **notificações**.

5. Criar o aplicativo e verifique se que você tem sem erros.  Aplicativo de cliente você agora deve registrar as notificações de modelo do aplicativo Mobile back-end. Repita esta seção para cada projeto do Windows em sua solução.


####<a name="test-push-notifications-in-your-windows-app"></a>Teste as notificações por push em seu aplicativo do Windows

1. No Visual Studio, clique com botão direito um projeto do Windows e clique em **Definir como projeto de inicialização**.

2. Pressione o botão **Executar** para criar o projeto e iniciar o aplicativo.

3. No aplicativo, digite um nome para um novo todoitem e, em seguida, clique no sinal de adição (**+**) ícone para adicioná-lo.

4. Verifique se que uma notificação é recebida quando o item é adicionado.

##<a name="next-steps"></a>Próximas etapas

Saiba mais sobre as notificações por push:

* [Diagnosticar problemas de notificação de envio](../notification-hubs/notification-hubs-push-notification-fixer.md)  
Há vários motivos por notificações podem obter descartadas ou não terminam em dispositivos. Este tópico mostra como analisar e descobrir a causa de falhas de notificação de envio. 

Considere a possibilidade de continuar dentre os seguintes tutoriais:

* [Adicione autenticação ao seu aplicativo](app-service-mobile-xamarin-forms-get-started-users.md)  
Saiba como autenticar os usuários do aplicativo com um provedor de identidade.

* [Habilitar a sincronização offline para seu aplicativo](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Saiba como adicionar suporte offline seu aplicativo usando um back-end do aplicativo Mobile. Sincronização offline permite que os usuários finais interagir com um aplicativo móvel&mdash;exibir, adicionar ou modificar dados&mdash;mesmo quando não há nenhuma conexão de rede.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

