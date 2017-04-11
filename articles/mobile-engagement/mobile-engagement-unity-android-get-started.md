<properties
    pageTitle="Introdução ao Azure Mobile contrato para implantação unidade Android"
    description="Saiba como usar o Azure Mobile contrato com a análise e notificações por Push para os aplicativos de unidade implantando em dispositivos iOS."
    services="mobile-engagement"
    documentationCenter="unity"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-unity-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-unity-android-deployment"></a>Introdução ao Azure Mobile contrato para implantação unidade Android

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile contrato para entender o uso do aplicativo e como enviar as notificações por push para segmentado usuários de um aplicativo de unidade ao implantar em um dispositivo Android.
Este tutorial usa a unidade clássico implementar um tutorial bola como ponto de partida. Você deve seguir as etapas deste [tutorial](mobile-engagement-unity-roll-a-ball.md) antes de prosseguir com a integração de contrato Mobile que podemos showcase no tutorial abaixo. 

Este tutorial requer o seguinte:

+ [Editor de unidade](http://unity3d.com/get-unity)
+ [Unidade de contrato móvel SDK](https://aka.ms/azmeunitysdk)
+ Google Android SDK

> [AZURE.NOTE] Para concluir este tutorial, você deve ter uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-android-get-started).

##<a id="setup-azme"></a>Configuração móvel contrato para seu aplicativo do Android

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Conectar seu aplicativo no back-end do contrato de celular

###<a name="import-the-unity-package"></a>Importar o pacote de unidade

1. Baixe o [pacote de unidade de contrato de celular](https://aka.ms/azmeunitysdk) e salvá-lo no computador local. 

2. Vá para **ativos -> Importar pacote -> pacote personalizado** e selecione o pacote baixado na etapa acima. 

    ![][70] 

3. Verifique se todos os arquivos são selecionados e clique em botão **Importar** . 

    ![][71] 

4. Depois que a importação for bem-sucedido, você verá os arquivos SDK importados em seu projeto.  

    ![][72] 

###<a name="update-the-engagementconfiguration"></a>Atualizar o EngagementConfiguration

1. Abra o arquivo de script **EngagementConfiguration** da pasta SDK e atualizar o **ANDROID\_conexão\_cadeia** com a cadeia de conexão que você obtido anteriormente o portal do Azure.  

    ![][73]

2. Salve o arquivo 

3. Executar **arquivo -> contrato -> Gerar manifesto Android**. Este é o plug-in adicionado pelo SDK do contrato Mobile e clicar nele atualizará automaticamente as configurações do projeto. 

    ![][74]

> [AZURE.IMPORTANT] Certifique-se executar esta sempre que você atualizar o arquivo de **EngagementConfiguration** caso contrário, que suas alterações não serão refletidas no aplicativo. 

###<a name="configure-the-app-for-basic-tracking"></a>Configurar o aplicativo para acompanhamento básico

1. Abra o script de **PlayerController** anexado ao objeto Player para edição. 

2. Adicione o seguinte usando instrução:

        using Microsoft.Azure.Engagement.Unity;

3. Adicione o seguinte para o `Start()` método
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###<a name="deploy-and-run-the-app"></a>Implante e execute o aplicativo
Certifique-se de que você tenha Android SDK instalado no seu computador antes de tentar implantar esse aplicativo de unidade em seu dispositivo. 

1. Conecte um dispositivo Android para o seu computador. 

2. Abrir o **arquivo -> configurações de compilação** 

    ![][40]

3. Selecione **Android** e clique na **Opção de plataforma**

    ![][51]

    ![][52]

4. Clique em **configurações do Player** e fornecem um identificador de pacote válido. 

    ![][53]

5. Finalmente, clique em **Criar e executar**

    ![][54]

6. Você pode ser solicitado a fornecer um nome de pasta para armazenar o pacote Android. 

7. Se tudo der pequeno, o pacote será implantado no seu dispositivo conectado e você verá o jogo de unidade em seu telefone! 

##<a id="monitor"></a>Conectar-se o aplicativo com monitoramento em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Ativar notificações por push e do aplicativo de mensagens

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

###<a name="update-the-engagementconfiguration"></a>Atualizar o EngagementConfiguration

1. Abra o arquivo de script **EngagementConfiguration** da pasta SDK e atualizar o **ANDROID\_GOOGLE\_número** com o **Número de projeto do Google** você obtido anteriormente o portal do desenvolvedor de nuvem do Google. Esta é uma string valor então certifique-se colocá-lo entre aspas duplas. 

    ![][75]

2. Salve o arquivo. 

3. Executar **arquivo -> contrato -> Gerar manifesto Android**. Este é o plug-in adicionado pelo SDK do contrato Mobile e clicar nele atualizará automaticamente as configurações do projeto. 

    ![][74]

###<a name="configure-the-app-to-receive-notifications"></a>Configurar o aplicativo para receber notificações

1. Abra o script de **PlayerController** anexado ao objeto Player para edição. 

2. Adicione o seguinte para o `Start()` método

        EngagementReachAgent.Initialize();

3. Agora que o aplicativo for atualizado, implantar e executar o aplicativo em um dispositivo seguindo as instruções fornecidas abaixo. 

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[40]: ./media/mobile-engagement-unity-android-get-started/40.png
[70]: ./media/mobile-engagement-unity-android-get-started/70.png
[71]: ./media/mobile-engagement-unity-android-get-started/71.png
[72]: ./media/mobile-engagement-unity-android-get-started/72.png
[73]: ./media/mobile-engagement-unity-android-get-started/73.png
[74]: ./media/mobile-engagement-unity-android-get-started/74.png
[75]: ./media/mobile-engagement-unity-android-get-started/75.png
[51]: ./media/mobile-engagement-unity-android-get-started/51.png
[52]: ./media/mobile-engagement-unity-android-get-started/52.png
[53]: ./media/mobile-engagement-unity-android-get-started/53.png
[54]: ./media/mobile-engagement-unity-android-get-started/54.png
