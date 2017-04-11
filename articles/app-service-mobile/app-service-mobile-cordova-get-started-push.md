<properties
    pageTitle="Adicionar notificações por Push Apache Cordova aplicativo com aplicativos móveis do Azure | Serviço de aplicativo do Azure"
    description="Saiba como usar aplicativos do Azure Mobile para enviar notificações por push para o seu aplicativo Apache Cordova."
    services="app-service\mobile"
    documentationCenter="javascript"
    manager="erikre"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Adicionar notificações por push para o seu aplicativo Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Visão geral

Neste tutorial, você adicionar notificações por push para o projeto [Iniciar Apache Cordova rápido] para que uma notificação de envio é enviada para o dispositivo sempre que um registro é inserido.

Se você não usa o projeto de servidor de início rápido baixado, você precisará do pacote de extensão de notificação de envio. Consulte [trabalhar com o servidor de back-end do .NET SDK para aplicativos do Azure móvel](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

##<a name="prerequisites"></a>Pré-requisitos

Este tutorial apresenta um aplicativo de Cordova Apache desenvolvido com o Visual Studio de 2015 que é executada no Google Android emulador, um dispositivo Android, um dispositivo com Windows e um dispositivo iOS.

Para concluir este tutorial, você precisa:

* Um PC com [2015 de comunidade do Visual Studio] ou versões posteriores.
* [Ferramentas do visual Studio para Apache Cordova].
* Uma [conta do Microsoft Azure active](https://azure.microsoft.com/pricing/free-trial/).
* Um projeto [Apache Cordova início rápido] concluído.
* (Android) Uma [conta do Google] com um endereço de email verificado.
* (iOS) Uma associação de programa de desenvolvedor do Apple e um dispositivo iOS (iOS Simulator não suporta envio).
* (Windows) Uma conta de desenvolvedor do Windows Store e um dispositivo com Windows 10.

##<a name="configure-hub"></a>Configurar um hub de notificação

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Assista a um vídeo mostrando as etapas nesta seção](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub)

##<a name="update-the-server-project-to-send-push-notifications"></a>Atualizar o projeto de servidor para enviar as notificações por push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="add-push-to-app"></a>Modificar seu aplicativo Cordova para receber notificações por push

Você deve garantir que seu projeto de aplicativo Apache Cordova está pronto para lidar com notificações por push ao instalar o plug-in Cordova push além de quaisquer serviços de envio de plataforma específica.

#### <a name="update-the-cordova-version-in-your-project"></a>Atualize a versão de Cordova em seu projeto.

É recomendável que você atualize o projeto de cliente para Cordova 6.1.1 se seu projeto está configurado usando uma versão anterior. Para atualizar o projeto, clique com botão direito config para abrir o designer de configuração. Selecione a guia plataformas e escolha 6.1.1 na caixa de texto **Cordova CLI** .

Escolha **Construir**, então **Criar solução** para atualizar o projeto.

#### <a name="install-the-push-plugin"></a>Instalar o plug-in push

Apache Cordova aplicativos nativamente não manipular os recursos de dispositivo ou rede.  Esses recursos são fornecidos pelo plug-ins que são publicadas em [npm](https://www.npmjs.com/) ou em GitHub.  O `phonegap-plugin-push` plug-in é usado para tratar notificações de envio de rede.

Você pode instalar o plug-in de envio de uma destas maneiras:

**No prompt de comando:**

Execute o seguinte comando:

    cordova plugin add phonegap-plugin-push

**De dentro do Visual Studio:**

1.  No Solution Explorer, abra a `config.xml` arquivo, clique em **Plug-ins** > **personalizada**, selecione **gito** como a fonte de instalação, em seguida, digite `https://github.com/phonegap/phonegap-plugin-push` como fonte.

    ![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)

2.  Clique na seta ao lado da fonte de instalação.

3. Em **SENDER_ID**, se você já tiver uma ID de projeto numérico para o projeto de Console de desenvolvedor do Google, você pode adicioná-la aqui. Caso contrário, insira um valor de espaço reservado, como 777777, e se você está destinando Android você pode atualizar esse valor config posteriormente.

4. Clique em **Adicionar**.

O plug-in push agora está instalado.

####<a name="install-the-device-plugin"></a>Instalar o plug-in do dispositivo

Siga o mesmo procedimento que você usou para instalar o plug-in push, mas você encontrará o plug-in do dispositivo na lista de plug-ins Core (clique em **Plug-ins** > **Core** para encontrá-lo). Você precisa este plug-in para obter o nome da plataforma (`device.platform`).

#### <a name="register-your-device-for-push-on-start-up"></a>Registrar seu dispositivo para envio na inicialização

Inicialmente, podemos incluirá alguns mínimo de código para Android. Posteriormente, podemos fará algumas modificações pequenas para ser executado no iOS ou Windows 10.

1. Adicione uma chamada para **registerForPushNotifications** durante o retorno de chamada para o processo de login, ou na parte inferior do método **onDeviceReady** :

        // Login to the service.
        client.login('google')
            .then(function () {
                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                    // Added to register for push notifications.
                registerForPushNotifications();

            }, handleError);

    Este exemplo mostra chamando **registerForPushNotifications** após autenticação é bem sucedida, o que é recomendado ao usar notificações por push e autenticação em seu aplicativo.

2. Adicione o novo método de **registerForPushNotifications** da seguinte maneira:

        // Register for Push Notifications. Requires that phonegap-plugin-push be installed.
        var pushRegistration = null;
        function registerForPushNotifications() {
          pushRegistration = PushNotification.init({
              android: { senderID: 'Your_Project_ID' },
              ios: { alert: 'true', badge: 'true', sound: 'true' },
              wns: {}
          });

        // Handle the registration event.
        pushRegistration.on('registration', function (data) {
          // Get the native platform of the device.
          var platform = device.platform;
          // Get the handle returned during registration.
          var handle = data.registrationId;
          // Set the device-specific message template.
          if (platform == 'android' || platform == 'Android') {
              // Register for GCM notifications.
              client.push.register('gcm', handle, {
                  mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'iOS') {
              // Register for notifications.            
              client.push.register('apns', handle, {
                  mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'windows') {
              // Register for WNS notifications.
              client.push.register('wns', handle, {
                  myTemplate: {
                      body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                      headers: { 'X-WNS-Type': 'wns/toast' } }
              });
          }
        });

        pushRegistration.on('notification', function (data, d2) {
          alert('Push Received: ' + data.message);
        });

        pushRegistration.on('error', handleError);
        }

3. (Android) No código acima, substitua `Your_Project_ID` com o valor numérico ID de projeto para o aplicativo do [Console de desenvolvedor do Google].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Opcional) Configurar e executar o aplicativo no Android

Conclua esta seção para ativar as notificações por push para o Android.

####<a name="enable-gcm"></a>Habilitar Firebase nuvem de mensagens

Desde que pretendemos a plataforma do Google Android inicialmente, você deve habilitar a nuvem de Firebase mensagens. Da mesma forma, se você estava direcionamento de dispositivos do Microsoft Windows, você deve ativar o suporte WNS.

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

####<a name="configure-backend"></a>Configurar o back-end do aplicativo Mobile para enviar solicitações de envio usando FCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

####<a name="configure-your-cordova-app-for-android"></a>Configurar seu aplicativo de Cordova para Android

Em seu aplicativo de Cordova, abra config e substitua `Your_Project_ID` com o valor numérico ID de projeto para o aplicativo do [Console de desenvolvedor do Google].

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

Abrir js e atualizar o código para usar sua ID de projeto numéricos.

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

####<a name="configure-device"></a>Configurar seu dispositivo Android para depuração de USB

Antes de implantar seu aplicativo para seu dispositivo Android, você precisa habilitar a depuração de USB.  Execute as etapas a seguir em seu telefone Android:

1. Vá para **configurações** > **sobre o telefone**, em seguida, toque em **número de compilação** até o modo de desenvolvedor está habilitado (cerca de 7 vezes).

2. Em **configurações** > **Opções do desenvolvedor** habilitar a **depuração de USB**e conecte seu telefone Android para o desenvolvimento do seu computador com um cabo USB.

Testamos isso usando um dispositivo de X Google Nexus 5 executando o Android 6.0 (Marshmallow).  No entanto, as técnicas são comuns em qualquer versão Android moderna.

#### <a name="install-google-play-services"></a>Instale os serviços do Google Play

O plug-in push depende Android Google Play Services para notificações por push.  

1.  No **Visual Studio**, clique em **Ferramentas** > **Android** > **Gerenciador de SDK do Android**, expanda a pasta **Extras** e marque a caixa para certificar-se de que cada um dos seguintes SDKs está instalada.
    * Android 2.3 ou superior
    * Revisão do repositório do Google 27 ou superior
    * Serviços do Google Play 9.0.2 ou superior

2.  Clique em **Instalar pacotes** e aguarde a conclusão da instalação.

Atual necessária bibliotecas estão listadas na [documentação de instalação phonegap de plug-in de envio].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Notificações de envio de teste no aplicativo no Android

Você pode agora teste as notificações por push executando o aplicativo e inserindo itens na tabela TodoItem. Você pode fazer isso do mesmo dispositivo ou de um segundo dispositivo, de enquanto você estiver usando o mesmo back-end. Teste seu aplicativo de Cordova na plataforma Android em uma das seguintes maneiras:

- **Em um dispositivo físico:**  
Anexe seu dispositivo Android com seu computador de desenvolvimento usando um cabo USB.  Em vez de **Google Android emulador**, selecione o **dispositivo**. Visual Studio irá implantar o aplicativo no dispositivo e executá-lo.  Você pode interagir com o aplicativo no dispositivo.  
Aprimorar sua experiência de desenvolvimento.  Compartilhamento de aplicativos, como o [Mobizen] de tela pode ajudá-lo no desenvolvimento de um aplicativo do Android, Projetando sua tela Android para um navegador da web em seu PC.

- **Em um emulador Android:**  
Há etapas de configuração adicionais necessárias durante a execução em um emulador.

    Certifique-se de que você está implantando ou depuração em um dispositivo virtual que tenha Google APIs definida como destino, conforme mostrado abaixo no Gerenciador de dispositivo Android Virtual (AVD).

    ![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Se você quiser usar um x86 mais rápido emulador, [Instale o driver HAXM](https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM) e configurar o emulador usá-lo.

    Adicionar uma conta do Google para o dispositivo Android clicando em **aplicativos** > **configurações** > **Adicionar conta**, em seguida, siga as instruções para adicionar um Google existente de conta no dispositivo (recomendamos usando uma conta existente em vez de criar um novo).

    ![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Executar o aplicativo de lista de tarefas pendentes como antes e inserir um novo item de todo. Desta vez, um ícone de notificação é exibido na área de notificação. Você pode abrir a registradora de notificação para exibir o texto completo da notificação.

    ![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

##<a name="optional-configure-and-run-on-ios"></a>(Opcional) Configurar e executar o IOS

Esta seção é para execução do projeto Cordova em dispositivos iOS. Se não estiver trabalhando com dispositivos iOS, você pode pular esta seção.

####<a name="install-and-run-the-ios-remotebuild-agent-on-a-mac-or-cloud-service"></a>Instalar e executar o agente de remotebuild iOS em um serviço de Mac ou na nuvem

Antes de poder executar um aplicativo de Cordova IOS usando o Visual Studio, percorra as etapas no [iOS guia de instalação](http://taco.visualstudio.com/en-us/docs/ios-guide/) para instalar e executar o agente de remotebuild.

Certificar-se de que você pode criar o aplicativo do iOS. As etapas no guia de configuração são necessárias para criar para iOS do Visual Studio. Se você não tem um Mac, você pode criar para iOS usando o agente de remotebuild em um serviço como MacInCloud. Para obter mais informações, consulte [executar seu aplicativo iOS na nuvem](http://taco.visualstudio.com/en-us/docs/build_ios_cloud/).

>[AZURE.NOTE] XCode 7 ou superior é necessário para usar o plug-in push IOS.

####<a name="find-the-id-to-use-as-your-app-id"></a>Localizar a identificação para usar como sua ID de aplicativo

Antes de você registrar seu aplicativo notificações de envio, config aberto em seu aplicativo de Cordova, encontre o `id` valor no elemento de widget do atributo e copie-o para uso posterior. No seguinte XML, a identificação é `io.cordova.myapp7777777`.

        <widget defaultlocale="en-US" id="io.cordova.myapp7777777"
        version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
            xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

Posteriormente, use esse identificador quando você cria uma ID de aplicativo no portal do desenvolvedor da Apple. (Se você criar uma ID de aplicativo diferente no portal do desenvolvedor e deseja usá-lo, você precisará fazer algumas etapas adicionais posteriormente neste tutorial para alterar essa ID config. A ID no elemento de widget deve corresponder a ID de aplicativo no portal do desenvolvedor.)

####<a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrar o aplicativo para notificações por push no portal do desenvolvedor da Apple

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Assista a um vídeo mostrando etapas semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

####<a name="configure-azure-to-send-push-notifications"></a>Configurar o Azure para enviar notificações por push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

####<a name="verify-that-your-app-id-matches-your-cordova-app"></a>Verifique se sua ID de aplicativo corresponde seu aplicativo Cordova

Se a ID de aplicativo que você criou na sua conta de desenvolvedor do Apple já corresponde a ID do elemento widget config, você pode ignorar esta etapa. No entanto, se as identificações não corresponderem, siga estas etapas:

1. Exclua a pasta de plataformas de seu projeto.

2. Exclua a pasta de plug-ins do seu projeto.

3. Exclua a pasta node_modules do seu projeto.

4. Atualize o atributo id do elemento widget config para usar a ID de aplicativo que você criou na sua conta de desenvolvedor do Apple.

5. Recrie o projeto.

#####<a name="test-push-notifications-in-your-ios-app"></a>Teste as notificações por push em seu aplicativo iOS

1. No Visual Studio, certifique-se de que **iOS** está selecionado como o destino de implantação e escolha o **dispositivo** para executar em seu dispositivo iOS conectada.

    Você pode executar em um dispositivo iOS conectado ao seu PC usando o iTunes. O iOS Simulator não dá suporte a notificações por push.

2. Pressione o botão **Executar** ou **F5** no Visual Studio para criar o projeto e iniciar o aplicativo em um dispositivo iOS, clique em **Okey** para aceitar as notificações por push.

    >[AZURE.NOTE] Explicitamente você deve aceitar as notificações por push de seu aplicativo. Esta solicitação ocorre apenas na primeira vez que o aplicativo é executado.

3. No aplicativo, digite uma tarefa e, em seguida, clique no sinal de adição (+) ícone.

4. Verifique se uma notificação for recebida e clique em Okey para descartar a notificação.

##<a name="optional-configure-and-run-on-windows"></a>(Opcional) Configurar e executar no Windows

Esta seção é para executar o projeto de aplicativo do Apache Cordova em dispositivos Windows 10 (o plug-in PhoneGap push é suportado no Windows 10). Você pode pular esta seção se você não estiver trabalhando com dispositivos Windows.

####<a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrar seu aplicativo do Windows para notificações por push WNS

Para usar as opções de armazenamento no Visual Studio, selecione um destino Windows na lista de plataformas de solução, como o **Windows x64** ou **Windows x86** (Evite **AnyCPU do Windows** para notificações por push).

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Assista a um vídeo mostrando etapas semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push)

####<a name="configure-the-notification-hub-for-wns"></a>Configurar o hub de notificação para WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

####<a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Configurar seu aplicativo de Cordova para oferecer suporte a notificações de envio do Windows

Abrir o designer de configuração (com o botão direito em config. XML e selecione **View Designer**), selecione a guia do **Windows** e escolha o **Windows 10** em **Versão de destino do Windows**.

>[AZURE.NOTE] Se você estiver usando uma versão de Cordova antes de Cordova 5.1.1 (6.1.1 recomendado), você também deve definir o sinalizador capaz de sistema para true config.

Para dar suporte ao envio notificações no seu padrão (depuração) constrói, arquivo de build.json aberto. Copie a configuração de "lançamento" à sua configuração de depuração.

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

Após a atualização, o código anterior deve esta aparência.

    "windows": {
        "release": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            },
        "debug": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            }
        }

Criar o aplicativo e verifique se você tem sem erros. Aplicativo de cliente você agora deve registrar as notificações do aplicativo Mobile back-end. Repita esta seção para cada projeto do Windows em sua solução.

####<a name="test-push-notifications-in-your-windows-app"></a>Teste as notificações por push em seu aplicativo do Windows

No Visual Studio, certifique-se de que uma plataforma Windows seja selecionada como o destino de implantação, como o **Windows x64** ou **Windows x86**. Para executar o aplicativo em um PC com Windows 10 hospedagem Visual Studio, escolha **Máquina Local**.

Pressione o botão de execução para criar o projeto e iniciar o aplicativo.

No aplicativo, digite um nome para um novo todoitem e, em seguida, clique no sinal de adição (+) ícone para adicioná-lo.

Verifique se que uma notificação é recebida quando o item é adicionado.

##<a name="next-steps"></a>Próximas etapas

* Leia sobre [Hubs de notificação] para saber mais sobre as notificações por push.
* Se você ainda não o fez, continue o tutorial pela [Adição de autenticação] para o seu aplicativo Apache Cordova.

Saiba como usar o SDK do.

* [Apache Cordova SDK]
* [SDK do servidor do ASP.NET]
* [SDK do servidor Node]

<!-- URLs -->
[Adicionando autenticação]: app-service-mobile-cordova-get-started-users.md
[Início rápido do Apache Cordova]: app-service-mobile-cordova-get-started.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Conta do Google]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Console de desenvolvedor do Google]: https://console.developers.google.com/home/dashboard
[documentação de instalação PhoneGap de plug-in de envio]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Comunidade do Visual Studio de 2015]: http://www.visualstudio.com/
[Ferramentas do Visual Studio para Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Hubs de notificação]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[SDK do servidor do ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[SDK do servidor Node]: app-service-mobile-node-backend-how-to-use-server-sdk.md
