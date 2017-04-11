<properties
    pageTitle="Notificação Hubs localizados Breaking News Tutorial para iOS"
    description="Saiba como usar Hubs de notificação de barramento de serviço do Azure para enviar notificações de notícias localizados ruptura (iOS)."
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
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-localized-breaking-news-to-ios-devices"></a>Use Hubs de notificação para enviar notícias localizados para dispositivos iOS

> [AZURE.SELECTOR]
- [C# da Windows Store](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification)
- [iOS](notification-hubs-ios-xplat-localized-apns-push-notification)


##<a name="overview"></a>Visão geral

Este tópico mostra como usar o recurso de [modelos](notification-hubs-templates-cross-platform-push-messages.md) de Hubs de notificação do Azure para transmitir notificações de notícias recentes que foram localizadas por idioma e dispositivo. Neste tutorial, você começar com o aplicativo iOS criado em [Hubs de notificação de uso para enviar as últimas notícias]. Ao concluir, que você poderá registrar para categorias de que seu interesse, especifique um idioma no qual deseja receber notificações e receber apenas as notificações por push para as categorias selecionadas nesse idioma.


Há duas partes para esse cenário:

- aplicativo iOS permite que dispositivos cliente para especificar um idioma e para assinar ruptura diferentes categorias de notícias;

- o back-end transmissões as notificações, usando os recursos de **marca** e **modelo** de Hubs de notificação do Azure.



##<a name="prerequisites"></a>Pré-requisitos

Você deve já tiver concluído o tutorial de [Hubs de notificação de uso para enviar as últimas notícias] e ter o código disponível, porque este tutorial baseia-se diretamente no código.

Visual Studio 2012 ou posterior é opcional.



##<a name="template-concepts"></a>Conceitos de modelo

Em [Hubs de notificação de uso para enviar as últimas notícias] você criou um aplicativo que usadas **marcas** para assinar notificações de novas diferentes categorias.
Muitos aplicativos, no entanto, vários mercados de destino e requerem localização. Isso significa que o conteúdo das notificações de si precisa ser localizado e entregue para o conjunto correto de dispositivos.
Neste tópico mostraremos como usar o recurso de **modelo** de notificação Hubs para entregar facilmente notificações de notícias ruptura localizados.

Observação: é uma maneira de enviar notificações localizadas criar várias versões de cada marca. Por exemplo, para oferecer suporte a inglês, francês e Mandarim, temos três marcas diferentes para obter notícias do mundo: "world_en", "world_fr" e "world_ch". Podemos precisaria enviar uma versão localizada da notícia mundo para cada uma dessas marcas. Neste tópico podemos usar modelos para evitar a proliferação de marcas e o requisito de envio de várias mensagens.

Em um alto nível, os modelos são uma maneira de especificar como um dispositivo específico deve receber uma notificação. O modelo especifica o formato de carga exata fazendo referência a propriedades que fazem parte da mensagem enviada por seu aplicativo back-end. Em nosso caso, enviaremos uma mensagem de independente de localidade contendo todos os idiomas suportados:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Em seguida, podemos garantirá que dispositivos registrar com um modelo que se refere à propriedade correta. Por exemplo, um aplicativo iOS que deseja registrar News francês registrará o seguinte:

    {
        aps:{
            alert: "$(News_French)"
        }
    }

Os modelos são um poderoso recurso que você pode saber mais sobre nosso artigo de [modelos](notification-hubs-templates-cross-platform-push-messages.md) .

##<a name="the-app-user-interface"></a>A interface de usuário do aplicativo

Agora, vamos modificar o aplicativo Breaking News que você criou no tópico [Hubs de notificação de uso para enviar as últimas notícias] enviar notícias localizados usando modelos.


No seu MainStoryboard_iPhone.storyboard, adicione um controle segmentado com três idiomas que forneceremos suporte: inglês, francês e Mandarim.

![][13]

Em seguida, certifique-se de adicionar um IBOutlet no seu ViewController.h, conforme mostrado abaixo:

![][14]

##<a name="building-the-ios-app"></a>Criar o aplicativo iOS


1. No seu Notification.h adicione o método de *retrieveLocale* e modificar o armazenamento e assinar métodos conforme mostrado abaixo:

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

        - (NSSet*) retrieveCategories;

        - (int) retrieveLocale;

    No seu Notification.m, modifique o método de *storeCategoriesAndSubscribe* , adicionando o parâmetro de localidade e armazená-la nos padrões de usuário:

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];

            [self subscribeWithLocale: locale categories:categories completion:completion];
        }

    Modifique o método *inscrever-se* para incluir a localidade:

        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }

            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }

    Observe como podemos agora estão usando o método *registerTemplateWithDeviceToken*, em vez de *registerNativeWithDeviceToken*. Quando podemos registrar para um modelo que temos que fornecer o modelo de json e também um nome para o modelo (como nosso aplicativo talvez queira registrar modelos diferentes). Certifique-se registrar suas categorias como marcas, como podemos quer certificar-se receber o notifciations esses News.

    Adicione um método para recuperar a localidade das configurações padrão do usuário:

        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            int locale = [defaults integerForKey:@"BreakingNewsLocale"];

            return locale < 0?0:locale;
        }

2. Agora que estamos modificado nossa classe de notificações, temos que garantir que nossos ViewController utiliza o novo UISegmentControl. Adicione a seguinte linha no método *viewDidLoad* para certificar-se mostrar a localidade em que está selecionada no momento:

        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

    Em seguida, no seu método de *assinar* , altere sua chamada para a *storeCategoriesAndSubscribe* para o seguinte:

        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

3. Por fim, você precisa atualizar o método *didRegisterForRemoteNotificationsWithDeviceToken* em seu AppDelegate.m, para que você possa atualizar corretamente seu registro quando seu aplicativo for iniciado. Altere sua chamada para o método *assinar* notificações com o seguinte:

        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

##<a name="optional-send-localized-template-notifications-from-net-console-app"></a>(opcional) Envie notificações de modelo localizado do aplicativo de console do .NET.

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]



##<a name="optional-send-localized-template-notifications-from-the-device"></a>(opcional) Enviar notificações de modelo localizado do dispositivo

Se você não tem acesso ao Visual Studio ou deseja apenas teste enviando as notificações de modelo localizado diretamente do aplicativo no dispositivo.  Simples, você pode adicionar os parâmetros de modelo localizado para o `SendNotificationRESTAPI` método definido no tutorial anterior.

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
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

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




## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar modelos, consulte:

- [Notificar os usuários com Hubs de notificação: ASP.NET]
- [Notificar os usuários com Hubs de notificação: serviços de celular]






<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Use Hubs de notificação para enviar últimas notícias]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notificar os usuários com Hubs de notificação: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notificar os usuários com Hubs de notificação: serviços de celular]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
