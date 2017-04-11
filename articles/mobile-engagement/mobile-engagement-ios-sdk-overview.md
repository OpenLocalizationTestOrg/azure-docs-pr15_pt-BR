<properties
    pageTitle="Azure Mobile contrato iOS visão geral de SDK | Microsoft Azure"
    description="Últimas atualizações e procedimentos para iOS SDK para contrato de celular do Azure"
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
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="piyushjo" />

#<a name="ios-sdk-for-azure-mobile-engagement"></a>iOS SDK para contrato de celular do Azure

Comece aqui para obter todos os detalhes sobre como integrar o contrato do Azure Mobile em um aplicativo do iOS. Se você gostaria de Experimente primeiro, certifique-se que você faça nosso [tutorial de 15 minutos](mobile-engagement-ios-get-started.md).

Clique para ver o [Conteúdo do SDK](mobile-engagement-ios-sdk-content.md)

##<a name="integration-procedures"></a>Procedimentos de integração
1. Comece aqui: [como integrar contrato Mobile em seu aplicativo iOS](mobile-engagement-ios-integrate-engagement.md)

2. Para notificações: [como integrar alcance (notificações) em seu aplicativo iOS](mobile-engagement-ios-integrate-engagement-reach.md)

3. Marcar plano de implementação: [como usar o contrato de celular avançado marcação API em seu aplicativo iOS](mobile-engagement-ios-use-engagement-api.md)


##<a name="release-notes"></a>Notas de versão

### <a name="400-09122016"></a>4.0.0 (12/09/2016)

-   Notificação fixa não actioned em dispositivos iOS 10.
-   Reprovar XCode 7.

Versão anterior, consulte as [notas de versão completa](mobile-engagement-ios-release-notes.md)

##<a name="upgrade-procedures"></a>Procedimentos de atualização

Se você já tiver integrado uma versão antiga do contrato em seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Você pode precisar acompanhar vários procedimentos se você perdeu várias versões do consulte SDK os completos [Procedimentos de atualização](mobile-engagement-ios-upgrade-procedure.md).

Para cada nova versão do SDK você deve primeiro substituir (remover e importar novamente no xcode) as pastas EngagementSDK e EngagementReach.

###<a name="from-300-to-400"></a>De 3.0.0 para 4.0.0

### <a name="xcode-8"></a>XCode 8
8 de XCode é obrigatório iniciando da versão 4.0.0 do SDK.

> [AZURE.NOTE] Se você realmente depender XCode 7 você pode usar o [iOS v3.2.4 SDK do contrato](https://aka.ms/r6oouh). Há um bug conhecido no módulo alcance desta versão anterior enquanto executa em dispositivos iOS 10: notificações do sistema não são actioned. Para corrigir isso, você terá que implementar a API preterida `application:didReceiveRemoteNotification:` em seu aplicativo de representante da seguinte maneira:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **Não recomendamos essa solução alternativa** como esse comportamento pode alterar em qualquer atualização da versão futuras iOS (mesmo menor) porque este iOS API está obsoleta. Você deve mudar XCode 8 assim que possível.

#### <a name="usernotifications-framework"></a>Estrutura de UserNotifications
Você precisa adicionar o `UserNotifications` framework em seu fases construir.

no Explorador de projeto, abra o painel de projeto e selecione o destino correto. Em seguida, abra a guia **"Criar fases"** e no menu **"Link binário com bibliotecas"** , adicione framework `UserNotifications.framework` -defina o link como`Optional`

#### <a name="application-push-capability"></a>Capacidade de envio do aplicativo
8 XCode pode redefinir seu aplicativo push capacidade, duplo Verifique no `capability` guia de destino selecionado.

#### <a name="add-the-new-ios-10-notification-registration-code"></a>Adicionar o novo código de registro de notificação do iOS 10
O trecho de código mais antigo para registrar o aplicativo notificações ainda funciona, mas está usando APIs obsoletas enquanto executa o IOS 10. 

Importar o `User Notification` framework:

        #import <UserNotifications/UserNotifications.h>

No seu representante de aplicativo `application:didFinishLaunchingWithOptions` método substituir:

        if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
            [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
            [application registerForRemoteNotifications];
        }
        else {

            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

por:

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

#### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>Se você já tiver sua própria implementação de UNUserNotificationCenterDelegate

O SDK tem sua própria implementação do protocolo UNUserNotificationCenterDelegate. Ele é usado pelo SDK para monitorar o ciclo de vida de notificações de contrato em dispositivos executando o IOS 10 ou superior. Se o SDK detectar seu representante ele não usará sua própria implementação como pode haver apenas um representante de UNUserNotificationCenter por aplicativo. Isso significa que você precisa adicionar a lógica de contrato para seu próprio representante.

Há duas maneiras de fazer isso.

Encaminhando seu representante chama o SDK:

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

Ou herdando do `AEUserNotificationHandler` classe

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [AZURE.NOTE] Você pode determinar se uma notificação vem de contrato ou não passando sua `userInfo` dicionário para o agente `isEngagementPushPayload:` método de classe.