<properties
    pageTitle="Azure AD v 2.0 AngularJS Introdução | Microsoft Azure"
    description="Como criar um aplicativo de Angular JS única página sinais em contas de trabalho ou da escola e usuários com ambas as contas pessoais do Microsoft."
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


# <a name="add-sign-in-to-an-angularjs-single-page-app---net"></a>Adicionar entrada a um aplicativo de página única AngularJS - .NET

Neste artigo, adicionaremos entrar com contas da Microsoft da plataforma um aplicativo de AngularJS usando o ponto de extremidade do Active Directory do Azure v 2.0.  O ponto de extremidade de v 2.0 permite realizar uma integração única em seu aplicativo e autenticar os usuários com contas pessoais e de trabalho/escola.

Este exemplo é um aplicativo de única página de lista de tarefas pendentes simple que armazena tarefas em um back-end API REST, escrito usando a MVC de 4,5 do .NET framework e protegidas usando tokens de portador OAuth do Azure AD.  O aplicativo de AngularJS usará o nossa Abrir origem de biblioteca de autenticação de JavaScript [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) para manipular o processo de inscrição inteiro e adquira tokens para chamar a API REST.  O mesmo padrão pode ser aplicado para autenticar para outras APIs REST, como o [Microsoft Graph](https://graph.microsoft.com).

> [AZURE.NOTE]
    Nem todos os cenários do Active Directory do Azure e recursos são compatíveis com o ponto de extremidade de v 2.0.  Para determinar se você deve usar o ponto de extremidade de v 2.0, leia sobre as [limitações da versão 2.0](active-directory-v2-limitations.md).

## <a name="download"></a>Baixar

Para começar, você precisará baixar e instalar o Visual Studio.  Em seguida, você pode clonar ou [Baixar](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) um aplicativo reduzido:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet.git
```

O aplicativo reduzido inclui todo o código clichê para um aplicativo de AngularJS simple, mas está faltando todas as partes relacionadas a identidade.  Se você não quiser acompanhá-lo, em vez disso, você pode clonar ou [Baixar](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/complete.zip) o exemplo concluído.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-DotNet.git
```

## <a name="register-an-app"></a>Registrar um aplicativo

Primeiro, crie um aplicativo no [Portal de registro do aplicativo](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou siga estas [etapas detalhadas](active-directory-v2-app-registration.md).  Certifique-se de:

- Adicione a plataforma de **Web** para o aplicativo.
- Insira o correto **Redirecionar URI**. O padrão para este exemplo é `https://localhost:44326/`.
- Deixe a caixa de seleção **Permitir fluxo implícito** habilitada. 

Copiar a **ID do aplicativo** que está atribuído a seu aplicativo, você precisará dela em breve. 

## <a name="install-adaljs"></a>Instalar o adal.js
Para começar, navegue para o project você baixou e instale adal.js.  Se você tiver [bower](http://bower.io/) instalado, você pode apenas executar este comando.  Para qualquer incompatibilidades de versão de dependência, basta escolha a versão mais recente.
```
bower install adal-angular#experimental
```

Como alternativa, você pode baixar manualmente [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) e [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js).  Adicione os dois arquivos a `app/lib/adal-angular-experimental/dist` diretório do `TodoSPA` projeto.

Abra o projeto no Visual Studio e carregar adal.js no final do corpo da página principal:

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## <a name="set-up-the-rest-api"></a>Configurar a API REST

Enquanto estamos configurando coisas, vamos começar a funcionar da API REST de back-end.  Na raiz do projeto, abra `web.config` e substitua o `audience` valor.  A API REST usará esse valor para validar tokens recebe do aplicativo Angular em solicitações AJAX.

```xml
<!--web.config-->

...

    <appSettings>
        <add key="ida:Audience" value="[Your-application-id]" />
    </appSettings>
    
...
```

Esse é o tempo todo, vamos gastar discutindo como funciona a API REST.  Fique à vontade para examinar o código, mas se você quiser saber mais sobre como proteger web APIs com Azure AD, confira [neste artigo](active-directory-v2-devquickstarts-dotnet-api.md). 

## <a name="sign-users-in"></a>Usuários de entrada em
Tempo para escrever um código de identidade.  Você deve ter já notado que adal.js contém um provedor de AngularJS, que é executado perfeitamente com mecanismos de roteamento Angular.  Comece adicionando o módulo adal para o aplicativo:

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

Agora você pode inicializar o `adalProvider` com sua ID de aplicativo:

```js
// app/scripts/app.js

...

adalProvider.init({
        
        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 
        
        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',
        
        // Your application id from the registration portal
        clientId: '<Your-application-id>',
        
        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',
         
    }, $httpProvider);
```

Ótimo, agora adal.js tem todas as informações necessárias para proteger o seu aplicativo e entre os usuários.  Para forçar entrar para um roteiro particular no aplicativo, tudo é uma linha de código:

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

Agora quando um usuário clica na `TodoList` link, adal.js redirecionará automaticamente ao Azure AD para entrar se necessário.  Você pode também explicitamente enviar solicitações de entrada e saídas chamando adal.js em seus controladores:

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {
        
        // Redirect the user to sign in
        adalService.login();
        
    };
    $scope.logout = function () {
        
        // Redirect the user to log out    
        adalService.logOut();
    
    };
...
```

## <a name="display-user-info"></a>Exibir informações do usuário
Agora que o usuário estiver conectado, você provavelmente precisará acessar dados de autenticação do usuário conectado em seu aplicativo.  Adal.js expõe essas informações para você no `userInfo` objeto.  Para acessar esse objeto em um modo de exibição, primeiro adicione adal.js ao escopo raiz do controlador de correspondentes:

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

Em seguida, você pode abordar diretamente a `userInfo` objeto no modo de exibição: 

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

Você também pode usar o `userInfo` objeto para determinar se o usuário estiver conectado ou não.

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## <a name="call-the-rest-api"></a>Chamar a API REST
Por fim, é hora para obter alguns tokens e chamar a API REST para criar, ler, atualizar e excluir tarefas.  Bem adivinhe?  Você não precisa fazer *algo*.  Adal.js executarão automaticamente obtendo, cache e atualizando tokens.  Ele também executarão de conectar esses tokens a saída solicitações de AJAX que você enviar a API REST.  

Exatamente como isso funciona? É tudo graças à magia [interceptores AngularJS](https://docs.angularjs.org/api/ng/service/$http), que permite adal.js transformar mensagens de http de entrada e saída.  Além disso, adal.js pressupõe que todas as solicitações de enviar para o mesmo domínio como a janela deve usar tokens destinados a mesma ID de aplicativo como o aplicativo AngularJS.  É por isso usamos a mesma ID de aplicativo no aplicativo Angular e na API REST NodeJS.  Claro, você pode substituir esse comportamento e informar adal.js obter tokens para outras APIs REST se necessário - mas para este cenário simples os padrões fará.

Aqui está um trecho que mostra como é fácil enviar solicitações com tokens de portador do Azure AD:

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

Parabéns!  Seu aplicativo de página única integrado do Azure AD está concluído.  Ir adiante, levar Laço.  Ele pode autenticar os usuários, com segurança chamar sua API REST usando a conexão de OpenID de back-end e obtenha informações básicas sobre o usuário.  Prontos para uso, ele dá suporte a qualquer usuário com uma Account da Microsoft ou uma conta corporativa/de estudante do Azure AD.  Executar o aplicativo e, em um navegador, vá para `https://localhost:44326/`.  Entre usando uma conta pessoal da Microsoft ou uma conta corporativa/de estudante.  Adicionar tarefas à lista de tarefas pendentes do usuário e sair.  Tente entrar com outro tipo de conta. Se você precisar de um locatário do Azure AD para criar usuários de trabalho/escola, [Aprenda a obter uma aqui](active-directory-howto-tenant.md) (ele é gratuito).

Para continuar aprendendo sobre o ponto de extremidade de v 2.0, retornar ao nosso [guia de desenvolvedor do v 2.0](active-directory-appmodel-v2-overview.md).  Para obter recursos adicionais, consulte:

- [Exemplos de Azure no GitHub >>](https://github.com/Azure-Samples)
- [Azure AD em excesso de pilha >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
- Documentação Azure AD [Azure.com >>](https://azure.microsoft.com/documentation/services/active-directory/)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para nossos produtos

Recomendamos que você obtenha as notificações de quando ocorrências de segurança ocorrerem visitando [esta página](https://technet.microsoft.com/security/dd252948) e assinar alertas de segurança comunicado.
