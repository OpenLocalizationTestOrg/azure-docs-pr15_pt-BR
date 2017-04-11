<properties 
    pageTitle="Ponte iOS exibição da Web com iOS nativo de contrato Mobile SDK" 
    description="Descreve como criar uma ponte entre a exibição da Web executando o Javascript e o iOS nativo do contrato Mobile SDK"      
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
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>Ponte iOS exibição da Web com iOS nativo de contrato Mobile SDK

> [AZURE.SELECTOR]
- [Ponte do Android](mobile-engagement-bridge-webview-native-android.md)
- [Ponte de iOS](mobile-engagement-bridge-webview-native-ios.md)

Alguns aplicativos móveis destinam-se como um aplicativo híbrido onde o próprio aplicativo é desenvolvido usando desenvolvimento de iOS nativo objetivo-C, mas algumas ou todas as telas são renderizadas em um exibição da Web do iOS. Você ainda poderá consumir Mobile contrato iOS SDK dentro tais aplicativos e este tutorial descreve como fazer isso. 

Há duas abordagens para fazer isso, embora ambas são documentadas:

- Primeiro uma é descrita neste [link](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip) que envolve registrar um `UIWebViewDelegate` em seu modo de exibição da web e variável-e-imediatamente-cancelar uma alteração de local feita em Javascript. 
- O segundo uma baseia nesta [sessão WWDC 2013](https://developer.apple.com/videos/play/wwdc2013/615), uma abordagem que é mais limpo que o primeiro e que podemos seguirá deste guia. Observe que essa abordagem só funciona no IOS 7 e superior. 

Siga as etapas abaixo para o iOS ponte exemplo:

1. Primeiro, é necessário garantir que você passou entre nosso [tutorial de Introdução](mobile-engagement-ios-get-started.md) para integrar o iOS de contrato Mobile SDK em seu aplicativo híbrido. Opcionalmente, você também pode habilitar teste log da seguinte maneira para que você possa ver os métodos SDK como podemos dispará-los da exibição da Web. 
    
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
            [EngagementAgent setTestLogEnabled:YES];
           ....
        }

2. Agora, certifique-se de que seu aplicativo híbrido tem uma tela com uma exibição da Web nele. Você pode adicioná-lo para o `Main.storyboard` do aplicativo. 

3. Associar essa exibição da Web com seu **ViewController** clicando e arrastando a exibição da Web da cena do controlador de exibição para o `ViewController.h` Editar tela, colocando-o logo abaixo do `@interface` linha. 

4. Depois de fazer isso, será exibida uma caixa de diálogo pedindo um nome. Fornece o nome como **exibição da Web**. Seu `ViewController.h` arquivo deve parecer com o seguinte:

        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
        
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
        
        @end

5. Atualizaremos a `ViewController.m` arquivo mais tarde, mas primeiro vamos criar o arquivo de ponte que cria uma embalagem sobre alguns iOS Mobile contrato comumente usados métodos SDK. Criar um novo arquivo de cabeçalho chamado **EngagementJsExports.h** que usa o `JSExport` mecanismo descrito na [sessão](https://developer.apple.com/videos/play/wwdc2013/615) mencionado anteriormente para expor os métodos de iOS nativo. 

        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
        
        @protocol EngagementJsExports <JSExport>
        
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
        
        @end

        @interface EngagementJs : NSObject <EngagementJsExports>

        @end

6. Em seguida, podemos criará a segunda parte do arquivo ponte. Crie um arquivo denominado **EngagementJsExports.m** que conterá a implementação criando os conteúdos adicionais reais chamando os métodos SDK do contrato móvel iOS. Observe também que estamos estiver analisando o `extras` estão sendo passados do javascript exibição da Web e colocar isso em um `NSMutableDictionary` objeto a ser transmitido com as chamadas de método de SDK do contrato.  

        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
        
        @implementation EngagementJs
        
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
        
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
        
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
        
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
        
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
        
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
           
           return extras;
        }
        
        @end

5. Agora vamos voltar para o **ViewController.m** e atualizá-lo com o código a seguir: 
        
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
        
        @interface ViewController ()
        
        @end
        
        @implementation ViewController
        
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
        
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
           
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
        
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
           
           context[@"EngagementJs"] = [EngagementJs class];
        }
        
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
        
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
        
        @end

6. Observe os seguintes pontos sobre o arquivo de **ViewController.m** :

    - No `loadWebView` método, estamos carregando um arquivo HTML local chamado **LocalPage.html** cujo código examinaremos Avançar. 
    - No `webViewDidFinishLoad` método, podemos são segurando o `JsContext` e associando nossa classe de embalagem ele. Isso permitirá chamar nossa embalagem métodos SDK usando a alça **EngagementJs** da exibição da Web. 

7. Crie um arquivo denominado **LocalPage.html** com o seguinte código:

        <!doctype html>
        <html>
           <head>
               <style type='text/css'>
                   html { font-family:Helvetica; color:#222; }
                   h1 { color:steelblue; font-size:22px; margin-top:16px; }
                   h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
               </style>
               
               <script type="text/javascript">
               
               window.onerror = function(err)
               {
                   alert('window.onerror: ' + err);
               }
               
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
                   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
                           
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
        
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
                           
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
               
           </head>
           <body>
               <h1>Bridge Tester</h1>
               
               <div id='engagement'>
                   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
                   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
                   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
                   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
               
               </div>
           </body>
        </html>

8. Observe os seguintes pontos sobre o arquivo HTML acima:

    -   Ele contém um conjunto de caixas de entrada, onde você pode fornecer dados a serem usados como nomes de seu evento, o trabalho, o erro, o AppInfo. Quando você clica no botão ao lado dela, uma chamada é feita para o Javascript que eventualmente chama os métodos do arquivo ponte para passar esta chamada para o iOS contrato Mobile SDK. 
    -   Nós são marcação em algumas informações extras estática ao eventos, trabalhos e até mesmo erros para demonstrar como isso pode ser feito. Esta informação extra é enviada como um JSON de cadeia de caracteres que, se você examinar o `EngagementJsExports.m` de arquivo, é analisado e passado juntamente com enviando eventos, trabalhos, erros. 
    -   Um trabalho de contrato Mobile é inicializado com o nome que você especifica na caixa de entrada, executar por 10 segundos e desligar. 
    -   Um contrato de celular appinfo ou marca é passada com customer_name como a chave estática e o valor que você inseriu na entrada como o valor para a marca. 
 
9. Executar o aplicativo e você verá o seguinte. Agora fornecer algum nome de um evento de teste semelhante ao seguinte e clique em **Enviar** ao lado dela. 

    ![][1]

10. Agora se você for para a guia **Monitor** de seus aplicativos e a aparência em **eventos -> detalhes**, você verá este evento aparecem junto com o aplicativo-info estático que estamos está enviando. 

    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png
