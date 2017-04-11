<properties
    pageTitle="Como usar o SDK do JavaScript para aplicativos móveis do Azure"
    description="Como usar v para aplicativos do Azure móvel"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Como usar a biblioteca de cliente JavaScript para aplicativos móveis do Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia ensina a executar cenários comuns usando o [SDK do JavaScript para aplicativos do Azure móvel]do mais recente. Se você estiver começando a usar os aplicativos do Azure Mobile, primeiro conclua o [Início rápido do Azure Mobile aplicativos] para criar um back-end e criar uma tabela. Neste guia, podemos focalizar usando o back-end móvel em aplicativos Web HTML/JavaScript.

## <a name="supported-platforms"></a>Plataformas com suporte

Podemos limitar o suporte ao navegador para as versões atuais e o últimos dos navegadores principais: Google Chrome, Microsoft Edge, Microsoft Internet Explorer e Mozilla Firefox.  Esperamos que o SDK para funcionar com qualquer navegador relativamente moderno.

O pacote é distribuído como um módulo de JavaScript Universal, para que ele oferece suporte globais, AMD, e formatos de CommonJS.

##<a name="Setup"></a>Pré-requisitos e configuração

Este guia pressupõe que você tenha criado um back-end com uma tabela. Este guia pressupõe que a tabela tenha o mesmo esquema as tabelas esses tutoriais.

Instalar o SDK do Azure Mobile aplicativos JavaScript pode ser feito através do `npm` comando:

```
npm install azure-mobile-apps-client --save
```

Depois de instalado, a biblioteca está localizada em `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.min.js`.  Copie esse arquivo na sua área de web.

```
<script src="path/to/MobileServices.Web.min.js"></script>
```

A biblioteca também pode ser usada como um módulo de ES2015, em ambientes de CommonJS como Browserify e Webpack e como uma biblioteca AMD.  Por exemplo:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>Como: autenticar os usuários

Serviço de aplicativo do Azure dá suporte a autenticação e a autorização de usuários de aplicativo usando vários provedores de identidade externa: Facebook, Google, Account da Microsoft e Twitter. Você pode definir permissões em tabelas para restringir o acesso para operações específicas somente para usuários autenticados. Você também pode usar a identidade dos usuários autenticados implementar regras de autorização em scripts de servidor. Para obter mais informações, consulte o tutorial de [Introdução ao autenticação] .

Dois fluxos de autenticação com suporte: um fluxo de servidor e um fluxo de cliente.  O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende de interface de autenticação do provedor da web. O fluxo de cliente possibilita maior integração com recursos específicos para dispositivos como single sign-on pois depende de SDKs específicas do provedor.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>Como: configurar o serviço de aplicativo móvel para URLs de redirecionamento externos.

Vários tipos de aplicativos de JavaScript usam um recurso de autoretorno para lidar com fluxos de OAuth UI.  Esses recursos incluem:

* Executar seu serviço localmente
* Usando recarregar ao vivo com a estrutura Ionic
* Redirecionar para o serviço de aplicativo para autenticação. 

Executando localmente pode causar problemas, porque, por padrão, a autenticação do serviço de aplicativo somente está configurada para permitir o acesso do seu back-end do aplicativo Mobile. Use as etapas a seguir para alterar as configurações de serviço de aplicativo para habilitar a autenticação ao executar o servidor localmente:

1. Faça logon [portal do Azure]
2. Navegue até seu back-end do aplicativo Mobile.
3. Selecione **Gerenciador de recurso** no menu **Ferramentas de desenvolvimento** .
4. Clique em **Ir** para abrir o Gerenciador de recursos para seu back-end do aplicativo móvel em uma nova guia ou janela.
5. Expanda o **config** > **authsettings** nó para o aplicativo.
6. Clique no botão **Editar** para habilitar a edição do recurso.
7. Localize o elemento **allowedExternalRedirectUrls** , que deve ser nulo. Adicione URLs de uma matriz:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Substituir as URLs na matriz com as URLs do seu serviço, que, neste exemplo, é `http://localhost:3000` para o serviço de amostra Node local. Você também pode usar `http://localhost:4400` para o serviço de cascata ou algum outro URL, dependendo de como seu aplicativo é configurado.

8. Na parte superior da página, clique em **Leitura/gravação**, clique em **colocar** para salvar suas atualizações.

Você também precisa adicionar as mesmas URLs de autoretorno para as configurações de lista branca CORS:

1. Navegar de volta para o [portal do Azure].
2. Navegue até seu back-end do aplicativo Mobile.
3. Clique em **CORS** no menu **API** .
4. Digite cada URL na caixa de texto vazia **Origens permitidos** .  Uma nova caixa de texto é criada.
5. Clique em **Salvar**
    
Depois de back-end atualizações, você poderá usar as novas URLs de autoretorno em seu aplicativo.

<!-- URLs. -->
[Início rápido de aplicativos móveis Azure]: app-service-mobile-cordova-get-started.md
[Introdução ao autenticação]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Portal do Azure]: https://portal.azure.com/
[SDK de JavaScript para aplicativos móveis do Azure]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx

