<properties
    pageTitle="Introdução ao Azure Mobile contrato para aplicativos Web | Microsoft Azure"
    description="Saiba como usar o Azure Mobile contrato com notificações por push e análises Web Apps."
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="js"
    ms.topic="hero-article"
    ms.date="06/01/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-web-apps"></a>Introdução ao Azure Mobile contrato para aplicativos Web

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile contrato para entender o uso de Web App.

Este tutorial requer o seguinte:

+ Visual Studio 2015 ou qualquer outro editor de sua escolha
+ [Web SDK](http://aka.ms/P7b453) 

Este SDK Web está no modo de visualização e apenas suporta análise no momento e não dá suporte enviando notificações de envio do navegador ou do aplicativo ainda. 

> [AZURE.NOTE] Para concluir este tutorial, você deve ter uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started).

##<a name="setup-mobile-engagement-for-your-web-app"></a>Configuração móvel contrato para o aplicativo Web

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Conectar seu aplicativo no back-end do contrato de celular

Este tutorial apresenta uma "integração básica," qual é o conjunto mínimo necessário para coletar dados.

Vamos criar um aplicativo web básica com o Visual Studio para demonstrar a integração, embora você pode seguir as etapas com qualquer aplicativo web criado fora do Visual Studio também. 

###<a name="create-a-new-web-app"></a>Criar um novo aplicativo Web

As etapas a seguir presumem o uso do Visual Studio de 2015, embora as etapas são semelhantes nas versões anteriores do Visual Studio. 

1. Inicie o Visual Studio e na tela **inicial** , selecione **Novo projeto**.

2. No pop-up, selecione **Web** -> **Aplicativo Web ASP.Net**. Preencha o aplicativo **nome**, **local** e o **nome da solução**e clique em **Okey**.

3. Em **Selecione um modelo de** pop-up, selecione **vazio** em **ASP.Net 4,5 modelos** e clique **Okey**. 

Agora você criou um novo projeto de aplicativo Web em branco em que estamos serão integradas o SDK do Azure Mobile contrato da Web.

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Conectar seu aplicativo a Mobile contrato back-end

1. Crie uma nova pasta chamada **javascript** em sua solução e adicione o arquivo de Web SDK JS **azure engagement.js** nela. 

2. Adicione um novo arquivo chamado **js** nesta pasta javascript com o código a seguir. Certifique-se de atualizar a cadeia de conexão. Isso `azureEngagement` objeto será usado para acessar os métodos de Web SDK. 

        var azureEngagement = {
            debug: true,
            connectionString: 'xxxxx'
        };

    ![Visual Studio com arquivos js][1]

##<a name="enable-real-time-monitoring"></a>Habilitar o monitoramento em tempo real

Para iniciar o envio de dados e garantindo que os usuários estão ativos, você deve enviar pelo menos uma atividade no back-end do contrato de celular. Uma atividade no contexto de um aplicativo web é uma página da web. 

1. Crie uma nova página chamada **home.html** em sua solução e defini-lo como a página inicial de seu aplicativo web. 
2. Inclua os dois javascripts que adicionamos anteriormente nesta página, adicionando o seguinte dentro da marca de corpo. 

        <script type="text/javascript" src="javascript/main.js"></script>
        <script type="text/javascript" src="javascript/azure-engagement.js"></script>

3. Atualizar a marca de corpo para chamar do EngagementAgent `startActivity` método
        
        <body onload="engagement.agent.startActivity('Home')">

4. Aqui está a aparência seu **home.html**
        
        <html>
        <head>
            ...
        </head>
        <body onload="engagement.agent.startActivity('Home')">
            <script type="text/javascript" src="javascript/main.js"></script>
            <script type="text/javascript" src="javascript/azure-engagement.js"></script>
        </body>
        </html>

##<a name="connect-app-with-real-time-monitoring"></a>Conectar-se o aplicativo com monitoramento em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

![][2]

##<a name="extend-analytics"></a>Estender a análise

Aqui estão todos os métodos disponíveis atualmente com SDK Web que você pode usar para análise:

1. Páginas de atividades/Web:

        engagement.agent.startActivity(name);
        engagement.agent.endActivity();

2. Eventos
        
        engagement.agent.sendEvent(name, extras);

3. Erros

        engagement.agent.sendError(name, extras);

4. Trabalhos

        engagement.agent.startJob(name);
        engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png

