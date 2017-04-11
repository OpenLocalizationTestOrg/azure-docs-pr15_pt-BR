Nesta seção, você pode atualizar código no seu projeto de back-end aplicativos Mobile existente para enviar uma notificação de envio sempre que um novo item é adicionado. Isso é ativado pelo recurso de [modelo](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) de notificação dos Hubs, permitindo que coloca entre plataformas. Os vários clientes são registrados para notificações de envio usando modelos e uma única push universal pode acessar todas as plataformas de cliente.

Escolha o procedimento a seguir que corresponde ao seu tipo de projeto de back-end&mdash; [.NET back-end](#dotnet) ou [back-end node](#nodejs).

### <a name="dotnet"></a>Projeto de back-end do .NET
1. No Visual Studio, clique com botão direito do projeto do servidor e clique em **Gerenciar pacotes NuGet**, procure por `Microsoft.Azure.NotificationHubs`, clique em **instalar**. Isso instala a biblioteca de Hubs de notificação para enviar notificações do seu back-end.

3. No project server, abra **controladores** > **TodoItemController.cs**e adicione o seguinte usando instruções:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
    

2. No método **PostTodoItem** , adicione o seguinte código após a chamada para **InsertAsync**:  

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Isso envia uma notificação de modelo que contém o item. Texto quando um novo item é inserido.

4. Republicar o project server. 

### <a name="nodejs"></a>Projeto de back-end node

1. Se você ainda não fez isso, [Baixe o projeto de back-end do início rápido](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ou então usa o [editor de online no portal do Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).

2. Substitua o código existente no todoitem.js com o seguinte:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');
    
        var table = azureMobileApps.table();
        
        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK, 
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');
        
        // Define the template payload.
        var payload = '{"messageParam": "' + context.item.text + '" }';  
        
        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a template notification.
                    context.push.send(null, payload, function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.info('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });

        module.exports = table;  

    Isso envia uma notificação de modelo que contém o item.text quando um novo item é inserido.

2. Ao editar o arquivo em seu computador local, republicar o project server.
