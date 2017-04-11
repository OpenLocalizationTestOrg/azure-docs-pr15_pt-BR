<properties
    pageTitle="Introdução ao Azure Mobile contrato para iOS em rápido | Microsoft Azure"
    description="Saiba como usar o Azure Mobile contrato com a análise e notificações por Push para iOS aplicativos."
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="swift"
    ms.topic="hero-article"
    ms.date="09/20/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-swift"></a>Introdução ao Azure Mobile contrato para iOS aplicativos no rápido

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile contrato para entender o uso do aplicativo e enviar notificações por push para segmentado usuários para um aplicativo iOS.
Neste tutorial, você criar um aplicativo iOS em branco que coleta de dados básico e recebe as notificações de envio usando o sistema de notificação de Push da Apple (APNS).

Este tutorial requer o seguinte:

+ XCode 8, que você pode instalar seu MAC App Store
+ o [contrato de celular iOS SDK]
+ Certificado de notificação (P12) que você pode obter notificações por push em seu centro de desenvolvimento de Apple

> [AZURE.NOTE] Este tutorial usa rápido versão 3.0. 

Concluir este tutorial é um pré-requisito para todos os outros tutoriais de contrato de celular para os aplicativos do iOS.

> [AZURE.NOTE] Para concluir este tutorial, você deve ter uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started).

##<a id="setup-azme"></a>Configurar o contrato de Mobile para seu aplicativo iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Conectar seu aplicativo no back-end do contrato de celular

Este tutorial apresenta uma "integração básica", que é o conjunto mínimo necessário para coletar dados e enviar uma notificação de envio. A documentação de integração completa pode ser encontrada na [integração do contrato móvel iOS SDK](mobile-engagement-ios-sdk-overview.md)

Vamos criar um aplicativo básico com XCode para demonstrar a integração:

###<a name="create-a-new-ios-project"></a>Criar um novo projeto do iOS

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Conectar seu aplicativo a Mobile contrato back-end

1. Baixar o [contrato de celular iOS SDK]
2. Extrair o. gz arquivo para uma pasta em seu computador
3. Clique com botão direito do projeto e selecione "Adicionar arquivos para..."

    ![][1]

4. Navegue até a pasta onde você extraídos do SDK e selecione o `EngagementSDK` pasta e pressione Okey.

    ![][2]

5. Abrir o `Build Phases` guia e no `Link Binary With Libraries` menu Adicionar as estruturas conforme mostrado abaixo:

    ![][3]

8. Criar um cabeçalho de pontes para poder usar objetivo C APIs do SDK escolhendo Arquivo > Novo > arquivo > iOS > fonte > arquivo de cabeçalho.

    ![][4]

9. Edite o arquivo de cabeçalho ponte para expor código de objetivo de contrato do Mobile-C ao seu código rápido, adicione as seguintes importações:

        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"

10. Em configurações de compilação, verifique se a compilação de cabeçalho de ponte de objetivo-C definindo em rápido compilador - geração de código tem um caminho para esse cabeçalho. Aqui está um exemplo de caminho: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (dependendo o caminho)**

    ![][6]

11. Volte para o portal do Azure na página de *Informações de Conexão* de seu aplicativo e copie a cadeia de Conexão

    ![][5]

12. Colar a cadeia de conexão no `didFinishLaunchingWithOptions` delegar

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
            [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
            [...]
        }

##<a id="monitor"></a>Ativando o monitoramento em tempo real

Para iniciar o envio de dados e garantindo que os usuários estão ativos, você deve enviar pelo menos uma tela (atividade) no back-end do contrato de celular.

1. Abra o arquivo **ViewController.swift** e substitua a classe base de **ViewController** seja **EngagementViewController**:

    `class ViewController : EngagementViewController {`

##<a id="monitor"></a>Conectar-se o aplicativo com monitoramento em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Ativar notificações por Push e do aplicativo de mensagens

Contrato de Mobile permite interagir e alcançar com seus usuários com notificações por Push e sistema de mensagens do aplicativo no contexto de campanhas. Este módulo é chamado alcance no portal do contrato de celular.
As seções a seguir irá configurar seu aplicativo para recebê-los.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Habilitar o seu aplicativo receber notificações por Push silencioso

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Adicionar a biblioteca de alcance ao seu projeto

1. Clique com botão direito seu projeto
2. Selecione`Add file to ...`
3. Navegue até a pasta onde você extraídos o SDK
4. Selecione o `EngagementReach` pasta
5. Clique em Adicionar
6. Edite o arquivo de cabeçalho ponte para expor Mobile contrato objetivo-C atinja cabeçalhos e adicione as seguintes importações:

        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### <a name="modify-your-application-delegate"></a>Modificar seu representante de aplicativo

1. Dentro do `didFinishLaunchingWithOptions` - crie um módulo de alcance e passe para sua linha de inicialização do contrato existente:

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

###<a name="enable-your-app-to-receive-apns-push-notifications"></a>Habilitar o seu aplicativo receber notificações de envio por Push APNS
1. Adicione a seguinte linha para o `didFinishLaunchingWithOptions` método:

        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }

2. Adicionar o `didRegisterForRemoteNotificationsWithDeviceToken` método da seguinte forma:

        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }

3. Adicionar o `didReceiveRemoteNotification:fetchCompletionHandler:` método da seguinte forma:

        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Contrato de celular iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png
