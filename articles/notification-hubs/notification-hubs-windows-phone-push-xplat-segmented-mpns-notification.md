<properties
    pageTitle="Use Hubs de notificação para enviar últimas notícias (Windows Phone)"
    description="Use Hubs de notificação do Azure para usar marca nos registros para enviar últimas notícias para um aplicativo do Windows Phone."
    services="notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Use Hubs de notificação para enviar últimas notícias

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Visão geral

Este tópico mostra como usar Hubs de notificação do Azure para transmitir notificações de notícias recentes para um aplicativo do Windows Phone 8.0/8.1 Silverlight. Se você está destinando da Windows Store ou o aplicativo do Windows Phone 8.1, consulte a versão [Universal do Windows](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md) . Ao concluir, você poderá registrar para dividir categorias de notícias que você está interessado e, receber apenas as notificações por push para essas categorias. Esse cenário é um padrão comum para muitos aplicativos onde notificações têm sejam enviadas aos grupos de usuários que têm declarados previamente juros nelas, por exemplo, leitor RSS, aplicativos para ventiladores de música, etc.

Cenários de difusão são habilitados, incluindo uma ou mais _marcas_ ao criar um registro no hub de notificação. Quando as notificações são enviadas para uma marca, todos os dispositivos que foram registrados para a marca receberão a notificação. Como marcas são simplesmente cadeias de caracteres, eles não precisam ser provisionados com antecedência. Para obter mais informações sobre marcas, consulte [expressões de marca e roteamento de Hubs de notificação](notification-hubs-tags-segment-push-message.md).

##<a name="prerequisites"></a>Pré-requisitos

Este tópico se baseia o aplicativo que você criou na [Introdução com Hubs de notificação]. Antes de iniciar este tutorial, você deve ter já concluído [começar com Hubs de notificação].

##<a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categoria para o aplicativo

A primeira etapa é adicionar os elementos de interface do usuário à sua página principal existente que permitem que o usuário selecione categorias para registrar. As categorias selecionadas por um usuário são armazenadas no dispositivo. Quando o aplicativo for iniciado, um registro de dispositivo é criado no seu hub de notificação com as categorias selecionadas como marcas.

1. Abra o arquivo de projeto MainPage, em seguida, substituir os elementos de **grade** chamados `TitlePanel` e `ContentPanel` com o seguinte código:

        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>

        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>

2. No projeto, crie uma nova classe denominada **notificações**, adicione a **público** modificadora à definição de classe e adicione as seguintes instruções de **uso** para o novo arquivo de código:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
        using System.IO.IsolatedStorage;
        using System.Windows;

3. Copie o código a seguir para a nova classe de **notificações** :

        private NotificationHub hub;

        // Registration task to complete registration in the ChannelUriUpdated event handler
        private TaskCompletionSource<Registration> registrationTask;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
            return categories != null ? categories.Split(',') : new string[0];
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            var categoriesAsString = string.Join(",", categories);
            var settings = IsolatedStorageSettings.ApplicationSettings;
            if (!settings.Contains("categories"))
            {
                settings.Add("categories", categoriesAsString);
            }
            else
            {
                settings["categories"] = categoriesAsString;
            }
            settings.Save();

            return await SubscribeToCategories();
        }

        public async Task<Registration> SubscribeToCategories()
        {
            registrationTask = new TaskCompletionSource<Registration>();

            var channel = HttpNotificationChannel.Find("MyPushChannel");

            if (channel == null)
            {
                channel = new HttpNotificationChannel("MyPushChannel");
                channel.Open();
                channel.BindToShellToast();
                channel.ChannelUriUpdated += channel_ChannelUriUpdated;

                // This is optional, used to receive notifications while the app is running.
                channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
            }

            // If channel.ChannelUri is not null, we will complete the registrationTask here.  
            // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
            if (channel.ChannelUri != null)
            {
                await RegisterTemplate(channel.ChannelUri);
            }
            
            return await registrationTask.Task;
        }

        async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
        {
            await RegisterTemplate(e.ChannelUri);
        }

        async Task<Registration> RegisterTemplate(Uri channelUri)
        {
            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.

            const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                                "<wp:Toast>" +
                                                    "<wp:Text1>$(messageParam)</wp:Text1>" +
                                                "</wp:Toast>" +
                                            "</wp:Notification>";

            // The stored categories tags are passed with the template registration.

            registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(), 
                templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

            return await registrationTask.Task;
        }

        // This is optional. It is used to receive notifications while the app is running.
        void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
        {
            StringBuilder message = new StringBuilder();
            string relativeUri = string.Empty;

            message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

            // Parse out the information that was part of the message.
            foreach (string key in e.Collection.Keys)
            {
                message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

                if (string.Compare(
                    key,
                    "wp:Param",
                    System.Globalization.CultureInfo.InvariantCulture,
                    System.Globalization.CompareOptions.IgnoreCase) == 0)
                {
                    relativeUri = e.Collection[key];
                }
            }

            // Display a dialog of all the fields in the toast.
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() => 
            { 
                MessageBox.Show(message.ToString()); 
            });
        }


    Essa classe usa o armazenamento isolado para armazenar as categorias de notícias que deve receber este dispositivo. Ele também contém métodos para registrar para estas categorias usando um registro de notificação do [modelo](notification-hubs-templates-cross-platform-push-messages.md) .


4. No arquivo de projeto App.xaml.cs, adicione a propriedade a seguir para a classe **App** . Substituir o `<hub name>` e `<connection string with listen access>` espaços reservados com seu nome de hub de notificação e a cadeia de conexão para *DefaultListenSharedAccessSignature* obtido anteriormente.

        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

    > [AZURE.NOTE] Como as credenciais que são distribuídas com um aplicativo cliente não são geralmente seguras, você só deve distribuir a chave para escutar acesso com seu aplicativo cliente. Ouça permite que o access não pode ser modificado seu aplicativo para registrar para notificações, mas os registros existentes e as notificações não podem ser enviadas. A tecla de acesso total é usada em um serviço de back-end segura para enviar notificações e alterar os registros existentes.

5. Em seu MainPage.xaml.cs, adicione a seguinte linha:

        using Windows.UI.Popups;

6. No arquivo de projeto MainPage.xaml.cs, adicione o seguinte método:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
          var categories = new HashSet<string>();
          if (WorldCheckBox.IsChecked == true) categories.Add("World");
          if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
          if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
          if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
          if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
          if (SportsCheckBox.IsChecked == true) categories.Add("Sports");
    
          var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
    
          MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
             result.RegistrationId);
        }

    Este método cria uma lista de categorias e usa a classe de **notificações** para armazenar a lista no armazenamento local e registrar as marcas correspondentes com seu hub de notificação. Quando as categorias são alteradas, o registro será recriado com as novas categorias.

Seu aplicativo agora é possível armazenar um conjunto de categorias no armazenamento local do dispositivo e registrar com o hub de notificação sempre que o usuário altera a seleção de categorias.

##<a name="register-for-notifications"></a>Registre-se para notificações

Estas etapas registram com o hub de notificação na inicialização usando as categorias que foram armazenadas no armazenamento local.

> [AZURE.NOTE] Como o canal URI atribuído pelo serviço de notificação por Push a Microsoft (MPNS) pode alterar a qualquer momento, você deve registrar para notificações frequentemente evitar falhas de notificação. Este exemplo registra para notificações toda vez que o aplicativo é iniciado. Para os aplicativos que são executados com frequência, mais de uma vez por dia, você pode provavelmente ignorar registro para preservar a largura de banda se menos de um dia passou desde o registro anterior.


1. Abra o arquivo App.xaml.cs e adicione a modificadora **assíncrona** ao método de **Application_Launching** e substitua o código de registro de Hubs de notificação que você adicionou na [Introdução com Hubs de notificação] com o seguinte código:

        private async void Application_Launching(object sender, LaunchingEventArgs e)
        {
            var result = await notifications.SubscribeToCategories();

            if (result != null)
                System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
                {
                    MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
                });
        }

    Isso garante que os que sempre que o aplicativo é iniciado, ele recupera as categorias de armazenamento local e as solicitações de um registro para estas categorias.

2. No arquivo de projeto MainPage.xaml.cs, adicione o seguinte código que implementa o método **OnNavigatedTo** :

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
            if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
            if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
            if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
            if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
            if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
        }

    Isso atualiza a página principal baseada no status das categorias salvas anteriormente.

O aplicativo está concluído e pode armazenar um conjunto de categorias no armazenamento local dispositivo usado para registrar com o hub de notificação sempre que o usuário altera a seleção de categorias. Em seguida, podemos definirá um back-end que pode enviar notificações de categoria para este aplicativo.

##<a name="sending-tagged-notifications"></a>Enviar notificações marcadas

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##<a name="run-the-app-and-generate-notifications"></a>Executar o aplicativo e gerar notificações

1. No Visual Studio, pressione F5 para compilar e iniciar o aplicativo.

    ![][1]

    Observe que o aplicativo de interface do usuário fornece um conjunto de alterna que permite escolher as categorias de assinar.

2. Habilitar um ou mais alterna de categorias e, em seguida, clique em **assinar**.

    O aplicativo converte categorias selecionadas em marcas e solicita um novo registro de dispositivo para as marcas selecionadas do hub notificação. As categorias registradas são retornadas e exibidas em uma caixa de diálogo.

    ![][2]

3. Depois de receber uma confirmação de que suas categorias eram assinatura concluída, execute o aplicativo de console para enviar notificações para cada categorias. Verifique se que você só recebe uma notificação para as categorias que você se inscreveu.

    ![][3]

Você concluiu neste tópico.

<!--##Next steps

In this tutorial we learned how to broadcast breaking news by category. Consider completing one of the following tutorials that highlight other advanced Notification Hubs scenarios:

+ [Use Notification Hubs to broadcast localized breaking news]

    Learn how to expand the breaking news app to enable sending localized notifications.

+ [Notify users with Notification Hubs]

    Learn how to push notifications to specific authenticated users. This is a good solution for sending notifications only to specific users.
-->

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[Começar com Hubs de notificação]: /manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Use Notification Hubs to broadcast localized breaking news]: ../breakingnews-localized-wp8.md
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone]: ??

