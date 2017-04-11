<properties
    pageTitle="Notificação Azure Hubs seguros por Push"
    description="Saiba como enviar notificações por push seguro para um aplicativo iOS do Azure. Exemplos de código escritos em objetivo-C e c#."
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-secure-push"></a>Notificação Azure Hubs seguros por Push

> [AZURE.SELECTOR]
- [Universal do Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)


##<a name="overview"></a>Visão geral

Suporte de notificação de envio no Microsoft Azure permite que você acesse uma infraestrutura de envio fácil de usar várias plataformas, dimensionada-out, que simplifica a implementação de notificações de envio para aplicativos do consumidor e empresa para plataformas móveis.

Devido a regulamentares ou restrições de segurança, às vezes, um aplicativo talvez queira incluir algo na notificação que não pode ser transmitida por meio de infraestrutura de notificação do envio padrão. Este tutorial descreve como atingir a mesma experiência enviando informações confidenciais por meio de uma conexão segura, autenticada entre o dispositivo de cliente e o back-end do aplicativo.

Em um alto nível, o fluxo é da seguinte maneira:

1. O back-end do aplicativo:
    - Carga de segurança de lojas no banco de dados back-end.
    - Envia a ID desta notificação no dispositivo (nenhuma informação segura é enviada).
2. O aplicativo do dispositivo, quando receber a notificação:
    - O dispositivo back-end solicitando a carga de segurança de contatos.
    - O aplicativo pode mostrar a carga como uma notificação no dispositivo.

É importante observar que no fluxo de anterior (e neste tutorial), vamos supor que o dispositivo armazena um token de autenticação em armazenamento local, depois que o usuário faz logon. Isso garante uma experiência perfeita completamente, conforme o dispositivo pode recuperar a carga de segurança da notificação usando este token. Se seu aplicativo não armazene tokens de autenticação do dispositivo, ou se esses tokens podem ser expirados, o aplicativo de dispositivo, após receber a notificação deverá exibir uma notificação genérica solicitando ao usuário para iniciar o aplicativo. Em seguida, o aplicativo autentica o usuário e mostra a carga de notificação.

Este tutorial de envio seguro mostra como enviar uma notificação de envio com segurança. O tutorial se baseia no tutorial [Notificar usuários](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) , portanto você deve concluir as etapas desse tutorial primeiro.

> [AZURE.NOTE] Este tutorial supõe que você criou e configurado seu hub de notificação, conforme descrito na [Introdução com Hubs de notificação (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Modificar o projeto do iOS

Agora que você modificou seu aplicativo back-end para enviar apenas a *identificação* de uma notificação, você precisa alterar seu aplicativo iOS para lidar com essa notificação e seu back-end para recuperar a mensagem segura para ser exibido de retorno de chamada.

Para atingir esse objetivo, temos de escrever a lógica para recuperar o conteúdo seguro do back-end do aplicativo.

1. No **AppDelegate.m**, verifique se os registradores de aplicativo para notificações silenciosos para que ele processa a identificação de notificação enviado de back-end. Adicione a opção de **UIRemoteNotificationTypeNewsstandContentAvailability** de didFinishLaunchingWithOptions:

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];

2. No seu **AppDelegate.m** adicione uma seção de implementação na parte superior com a declaração a seguir:

        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end

3. Adicione na seção implementação código a seguir, substituindo o espaço reservado `{back-end endpoint}` com o ponto de extremidade para seu back-end obtido anteriormente:

```
        NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        {
            // check if authenticated
            ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
            NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
            if (!authenticationHeader) return;


            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                    NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                    completion([json objectForKey:@"Payload"], nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }
```

    This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

4. Agora temos trate a notificação de entrada e use o método acima para recuperar o conteúdo para exibir. Primeiro, precisamos habilitar seu aplicativo iOS seja executado em segundo plano ao receber uma notificação de envio. No **XCode**, selecione seu projeto de aplicativo no painel esquerdo, clique em seu destino de aplicativo principal na seção **destinos** do painel central.

5. Clique sua guia de **recursos** na parte superior do seu painel central e marque a caixa de seleção de **Notificações remoto** .

    ![][IOS1]


6. No **AppDelegate.m** , adicione o seguinte método para lidar com notificações por push:

        -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"%@", userInfo);

            [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
                if (!error) {
                    // show local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                    localNotification.alertBody = payload;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    completionHandler(UIBackgroundFetchResultNewData);
                } else {
                    completionHandler(UIBackgroundFetchResultFailed);
                }
            }];

        }

    Observe que é preferível para tratar de casos de propriedade de cabeçalho de autenticação ausente ou rejeição pelo back-end. A manipulação específica desses casos dependem principalmente a experiência do usuário de destino. É uma opção Exibir uma notificação com uma instrução genérica do usuário autenticar para recuperar a notificação real.

## <a name="run-the-application"></a>Executar o aplicativo

Para executar o aplicativo, faça o seguinte:

1. No XCode, execute o aplicativo em um dispositivo iOS físico (o simulator não funcionam notificações push).

2. No aplicativo iOS interface do usuário, digite um nome de usuário e senha. Eles podem ser qualquer cadeia de caracteres, mas eles devem ser o mesmo valor.

3. No aplicativo iOS interface do usuário, clique em **login**. Clique em **Enviar por push**. Você deve ver a notificação segura sejam exibida no seu centro de notificação.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
