<properties
    pageTitle="Começar a usar o Android aplicativos Azure Mobile contrato"
    description="Saiba como usar Azure Mobile contrato com notificações por push e análises para aplicativos Android."
    services="mobile-engagement"
    documentationCenter="android"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="08/10/2016"
    ms.author="piyushjo;ricksal" />

# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>Introdução ao Azure Mobile contrato para aplicativos Android

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile contrato para entender o uso do aplicativo e como enviar notificações por push para segmentado usuários de um aplicativo do Android.
Este tutorial demonstra o cenário de difusão simples usando o contrato de celular. Nele, você criar um aplicativo Android em branco que coleta de dados básico e recebe as notificações de envio usando mensagens de nuvem do Google (GCM).

## <a name="prerequisites"></a>Pré-requisitos

Concluir este tutorial requer as [Ferramentas de desenvolvedor do Android](https://developer.android.com/sdk/index.html), que inclui o ambiente de desenvolvimento integrado do Android Studio e a plataforma mais recente do Android.

Ele também requer o [SDK do Mobile contrato Android](https://aka.ms/vq9mfn).

> [AZURE.IMPORTANT] Para concluir este tutorial, você precisa de uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).

## <a name="set-up-mobile-engagement-for-your-android-app"></a>Configurar o contrato de Mobile para seu aplicativo do Android

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Conectar seu aplicativo no back-end do contrato de celular

Este tutorial apresenta uma "integração básica", que é o conjunto mínimo necessário para coletar dados e enviar uma notificação de envio. Você pode criar um aplicativo básico com Android Studio para demonstrar a integração.

A documentação de integração completa pode ser encontrada na [integração com o SDK do Mobile contrato Android](mobile-engagement-android-sdk-overview.md).

### <a name="create-an-android-project"></a>Criar um projeto Android

1. Comece **Studio Android**e no pop-up, selecione **Iniciar um novo projeto de Studio Android**.

    ![][1]

2. Forneça um domínio de nome e empresa do aplicativo. Tome nota dos quais você está preenchendo, pois você precisará dele posteriormente. Clique em **Avançar**.

    ![][2]

3. Selecione o nível de API e fator de formulário de destino e clique em **Avançar**.

    >[AZURE.NOTE] Contrato de celular requer o nível de API 10 mínimo (2.3.3 de não Android).

    ![][3]

4. Selecione a **Atividade em branco** aqui, que é a única tela para este aplicativo e clique em **Avançar**.

    ![][4]

5. Finalmente, deixe os padrões como está e clique em **Concluir**.

    ![][5]

Studio Android agora cria o aplicativo de demonstração em que podemos integrar o contrato de celular.

### <a name="include-the-sdk-library-in-your-project"></a>Incluir biblioteca SDK em seu projeto

1. Baixe o [SDK do contrato móvel Android](https://aka.ms/vq9mfn).
2. Extrai o arquivo para uma pasta em seu computador.
3. Identificar a biblioteca. jar para a versão atual deste SDK e copiá-lo para a área de transferência.

      ![][6]

4. Navegue até a seção de **projeto** (1) e cole o JAR na pasta de bibliotecas (2).

      ![][7]

5. Para carregar a biblioteca, sincronize o projeto.

      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>Conectar seu aplicativo a Mobile contrato back-end com a cadeia de Conexão

1. Copie as seguintes linhas de código para a criação de atividade (deve ser feito apenas em um local do seu aplicativo, geralmente a atividade principal). Para este aplicativo de amostra, abra a MainActivity em src -> principal -> pasta java e adicione o seguinte:

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Resolver as referências pressionando Alt + Enter ou adicionando as instruções de importação a seguir:

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;

3. Volte para o Portal de clássico do Azure na página de **Informações de Conexão** de seu aplicativo e copie a **Cadeia de Conexão**.

      ![][9]

4. Colá-lo para o `setConnectionString` parâmetro, substituindo a cadeia de caracteres inteira mostrada no código a seguir:

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>Adicionar permissões e uma declaração de serviço

1. Adicionar essas permissões para o manifest do seu projeto imediatamente antes ou depois do `<application>` marca:

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. Para declarar o serviço do agente, adicione este código entre o `<application>` e `</application>` marcas:

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

3. No código colado, substitua `"<Your application name>"` na etiqueta, que é exibido no menu **configurações** , onde você pode ver os serviços em execução no dispositivo. Você pode adicionar a palavra "Serviço" dessa etiqueta por exemplo.

### <a name="send-a-screen-to-mobile-engagement"></a>Enviar uma tela para contrato de celular

Para iniciar o envio de dados e certifique-se de que os usuários estão ativos, você deve enviar pelo menos uma tela (atividade) no back-end do contrato de celular.

Vá para **MainActivity.java** e adicione o seguinte para substituir a classe base de **MainActivity** para **EngagementActivity**:

    public class MainActivity extends EngagementActivity {

> [AZURE.NOTE] Se sua classe base não é *atividade*, consulte [Avançadas de relatório Android](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes) para como herdar de classes diferentes.


Comentar a seguinte linha para esse cenário de exemplo simples:

    // setSupportActionBar(toolbar);

Se você quiser manter o `ActionBar` em seu aplicativo, consulte [Avançadas de relatório Android](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes).

## <a name="connect-app-with-real-time-monitoring"></a>Conectar-se o aplicativo com monitoramento em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-in-app-messaging"></a>Ativar notificações por push e do aplicativo de mensagens

Durante uma campanha, Mobile contrato permite interagir com e ATINJA seus usuários com notificações por push e sistema de mensagens do aplicativo. Este módulo é chamado alcance no portal do contrato de celular.
A seção a seguir configura seu aplicativo para recebê-los.

### <a name="copy-sdk-resources-in-your-project"></a>Recursos SDK de cópia do seu projeto

1. Navegue de volta para seu conteúdo de download SDK e copie a pasta **res** .

    ![][10]

2. Vá para Android Studio, selecione o diretório **principal** de seus arquivos de projeto e colá-lo para adicionar recursos ao projeto.

    ![][11]

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Acesse o [SDK do Android](mobile-engagement-android-sdk-overview.md) para obter conhecimento detalhado sobre a integração de SDK.

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png
