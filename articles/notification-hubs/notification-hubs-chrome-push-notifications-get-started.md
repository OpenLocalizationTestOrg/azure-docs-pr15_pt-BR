<properties
    pageTitle="Enviar notificações por push para aplicativos de Chrome com Hubs de notificação do Azure | Microsoft Azure"
    description="Saiba como usar Hubs de notificação do Azure para enviar notificações por push para um aplicativo do Chrome."
    services="notification-hubs"
    keywords="notificações push móveis, as notificações de envio por push notificação, notificações por push do chrome"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-chrome"
    ms.devlang="JavaScript"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="send-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Enviar notificações por push para aplicativos de Chrome com Hubs de notificação do Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Este tópico mostra como usar Hubs de notificação do Azure para enviar notificações por push para um aplicativo do Chrome, que será exibida dentro do contexto do navegador Google Chrome. Neste tutorial, vamos criar um aplicativo de Chrome que recebe as notificações por push usando [Mensagens de nuvem do Google (GCM)](https://developers.google.com/cloud-messaging/). 

>[AZURE.NOTE] Para concluir este tutorial, você deve ter uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).

O tutorial orienta você pelas seguintes etapas básicas para ativar as notificações de envio:

* [Habilitar o Google Cloud de mensagens](#register)
* [Configurar seu hub de notificação](#configure-hub)
* [Conectar seu aplicativo Chrome para o hub de notificação](#connect-app)
* [Enviar uma notificação por push para o seu aplicativo Chrome](#send)
* [Recursos e funcionalidade adicional](#next-steps)

>[AZURE.NOTE] Notificações de envio do aplicativo Chrome não são genéricas notificações do navegador, eles são específicos para a extensibilidade de navegador modelo (consulte [Visão geral de aplicativos do Chrome] para obter detalhes). Além do navegador de desktop, Chrome aplicativos executados no celular (Android e iOS) através de Apache Cordova. Consulte [Chrome aplicativos no celular] para saber mais.

Configurando GCM e Hubs de notificação do Azure é idêntico ao configurar para Android, como [Mensagens de nuvem do Google para o Chrome] foi substituído e a mesma GCM agora dá suporte a dispositivos Android e instâncias do Chrome.

##<a id="register"></a>Habilitar o Google Cloud de mensagens

1. Navegue até o site de [Console de nuvem do Google] , entrar com suas credenciais de conta do Google e clique no botão **Criar projeto** . Forneça um **Nome de projeto**de apropriado e, em seguida, clique no botão **criar** .

    ![Nuvem do Google Console - criar projeto][1]

2. Anote o **Número do projeto** na página **projetos** para o projeto que você acabou de criar. Você usará isso como a **ID do remetente GCM** no aplicativo Chrome para registrar GCM.

    ![Console de nuvem do Google - número do projeto][2]

3. No painel esquerdo, clique em **APIs & auth**e, em seguida, role para baixo e clique o botão de alternância para habilitar as **Mensagens de nuvem do Google para o Android**. Você não precisa habilitar **Mensagens de nuvem do Google para o Chrome**.

    ![Console de nuvem do Google - chave do servidor][3]

4. No painel esquerdo, clique em **credenciais** > **Criar nova chave** > **Chave servidor** > **criar**.

    ![Console de nuvem do Google - credenciais][4]

5. Tome nota do servidor **Chave API**. Você irá configurar isso no seu hub de notificação em seguida, para habilitá-lo enviar notificações por push para GCM.

    ![Console de nuvem do Google - chave API][5]

##<a id="configure-hub"></a>Configurar seu hub de notificação

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;6. na lâmina **configurações** , selecione **Os serviços de notificação** e **Google (GCM)**. Insira a chave de API e salvar.

&emsp;&emsp;![Notificação Azure Hubs - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

##<a id="connect-app"></a>Conectar seu aplicativo Chrome para o hub de notificação

Seu hub de notificação agora está configurado para trabalhar com GCM e você tiver as cadeias de caracteres de conexão para registrar seu aplicativo para receber e enviar notificações por push. LK

###<a name="create-a-new-chrome-app"></a>Criar um novo aplicativo de Cromado

O exemplo abaixo baseia-se no [Chrome aplicativo GCM amostra] e usa a maneira recomendada para criar um aplicativo do Chrome. Vamos destacar as etapas especificamente relacionadas à Hubs de notificação do Azure. 

>[AZURE.NOTE] Recomendamos que você baixe a fonte para este aplicativo Chrome de [Exemplo de Hub de notificação de aplicativo Chrome].

O aplicativo Chrome é criado por meio de JavaScript, e você pode usar qualquer um dos seus editores word preferencial para criá-lo. Abaixo está aparência este aplicativo Chrome.

![Aplicativo do Google Chrome][15]

1. Crie uma pasta e nomeie- `ChromePushApp`. Claro, o nome é aleatório - se você nomear-algo diferente, certifique-se de que você substitua o caminho nos segmentos de código necessária.

2. Baixe a [biblioteca de Cripto-js] na pasta que você criou na segunda etapa. Esta pasta de biblioteca conterá duas subpastas: `components` e `rollups`.

3. Criar um `manifest.json` arquivo. Todos os aplicativos de Chrome contam com um arquivo de manifesto que contém os metadados de aplicativo e, mais importante, todas as permissões que são concedidas ao aplicativo quando o usuário instala-lo.

        {
          "name": "NH-GCM Notifications",
          "description": "Chrome platform app.",
          "manifest_version": 2,
          "version": "0.1",
          "app": {
            "background": {
              "scripts": ["background.js"]
            }
          },
          "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
          "icons": { "128": "gcm_128.png" }
        }

    Observe o `permissions` elemento, que especifica que esse aplicativo Chrome poderão receber notificações por push do GCM. Ele também deverá especificar o URI de Hubs de notificação do Azure onde o aplicativo Chrome fará uma chamada REST para registrar.
    Nosso aplicativo de exemplo também usa um arquivo de ícone, `gcm_128.png`, que você encontrará a fonte que é reutilizada do exemplo GCM original. Você pode substituir qualquer imagem que atenda aos [critérios de ícone](https://developer.chrome.com/apps/manifest/icons).

4. Criar um arquivo chamado `background.js` com o seguinte código:

        // Returns a new notification ID used in the notification.
        function getNotificationId() {
          var id = Math.floor(Math.random() * 9007199254740992) + 1;
          return id.toString();
        }

        function messageReceived(message) {
          // A message is an object with a data property that
          // consists of key-value pairs.

          // Concatenate all key-value pairs to form a display string.
          var messageString = "";
          for (var key in message.data) {
            if (messageString != "")
              messageString += ", "
            messageString += key + ":" + message.data[key];
          }
          console.log("Message received: " + messageString);

          // Pop up a notification to show the GCM message.
          chrome.notifications.create(getNotificationId(), {
            title: 'GCM Message',
            iconUrl: 'gcm_128.png',
            type: 'basic',
            message: messageString
          }, function() {});
        }

        var registerWindowCreated = false;

        function firstTimeRegistration() {
          chrome.storage.local.get("registered", function(result) {

            registerWindowCreated = true;
            chrome.app.window.create(
              "register.html",
              {  width: 520,
                 height: 500,
                 frame: 'chrome'
              },
              function(appWin) {}
            );
          });
        }

        // Set up a listener for GCM message event.
        chrome.gcm.onMessage.addListener(messageReceived);

        // Set up listeners to trigger the first-time registration.
        chrome.runtime.onInstalled.addListener(firstTimeRegistration);
        chrome.runtime.onStartup.addListener(firstTimeRegistration);

    Este é o arquivo que surge a janela do aplicativo do Chrome HTML (**register.html**) e também define o manipulador **messageReceived** para lidar com a entrada notificações de envio.

5. Criar um arquivo chamado `register.html` -define a interface do usuário do aplicativo Chrome. 

   >[AZURE.NOTE] Este exemplo utiliza **v 3.1.2 CryptoJS**. Se você baixou outra versão da biblioteca, verifique se você substituir corretamente a versão no `src` caminho.

        <html>

        <head>
        <title>GCM Registration</title>
        <script src="register.js"></script>
        <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
        <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>

        <body>

        Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
        <button id="registerWithGCM">Register with GCM</button>
        <br/>
        <br/>
        <br/>

        Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
        Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

        <br/>

        <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>

        <br/>
        <br/>

        <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>

        </body>

        </html>

6. Criar um arquivo chamado `register.js` com o código a seguir. Este arquivo Especifica o script atrás `register.html`. Aplicativos do Chrome não permitir a execução de embutida, para que você precise criar um script de apoio separado para a interface do usuário.

        var registrationId = "";
        var hubName        = "", connectionString = "";
        var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

        window.onload = function() {
           document.getElementById("registerWithGCM").onclick = registerWithGCM;  
           document.getElementById("registerWithNH").onclick = registerWithNH;
           updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
        }

        function updateLog(status) {
          currentStatus = document.getElementById("console").innerHTML;
          if (currentStatus != "") {
            currentStatus = currentStatus + "\n\n";
          }

          document.getElementById("console").innerHTML = currentStatus  + status;
        }

        function registerWithGCM() {
          var senderId = document.getElementById("senderId").value.trim();
          chrome.gcm.register([senderId], registerCallback);

          // Prevent register button from being clicked again before the registration finishes.
          document.getElementById("registerWithGCM").disabled = true;
        }

        function registerCallback(regId) {
          registrationId = regId;
          document.getElementById("registerWithGCM").disabled = false;

          if (chrome.runtime.lastError) {
            // When the registration fails, handle the error and retry the
            // registration later.
            updateLog("Registration failed: " + chrome.runtime.lastError.message);
            return;
          }

          updateLog("Registration with GCM succeeded.");
          document.getElementById("registerWithNH").disabled = false;

          // Mark that the first-time registration is done.
          chrome.storage.local.set({registered: true});
        }

        function registerWithNH() {
          hubName = document.getElementById("hubName").value.trim();
          connectionString = document.getElementById("connectionString").value.trim();
          splitConnectionString();
          generateSaSToken();
          sendNHRegistrationRequest();
        }

        // From http://msdn.microsoft.com/library/dn495627.aspx
        function splitConnectionString()
        {
          var parts = connectionString.split(';');
          if (parts.length != 3)
          throw "Error parsing connection string";

          parts.forEach(function(part) {
            if (part.indexOf('Endpoint') == 0) {
            endpoint = 'https' + part.substring(11);
            } else if (part.indexOf('SharedAccessKeyName') == 0) {
            sasKeyName = part.substring(20);
            } else if (part.indexOf('SharedAccessKey') == 0) {
            sasKeyValue = part.substring(16);
            }
          });

          originalUri = endpoint + hubName;
        }

        function generateSaSToken()
        {
          targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
          var expiresInMins = 10; // 10 minute expiration

          // Set expiration in seconds.
          var expireOnDate = new Date();
          expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
          var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
            .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
            .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
            .getUTCSeconds()) / 1000;
          var tosign = targetUri + '\n' + expires;

          // Using CryptoJS.
          var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
          var base64signature = signature.toString(CryptoJS.enc.Base64);
          var base64UriEncoded = encodeURIComponent(base64signature);

          // Construct authorization string.
          sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                          + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
        }

        function sendNHRegistrationRequest()
        {
          var registrationPayload =
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                      "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                  "</GcmRegistrationDescription>" +
              "</content>" +
          "</entry>";

          // Update the payload with the registration ID obtained earlier.
          registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

          var url = originalUri + "/registrations/?api-version=2014-09";
          var client = new XMLHttpRequest();

          client.onload = function () {
            if (client.readyState == 4) {
              if (client.status == 200) {
                updateLog("Notification Hub Registration succesful!");
                updateLog(client.responseText);
              } else {
                updateLog("Notification Hub Registration did not succeed!");
                updateLog("HTTP Status: " + client.status + " : " + client.statusText);
                updateLog("HTTP Response: " + "\n" + client.responseText);
              }
            }
          };

          client.onerror = function () {
                updateLog("ERROR - Notification Hub Registration did not succeed!");
          }

          client.open("POST", url, true);
          client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
          client.setRequestHeader("Authorization", sasToken);
          client.setRequestHeader("x-ms-version", "2014-09");

          try {
              client.send(registrationPayload);
          }
          catch(err) {
              updateLog(err.message);
          }
        }

    O script acima tem os seguintes parâmetros de chave:
    - **OnLoad** define os eventos de clique de botão dos dois botões da interface do usuário. Um registra com GCM e outra usa a identificação de registro retornado após o registro com GCM para registrar com Hubs de notificação do Azure.
    - **updateLog** é a função que nos permite lidar com recursos de log simples.
    - **registerWithGCM** é o primeiro manipulador de clique de botão, que torna o `chrome.gcm.register` chamar GCM para registrar a instância atual do aplicativo do Chrome.
    - **registerCallback** é a função de retorno de chamada que é chamada quando a chamada de registro GCM retorna.
    - **registerWithNH** é o segundo manipulador de clique de botão, que registra com Hubs de notificação. Ele obtém `hubName` e `connectionString` (que o usuário tiver especificado) e monta a chamada de API de REST de registro de Hubs de notificação.
    - **splitConnectionString** e **generateSaSToken** são auxiliares que representam a implementação de JavaScript de um processo de criação de token SaS, que deve ser usado em todas as chamadas de API REST. Para obter mais informações, consulte [Conceitos comuns](http://msdn.microsoft.com/library/dn495627.aspx).
    - **sendNHRegistrationRequest** é a função que torna um HTTP (REST) chamar Hubs de notificação do Azure.
    - **registrationPayload** define a carga XML de registro. Para obter mais informações, consulte [Criar API REST de NH de registro]. Podemos atualizar a identificação de registro com o que recebemos de GCM.
    - **cliente** é uma instância de **XMLHttpRequest** que usamos para fazer a solicitação de HTTP POST. Observe que podemos atualizar o `Authorization` cabeçalho com `sasToken`. Conclusão bem-sucedida desta chamada registrará esta instância do aplicativo Chrome com Hubs de notificação do Azure.


A estrutura de pasta geral para esse projeto deve ser semelhante a esta:     ![Google Chrome aplicativo - estrutura da pasta][21]

###<a name="set-up-and-test-your-chrome-app"></a>Configurar e testar seu aplicativo Chrome

1. Abra o navegador Chrome. Abrir **as extensões de Chrome** e habilitar o **modo de desenvolvedor**.

    ![Google Chrome - habilitar o modo de desenvolvedor][16]

2. Clique em **carregar a extensão descompactado** e navegue até a pasta onde você criou os arquivos. Você pode também opcionalmente usar o **Chrome aplicativos & ferramenta de desenvolvedor de extensões**. Essa ferramenta é um aplicativo de Chrome em si (instalado do repositório Web Chrome) e fornece recursos avançados de depuração para o desenvolvimento de aplicativos do Chrome.

    ![Google Chrome - carregar a extensão descompactado][17]

3. Se o aplicativo Chrome for criado sem erros, em seguida, você verá seu aplicativo Chrome aparecem.

    ![Google Chrome - Chrome aplicativo exibição][18]

4. Insira o **Número do projeto** obtido anteriormente no **Console de nuvem do Google** como a ID do remetente e clique em **registrar com GCM**. Você deve ver a mensagem **registro com GCM bem-sucedida.**

    ![Google Chrome - Chrome personalização de aplicativo][19]

5. Insira seu **Nome de Hub de notificação** e **DefaultListenSharedAccessSignature** que você adquiriu a partir do portal anteriormente e clique em **registrar com Hub de notificação do Azure**. Você deve ver a mensagem **o registro de notificação de Hub bem-sucedido!** e os detalhes da resposta de registro, que contém o registro de Hubs de notificação do Azure ID.

    ![Google Chrome - especificar detalhes de Hub de notificação][20]  

##<a name="send"></a>Enviar uma notificação para o seu aplicativo Chrome

Para fins de teste, enviaremos aplicativo de console de notificações de envio do Chrome usando um .NET. 

>[AZURE.NOTE] Você pode enviar notificações de envio com Hubs de notificação de qualquer back-end por meio de nosso pública <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>. Confira nosso [portal da documentação](https://azure.microsoft.com/documentation/services/notification-hubs/) para obter mais exemplos de entre plataformas.

1. No Visual Studio, no menu **arquivo** , selecione **novo** e **projeto**. Em **Visual c#**, clique em **Windows** e o **Aplicativo de Console**e clique em **Okey**.  Isso cria um novo projeto de aplicativo de console.

2. No menu **Ferramentas** , clique em **Gerenciador de pacote de biblioteca** e, em seguida, **Package Manager Console**. Isso exibe o Package Manager Console.

3. Na janela do console, execute o seguinte comando:

        Install-Package Microsoft.Azure.NotificationHubs

    Isso adiciona uma referência para o SDK de barramento de serviço do Azure com o <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">pacote do WindowsAzure.ServiceBus NuGet</a>.

4. Abrir `Program.cs` e adicione o seguinte `using` instrução:

        using Microsoft.Azure.NotificationHubs;

5. No `Program` classe, adicione o seguinte método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

    Certifique-se de substituir o `<hub name>` espaço reservado com o nome do hub notificação que aparece no [portal](https://portal.azure.com) em seu blade Hub de notificação. Além disso, substitua o espaço reservado cadeia de conexão com a cadeia de conexão chamada `DefaultFullSharedAccessSignature` obtido na seção de configuração do hub de notificação.

    >[AZURE.NOTE] Certifique-se de que você use a cadeia de conexão com acesso **total** , acesso não **ouvir** . A cadeia de conexão de acesso **ouvir** não conceder permissões para enviar notificações por push.

5. Adicione as seguintes chamadas no `Main` método:

         SendNotificationAsync();
         Console.ReadLine();
         
6. Certifique-se de que está executando o Chrome e executar o aplicativo de console.

7. Você verá a seguinte notificação pop-up na área de trabalho.

    ![Google Chrome - notificação][13]

8. Você também pode ver todas as suas notificações usando a janela de notificações de Chrome na barra de tarefas (no Windows) quando o Chrome está sendo executado.

    ![Google Chrome - lista de notificações][14]

>[AZURE.NOTE] Não é necessário ter a execução de aplicativo do Chrome ou abrir no navegador (embora deve estar executando o próprio navegador Chrome). Você também pode obter uma visão consolidada de todas as suas notificações na janela do Chrome notificações.

## <a name="next-steps"> </a>Próximos passos

Saiba mais sobre Hubs de notificação na [Visão geral de Hubs de notificação].

Para direcionar usuários específicos, consulte o tutorial do [Azure notificação Hubs notificar usuários] . 

Se você quiser segmento seus usuários por grupos de interesse, você pode seguir o tutorial de [Hubs de notificação do Azure últimas notícias] .

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Exemplo de Hub de notificação de aplicativo Chrome]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Console de nuvem do Google]: http://cloud.google.com/console
[Azure Classic Portal]: https://manage.windowsazure.com/
[Visão geral de Hubs de notificação]: notification-hubs-push-notification-overview.md
[Visão geral de aplicativos do Chrome]: https://developer.chrome.com/apps/about_apps
[Amostra de aplicativo GCM Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Chrome aplicativos no celular]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Criar API REST de registro NH]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[biblioteca de Cripto-js]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Nuvem do Google para o Chrome de mensagens]: https://developer.chrome.com/apps/cloudMessagingV1
[Notificação Azure Hubs notificam os usuários]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Notícias de Azure Hubs de notificação]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
