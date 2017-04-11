<properties
    pageTitle="Enviando notificações por push para Android com Hubs de notificação do Azure | Microsoft Azure"
    description="Neste tutorial, você aprenderá a usar Hubs de notificação do Azure para notificações por push para dispositivos Android."
    services="notification-hubs"
    documentationCenter="android"
    keywords="notificações de envio, notificação de envio, notificação de envio android"
    authors="ysxu"
    manager="erikre"
    editor=""/>
<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="07/05/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-to-android-with-azure-notification-hubs"></a>Enviando notificações por push para Android com Hubs de notificação do Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Visão geral

> [AZURE.IMPORTANT] Este tópico demonstra notificações de envio com o Google Cloud mensagens (GCM). Se você estiver usando o sistema de mensagens de nuvem de Firebase (FCM do Google), consulte [Enviar as notificações por push para o Android com Hubs de notificação do Azure e FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

Este tutorial mostra como usar Hubs de notificação do Azure para enviar notificações por push para um aplicativo do Android.
Você vai criar um aplicativo Android em branco que recebe as notificações por push usando mensagens de nuvem do Google (GCM).

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

O código completo para este tutorial pode ser baixado do GitHub [aqui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted).


##<a name="prerequisites"></a>Pré-requisitos

> [AZURE.IMPORTANT] Para concluir este tutorial, você deve ter uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).

Além de uma conta do Azure active mencionada acima, este tutorial requer somente a versão mais recente do [Studio Android](http://go.microsoft.com/fwlink/?LinkId=389797).

Concluir este tutorial é um pré-requisito para todos os outros tutoriais de Hubs de notificação para aplicativos Android.

##<a name="creating-a-project-that-supports-google-cloud-messaging"></a>Criar um projeto que dá suporte a mensagens de nuvem do Google

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


##<a name="configure-a-new-notification-hub"></a>Configurar um novo hub de notificação


[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;6. na lâmina **configurações** , selecione **Os serviços de notificação** e **Google (GCM)**. Insira a chave de API e clique em **Salvar**.

&emsp;&emsp;![Notificação Azure Hubs - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Seu hub de notificação agora está configurado para trabalhar com GCM e você tiver as cadeias de caracteres de conexão para ambos registrar seu aplicativo para receber e enviar notificações por push.

##<a id="connecting-app"></a>Conectar seu aplicativo para o hub de notificação

###<a name="create-a-new-android-project"></a>Criar um novo projeto Android

1. No Android Studio, inicie um novo projeto de Studio Android.

    ![Android Studio - novo projeto][13]

2. Escolha o fator de formulário de **telefone e Tablet** e o **Mínimo SDK** que você deseja suportar. Clique em **Avançar**.

    ![Android Studio - fluxo de trabalho de criação de projeto][14]

3. Escolha **Atividade vazia** para a atividade principal, clique em **Avançar**e, em seguida, clique em **Concluir**.

###<a name="add-google-play-services-to-the-project"></a>Adicionar serviços do Google Play ao projeto

[AZURE.INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###<a name="adding-azure-notification-hubs-libraries"></a>Adicionando bibliotecas de Hubs de notificação do Azure


1. No `Build.Gradle` arquivo para o **aplicativo**, adicione as seguintes linhas na seção **dependências** .

        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

2. Adicione o seguinte repositório após a seção de **dependências** .

        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }

### <a name="updating-the-androidmanifestxml"></a>Atualizando o AndroidManifest.xml.


1. Para dar suporte GCM, devemos implementar um serviço de ouvinte ID da instância em nosso código que é usada para [obter tokens de registro](https://developers.google.com/cloud-messaging/android/client#sample-register) usando [API de ID de instância do Google](https://developers.google.com/instance-id/). Neste tutorial, podemos nomeará a classe `MyInstanceIDService`. 
 
    Adicione a seguinte definição de serviço para o arquivo de AndroidManifest.xml, dentro do `<application>` marca. Substituir o `<your package>` espaço reservado com o nome do pacote real mostrado na parte superior da `AndroidManifest.xml` arquivo.

        <service android:name="<your package>.MyInstanceIDService" android:exported="false">
            <intent-filter>
                <action android:name="com.google.android.gms.iid.InstanceID"/>
            </intent-filter>
        </service>


2. Depois que recebemos nosso token de registro GCM da instância ID API, usaremos-lo para [se registrar com o Hub de notificação do Azure](notification-hubs-push-notification-registration-management.md). Suportamos esse registro no plano de fundo usando um `IntentService` chamado `RegistrationIntentService`. Esse serviço também será responsável por [atualizar o nosso token de registro do GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).
 
    Adicione a seguinte definição de serviço para o arquivo de AndroidManifest.xml, dentro do `<application>` marca. Substituir o `<your package>` espaço reservado com o nome do pacote real mostrado na parte superior da `AndroidManifest.xml` arquivo. 

        <service
            android:name="<your package>.RegistrationIntentService"
            android:exported="false">
        </service>



3. Também será definimos um receptor para receber notificações. Adicione a seguinte definição de receptor ao arquivo AndroidManifest.xml, dentro do `<application>` marca. Substituir o `<your package>` espaço reservado com o nome do pacote real mostrado na parte superior da `AndroidManifest.xml` arquivo.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>



4. Adicione o seguinte GCM necessária relacionadas a permissões abaixo a `</application>` marca. Certifique-se de substituir `<your package>` com o nome do pacote mostrado na parte superior da `AndroidManifest.xml` arquivo.

    Para obter mais informações sobre essas permissões, consulte [Configurar um aplicativo cliente GCM para Android](https://developers.google.com/cloud-messaging/android/client#manifest).

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

        <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
        <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>


### <a name="adding-code"></a>Adicionar código


1. No modo de exibição de projeto, expanda **aplicativo** > **src** > **principal** > **java**. Sua pasta de pacote em **java**de atalho, clique em **novo**e, em seguida, clique em **Classe Java**. Adicionar uma nova classe denominada `NotificationSettings`. 

    ![Android Studio - nova classe Java][6]

    Certifique-se de atualizar esses três espaços reservados no código a seguir para o `NotificationSettings` classe:
    * **SenderId**: O número do projeto que você obteve anteriormente no [Console de nuvem do Google](http://cloud.google.com/console).
    * **HubListenConnectionString**: A cadeia de conexão **DefaultListenAccessSignature** para seu hub. Você pode copiar essa cadeia de caracteres de conexão clicando em **Políticas de acesso** no blade **configurações** do seu hub no [Portal do Azure].
    * **HubName**: Use o nome do seu hub de notificação que aparece na lâmina hub no [Portal do Azure].

    `NotificationSettings`código:

        public class NotificationSettings {
            public static String SenderId = "<Your project number>";
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Your default listen connection string>";
        }

2. Usando as etapas acima, adicione outra classe nova chamada `MyInstanceIDService`. Este será nossa implementação de serviço de ouvinte de ID da instância.

    O código para esta classe chamará nosso `IntentService` [o token GCM de atualização](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) em segundo plano.

        import android.content.Intent;
        import android.util.Log;
        import com.google.android.gms.iid.InstanceIDListenerService;
        
        
        public class MyInstanceIDService extends InstanceIDListenerService {
        
            private static final String TAG = "MyInstanceIDService";
        
            @Override
            public void onTokenRefresh() {
        
                Log.i(TAG, "Refreshing GCM Registration Token");
        
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        };


3. Adicionar uma nova classe ao seu projeto nomeado, `RegistrationIntentService`. Esta será a implementação para nosso `IntentService` que tratará [Atualizando o token GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) e [registrar com o hub de notificação](notification-hubs-push-notification-registration-management.md).

    Use o seguinte código para esta classe.

        import android.app.IntentService;
        import android.content.Intent;
        import android.content.SharedPreferences;
        import android.preference.PreferenceManager;
        import android.util.Log;
        
        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.google.android.gms.iid.InstanceID;
        import com.microsoft.windowsazure.messaging.NotificationHub;
        
        public class RegistrationIntentService extends IntentService {
        
            private static final String TAG = "RegIntentService";
        
            private NotificationHub hub;
        
            public RegistrationIntentService() {
                super(TAG);
            }
        
            @Override
            protected void onHandleIntent(Intent intent) {      
                SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
                String resultString = null;
                String regID = null;
        
                try {
                    InstanceID instanceID = InstanceID.getInstance(this);
                    String token = instanceID.getToken(NotificationSettings.SenderId,
                            GoogleCloudMessaging.INSTANCE_ID_SCOPE);        
                    Log.i(TAG, "Got GCM Registration Token: " + token);
        
                    // Storing the registration id that indicates whether the generated token has been
                    // sent to your server. If it is not stored, send the token to your server,
                    // otherwise your server should have already received the token.
                    if ((regID=sharedPreferences.getString("registrationID", null)) == null) {      
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.i(TAG, "Attempting to register with NH using token : " + token);

                        regID = hub.register(token).getRegistrationId();

                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1", "tag2").getRegistrationId();

                        resultString = "Registered Successfully - RegId : " + regID;
                        Log.i(TAG, resultString);       
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                    } else {
                        resultString = "Previously Registered Successfully - RegId : " + regID;
                    }
                } catch (Exception e) {
                    Log.e(TAG, resultString="Failed to complete token refresh", e);
                    // If an exception happens while fetching the new token or updating our registration data
                    // on a third-party server, this ensures that we'll attempt the update at a later time.
                }
        
                // Notify UI that registration has completed.
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(resultString);
                }
            }
        }


        
4. No seu `MainActivity` classe, adicione o seguinte `import` instruções acima da declaração de classe.

        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.google.android.gms.gcm.*;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;

5. Adicione os seguintes membros particular na parte superior da classe. Usaremos essas [verifica a disponibilidade de Google Play Services conforme recomendado pelo Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private GoogleCloudMessaging gcm;
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;

6. No seu `MainActivity` classe, adicione o seguinte método para a disponibilidade do Google Play Services. 

        /**
         * Check the device to make sure it has the Google Play Services APK. If
         * it doesn't, display a dialog that allows users to download the APK from
         * the Google Play Store or enable it in the device's system settings.
         */
        private boolean checkPlayServices() {
            GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
            int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
            if (resultCode != ConnectionResult.SUCCESS) {
                if (apiAvailability.isUserResolvableError(resultCode)) {
                    apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                            .show();
                } else {
                    Log.i(TAG, "This device is not supported by Google Play Services.");
                    ToastNotify("This device is not supported by Google Play Services.");
                    finish();
                }
                return false;
            }
            return true;
        }


7. No seu `MainActivity` classe, adicione o seguinte código verificará Google Play Services antes de chamar seu `IntentService` para obter o seu registro GCM token e registrar seu hub de notificação.

        public void registerWithNotificationHubs()
        {
            Log.i(TAG, " Registering with Notification Hubs");
    
            if (checkPlayServices()) {
                // Start IntentService to register this application with GCM.
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        }


8. No `OnCreate` método do `MainActivity` classe, adicione o seguinte código para iniciar o processo de registro quando atividade é criada.

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }


9. Adicione esses métodos adicionais para o `MainActivity` para verificar o status de estado e relatório do aplicativo em seu aplicativo.

        @Override
        protected void onStart() {
            super.onStart();
            isVisible = true;
        }
    
        @Override
        protected void onPause() {
            super.onPause();
            isVisible = false;
        }
    
        @Override
        protected void onResume() {
            super.onResume();
            isVisible = true;
        }
    
        @Override
        protected void onStop() {
            super.onStop();
            isVisible = false;
        }
    
        public void ToastNotify(final String notificationMessage) {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                    TextView helloText = (TextView) findViewById(R.id.text_hello);
                    helloText.setText(notificationMessage);
                }
            });
        }


10. O `ToastNotify` método usa *"Olá, mundo"* `TextView` controle ao relatório de status e notificações constantemente no aplicativo. No layout activity_main.xml, adicione a seguinte identificação para o controle.

        android:id="@+id/text_hello"

11. Em seguida, adicionaremos uma subclasse para nosso receptor que definimos na AndroidManifest.xml. Adicionar uma nova classe ao seu projeto chamado `MyHandler`.

12. Adicione as seguintes instruções de importação na parte superior da `MyHandler.java`:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;

13. Adicione o seguinte código para o `MyHandler` classe tornando uma subclasse de `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Esse código substitui o `OnReceive` método, para que o manipulador informará notificações recebidas. O manipulador também envia a notificação de envio para o Gerenciador de notificação Android usando o `sendNotification()` método. O `sendNotification()` método deve ser executado quando o aplicativo não está sendo executado e uma notificação for recebida.

        public class MyHandler extends NotificationsHandler {
            public static final int NOTIFICATION_ID = 1;
            private NotificationManager mNotificationManager;
            NotificationCompat.Builder builder;
            Context ctx;
        
            @Override
            public void onReceive(Context context, Bundle bundle) {
                ctx = context;
                String nhMessage = bundle.getString("message");
                sendNotification(nhMessage);
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(nhMessage);
                }
            }
        
            private void sendNotification(String msg) {
        
                Intent intent = new Intent(ctx, MainActivity.class);
                intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
        
                mNotificationManager = (NotificationManager)
                        ctx.getSystemService(Context.NOTIFICATION_SERVICE);
        
                PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                        intent, PendingIntent.FLAG_ONE_SHOT);
        
                Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
                NotificationCompat.Builder mBuilder =
                        new NotificationCompat.Builder(ctx)
                                .setSmallIcon(R.mipmap.ic_launcher)
                                .setContentTitle("Notification Hub Demo")
                                .setStyle(new NotificationCompat.BigTextStyle()
                                        .bigText(msg))
                                .setSound(defaultSoundUri)
                                .setContentText(msg);
        
                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }
        }


14. No Android Studio na barra de menus, clique em **Compilar** > **Recriar o projeto** para certificar-se de que não há erros estão presentes em seu código.

##<a name="sending-push-notifications"></a>Enviar notificações por push

Você pode testar a receber notificações por push em seu aplicativo enviando-lhes através do [Portal do Azure] - aparência para a seção de **solução de problemas** na lâmina hub, conforme mostrado abaixo.

![Enviar notificação Azure Hubs - teste](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="optional-send-push-notifications-directly-from-the-app"></a>(Opcional) Enviar notificações por push diretamente do aplicativo

Normalmente, você poderia enviar notificações usando um servidor back-end. Em alguns casos, você talvez queira possam enviar notificações por push diretamente do aplicativo cliente. Esta seção explica como enviar notificações do cliente usando a [API REST do Azure notificação Hub](https://msdn.microsoft.com/library/azure/dn223264.aspx).

1. No modo de exibição de projeto de Studio Android, expanda o **aplicativo** > **src** > **principal** > **res** > **layout**. Abrir o `activity_main.xml` arquivo de layout e clique em **texto** da guia para atualizar o conteúdo de texto do arquivo. Atualizá-lo com o código abaixo, que adiciona novos `Button` e `EditText` controles para enviar mensagens de notificação de envio para o hub de notificação. Adicione este código na parte inferior, logo antes `</RelativeLayout>`.

        <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

        <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. No modo de exibição de projeto de Studio Android, expanda o **aplicativo** > **src** > **principal** > **res** > **valores**. Abrir o `strings.xml` de arquivo e adicione os valores de cadeia de caracteres que são referenciados pela nova `Button` e `EditText` controles. Adicione esses na parte inferior do arquivo, logo antes `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. No seu `NotificationSetting.java` arquivo, adicione a seguinte configuração para o `NotificationSettings` classe.

    Atualização `HubFullAccess` com a cadeia de conexão **DefaultFullSharedAccessSignature** para seu hub. Essa cadeia de caracteres de conexão pode ser copiada a partir do [Portal Azure] clicando em **Políticas de acesso** no blade **configurações** para o seu hub de notificação.

        public static String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

4. No seu `MainActivity.java` arquivo, adicione o seguinte `import` instruções acima a `MainActivity` classe.

        import java.io.BufferedOutputStream;
        import java.io.BufferedReader;
        import java.io.InputStreamReader;
        import java.io.OutputStream;
        import java.net.HttpURLConnection;
        import java.net.URL;
        import java.net.URLEncoder;
        import javax.crypto.Mac;
        import javax.crypto.spec.SecretKeySpec;
        import android.util.Base64;
        import android.view.View;
        import android.widget.EditText;

6. No seu `MainActivity.java` arquivo, adicione os seguintes membros na parte superior da `MainActivity` classe.  

        private String HubEndpoint = null;
        private String HubSasKeyName = null;
        private String HubSasKeyValue = null;

6. Você deve criar um token de assinatura de acesso de Software (SaS) para autenticar uma solicitação POST para enviar mensagens para o seu hub de notificação. Isso é feito ao analisar os dados da chave da cadeia de conexão e criando o token de SaS, como mencionado na Referência API REST [Conceitos comuns](http://msdn.microsoft.com/library/azure/dn495627.aspx) . O código a seguir é um exemplo de implementação.

    Em `MainActivity.java`, adicione o seguinte método para o `MainActivity` classe para analisar a cadeia de caracteres de conexão.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
         * to parse the connection string so a SaS authentication token can be
         * constructed.
         *
         * @param connectionString This must be the DefaultFullSharedAccess connection
         *                         string for this example.
         */
        private void ParseConnectionString(String connectionString)
        {
            String[] parts = connectionString.split(";");
            if (parts.length != 3)
                throw new RuntimeException("Error parsing connection string: "
                        + connectionString);
    
            for (int i = 0; i < parts.length; i++) {
                if (parts[i].startsWith("Endpoint")) {
                    this.HubEndpoint = "https" + parts[i].substring(11);
                } else if (parts[i].startsWith("SharedAccessKeyName")) {
                    this.HubSasKeyName = parts[i].substring(20);
                } else if (parts[i].startsWith("SharedAccessKey")) {
                    this.HubSasKeyValue = parts[i].substring(16);
                }
            }
        }


7. Em `MainActivity.java`, adicione o seguinte método para o `MainActivity` classe para criar um token de autenticação SaS.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         *
         * @param uri The unencoded resource URI string for this operation. The resource
         *            URI is the full URI of the Service Bus resource to which access is
         *            claimed. For example,
         *            "http://<namespace>.servicebus.windows.net/<hubName>"
         */
        private String generateSasToken(String uri) {
    
            String targetUri;
            String token = null;
            try {
                targetUri = URLEncoder
                        .encode(uri.toString().toLowerCase(), "UTF-8")
                        .toLowerCase();
    
                long expiresOnDate = System.currentTimeMillis();
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60 * 1000;
                long expires = expiresOnDate / 1000;
                String toSign = targetUri + "\n" + expires;
    
                // Get an hmac_sha1 key from the raw key bytes
                byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
                SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
    
                // Get an hmac_sha1 Mac instance and initialize with the signing key
                Mac mac = Mac.getInstance("HmacSHA256");
                mac.init(signingKey);
    
                // Compute the hmac on input data bytes
                byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
    
                // Using android.util.Base64 for Android Studio instead of
                // Apache commons codec
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
    
                // Construct authorization string
                token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
            } catch (Exception e) {
                if (isVisible) {
                    ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
                }
            }
    
            return token;
        }



8. Em `MainActivity.java`, adicione o seguinte método para o `MainActivity` classe para lidar com o clique do botão **Enviar notificação** e enviar a mensagem de notificação por push para o hub usando a API REST interno.

        /**
         * Send Notification button click handler. This method parses the
         * DefaultFullSharedAccess connection string and generates a SaS token. The
         * token is added to the Authorization header on the POST request to the
         * notification hub. The text in the editTextNotificationMessage control
         * is added as the JSON body for the request to add a GCM message to the hub.
         *
         * @param v
         */
        public void sendNotificationButtonOnClick(View v) {
            EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
            final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";
    
            new Thread()
            {
                public void run()
                {
                    try
                    {
                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(NotificationSettings.HubFullAccess);
                        URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                                "/messages/?api-version=2015-01");
    
                        HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
    
                        try {
                            // POST request
                            urlConnection.setDoOutput(true);
    
                            // Authenticate the POST request with the SaS token
                            urlConnection.setRequestProperty("Authorization", 
                                generateSasToken(url.toString()));
    
                            // Notification format should be GCM
                            urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");
    
                            // Include any tags
                            // Example below targets 3 specific tags
                            // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                            // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
                            //      "tag1 || tag2 || tag3");
    
                            // Send notification message
                            urlConnection.setFixedLengthStreamingMode(json.length());
                            OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                            bodyStream.write(json.getBytes());
                            bodyStream.close();
    
                            // Get reponse
                            urlConnection.connect();
                            int responseCode = urlConnection.getResponseCode();
                            if ((responseCode != 200) && (responseCode != 201)) {
                                BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                                String line;
                                StringBuilder builder = new StringBuilder("Send Notification returned " +
                                        responseCode + " : ")  ;
                                while ((line = br.readLine()) != null) {
                                    builder.append(line);
                                }

                                ToastNotify(builder.toString());
                            }
                        } finally {
                            urlConnection.disconnect();
                        }
                    }
                    catch(Exception e)
                    {
                        if (isVisible) {
                            ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                        }
                    }
                }
            }.start();
        }



##<a name="testing-your-app"></a>Teste seu aplicativo

####<a name="push-notifications-in-the-emulator"></a>Notificações de envio no emulador

Se você quiser testar as notificações por push dentro de um emulador, certifique-se de que sua imagem de emulador compatível com o nível de API do Google que você escolheu para o aplicativo. Se sua imagem não dá suporte a APIs nativas do Google, você terminarão com o **SERVICE\_não\_disponível** exceção.

Além dos acima, certifique-se de que você adicionou a conta do Google para o emulador em execução em **configurações** > **contas**. Caso contrário, suas tentativas de registrar GCM podem resultar na **autenticação\_falha** exceção.

####<a name="running-the-application"></a>Executando o aplicativo

1. Executar o aplicativo e observe que a identificação de registro é relatada para um registro bem-sucedido.

    ![Teste no Android - registro de canal][18]

2. Insira uma mensagem de notificação sejam enviadas para todos os dispositivos Android que foram registrados com o hub.

    ![Teste no Android - enviar uma mensagem][19]

3. Pressione **Enviar notificação**. Todos os dispositivos que tem o aplicativo executando mostrará um `AlertDialog` instância com a mensagem de notificação de envio. Dispositivos que não tem o aplicativo em execução, mas anteriormente foram registrados para notificações por push receberão uma notificação no Gerenciador de notificação do Android. Aqueles podem ser vistas passando o dedo para baixo no canto superior esquerdo.

    ![Teste no Android - notificações][21]

##<a name="next-steps"></a>Próximas etapas

Recomendamos o tutorial de [Hubs de notificação de uso para as notificações por push aos usuários] como a próxima etapa. Ele mostrará como enviar notificações de um back-end do ASP.NET usando marcas para usuários específicos de destino.

Se você quiser segmento seus usuários por grupos de interesse, confira o tutorial de [Hubs de notificação de uso para enviar as últimas notícias] .

Para obter informações mais gerais sobre Hubs de notificação, consulte nossa [Orientação de Hubs de notificação].

<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png

[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png


<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Classic Portal]: https://manage.windowsazure.com/
[Orientação de Hubs de notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Use Hubs de notificação para notificações de envio para os usuários]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Hubs de notificação para enviar últimas notícias]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Portal do Azure]: https://portal.azure.com
