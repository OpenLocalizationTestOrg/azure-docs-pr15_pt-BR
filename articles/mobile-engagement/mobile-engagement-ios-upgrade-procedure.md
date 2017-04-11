<properties
    pageTitle="IOS de contrato Mobile Azure SDK atualizar procedimento | Microsoft Azure"
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

#<a name="upgrade-procedures"></a>Procedimentos de atualização

Se você já tiver integrado uma versão antiga do contrato em seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Para cada nova versão do SDK você deve primeiro substituir (remover e importar novamente no xcode) as pastas EngagementSDK e EngagementReach.

##<a name="from-300-to-400"></a>De 3.0.0 para 4.0.0

### <a name="xcode-8"></a>XCode 8
8 de XCode é obrigatório iniciando da versão 4.0.0 do SDK.

> [AZURE.NOTE] Se você realmente depender XCode 7 você pode usar o [iOS v3.2.4 SDK do contrato](https://aka.ms/r6oouh). Há um bug conhecido no módulo alcance desta versão anterior enquanto executa em dispositivos iOS 10: notificações do sistema não são actioned. Para corrigir isso, você terá que implementar a API preterida `application:didReceiveRemoteNotification:` em seu aplicativo de representante da seguinte maneira:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **Não recomendamos essa solução alternativa** como esse comportamento pode alterar em qualquer atualização da versão futuras iOS (mesmo menor) porque este iOS API está obsoleta. Você deve mudar XCode 8 assim que possível.

### <a name="usernotifications-framework"></a>Estrutura de UserNotifications
Você precisa adicionar o `UserNotifications` framework em seu fases construir.

no Explorador de projeto, abra o painel de projeto e selecione o destino correto. Em seguida, abra a guia **"Criar fases"** e no menu **"Link binário com bibliotecas"** , adicione framework `UserNotifications.framework` -defina o link como`Optional`

### <a name="application-push-capability"></a>Capacidade de envio do aplicativo
8 XCode pode redefinir seu aplicativo push capacidade, duplo Verifique no `capability` guia de destino selecionado.

### <a name="add-the-new-ios-10-notification-registration-code"></a>Adicionar o novo código de registro de notificação do iOS 10
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

### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>Se você já tiver sua própria implementação de UNUserNotificationCenterDelegate

O SDK também tem sua própria implementação do protocolo UNUserNotificationCenterDelegate. Ele é usado pelo SDK para monitorar o ciclo de vida de notificações de contrato em dispositivos executando o IOS 10 ou superior. Se o SDK detectar seu representante ele não usará sua própria implementação como pode haver apenas um representante de UNUserNotificationCenter por aplicativo. Isso significa que você precisa adicionar a lógica de contrato para seu próprio representante.

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

##<a name="from-200-to-300"></a>De 2.0.0 para 3.0.0
Descartados suporte para iOS 4. x. Iniciando a partir desta versão de destino de implantação do seu aplicativo deve ter pelo menos iOS 6.

Se você estiver usando o alcance em seu aplicativo, você deve adicionar `remote-notification` valor para o `UIBackgroundModes` matriz no seu arquivo de Info.plist para receberem notificações remotas.

O método `application:didReceiveRemoteNotification:` precisa ser substituído pelo `application:didReceiveRemoteNotification:fetchCompletionHandler:` no seu representante de aplicativo.

"AEPushDelegate.h" está obsoleta interface e você precisa remover todas as referências. Isso inclui a remoção `[[EngagementAgent shared] setPushDelegate:self]` e os métodos de representante do seu representante de aplicativo:

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

##<a name="from-1160-to-200"></a>De 1.16.0 a 2.0.0
A seguir descreve como migrar uma integração SDK do serviço Capptain oferecido pelo Capptain SAS para um aplicativo do Azure Mobile contrato da plataforma.
Se você estiver migrando de uma versão anterior, consulte o site do Capptain para migrar para o 1.16 primeiro e em seguida, aplique o procedimento a seguir.

>[AZURE.IMPORTANT] Capptain e Mobile contrato não são os mesmos serviços e o procedimento abaixo realça apenas como migrar o aplicativo cliente. Migrar o SDK no aplicativo será não migrar seus dados de servidores Capptain aos servidores do contrato de celular

### <a name="agent"></a>Agente

O método `registerApp:` foi substituído pelo novo método `init:`. Seu representante do aplicativo deverá ser atualizado adequadamente e use a cadeia de conexão:

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

Acompanhamento de SmartAd foi removido do SDK apenas tiver para remover todas as instâncias de `AETrackModule` classe

### <a name="class-name-changes"></a>Alterações de nomes de classe

Como parte do rebranding, há algumas nomes de arquivo de classe/que precisam ser alterado.

Todas as classes com "CP" prefixadas são renomeadas com o prefixo "AE".

Exemplo:

-   `CPModule.h`é renomeado para `AEModule.h`.

Todas as classes com "Capptain" prefixadas são renomeadas com o prefixo "Contrato".

Exemplos:

-   A classe `CapptainAgent` é renomeado para `EngagementAgent`.
-   A classe `CapptainTableViewController` é renomeado para `EngagementTableViewController`.
-   A classe `CapptainUtils` é renomeado para `EngagementUtils`.
-   A classe `CapptainViewController` é renomeado para `EngagementViewController`.
