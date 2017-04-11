<properties
    pageTitle="IOS de contrato Mobile Azure SDK atinja integração | Microsoft Azure"
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

#<a name="how-to-integrate-engagement-reach-on-ios"></a>Como integrar o contrato alcançar IOS

Você deve seguir o procedimento de integração descrito em [como integrar o contrato no iOS documento](mobile-engagement-ios-integrate-engagement.md) antes de seguir este guia.

Esta documentação requer XCode 8. Se você realmente depender XCode 7 você pode usar o [iOS v3.2.4 SDK do contrato](https://aka.ms/r6oouh). Há um bug conhecido nesta versão anterior enquanto executa em dispositivos iOS 10: notificações do sistema não são actioned. Para corrigir isso, você terá que implementar a API preterida `application:didReceiveRemoteNotification:` em seu aplicativo de representante da seguinte maneira:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **Não recomendamos essa solução alternativa** como esse comportamento pode alterar em qualquer atualização da versão futuras iOS (mesmo menor) porque este iOS API está obsoleta. Você deve mudar XCode 8 assim que possível.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Habilitar o seu aplicativo receber notificações por Push silencioso

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

##<a name="integration-steps"></a>Etapas de integração

### <a name="embed-the-engagement-reach-sdk-into-your-ios-project"></a>Incorporar o SDK do contrato atinja seu projeto do iOS

-   Adicione o sdk alcance em seu projeto Xcode. No Xcode, acesse **projeto \> adicionar ao projeto** e escolha o `EngagementReach` pasta.

### <a name="modify-your-application-delegate"></a>Modificar seu representante de aplicativo

-   Na parte superior do seu arquivo de implementação, importe o módulo do contrato atinja:

        [...]
        #import "AEReachModule.h"

-   Dentro do método `applicationDidFinishLaunching:` ou `application:didFinishLaunchingWithOptions:`, crie um módulo de alcance e passe para sua linha de inicialização do contrato existente:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
          AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
          [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
          [...]

          return YES;
        }

-   Modificar a cadeia de caracteres **'icon.png'** com o nome de imagem desejado como o ícone de notificação.
-   Se você quiser usar a opção *valor de selo de atualização* em campanhas de alcance ou se você quiser usar push nativo \<SaaS/alcance API/campanha formato/nativo Push\> campanhas, você deve deixar o alcance módulo gerenciar o selo próprio ícone (ele será automaticamente limpar o selo de aplicativo e também redefinir o valor armazenado por contrato sempre que o aplicativo é iniciado ou foregrounded). Isso é feito adicionando a seguinte linha após a inicialização do módulo alcance:

        [reach setAutoBadgeEnabled:YES];

-   Se você deseja lidar com o envio de dados de alcance, você deve permitir que seu representante de aplicativo estão em conformidade com a `AEReachDataPushDelegate` protocolo. Adicione a seguinte linha após a inicialização do módulo alcance:

        [reach setDataPushDelegate:self];

-   Em seguida, você pode implementar os métodos `onDataPushStringReceived:` e `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` no seu representante de aplicativo:

        -(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
        {
           NSLog(@"String data push message with category <%@> received: %@", category, body);
           return YES;
        }

        -(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
        {
           NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
           // Do something useful with decodedBody like updating an image view
           return YES;
        }

### <a name="category"></a>Categoria

O parâmetro de categoria é opcional quando você criar uma campanha de envio de dados e permite que você para filtrar dados envia. Isso é útil se você quiser push tipos diferentes de `Base64` dados e deseja identificar seu tipo antes da análise-los.

**Seu aplicativo agora está pronto para receber e exibir conteúdo alcance!**

##<a name="how-to-receive-announcements-and-polls-at-any-time"></a>Como receber anúncios e votações a qualquer momento

Contrato pode enviar notificações de alcance aos usuários finais a qualquer momento, usando o serviço de notificação por Push da Apple.

Para habilitar essa funcionalidade, você terá que preparar seu aplicativo para notificações de envio do Apple e modificar seu representante de aplicativo.

### <a name="prepare-your-application-for-apple-push-notifications"></a>Preparar seu aplicativo para notificações de push da Apple

Siga o guia: [como preparar seu aplicativo Apple notificações por Push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

### <a name="add-the-necessary-client-code"></a>Adicionar o código do cliente necessário

*Neste ponto seu aplicativo deve ter um certificado de envio do Apple registrado no frontend contrato.*

Se ele não ainda tiver feito isso, você precisa registrar seu aplicativo para receber notificações por push.

* Importar o `User Notification` framework:

        #import <UserNotifications/UserNotifications.h>

* Adicione a seguinte linha quando seu aplicativo é iniciado (normalmente em `application:didFinishLaunchingWithOptions:`):

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

Em seguida, você precisa fornecer aos contrato o token de dispositivo retornado por servidores da Apple. Isso é feito no método chamado `application:didRegisterForRemoteNotificationsWithDeviceToken:` no seu representante de aplicativo:

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
        [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

Por fim, você precisa informar o SDK do contrato quando seu aplicativo recebe uma notificação remota. Para fazer isso, chame o método `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` no seu representante de aplicativo:

    - (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

> [AZURE.NOTE] O método acima é apresentado no iOS 7. Se você está destinando iOS < 7, verifique se implementar o método `application:didReceiveRemoteNotification:` no seu representante de aplicativo e chamada `applicationDidReceiveRemoteNotification` sobre o EngagementAgent passando nulo em vez da `handler` argumento:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] Por padrão, o contrato atinja controla o completionHandler. Se você deseja responder manualmente a `handler` bloquear no seu código, você pode passar nulo a `handler` argumento e controlar a conclusão bloqueiam por conta própria. Consulte o `UIBackgroundFetchResult` tipo para obter uma lista de valores possíveis.


### <a name="full-example"></a>Exemplo completo

Aqui está um exemplo completo de integração:

    #pragma mark -
    #pragma mark Application lifecycle

    - (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    {
      /* Reach module */
      AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
      [reach setAutoBadgeEnabled:YES];

      /* Engagement initialization */
      [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
      [[EngagementAgent shared] setPushDelegate:self];

      /* Views */
      [window addSubview:[tabBarController view]];
      [window makeKeyAndVisible];

      [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
      return YES;
    }

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
      [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

### <a name="if-you-have-your-own-unusernotificationcenterdelegate-implementation"></a>Se você tiver sua própria implementação de UNUserNotificationCenterDelegate

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

##<a name="how-to-customize-campaigns"></a>Como personalizar campanhas

### <a name="notifications"></a>Notificações

Há dois tipos de notificações: notificações do sistema e do aplicativo.

Notificações do sistema são tratadas pelo iOS e não podem ser personalizadas.

Notificações do aplicativo são feitas de um modo de exibição que dinamicamente é adicionado à janela atual do aplicativo. Isso se chama uma sobreposição de notificação. As sobreposições de notificação são ótimas para uma integração rápida porque eles não exigem a modificar qualquer modo de exibição em seu aplicativo.

#### <a name="layout"></a>Layout

Para modificar a aparência das suas notificações do aplicativo, você pode simplesmente modificar o arquivo `AENotificationView.xib` às suas necessidades, desde que você mantém os valores de marca e tipos das subexibições existentes.

Por padrão, as notificações do aplicativo são apresentadas na parte inferior da tela. Se você preferir para exibi-las na parte superior da tela, editar fornecida `AENotificationView.xib` e altere o `AutoSizing` propriedades do modo de exibição principal para que ele possa ser mantido na parte superior da sua visão ampla do.

#### <a name="categories"></a>Categorias

Quando você modifica o layout fornecido, você pode modificar a aparência de todas as suas notificações. Categorias permitem definir várias aparências de destino (possivelmente comportamentos) para notificações. Uma categoria pode ser especificada quando você cria uma campanha de alcance. Tenha em mente que categorias também permitem que você personalizar anúncios e pesquisas, que é descrito mais adiante neste documento.

Para registrar um manipulador de categoria para suas notificações, você precisa adicionar uma chamada depois que o módulo de alcance é inicializado.

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:@"my_category"];
    ...

`myNotifier`deve ser uma instância de um objeto que está em conformidade com o protocolo `AENotifier`.

Você pode implementar os métodos de protocolo sozinho ou você pode optar por implementar novamente a classe existente `AEDefaultNotifier` que já executa a maioria do trabalho.

Por exemplo, se você deseja redefinir o modo de exibição de notificação para uma categoria específica, você poderá seguir este exemplo:

    #import "AEDefaultNotifier.h"
    #import "AENotificationView.h"
    @interface MyNotifier : AEDefaultNotifier
    @end

    @implementation MyNotifier

    -(NSString*)nibNameForCategory:(NSString*)category
    {
      return "MyNotificationView";
    }

    @end

Esse exemplo simples de categoria pressupõem que você tenha um arquivo denominado `MyNotificationView.xib` no pacote de aplicativo principal. Se o método não conseguir encontrar um correspondente `.xib`, a notificação não será exibida e contrato resultará em uma mensagem no console.

O arquivo de ponta fornecido deve respeita as seguintes regras:

-   Ele deve conter apenas um modo de exibição.
-   Sub-visualizações devem ser os mesmos tipos como aquelas dentro do arquivo de ponta fornecido nomeadas`AENotificationView.xib`
-   Sub-visualizações devem ter as mesmas marcas como aquelas dentro fornecida arquivo de ponta chamado`AENotificationView.xib`

> [AZURE.TIP] Basta copiar o arquivo de ponta fornecido, chamado `AENotificationView.xib`e começar a trabalhar a partir daí. Mas tenha cuidado, o modo de exibição dentro desse arquivo de ponta está associado à classe `AENotificationView`. Essa classe redefinido o método `layoutSubViews` mover e redimensionar suas sub-visualizações de acordo com o contexto. Talvez você queira substituí-la com um `UIView` ou classe do modo de exibição personalizado.

Se você precisar de mais profunda personalização das suas notificações (se você quiser por exemplo carregar seu modo de exibição diretamente do código), é recomendável dar uma olhada a documentação de código e classe de fonte fornecido de `Protocol ReferencesDefaultNotifier` e `AENotifier`.

Observe que você pode usar a mesma notificação para várias categorias.

Você também pode redefinido a notificação padrão como esta:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### <a name="notification-handling"></a>Manipulação de notificação

Ao usar a categoria padrão, alguns métodos de ciclo de vida são chamados na `AEReachContent` objeto para relatar estatísticas e atualizar o estado de campanha:

-   Quando a notificação é exibida no aplicativo, o `displayNotification` método é chamado (que relata estatísticas) por `AEReachModule` se `handleNotification:` retorna `YES`.
-   Se a notificação é descartada, o `exitNotification` método é chamado, estatística é informada e campanhas próxima agora podem ser processadas.
-   Se a notificação é clicada, `actionNotification` é chamado, estatística é informada e a ação associada é executada.

Se sua implementação de `AENotifier` ignora o comportamento padrão, você precisa chamar esses métodos de ciclo de vida sozinho. Os exemplos a seguir ilustram alguns casos onde o comportamento padrão é ignorado:

-   Você não estender `AEDefaultNotifier`, por exemplo, você implementado manipulação de categoria do zero.
-   Você substitui `prepareNotificationView:forContent:`, certifique-se de mapear pelo menos `onNotificationActioned` ou `onNotificationExited` para um dos seus U.I controles.

> [AZURE.WARNING] Se `handleNotification:` gera uma exceção, o conteúdo é excluída e `drop` é chamado, isso é relatado em estatísticas e campanhas próxima agora podem ser processadas.

#### <a name="include-notification-as-part-of-an-existing-view"></a>Incluir notificação como parte de uma exibição existente

Sobreposições são ótimas para uma integração rápida, mas podem ser às vezes não convenientes ou podem ter efeitos indesejáveis do lado.

Se você não estiver satisfeito com o sistema de sobreposição em alguns dos modos de exibição do, você pode personalizá-lo para esses modos de exibição.

Você pode optar por incluir nosso layout de notificação em modos de exibição do existentes. Para fazer isso, há dois estilos de implementação:

1.  Adicionar o modo de exibição de notificação usando o construtor de interface

    -   Abrir *Construtor de Interface*
    -   Coloque um 320 x 60 (ou 60 x 768 se você estiver no iPad) `UIView` onde você deseja que a notificação seja exibido
    -   Definir o valor de marca para este modo de exibição para: **36822491**

2.  Adicione o modo de exibição de notificação programaticamente. Basta adicione o seguinte código ao seu modo de exibição foi inicializado:

        UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
        notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
        [self.view addSubview:notificationView];

`NOTIFICATION_AREA_VIEW_TAG`ação de macro pode ser encontrada na `AEDefaultNotifier.h`.

> [AZURE.NOTE] A notificação padrão detecta automaticamente que o layout de notificação é incluído neste modo de exibição e não adicionará uma sobreposição para ele.

### <a name="announcements-and-polls"></a>Anúncios e votações

#### <a name="layouts"></a>Layouts

Você pode modificar os arquivos `AEDefaultAnnouncementView.xib` e `AEDefaultPollView.xib` desde que você mantém os valores de marca e tipos das subexibições existentes.

#### <a name="categories"></a>Categorias

##### <a name="alternate-layouts"></a>Layouts alternativos

Como as notificações, categoria de campanha pode ser usada para possuem layouts alternativos para seus anúncios e votações.

Para criar uma categoria para um comunicado, você deve estender **AEAnnouncementViewController** e registre-após o módulo alcance foi inicializado:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [AZURE.NOTE] Sempre que um usuário irá clicar em uma notificação para um aviso com a categoria "meu\_categoria", seu controlador de exibição registrado (neste caso `MyCustomAnnouncementViewController`) serão inicializadas chamando o método `initWithAnnouncement:` e o modo de exibição será adicionado à janela do aplicativo atual.

Na sua implementação do `AEAnnouncementViewController` classe terá de ler a propriedade `announcement` inicializar seu sub-visualizações. Considere o exemplo a seguir, onde dois rótulos são inicializadas usando `title` e `body` propriedades do `AEReachAnnouncement` classe:

    -(void)loadView
    {
        [super loadView];

        UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        titleLabel.font = [UIFont systemFontOfSize:32.0];
        titleLabel.text = self.announcement.title;

        UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        bodyLabel.font = [UIFont systemFontOfSize:24.0];
        bodyLabel.text = self.announcement.body;

        [self.view addSubview:titleLabel];
        [self.view addSubview:bodyLabel];
    }

Se você não deseja carregar suas exibições sozinho mas deseja apenas reutilizar o layout do modo de exibição de lançamento padrão, você pode simplesmente tornar seu controlador de modo de exibição personalizado estende a classe fornecida `AEDefaultAnnouncementViewController`. Duplicar nesse caso, o arquivo de ponta `AEDefaultAnnouncementView.xib` e renomeie-a para que possam ser carregados pelo seu controlador de modo de exibição personalizado (para um controlador denominado `CustomAnnouncementViewController`, você deve ligar para o seu arquivo de ponta `CustomAnnouncementView.xib`).

Para substituir a categoria padrão de anúncios, basta registrar seu controlador de modo de exibição personalizado para a categoria definida no `kAEReachDefaultCategory`:

    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

Votações podem ser personalizadas da mesma maneira:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

Neste momento, fornecida `MyCustomPollViewController` deve estender `AEPollViewController`. Ou você pode escolher estender a partir do controlador de padrão: `AEDefaultPollViewController`.

> [AZURE.IMPORTANT] Não se esqueça de chamar tanto `action` (`submitAnswers:` para controladores de exibição personalizado de votação) ou `exit` método antes que o controlador de exibição é descartado. Caso contrário, estatísticas não serão enviadas (ou seja, sem análise na campanha) e mais importante próximas campanhas não serão notificadas até que o processo de aplicativo seja reiniciado.

##### <a name="implementation-example"></a>Exemplo de implementação

Nesta implementação o modo de exibição personalizado comunicado é carregado de um arquivo xib externos.

Como para personalização de notificação avançados, é recomendável examinar o código-fonte da implementação do padrão.

`CustomAnnouncementViewController.h`

    //Interface
    @interface CustomAnnouncementViewController : AEAnnouncementViewController {
      UILabel* titleLabel;
      UITextView* descTextView;
      UIWebView* htmlWebView;
      UIButton* okButton;
      UIButton* cancelButton;
    }

    @property (nonatomic, retain) IBOutlet UILabel* titleLabel;
    @property (nonatomic, retain) IBOutlet UITextView* descTextView;
    @property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
    @property (nonatomic, retain) IBOutlet UIButton* okButton;
    @property (nonatomic, retain) IBOutlet UIButton* cancelButton;

    -(IBAction)okButtonClicked:(id)sender;
    -(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

    //Implementation
    @implementation CustomAnnouncementViewController
    @synthesize titleLabel;
    @synthesize descTextView;
    @synthesize htmlWebView;
    @synthesize okButton;
    @synthesize cancelButton;

    -(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
    {
      self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
      if (self != nil) {
        self.announcement = anAnnouncement;
      }
      return self;
    }

    - (void) dealloc
    {
      [titleLabel release];
      [descTextView release];
      [htmlWebView release];
      [okButton release];
      [cancelButton release];
      [super dealloc];
    }

    - (void)viewDidLoad {
      [super viewDidLoad];

      /* Init announcement title */
      titleLabel.text = self.announcement.title;

      /* Init announcement body */
      if(self.announcement.type == AEAnnouncementTypeHtml)
      {
        titleLabel.hidden = YES;
        htmlWebView.hidden = NO;
        [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
      }
      else
      {
        titleLabel.hidden = NO;
        htmlWebView.hidden = YES;
        descTextView.text = self.announcement.body;
      }

      /* Set action button label */
      if([self.announcement.actionLabel length] > 0)
        [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

      /* Set exit button label */
      if([self.announcement.exitLabel length] > 0)
        [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
    }

    #pragma mark Actions

    -(IBAction)okButtonClicked:(id)sender
    {
        [self action];
    }

    -(IBAction)cancelButtonClicked:(id)sender
    {
        [self exit];
    }

    @end
