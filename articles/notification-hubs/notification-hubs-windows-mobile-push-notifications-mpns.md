<properties
    pageTitle="Enviando notificações de envio com Hubs de notificação do Azure no Windows Phone | Microsoft Azure"
    description="Neste tutorial, você aprenderá a usar Hubs de notificação do Azure para notificações por push para um aplicativo Windows Phone 8 ou Windows Phone 8.1 Silverlight."
    services="notification-hubs"
    documentationCenter="windows"
    keywords="notificação por push, notificação, por push push do windows phone"
    authors="ysxu"
    manager="erikre"
    editor="erikre"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-with-azure-notification-hubs-on-windows-phone"></a>Enviando notificações de envio com Hubs de notificação do Azure no Windows Phone

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Visão geral

> [AZURE.NOTE] Para concluir este tutorial, você deve ter uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).

Este tutorial mostra como usar Hubs de notificação do Azure para enviar notificações por push para um aplicativo Windows Phone 8 ou Windows Phone 8.1 Silverlight. Se você está destinando Windows Phone 8.1 (não Silverlight), em seguida, consulte a versão [Universal do Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .
Neste tutorial, você criar um aplicativo do Windows Phone 8 em branco que recebe as notificações por push usando o serviço de notificação de envio (MPNS) da Microsoft. Quando tiver terminado, você poderá usar o seu hub de notificação para transmitir notificações por push para todos os dispositivos executando o aplicativo.

> [AZURE.NOTE] O Windows Phone SDK do notificação Hubs não suporta usando o serviço de notificação por Push (WNS) do Windows com aplicativos do Windows Phone 8.1 Silverlight. Para usar o WNS (em vez de MPNS) com aplicativos do Windows Phone 8.1 Silverlight, siga os [Hubs de notificação - tutorial do Windows Phone Silverlight], que usa APIs REST.

O tutorial demonstra o cenário de difusão simples usando Hubs de notificação.

##<a name="prerequisites"></a>Pré-requisitos

Este tutorial requer o seguinte:

+ [Visual Studio 2012 Express para Windows Phone], ou uma versão posterior.

Concluir este tutorial é um pré-requisito para todos os outros tutoriais Hubs de notificação para os aplicativos do Windows Phone 8.

##<a name="create-your-notification-hub"></a>Criar seu hub de notificação

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Clique na seção de <b>Serviços de notificação</b> (nas <i>configurações</i>), clique no <b>Windows Phone (MPNS)</b> e clique em caixa de seleção <b>Habilitar push não autenticado</b> .</p>
</li>
</ol>

&emsp;&emsp;![Portal Azure - habilitar unathenticated as notificações por push](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

Agora, seu hub é criado e configurado para enviar notificação não autenticada para Windows Phone.

> [AZURE.NOTE] Este tutorial usa MPNS no modo não autenticado. Modo não autenticado MPNS vem com restrições notificações que você pode enviar a cada canal. Notificação Hubs suporta [MPNS autenticado modo](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) , permitindo que você carregar seu certificado.

##<a name="connecting-your-app-to-the-notification-hub"></a>Conectando seu aplicativo para o hub de notificação

1. No Visual Studio, crie um novo aplicativo do Windows Phone 8.

    ![Aplicativo do Visual Studio - novo projeto - Windows Phone][13]

    No Visual Studio 2013 atualização 2 ou posterior, em vez disso, você criar um aplicativo do Silverlight do Windows Phone.

    ![Visual Studio - novo projeto - branco aplicativo - Windows Phone Silverlight][11]

2. No Visual Studio, a solução de atalho e, em seguida, clique em **Gerenciar pacotes do NuGet**.

    Isso exibe a caixa de diálogo **Gerenciar pacotes do NuGet** .

3. Procurar `WindowsAzure.Messaging.Managed` e clique em **instalar**e, em seguida, aceite os termos de uso.

    ![Visual Studio - NuGet Package Manager][20]

    Isso downloads, instalações e adiciona uma referência para a biblioteca de mensagens do Azure para Windows, usando o <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">pacote WindowsAzure.Messaging.Managed NuGet</a>.

4. Abra o arquivo App.xaml.cs e adicione o seguinte `using` instruções:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. Adicione o seguinte código na parte superior do método de **Application_Launching** em App.xaml.cs:

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());

            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });

    >[AZURE.NOTE] O valor **MyPushChannel** é um índice que é usado para pesquisar um canal existente no conjunto de [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx) . Se não houver um lá, crie uma nova entrada com esse nome.
    
    Certifique-se de inserir o nome do seu hub e a cadeia de caracteres de conexão chamado **DefaultListenSharedAccessSignature** obtido na seção anterior.
    Este código recupera o canal URI para o aplicativo de MPNS e registra desse canal URI com seu hub de notificação. Ele também garante que o canal que URI é registrado no seu hub de notificação sempre que o aplicativo é iniciado.

    >[AZURE.NOTE]Este tutorial envia uma notificação do sistema para o dispositivo. Quando você envia uma notificação de peças, em vez disso, você deve chamar o método **BindToShellTile** no canal. Ambos os sistema de suporte e notificações de bloco, chamadas **BindToShellTile** e **BindToShellToast**.

6. No Solution Explorer, expanda **Propriedades**, abra o `WMAppManifest.xml` de arquivo, clique na guia **recursos** e certifique-se de que o recurso **ID_CAP_PUSH_NOTIFICATION** está marcado.

    ![Visual Studio - Windows Phone recursos de aplicativo][14]

    Isso garante que o seu aplicativo pode receber notificações por push. Sem ele, qualquer tentativa de enviar uma notificação por push para o aplicativo falhará.

7. Pressione a `F5` tecla para executar o aplicativo.

    Será exibida uma mensagem de registro no aplicativo.

8. Feche o aplicativo.  

   >[AZURE.NOTE] Para receber uma notificação de envio do sistema, o aplicativo não deve estar executando em primeiro plano.

##<a name="send-push-notifications-from-your-backend"></a>Enviar notificações por push do seu back-end

Você pode enviar notificações por push usando Hubs de notificação de qualquer back-end por meio da <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>do público. Neste tutorial, você enviar notificações por push usando um aplicativo de console do .NET. 

Para um exemplo de como enviar notificações por push de um back-end do ASP.NET WebAPI integrada com Hubs de notificação, consulte [Azure notificação Hubs notificar os usuários com .NET back-end](./notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md).  

Para obter um exemplo de como enviar notificações por push usando as [APIs REST](https://msdn.microsoft.com/library/azure/dn223264.aspx), confira [como usar Hubs de notificação do Java](./notification-hubs-java-push-notification-tutorial.md) e [como usar Hubs de notificação do PHP](./notification-hubs-php-push-notification-tutorial.md).

1. A solução de atalho, selecione **Adicionar** e **Novo projeto...**, em seguida, em **Visual c#**, clique em **Windows** e o **Aplicativo de Console**e clique em **Okey**.

    ![Aplicativo de Console do Visual Studio - novo projeto-][6]

    Isso adiciona um novo aplicativo Visual c# console à solução. Você também pode fazer isso em uma solução separada.

4. Clique em **Ferramentas**, clique em **Gerenciador de pacote de biblioteca**e clique em **Package Manager Console**.

    Isso exibe o Package Manager Console.

5.  Na janela do **Console do Gerenciador de pacote** , defina o **padrão de projeto** para seu novo projeto de aplicativo de console e, em seguida, na janela do console, execute o seguinte comando:

        Install-Package Microsoft.Azure.NotificationHubs

    Isso adiciona uma referência para o SDK de Hubs de notificação do Azure usando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacote Microsoft.Azure.Notification Hubs NuGet</a>.

6. Abrir o `Program.cs` de arquivo e adicione o seguinte `using` instrução:

        using Microsoft.Azure.NotificationHubs;

6. No `Program` classe, adicione o seguinte método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

    Certifique-se de substituir o `<hub name>` espaço reservado com o nome do hub notificação que aparece no portal. Além disso, substitua o espaço reservado cadeia de conexão com a cadeia de conexão chamada **DefaultFullSharedAccessSignature** obtido na seção "Configurar seu hub de notificação".

    >[AZURE.NOTE]Certifique-se de que você use a cadeia de conexão com acesso **total** , acesso não **ouvir** . A cadeia de caracteres de acesso ouvir não tem permissões para enviar notificações por push.

4. Adicione a seguinte linha em seu `Main` método:

         SendNotificationAsync();
         Console.ReadLine();

5. Com o emulador do Windows Phone executando e seu aplicativo fechado, defina o projeto de aplicativo de console como o projeto de inicialização padrão e, em seguida, pressione o `F5` tecla para executar o aplicativo.

    Você receberá uma notificação de envio do sistema. Tocando na faixa de sistema carrega o aplicativo.

Você pode encontrar todas as cargas possíveis os tópicos de [Catálogo de sistema] e [Catálogo de bloco] no MSDN.

##<a name="next-steps"></a>Próximas etapas

Neste exemplo simples, você transmitida notificações por push para todos os seus dispositivos Windows Phone 8. 

Para usuários específicos de destino, consulte o tutorial de [Hubs de notificação de uso para as notificações por push para os usuários] . 

Se você quiser segmento seus usuários por grupos de interesse, você pode ler [Hubs de notificação de uso para enviar as últimas notícias]. 

Saiba mais sobre como usar Hubs de notificação na [Orientação de Hubs de notificação].



<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Orientação de Hubs de notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Use Hubs de notificação para notificações de envio para os usuários]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Hubs de notificação para enviar últimas notícias]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[Catálogo de sistema]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[Catálogo de peças]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notificação Hubs - tutorial do Windows Phone Silverlight]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

