<properties
    pageTitle="Introdução ao Azure contrato móvel para Xamarin.Android"
    description="Saiba como usar o Azure Mobile contrato com a análise e notificações por Push para os aplicativos de Xamarin.Android."
    services="mobile-engagement"
    documentationCenter="xamarin"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/16/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-xamarinandroid-apps"></a>Introdução ao Azure contrato móvel para aplicativos de Xamarin.Android

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile contrato para entender o uso do aplicativo e como enviar notificações por push para segmentado usuários de um aplicativo de Xamarin.Android.
Este tutorial demonstra o cenário de difusão simples usando o contrato de celular. Nele, você criar um aplicativo de Xamarin.Android em branco que coleta de dados básico e recebe as notificações de envio usando mensagens de nuvem do Google (GCM).

Este tutorial requer o seguinte:

+ [Xamarin Studio](http://xamarin.com/studio). Você também pode usar o Visual Studio com Xamarin, mas este tutorial usa Xamarin Studio. Para obter instruções de instalação, consulte [instalação e instalar para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
+ [Contrato móvel Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] Para concluir este tutorial, você deve ter uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started).

##<a id="setup-azme"></a>Configuração móvel contrato para seu aplicativo do Android

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Conectar seu aplicativo no back-end do contrato de celular

Este tutorial apresenta uma "integração básica", que é o conjunto mínimo necessário para coletar dados e enviar uma notificação de envio. 

Vamos criar um aplicativo básico com Xamarin Studio para demonstrar a integração.

###<a name="create-a-new-xamarinandroid-project"></a>Criar um novo projeto de Xamarin.Android

1. Início **Xamarin Studio** vá para **arquivo** -> **nova** -> **solução** 

    ![][1]

2. Selecione **Aplicativos Android** e em seguida, verifique se que o idioma selecionado é **c#** e clique em **Avançar**.

    ![][2]

3. Preencha o **Nome do aplicativo** e o **identificador de organização**. Verifique se a marca de seleção **Google Play Services** e clique em **Avançar**. 

    ![][3]
    
4. Atualize o **Nome do projeto**, o **Nome da solução** e o **local** se necessário e clique em **criar**.

    ![][4]
 
Xamarin Studio criará o aplicativo no qual nós serão integradas contrato de celular. 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Conectar seu aplicativo a Mobile contrato back-end

1. Clique com botão direito a pasta de **pacotes** nas janelas de solução e selecione **Adicionar pacotes …**

    ![][5]

2. Procure o **SDK do Microsoft Azure Mobile contrato Xamarin** e adicioná-lo à sua solução.  

    ![][6]
   
3. Abra **MainActivity.cs** e adicione o seguinte usando instruções:

        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;

4. No `OnCreate` método, adicione o seguinte para inicializar a conexão com o contrato de celular back-end. Certifique-se de adicionar seu **ConnectionString**. 

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

###<a name="add-permissions-and-a-service-declaration"></a>Adicionar permissões e uma declaração de serviço

1. Abra o arquivo **manifest** sob a pasta de propriedades. Selecione a guia fonte para que você atualiza o código-fonte XML diretamente.
 
2. Adicionar essas permissões para o manifest. XML (que pode ser encontrado na pasta de **Propriedades** ) do seu projeto imediatamente antes ou depois do `<application>` marca:

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

3. Adicione o seguinte entre o `<application>` e `</application>` marcas declarar o serviço do agente:

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

4. No código que você acabou de colar, substitua `"<Your application name>"` no rótulo. Isso é exibido no menu **configurações** , onde os usuários podem ver os serviços em execução no dispositivo. Você pode adicionar a palavra "Serviço" dessa etiqueta por exemplo.

###<a name="send-a-screen-to-mobile-engagement"></a>Enviar uma tela para contrato de celular

Para iniciar o envio de dados e garantindo que os usuários estão ativos, você deve enviar pelo menos uma tela no back-end do contrato de celular. Para fazer isso-garantir que o `MainActivity` herda `EngagementActivity` em vez de `Activity`.

    public class MainActivity : EngagementActivity
    
Como alternativa, se você não pode herdar de `EngagementActivity` , em seguida, você deve adicionar `.StartActivity` e `.EndActivity` métodos em `OnResume` e `OnPause` respectivamente.  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }
    
            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

##<a id="monitor"></a>Conectar-se o aplicativo com monitoramento em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Ativar notificações por push e do aplicativo de mensagens

Mobile contrato permite interagir e ATINJA seus usuários com notificações por push e no contexto de campanhas de mensagens do aplicativo. Este módulo é chamado alcance no portal do contrato de celular.
As seções a seguir configura seu aplicativo para recebê-los.

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png
