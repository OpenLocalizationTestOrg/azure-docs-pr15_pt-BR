<properties
    pageTitle="Notificação Azure Hubs seguros por Push"
    description="Saiba como enviar notificações por push seguro no Azure. Exemplos de código escritos em c# usando a API do .NET."
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs" 
    ms.workload="mobile"
    ms.tgt_pltfrm="windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-secure-push"></a>Notificação Azure Hubs seguros por Push

> [AZURE.SELECTOR]
- [Universal do Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)


##<a name="overview"></a>Visão geral

Suporte de notificação de envio no Microsoft Azure permite que você acesse uma infraestrutura de envio fácil de usar várias plataformas, dimensionada-out, que simplifica a implementação de notificações de envio para aplicativos do consumidor e empresa para plataformas móveis.

Devido a regulamentares ou restrições de segurança, às vezes, um aplicativo talvez queira incluir algo na notificação que não pode ser transmitida por meio de infraestrutura de notificação do envio padrão. Este tutorial descreve como atingir a mesma experiência enviando informações confidenciais por meio de uma conexão segura, autenticada entre o dispositivo de cliente e o back-end do aplicativo.

Em um alto nível, o fluxo é da seguinte maneira:

1. O back-end do aplicativo:
    - Carga de segurança de lojas no banco de dados back-end.
    - Envia a ID desta notificação no dispositivo (nenhuma informação segura é enviada).
2. O aplicativo do dispositivo, quando receber a notificação:
    - O dispositivo back-end solicitando a carga de segurança de contatos.
    - O aplicativo pode mostrar a carga como uma notificação no dispositivo.

É importante observar que no fluxo de anterior (e neste tutorial), vamos supor que o dispositivo armazena um token de autenticação em armazenamento local, depois que o usuário faz logon. Isso garante uma experiência perfeita completamente, conforme o dispositivo pode recuperar a carga de segurança da notificação usando este token. Se seu aplicativo não armazene tokens de autenticação do dispositivo, ou se esses tokens podem ser expirados, o aplicativo de dispositivo, após receber a notificação deverá exibir uma notificação genérica solicitando ao usuário para iniciar o aplicativo. Em seguida, o aplicativo autentica o usuário e mostra a carga de notificação.

Este tutorial de envio seguro mostra como enviar uma notificação de envio com segurança. O tutorial se baseia no tutorial [Notificar usuários](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) , portanto você deve concluir as etapas desse tutorial primeiro.

> [AZURE.NOTE] Este tutorial supõe que você criou e configurado seu hub de notificação, conforme descrito na [Introdução com Hubs de notificação (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
Além disso, observe que o Windows Phone 8.1 requer credenciais do Windows (não Windows Phone), e que tarefas em segundo plano não funcionam no Windows Phone 8.0 ou 8.1 do Silverlight. Para aplicativos da Windows Store, você pode receber notificações por meio de uma tarefa de plano de fundo somente se o aplicativo está habilitada na tela de bloqueio (clique na caixa de seleção no Appmanifest).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Modificar o projeto do Windows Phone

1. No projeto **NotifyUserWindowsPhone** , adicione o seguinte código a App.xaml.cs para registrar a tarefa de plano de fundo de envio. Adicione a seguinte linha de código no final do `OnLaunched()` método:

        RegisterBackgroundTask();

2. Ainda em App.xaml.cs, adicione o seguinte código imediatamente após o `OnLaunched()` método:

        private async void RegisterBackgroundTask()
        {
            if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
            {
                var result = await BackgroundExecutionManager.RequestAccessAsync();
                var builder = new BackgroundTaskBuilder();

                builder.Name = "PushBackgroundTask";
                builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
                builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
                BackgroundTaskRegistration task = builder.Register();
            }
        }

3. Adicione o seguinte `using` instruções na parte superior do arquivo App.xaml.cs:

        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;

4. No menu **arquivo** no Visual Studio, clique em **Salvar tudo**.

## <a name="create-the-push-background-component"></a>Criar o componente de plano de fundo de envio

A próxima etapa é criar o componente de plano de fundo de envio.

1. No Solution Explorer, clique com botão direito o nó de nível superior da solução (**SecurePush solução** nesse caso), em seguida, clique em **Adicionar**, clique em **Novo projeto**.

2. Expanda o **Armazenamento de aplicativos**, e em seguida, clique em **Aplicativos do Windows Phone**, clique em **Componente de tempo de execução do Windows (Windows Phone)**. Nomeie o projeto **PushBackgroundComponent**e clique em **Okey** para criar o projeto.

    ![][12]

3. Na Solution Explorer, clique com botão direito no projeto **PushBackgroundComponent (Windows Phone 8.1)** , e em seguida, clique em **Adicionar**, clique em **classe**. Nomeie a nova classe **PushBackgroundTask.cs**. Clique em **Adicionar** para gerar a classe.

4. Substitua todo o conteúdo da definição de namespace **PushBackgroundComponent** com o seguinte código, substituindo o espaço reservado `{back-end endpoint}` com o ponto de extremidade de back-end obtido ao implantar seu back-end:

        public sealed class Notification
            {
                public int Id { get; set; }
                public string Payload { get; set; }
                public bool Read { get; set; }
            }

            public sealed class PushBackgroundTask : IBackgroundTask
            {
                private string GET_URL = "{back-end endpoint}/api/notifications/";

                async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
                {
                    // Store the content received from the notification so it can be retrieved from the UI.
                    RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                    var notificationId = raw.Content;

                    // retrieve content
                    BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                    var httpClient = new HttpClient();
                    var settings = ApplicationData.Current.LocalSettings.Values;
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                    var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                    ShowToast(notification);

                    deferral.Complete();
                }

                private void ShowToast(Notification notification)
                {
                    ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                    XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                    XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                    toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                    ToastNotification toast = new ToastNotification(toastXml);
                    ToastNotificationManager.CreateToastNotifier().Show(toast);
                }
            }

5. No Solution Explorer, clique com botão direito no projeto **PushBackgroundComponent (Windows Phone 8.1)** e clique em **Gerenciar pacotes do NuGet**.

6. No lado esquerdo, clique em **Online**.

7. Na caixa de **pesquisa** , digite **Cliente Http**.

8. Na lista de resultados, clique em **Bibliotecas de cliente do Microsoft HTTP**e, em seguida, clique em **instalar**. Conclua a instalação.

9. Novamente na caixa NuGet **pesquisa** , digite **Json.net**. Instale o pacote de **Json.NET** e, em seguida, feche a janela Gerenciador de pacote do NuGet.

10. Adicione o seguinte `using` instruções na parte superior do arquivo **PushBackgroundTask.cs** :

        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;

11. No Solution Explorer, no projeto **NotifyUserWindowsPhone (Windows Phone 8.1)** , **referências**de atalho, clique em **Add Reference...**. Na caixa de diálogo Gerenciador de referência, marque a caixa ao lado de **PushBackgroundComponent**e clique em **Okey**.

12. No Solution Explorer, clique duas vezes em **Package.appxmanifest** no projeto **NotifyUserWindowsPhone (Windows Phone 8.1)** . Em **notificações**, defina **Capaz de sistema** para **Sim**.

    ![][3]

13. Ainda no **Package.appxmanifest**, clique no menu de **declarações** , na parte superior. Na lista suspensa de **Declarações disponíveis** , clique em **Tarefas em segundo plano**e, em seguida, clique em **Adicionar**.

14. Em **Package.appxmanifest**, em **Propriedades**, verifique a **notificação por Push**.

15. Em **Package.appxmanifest**, em **Configurações do aplicativo**, digite **PushBackgroundComponent.PushBackgroundTask** no campo do **Ponto de entrada** .

    ![][13]

16. No menu **arquivo** , clique em **Salvar tudo**.

## <a name="run-the-application"></a>Executar o aplicativo

Para executar o aplicativo, faça o seguinte:

1. No Visual Studio, execute o aplicativo de Web API **AppBackend** . Uma página da web é exibida.

2. No Visual Studio, execute o aplicativo do Windows Phone **NotifyUserWindowsPhone (Windows Phone 8.1)** . O emulador do Windows Phone é executado e carrega o aplicativo automaticamente.

3. No aplicativo **NotifyUserWindowsPhone** interface do usuário, digite um nome de usuário e senha. Eles podem ser qualquer cadeia de caracteres, mas eles devem ser o mesmo valor.

4. No aplicativo **NotifyUserWindowsPhone** interface do usuário, clique em **login e registrar**. Clique em **Enviar por push**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
