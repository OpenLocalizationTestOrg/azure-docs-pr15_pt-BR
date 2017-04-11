<properties
    pageTitle="Introdução ao Azure AD entrar e sair usando Node"
    description="Como criar um Node Express MVC aplicativo Web que se integra ao Azure AD para entrar."
    services="active-directory"
    documentationCenter="nodejs"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
  ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="brandwe"/>

# <a name="nodejs-web-app-sign-in--sign-out-with-azure-ad"></a>NodeJS entrada do aplicativo da Web & assinar Out com o Azure AD


Aqui, usaremos Passport para:

- Entrar do usuário no aplicativo usando o Azure AD.
- Exiba algumas informações sobre o usuário.
- Assine o usuário sair do aplicativo.

**Passport** é middleware de autenticação para Node. Muito flexível e modular, Passport pode ser atualizam descartado para qualquer expressas ou Resitify aplicativo web. Um conjunto abrangente de estratégias de suporte à autenticação usando um nome de usuário e senha, Facebook, Twitter e muito mais. Podemos desenvolveu uma estratégia Microsoft Azure Active Directory. Podemos irá instalar este módulo e adicione o Active Directory do Microsoft Azure `passport-azure-ad` plug-in.

Para fazer isso, você precisará:

1. Registre um aplicativo.
2. Configure seu aplicativo para usar a estratégia de Passport azure-ad.
3. Use Passport para emitir solicitações de entrada e saídas para Azure AD.
4. Imprima dados sobre o usuário.

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS).  Para acompanhar, você pode [Baixar esqueleto do aplicativo como um. zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) ou clonar a estrutura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

O aplicativo concluído é fornecido no final deste tutorial também.

## <a name="1-register-an-app"></a>1. registrar um aplicativo
- Entrar no Portal de gerenciamento do Azure.
- No painel de navegação à esquerda, clique no **Active Directory**.
- Selecione o locatário onde você deseja registrar o aplicativo.
- Clique na guia **aplicativos** e clique em Adicionar na parte inferior registradora.
- Siga os avisos e criar um novo **aplicativo Web e/ou WebAPI**.
    - O **nome** do aplicativo descreverá seu aplicativo para usuários finais
    -   A **URL de logon** é a URL base do aplicativo.  Padrão de esqueleto é ' http://localhost:3000/auth/openid/return '.
    - O **URI de ID de aplicativo** é um identificador exclusivo para o seu aplicativo.  A convenção é usar `https://<tenant-domain>/<app-name>`, por exemplo,`https://contoso.onmicrosoft.com/my-first-aad-app`
- Após concluir registro, AAD atribuirá seu aplicativo um identificador de cliente exclusivo.  Você precisará desse valor nas próximas seções, portanto copiá-lo na guia Configurar.

## <a name="2-add-pre-requisities-to-your-directory"></a>2. Adicionar pré-requisities ao seu diretório

Na linha de comando, altere diretórios para a pasta raiz se não estiver lá e execute os seguintes comandos:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`

- Além disso, você precisará nosso `passport-azure-ad` assim:

- `npm install passport-azure-ad`

Isto irá instalar as bibliotecas que passport azure-ad dependem.

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3. configurar seu aplicativo para usar a estratégia de passport-nó-js
Aqui, estamos vai configurar o middleware Express para usar o protocolo de autenticação OpenID se conectar.  Passport será usado para emitir solicitações de entrada e saídas, gerenciar a sessão do usuário e obter informações sobre o usuário, entre outras coisas.

-   Para começar, abra o `config.js` arquivo na raiz do projeto e insira os valores de configuração do seu aplicativo no `exports.creds` seção.
    -   O `clientID:` é a **Id de aplicativo** atribuída ao seu aplicativo no portal do registro.
    -   O `returnURL` é o **Uri redirecionar** inseridas no portal.
    - O `clientSecret` é o segredo gerado no portal

- Em seguida abra `app.js` arquivo na raiz do projeto e adicionar a chamada seguinte para chamar o `OIDCStrategy` estratégia que vem com`passport-azure-ad`


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// add a logger

var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

- Depois disso, use a estratégia podemos referenciados apenas para lidar com nossas solicitações de logon

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 
// 
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    if (!profile.email) {
      return done(new Error("No email found"), null);
    }
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport usa um padrão semelhante para todos é estratégias (Twitter, Facebook, etc.) que todos os autores de estratégia adicional. Examinando a estratégia verá que podemos passar uma função que tenha um token e concluído como os parâmetros. A estratégia obedientemente retornarão conosco depois que ela faz tudo-lo do trabalho. Depois que ela faz será queremos armazenar o usuário e guardar o token, portanto, não precisamos perguntá-lo novamente.


> [AZURE.IMPORTANT] 
O código acima leva qualquer usuário que acontece se autenticar com nosso servidor. Isso é conhecido como registro automático. Em servidores de produção não quiser permitir que qualquer pessoa sem precisar primeiro-los passar por um processo de registro que você decida. Isso geralmente é o padrão que você vê nos aplicativos do consumidor que permitem que você registrar com o Facebook, mas, em seguida, pedimos que você preencha informações adicionais. Se isso não era um aplicativo de exemplo, podemos poderia ter apenas extraídos o email do objeto de token que é retornado e pedir que ele preencha informações adicionais. Como este é um servidor de teste nós simplesmente adicioná-los ao banco de dados na memória.

- Em seguida, vamos adicionar os métodos que permitirão controlar o conectado em usuários conforme necessário pelo Passport. Isso inclui serializando e desserializando as informações do usuário:

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
   log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};
```

- Em seguida, vamos adicionar o código para carregar o mecanismo express. Veja aqui usamos o /views padrão e /routes padrão que Express fornece.

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- Por fim, vamos adicionar as rotas que serão entregar as solicitações de logon real para o `passport-azure-ad` mecanismo:

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return
app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });
  ```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4. usar Passport para emitir solicitações de entrada e saídas para Azure AD

Seu aplicativo agora está configurado corretamente para se comunicar com o ponto de extremidade de v 2.0 usando o protocolo de autenticação OpenID se conectar.  `passport-azure-ad`tem tratado de todos os detalhes boa aparência de criar mensagens de autenticação, validando tokens do Azure AD e mantendo a sessão do usuário.  Tudo o que falta é dar aos usuários uma maneira para entrar, saia e coletar informações adicionais sobre o usuário conectado.

- Primeiro, permite adicionar os métodos padrão, login, conta e logout à nossa `app.js` arquivo:

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

-   Vamos revisar esses detalhadamente:
    -   O `/` rota redirecionará para o modo de exibição de index.ejs passando o usuário na solicitação (se existir)
    - O `/account` rota será primeiro ***Certifique-se de que estamos são autenticados*** (podemos implementar que abaixo) e, em seguida, passe o usuário na solicitação de forma que podemos obter informações adicionais sobre o usuário.
    - O `/login` rota chamará nosso autenticador azuread openidconnect de `passport-azuread` e se que não conseguiu será redirecionar o usuário voltar ao /login
    - O `/logout` será simplesmente chamar o logout.ejs (e rotear) que limpa cookies e retorne o usuário para index.ejs


- Para a última parte da `app.js`, vamos adicionar o método de EnsureAuthenticated que é usado em `/account` acima.

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}
```

- Por fim, vamos realmente criar o próprio servidor em `app.js`:

```JavaScript

app.listen(3000);

```


## <a name="5-create-the-views-and-routes-in-express-to-display-our-user-in-the-website"></a>5. criar modos de exibição e rotas no express para exibir o usuário do site

Temos nosso `app.js` concluída. Agora, podemos simplesmente precisa adicionar rotas e modos de exibição que mostram as informações que podemos chegar ao usuário, bem como alça a `/logout` e `/login` rotas criamos.

- Criar a `/routes/index.js` rota sob o diretório raiz.

```JavaScript
/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- Criar a `/routes/user.js` rota sob o diretório raiz

```JavaScript
/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Essas rotas simples apenas passará ao longo de solicitação de nossos modos de exibição, incluindo o usuário se estiver presente.

- Criar a `/views/index.ejs` exibição sob o diretório raiz. Esta é uma página simple que irá chamar nossos métodos de login e logout e nos permitem capturar informações de conta. Observe que podemos usar a condicional `if (!user)` como o usuário sendo passou na solicitação é uma prova temos um conectado em usuário.

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account Info</a></br>
    <a href="/logout">Log Out</a>
<% } %>
```

- Criar a `/views/account.ejs` exibir sob o diretório raiz para que estamos pode exibir informações adicionais que `passport-azuread` colocou na solicitação de usuário.

```Javascript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

- Por fim, vamos fazer essa aparência bastante adicionando um layout. Criar o ' / modo de exibição dos views/layout.ejs sob o diretório raiz

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>Passport-OpenID Example</title>
    </head>
    <body>
        <% if (!user) { %>
            <p>
            <a href="/">Home</a> | 
            <a href="/login">Log In</a>
            </p>
        <% } else { %>
            <p>
            <a href="/">Home</a> | 
            <a href="/account">Account</a> | 
            <a href="/logout">Log Out</a>
            </p>
        <% } %>
        <%- body %>
    </body>
</html>
```

Finalmente, criar e executar o aplicativo! 

Executar `node app.js` e navegue até`http://localhost:3000`


Entre com uma Account da Microsoft ou uma conta corporativa ou escolar e observe como a identidade do usuário é refletida na lista de conta.  Agora você tem um aplicativo web protegido usando protocolos padrão do setor que podem autenticar usuários com suas contas pessoais e de trabalho/escola.

Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um. zip aqui](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```


Agora, você pode mover em tópicos mais avançados.  Talvez você queira experimentar:

[Proteger uma Web API com Azure AD >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
