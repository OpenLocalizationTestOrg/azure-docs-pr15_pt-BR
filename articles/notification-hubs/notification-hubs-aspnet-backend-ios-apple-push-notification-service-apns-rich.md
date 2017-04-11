<properties
    pageTitle="Notificação Azure Hubs Rich Push"
    description="Saiba como enviar notificações por push avançada para um aplicativo iOS do Azure. Exemplos de código escritos em objetivo-C e c#."
    documentationCenter="ios"
    services="notification-hubs"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-rich-push"></a>Notificação Azure Hubs Rich Push


##<a name="overview"></a>Visão geral

Para se envolver usuários com conteúdo de rich instantâneo, um aplicativo talvez queira push além de texto sem formatação. Essas notificações promovem interações do usuário e apresentar conteúdo como urls, sons, imagens/cupons e muito mais. Este tutorial cria no tópico [Notificar usuários](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) e mostra como enviar notificações por push que incorporam cargas (por exemplo, imagem).


Este tutorial é compatível com iOS 7 e 8.

  ![][IOS1]

Em um alto nível:

1. O back-end do aplicativo:
    - Armazena a carga rich (neste caso, imagem) no armazenamento/local do banco de dados back-end
    - Envia ID desta notificação avançada no dispositivo
2. Aplicativo no dispositivo:
    - O back-end solicitando a carga avançada com a ID que ele recebe de contatos
    - Envia notificações de usuários no dispositivo quando a recuperação de dados é concluída e mostra a carga imediatamente quando os usuários toque para saber mais


## <a name="webapi-project"></a>Projeto de WebAPI

1. No Visual Studio, abra o projeto **AppBackend** que você criou no tutorial [Notificar usuários](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .
2. Obtenha uma imagem que você gostaria de notificar os usuários com e colocá-lo em uma pasta de **img** no diretório do projeto.
3. Clique em **Mostrar todos os arquivos** no Solution Explorer e clique na pasta para **Incluir no projeto**.
4. Com a imagem selecionada, altere seu Build Action na janela Propriedades para **Recurso incorporado**.

    ![][IOS2]

5. Em **Notifications.cs**, adicione o seguinte usando instrução:

        using System.Reflection;

6. Atualize a classe de **notificações** inteira com o código a seguir. Certifique-se de que substitua os espaços reservados com suas credenciais do hub de notificação e o nome do arquivo de imagem.

        public class Notification {
            public int Id { get; set; }
            // Initial notification message to display to users
            public string Message { get; set; }
            // Type of rich payload (developer-defined)
            public string RichType { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public class Notifications {
            public static Notifications Instance = new Notifications();

            private List<Notification> notifications = new List<Notification>();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
            }

            public Notification CreateNotification(string message, string richType, string payload) {
                var notification = new Notification() {
                    Id = notifications.Count,
                    Message = message,
                    RichType = richType,
                    Payload = payload,
                    Read = false
                };

                notifications.Add(notification);

                return notification;
            }

            public Stream ReadImage(int id) {
                var assembly = Assembly.GetExecutingAssembly();
                // Placeholder: image file name (for example, logo.png).
                return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
            }
        }

    >[AZURE.NOTE]  (opcional) Consulte [como incorporar e acessar recursos usando o Visual c#](http://support.microsoft.com/kb/319292) para obter mais informações sobre como adicionar e obter recursos do projeto.

7. Em **NotificationsController.cs**, redefina **NotificationsController** com os seguintes trechos. Isso envia uma id de notificação de rich silencioso inicial ao dispositivo e permite recuperação do lado do cliente de imagem:

        // Return http response with image binary
        public HttpResponseMessage Get(int id) {
            var stream = Notifications.Instance.ReadImage(id);

            var result = new HttpResponseMessage(HttpStatusCode.OK);
            result.Content = new StreamContent(stream);
            // Switch in your image extension for "png"
            result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

            return result;
        }

        // Create rich notification and send initial silent notification (containing id) to client
        public async Task<HttpResponseMessage> Post() {
            // Replace the placeholder with image file name
            var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

            var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

            // Silent notification with content available
            var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

            // Send notification to apns
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

8. Agora podemos implantará novamente este aplicativo para um site do Azure para torná-lo acessível de todos os dispositivos. Clique com botão direito no projeto **AppBackend** e selecione **Publicar**.

9. Selecione site Azure como destino publicar. Fazer logon com sua conta do Azure e selecione um site novo ou existente e anote a propriedade **URL de destino** na guia **Conexão** . Nós será consulte esta URL como seu *ponto de extremidade de back-end* mais adiante neste tutorial. Clique em **Publicar**.

## <a name="modify-the-ios-project"></a>Modificar o projeto do iOS

Agora que você modificou seu back-end do aplicativo para enviar apenas a *identificação* de uma notificação, você irá alterar seu aplicativo iOS para lidar com essa id e recuperar a mensagem rich do seu back-end.

1. Abra seu projeto do iOS e habilitar notificações remotas indo para o destino de aplicativo principal da seção de **destinos** .

2. Clique em **recursos**, ative **Modos de plano de fundo**e marque a caixa de seleção de **Notificações remoto** .

    ![][IOS3]

3. Vá para **Main.storyboard**e verifique se que você tem um controlador de exibição (referenciado como Home controlador de exibição neste tutorial) do tutorial do [Usuário notificar](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .

4. Adicionar um **Controlador de navegação** para seu storyboard e controle e arraste a Home controlador de exibição para torná-lo de **modo de exibição de raiz** da navegação. Verifique se que o **Controlador de exibição inicial é** no Inspetor de atributos está marcada para somente o controlador de navegação.

5. Adicione um **Controlador de exibição** para storyboard e adicionar um **Modo de exibição de imagem**. Esta é a página que os usuários verão depois que eles escolher saber mais, clicando na notifiication. Seu storyboard deve ter a seguinte aparência:

    ![][IOS4]

6. Clique no **Controlador de exibição Home** storyboard e verifique se que ela tem **homeViewController** como sua **Classe personalizada** e a **ID de Storyboard** sob o Inspetor de identidade.

7. Faça o mesmo para controlador de exibição de imagem como **imageViewController**.

8. Em seguida, crie uma nova classe de controlador de exibição intitulado **imageViewController** para lidar com a interface do usuário que você acabou de criar.

9. Em **imageViewController.h**, adicione o seguinte para declarações da interface do controlador. Certifique-se de controle e arraste do modo de exibição de imagem storyboard a essas propriedades vincular os dois:

        @property (weak, nonatomic) IBOutlet UIImageView *myImage;
        @property (strong) UIImage* imagePayload;

10. Em **imageViewController.m**, adicione o seguinte no final da **viewDidload**:

        // Display the UI Image in UI Image View
        [self.myImage setImage:self.imagePayload];

11. No **AppDelegate.m**, importe o controlador de imagem que você criou:

        #import "imageViewController.h"

12. Adicione uma seção de interface com a declaração a seguir:

        @interface AppDelegate ()

        @property UIImage* imagePayload;
        @property NSDictionary* userInfo;
        @property BOOL iOS8;

        // Obtain content from backend with notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

        // Redirect to Image View Controller after notification interaction
        - (void)redirectToImageViewWithImage: (UIImage *)img;

        @end

13. No **AppDelegate**, verifique se seu aplicativo registra para notificações de silenciosas no **aplicativo: didFinishLaunchingWithOptions**:

        // Software version
        self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

        // Register for remote notifications for iOS8 and previous versions
        if (self.iOS8) {
            NSLog(@"This device is running with iOS8.");

            // Action
            UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
            richPushAction.identifier = @"richPushMore";
            richPushAction.activationMode = UIUserNotificationActivationModeForeground;
            richPushAction.authenticationRequired = NO;
            richPushAction.title = @"More";

            // Notification category
            UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
            richPushCategory.identifier = @"richPush";
            [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

            // Notification categories
            NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];


            UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                    UIUserNotificationTypeAlert |
                                                    UIUserNotificationTypeBadge
                                                                                     categories:richPushCategories];

            [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
            [[UIApplication sharedApplication] registerForRemoteNotifications];

        }
        else {
            // Previous iOS versions
            NSLog(@"This device is running with iOS7 or earlier versions.");

            [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
        }

        return YES;

14. SUBSTITUTE na seguinte implementação do **aplicativo: didRegisterForRemoteNotificationsWithDeviceToken** para tirar o storyboard UI muda para conta:

        // Access navigation controller which is at the root of window
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        // Get home view controller from stack on navigation controller
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        hvc.deviceToken = deviceToken;

15. Em seguida, adicione os seguintes métodos para **AppDelegate.m** para recuperar a imagem de seu ponto de extremidade e enviar uma notificação local quando a recuperação estiver concluída. Certifique-se de substituir o espaço reservado `{backend endpoint}` com seu ponto de extremidade de back-end:

        NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

        // Helper: retrieve notification content from backend with rich notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
            UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
            homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
            NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
            // Check if authenticated
            if (!authenticationHeader) return;

            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];

            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200) {
                    // From NSData to UIImage
                    self.imagePayload = [UIImage imageWithData:data];

                    completion(nil);
                }
                else {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(error);
                    else {
                        completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

        // Handle silent push notifications when id is sent from backend
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
            self.userInfo = userInfo;
            int richId = [[self.userInfo objectForKey:@"richId"] intValue];
            NSString* richType = [self.userInfo objectForKey:@"richType"];

            // Retrieve image data
            if ([richType isEqualToString:@"img"]) {  
                [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                    if (!error){
                        // Send local notification
                        UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                        // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                        localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                        localNotification.userInfo = self.userInfo;
                        localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                        localNotification.timeZone = [NSTimeZone defaultTimeZone];

                        // iOS8 categories
                        if (self.iOS8) {
                            localNotification.category = @"richPush";
                        }

                        [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                        handler(UIBackgroundFetchResultNewData);
                    }
                    else{
                        handler(UIBackgroundFetchResultFailed);
                    }
                }];
            }
            // Add "else if" here to handle more types of rich content such as url, sound files, etc.
        }

16. Trate a notificação local acima, abrindo o controlador de exibição de imagem em **AppDelegate.m** com os seguintes métodos:

        // Helper: redirect users to image view controller
        - (void)redirectToImageViewWithImage: (UIImage *)img {
            UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
            UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main"
                                                                     bundle: nil];
            imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
            // Pass data/image to image view controller
            imgViewController.imagePayload = img;

            // Redirect
            [navigationController pushViewController:imgViewController animated:YES];
        }

        // Handle local notification sent above in didReceiveRemoteNotification
        - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
            if (application.applicationState == UIApplicationStateActive) {
                // Show in-app alert with an extra "more" button
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];

                [alert show];
            }
            // App becomes active from user's tap on notification
            else {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
        }

        // Handle buttons in in-app alerts and redirect with data/image
        - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
            // Handle "more" button
            if (buttonIndex == 1)
            {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
            // Add "else if" here to handle more buttons
        }

        // Handle notification setting actions in iOS8
        - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
            // Handle richPush related buttons
            if ([identifier isEqualToString:@"richPushMore"]) {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
            completionHandler();
        }

## <a name="run-the-application"></a>Executar o aplicativo

1. No XCode, execute o aplicativo em um dispositivo iOS físico (o simulator não funcionam notificações push).

2. No aplicativo iOS interface do usuário, insira um nome de usuário e senha do mesmo valor para autenticação e clique em **Log In**.

3. Clique em **Enviar por push** e você verá um alerta do aplicativo. Se você clicar em **mais**, você será levado até a imagem que você escolheu para incluir em seu back-end do aplicativo.

4. Você pode também clicar em **Enviar por push** e pressione imediatamente o botão página inicial do seu dispositivo. Em alguns momentos, você receberá uma notificação de envio. Se você toque nela ou clique em mais, você será levado para seu aplicativo e o conteúdo da imagem avançada.


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
