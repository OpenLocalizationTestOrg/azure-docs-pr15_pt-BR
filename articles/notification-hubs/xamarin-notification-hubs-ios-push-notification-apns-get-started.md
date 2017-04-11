<properties
    pageTitle="iOS notificações Push com Hubs de notificação para os aplicativos de Xamarin | Microsoft Azure"
    description="Neste tutorial, você saiba como usar Hubs de notificação do Azure para enviar notificações por push para um aplicativo do iOS Xamarin."
    services="notification-hubs"
    keywords="notificações, mensagens de envio por push IOS notificações por push, enviar mensagem"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="ios-push-notifications-with-notification-hubs-for-xamarin-apps"></a>iOS notificações Push com Hubs de notificação para os aplicativos de Xamarin

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Visão geral
> [AZURE.IMPORTANT] Para concluir este tutorial, você deve ter uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Este tutorial mostra como usar Hubs de notificação do Azure para enviar notificações por push para um aplicativo iOS.
Você vai criar um aplicativo de Xamarin.iOS em branco que recebe as notificações por push usando o [Serviço de notificações de envio do Apple (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html). Quando tiver terminado, você poderá usar o seu hub de notificação para transmitir notificações por push para todos os dispositivos executando o aplicativo. O código terminar está disponível no [aplicativo de NotificationHubs] [ GitHub] amostra.

Este tutorial demonstra o cenário de transmissão de mensagem de envio simples com Hubs de notificação.

##<a name="prerequisites"></a>Pré-requisitos

Este tutorial requer o seguinte:

+ [Xcode 6.0][Install Xcode]
+ Um iOS 7.0 (ou versão posterior) dispositivo compatível
+ iOS associação de programa de desenvolvedor
+ [Xamarin Studio]

   > [AZURE.NOTE] Devido a requisitos de configuração para iOS push notificações, você deve implantar e testar o aplicativo de exemplo em um dispositivo iOS física (iPhone ou iPad), em vez de no simulador.

Concluir este tutorial é um pré-requisito para todos os outros tutoriais Hubs de notificação do Xamarin iOS aplicativos.


[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]


##<a name="configure-your-notification-hub"></a>Configurar seu hub de notificação

Esta seção orienta você criando um novo hub de notificação e configurando a autenticação com APNS usando o certificado de envio **P12** que você criou. Se você quiser usar um hub de notificação que você já tiver criado, você pode pular para a etapa 5.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li>
<p>Como queremos configurar conexão APNS, no Portal do Azure, abra as configurações de notificação Hub, ande clique em <b>Serviços de notificação</b>e, em seguida, clique no item de <b>Apple (APNS)</b> na lista. Quando terminar, clique em <b>Carregar certificado</b> e selecione o certificado de <b>P12</b> que você exportou anteriormente, bem como a senha para o certificado.</p>
<p>Certifique-se de selecionar <b>a área restrita</b> , já que você está enviando mensagens de envio em um ambiente de desenvolvimento. Use a configuração de <b>produção</b> somente se você quiser enviar notificações por push para os usuários que já comprou seu aplicativo do repositório.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-apns.png)

&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-sandbox.png)


Seu hub de notificação agora está configurado para trabalhar com APNS e você tiver as cadeias de caracteres de conexão para registrar seu aplicativo e enviar notificações por push.


##<a name="connect-your-app-to-the-notification-hub"></a>Conectar seu aplicativo para o hub de notificação

#### <a name="create-a-new-project"></a>Criar um novo projeto

1. No Xamarin Studio, criar um novo projeto de iOS e selecione a **API unificada** > modelo de**Aplicativo Single View** .

    ![Xamarin Studio - tipo de selecione o aplicativo][31]

2. Adicione uma referência para o componente de mensagens do Azure. No modo de exibição de solução, clique com botão direito na pasta de **componentes** para o seu projeto e escolha **Obter mais componentes**. Pesquise o componente **Messaging do Azure** e adicione o componente ao seu projeto.

3. Em **AppDelegate.cs**, adicione o seguinte usando instrução:

        using WindowsAzure.Messaging;

4. Declare uma instância de **SBNotificationHub**:

        private SBNotificationHub Hub { get; set; }

5. Crie uma classe **Constants.cs** com as variáveis a seguir:

        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. Em **AppDelegate.cs**, atualize **FinishedLaunching()** para coincidir com o seguinte:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }

            return true;
        }

7. Substitua o método de **RegisteredForRemoteNotifications()** em **AppDelegate.cs**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

8. Substitua o método de **ReceivedRemoteNotification()** em **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. Crie o seguinte método de **ProcessNotification()** em **AppDelegate.cs**:

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }

    > [AZURE.NOTE] Você pode optar por substituir **FailedToRegisterForRemoteNotifications()** para lidar com situações como nenhuma conexão de rede. Isso é especialmente importante onde o usuário pode iniciar seu aplicativo no modo offline (por exemplo, avião) e você quer tratar push cenários específicos para seu aplicativo de mensagens.


10. Execute o aplicativo em seu dispositivo.


## <a name="sending-push-notifications"></a>Enviar notificações por Push


Você pode testar a receber notificações por push em seu aplicativo enviando notificações no [Portal do Azure] via a capacidade de **Teste enviar** no conjunto de ferramentas de **solução de problemas** , direita na página de hub de notificação, conforme mostrado na tela abaixo.

![](./media/notification-hubs-ios-get-started/notification-hubs-test-send.png)

Notificações por push normalmente são enviadas por meio de um serviço de back-end como os serviços de celular ou ASP.NET usando uma biblioteca compatível. Você também pode usar a API REST diretamente para enviar mensagens de envio se uma biblioteca não estiver disponível em seu cenário. 

Neste tutorial, abordaremos mantenha a simplicidade e apenas demonstram teste seu aplicativo cliente enviando notificações usando o SDK do .NET para hubs de notificação em um aplicativo de console em vez de um serviço de back-end. Recomendamos o tutorial de [Hubs de notificação de uso para as notificações por push aos usuários](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) como a próxima etapa para enviar notificações de um back-end do ASP.NET. No entanto, as seguintes abordagens podem ser usadas para notificações de envio:

* **Interface REST**: você pode oferecer suporte a notificação de envio em qualquer plataforma de back-end usando a [interface REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **SDK do Microsoft Azure notificação Hubs .NET**: no Nuget Package Manager para Visual Studio, executar o [Pacote de instalação Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node** : [como usar Hubs de notificação do Node](notification-hubs-nodejs-push-notification-tutorial.md).

**Aplicativos Mobile**: para obter um exemplo de como enviar notificações de um back-end do Azure aplicativos de Mobile de serviço de aplicativo integrado com Hubs de notificação, consulte [Adicionar as notificações por push para o seu aplicativo móvel](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

* **Java / PHP**: para obter um exemplo de como enviar notificações por push usando as APIs REST, consulte "Como usar Hubs de notificação do Java/PHP" ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).


####<a name="optional-send-push-notifications-from-a-net-console-app"></a>(Opcional) Enviar notificações de envio de um aplicativo de Console do .NET

Nesta seção, enviaremos notificações por push usando um aplicativo de console simple do .NET. Para os fins deste exemplo, podemos irá alternar para um ambiente de desenvolvimento do Windows com o Visual Studio já instalada.

1. No Visual Studio, crie um novo aplicativo console Visual c#:

    ![Visual Studio - criar um novo aplicativo de console][213]

2. No Visual Studio, clique em **Ferramentas**, clique em **Gerenciador de pacote do NuGet**e clique em **Package Manager Console**.

    O console do Gerenciador de pacote deverá aparecer encaixado à parte inferior do seu espaço de trabalho do Visual Studio.

3. Na janela do Console do Gerenciador de pacote, defina o **padrão de projeto** para seu novo projeto de aplicativo de console e, em seguida, na janela do console, execute o seguinte comando:

        Install-Package Microsoft.Azure.NotificationHubs

    Isso adiciona uma referência para o SDK de Hubs de notificação do Azure usando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacote Microsoft.Azure.Notification Hubs NuGet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)


4. Abrir o `Program.cs` de arquivo e adicione o seguinte `using` instrução, garantindo que podemos usar classes Azure e funções em sua classe principal:

        using Microsoft.Azure.NotificationHubs;

3. No seu `Program` classe, adicione o seguinte método (não se esqueça de substituir a **cadeia de conexão** e o **nome do hub**):

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Adicione as seguintes linhas no seu `Main` método:

         SendNotificationAsync();
         Console.ReadLine();

5. Pressione a tecla F5 para executar o aplicativo. Em segundos, você verá uma notificação de envio aparecem em seu dispositivo. Se você estiver usando uma rede de dados de celular ou Wi-Fi, certifique-se de que você tenha uma conexão de internet ativa no dispositivo.

Você pode encontrar todas as cargas possíveis na Apple [Local e guia de programação de notificação por Push].

####<a name="optional-send-notifications-from-a-mobile-service"></a>(Opcional) Enviar notificações de um serviço móvel

Nesta seção, enviaremos notificações de envio utilizando um serviço móvel por meio de um script de nó.

Para enviar uma notificação usando um serviço móvel, execute a [começar a usar os serviços de celular]e então:

1. Entre [Portal de clássico do Azure]e selecione o serviço móvel.

2. Selecione a guia **Agendador** na parte superior.

    ![Portal clássico Azure - Agendador][215]

3. Crie uma nova tarefa agendada, insira um nome e selecione **sob demanda**.

    ![Portal do Azure clássico - criar novo trabalho][216]

4. Quando o trabalho for criado, clique no nome de trabalho. Clique na guia **Script** na barra superior.

5. Insira o seguinte script dentro de sua função Agendador. Certifique-se de substituir os espaços reservados com seu nome de hub de notificação e a cadeia de conexão para *DefaultFullSharedAccessSignature* obtido anteriormente. Clique em **Salvar**.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
        notificationHubService.apns.send(
            null,
            {"aps":
                {
                "alert": "Hello from Mobile Services!"
                }
            },
            function (error)
            {
                if (!error) {
                    console.warn("Notification successful");
                }
            }
        );


6. Clique em **Executar uma vez** na barra inferior. Você deve receber um alerta em seu dispositivo.

##<a name="next-steps"></a>Próximas etapas

Neste exemplo simples, você transmitida notificações por push para todos os seus dispositivos iOS. Para usuários específicos de destino, consulte o tutorial [Hubs de notificação de uso para as notificações por push para os usuários]. Se você quiser segmento seus usuários por grupos de interesse, você pode ler [Hubs de notificação de uso para enviar as últimas notícias]. Saiba mais sobre como usar Hubs de notificação na [Orientação de Hubs de notificação] e na [Notificação Hubs instruções para iOS].


<!-- Images. -->

[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Começar a usar os serviços de celular]: /develop/mobile/tutorials/get-started-xamarin-ios
[Azure Portal clássico]: https://manage.windowsazure.com/
[Orientação de Hubs de notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Notificação Hubs instruções para iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Use Hubs de notificação para notificações de envio para os usuários]: /manage/services/notification-hubs/notify-users-aspnet
[Use Hubs de notificação para enviar últimas notícias]: /manage/services/notification-hubs/breaking-news-dotnet

[Local e guia de programação de notificação por Push]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin Studio]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
[Portal do Azure]: https://portal.azure.com
