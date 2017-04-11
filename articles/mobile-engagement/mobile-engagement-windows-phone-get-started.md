<properties
    pageTitle="Introdução ao contrato do Azure Mobile para Windows Phone Silverlight apps"
    description="Saiba como usar o Azure Mobile contrato com notificações por push e análises para os aplicativos do Windows Phone Silverlight."
    services="mobile-engagement"
    documentationCenter="windows"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-windows-phone-silverlight-apps"></a>Introdução ao contrato do Azure Mobile para Windows Phone Silverlight apps

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile contrato para entender o uso do aplicativo e enviar notificações por push para segmentado usuários de um aplicativo do Silverlight do Windows Phone.
Este tutorial demonstra o cenário de difusão simples usando o contrato de celular. Nele, você criar um aplicativo do Windows Phone Silverlight em branco que coleta de dados básico e recebe as notificações de envio usando o serviço de notificação de envio da Microsoft (MPNS).

> [AZURE.NOTE] Se você está destinando Windows Phone 8.1 (não Silverlight), consulte o [tutorial Universal do Windows](mobile-engagement-windows-store-dotnet-get-started.md).

Este tutorial requer o seguinte:

+ Visual Studio 2013
+ Pacote do Nuget [MicrosoftAzure.MobileEngagement]

> [AZURE.NOTE] Para concluir este tutorial, você deve ter uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started).

##<a id="setup-azme"></a>Configuração móvel contrato para seu Windows Phone app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Conectar seu aplicativo no back-end do contrato de celular

Este tutorial apresenta uma "integração básica", que é o conjunto mínimo necessário para coletar dados e enviar uma notificação de envio. A documentação de integração completa pode ser encontrada na [integração com o SDK do Mobile contrato Windows Phone](mobile-engagement-windows-phone-sdk-overview.md)

Vamos criar um aplicativo básico com o Visual Studio para demonstrar a integração.

###<a name="create-a-new-windows-phone-silverlight-project"></a>Criar um novo projeto do Windows Phone Silverlight

As etapas a seguir presumem o uso do Visual Studio de 2015, embora as etapas são semelhantes nas versões anteriores do Visual Studio. 

1. Inicie o Visual Studio e na tela **inicial** , selecione **Novo projeto**.

2. No pop-up, selecione **Windows 8** -> **Do Windows Phone** -> **Aplicativo em branco (Windows Phone Silverlight)**. Preencha o **nome** do aplicativo e o **nome da solução**e clique em **Okey**.

    ![][1]

3. Você pode optar por direcionar **Windows Phone 8.0** ou **Windows Phone 8.1**.

Agora que você criou um novo aplicativo Windows Phone Silverlight no qual nós serão integradas o SDK do contrato do Azure Mobile.

###<a name="connect-your-app-to-the-mobile-engagement-backend"></a>Conectar seu aplicativo no back-end do contrato de celular

1. Instale o pacote de nuget [MicrosoftAzure.MobileEngagement] em seu projeto.

2. Abrir `WMAppManifest.xml` (sob a pasta de propriedades) e verifique se a seguir é declarada (Adicionar--las se não estiverem) na `<Capabilities />` marca:

        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />

    ![][2]

3. Cole a cadeia de conexão que você copiou anteriormente para o aplicativo móvel contrato agora e colá-lo no `Resources\EngagementConfiguration.xml` arquivo, entre o `<connectionString>` e `</connectionString>` marcas:

    ![][3]

4. No `App.xaml.cs` arquivo:

    a. Adicionar o `using` instrução:

            using Microsoft.Azure.Engagement;

    b. Inicializar o SDK no `Application_Launching` método:

            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }

    c. Insira o seguinte no `Application_Activated`:

            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

##<a id="monitor"></a>Habilitar o monitoramento em tempo real

Para iniciar o envio de dados e garantindo que os usuários estão ativos, você deve enviar pelo menos uma tela (atividade) no back-end do contrato de celular.

1. Em MainPage.xaml.cs, adicione a `using` instrução:

        using Microsoft.Azure.Engagement;

2. Substitua a classe base de **MainPage**, que é antes **PhoneApplicationPage**, **EngagementPage**.

        class MainPage : EngagementPage 
    
3. No seu `MainPage.xml` arquivo:

    a. Adicione a suas declarações de namespaces:

         xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

    b. Substituir `phone:PhoneApplicationPage` no nome da marca XML com `engagement:EngagementPage`.

##<a id="monitor"></a>Conectar-se o aplicativo com monitoramento em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Ativar notificações por push e do aplicativo de mensagens

Contrato de Mobile permite interagir e atinja seus usuários com notificações por Push e sistema de mensagens do aplicativo no contexto de campanhas. Este módulo é chamado alcance no portal do contrato de celular.
As seções a seguir configure seu aplicativo para recebê-los.

###<a name="enable-your-app-to-receive-mpns-push-notifications"></a>Habilitar o seu aplicativo receber notificações por Push de MPNS

Adicionar novos recursos à sua `WMAppManifest.xml` arquivo:

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

###<a name="initialize-the-reach-sdk"></a>Inicializar o SDK do alcance

1. Em `App.xaml.cs`, chamar `EngagementReach.Instance.Init();` na função **Application_Launching** , para a direita após a inicialização do agente:

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

2. Em `App.xaml.cs`, chamar `EngagementReach.Instance.OnActivated(e);` na função **Application_Activated** , para a direita após a inicialização do agente:

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

Está tudo pronto. Agora, podemos verificará que você corretamente gritou esta integração básica.

##<a id="send"></a>Enviar uma notificação para o seu aplicativo

[AZURE.INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Agora você deve ver uma notificação que será mostrado em seu dispositivo como uma notificação do aplicativo se o aplicativo estiver aberto como uma notificação do sistema semelhante ao seguinte: 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
