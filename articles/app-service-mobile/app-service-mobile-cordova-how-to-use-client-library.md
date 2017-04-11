<properties
    pageTitle="Como usar o plug-in do Apache Cordova para aplicativos móveis do Azure"
    description="Como usar o plug-in do Apache Cordova para aplicativos móveis do Azure"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Como usar a biblioteca de cliente de Cordova Apache para aplicativos móveis do Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia ensina a executar cenários comuns usando o mais recente [Apache Cordova plug-in para aplicativos do Azure móvel]. Se você estiver começando a usar os aplicativos do Azure Mobile, primeiro completa [Início rápido do Azure Mobile aplicativos] para criar um back-end, criar uma tabela e fazer o download de um projeto de Apache Cordova previamente criado. Neste guia, podemos focalizar o plug-in do lado do cliente Apache Cordova.

## <a name="supported-platforms"></a>Plataformas com suporte

Este SDK suporta Apache Cordova v6.0.0 e posterior no iOS, Android e Windows dispositivos.  O suporte de plataforma é da seguinte maneira:

* API Android 19-24 (KitKat por meio de Nougat)
* iOS versões 8.0 e posteriores.
* Windows Phone 8.0
* Windows Phone 8.1
* Plataforma Windows universal

##<a name="Setup"></a>Pré-requisitos e configuração

Este guia pressupõe que você tenha criado um back-end com uma tabela. Este guia pressupõe que a tabela tenha o mesmo esquema as tabelas esses tutoriais. Este guia também pressupõe que você tenha adicionado o plug-in do Apache Cordova ao seu código.  Se você não tiver feito isso, você pode adicionar o plug-in Apache Cordova ao seu projeto na linha de comando:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Para obter mais informações sobre como criar [seu primeiro aplicativo Apache Cordova], consulte sua documentação.

[AZURE.INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]


##<a name="auth"></a>Como: autenticar os usuários

Serviço de aplicativo do Azure dá suporte a autenticação e a autorização de usuários de aplicativo usando vários provedores de identidade externa: Facebook, Google, Account da Microsoft e Twitter. Você pode definir permissões em tabelas para restringir o acesso para operações específicas somente para usuários autenticados. Você também pode usar a identidade dos usuários autenticados implementar regras de autorização em scripts de servidor. Para obter mais informações, consulte o tutorial de [Introdução ao autenticação] .

Ao usar autenticação em um aplicativo do Apache Cordova, os seguintes plug-ins do Cordova devem estar disponíveis:

* [cordova de plug-in de dispositivo]
* [cordova de plug-in de inappbrowser]

Dois fluxos de autenticação com suporte: um fluxo de servidor e um fluxo de cliente.  O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende de interface de autenticação do provedor da web. O fluxo de cliente possibilita maior integração com recursos específicos para dispositivos como single sign-on pois depende de SDKs de dispositivo específico específicas do provedor.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>Como: configurar o serviço de aplicativo móvel para URLs de redirecionamento externos.

Vários tipos de aplicativos Apache Cordova usam um recurso de autoretorno para lidar com fluxos de OAuth UI.  Fluxos de OAuth UI no host local causam problemas, desde que o serviço de autenticação só sabe como utilizar o seu serviço por padrão.  Exemplos de fluxos de OAuth UI problemáticos incluem:

- O emulador de cascata.
- Visualização recarregar com Ionic.
- Executando o back-end móvel localmente
- Executando o back-end móvel em outro serviço de aplicativo do Azure que a autenticação fornecendo um.

Siga estas instruções para adicionar as configurações locais na configuração:

1. Faça logon [portal do Azure]
2. Selecione **todos os recursos** ou **Os serviços de aplicativo** e clique no nome do seu aplicativo de celular.
3. Clique em **Ferramentas**
4. Clique em **Gerenciador de recursos** no menu OBSERVE, clique em **Ir**.  Abre uma nova janela ou guia.
5. Expanda a **configuração**, nós **authsettings** para seu site no painel de navegação à esquerda.
6. Clique em **Editar**
7. Procure o elemento de "allowedExternalRedirectUrls".  Ele pode ser definido como nulo ou uma matriz de valores.  Altere o valor para o seguinte valor:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Substitua as URLs com as URLs do seu serviço.  Alguns exemplos incluem "http://localhost:3000" (para o serviço de amostra node) ou "http://localhost:4400" (para o serviço de ondulação).  No entanto, esses URLs são exemplos - sua situação, incluindo para os serviços mencionados nos exemplos, podem ser diferentes.
8. Clique no botão de **Leitura/gravação** no canto superior direito da tela.
9. Clique em verde botão **colocar** .

As configurações são salvas neste momento.  Não fechar a janela do navegador até terminar as configurações de salvamento.
Também adicione essas URLs de autoretorno para as configurações de CORS de seu serviço de aplicativo:

1. Faça logon [portal do Azure]
2. Selecione **todos os recursos** ou **Os serviços de aplicativo** e clique no nome do seu aplicativo de celular.
3. A lâmina de configurações é aberto automaticamente.  Se não estiver, clique em **Todas as configurações**.
4. Clique em **CORS** em menu API.
5. Digite a URL que você deseja adicionar na caixa fornecida e pressione Enter.
6. Digite as URLs adicionais conforme necessário.
7. Clique em **Salvar** para salvar as configurações.

Leva aproximadamente 10 a 15 segundos para que as novas configurações serão efetivadas.

##<a name="register-for-push"></a>Como: registrar para notificações por Push

Instale o [phonegap de plug-in de envio] para lidar com notificações por push.  Este plug-in pode ser adicionado facilmente usando o `cordova plugin add` comando na linha de comando, ou via o instalador do plug-in gito dentro do Visual Studio.  O seguinte código em seu aplicativo Apache Cordova registra seu dispositivo para notificações por push:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Use o SDK de Hubs de notificação para enviar notificações por push do servidor.  Nunca envie notificações por push diretamente de clientes. Isso pode ser usado para disparar um ataque de negação de serviço contra Hubs de notificação ou o PNS.  O PNS pode proibir o tráfego como resultado de tais ataques.

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com
[Início rápido de aplicativos móveis Azure]: app-service-mobile-cordova-get-started.md
[Introdução ao autenticação]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Plug-in do Apache Cordova para aplicativos móveis do Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[seu aplicativo Apache Cordova primeiro]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[PhoneGap de plug-in de envio]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova de plug-in de dispositivo]: https://www.npmjs.com/package/cordova-plugin-device
[cordova de plug-in de inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx
