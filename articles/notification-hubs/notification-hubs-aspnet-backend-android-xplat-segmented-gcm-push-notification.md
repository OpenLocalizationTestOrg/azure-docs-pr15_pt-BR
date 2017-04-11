<properties
    pageTitle="Hubs de notificação últimas notícias Tutorial - Android"
    description="Saiba como usar Hubs de notificação do Azure serviço barramento enviar notificações de notícias ruptura para dispositivos Android."
    services="notification-hubs"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>


# <a name="use-notification-hubs-to-send-breaking-news"></a>Use Hubs de notificação para enviar últimas notícias

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

##<a name="overview"></a>Visão geral

Este tópico mostra como usar Hubs de notificação do Azure para transmitir notificações de notícias recentes para um aplicativo do Android. Ao concluir, você poderá registrar para dividir categorias de notícias que você está interessado e, receber apenas as notificações por push para essas categorias. Esse cenário é um padrão comum para muitos aplicativos onde notificações têm sejam enviadas aos grupos de usuários que têm declarados previamente juros nelas, por exemplo, leitor RSS, aplicativos para ventiladores de música, etc.

Cenários de difusão são habilitados, incluindo uma ou mais _marcas_ ao criar um registro no hub de notificação. Quando as notificações são enviadas para uma marca, todos os dispositivos que foram registrados para a marca receberão a notificação. Como marcas são simplesmente cadeias de caracteres, eles não precisam ser provisionados com antecedência. Para obter mais informações sobre marcas, consulte [expressões de marca e roteamento de Hubs de notificação](notification-hubs-tags-segment-push-message.md).


##<a name="prerequisites"></a>Pré-requisitos

Este tópico se baseia o aplicativo que você criou na [Introdução com Hubs de notificação][get-started]. Antes de iniciar este tutorial, você deve já concluiu [começar com Hubs de notificação][get-started].

##<a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categoria para o aplicativo

A primeira etapa é adicionar os elementos de interface do usuário a sua atividade principal existente que permitem que o usuário selecione categorias para registrar. As categorias selecionadas por um usuário são armazenadas no dispositivo. Quando o aplicativo for iniciado, um registro de dispositivo é criado no seu hub de notificação com as categorias selecionadas como marcas.

1. Abra o arquivo res/layout/activity_main.xml e substitua o conteúdo com o seguinte:

        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            tools:context="com.example.breakingnews.MainActivity"
            android:orientation="vertical">

                <CheckBox
                    android:id="@+id/worldBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_world" />
                <CheckBox
                    android:id="@+id/politicsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_politics" />
                <CheckBox
                    android:id="@+id/businessBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_business" />
                <CheckBox
                    android:id="@+id/technologyBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_technology" />
                <CheckBox
                    android:id="@+id/scienceBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_science" />
                <CheckBox
                    android:id="@+id/sportsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_sports" />
                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:onClick="subscribe"
                    android:text="@string/button_subscribe" />
        </LinearLayout>

2. Abra o arquivo res/values/strings.xml e adicione as seguintes linhas:

        <string name="button_subscribe">Subscribe</string>
        <string name="label_world">World</string>
        <string name="label_politics">Politics</string>
        <string name="label_business">Business</string>
        <string name="label_technology">Technology</string>
        <string name="label_science">Science</string>
        <string name="label_sports">Sports</string>

    O layout de gráfico main_activity.xml deve agora esta aparência:

    ![][A1]

3. Agora crie uma classe **notificações** no mesmo pacote como sua classe **MainActivity** .

        import java.util.HashSet;
        import java.util.Set;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.os.AsyncTask;
        import android.util.Log;
        import android.widget.Toast;

        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.microsoft.windowsazure.messaging.NotificationHub;

        public class Notifications {
            private static final String PREFS_NAME = "BreakingNewsCategories";
            private GoogleCloudMessaging gcm;
            private NotificationHub hub;
            private Context context;
            private String senderId;

            public Notifications(Context context, String senderId, String hubName, 
                                    String listenConnectionString) {
                this.context = context;
                this.senderId = senderId;
        
                gcm = GoogleCloudMessaging.getInstance(context);
                hub = new NotificationHub(hubName, listenConnectionString, context);
            }

            public void storeCategoriesAndSubscribe(Set<String> categories)
            {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                settings.edit().putStringSet("categories", categories).commit();
                subscribeToCategories(categories);
            }

            public Set<String> retrieveCategories() {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                return settings.getStringSet("categories", new HashSet<String>());
            }

            public void subscribeToCategories(final Set<String> categories) {
                new AsyncTask<Object, Object, Object>() {
                    @Override
                    protected Object doInBackground(Object... params) {
                        try {
                            String regid = gcm.register(senderId);
        
                            String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
        
                            hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM, 
                                categories.toArray(new String[categories.size()]));
                        } catch (Exception e) {
                            Log.e("MainActivity", "Failed to register - " + e.getMessage());
                            return e;
                        }
                        return null;
                    }
        
                    protected void onPostExecute(Object result) {
                        String message = "Subscribed for categories: "
                                + categories.toString();
                        Toast.makeText(context, message,
                                Toast.LENGTH_LONG).show();
                    }
                }.execute(null, null, null);
            }

        }

    Essa classe usa o armazenamento local para armazenar as categorias de notícias que tem esse dispositivo para receber. Ele também contém métodos para registrar essas categorias.


4. Em sua classe **MainActivity** remover seus campos particulares para **NotificationHub** e **GoogleCloudMessaging**e adicione um campo para **notificações**:

        // private GoogleCloudMessaging gcm;
        // private NotificationHub hub;
        private Notifications notifications;

5. Em seguida, no método **onCreate** , remova a inicialização do campo **hub** e o método **registerWithNotificationHubs** . Em seguida, adicione as seguintes linhas que inicializar uma instância da classe **notificações** . 


        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            MyHandler.mainActivity = this;
    
            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);
    
            notifications = new Notifications(this, SENDER_ID, HubName, HubListenConnectionString);
    
            notifications.subscribeToCategories(notifications.retrieveCategories());
        }

    `HubName`e `HubListenConnectionString` já deve ser definida com a `<hub name>` e `<connection string with listen access>` espaços reservados com seu nome de hub de notificação e a cadeia de conexão para *DefaultListenSharedAccessSignature* obtido anteriormente.

    > [AZURE.NOTE] Como as credenciais que são distribuídas com um aplicativo cliente não são geralmente seguras, você só deve distribuir a chave para escutar acesso com seu aplicativo cliente. Ouça permite que o access não pode ser modificado seu aplicativo para registrar para notificações, mas os registros existentes e as notificações não podem ser enviadas. A tecla de acesso total é usada em um serviço de back-end segura para enviar notificações e alterar os registros existentes.


6. Em seguida, adicione as seguintes importações e `subscribe` método para lidar com o botão inscrever em evento:
        
        import android.widget.CheckBox;
        import java.util.HashSet;
        import java.util.Set;

        public void subscribe(View sender) {
            final Set<String> categories = new HashSet<String>();

            CheckBox world = (CheckBox) findViewById(R.id.worldBox);
            if (world.isChecked())
                categories.add("world");
            CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
            if (politics.isChecked())
                categories.add("politics");
            CheckBox business = (CheckBox) findViewById(R.id.businessBox);
            if (business.isChecked())
                categories.add("business");
            CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
            if (technology.isChecked())
                categories.add("technology");
            CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
            if (science.isChecked())
                categories.add("science");
            CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
            if (sports.isChecked())
                categories.add("sports");

            notifications.storeCategoriesAndSubscribe(categories);
        }

    Este método cria uma lista de categorias e usa a classe de **notificações** para armazenar a lista no armazenamento local e registrar as marcas correspondentes com seu hub de notificação. Quando as categorias são alteradas, o registro será recriado com as novas categorias.

Seu aplicativo agora é possível armazenar um conjunto de categorias no armazenamento local do dispositivo e registrar com o hub de notificação sempre que o usuário altera a seleção de categorias.

##<a name="register-for-notifications"></a>Registre-se para notificações

Estas etapas registram com o hub de notificação na inicialização usando as categorias que foram armazenadas no armazenamento local.

> [AZURE.NOTE] Como o registrationId atribuído pelo Google Cloud mensagens (GCM) pode alterar a qualquer momento, você deve registrar para notificações frequentemente evitar falhas de notificação. Este exemplo registra para notificação sempre que o aplicativo é iniciado. Para os aplicativos que são executados com frequência, mais de uma vez por dia, você pode provavelmente ignorar registro para preservar a largura de banda se menos de um dia passou desde o registro anterior.


1. Adicione o seguinte código no final do método **onCreate** na classe **MainActivity** :

        notifications.subscribeToCategories(notifications.retrieveCategories());

    Isso garante que os que sempre que o aplicativo é iniciado, ele recupera as categorias de armazenamento local e solicita um registeration para estas categorias. 

2. Em seguida, atualize o `onStart()` método do `MainActivity` classe da seguinte maneira:

    @Overrideprotegido anular onStart() {super.onStart();      isVisible = verdadeiro;

        Set<String> categories = notifications.retrieveCategories();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }

    Isso atualiza a atividade principal baseada no status das categorias salvas anteriormente.

O aplicativo está concluído e pode armazenar um conjunto de categorias no armazenamento local dispositivo usado para registrar com o hub de notificação sempre que o usuário altera a seleção de categorias. Em seguida, podemos definirá um back-end que pode enviar notificações de categoria para este aplicativo.

##<a name="sending-tagged-notifications"></a>Enviar notificações marcadas

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##<a name="run-the-app-and-generate-notifications"></a>Executar o aplicativo e gerar notificações

1. No Android Studio, criar o aplicativo e inicie-o em um dispositivo ou emulador.

    Observe que o aplicativo de interface do usuário fornece um conjunto de alterna que permite escolher as categorias de assinar.

2. Habilitar um ou mais alterna de categorias e, em seguida, clique em **assinar**.

    O aplicativo converte categorias selecionadas em marcas e solicita um novo registro de dispositivo para as marcas selecionadas do hub notificação. As categorias registradas são retornadas e exibidas em uma notificação do sistema.

4. Envie uma notificação de novo executando o aplicativo de Console do .NET.  Como alternativa, você pode enviar notificações de modelo marcadas usando a guia de depuração de seu hub de notificação no [Portal de clássico do Azure].

    Notificações para as categorias selecionadas aparecem como notificações do sistema.

##<a name="next-steps"></a>Próximas etapas

Neste tutorial aprendemos como transmitir notícias por categoria. Considere concluindo um dos seguintes tutoriais que destacam outros cenários avançados de Hubs de notificação:

+ [Use Hubs de notificação para transmitir notícias localizados]

    Saiba como expandir o aplicativo de notícias ruptura para habilitar o envio de notificações localizadas.





<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Use Hubs de notificação para transmitir notícias localizados]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure Portal clássico]: https://manage.windowsazure.com
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
