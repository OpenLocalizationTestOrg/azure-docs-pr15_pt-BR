<properties
    pageTitle="Notificação Hubs ruptura notícias Tutorial - iOS"
    description="Saiba como usar Hubs de notificação do Azure serviço barramento enviar notificações de notícias ruptura para dispositivos iOS."
    services="notification-hubs"
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Use Hubs de notificação para enviar últimas notícias

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Visão geral

Este tópico mostra como usar Hubs de notificação do Azure para transmitir notificações de notícias recentes para um aplicativo iOS. Ao concluir, você poderá registrar para dividir categorias de notícias que você está interessado e, receber apenas as notificações por push para essas categorias. Esse cenário é um padrão comum para muitos aplicativos onde notificações têm sejam enviadas aos grupos de usuários que têm declarados previamente juros nelas, por exemplo, leitor RSS, aplicativos para ventiladores de música, etc.

Cenários de difusão são habilitados, incluindo uma ou mais _marcas_ ao criar um registro no hub de notificação. Quando as notificações são enviadas para uma marca, todos os dispositivos que foram registrados para a marca receberão a notificação. Como marcas são simplesmente cadeias de caracteres, eles não precisam ser provisionados com antecedência. Para obter mais informações sobre marcas, consulte [expressões de marca e roteamento de Hubs de notificação](notification-hubs-tags-segment-push-message.md).


##<a name="prerequisites"></a>Pré-requisitos

Este tópico se baseia o aplicativo que você criou na [Introdução com Hubs de notificação][get-started]. Antes de iniciar este tutorial, você deve já concluiu [começar com Hubs de notificação][get-started].

##<a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categoria para o aplicativo

A primeira etapa é adicionar os elementos de interface do usuário para seu storyboard existente que permitem que o usuário selecione categorias para registrar. As categorias selecionadas por um usuário são armazenadas no dispositivo. Quando o aplicativo for iniciado, um registro de dispositivo é criado no seu hub de notificação com as categorias selecionadas como marcas.

1. No seu MainStoryboard_iPhone.storyboard adicione os seguintes componentes da biblioteca do objeto:
    + Um rótulo com o texto "Breaking News",
    + Etiquetas com textos de categoria "Mundo", "Política", "Business", "Tecnologia", "Ciências", "Esportes"
    + Seis opções, um por categoria, defina cada opção **estado** para estar **desativada** por padrão.
    + Um botão rotulado "Assinar"

    Seu storyboard deve ter a seguinte aparência:

    ![][3]

2. No editor do assistente, crie tomadas para todas as opções e chamam "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"


3. Crie uma ação para o botão chamado "assinar". Seu ViewController.h deve conter o seguinte:

        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

        - (IBAction)subscribe:(id)sender;

4. Criar uma nova **Classe de toque Cocoa** chamado `Notifications`. Copie o seguinte código na seção de interface do arquivo Notifications.h:

        @property NSData* deviceToken;

        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                    completion:(void (^)(NSError* error))completion;

        - (NSSet*)retrieveCategories;

        - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;

5. Adicione a seguinte diretiva de importação para Notifications.m:

        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. Copie o seguinte código na seção implementação do arquivo Notifications.m.

        SBNotificationHub* hub;

        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

            hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                        notificationHubPath:hubName];

            return self;
        }

        - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

            [self subscribeWithCategories:categories completion:completion];
        }


        - (NSSet*)retrieveCategories {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

            if (!categories) return [[NSSet alloc] init];
            return [[NSSet alloc] initWithArray:categories];
        }


        - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
        {
           //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

            NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
                jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
        }



    Essa classe usa o armazenamento local para armazenar e recuperar as categorias de notícias que esse dispositivo será exibida. Além disso, ele contém um método para registrar para estas categorias usando um registro de [modelo](notification-hubs-templates-cross-platform-push-messages.md) .

7. No arquivo AppDelegate.h, adicione uma instrução de importação para Notifications.h e adicione uma propriedade de uma instância da classe notificações:

        #import "Notifications.h"

        @property (nonatomic) Notifications* notifications;
    

8. No método **didFinishLaunchingWithOptions** na AppDelegate.m, adicione o código para inicializar a instância de notificações no início do método.  
 
    `HUBNAME`e `HUBLISTENACCESS` (definido no hubinfo.h) já deve ter o `<hub name>` e `<connection string with listen access>` espaços reservados substituídos com seu nome de hub de notificação e a cadeia de conexão para *DefaultListenSharedAccessSignature* obtido anteriormente

        self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];

    > [AZURE.NOTE] Como as credenciais que são distribuídas com um aplicativo cliente não são geralmente seguras, você só deve distribuir a chave para escutar acesso com seu aplicativo cliente. Ouça permite que o access não pode ser modificado seu aplicativo para registrar para notificações, mas os registros existentes e as notificações não podem ser enviadas. A tecla de acesso total é usada em um serviço de back-end segura para enviar notificações e alterar os registros existentes.


9. No método **didRegisterForRemoteNotificationsWithDeviceToken** na AppDelegate.m, substitua o código no método com o seguinte código para passar o token de dispositivo para a classe de notificações. A classe de notificações executará a se registrar para notificações com as categorias. Se o usuário mudar seleções de categoria, chamamos o `subscribeWithCategories` método em resposta ao botão **inscrever-se** para atualizá-los.

    > [AZURE.NOTE] Porque o token de dispositivo atribuído pelo serviço de notificação por Push o Apple (APNS) pode escolher a qualquer momento, você deve registrar para notificações frequentemente evitar falhas de notificação. Este exemplo registra para notificação sempre que o aplicativo é iniciado. Para os aplicativos que são executados com frequência, mais de uma vez por dia, você pode provavelmente ignorar registro para preservar a largura de banda se menos de um dia passou desde o registro anterior.

        self.notifications.deviceToken = deviceToken;

        // Retrieves the categories from local storage and requests a registration for these categories
        // each time the app starts and performs a registration.

        NSSet* categories = [self.notifications retrieveCategories];
        [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];


    Observe que neste momento não deve haver nenhum outro código no método **didRegisterForRemoteNotificationsWithDeviceToken** .

10. Os seguintes métodos já devem estar presentes em AppDelegate.m de concluir a [começar com Hubs de notificação] [ get-started] tutorial.  Caso contrário, adicioná-los.

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
            (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

    Este método trata notificações recebidas quando o aplicativo está em execução, exibindo um simples **UIAlert**.

11. Em ViewController.m, adicione uma instrução de importação para AppDelegate.h e copie o seguinte código para o método gerado XCode **assinar** . Este código irá atualizar o registro de notificação para usar as novas marcas de categoria que o usuário escolheu na interface do usuário.

        ```
        #import "Notifications.h"
        ```

        NSMutableArray* categories = [[NSMutableArray alloc] init];

        if (self.WorldSwitch.isOn) [categories addObject:@"World"];
        if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
        if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
        if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
        if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
        if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
            if (!error) {
                [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

    Este método cria uma **NSMutableArray** das categorias e usa a classe de **notificações** para armazenar a lista no armazenamento local e registra as marcas correspondentes com seu hub de notificação. Quando as categorias são alteradas, o registro será recriado com as novas categorias.

12. No ViewController.m, adicione o seguinte código no método **viewDidLoad** para definir a interface do usuário com base em categorias salvas anteriormente.


        // This updates the UI on startup based on the status of previously saved categories.

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];

        if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
        if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
        if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
        if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
        if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
        if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;



O aplicativo agora pode armazenar um conjunto de categorias no armazenamento local dispositivo usado para registrar com o hub de notificação sempre que o aplicativo é iniciado.  O usuário pode alterar a seleção de categorias no tempo de execução e clique no método de **inscrever-se** para atualizar o registro do dispositivo. Em seguida, você irá atualizar o aplicativo para enviar as notificações de notícias recentes diretamente no próprio aplicativo.


##<a name="optional-sending-tagged-notifications"></a>(opcional) Enviar notificações marcadas

Se você não tiver acesso ao Visual Studio, você pode pular para a próxima seção e enviar notificações do aplicativo em questão. Você também pode enviar a notificação de modelo apropriado a partir do [Portal clássico do Azure] usando a guia depurar seu hub de notificação. 

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]


##<a name="optional-send-notifications-from-the-device"></a>(opcional) Enviar notificações do dispositivo

Normalmente notificações seriam enviadas por um serviço de back-end, mas, você pode enviar notificações de notícias de ruptura diretamente do aplicativo. Para fazer isso atualizaremos a `SendNotificationRESTAPI` método que definimos na [começar com Hubs de notificação] [ get-started] tutorial.


1. Atualização de ViewController.m a `SendNotificationRESTAPI` método como segue para que ele aceita um parâmetro para a marca de categoria e envia a notificação de PRI [modelo](notification-hubs-templates-cross-platform-push-messages.md) .

        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];

            NSString *json;

            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];

            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];

            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                    categoryTag, self.notificationMessage.text];

            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];

            [dataTask resume];
        }



2. No ViewController.m atualize a ação de **Enviar notificação** , conforme mostrado no código a seguir. Para que ele enviará as notificações usando cada marca individualmente e enviar para várias plataformas.



        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";

            NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                    @"Technology", @"Science", @"Sports", nil];

            // Lets send the message as breaking news for each category to WNS, GCM, and APNS
            // using a template.
            for(NSString* category in categories)
            {
                [self SendNotificationRESTAPI:category];
            }
        }



3. Recrie seu projeto e verifique se que você tem sem erros de compilação.


##<a name="run-the-app-and-generate-notifications"></a>Executar o aplicativo e gerar notificações

1. Pressione o botão de execução para criar o projeto e iniciar o aplicativo. Selecione algumas opções de notícias ruptura se inscrever e pressione o botão **assinar** . Você deve ver uma caixa de diálogo indicando que as notificações foram se inscreveu.

    ![][1]

    Quando você escolhe **assinar**, o aplicativo converte categorias selecionadas em marcas e solicita um novo registro de dispositivo para as marcas selecionadas do hub notificação.

2. Insira uma mensagem para ser enviado como últimas notícias e pressione o botão **Enviar notificação** . Como alternativa, execute o aplicativo de console do .NET para gerar notificações.

    ![][2]


3. Cada dispositivo assinado últimas notícias receberá as notificações de notícias de quebra que você acabou de enviar.



## <a name="next-steps"></a>Próximas etapas

Neste tutorial aprendemos como transmitir notícias por categoria. Considere concluindo um dos seguintes tutoriais que destacam outros cenários avançados de Hubs de notificação:

+ **[Use Hubs de notificação para transmitir notícias localizados]**

    Saiba como expandir o aplicativo de notícias ruptura para habilitar o envio de notificações localizadas.





<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png








<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Use Hubs de notificação para transmitir notícias localizados]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
[Azure Portal clássico]: https://manage.windowsazure.com
