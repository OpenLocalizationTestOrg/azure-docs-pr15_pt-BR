<properties
    pageTitle="Enviando notificações de envio seguro com Hubs de notificação do Azure"
    description="Saiba como enviar notificações por push seguro para um aplicativo do Android do Azure. Exemplos de código escritos em Java e c#."
    documentationCenter="android"
    keywords="notificação, as notificações de envio por push push mensagens, notificações por push android"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

#<a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Enviando notificações de envio seguro com Hubs de notificação do Azure

> [AZURE.SELECTOR]
- [Universal do Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

##<a name="overview"></a>Visão geral

> [AZURE.IMPORTANT] Para concluir este tutorial, você deve ter uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Suporte de notificação de envio no Microsoft Azure permite que você acesse uma infraestrutura de mensagem de envio fácil de usar várias plataformas, dimensionada-out, que simplifica a implementação de notificações de envio para aplicativos do consumidor e empresa para plataformas móveis.

Devido a regulamentares ou restrições de segurança, às vezes, um aplicativo talvez queira incluir algo na notificação que não pode ser transmitida por meio de infraestrutura de notificação do envio padrão. Este tutorial descreve como atingir a mesma experiência enviando informações confidenciais por meio de uma conexão segura, autenticada entre o dispositivo Android do cliente e o back-end do aplicativo.

Em um alto nível, o fluxo é da seguinte maneira:

1. O back-end do aplicativo:
    - Carga de segurança de lojas no banco de dados back-end.
    - Envia a ID desta notificação no dispositivo Android (nenhuma informação segura é enviada).
2. O aplicativo do dispositivo, quando receber a notificação:
    - O dispositivo Android back-end solicitando a carga de segurança de contatos.
    - O aplicativo pode mostrar a carga como uma notificação no dispositivo.

É importante observar que no fluxo de anterior (e neste tutorial), vamos supor que o dispositivo armazena um token de autenticação em armazenamento local, depois que o usuário faz logon. Isso garante uma experiência perfeita completamente, conforme o dispositivo pode recuperar a carga de segurança da notificação usando este token. Se seu aplicativo não armazene tokens de autenticação do dispositivo, ou se esses tokens podem ser expirados, o aplicativo de dispositivo, após receber a notificação de envio deverá exibir uma notificação genérica solicitando ao usuário para iniciar o aplicativo. Em seguida, o aplicativo autentica o usuário e mostra a carga de notificação.

Este tutorial mostra como enviar notificações por push seguro. Ele se baseia no tutorial [Notificar usuários](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) , portanto você deve concluir as etapas desse tutorial primeiro se você ainda não fez isso.

> [AZURE.NOTE] Este tutorial supõe que você criou e configurado seu hub de notificação, conforme descrito na [Introdução com Hubs de notificação (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Modificar o projeto Android

Agora que você modificou seu aplicativo back-end para enviar apenas a *identificação* de uma notificação de envio, você precisa alterar seu aplicativo do Android para lidar com essa notificação e seu back-end para recuperar a mensagem segura para ser exibido de retorno de chamada.
Para atingir esse objetivo, você precisa certificar-se de que seu aplicativo do Android Saiba como autenticar com seu back-end quando ele recebe as notificações de envio.

Agora vamos modificar o fluxo de *logon* para salvar o valor de cabeçalho de autenticação nas preferências do aplicativo compartilhadas. Mecanismos de análogos podem ser usados para armazenar qualquer token de autenticação (por exemplo, OAuth tokens) que o aplicativo terá que usar sem exigir credenciais de usuário.

1. No seu projeto de aplicativo do Android, adicione as seguintes constantes na parte superior da classe **MainActivity** :

        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";

2. Ainda na classe **MainActivity** , atualize o `getAuthorizationHeader()` método para conter o seguinte código:

        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

            return basicAuthHeader;
        }

3. Adicione o seguinte `import` instruções na parte superior do arquivo **MainActivity** :

        import android.content.SharedPreferences;

Agora, podemos alterará o manipulador que é chamado quando a notificação é recebida.

4. No **MyHandler** classe alterar o `OnReceive()` método para conter:

        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }

5. Adicione o `retrieveNotification()` método, substituindo o espaço reservado `{back-end endpoint}` com o ponto de extremidade de back-end obtido ao implantar seu back-end:

        private void retrieveNotification(final String secureMessageId) {
            SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                        request.addHeader("Authorization", "Basic "+authorizationHeader);
                        HttpResponse response = new DefaultHttpClient().execute(request);
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                            throw new RuntimeException("Error retrieving secure notification");
                        }
                        String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                        JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                        sendNotification(secureNotification.getString("Payload"));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
        }


Este método chama o seu aplicativo back-end para recuperar o conteúdo de notificação usando as credenciais armazenadas nas preferências de compartilhado e exibe como uma notificação normal. Procura a notificação para o usuário do aplicativo exatamente como qualquer outra notificação de envio.

Observe que é preferível para tratar de casos de propriedade de cabeçalho de autenticação ausente ou rejeição pelo back-end. A manipulação específica desses casos dependem principalmente a experiência do usuário de destino. É uma opção Exibir uma notificação com uma instrução genérica do usuário autenticar para recuperar a notificação real.

## <a name="run-the-application"></a>Executar o aplicativo

Para executar o aplicativo, faça o seguinte:

1. Certifique-se de **que appbackend** é implantado no Azure. Se usando o Visual Studio, execute o aplicativo de Web API **AppBackend** . Uma página da web é exibida.

2. No Eclipse, execute o aplicativo em um dispositivo Android físico ou o emulador.

3. No aplicativo do Android interface do usuário, digite um nome de usuário e senha. Eles podem ser qualquer cadeia de caracteres, mas eles devem ser o mesmo valor.

4. No aplicativo do Android interface do usuário, clique em **login**. Clique em **Enviar por push**.
