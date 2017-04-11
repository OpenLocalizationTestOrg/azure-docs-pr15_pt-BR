<properties
    pageTitle="Introdução ao Hubs de notificação para aplicativos de Xamarin.Android | Microsoft Azure"
    description="Neste tutorial, você saiba como usar Hubs de notificação do Azure para enviar notificações por push para um aplicativo de Xamarin Android."
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"
    documentationCenter="xamarin"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-with-xamarin-for-android"></a>Introdução com Hubs de notificação com Xamarin para Android

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Visão geral

Este tutorial mostra como usar Hubs de notificação do Azure para enviar notificações por push para um aplicativo de Xamarin.Android.
Você vai criar um aplicativo de Xamarin.Android em branco que recebe as notificações por push usando mensagens de nuvem do Google (GCM). Quando tiver terminado, você poderá usar o seu hub de notificação para transmitir notificações por push para todos os dispositivos executando o aplicativo. O código terminar está disponível no [aplicativo de NotificationHubs] [ GitHub] amostra.

Este tutorial demonstra o cenário de difusão simples usando Hubs de notificação.


## <a name="before-you-begin"></a>Antes de começar

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

O código concluído para este tutorial pode ser encontrado no GitHub [aqui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid).



##<a name="prerequisites"></a>Pré-requisitos

Este tutorial requer o seguinte:

+ Visual Studio com Xamarin no Windows ou Xamarin Studio no Mac OS X. completa instruções de instalação estão na [instalação e instalar para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
+ Conta do Google ativa
+ [Componente de mensagens do Azure]
+ [Componente do cliente de mensagens de nuvem do Google]

Concluir este tutorial é um pré-requisito para todos os outros tutoriais Hubs de notificação para os aplicativos de Xamarin.Android.

> [AZURE.IMPORTANT] Para concluir este tutorial, você deve ter uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).

##<a name="enable-google-cloud-messaging"></a>Habilitar o Google Cloud de mensagens

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a name="configure-your-notification-hub"></a>Configurar seu hub de notificação

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li><p>Clique na guia <b>Configurar</b> na parte superior, insira o valor de <b>Chave API</b> obtida na seção anterior e, em seguida, clique em <b>Salvar</b>.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)


Seu hub de notificação agora está configurado para trabalhar com GCM e você tiver as cadeias de caracteres de conexão para ambos registrar seu aplicativo para receber notificações e enviar notificações por push.

##<a name="connect-your-app-to-the-notification-hub"></a>Conectar seu aplicativo para o hub de notificação

###<a name="create-a-new-project"></a>Criar um novo projeto

1. No Xamarin Studio, clique em **Nova solução**, clique em **Aplicativo Android**e clique em **Avançar**.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png)

2. Insira seu **Nome do aplicativo** e o **identificador**. Clique em **Plaforms de destino** que você deseja suporte e, em seguida, clique em **Avançar** e **criar**.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png)


    Isso cria um novo projeto Android.

2. Abra as propriedades do projeto clicando seu novo projeto no modo de exibição de solução e escolhendo **Opções**. Selecione o item de **Aplicativo Android** na seção **criar** .

    Certifique-se de que a primeira letra de seu **nome do pacote** é minúsculas.

    > [AZURE.IMPORTANT] A primeira letra do nome do pacote deve ser minúscula. Caso contrário, você receberá erros manifesto do aplicativo quando você registrar seu **BroadcastReceiver** e **IntentFilter** para notificações por push abaixo.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub--xamarin-android-app-options.png)


3. Opcionalmente, defina a **versão mínima Android** para outro nível de API.

4. Opcionalmente, defina a **versão de destino Android** para a outra versão da API que você deseja destinar (deve ser API nível 8 ou superior).

Clique em **Okey** e feche a caixa de diálogo Opções do Project.


###<a name="add-the-required-components-to-your-project"></a>Adicionar os componentes necessários ao seu projeto

O Google Cloud mensagens cliente disponível no repositório de componente Xamarin simplifica o processo de suporte para notificações por push Xamarin.Android.

1. A pasta de componentes no aplicativo de Xamarin.Android de atalho e escolha **Obter mais componentes**.

2. Pesquise o componente **Messaging do Azure** e adicioná-lo ao projeto.

3. Pesquise o componente do **Cliente de mensagens de nuvem do Google** e adicioná-lo ao projeto.


###<a name="set-up-notification-hubs-in-your-project"></a>Configurar hubs de notificação no seu projeto

1. Reúna as seguintes informações para o seu hub de aplicativo e notificação Android:

    - **GoogleProjectNumber**: obter esse valor de número do projeto da visão geral do seu aplicativo no Portal do desenvolvedor do Google. Você fez uma anotação desse valor anteriormente quando você criou o aplicativo no portal.
    - **Ouvir a cadeia de conexão**: no painel no [Portal de clássico do Azure], clique em **cadeias de caracteres de conexão de modo de exibição**. Copie a cadeia de conexão *DefaultListenSharedAccessSignature* para esse valor.
    - **Nome do hub**: Este é o nome do seu hub a partir do [Portal clássico do Azure]. Por exemplo, *mynotificationhub2*.

    Crie uma classe **Constants.cs** para o seu projeto de Xamarin e defina os seguintes valores constantes na classe. Substitua os espaços reservados com seus valores.

        public static class Constants
        {
            public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number
            public const string ListenConnectionString = "<Listen connection string>";
            public const string NotificationHubName = "<hub name>";
        }

2. Adicione o seguinte usando instruções para **MainActivity.cs**:

        using Android.Util;
        using Gcm.Client;

3. Adicionar uma variável de instância para o `MainActivity` classe que será usado para mostrar uma caixa de diálogo alerta quando o aplicativo é executado:

        public static MainActivity instance;


3. Crie o seguinte método na classe **MainActivity** :

        private void RegisterWithGCM()
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            Log.Info("MainActivity", "Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. No `OnCreate` método **MainActivity.cs**, inicializar o `instance` variável e adicionar uma chamada para `RegisterWithGCM`:

        protected override void OnCreate (Bundle bundle)
        {
            instance = this;

            base.OnCreate (bundle);

            // Set your view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get your button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            RegisterWithGCM();
        }


4. Crie uma nova classe, **MyBroadcastReceiver**.

    > [AZURE.NOTE] Vamos percorrer criando uma classe **BroadcastReceiver** do zero abaixo. No entanto, uma alternativa rápida para criar manualmente **MyBroadcastReceiver.cs** é para se referir ao arquivo **GcmService.cs** encontrado no projeto de Xamarin.Android de exemplo incluído com as [amostras de NotificationHubs][GitHub]. Duplicar **GcmService.cs** e alterando os nomes de classe podem ser um ótimo lugar para começar também.

5. Adicione o seguinte usando instruções para **MyBroadcastReceiver.cs** (consultando o componente e o conjunto que você adicionou anteriormente):

        using System.Collections.Generic;
        using System.Text;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Gcm.Client;
        using WindowsAzure.Messaging;

5. No **MyBroadcastReceiver.cs**, adicione as seguintes solicitações de permissão entre as instruções de **uso** e a declaração de **namespace** :

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is needed only for Android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. No **MyBroadcastReceiver.cs**, altere a classe **MyBroadcastReceiver** para coincidir com o seguinte:

        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

7. Adicione outra classe em **MyBroadcastReceiver.cs** chamado **PushHandlerService**, que deriva de **GcmServiceBase**. Verifique se aplicar o atributo de **serviço** à classe:

        [Service] // Must use the service tag
        public class PushHandlerService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
            private NotificationHub Hub { get; set; }

            public PushHandlerService() : base(Constants.SenderID)
            {
                Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
            }
        }


8. **GcmServiceBase** implementa métodos **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()**e **OnError()**. Nossa classe de implementação de **PushHandlerService** deve substituir esses métodos e esses métodos serão acionada em resposta a interação com o hub de notificação.


9. Substitua o método **OnRegistered()** em **PushHandlerService** usando o seguinte código:

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("PushHandlerService-GCM Registered...",
                                "The device has been Registered!");

            Hub = new NotificationHub(Constants.NotificationHubName, Constants.ListenConnectionString,
                                        context);
            try
            {
                Hub.UnregisterAll(registrationId);
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }

            //var tags = new List<string>() { "falcons" }; // create tags if you want
            var tags = new List<string>() {};

            try
            {
                var hubRegistration = Hub.Register(registrationId, tags.ToArray());
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }
        }

    > [AZURE.NOTE] No código **OnRegistered()** acima, você deve observar a capacidade de especificar marcas de formatação para registrar para canais de mensagens específicas.

10. Substitua o método **OnMessage** em **PushHandlerService** usando o seguinte código:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty (messageText))
            {
                createNotification ("New hub message!", messageText);
            }
            else
            {
                createNotification ("Unknown message details", msg.ToString ());
            }
        }

11. Adicione os seguintes métodos **createNotification** e **dialogNotify** para **PushHandlerService** para notificar os usuários quando uma notificação é recebida.

    >[AZURE.NOTE] Design de notificação no Android versão 5.0 e posterior representa um desvio significativo das versões anteriores. Se você testar no Android 5.0 ou posterior, o aplicativo precisará estar em execução para receber a notificação. Para obter mais informações, consulte [Notificações Android](http://go.microsoft.com/fwlink/?LinkId=615880).

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show UI
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto-cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over, which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
            dialogNotify (title, desc);
        }

        protected void dialogNotify(String title, String message)
        {

            MainActivity.instance.RunOnUiThread(() => {
                AlertDialog.Builder dlg = new AlertDialog.Builder(MainActivity.instance);
                AlertDialog alert = dlg.Create();
                alert.SetTitle(title);
                alert.SetButton("Ok", delegate {
                    alert.Dismiss();
                });
                alert.SetMessage(message);
                alert.Show();
            });
        }


12. Substitua membros abstratos **OnUnRegistered()**, **OnRecoverableError()**e **OnError()** para que seu código compila:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Unregistered: " + registrationId);

            createNotification("GCM Unregistered...", "The device has been unregistered!");
        }

        protected override bool OnRecoverableError(Context context, string errorId)
        {
            Log.Warn(MyBroadcastReceiver.TAG, "Recoverable Error: " + errorId);

            return base.OnRecoverableError (context, errorId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error(MyBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }


##<a name="run-your-app-in-the-emulator"></a>Executar o aplicativo no emulador

Se você executar esse aplicativo no emulador, certifique-se de que você use um Android Virtual dispositivo (AVD) que oferece suporte a APIs do Google.

> [AZURE.IMPORTANT] Para receber notificações por push, você deve configurar uma conta do Google em seu dispositivo Virtual Android. (No emulador, vá para **configurações** e clique em **Adicionar conta**.) Além disso, certifique-se de que o emulador está conectado à Internet.

>[AZURE.NOTE] Design de notificação no Android versão 5.0 e posterior representa um desvio significativo das versões anteriores. Para obter mais informações, consulte [Notificações Android](http://go.microsoft.com/fwlink/?LinkId=615880).


1. Em **Ferramentas**, clique em **Abrir o Gerenciador de emulador Android**, selecione seu dispositivo e, em seguida, clique em **Editar**.

    ![][18]

2. Selecione o **Google APIs** em **destino**e clique em **Okey**.

    ![][19]

3. Na barra superior, clique em **Executar**e, em seguida, selecione seu aplicativo. Isso inicia o emulador e executa o aplicativo.

  O aplicativo recupera o *registrationId* de GCM e registra com o hub de notificação.

##<a name="send-notifications-from-your-backend"></a>Enviar notificações do seu back-end


Você pode testar a receber notificações em seu aplicativo enviando notificações no [Portal de clássico do Azure] por meio da guia depuração no hub notificação, conforme mostrado na tela abaixo.

![][30]


Notificações por push normalmente são enviadas em um serviço de back-end como os serviços de celular ou ASP.NET por meio de uma biblioteca compatível. Você também pode usar a API REST diretamente para enviar mensagens de notificação se uma biblioteca não estiver disponível para seu back-end.

Aqui está uma lista de alguns outros tutoriais que talvez você queira examinar para enviar notificações:

- ASP.NET: Consulte [Hubs de notificação de uso para as notificações por push para os usuários].
- Azure Java de Hubs de notificação SDK: veja [como usar Hubs de notificação do Java](notification-hubs-java-push-notification-tutorial.md) para enviar notificações de Java. Isso foi testado no Eclipse para desenvolvimento Android.
- PHP: Veja [como usar Hubs de notificação do PHP](notification-hubs-php-push-notification-tutorial.md).


Em subdiretórios próximos tutorial, você enviar notificações usando um aplicativo de console do .NET e usando um serviço móvel por meio de um script de nó.

####<a name="optional-send-notifications-by-using-a-net-app"></a>(Opcional) Enviar notificações usando um aplicativo do .NET

Nesta seção, enviaremos notificações usando um aplicativo de console do .NET

1. Crie um novo aplicativo console Visual c#:

    ![][20]

2. No Visual Studio, clique em **Ferramentas**, clique em **Gerenciador de pacote do NuGet**e clique em **Package Manager Console**.

    Isso exibe o Package Manager Console no Visual Studio.

3. Na janela do Console do Gerenciador de pacote, defina o **padrão de projeto** para seu novo projeto de aplicativo de console e, em seguida, na janela do console, execute o seguinte comando:

        Install-Package Microsoft.Azure.NotificationHubs

    Isso adiciona uma referência para o SDK de Hubs de notificação do Azure usando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacote Microsoft.Azure.Notification Hubs NuGet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

4. Abra o arquivo Program.cs e adicione o seguinte `using` instrução:

        using Microsoft.Azure.NotificationHubs;

5. No seu `Program` classe, adicione o seguinte método. Atualize o texto de espaço reservado com seu nome *DefaultFullSharedAccessSignature* de hub e de cadeia de conexão a partir do [Portal clássico do Azure].

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"message\":\"Hello from Azure!\"}}");
        }

6. Adicione as seguintes linhas no seu método de **principal** :

         SendNotificationAsync();
         Console.ReadLine();

7. Pressione a tecla F5 para executar o aplicativo. Você deve receber uma notificação no aplicativo.

    ![][21]

####<a name="optional-send-notifications-by-using-a-mobile-service"></a>(Opcional) Enviar notificações usando um serviço móvel

1. Siga a [começar a usar os serviços de celular].

1. Entre [Portal de clássico do Azure]e selecione o serviço móvel.

2. Selecione a guia **Agendador** na parte superior.

    ![][22]

3. Crie uma nova tarefa agendada, insira um nome e selecione **sob demanda**.

    ![][23]

4. Quando o trabalho for criado, clique no nome de trabalho. Clique na guia **Script** na barra superior.

5. Insira o seguinte script dentro de sua função Agendador. Certifique-se de substituir os espaços reservados com seu nome de hub de notificação e a cadeia de conexão para *DefaultFullSharedAccessSignature* obtido anteriormente. Clique em **Salvar**.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
        notificationHubService.gcm.send(null,'{"data":{"message" : "Hello from Mobile Services!"}}',
          function (error)
          {
            if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
        );

6. Clique em **Executar uma vez** na barra inferior. Você deve receber uma notificação do sistema.

##<a name="next-steps"></a>Próximas etapas

Neste exemplo simples, você transmitida notificações para todos os seus dispositivos Android. Para usuários específicos de destino, consulte o tutorial [Hubs de notificação de uso para as notificações por push para os usuários]. Se você quiser segmento seus usuários por grupos de interesse, você pode ler [Hubs de notificação de uso para enviar as últimas notícias]. Saiba mais sobre como usar Hubs de notificação na [Orientação de Hubs de notificação] e na [Notificação Hubs instruções para Android].

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

[30]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Começar a usar os serviços de celular]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Azure Portal clássico]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Orientação de Hubs de notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Instruções de Hubs de notificação para Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Use Hubs de notificação para notificações de envio para os usuários]: /manage/services/notification-hubs/notify-users-aspnet
[Use Hubs de notificação para enviar últimas notícias]: /manage/services/notification-hubs/breaking-news-dotnet
[GCMClient Component page]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub GitHub page]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Componente do cliente de mensagens de nuvem do Google]: http://components.xamarin.com/view/GCMClient/
[Componente de mensagens do Azure]: http://components.xamarin.com/view/azure-messaging
