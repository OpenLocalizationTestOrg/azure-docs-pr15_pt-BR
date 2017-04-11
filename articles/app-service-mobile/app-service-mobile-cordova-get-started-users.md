<properties
    pageTitle="Adicionar autenticação em Apache Cordova aos aplicativos Mobile | Serviço de aplicativo do Azure"
    description="Saiba como usar aplicativos Mobile no serviço de aplicativo do Azure para autenticar os usuários do aplicativo Apache Cordova por meio de uma variedade de provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-apache-cordova-app"></a>Adicione autenticação ao seu aplicativo Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
    
## <a name="summary"></a>Resumo

Neste tutorial, você pode adicionar autenticação para o projeto de início rápido de lista de tarefas pendentes no Apache Cordova usando um provedor de identidade que tem suporte. Este tutorial se baseia o tutorial de [Introdução aos aplicativos Mobile] , que devem ser concluídas primeiro.

##<a name="register"></a>Registrar seu aplicativo para autenticação e configurar o serviço de aplicativo

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Assista a um vídeo mostrando etapas semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

##<a name="permissions"></a>Restringir permissões para usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Agora, você pode confirmar que o acesso anônimo a seu back-end foi desativado. No Visual Studio, abra o projeto que você criou quando você concluiu o tutorial [Introdução aos aplicativos Mobile], e em seguida, execute o aplicativo no **Google Android emulador** e verifique se uma falha de Conexão inesperada é mostrada depois que o aplicativo for iniciado.

Em seguida, você irá atualizar o aplicativo para autenticar os usuários antes de solicitar recursos do aplicativo Mobile back-end.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

1. Abra o projeto no **Visual Studio**, abra o `www/index.html` arquivo para edição.

2. Localize o `Content-Security-Policy` metamarca na seção de cabeçalho.  Você precisará adicionar o host OAuth à lista de fontes de permitidos.

  	| Provedor               | Nome do provedor SDK | OAuth Host                  |
  	| :--------------------- | :---------------- | :-------------------------- |
  	| Active Directory do Azure | AAD               | https://login.Windows.NET   |
  	| Facebook               | Facebook          | https://www.Facebook.com    |
  	| Google                 | Google            | https://Accounts.Google.com |
  	| Microsoft              | MicrosoftAccount  | https://login.Live.com      |
  	| Twitter                | Twitter           | https://API.twitter.com     |

    Um exemplo de conteúdo-política de segurança (implementado para Azure Active Directory) é o seguinte:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.windows.net https://yourapp.azurewebsites.net; style-src 'self'">

    Você deve substituir `https://login.windows.net` com o host OAuth da tabela acima.  Consulte a [documentação de política de segurança de conteúdo] para obter mais informações sobre essa marca meta.

    Observe que alguns provedores de autenticação não exigem que alterações de política de segurança de conteúdo quando usada em dispositivos móveis apropriados.  Por exemplo, nenhuma alteração de política de segurança de conteúdo é necessárias ao usar a autenticação do Google em um dispositivo Android.

3. Abrir o `www/js/index.js` para edição de arquivos, localize o `onDeviceReady()` método, e sob a criação de cliente código adicione o seguinte:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Observe que este código substitua o código existente que cria a referência de tabela e atualiza a interface do usuário.

    O método de login () inicia autenticação com o provedor. O método de login () é uma função de assíncrono que retorna uma promessa de JavaScript.  O restante da inicialização é colocado dentro da resposta promessa para que ele não é executado até que o método de login () seja concluída.

4. No código que você acabou de adicionar, substitua `SDK_Provider_Name` com o nome do seu provedor de login. Por exemplo, para o Active Directory do Azure, use `client.login('aad')`.

4. Execute o seu projeto.  Quando o projeto concluiu a inicialização, o seu aplicativo mostrará a página de login OAuth para o provedor de autenticação escolhido.

##<a name="next-steps"></a>Próximas etapas

* Saiba mais [Sobre autenticação] com o serviço de aplicativo do Azure.
* Continue o tutorial adicionando [Notificações por Push] para o seu aplicativo Apache Cordova.

Saiba como usar o SDK do.

* [Apache Cordova SDK]
* [SDK do servidor do ASP.NET]
* [SDK do servidor Node]

<!-- URLs. -->
[Introdução aos aplicativos Mobile]: app-service-mobile-cordova-get-started.md
[Documentação de política de segurança de conteúdo]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Notificações por push]: app-service-mobile-cordova-get-started-push.md
[Sobre a autenticação]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md 
[SDK do servidor do ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[SDK do servidor Node]: app-service-mobile-node-backend-how-to-use-server-sdk.md
