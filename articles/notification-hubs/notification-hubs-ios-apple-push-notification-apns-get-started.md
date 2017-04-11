<properties
    pageTitle="Enviando notificações por push para iOS com Hubs de notificação do Azure | Microsoft Azure"
    description="Neste tutorial, você aprenderá a usar Hubs de notificação do Azure para enviar notificações por push para um aplicativo iOS."
    services="notification-hubs"
    documentationCenter="ios"
    keywords="notificação, as notificações de envio por push notificações de envio do ios"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-to-ios-with-azure-notification-hubs"></a>Enviando notificações por push para iOS com Hubs de notificação do Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Visão geral

> [AZURE.NOTE] Para concluir este tutorial, você deve ter uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).

Este tutorial mostra como usar Hubs de notificação do Azure para enviar notificações por push para um aplicativo iOS. Você vai criar um aplicativo iOS em branco que recebe as notificações por push usando o [serviço de notificações de envio do Apple (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html). 

Quando tiver terminado, você poderá usar o seu hub de notificação para transmitir notificações por push para todos os dispositivos executando o aplicativo.

## <a name="before-you-begin"></a>Antes de começar

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

O código completo para este tutorial pode ser encontrado [no GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

##<a name="prerequisites"></a>Pré-requisitos

Este tutorial requer o seguinte:

+ [Serviços de celular iOS SDK versão 1.2.4]
+ Versão mais recente do [Xcode]
+ Um iOS 8 (ou versão posterior)-dispositivo compatível com
+ Membros do [Programa de desenvolvedor do Apple](https://developer.apple.com/programs/) .

   > [AZURE.NOTE] Devido a requisitos de configuração para notificações por push, você deve implantar e testar as notificações por push em um dispositivo iOS física (iPhone ou iPad) em vez do iOS Simulator.

Concluir este tutorial é um pré-requisito para todos os outros tutoriais Hubs de notificação para os aplicativos do iOS.

[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##<a name="configure-your-notification-hub-for-ios-push-notifications"></a>Configurar o seu Hub de notificação para notificações de envio do iOS

Esta seção orienta você criando um novo hub de notificação e configurando a autenticação com APNS usando o certificado de envio **P12** que você criou. Se você quiser usar um hub de notificação que você já tiver criado, você pode pular para a etapa 5.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li>
<p>Clique no botão de <b>Serviços de notificação</b> na lâmina <b>configurações</b> e selecione <b>Apple (APNS)</b>. Clique em <b>Carregar certificado</b> e selecione o arquivo <b>. p12</b> que você exportou anteriormente. Verifique se que você também especificar a senha correta.</p>
<p>Certifique-se de selecionar <b>a área restrita</b> como isso é para desenvolvimento. Use a <b>produção</b> apenas se você quiser enviar notificações por push para usuários que adquiriram o seu aplicativo do repositório.</p>
</li>
</ol>
&emsp;&emsp;![Configurar APNS no Portal do Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png)

&emsp;&emsp;![Configurar certificação APNS no Portal do Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)



Seu hub de notificação agora está configurado para trabalhar com APNS e você tiver as cadeias de caracteres de conexão para registrar seu aplicativo e enviar notificações por push.

##<a name="connect-your-ios-app-to-notification-hubs"></a>Conectar seu aplicativo iOS a Hubs de notificação

1. No Xcode, crie um novo projeto de iOS e selecione o modelo de **Aplicativo Single View** .

    ![Xcode - aplicativo único modo de exibição][8]

2. Ao definir as opções para seu novo projeto, verifique se usar o mesmo **Nome de produto** e **Identificador de organização** que você usou quando você a ID de pacote definido anteriormente no portal do desenvolvedor da Apple.

    ![Xcode - opções do project][11]

3. Em **destinos**, clique no seu nome de projeto, clique na guia **Configurações construir** expandir **Identidade de assinatura de código**e, em seguida, em **Depurar**, defina sua identidade de assinatura de código. Alternar **níveis** de **básico** para **todos**e defina **Provisionamento perfil** para o perfil de provisionamento que você criou anteriormente.

    Se você não vir o novo perfil de provisionamento que você criou no Xcode, tente atualizar os perfis para sua identidade de assinatura. Clique **Xcode** na barra de menus, clique em **Preferências**, clique na guia **conta** , clique no botão **Exibir detalhes** , clique em sua identidade de assinatura e, em seguida, clique no botão Atualizar no canto inferior direito.

    ![Xcode - perfil de provisionamento][9]

4. Baixe o [iOS serviços Mobile SDK versão 1.2.4] e descompacte o arquivo. No Xcode, seu projeto de atalho e clique na opção de **Adicionar arquivos ao** adicionar a pasta **WindowsAzureMessaging.framework** ao seu projeto Xcode. Selecione **Copiar itens se necessário**e clique em **Adicionar**.

    >[AZURE.NOTE] Os hubs de notificação SDK não suportamos bitcode em Xcode 7.  Você deve definir **Habilitar Bitcode** para **não** nas **Opções de compilação** do seu projeto.

    ![Descompacte SDK do Azure][10]

5. Adicionar um novo arquivo de cabeçalho ao seu projeto chamado `HubInfo.h`. Este arquivo manterá as constantes para seu hub de notificação.  Adicione as seguintes definições e substitua os espaços reservados de literal de cadeia de caracteres com o *nome do hub* e *DefaultListenSharedAccessSignature* que você anotou anteriormente.

        #ifndef HubInfo_h
        #define HubInfo_h
        
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
        
        #endif /* HubInfo_h */

6. Abrir sua `AppDelegate.h` arquivo adicione as seguintes diretivas de importação:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h> 
         #import "HubInfo.h"
        
7. No seu `AppDelegate.m file`, adicione o seguinte código no `didFinishLaunchingWithOptions` método com base em sua versão do iOS. Este código registra o identificador de dispositivo com APNs:

    Para iOS 8:

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    Para versões do iOS antes de 8:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];


8. No mesmo arquivo, adicione os seguintes métodos. Este código se conecta ao hub notificação usando as informações de conexão que você especificou no HubInfo.h. Em seguida, oferece o token de dispositivo para o hub de notificação para que o hub de notificação pode enviar notificações:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];

            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                    [self MessageBox:@"Registration Status" message:@"Registered"];
                }
            }];
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }


9. No mesmo arquivo, adicione o seguinte método para exibir um **UIAlert** se a notificação é recebida enquanto o aplicativo está ativo:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

10. Criar e executar o aplicativo em seu dispositivo para verificar se há sem falhas.

## <a name="send-test-push-notifications"></a>Enviar notificações de envio de teste


Você pode testar a receber notificações em seu aplicativo enviando notificações por push no [Portal do Azure] por meio da seção de **solução de problemas** na lâmina hub (use a opção *Testar enviar* ).

![Portal Azure - enviar de teste][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="optional-send-push-notifications-from-the-app"></a>(Opcional) Enviar notificações por push do aplicativo

>[AZURE.IMPORTANT] Este exemplo de enviar notificações do aplicativo cliente é fornecido para fins de aprendizagem. Como isso exigirá o `DefaultFullSharedAccessSignature` para estar presente no aplicativo do cliente, ele expõe seu hub de notificação para o risco de que um usuário pode obter acesso para enviar notificações não autorizadas aos seus clientes.

Se você deseja enviar notificações de envio de dentro de um aplicativo, esta seção fornece um exemplo de como fazer isso usando a interface REST.

1. No Xcode, abra `Main.storyboard` e adicione os seguintes componentes de interface do usuário da biblioteca do objeto para permitir que o usuário envie notificações por push no aplicativo:

    - Um rótulo sem texto do rótulo. Ele será usado para reportar erros nos enviar notificações. A propriedade de **linhas** deve ser definida como **0** para que ela será automaticamente tamanho restringido a margens esquerdas e direita e parte superior do modo de exibição.
    - Definir um campo de texto com texto de **espaço reservado** para **Digitar a mensagem de notificação**. Restringir o campo logo abaixo do rótulo conforme mostrado abaixo. Configure o controlador de exibição como representante tomada.
    - Um botão intitulado **Enviar notificação** restrito logo abaixo do campo de texto e na Central de horizontal.

    O modo de exibição deve ter a seguinte aparência:

    ![Designer de Xcode][32]


2. [Adicionar tomadas](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) para o campo de rótulo e texto conectado seu modo de exibição e atualizar seu `interface` definição para dar suporte a `UITextFieldDelegate` e `NSXMLParserDelegate`. Adicione as declarações de três propriedade mostradas abaixo para ajudar no suporte chamando a API REST e análise da resposta.

    Seu arquivo de ViewController.h deve ter a seguinte aparência:

        #import <UIKit/UIKit.h>

        @interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
        {
            NSXMLParser *xmlParser;
        }

        // Make sure these outlets are connected to your UI by ctrl+dragging
        @property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
        @property (weak, nonatomic) IBOutlet UILabel *sendResults;

        @property (copy, nonatomic) NSString *statusResult;
        @property (copy, nonatomic) NSString *currentElement;

        @end

3. Abrir `HubInfo.h` e adicione as seguintes constantes que serão usadas para enviar notificações para seu hub. Substitua a cadeia de caracteres de espaço reservado literal com sua cadeia de conexão *DefaultFullSharedAccessSignature* real.

        #define API_VERSION @"?api-version=2015-01"
        #define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"

4. Adicione o seguinte `#import` instruções para sua `ViewController.h` arquivo.

        #import <CommonCrypto/CommonHMAC.h>
        #import "HubInfo.h"

5. No `ViewController.m` adicione o seguinte código para a implementação de interface. Este código analisará a cadeia de conexão *DefaultFullSharedAccessSignature* . Conforme mencionado na [referência de API REST](http://msdn.microsoft.com/library/azure/dn495627.aspx), estes analisada informações serão usadas para gerar um token de SaS para o cabeçalho de solicitação de **autorização** .

        NSString *HubEndpoint;
        NSString *HubSasKeyName;
        NSString *HubSasKeyValue;

        -(void)ParseConnectionString
        {
            NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
            NSString *part;

            if ([parts count] != 3)
            {
                NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
                    reason:@"Invalid full shared access connection string" userInfo:nil];

                @throw parseException;
            }

            for (part in parts)
            {
                if ([part hasPrefix:@"Endpoint"])
                {
                    HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
                }
                else if ([part hasPrefix:@"SharedAccessKeyName"])
                {
                    HubSasKeyName = [part substringFromIndex:20];
                }
                else if ([part hasPrefix:@"SharedAccessKey"])
                {
                    HubSasKeyValue = [part substringFromIndex:16];
                }
            }
        }

6. Em `ViewController.m`, atualize o `viewDidLoad` método para analisar a cadeia de conexão quando o modo de exibição for carregada. Adicione os métodos de utilitário, mostrados abaixo, para a implementação de interface.  


        - (void)viewDidLoad
        {
            [super viewDidLoad];
            [self ParseConnectionString];
            [_notificationMessage setDelegate:self];
        }

        -(NSString *)CF_URLEncodedString:(NSString *)inputString
        {
           return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
                NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }





7. Em `ViewController.m`, adicione o seguinte código para a implementação de interface para gerar o token de autorização de SaS que será fornecido no cabeçalho de **autorização** , como mencionado na [Referência da API REST](http://msdn.microsoft.com/library/azure/dn495627.aspx).

        -(NSString*) generateSasToken:(NSString*)uri
        {
            NSString *targetUri;
            NSString* utf8LowercasedUri = NULL;
            NSString *signature = NULL;
            NSString *token = NULL;

            @try
            {
                // Add expiration
                uri = [uri lowercaseString];
                utf8LowercasedUri = [self CF_URLEncodedString:uri];
                targetUri = [utf8LowercasedUri lowercaseString];
                NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60;
                UInt64 expires = trunc(expiresOnDate);
                NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];

                // Get an hmac_sha1 Mac instance and initialize with the signing key
                const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
                const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
                unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
                CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
                NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
                signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];

                // Construct authorization token string
                token = [NSString stringWithFormat:@"SharedAccessSignature sig=%@&se=%qu&skn=%@&sr=%@",
                    signature, expires, HubSasKeyName, targetUri];
            }
            @catch (NSException *exception)
            {
                [self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
            }
            @finally
            {
                if (utf8LowercasedUri != NULL)
                    CFRelease((CFStringRef)utf8LowercasedUri);
                if (signature != NULL)
                CFRelease((CFStringRef)signature);
            }

            return token;
        }


8. CTRL + arrastar a **Enviar notificação** botão para `ViewController.m` para adicionar uma ação chamada **SendNotificationMessage** para o evento **De toque para baixo** . Atualização do método com o seguinte código para enviar a notificação usando a API REST.

        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";
            [self SendNotificationRESTAPI];
        }

        - (void)SendNotificationRESTAPI
        {
            NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];

            // Apple Notification format of the notification message
            NSString *json = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"%@\"}}",
                                self.notificationMessage.text];

            // Construct the message's REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                                HUBNAME, API_VERSION]];

            // Generate the token to be used in the authorization header
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the APNs notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            // Signify Apple notification format
            [request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || (httpResponse.statusCode != 200 && httpResponse.statusCode != 201))
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                    [xmlParser parse];
                }
            }];
            [dataTask resume];
        }


9. Em `ViewController.m`, adicione o seguinte método de representante para oferecer suporte a fechando o teclado para o campo de texto. CTRL + arrastar do campo de texto para o ícone de controlador de exibição no designer de interface para definir o controlador de exibição como representante tomada.

        //===[ Implement UITextFieldDelegate methods ]===

        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }


10. Em `ViewController.m`, adicione os seguintes métodos de representante para oferecer suporte a análise da resposta usando `NSXMLParser`.

        //===[ Implement NSXMLParserDelegate methods ]===

        -(void)parserDidStartDocument:(NSXMLParser *)parser
        {
            self.statusResult = @"";
        }

        -(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
            namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
            attributes:(NSDictionary *)attributeDict
        {
            NSString * element = [elementName lowercaseString];
            NSLog(@"*** New element parsed : %@ ***",element);

            if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
            {
                self.currentElement = element;
            }
        }

        -(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
        {
            self.statusResult = [self.statusResult stringByAppendingString:
                [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
        }

        -(void)parserDidEndDocument:(NSXMLParser *)parser
        {
            // Set the status label text on the UI thread
            dispatch_async(dispatch_get_main_queue(),
            ^{
                [self.sendResults setText:self.statusResult];
            });
        }



11. Criar o projeto e verifique se há sem erros.


> [AZURE.NOTE] Se você encontrar um erro de compilação no Xcode7 sobre o suporte de bitcode, você deve alterar as **Configurações de compilação** > **Bitcode habilitar (ENABLE_BITCODE)** para **não** no Xcode. O SDK do Hubs de notificação atualmente não oferece suporte para bitcode. 

Você pode encontrar todas as cargas de notificação possíveis na Apple [Local e guia de programação de notificação por Push].


##<a name="checking-if-your-app-can-receive-push-notifications"></a>Verificando se o seu aplicativo pode receber notificações por push

Para testar as notificações por push IOS, você deve implantar o aplicativo em um dispositivo iOS física. Você não consegue enviar notificações de envio do Apple usando o iOS Simulator.

1. Executar o aplicativo e verificar que o registro é bem sucedida e pressione **Okey**.

    ![iOS teste do registro de notificação por Push do aplicativo][33]

2. Você pode enviar uma notificação de envio de teste do [Portal do Azure], conforme descrito acima. Se você adicionou o código para o envio de notificações por push no aplicativo, toque no interior do campo de texto para inserir uma mensagem de notificação. Pressione o botão **Enviar** no teclado ou o botão de **Enviar notificação** no modo de exibição para enviar a mensagem de notificação.

    ![iOS aplicativo Push notificação Enviar teste][34]

3. A notificação de envio é enviada para todos os dispositivos que são registrados para receber notificações do Hub notificação específica.

    ![iOS aplicativo Push receber teste de notificação][35]


##<a name="next-steps"></a>Próximas etapas

Neste exemplo simples, você transmitida notificações por push para todos os seus dispositivos iOS registrado. Sugerimos como uma próxima etapa no seu aprendizado que prosseguir para o tutorial [Azure notificação Hubs notificar os usuários para iOS com .NET back-end] , que apresentará criando um back-end para enviar notificações por push usando marcas. 

Se você quiser segmento seus usuários por grupos de interesse, você pode mover além em para o tutorial de [Hubs de notificação de uso para enviar as últimas notícias] . 

Para obter informações gerais sobre Hubs de notificação, consulte [Diretrizes de Hubs de notificação].



<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Serviços de celular iOS SDK versão 1.2.4]: http://aka.ms/kymw2g
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Azure Classic Portal]: https://manage.windowsazure.com/
[Orientação de Hubs de notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure notificação Hubs notificar os usuários para iOS com .NET back-end]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Hubs de notificação para enviar últimas notícias]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Local e guia de programação de notificação por Push]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Portal do Azure]: https://portal.azure.com