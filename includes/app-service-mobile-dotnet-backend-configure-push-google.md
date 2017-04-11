Use o procedimento que corresponde ao seu tipo de projeto de back-end&mdash; [.NET back-end](#dotnet) ou [back-end node](#nodejs).

### <a name="dotnet"></a>Projeto de back-end do .NET

1. No Visual Studio, clique com botão direito do projeto do servidor e clique em **Gerenciar pacotes NuGet**, procure por `Microsoft.Azure.NotificationHubs`, clique em **instalar**. Isso instala a biblioteca de cliente Hubs de notificação.

2. Na pasta controladores, abra TodoItemController.cs e adicione o seguinte `using` instruções:

        using Microsoft.Azure.Mobile.Server.Config;
        using Microsoft.Azure.NotificationHubs;

3. Substituir o `PostTodoItem` método com o seguinte código:  

      
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
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

            // Android payload
            var androidNotificationPayload = "{ \"data\" : {\"message\":\"" + item.Text + "\"}}";

            try
            {
                // Send the push notification and log the results.
                var result = await hub.SendGcmNativeNotificationAsync(androidNotificationPayload);

                // Write the success result to the logs.
                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                // Write the failure result to the logs.
                config.Services.GetTraceWriter()
                    .Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

4. Republicar o project server.

### <a name="nodejs"></a>Projeto de back-end node

1. Se você ainda não fez isso, [Baixe o projeto de início rápido](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ou então usa o [editor de online no portal do Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
 
1. Substitua o código existente no arquivo todoitem.js com o seguinte:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');
        
        var table = azureMobileApps.table();
        
        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK, 
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');
        
        // Define the GCM payload.
        var payload = {
            "data": {
                "message": context.item.text
            }
        };   
        
        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a GCM native notification.
                    context.push.gcm.send(null, payload, function (error) {
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

    Isso envia uma notificação de GCM que contém o item.text quando um novo item de todo é inserido. 

2. Ao editar o arquivo no seu computador local, republicar o project server. 
