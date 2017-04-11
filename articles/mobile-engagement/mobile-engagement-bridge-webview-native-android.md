<properties 
    pageTitle="Exibição da Web Android ponte com nativo SDK Android contrato de celular" 
    description="Descreve como criar uma ponte entre a exibição da Web executando o Javascript e Android SDK de contrato do Mobile nativo"      
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-android" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>Exibição da Web Android ponte com nativo SDK Android contrato de celular

> [AZURE.SELECTOR]
- [Ponte do Android](mobile-engagement-bridge-webview-native-android.md)
- [Ponte de iOS](mobile-engagement-bridge-webview-native-ios.md)

Alguns aplicativos móveis destinam-se como um aplicativo híbrido onde próprio aplicativo foi desenvolvido usando desenvolvimento Android nativo, mas algumas ou todas as telas são renderizadas em uma exibição da Web Android. Você ainda poderá consumir Mobile contrato Android SDK dentro tais aplicativos e este tutorial descreve como fazer isso. O código de exemplo abaixo baseia-se na documentação do Android [aqui](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript). Ele descreve como essa abordagem documentada poderia ser usada para implementar o mesmo para métodos comumente usados do Mobile contrato Android SDK que uma exibição da Web a partir de um aplicativo híbrido também pode iniciar solicitações para controlar eventos, trabalhos, erros, informações de aplicativo enquanto tubulação-las por meio de nosso SDK Android. 

1. Primeiro, é necessário garantir que você passou entre nosso [tutorial de Introdução](mobile-engagement-android-get-started.md) para integrar o SDK do Android contrato Mobile em seu aplicativo híbrido. Depois de fazer isso, seu `OnCreate` método será a aparência a seguir.  
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }

2. Agora, certifique-se de que seu aplicativo híbrido tem uma tela com uma exibição da Web nele. O código para ele será semelhante ao seguinte onde estamos carregando uma HTML local **Sample.html** de arquivo na exibição da Web no `onCreate` método de sua tela. 

        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }

        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }

3. Agora crie um arquivo de ponte chamado **WebAppInterface** que cria uma embalagem sobre alguns métodos Mobile contrato Android SDK comumente usados usando o `@JavascriptInterface` abordagem descrita na [documentação Android](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript):

        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
        
        import com.microsoft.azure.engagement.EngagementAgent;
        
        import org.json.JSONArray;
        import org.json.JSONObject;
        
        public class WebAppInterface {
            Context mContext;
        
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
        
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
        
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
        
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
        
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  

4. Após criar o arquivo de ponte acima, precisamos garantir que ele está associado a nossa exibição da Web. Para isso acontecer, você precisa editar seu `SetWebview` método para que ele se pareça com o seguinte:

        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }

5. O trecho acima, chamamos `addJavascriptInterface` para associar nossa classe de ponte nossa exibição da Web e também criou uma alça chamada **EngagementJs** para chamar os métodos do arquivo ponte. 

6. Agora crie o arquivo a seguir chamado **Sample.html** em seu projeto em uma pasta chamada **ativos** que está colocado corretamente a exibição da Web e onde estamos chamará os métodos do arquivo ponte.

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
                      log('window.onerror: ' + err);
                    }
        
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
        
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
        
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
        
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
        
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
        
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
        
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
        
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>

8. Observe os seguintes pontos sobre o arquivo HTML acima:

    -   Ele contém um conjunto de caixas de entrada, onde você pode fornecer dados a serem usados como nomes de seu evento, o trabalho, o erro, o AppInfo. Quando você clica no botão ao lado dela, uma chamada é feita para o Javascript que eventualmente chama os métodos do arquivo ponte para passar esta chamada para o SDK do Mobile contrato Android. 
    -   Nós são marcação em algumas informações extras estática ao eventos, trabalhos e até mesmo erros para demonstrar como isso pode ser feito. Esta informação extra é enviada como um JSON de cadeia de caracteres que, se você examinar o `WebAppInterface` de arquivo, é analisado e colocar em um dispositivo Android `Bundle` e é passado juntamente com enviando eventos, trabalhos, erros. 
    -   Um trabalho de contrato Mobile é inicializado com o nome que você especifica na caixa de entrada, executar por 10 segundos e desligar. 
    -   Um contrato de celular appinfo ou marca é passada com customer_name como a chave estática e o valor que você inseriu na entrada como o valor para a marca. 
 
9. Executar o aplicativo e você verá o seguinte. Agora, forneça alguns nome de um evento de teste semelhante ao seguinte e clique em **Enviar** abaixo dela. 

    ![][1]

10. Agora se você for para a guia **Monitor** de seus aplicativos e a aparência em **eventos -> detalhes**, você verá este evento aparecem junto com o aplicativo-info estático que estamos está enviando. 

    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png
