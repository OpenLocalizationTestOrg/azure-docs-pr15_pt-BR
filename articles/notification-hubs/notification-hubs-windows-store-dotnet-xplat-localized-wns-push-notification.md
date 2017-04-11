<properties
    pageTitle="Notificação Hubs localizados ruptura notícias Tutorial"
    description="Saiba como usar Hubs de notificação do Azure para enviar notificações de notícias ruptura localizados."
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

# <a name="use-notification-hubs-to-send-localized-breaking-news"></a>Use Hubs de notificação para enviar localizados últimas notícias

> [AZURE.SELECTOR]
- [C# da Windows Store](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
- [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

##<a name="overview"></a>Visão geral

Este tópico mostra como usar o recurso de **modelo** de Hubs de notificação do Azure transmitir notificações de notícias recentes que foram localizadas por idioma e dispositivo. Neste tutorial, você começar com o aplicativo da Windows Store criado em [Hubs de notificação de uso para enviar as últimas notícias]. Ao concluir, que você poderá registrar para categorias de que seu interesse, especifique um idioma no qual deseja receber notificações e receber apenas as notificações por push para as categorias selecionadas nesse idioma.


Há duas partes para esse cenário:

- o aplicativo da Windows Store permite que dispositivos cliente para especificar um idioma e para assinar ruptura diferentes categorias de notícias;

- o back-end transmissões as notificações, usando os recursos de **marca** e **modelo** de Hubs de notificação do Azure.



##<a name="prerequisites"></a>Pré-requisitos

Você deve já tiver concluído o tutorial de [Hubs de notificação de uso para enviar as últimas notícias] e ter o código disponível, porque este tutorial baseia-se diretamente no código.

Você também precisa Visual Studio 2012 ou posterior.


##<a name="template-concepts"></a>Conceitos de modelo

Em [Hubs de notificação de uso para enviar as últimas notícias] você criou um aplicativo que usadas **marcas** para assinar notificações de novas diferentes categorias.
Muitos aplicativos, no entanto, vários mercados de destino e requerem localização. Isso significa que o conteúdo das notificações de si precisa ser localizado e entregue para o conjunto correto de dispositivos.
Neste tópico mostraremos como usar o recurso de **modelo** de notificação Hubs para entregar facilmente notificações de notícias ruptura localizados.

Observação: é uma maneira de enviar notificações localizadas criar várias versões de cada marca. Por exemplo, para oferecer suporte a inglês, francês e Mandarim, temos três marcas diferentes para obter notícias do mundo: "world_en", "world_fr" e "world_ch". Podemos precisaria enviar uma versão localizada da notícia mundo para cada uma dessas marcas. Neste tópico podemos usar modelos para evitar a proliferação de marcas e o requisito de envio de várias mensagens.

Em um alto nível, os modelos são uma maneira de especificar como um dispositivo específico deve receber uma notificação. O modelo especifica o formato de carga exata fazendo referência a propriedades que fazem parte da mensagem enviada por seu aplicativo back-end. Em nosso caso, enviaremos uma mensagem de independente de localidade contendo todos os idiomas suportados:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Em seguida, podemos garantirá que dispositivos registrar com um modelo que se refere à propriedade correta. Por exemplo, um aplicativo da Windows Store que deseja receber uma mensagem de sistema simples registrará para o modelo com todas as marcas correspondentes a seguir:

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">$(News_English)</text>
        </binding>
      </visual>
    </toast>



Os modelos são um poderoso recurso que você pode saber mais sobre nosso artigo de [modelos](notification-hubs-templates-cross-platform-push-messages.md) . 


##<a name="the-app-user-interface"></a>A interface de usuário do aplicativo

Agora, vamos modificar o aplicativo Breaking News que você criou no tópico [Hubs de notificação de uso para enviar as últimas notícias] enviar notícias localizados usando modelos.

Em seu aplicativo da Windows Store:

Altere sua MainPage para incluir uma caixa de combinação localidade:

    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>

##<a name="building-the-windows-store-client-app"></a>Criar o aplicativo de cliente da Windows Store

1. Na sua classe de notificações, adicione um parâmetro de localidade para seus métodos *StoreCategoriesAndSubscribe* e *SubscribeToCateories* .

        public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            return await SubscribeToCategories(categories);
        }

        public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration. This makes supporting notifications across other platforms much easier.
            // Using the localized tags based on locale selected.
            string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
        }

    Observe que, em vez de chamar o método *RegisterNativeAsync* chamamos *RegisterTemplateAsync*: nós são Registrando um formato de notificação específica na qual o modelo depende da localidade. Também fornecemos um nome para o modelo ("localizedWNSTemplateExample"), porque queremos registrar mais de um modelo (por exemplo uma para notificações do sistema) e outra para blocos e precisamos nomeá-los para poder atualizar ou excluí-las.

    Observe que se um dispositivo registra vários modelos com a mesma marca, uma entrada mensagem direcionamento que marca resultarão em várias notificações entregues no dispositivo (um para cada modelo). Esse comportamento é útil quando a mesma mensagem lógica tem que resultam em várias notificações visuais, por exemplo mostrando um selo e um brinde em um aplicativo da Windows Store.

2. Adicione o seguinte método para recuperar a localidade armazenada:

        public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3. Em seu MainPage.xaml.cs, atualize seu botão manipulador de clique recuperar o valor atual da caixa de combinação localidade e fornecendo-lo à chamada para a classe de notificações, conforme mostrado:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var locale = (string)Locale.SelectedItem;

            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                 categories);

            var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
                string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }


4. Por fim, no seu arquivo App.xaml.cs, certifique-se de atualizar seu `InitNotificationsAsync` método para recuperar a localidade e usá-lo ao inscrever-se:

        private async void InitNotificationsAsync()
        {
            var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


##<a name="send-localized-notifications-from-your-back-end"></a>Enviar notificações localizadas do seu back-end

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]






<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Use Hubs de notificação para enviar últimas notícias]: /manage/services/notification-hubs/breaking-news-dotnet

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
