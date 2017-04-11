<properties
    pageTitle="Enviar notificações de plataforma cruzada aos usuários com Hubs de notificação (ASP.NET)"
    description="Saiba como usar modelos de Hubs de notificação para enviar, em uma única solicitação, uma notificação de independente de plataforma que direciona todas as plataformas."
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/03/2016" 
    ms.author="yuaxu"/>

# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Enviar notificações de plataforma cruzada para usuários com Hubs de notificação


No tutorial anterior [Notificar usuários com Hubs de notificação], você aprendeu a notificações por push para todos os dispositivos registrados por um usuário autenticado específico. Esse tutorial, várias solicitações foram necessárias para enviar uma notificação para cada plataforma do cliente com suporte. Notificação Hubs dá suporte a modelos, que permitem que você especifique como deseja receber notificações de um dispositivo específico. Isso simplifica enviar notificações entre plataformas. Este tópico demonstra como tirar proveito dos modelos para enviar, em uma única solicitação, uma notificação de independente de plataforma que direciona todas as plataformas. Para obter mais informações sobre modelos, consulte [Visão geral de Hubs de notificação do Azure][Templates].

> [AZURE.NOTE] Hubs de notificação permite que um dispositivo registrar vários modelos com a mesma marca. Nesse caso, uma mensagem de entrada direcionamento essa marca resulta em várias notificações entregues para o dispositivo, um para cada modelo. Isso permite exibir a mesma mensagem em várias notificações visuais, como ambos como um selo e uma notificação do sistema em um aplicativo da Windows Store.

Conclua as seguintes etapas para enviar notificações de várias plataformas usando modelos:

1. No Explorador de solução no Visual Studio, expanda a pasta de **controladores** e abra o arquivo RegisterController.cs.

2. Localize o bloco de código no método **postagem** que cria uma nova substituição de registro do `switch` conteúdo com o seguinte código:

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

    Este código chama o método específicos de plataforma para criar um registro de modelo em vez de um registro nativo. Registros existentes não precisam ser modificados porque registros de modelo derivam de registros nativos.

3. No controlador de **notificações** , substitua o método de **sendNotification** com o seguinte código:

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;

            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

    Este código envia uma notificação para todas as plataformas ao mesmo tempo e sem precisar especificar uma carga nativa. Notificação Hubs cria e oferece a carga correta para cada dispositivo com o valor fornecido _marca_ , conforme especificado nos modelos registrados.

4. Republicar seu projeto de back-end WebApi.

5. Execute o aplicativo cliente novamente e verificar se o registro teve êxito.

6. (Opcional) Implantar o aplicativo cliente em um segundo dispositivo e, em seguida, executar o aplicativo.

    Observe que uma notificação é exibida em cada dispositivo.

## <a name="next-steps"></a>Próximas etapas

Agora que você concluiu este tutorial, saiba mais sobre como Hubs de notificação e modelos nestes tópicos:

+ **[Use Hubs de notificação para enviar últimas notícias]** <br/>Demonstra outra situação para usar modelos

+  **[Visão geral de Hubs de notificação do Azure][Templates]**<br/>Tópico de visão geral tem informações mais detalhadas sobre modelos.


<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Use Hubs de notificação para enviar últimas notícias]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Notificar os usuários com Hubs de notificação]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
