<properties
    pageTitle="Azure AD AngularJS Introdução | Microsoft Azure"
    description="Como criar um aplicativo de Angular JS única página que se integra ao Azure AD para entrar e chama Azure AD protegido APIs usando OAuth."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="securing-angularjs-single-page-apps-with-azure-ad"></a>Protegendo AngularJS única página aplicativos com o Azure AD

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD torna mais simples e direto para você adicionar sign in, sair e proteger chamadas de API de OAuth seus aplicativos de única página.  Ele permite que seu aplicativo autenticar usuários com suas contas do Active Directory e consumir qualquer web API protegido por Azure AD, como as APIs do Office 365 ou a API do Azure.

Para aplicativos de javascript executados em um navegador, o Azure AD fornece a biblioteca de autenticação do Active Directory ou adal.js.  A única finalidade do Adal.js na vida é facilite para seu aplicativo obter tokens de acesso.  Para demonstrar apenas como é fácil, aqui será criamos um aplicativo de AngularJS lista de tarefas pendentes que:

- O usuário se inscreve no aplicativo usando o Azure AD como provedor de identidade.
- Exibe algumas informações sobre o usuário.
- Chamadas com segurança para fazer lista API do aplicativo usando tokens de portador de AAD.
- Assina o usuário sair do aplicativo.

Para criar o aplicativo de trabalho concluído, você precisará:

2. Registre seu aplicativo com o Azure AD.
3. ADAL de instalar e configurar o SPA.
5. Use ADAL para proteger as páginas na SPA.

Para começar a, [Baixar a estrutura do aplicativo](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) ou [baixar o exemplo concluído](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).  Você também será preciso um locatário do Azure AD na qual você pode criar usuários e registrar um aplicativo.  Se você ainda não tiver um locatário, [Saiba como obter uma](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>*1. Registre o aplicativo de DirectorySearcher*
Para ativar seu aplicativo autenticar usuários e obter tokens, você precisará primeiro registrá-lo no seu locatário do Azure AD:

-   Entrar no [Portal de gerenciamento do Azure](https://manage.windowsazure.com)
-   No painel de navegação à esquerda, clique no **Active Directory**
-   Selecione um locatário no qual deseja registrar o aplicativo.
-   Clique na guia **aplicativos** e clique em **Adicionar** na parte inferior registradora.
-   Siga os avisos e criar um novo **aplicativo Web e/ou WebAPI**.
    -   O **nome** do aplicativo descreverá seu aplicativo para usuários finais.
    -   O **Uri redirecionar** é local para o qual AAD retornarão tokens.  O local padrão para este exemplo é`https://localhost:44326/`
-   Após concluir registro, AAD atribuirá seu aplicativo uma exclusiva **ID do cliente**.  Você precisará desse valor nas próximas seções, portanto copiá-lo na guia **Configurar** .
- Adal.js usa o fluxo de implícito OAuth para se comunicar com o Azure AD.  Você deve ativar o fluxo implícito para seu aplicativo por:
    - Baixe o manifesto do aplicativo clicando em **Gerenciar manifesto**.
    - Abra o manifesto e localize o `oauth2AllowImplicitFlow` propriedade. Defina seu valor para `true`.
    - Salve e carregue o manifesto do aplicativo clicando em **Gerenciar manifesto** novamente.

## <a name="2-install-adal--configure-the-spa"></a>*2. ADAL de instalar e configurar o SPA*
Agora que você tem um aplicativo no Azure AD, você pode instalar adal.js e escrever seu código relacionadas a identidade.

-   Comece adicionando adal.js ao projeto TodoSPA usando o Package Manager Console:
  - Baixe [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) e adicioná-lo a `App/Scripts/` diretório do projeto.
  - Baixe [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) e adicioná-lo a `App/Scripts/` diretório do projeto.
  - Carregar cada script antes do final do `</body>` na `index.html`:

```js
...
<script src="App/Scripts/adal.js"></script>
<script src="App/Scripts/adal-angular.js"></script>
...
```

-   Para back-end do SPA para fazer lista API do aceite tokens do navegador, back-end precisa de informações de configuração sobre o registro do aplicativo. No projeto TodoSPA, abra `web.config`.  Substitua os valores dos elementos na `<appSettings>` seção para refletir os valores que você insere no Portal do Azure.  Seu código fará referência esses valores sempre que ele usa ADAL.
    -   O `ida:Tenant` é o domínio de seu locatário do Azure AD, por exemplo, contoso.onmicrosoft.com
    -   O `ida:Audience` deve ser a **ID do cliente** do aplicativo que você copiou do portal.

## <a name="3--use-adal-to-secure-pages-in-the-spa"></a>*3. use ADAL para proteger as páginas na SPA*
Adal.js foi desenvolvido para integrar com provedores de roteiro e http AngularJS, que permite exibições individuais em seu SPA seguras.

- Em `App/Scripts/app.js`, trazer o módulo adal.js:

```js
angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {
...
```
- Agora você pode inicializar o `adalProvider` com os valores de configuração de seu registro de aplicativo, também em `App/Scripts/app.js`:

```js
adalProvider.init(
  {
      instance: 'https://login.microsoftonline.com/',
      tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
      clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
      extraQueryParameter: 'nux=1',
      //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
  },
  $httpProvider
);
```
- Agora para proteger o `TodoList` exibir no aplicativo, somente uma linha de código é necessária - `requireADLogin`.

```js
...
}).when("/TodoList", {
        controller: "todoListCtrl",
        templateUrl: "/App/Views/TodoList.html",
        requireADLogin: true,
...
```

Agora você tem um aplicativo de página única segura com a capacidade de entrar usuários e emitir solicitações protegidas token de portador para sua API de back-end.  Quando um usuário clica na `TodoList` link, adal.js redirecionará automaticamente ao Azure AD para entrar se necessário.  Além disso, adal.js automaticamente irá anexar um access_token a qualquer solicitação de ajax que é enviadas para back-end do aplicativo.  A acima é o mínimo necessário para construir um SPA com adal.js - mas há um número de outros recursos que são úteis no SPAs:

- Para explicitamente problema entrar e sair solicitações, você pode definir funções em seus controladores que invocar adal.js.  In `App/Scripts/homeCtrl.js`:

```js
...
$scope.login = function () {
    adalService.login();
};
$scope.logout = function () {
    adalService.logOut();
};
...
```
- Você também pode querer apresentar informações do usuário na interface do usuário do aplicativo.  O serviço de adal já foi adicionado para o `userDataCtrl` controlador, para poder acessar o `userInfo` objeto no modo de exibição associado, `App/Views/UserData.html`:

```js
<p>{{userInfo.userName}}</p>
<p>aud:{{userInfo.profile.aud}}</p>
<p>iss:{{userInfo.profile.iss}}</p>
...
```

- Também existem muitos cenários em que você irá querer saber se o usuário estiver conectado ou não.  Você também pode usar o `userInfo` objeto para coletar essas informações.  Por exemplo, em `index.html` você pode mostrar o "Logon" ou "Logout" botão com base no status de autenticação:

```js
<li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
<li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
```

Parabéns! Aplicativo de página única integrado ao seu Azure AD está concluída.  Ele pode autenticar os usuários, com segurança chamar seu back-end usando OAuth 2.0 e obtenha informações básicas sobre o usuário.  Se você ainda não começou, agora é o tempo para preencher seu locatário com alguns usuários.  Executar sua à faça lista SPA e entrar em um desses usuários.  Adicione tarefas aos usuários para listar, sair e entrar novamente.

Adal.js facilita a incorporar todos esses recursos de identidade comuns em seu aplicativo.  Cuida de todo o trabalho sujo para você - gerenciamento de cache, o suporte do protocolo de OAuth, apresentando o usuário com um logon UI, atualizando tokens expirados e muito mais.

Para referência, o exemplo concluído (sem os valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).  Agora você pode mover-se em cenários adicionais.  Talvez você queira experimentar:

[Chamar um Web CORS API de um SPA >>](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
