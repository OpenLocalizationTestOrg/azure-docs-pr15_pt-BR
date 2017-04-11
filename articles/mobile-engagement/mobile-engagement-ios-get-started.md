<properties
    pageTitle="Introdução ao Azure Mobile contrato para iOS em objetivo C | Microsoft Azure"
    description="Saiba como usar o Azure Mobile contrato com notificações por push e análises para os aplicativos do iOS."
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="10/05/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-objective-c"></a>Introdução ao Azure Mobile contrato para aplicativos iOS em objetivo C

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile contrato para entender o uso do aplicativo e enviar notificações por push para segmentado usuários para um aplicativo iOS.
Neste tutorial, você criar um aplicativo iOS em branco que coleta de dados básico e recebe as notificações de envio usando o sistema de notificação de Push da Apple (APNS).

Este tutorial requer o seguinte:

+ XCode 8, que você pode instalar seu MAC App Store
+ o [contrato de celular iOS SDK]

Concluir este tutorial é um pré-requisito para todos os outros tutoriais de contrato de celular para os aplicativos do iOS.

> [AZURE.NOTE] Para concluir este tutorial, você deve ter uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started).

##<a id="setup-azme"></a>Configurar o contrato de Mobile para seu aplicativo iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Conectar seu aplicativo no back-end do contrato de celular

Este tutorial apresenta uma "integração básica", que é o conjunto mínimo necessário para coletar dados e enviar uma notificação de envio. A documentação de integração completa pode ser encontrada na [integração do contrato móvel iOS SDK](mobile-engagement-ios-sdk-overview.md)

Vamos criar um aplicativo básico com XCode para demonstrar a integração.

###<a name="create-a-new-ios-project"></a>Criar um novo projeto do iOS

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###<a name="connect-your-app-to-the-mobile-engagement-backend"></a>Conectar seu aplicativo no back-end do contrato de celular

1. Baixe o [contrato de celular iOS SDK].
2. Extrair o. gz arquivo para uma pasta em seu computador.
3. Clique com botão direito do projeto e selecione **Adicionar arquivos ao**.

    ![][1]

4. Navegue até a pasta onde você extraídos o SDK, selecione o `EngagementSDK` pasta e pressione **Okey**.

    ![][2]

5. Abra a guia **Criar fases** e, em seguida, no menu de **Link binário com bibliotecas** , adicione as estruturas conforme mostrado abaixo:

    ![][3]

6. Volte para o portal do Azure na página de **Informações de Conexão** de seu aplicativo e copie a cadeia de conexão.

    ![][4]

7. Adicione a seguinte linha de código no seu arquivo de **AppDelegate.m** .

        #import "EngagementAgent.h"

8. Colar a cadeia de conexão no `didFinishLaunchingWithOptions` representante.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [...]   
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
            [...]
        }

9. `setTestLogEnabled`é uma instrução opcional que permite que os logs SDK para identificar problemas. 

##<a id="monitor"></a>Habilitar o monitoramento em tempo real

Para iniciar o envio de dados e garantindo que os usuários estão ativos, você deve enviar pelo menos uma tela (atividade) no back-end do contrato de celular.

1. Abra o arquivo **ViewController.h** e importar **EngagementViewController.h**:

    `# import "EngagementViewController.h"`

2. Agora substituir super classe da interface de **ViewController** por `EngagementViewController`:

    `@interface ViewController : EngagementViewController`

##<a id="monitor"></a>Conectar-se o aplicativo com monitoramento em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Ativar notificações por push e do aplicativo de mensagens

Contrato de celular permite que você interaja com seus usuários e alcançar com notificações por push e no contexto de campanhas de mensagens do aplicativo. Este módulo é chamado alcance no portal do contrato de celular.
As seções a seguir configure seu aplicativo para recebê-los.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Habilitar o seu aplicativo receber notificações por Push silencioso

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]  

### <a name="add-the-reach-library-to-your-project"></a>Adicionar a biblioteca de alcance ao seu projeto

1. Clique com botão direito seu projeto.
2. Selecione **Adicionar arquivo**.
3. Navegue até a pasta onde você extraídos o SDK.
4. Selecione o `EngagementReach` pasta.
5. Clique em **Adicionar**.

### <a name="modify-your-application-delegate"></a>Modificar seu representante de aplicativo

1. Volta ao arquivo **AppDeletegate.m** , importe o módulo contrato atinja.

        #import "AEReachModule.h"
        #import <UserNotifications/UserNotifications.h>

2. Dentro do `application:didFinishLaunchingWithOptions` método, crie um módulo de alcance e passe para sua linha de inicialização do contrato existente:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
            AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
            [...]
            return YES;
        }

###<a name="enable-your-app-to-receive-apns-push-notifications"></a>Habilitar o seu aplicativo receber notificações de envio por Push APNS

1. Adicione a seguinte linha para o `application:didFinishLaunchingWithOptions` método:

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

2. Adicionar o `application:didRegisterForRemoteNotificationsWithDeviceToken` método da seguinte forma:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
        {
            [[EngagementAgent shared] registerDeviceToken:deviceToken];
            NSLog(@"Registered Token: %@", deviceToken);
        }

3. Adicionar o `didFailToRegisterForRemoteNotificationsWithError` método da seguinte forma:

        - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
        {
           
           NSLog(@"Failed to get token, error: %@", error);
        }

4. Adicionar o `didReceiveRemoteNotification:fetchCompletionHandler` método da seguinte forma:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
        {
            [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Contrato de celular iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png

