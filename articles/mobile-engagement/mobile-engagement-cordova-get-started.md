<properties
    pageTitle="Introdução ao Azure contrato móvel para Cordova/Phonegap"
    description="Saiba como usar o Azure Mobile contrato com a análise e notificações por Push para os aplicativos de Cordova/Phonegap."
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-phonegap"
    ms.devlang="js"
    ms.topic="hero-article" 
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Introdução ao Azure contrato móvel para Cordova/Phonegap

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile contrato para entender o uso do aplicativo e enviar notificações por push para segmentado usuários para um aplicativo móvel desenvolvidos com Cordova.

Neste tutorial, podemos criará um aplicativo de Cordova em branco usando Mac e integrar o SDK do contrato Mobile. Ele coleta dados analytics básicas e recebe as notificações de envio usando o sistema de notificação de Push da Apple (APNS) para iOS e mensagens de nuvem do Google (GCM) para Android. Nós implantará a um iOS ou dispositivo Android para teste. 

> [AZURE.NOTE] Para concluir este tutorial, você deve ter uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started).

Este tutorial requer o seguinte:

+ XCode, que você pode instalar Mac App Store (para implantar em iOS)
+ [Emulador & SDK do android](http://developer.android.com/sdk/installing/index.html) (para implantar em Android)
+ Certificado de notificação de envio (P12) que você pode obter a partir do Centro de desenvolvimento de Apple para APNS
+ Número de projeto GCM que pode ser obtido seu Console de desenvolvedor do Google para GCM
+ [Plug-in Cordova contrato móvel](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE] Você pode encontrar o código-fonte e o arquivo Leiame do plug-in Cordova no [Github](https://github.com/Azure/azure-mobile-engagement-cordova)

##<a id="setup-azme"></a>Configuração móvel contrato para o aplicativo de Cordova

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Conectando seu aplicativo no back-end do contrato de celular

Este tutorial apresenta uma "integração básica" qual é o mínimo definir necessários para coletar dados e enviar uma notificação de envio. 

Vamos criar um aplicativo básico com Cordova para demonstrar a integração:

###<a name="create-a-new-cordova-project"></a>Criar um novo projeto de Cordova

1. Inicie a janela *Terminal* em sua máquina de Mac e digite o seguinte que criará um novo projeto de Cordova do modelo padrão. Certifique-se de que o perfil de publicação que você eventualmente use para implantar o seu aplicativo iOS está usando 'com.mycompany.myapp' como o App ID. 

        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova

2. Execute o seguinte para configurar seu projeto para **iOS** e executá-lo no iOS Simulator:

        $ cordova platform add ios 
        $ cordova run ios

3. Execute o seguinte para configurar seu projeto para o **Android** e executá-lo no emulador Android. Verifique se suas configurações de Android SDK emulador tem seu destino como Google APIs (Google Inc.) com a CPU / ABI como Google APIs ARM.  

        $ cordova platform add android
        $ cordova run android

4.  Adicione o plug-in do Console de Cordova. 

        $ cordova plugin add cordova-plugin-console 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Conectar seu aplicativo a Mobile contrato back-end

1. Instale o plug-in do Azure Mobile contrato Cordova fornecendo os valores de variáveis para configurar o plug-in:

        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
            --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Ícone de alcançar Android* : deve ser o nome do recurso sem qualquer extensão, nem o prefixo drawable (ex: mynotificationicon), e o arquivo de ícone deve ser copiado em seu projeto android (plataformas/android/res/drawable)

*iOS ícone atinja* : deve ser o nome do recurso com sua extensão (ex: mynotificationicon.png), e o arquivo de ícone deve ser adicionado ao seu projeto do iOS com XCode (usando o Menu de arquivos Adicionar)

##<a id="monitor"></a>Ativando o monitoramento em tempo real

1. No projeto Cordova - edite **www/js/index.js** para adicionar a chamada para contrato de celular declarar uma nova atividade de uma vez o evento *deviceReady* for recebida.

         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }

2. Execute o aplicativo:
        
    - **Para iOS**
    
        No `Terminal` janela iniciar seu aplicativo em uma nova instância Simulator executando o seguinte:

            cordova run ios

    - **Para o Android**
        
        No `Terminal` janela iniciar seu aplicativo em uma nova instância de emulador executando o seguinte:

            cordova run android

3. Você pode ver o seguinte no console logs:

        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

##<a id="monitor"></a>Conectar-se o aplicativo com monitoramento em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Ativar notificações por Push e do aplicativo de mensagens

Contrato de Mobile permite interagir com seus usuários usando notificações por Push e no contexto de campanhas de mensagens do aplicativo. Este módulo é chamado alcance no portal do contrato de celular.
As seções a seguir irá configurar seu aplicativo para recebê-los.

###<a name="configure-push-credentials-for-mobile-engagement"></a>Configurar credenciais de envio para contrato de celular

Para permitir que o contrato de celular enviar notificações por Push em seu nome, você precisa conceder acesso ao seu certificado do Apple iOS ou GCM chave de API de servidor. 
    
1. Navegue até seu portal de contrato de celular. Certifique-se de que você estiver no aplicativo nós estiver usando para esse projeto e, em seguida, clique no botão **Engage** na parte inferior:
    
    ![][1]
    
2. Você irá pouse na página configurações em seu Portal de contrato. Clique em há na seção **Push nativo** :
    
    ![][2]

3. Configurar iOS chave de API do servidor de certificado/GCM

    **[iOS]**

    a. Selecione seu p12, carregue-o e digite sua senha:
    
    ![][3]

    **[Android]**

    a. Clique no ícone de edição na frente de **Chave API** na seção configurações do GCM e no menu pop-up que mostra, cole a chave do servidor GCM e clique em **Okey**. 
        
    ![][4]

###<a name="enable-push-notifications-in-the-cordova-app"></a>Ativar notificações por push no aplicativo Cordova

Edite **www/js/index.js** para adicionar a chamada para contrato de celular para solicitar notificações por push e declarar um manipulador:

     onDeviceReady: function() {
           Engagement.initializeReach(  
                // on OpenUrl  
                function(_url) {   
                alert(_url);   
                });  
            Engagement.startActivity("myPage",{});  
        }

###<a name="run-the-app"></a>Executar o aplicativo

**[iOS]**

1. Usaremos XCode criem e implantem o aplicativo no dispositivo para testar as notificações por push desde iOS permite apenas as notificações por push para um dispositivo real. Vá para o local onde seu projeto Cordova é criado e navegue até o local do **...\platforms\ios** . Abra o arquivo .xcodeproj nativo em XCode. 
    
2. Construa e implante o aplicativo Cordova ao dispositivo iOS usando a conta que tem o provisionamento perfil que contém o certificado que você acabou carregado para o portal de contrato de celular e a Id de aplicativo do quais correspondências aquele que você forneceu ao criar o aplicativo Cordova. Você pode conferir o *identificador de pacote* no seu **recursos\*-info.plist** arquivo em XCode correspondente para cima. 

3. Você verá o popup iOS padrão em seu dispositivo dizendo que o aplicativo solicita permissão para enviar notificações. Conceda a permissão. 

**[Android]**

Você pode simplesmente usar o emulador para executar o aplicativo do Android, como notificações de GCM são suportadas no emulador Android. 

    cordova run android

##<a id="send"></a>Enviar uma notificação para o seu aplicativo

Agora vamos criar uma campanha de notificação de envio simple que enviará um envio para seu aplicativo em execução no dispositivo:

1. Navegue até a guia **atingir** em seu portal de contrato de celular

2. Clique em **Novo aviso** para criar sua campanha de envio

    ![][6]

3. Forneça entradas para criar sua campanha **[Android]**
    
    - Forneça um **nome** para sua campanha. 
    - Selecione o **Tipo de entrega** como *simples* *notificação do sistema*
    - Selecione a **hora de entrega** como *"qualquer hora"*
    - Fornece um **título** para sua notificação de que será a primeira linha do envio.
    - Forneça uma **mensagem** para sua notificação de que servirá como o corpo da mensagem. 

    ![][11]

4. Forneça entradas para criar sua campanha **[iOS]**

    - Forneça um **nome** para sua campanha. 
    - Selecione a **hora de entrega** como *"sair do aplicativo"*
    - Fornece um **título** para sua notificação de que será a primeira linha do envio.
    - Forneça uma **mensagem** para sua notificação de que servirá como o corpo da mensagem. 
 
    ![][12]

5. Role para baixo e, na seção conteúdo selecione **notificação somente**

    ![][8]

6. [Opcional] Você também pode fornecer uma URL de ação. Certifique-se de que ele usa um esquema de URL fornecido ao configurar o plug-in **AZME\_REDIRECIONAR\_URL** variável por exemplo, *myapp://test*.  

7. Terminar configuração mais básica campanha possível. Agora, role para baixo novamente e clique no botão **criar** para salvar sua campanha.

8. Finalmente **Ativar** sua campanha
    
    ![][10]

9. Agora você deve ver uma notificação de envio do dispositivo ou emulador como parte desta campanha. 

##<a id="next-steps"></a>Próximas etapas
[Visão geral de todos os métodos disponíveis com Cordova Mobile contrato SDK](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png

