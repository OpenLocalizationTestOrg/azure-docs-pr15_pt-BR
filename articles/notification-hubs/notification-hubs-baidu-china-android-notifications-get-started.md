<properties
    pageTitle="Começar com Hubs de notificação do Azure usando Baidu | Microsoft Azure"
    description="Neste tutorial, você aprenderá a usar Hubs de notificação do Azure para notificações por push para dispositivos Android usando Baidu."
    services="notification-hubs"
    documentationCenter="android"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="mobile-baidu"
    ms.workload="mobile"
    ms.date="08/19/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-using-baidu"></a>Começar com Hubs de notificação usando Baidu

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Visão geral

Envio de nuvem Baidu é um serviço de nuvem chinês que você pode usar para enviar notificações por push para dispositivos móveis. Esse serviço é especialmente útil na China, onde entregar notificações por push para o Android é complexa devido a presença de lojas de diferentes aplicativos e serviços de envio, além da disponibilidade de dispositivos Android que normalmente não estão conectados a GCM (Google Cloud mensagens).

##<a name="prerequisites"></a>Pré-requisitos

Este tutorial requer o seguinte:

+ Android SDK (estamos supondo que você usará Eclipse), que você pode baixar do <a href="http://go.microsoft.com/fwlink/?LinkId=389797">site do Android</a>
+ [Serviços móveis Android SDK]
+ [Baidu Push Android SDK]

>[AZURE.NOTE] Para concluir este tutorial, você deve ter uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).


##<a name="create-a-baidu-account"></a>Criar uma conta de Baidu

Para usar Baidu, você deve ter uma conta de Baidu. Se você já tiver uma, faça logon no [portal de Baidu] e pule para a próxima etapa. Caso contrário, consulte as instruções abaixo sobre como criar uma conta de Baidu.  

1. Acesse o [portal de Baidu] e clique no link**登录**(**Login**). Clique em**立即注册**para iniciar o processo de registro de conta.

    ![][1]

2. Insira os detalhes necessários — telefone/código de verificação, senha e endereço de email — e clique em **inscrever**.

    ![][2]

3. Você receberá um email para o endereço de email que você inseriu com um link para ativar a sua conta de Baidu.

    ![][3]

4. Faça logon em sua conta de email, abra o email de ativação de Baidu e clique no link de ativação para ativar a sua conta de Baidu.

    ![][4]

Depois que você tiver uma conta de Baidu ativada, faça logon [portal de Baidu].

##<a name="register-as-a-baidu-developer"></a>Registre-se como um desenvolvedor de Baidu

1. Depois de fazer logon [portal de Baidu], clique em**更多 >>** (**mais**).

    ![][5]

2. Role para baixo na seção**站长与开发者服务 (para webmasters e serviços de desenvolvedor)** e clique em**百度开放云平台**(**Baidu abrir plataforma de nuvem**).

    ![][6]

3. Na próxima página, clique em**开发者服务**(**Serviços de desenvolvedor**) no canto superior direito.

    ![][7]

4. Na próxima página, clique em**注册开发者**(**Desenvolvedores registrado**) no menu no canto superior direito.

    ![][8]

5. Insira seu nome, uma descrição e um número de telefone celular para receber uma mensagem de texto de verificação e clique em**送验证码**(**Código de verificação de enviar**). Observe que, para números de telefone internacionais, é preciso colocar o código do país entre parênteses. Por exemplo, para um número de Estados Unidos, ele será **(1) 1234567890**.

    ![][9]

6. Em seguida, você deve receber uma mensagem de texto com um número de verificação, conforme mostrado no exemplo a seguir:

    ![][10]

7. Insira o número de verificação da mensagem no**验证码**(**código de confirmação**).

8. Por fim, conclua o registro de desenvolvedor aceitar o contrato de Baidu e clicando em**提交**(**Enviar**). Você verá a página seguinte na conclusão bem-sucedida de registro:

    ![][11]

##<a name="create-a-baidu-cloud-push-project"></a>Criar um projeto de envio de nuvem Baidu

Quando você cria um projeto de envio de nuvem Baidu, você receberá sua ID de aplicativo, chave API e chave secreta.

1. Depois de fazer logon [portal de Baidu], clique em**更多 >>** (**mais**).

    ![][5]

2. Role para baixo na seção**站长与开发者服务**(**para webmasters e serviços de desenvolvedor**) e clique em**百度开放云平台**(**Baidu abrir plataforma de nuvem**).

    ![][6]

3. Na próxima página, clique em**开发者服务**(**Serviços de desenvolvedor**) no canto superior direito.

    ![][7]

4. Na próxima página, clique em**云推送**(**Push de nuvem**) da seção**云服务**(**Serviços de nuvem**).

    ![][12]

5. Quando você for um desenvolvedor registrado, você verá**管理控制台**(**Console de gerenciamento**) no menu superior. Clique em**开发者服务管理**(**gerenciamento de serviços de desenvolvedores**).

    ![][13]

6. Na próxima página, clique em**创建工程**(**Criar projeto**).

    ![][14]

7. Insira um nome de aplicativo e clique em**创建**(**criar**).

    ![][15]

8. Durante a criação bem-sucedida de um projeto de envio de nuvem Baidu, você verá uma página com **AppID**, **Chave API**e **Chave secreta**. Anote a chave API e chave secreta, que usaremos posteriormente.

    ![][16]

9. Configure o projeto para notificações por push clicando em**云推送**(**Envio de nuvem**) no painel esquerdo.

    ![][31]

10. Na próxima página, clique no botão**推送设置**(**Push configurações**).

    ![][32]  

11. Na página de configuração, adicione o nome do pacote que você vai ser usando em seu projeto Android no campo**应用包名**(**pacote de aplicativos**) e clique em**保存设置**(**Salvar**).  

    ![][33]

Você verá o**保存成功!** (**salvas com êxito!**) mensagem.

##<a name="configure-your-notification-hub"></a>Configurar seu hub de notificação

1. Entre [Portal de clássico do Azure]e clique em **+ nova** na parte inferior da tela.

2. Clique em **Serviços de aplicativo**, clique em **Barramento de serviço**, clique **Hub de notificação**e, em seguida, clique em **Criar rápida**.

3. Forneça um nome para seu **Hub de notificação**, selecione a **região** e o **Namespace** onde este hub de notificação será criado e clique em **criar um novo Hub de notificação**.  

    ![][17]

4. Clique namespace em que você criou seu hub de notificação e, em seguida, clique em **Hubs de notificação** na parte superior.

    ![][18]

5. Selecione o hub de notificação que você criou e clique em **Configurar** no menu superior.

    ![][19]

6. Role até a seção **configurações de notificação de baidu** e insira a chave API e chave secreta que você obteve do console Baidu anteriormente para o seu projeto de envio de nuvem Baidu. Clique em **Salvar**.

    ![][20]

7. Clique na guia do **Painel de controle** na parte superior para o hub de notificação e clique em **Exibir cadeia de Conexão**.

    ![][21]

8. Tome nota dos **DefaultListenSharedAccessSignature** e **DefaultFullSharedAccessSignature** da janela de **informações de conexão do Access** .

    ![][22]

##<a name="connect-your-app-to-the-notification-hub"></a>Conectar seu aplicativo para o hub de notificação

1. No Eclipse ADT, crie um novo projeto Android (**arquivo** > **novo** > **Android projeto de aplicativo**).

    ![][23]

2. Insira um **Nome do aplicativo** e certifique-se de que a versão **Mínima necessária SDK** está definida como **API 16: Android 4.1**.

    ![][24]

3. Clique em **Avançar** e continue seguindo o assistente até que a janela **Criar atividade** é exibida. Certifique-se de que a **Atividade em branco** está selecionada e por fim, selecione **Concluir** para criar um novo aplicativo Android.

    ![][25]

4. Certifique-se de que o **Destino de compilação de projeto** está definida corretamente.

    ![][26]

5. Baixe o arquivo de notificação de hubs de 0.4.jar da guia **arquivos** da [Notificação-Hubs-Android-SDK em Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4). Adicione o arquivo para a pasta de **bibliotecas** do seu projeto Eclipse e atualize a pasta de *bibliotecas* .

6. Baixe e descompacte o [SDK do Android Push Baidu], abra a pasta de **bibliotecas** e copie o arquivo de jar **x.y.z pushservice** e o **armeabi** & **mips** pastas na pasta **bibliotecas** do seu aplicativo Android.

7. Abra o arquivo **AndroidManifest.xml** do seu projeto Android e adicione as permissões que são necessários para o SDK do Baidu.

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.WRITE_SETTINGS" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
        <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />

8. Adicione a propriedade **android: nome** para seu elemento de **aplicativo** em **AndroidManifest.xml**, substituindo *yourprojectname* (por exemplo, **com.example.BaiduTest**). Certifique-se de que esse nome de projeto coincida com o que você configurou no console de Baidu.

        <application android:name="yourprojectname.DemoApplication"

9. Adicione a seguinte configuração dentro do elemento de aplicativo após o **. MainActivity** elemento de atividade, substituindo *yourprojectname* (por exemplo, **com.example.BaiduTest**):

        <receiver android:name="yourprojectname.MyPushMessageReceiver">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
                <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
                <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
                <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>
        </receiver>

        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>

9. Adicione uma nova classe chamada **ConfigurationSettings.java** ao projeto.

    ![][28]

    ![][29]

10. Adicione o seguinte código para ele:

        public class ConfigurationSettings {
                public static String API_KEY = "...";
                public static String NotificationHubName = "...";
                public static String NotificationHubConnectionString = "...";
            }

    Defina o valor de **API_KEY** com o que você recuperou do Baidu nuvem projeto anteriormente, **NotificationHubName** com seu nome de hub de notificação do Portal de clássico do Azure e **NotificationHubConnectionString** com DefaultListenSharedAccessSignature a partir do Portal de clássico do Azure.

11. Adicionar uma nova classe chamada **DemoApplication.java**e adicione o seguinte código para ele:

        import com.baidu.frontia.FrontiaApplication;

        public class DemoApplication extends FrontiaApplication {
            @Override
            public void onCreate() {
                super.onCreate();
            }
        }

12. Adicione outra nova classe chamada **MyPushMessageReceiver.java**e adicione o código abaixo para ele. Esta é a classe que trata as notificações de envio que são recebidas do servidor de envio de Baidu.

        import java.util.List;
        import android.content.Context;
        import android.os.AsyncTask;
        import android.util.Log;
        import com.baidu.frontia.api.FrontiaPushMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub;

        public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
            /** TAG to Log */
            public static NotificationHub hub = null;
            public static String mChannelId, mUserId;
            public static final String TAG = MyPushMessageReceiver.class
                    .getSimpleName();

            @Override
            public void onBind(Context context, int errorCode, String appid,
                    String userId, String channelId, String requestId) {
                String responseString = "onBind errorCode=" + errorCode + " appid="
                        + appid + " userId=" + userId + " channelId=" + channelId
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
                mChannelId = channelId;
                mUserId = userId;

                try {
                 if (hub == null) {
                        hub = new NotificationHub(
                                ConfigurationSettings.NotificationHubName,
                                ConfigurationSettings.NotificationHubConnectionString,
                                context);
                        Log.i(TAG, "Notification hub initialized");
                    }
                } catch (Exception e) {
                   Log.e(TAG, e.getMessage());
                }

                registerWithNotificationHubs();
            }

            private void registerWithNotificationHubs() {
               new AsyncTask<Void, Void, Void>() {
                  @Override
                  protected Void doInBackground(Void... params) {
                     try {
                         hub.registerBaidu(mUserId, mChannelId);
                         Log.i(TAG, "Registered with Notification Hub - '"
                                + ConfigurationSettings.NotificationHubName + "'"
                                + " with UserId - '"
                                + mUserId + "' and Channel Id - '"
                                + mChannelId + "'");
                     } catch (Exception e) {
                         Log.e(TAG, e.getMessage());
                     }
                     return null;
                 }
               }.execute(null, null, null);
            }

            @Override
            public void onSetTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onSetTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onDelTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onDelTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onListTags(Context context, int errorCode, List<String> tags,
                    String requestId) {
                String responseString = "onListTags errorCode=" + errorCode + " tags="
                        + tags;
                Log.d(TAG, responseString);
            }

            @Override
            public void onUnbind(Context context, int errorCode, String requestId) {
                String responseString = "onUnbind errorCode=" + errorCode
                        + " requestId = " + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onNotificationClicked(Context context, String title,
                    String description, String customContentString) {
                String notifyString = "title=\"" + title + "\" description=\""
                        + description + "\" customContent=" + customContentString;
                Log.d(TAG, notifyString);
            }

            @Override
            public void onMessage(Context context, String message,
                    String customContentString) {
                String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
                Log.d(TAG, messageString);
            }
        }

13. Abra **MainActivity.java**e adicione o seguinte para o método **onCreate** :

            PushManager.startWork(getApplicationContext(),
                    PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

14. Abra as seguintes instruções de importação na parte superior:

            import com.baidu.android.pushservice.PushConstants;
            import com.baidu.android.pushservice.PushManager;

##<a name="send-notifications-to-your-app"></a>Enviar notificações para seu aplicativo


Você pode testar rapidamente a receber notificações em seu aplicativo enviando notificações no [Portal do Azure](https://portal.azure.com/) usando o botão **Testar enviar** no hub do notificação, conforme mostrado na tela abaixo.

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

Notificações por push normalmente são enviadas em um serviço de back-end como os serviços de celular ou ASP.NET usando uma biblioteca compatível. Você também pode usar a API REST diretamente para enviar mensagens de notificação se uma biblioteca não estiver disponível para seu back-end.

Neste tutorial, abordaremos mantenha a simplicidade e apenas demonstram teste seu aplicativo cliente enviando notificações usando o SDK do .NET para hubs de notificação em um aplicativo de console em vez de um serviço de back-end. Recomendamos o tutorial de [Hubs de notificação de uso para as notificações por push aos usuários](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) como a próxima etapa para enviar notificações de um back-end do ASP.NET. No entanto, as seguintes abordagens podem ser usadas para notificações de envio:

* **Interface REST**: você pode oferecer suporte a notificação em qualquer plataforma de back-end usando a [interface REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **SDK do Microsoft Azure notificação Hubs .NET**: no Nuget Package Manager para Visual Studio, executar o [Pacote de instalação Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node** : [como usar Hubs de notificação do Node](notification-hubs-nodejs-push-notification-tutorial.md).

* **Aplicativos Mobile**: para obter um exemplo de como enviar notificações de um back-end do Azure aplicativos de Mobile de serviço de aplicativo integrado com Hubs de notificação, consulte [Adicionar as notificações por push para o seu aplicativo móvel](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).

* **Java / PHP**: para obter um exemplo de como enviar notificações usando as APIs REST, consulte "Como usar Hubs de notificação do Java/PHP" ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

##<a name="optional-send-notifications-from-a-net-console-app"></a>(Opcional) Envie notificações a partir de um aplicativo de console do .NET.

Nesta seção, vamos mostrar enviar uma notificação usando um aplicativo de console do .NET.

1. Crie um novo aplicativo console Visual c#:

    ![][30]

2. Na janela do Console do Gerenciador de pacote, defina o **padrão de projeto** para seu novo projeto de aplicativo de console e, em seguida, na janela do console, execute o seguinte comando:

        Install-Package Microsoft.Azure.NotificationHubs

    Isso adiciona uma referência para o SDK de Hubs de notificação do Azure usando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacote Microsoft.Azure.Notification Hubs NuGet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

3. Abra o arquivo **Program.cs** e adicione o seguinte usando instrução:

        using Microsoft.Azure.NotificationHubs;

4. No seu `Program` classe, adicione o seguinte método e substituir *DefaultFullSharedAccessSignatureSASConnectionString* e *NotificationHubName* com os valores que você tem.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
            string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
            var result = await hub.SendBaiduNativeNotificationAsync(message);
        }

5. Adicione as seguintes linhas no seu método de **principal** :

         SendNotificationAsync();
         Console.ReadLine();

##<a name="test-your-app"></a>Testar seu aplicativo

Para testar esse aplicativo com um telefone real, apenas conecte o telefone ao seu computador usando um cabo USB. Isso carrega seu aplicativo para o telefone anexado.

Para testar esse aplicativo com o emulador, na barra de ferramentas superior do Eclipse, clique em **Executar**e, em seguida, selecione seu aplicativo. Isso inicia o emulador e, em seguida, carrega e executa o aplicativo.

O aplicativo recupera a 'ID' e 'channelId' de serviço de notificação por Baidu Push e registra com o hub de notificação.

Para enviar uma notificação de teste, você pode usar a guia depuração do Portal de clássico do Azure. Se você criou o aplicativo de console do .NET para Visual Studio, basta pressione a tecla F5 no Visual Studio para executar o aplicativo. O aplicativo envia uma notificação que será exibido na área de notificação superior do seu dispositivo ou emulador.


<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[Serviços móveis Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Baidu Push Android SDK]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Azure Portal clássico]: https://manage.windowsazure.com/
[Portal de Baidu]: http://www.baidu.com/
