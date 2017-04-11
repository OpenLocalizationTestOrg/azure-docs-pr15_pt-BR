<properties
    pageTitle="Registrar o usuário atual para notificações por push usando API Web | Microsoft Azure"
    description="Saiba como solicitar o registro de notificação de envio em um aplicativo do iOS com Hubs de notificação do Azure quando registeration é executada pela API Web do ASP.NET."
    services="notification-hubs"
    documentationCenter="ios"
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

# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Registrar o usuário atual para notificações por push usando ASP.NET

> [AZURE.SELECTOR]
- [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)



##<a name="overview"></a>Visão geral

Este tópico mostra como solicitar o registro de notificação de envio com Hubs de notificação do Azure ao registro é realizado por API Web do ASP.NET. Este tópico estende o tutorial [Notificar usuários com Hubs de notificação]. Você já deve ter concluído as etapas necessárias esse tutorial para criar o serviço móvel autenticado. Para obter mais informações sobre o cenário de usuários de notificação, consulte [Notificar usuários com Hubs de notificação].

##<a name="update-your-app"></a>Atualizar seu aplicativo  

1. No seu MainStoryboard_iPhone.storyboard, adicione os seguintes componentes da biblioteca do objeto:

    + **Rótulo**: "Enviar para o usuário com Hubs de notificação"
    + **Rótulo**: "InstallationId"
    + **Rótulo**: "Usuário"
    + **Campo de texto**: "Usuário"
    + **Rótulo**: "Senha"
    + **Campo de texto**: "Senha"
    + **Botão**: "Logon"

    Neste ponto, seu storyboard a seguinte aparência:

    ![][0]

2. No editor de assistente, criar tomadas para todos os controles comutadas e chame-los, conectar os campos de texto com o controlador de exibição (delegado) e criar uma **ação** para o botão de **logon** .

    ![][1]

    Seu arquivo de BreakingNewsViewController.h agora deve conter o seguinte código:

        @property (weak, nonatomic) IBOutlet UILabel *installationId;
        @property (weak, nonatomic) IBOutlet UITextField *User;
        @property (weak, nonatomic) IBOutlet UITextField *Password;

        - (IBAction)login:(id)sender;

5. Crie uma classe denominada **DeviceInfo**e copie o código a seguir para a seção de interface do arquivo DeviceInfo.h:

        @property (readonly, nonatomic) NSString* installationId;
        @property (nonatomic) NSData* deviceToken;

6. Copie o seguinte código na seção implementação do arquivo DeviceInfo.m:

            @synthesize installationId = _installationId;

            - (id)init {
                if (!(self = [super init]))
                    return nil;

                NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
                _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
                if(!_installationId) {
                    CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
                    _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
                    CFRelease(newUUID);

                    //store the install ID so we don't generate a new one next time
                    [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
                    [defaults synchronize];
                }

                return self;
            }

            - (NSString*)getDeviceTokenInHex {
                const unsigned *tokenBytes = [[self deviceToken] bytes];
                NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                      ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                      ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                      ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
                return hexToken;
            }

7. No PushToUserAppDelegate.h, adicione o seguinte simples de propriedade:

        @property (strong, nonatomic) DeviceInfo* deviceInfo;

8. No método **didFinishLaunchingWithOptions** na PushToUserAppDelegate.m, adicione o seguinte código:

        self.deviceInfo = [[DeviceInfo alloc] init];

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

    A primeira linha inicializa o único **DeviceInfo** . A segunda linha inicia o registro para notificações, que já está presente de envio é que você já tiver concluído o tutorial de [Introdução com Hubs de notificação] .

9. No PushToUserAppDelegate.m, implementar o método **didRegisterForRemoteNotificationsWithDeviceToken** no seu AppDelegate e adicione o seguinte código:

        self.deviceInfo.deviceToken = deviceToken;

    Define o token de dispositivo para a solicitação.

    > [AZURE.NOTE] Neste ponto, não deve haver qualquer outro código nesse método. Se você já tiver uma chamada para o método de **registerNativeWithDeviceToken** que foi adicionada quando você concluiu o tutorial de [Introdução com Hubs de notificação](/manage/services/notification-hubs/get-started-notification-hubs-ios/) , você deve comentário-out ou remover essa chamada.

10. No arquivo PushToUserAppDelegate.m, adicione o seguinte método de manipulador:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                  [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                                  @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

     Esse método exibe um alerta na interface do usuário quando seu aplicativo recebe as notificações enquanto ele é executado.

9. Abra o arquivo PushToUserViewController.m e retornar o teclado na seguinte implementação:

        - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
            if (theTextField == self.User || theTextField == self.Password) {
                [theTextField resignFirstResponder];
            }
            return YES;
        }

9. No método **viewDidLoad** no arquivo PushToUserViewController.m, inicialize o rótulo de installationId da seguinte maneira:

        DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
        Self.installationId.text = deviceInfo.installationId;

10. Adicione as seguintes propriedades na interface em PushToUserViewController.m:

        @property (readonly) NSOperationQueue* downloadQueue;
        - (NSString*)base64forData:(NSData*)theData;

11. Em seguida, adicione a seguinte implementação:

            - (NSOperationQueue *)downloadQueue {
                if (!_downloadQueue) {
                    _downloadQueue = [[NSOperationQueue alloc] init];
                    _downloadQueue.name = @"Download Queue";
                    _downloadQueue.maxConcurrentOperationCount = 1;
                }
                return _downloadQueue;
            }

            // base64 encoding
            - (NSString*)base64forData:(NSData*)theData
            {
                const uint8_t* input = (const uint8_t*)[theData bytes];
                NSInteger length = [theData length];

                static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

                NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
                uint8_t* output = (uint8_t*)data.mutableBytes;

                NSInteger i;
                for (i=0; i < length; i += 3) {
                    NSInteger value = 0;
                    NSInteger j;
                    for (j = i; j < (i + 3); j++) {
                        value <<= 8;

                        if (j < length) {
                            value |= (0xFF & input[j]);
                        }
                    }

                    NSInteger theIndex = (i / 3) * 4;
                    output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
                    output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
                    output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
                    output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
                }

                return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
            }


12. Copie o seguinte código para o método de manipulador de **login** criado pelo XCode:

            DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

            // build JSON
            NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

            // build auth string
            NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
            [request setHTTPMethod:@"POST"];
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
            [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

            // connect with POST
            [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
                // add UIAlert depending on response.
                if (error != nil) {
                    NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                    if ([httpResponse statusCode] == 200) {
                        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                        [alert show];
                    } else {
                        NSLog(@"status: %ld", (long)[httpResponse statusCode]);
                    }
                } else {
                    NSLog(@"error: %@", error);
                }
            }];

    Este método obtém uma identificação de instalação e o canal para notificações por push e enviá-la, juntamente com o tipo de dispositivo, para o método de Web API autenticado que cria um registro no Hubs de notificação. Essa API Web foi definida em [Notificar usuários com Hubs de notificação].

Agora que o aplicativo cliente foi atualizado, retorne para os [usuários de notificar com Hubs de notificação] e atualizar o serviço móvel para enviar notificações usando Hubs de notificação.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Notificar os usuários com Hubs de notificação]: /manage/services/notification-hubs/notify-users-aspnet

[Começar com Hubs de notificação]: /manage/services/notification-hubs/get-started-notification-hubs-ios
