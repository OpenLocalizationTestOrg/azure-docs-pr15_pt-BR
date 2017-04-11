<properties
    pageTitle="Introdução ao Azure contrato móvel para Xamarin.iOS"
    description="Saiba como usar o Azure Mobile contrato com a análise e notificações por Push para os aplicativos de Xamarin.iOS."
    services="mobile-engagement"
    documentationCenter="xamarin"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-xamarinios-apps"></a>Introdução ao Azure contrato móvel para aplicativos de Xamarin.iOS

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile contrato para entender o uso do aplicativo e enviar notificações por push para segmentado usuários em um aplicativo de Xamarin.iOS.
Neste tutorial, você criar um aplicativo de Xamarin.iOS em branco que coleta de dados básico e recebe as notificações de envio usando o sistema de notificação de Push da Apple (APNS).

Este tutorial requer o seguinte:

+ [Xamarin Studio](http://xamarin.com/studio). Você também pode usar o Visual Studio com Xamarin, mas este tutorial usa Xamarin Studio. Para obter instruções de instalação, consulte [instalação e instalar para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 
+ [Contrato móvel Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] Para concluir este tutorial, você deve ter uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started).

##<a id="setup-azme"></a>Configurar o contrato de Mobile para seu aplicativo iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Conectar seu aplicativo no back-end do contrato de celular

Este tutorial apresenta uma "integração básica" qual é o mínimo definir necessários para coletar dados e enviar uma notificação de envio.

Vamos criar um aplicativo básico com Xamarin para demonstrar a integração:

###<a name="create-a-new-xamarinios-project"></a>Criar um novo projeto de Xamarin.iOS

1. Inicie o Xamarin Studio. Vá para **arquivo** -> **nova** -> **solução** 

    ![][1]

2. Selecione **Único aplicativo de modo de exibição**, verifique se que o idioma selecionado é **c#** e clique em **Avançar**.

    ![][2]

3. Preencha o **Nome do aplicativo** e o **Identificador de organização** e clique em **Avançar**. 

    ![][3]

    > [AZURE.IMPORTANT] Certifique-se de que o perfil de publicação que você eventualmente use para implantar o seu aplicativo iOS está usando uma ID de aplicativo que corresponde exatamente com o identificador de pacote aqui. 

4. Atualize o **Nome do projeto**, o **Nome da solução** e o **local** se necessário e clique em **criar**.

    ![][4]
 
Xamarin Studio criará o aplicativo de demonstração em que podemos serão integradas contrato de celular. 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Conectar seu aplicativo a Mobile contrato back-end

1. Clique com botão direito a pasta de **pacotes** nas janelas de solução e selecione **Adicionar pacotes …**

    ![][5]

2. Procure o **SDK do Microsoft Azure Mobile contrato Xamarin** e adicioná-lo à sua solução.  

    ![][6]
   
3. Abra **AppDelegate.cs** e adicione o seguinte usando instrução:

        using Microsoft.Azure.Engagement.Xamarin;

4. No método **FinishedLaunching** , adicione o seguinte para inicializar a conexão com o contrato de celular back-end. Certifique-se de adicionar seu **ConnectionString**. Este código também usa um fictício **NotificationIcon** que é adicionado pelo SDK do contrato Mobile que talvez você queira substituir. 

        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

##<a id="monitor"></a>Ativando o monitoramento em tempo real

Para iniciar o envio de dados e garantindo que os usuários estão ativos, você deve enviar pelo menos uma tela no back-end do contrato de celular.

1. Abra **ViewController.cs** e adicione o seguinte usando instrução:

        using Microsoft.Azure.Engagement.Xamarin;

2. Substitua a classe da qual `ViewController` herda `UIViewController` para `EngagementViewController`. 

##<a id="monitor"></a>Conectar-se o aplicativo com monitoramento em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Ativar notificações por push e do aplicativo de mensagens

Contrato de celular permite que você interaja com seus usuários e alcançar com notificações por push e no contexto de campanhas de mensagens do aplicativo. Este módulo é chamado alcance no portal do contrato de celular.
As seções a seguir configure seu aplicativo para recebê-los.

### <a name="modify-your-application-delegate"></a>Modificar seu representante de aplicativo

1. Abra o **AppDelegate.cs** e adicione o seguinte usando instrução:

        using System; 

2. Agora dentro do `FinishedLaunching` método, adicione o seguinte para registrar para mensagens de envio após`EngagementAgent.init(...)`

        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }

3. Finalmente - atualizar ou adicione os seguintes métodos:

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }

4. No seu arquivo de **Info.plist** na solução, confirme se o **Identificador de pacote** corresponde com a **ID de aplicativo** que você tem no seu perfil de provisionamento no Centro de desenvolvimento do Apple. 

    ![][7]

5. No mesmo arquivo **Info.plist** , certifique-se de que você marcou a **Habilitar modos de plano de fundo** e **Notificações remoto**. 

    ![][8]

6. Execute o aplicativo no dispositivo associado a este perfil de publicação. 

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png
