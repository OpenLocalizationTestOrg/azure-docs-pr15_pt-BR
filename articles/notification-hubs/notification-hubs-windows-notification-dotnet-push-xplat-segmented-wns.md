<properties
    pageTitle="Use Hubs de notificação para enviar últimas notícias (Windows Universal)"
    description="Use Hubs de notificação do Azure com marcas no registro para enviar últimas notícias para um aplicativo universal do Windows."
    services="notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""/>


<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Use Hubs de notificação para enviar últimas notícias


[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Visão geral

Este tópico mostra como usar Hubs de notificação do Azure para transmitir notificações de notícias recentes para um Windows Store ou o aplicativo do Windows Phone 8.1 (não Silverlight). Se você está destinando Silverlight do Windows Phone 8.1, consulte a versão [Do Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) . Ao concluir, você poderá registrar para dividir categorias de notícias que você está interessado e, receber apenas as notificações por push para essas categorias. Esse cenário é um padrão comum para muitos aplicativos onde notificações têm sejam enviadas aos grupos de usuários que têm declarados previamente juros nelas, por exemplo, leitor RSS, aplicativos para ventiladores de música e assim por diante. 

Cenários de difusão são habilitados, incluindo uma ou mais _marcas_ ao criar um registro no hub de notificação. Quando as notificações são enviadas para uma marca, todos os dispositivos que foram registrados para a marca receberão a notificação. Como marcas são simplesmente cadeias de caracteres, eles não precisam ser provisionados com antecedência. Para obter mais informações sobre marcas, consulte [expressões de marca e roteamento de Hubs de notificação](notification-hubs-tags-segment-push-message.md).

##<a name="prerequisites"></a>Pré-requisitos

Este tópico se baseia o aplicativo que você criou na [Introdução com Hubs de notificação][get-started]. Antes de iniciar este tutorial, você deve já concluiu [começar com Hubs de notificação][get-started].

##<a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categoria para o aplicativo

A primeira etapa é adicionar os elementos de interface do usuário à sua página principal existente que permitem que o usuário selecione categorias para registrar. As categorias selecionadas por um usuário são armazenadas no dispositivo. Quando o aplicativo for iniciado, um registro de dispositivo é criado no seu hub de notificação com as categorias selecionadas como marcas.

1. Abra o arquivo de projeto MainPage, em seguida, copie o seguinte código no elemento de **grade** :

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>


2. Clique com botão direito no projeto **Shared** e adicionar uma nova classe denominada **notificações**, adicione a **público** modificadora à definição de classe, depois adicione as seguintes instruções de **uso** para o novo arquivo de código:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.Storage;
        using System.Threading.Tasks;

3. Copie o código a seguir para a nova classe de **notificações** :

        private NotificationHub hub;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            return await SubscribeToCategories(categories);
        }

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }

        public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.

            const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                    categories);
        }

    Essa classe usa o armazenamento local para armazenar as categorias de notícias que tem esse dispositivo para receber. Observe que, em vez de chamar o método *RegisterNativeAsync* chamamos *RegisterTemplateAsync* para registrar para as categorias usando um registro de modelo. 
    
    Também fornecemos um nome para o modelo ("simpleWNSTemplateExample"), porque queremos registrar mais de um modelo (por exemplo uma para notificações do sistema) e outra para blocos e precisamos nomeá-los para poder atualizar ou excluí-las.

    Observe que se um dispositivo registra vários modelos com a mesma marca, uma entrada mensagem direcionamento que marca resultarão em várias notificações entregues no dispositivo (um para cada modelo). Esse comportamento é útil quando a mesma mensagem lógica tem que resultam em várias notificações visuais, por exemplo mostrando um selo e um brinde em um aplicativo da Windows Store.

    Para obter mais informações sobre modelos, consulte [modelos](notification-hubs-templates-cross-platform-push-messages.md).




4. No arquivo de projeto App.xaml.cs, adicione a propriedade a seguir para a classe de **aplicativo** :

        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

    Essa propriedade é usada para criar e acessar uma instância de **notificações** .

    No código acima, substitua o `<hub name>` e `<connection string with listen access>` espaços reservados com seu nome de hub de notificação e a cadeia de conexão para *DefaultListenSharedAccessSignature* obtido anteriormente.

    > [AZURE.NOTE] Como as credenciais que são distribuídas com um aplicativo cliente não são geralmente seguras, você só deve distribuir a chave para escutar acesso com seu aplicativo cliente. Ouça permite que o access não pode ser modificado seu aplicativo para registrar para notificações, mas os registros existentes e as notificações não podem ser enviadas. A tecla de acesso total é usada em um serviço de back-end segura para enviar notificações e alterar os registros existentes.

5. Em seu MainPage.xaml.cs, adicione a seguinte linha:

        using Windows.UI.Popups;

6. No arquivo de projeto MainPage.xaml.cs, adicione o seguinte método:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    Este método cria uma lista de categorias e usa a classe de **notificações** para armazenar a lista no armazenamento local e registrar as marcas correspondentes com seu hub de notificação. Quando as categorias são alteradas, o registro será recriado com as novas categorias.

Seu aplicativo agora é possível armazenar um conjunto de categorias no armazenamento local do dispositivo e registrar com o hub de notificação sempre que o usuário altera a seleção de categorias.

##<a name="register-for-notifications"></a>Registre-se para notificações

Estas etapas registram com o hub de notificação na inicialização usando as categorias que foram armazenadas no armazenamento local.

> [AZURE.NOTE] Como o canal URI atribuído pelo serviço de notificação do Windows (WNS) pode alterar a qualquer momento, você deve registrar para notificações frequentemente evitar falhas de notificação. Este exemplo registra para notificação sempre que o aplicativo é iniciado. Para os aplicativos que são executados com frequência, mais de uma vez por dia, você pode provavelmente ignorar registro para preservar a largura de banda se menos de um dia passou desde o registro anterior.

1. Abra o arquivo App.xaml.cs e atualize o método **InitNotificationsAsync** para usar o `notifications` classe assine com base em categorias.

        // *** Remove or comment out these lines *** 
        //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
        //var hub = new NotificationHub("your hub name", "your listen connection string");
        //var result = await hub.RegisterNativeAsync(channel.Uri);
    
        var result = await notifications.SubscribeToCategories();

    Isso garante que os que sempre que o aplicativo é iniciado, ele recupera as categorias de armazenamento local e solicita um registeration para estas categorias. O método **InitNotificationsAsync** foi criado como parte da [começar com Hubs de notificação] [ get-started] tutorial.

3. No arquivo de projeto MainPage.xaml.cs, adicione o seguinte código para o método *OnNavigatedTo* :

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldToggle.IsOn = true;
            if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
            if (categories.Contains("Business")) BusinessToggle.IsOn = true;
            if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
            if (categories.Contains("Science")) ScienceToggle.IsOn = true;
            if (categories.Contains("Sports")) SportsToggle.IsOn = true;
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

    ![][19]

4. Envie uma nova notificação de back-end em uma das seguintes maneiras:

    + **Aplicativo de console:** iniciar o aplicativo de console.

    + **Java/PHP:** executar seu aplicativo/script.

    Notificações para as categorias selecionadas aparecem como notificações do sistema.

    ![][14]

##<a name="next-steps"></a>Próximas etapas

Neste tutorial aprendemos como transmitir notícias por categoria. Considere concluindo um dos seguintes tutoriais que destacam outros cenários avançados de Hubs de notificação:

+ [Use Hubs de notificação para transmitir notícias localizados]

    Saiba como expandir o aplicativo de notícias ruptura para habilitar o envio de notificações localizadas.



<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Use Hubs de notificação para transmitir notícias localizados]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
