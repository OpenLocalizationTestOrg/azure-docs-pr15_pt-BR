<properties
    pageTitle="Introdução ao Hubs de notificação do Azure para aplicativos de dispositivos Kindle | Microsoft Azure"
    description="Neste tutorial, você saiba como usar Hubs de notificação do Azure para enviar notificações por push para um aplicativo de dispositivos Kindle."
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-kindle"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Introdução ao Hubs de notificação para aplicativos de dispositivos Kindle

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Visão geral

Este tutorial mostra como usar Hubs de notificação do Azure para enviar notificações por push para um aplicativo de dispositivos Kindle.
Você vai criar um aplicativo de dispositivos Kindle em branco que recebe as notificações por push usando mensagens de dispositivo Amazon (ADM).

##<a name="prerequisites"></a>Pré-requisitos

Este tutorial requer o seguinte:

+ Obtenha o SDK do Android (estamos supondo que você usará Eclipse) do <a href="http://go.microsoft.com/fwlink/?LinkId=389797">site Android</a>.
+ Siga as etapas em <a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">Configuração o seu ambiente de desenvolvimento</a> para configurar seu ambiente de desenvolvimento para dispositivos Kindle.

##<a name="add-a-new-app-to-the-developer-portal"></a>Adicionar um novo aplicativo para o portal do desenvolvedor

1. Primeiro, crie um aplicativo no [portal do desenvolvedor Amazon].

    ![][0]

2. Copie a **chave de aplicativo**.

    ![][1]

3. No portal do, clique no nome do seu aplicativo e clique na guia **Dispositivo de mensagens** .

    ![][2]

4. Clique em **criar um novo perfil de segurança**e, em seguida, criar um novo perfil de segurança (por exemplo, o **perfil de segurança de TestAdm**). Clique em **Salvar**.

    ![][3]

5. Clique em **Perfis de segurança** para exibir o perfil de segurança que você acabou de criar. Copie os valores de **ID do cliente** e **Segredo cliente** para uso posterior.

    ![][4]

## <a name="create-an-api-key"></a>Criar uma chave API

1. Abra um prompt de comando com privilégios de administrador.
2. Navegue até a pasta SDK Android.
3. Digite o seguinte comando:

        keytool -list -v -alias androiddebugkey -keystore ./debug.keystore

    ![][5]

4.  A senha de **repositório de chaves** , digite **android**.

5.  Copie a impressão digital **MD5** .
6.  De volta ao portal do desenvolvedor, na guia **mensagens** , clique em **Dispositivos Android/Kindle** e insira o nome do pacote de seu aplicativo (por exemplo, **com.sample.notificationhubtest**) e o valor de **MD5** e clique em **Gerar chave de API**.

## <a name="add-credentials-to-the-hub"></a>Adicionar credenciais para o hub

No portal do, adicione o segredo cliente e a ID do cliente para a guia **Configurar** do seu hub de notificação.

## <a name="set-up-your-application"></a>Configurar o seu aplicativo

> [AZURE.NOTE] Quando você estiver criando um aplicativo, use pelo menos 17 de nível de API.

Adicione as bibliotecas ADM ao seu projeto Eclipse:

1. Para obter a biblioteca ADM, [Baixe o SDK]. Extraia o arquivo zip SDK.
2. No Eclipse, seu projeto de atalho e, em seguida, clique em **Propriedades**. Selecione **Java construir caminho** à esquerda e selecione a guia **bibliotecas **na parte superior. Clique em **Adicionar Jar externos**e selecione o arquivo `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` do diretório em que você extraídas o SDK do Amazon.
3. Baixe o SDK do Android NotificationHubs (link).
4. Descompactar o pacote e, em seguida, arraste o arquivo `notification-hubs-sdk.jar` para o `libs` pasta no Eclipse.

Edite seu manifesto de aplicativo para oferecer suporte a ADM:

1. Adicione o namespace Amazon no elemento manifesto raiz:


        xmlns:amazon="http://schemas.amazon.com/apk/res/android"

2. Adicione permissões como o primeiro elemento sob o elemento manifesto. Substitua **[Nome do seu pacote]** com o pacote que você usou para criar o seu aplicativo.

        <permission
         android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
         android:protectionLevel="signature" />

        <uses-permission android:name="android.permission.INTERNET"/>

        <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

        <!-- This permission allows your app access to receive push notifications
        from ADM. -->
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

        <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Inserir o seguinte elemento como o primeiro filho do elemento de aplicativo. Lembre-se de substitua **[Nome do seu serviço]** com o nome do seu manipulador de mensagem ADM que você criar na próxima seção (incluindo o pacote) e substitua **[Nome do seu pacote]** com o nome do pacote com a qual você criou seu aplicativo.

        <amazon:enable-feature
              android:name="com.amazon.device.messaging"
                     android:required="true"/>
        <service
            android:name="[YOUR SERVICE NAME]"
            android:exported="false" />

        <receiver
            android:name="[YOUR SERVICE NAME]$Receiver" />

            <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
          <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
          <action android:name="com.amazon.device.messaging.intent.RECEIVE" />

          <!-- Replace the name in the category tag with your app's package name. -->
          <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>

## <a name="create-your-adm-message-handler"></a>Criar seu manipulador de mensagem ADM

1. Criar uma nova classe que herda `com.amazon.device.messaging.ADMMessageHandlerBase` e chame- `MyADMMessageHandler`, conforme mostrado na figura a seguir:

    ![][6]

2. Adicione o seguinte `import` instruções:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.support.v4.app.NotificationCompat;
        import com.amazon.device.messaging.ADMMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub

3. Adicione o seguinte código na classe que você criou. Lembre-se de substituir hub nome e conexão cadeia de caracteres (escutar):

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        private static NotificationHub hub;
        public static NotificationHub getNotificationHub(Context context) {
            Log.v("com.wa.hellokindlefire", "getNotificationHub");
            if (hub == null) {
                hub = new NotificationHub("[hub name]", "[listen connection string]", context);
            }
            return hub;
        }

        public MyADMMessageHandler() {
                super("MyADMMessageHandler");
            }

            public static class Receiver extends ADMMessageReceiver
            {
                public Receiver()
                {
                    super(MyADMMessageHandler.class);
                }
            }

            private void sendNotification(String msg) {
                Context ctx = getApplicationContext();

             mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

            NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                .setSmallIcon(R.mipmap.ic_launcher)
                .setContentTitle("Notification Hub Demo")
                .setStyle(new NotificationCompat.BigTextStyle()
                         .bigText(msg))
                .setContentText(msg);

            mBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }

4. Adicione o seguinte código para o `OnMessage()` método:

        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);

5. Adicione o seguinte código para o `OnRegistered` método:

            try {
        getNotificationHub(getApplicationContext()).register(registrationId);
            } catch (Exception e) {
        Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
            }

6.  Adicione o seguinte código para o `OnUnregistered` método:

            try {
                getNotificationHub(getApplicationContext()).unregister();
            } catch (Exception e) {
                Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
            }

7. No `MainActivity` método, adicione a seguinte instrução de importação:

        import com.amazon.device.messaging.ADM;

8. Adicione o seguinte código no final do `OnCreate` método:

        final ADM adm = new ADM(this);
        if (adm.getRegistrationId() == null)
        {
           adm.startRegister();
        } else {
            new AsyncTask() {
                  @Override
                  protected Object doInBackground(Object... params) {
                     try {                       MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
                     } catch (Exception e) {
                         Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
                         return e;
                     }
                     return null;
                 }
               }.execute(null, null, null);
        }

## <a name="add-your-api-key-to-your-app"></a>Adicionar a sua chave API ao seu aplicativo

1. No Eclipse, crie um novo arquivo chamado **api_key.txt** nos ativos de diretório do seu projeto.
2. Abra o arquivo e copie a chave de API geradas no portal do desenvolvedor do Amazon.

## <a name="run-the-app"></a>Executar o aplicativo

1. Inicie o emulador.
2. No emulador, passe o dedo da parte superior e clique em **configurações**, clique em **Minha conta** e registrar com uma conta Amazon válida.
3. No Eclipse, execute o aplicativo.

> [AZURE.NOTE] Se ocorrer um problema, verifique a hora do emulador (ou dispositivo). O valor de tempo deve ser preciso. Para alterar a hora do emulador dispositivos Kindle, você pode executar o seguinte comando do seu diretório de ferramentas da plataforma SDK Android:

        adb shell  date -s "yyyymmdd.hhmmss"

## <a name="send-a-message"></a>Enviar uma mensagem

Para enviar uma mensagem usando .NET:

        static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
        }

![][7]

<!-- URLs. -->
[Portal do desenvolvedor Amazon]: https://developer.amazon.com/home.html
[Baixe o SDK]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
