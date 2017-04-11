<properties
    pageTitle="B2C do Azure AD: Proteger um web API usando Node | Microsoft Azure"
    description="Como criar um API que aceita tokens de um locatário B2C da web Node"
    services="active-directory-b2c"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="08/30/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c-secure-a-web-api-by-using-nodejs"></a>B2C do Azure AD: Proteger um web API usando Node

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Com B2C do Azure Active Directory (AD Azure), você pode proteger um web API usando tokens de acesso OAuth 2.0. Esses tokens permitir que seus aplicativos cliente que usam o Azure AD B2C para autenticar à API. Este artigo mostra como criar uma API de "lista de tarefas pendentes" que permite aos usuários adicionar e lista de tarefas. Web API é protegido através do Azure AD B2C e permite apenas usuários autenticados ao gerenciar sua lista de tarefas pendentes.

> [AZURE.NOTE]  Este exemplo foi escrito estar conectado ao usando o nosso [aplicativo de exemplo B2C iOS](active-directory-b2c-devquickstarts-ios.md). Fazer Noções básicas do atual primeiro e depois siga junto com esse exemplo.

**Passport** é middleware de autenticação para Node. Flexível e modular, Passport atualizam pode ser instalado em qualquer expressas ou Restify aplicativo web. Um conjunto abrangente de estratégias dá suporte à autenticação usando um nome de usuário e senha, Facebook, Twitter e muito mais. Podemos desenvolveu uma estratégia para Azure Active Directory (AD Azure). Instalar este módulo e em seguida, adicione o Azure AD `passport-azure-ad` plug-in.

Para executar este exemplo, você precisa:

1. Registre um aplicativo do Azure AD.
2. Configurar seu aplicativo para usar do Passport `azure-ad-passport` plug-in.
3. Configure um aplicativo cliente para web "lista de tarefas pendentes" API de chamadas.


## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório de Azure AD B2C

Antes de poder usar Azure AD B2C, você deve criar um diretório ou de locatários.  Um diretório é um contêiner para todos os usuários, aplicativos, grupos e muito mais.  Se você não tiver um já, [Crie um diretório de B2C](active-directory-b2c-get-started.md) antes de continuar.

## <a name="create-an-application"></a>Criar um aplicativo

Em seguida, você precisa criar um aplicativo no seu diretório B2C que dá Azure AD algumas informações que ele precisa se comunicar com segurança com seu aplicativo. Nesse caso, o aplicativo cliente e web API são representados por um único **ID do aplicativo**, pois elas compõem um aplicativo lógico. Para criar um aplicativo, siga [estas instruções](active-directory-b2c-app-registration.md). Certifique-se de:

- Incluir um **web app/web api** no aplicativo
- Insira `http://localhost/TodoListService` como uma **URL de resposta**. É a URL padrão para este exemplo de código.
- Crie um **segredo do aplicativo** para o seu aplicativo e copiá-lo. Você precisa esses dados mais tarde. Observe que este valor deve ser [precedido de XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) antes de você usá-lo.
- Copie a **ID do aplicativo** que está atribuído a seu aplicativo. Você precisa esses dados mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar suas regras

No Azure AD B2C, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Este aplicativo contém duas experiências de identidade: inscrever-se e entre. Você precisa criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).  Quando você cria sua três diretivas, certifique-se a:

- Escolha o **nome de exibição** e outros atributos de inscrição na sua política de inscrição.
- Escolha as declarações de aplicativo **nome para exibição** e a **ID de objeto** em cada política.  Você pode escolher outras reivindicações também.
- Copiar o **nome** de cada política após criá-lo. Ele deve ter o prefixo `b2c_1_`.  É necessário os nomes de política mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Após ter criado suas políticas de três, você está pronto para criar seu aplicativo.

Para saber mais sobre como as políticas funcionam no Azure AD B2C, comece com o [.NET web app tutorial de Introdução](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Baixe o código

O código para este tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS). Para criar a amostra enquanto você trabalha, você pode [baixar um projeto reduzido como um arquivo. zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip). Também é possível clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

O aplicativo concluído também está [disponível como um arquivo. zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip) ou na `complete` ramificação do mesmo repositório.

## <a name="download-nodejs-for-your-platform"></a>Baixar Node para sua plataforma

Para usar este exemplo com êxito, é necessário uma instalação funcional do Node. 

Instale o Node de [nodejs.org](http://nodejs.org).

## <a name="install-mongodb-for-your-platform"></a>Instalar o MongoDB para sua plataforma

Para usar este exemplo com êxito, é necessário uma instalação funcional do MongoDB. Usamos MongoDB para tornar sua API REST persistente nas instâncias do servidor.

Instale o MongoDB de [mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Este passo a passo supõe que você usa os pontos de extremidade de instalação e servidor para MongoDB, que no momento da redação deste artigo é `mongodb://localhost`.

## <a name="install-the-restify-modules-in-your-web-api"></a>Instalar os módulos Restify em sua web API

Usamos Restify para construir sua API REST. Restify é uma estrutura de aplicativo Node mínimo e flexível derivada de Express. Ela tem um conjunto robusto de recursos para a criação de APIs REST sobre conectar.

### <a name="install-restify"></a>Restify de instalação

Na linha de comando, altere o diretório para `azuread`. Se o `azuread` diretório não existe, criá-lo.

`cd azuread`ou`mkdir azuread;`

Digite o seguinte comando:

`npm install restify`

Este comando instala Restify.

#### <a name="did-you-get-an-error"></a>Você obteve um erro?

Em alguns sistemas operacionais, quando você usa `npm`, você pode receber o erro `Error: EPERM, chmod '/usr/local/bin/..'` e uma solicitação que você execute a conta como um administrador. Se esse problema ocorre, use o `sudo` comando para executar `npm` em um nível de privilégio superior.

#### <a name="did-you-get-a-dtrace-error"></a>Você obteve um erro de DTrace?

Você pode ver algo parecido com este texto quando você instala o Restify:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

Restify fornece um poderoso mecanismo para rastreamento restante chamadas usando o DTrace. No entanto, muitos sistemas operacionais não têm DTrace disponível. Você pode ignorar com segurança esses erros.

A saída do comando deve aparecer semelhante a este texto:

    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)

## <a name="install-passport-in-your-web-api"></a>Instalar o Passport em sua web API

Na linha de comando, altere o diretório para `azuread`, se ele não ainda estiver lá.

Instale o Passport usando o seguinte comando:

`npm install passport`

A saída do comando deve ser semelhante ao texto:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passport-azuread-to-your-web-api"></a>Adicionar passport azuread à sua web API

Em seguida, adicione a estratégia de OAuth usando `passport-azuread`, um conjunto de estratégias que conectam Azure AD com Passport. Use essa estratégia para tokens de portador na amostra API REST.

> [AZURE.NOTE] Embora OAuth2 fornece uma estrutura em que qualquer tipo de token conhecido pode ser emitido, somente determinados tipos de token ganharam amplo uso. Os tokens para a proteção de pontos de extremidade são tokens de portador. Esses tipos de tokens são mais amplamente emitidos em OAuth2. Muitas implementações presumem que tokens de portador são o único tipo de token emitido.

Na linha de comando, altere o diretório para `azuread`, se ele não ainda estiver lá.

Instalar o Passport `passport-azure-ad` módulo usando o seguinte comando:

`npm install passport-azure-ad`

A saída do comando deve ser semelhante ao texto:

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## <a name="add-mongodb-modules-to-your-web-api"></a>Adicionar módulos MongoDB à sua web API

Este exemplo utiliza MongoDB como seu armazenamento de dados. Para que instalar Mongoose, amplamente usada plug-in de gerenciamento de modelos e esquemas.

* `npm install mongoose`

## <a name="install-additional-modules"></a>Instalar módulos adicionais

Em seguida, instale os módulos necessários restantes.

Na linha de comando, altere o diretório para `azuread`, se ele não ainda esteja lá:

`cd azuread`

Instalar os módulos no seu `node_modules` diretório:

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`


## <a name="create-a-serverjs-file-with-your-dependencies"></a>Criar um arquivo de Server. js com suas dependências

O `server.js` arquivo fornece a maioria das funcionalidades de seu servidor de Web API. 

Na linha de comando, altere o diretório para `azuread`, se ele não ainda esteja lá:

`cd azuread`

Criar um `server.js` arquivo em um editor. Adicione as seguintes informações:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Salve o arquivo. Você voltar a ele mais tarde.

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>Criar um arquivo de config.js para armazenar suas configurações do Azure AD

Este arquivo de código passa os parâmetros de configuração de seu Portal do AD do Azure para o `Passport.js` arquivo. Você criou esses valores de configuração quando você adicionou web API portal na primeira parte de Noções básicas. Podemos explicam o que colocar os valores desses parâmetros após copiar o código.

Na linha de comando, altere o diretório para `azuread`, se ele não ainda esteja lá:

`cd azuread`

Criar um `config.js` arquivo em um editor. Adicione as seguintes informações:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>', 
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>Valores necessários

`clientID`: A ID do cliente do seu aplicativo Web API.

`IdentityMetadata`: Isso é onde `passport-azure-ad` procura os dados de configuração para o provedor de identidade. Ele também procura as teclas para validar os tokens de web JSON. 

`audience`: O identificador de recursos uniforme (URI) a partir do portal que identifica seu aplicativo de chamada. 

`tenantName`: Seu nome do locatário (por exemplo, **contoso.onmicrosoft.com**).

`policyName`: A política que você deseja validar os tokens chegando ao seu servidor. Esta política deve ser a mesma política que você usa no aplicativo do cliente para entrar.

> [AZURE.NOTE] Para nossa visualização B2C, use as mesmas políticas pela configuração de cliente e servidor. Se você já tiver concluído uma passo a passo e criou essas políticas, você não precisa fazê-lo novamente. Porque você concluiu Noções básicas do, você não deve precisa configurar novas políticas para orientações de cliente no site.

## <a name="add-configuration-to-your-serverjs-file"></a>Adicionar configuração ao arquivo Server. js

Ler os valores da `config.js` arquivo criado, adicione a `.config` arquivo como um recurso necessário em seu aplicativo e defina as variáveis globais ao `config.js` documento.

Na linha de comando, altere o diretório para `azuread`, se ele não ainda esteja lá:

`cd azuread`

Abrir o `server.js` arquivo em um editor. Adicione as seguintes informações:

```Javascript
var config = require('./config');
```
Adicionar uma nova seção ao `server.js` que inclui o seguinte código:

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

Em seguida, vamos adicionar alguns espaços reservados para os usuários recebemos dos nossos aplicativos de chamada.

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

Vamos prosseguir e também criar nosso agente de log.

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Adicione as informações de modelo e esquema de MongoDB usando Mongoose

A preparação anterior paga conforme você trazer esses três arquivos juntos em um serviço de API REST.

Para este passo a passo, use MongoDB para armazenar suas tarefas, conforme discutido anteriormente.

No `config.js` arquivo, que você chamou o seu banco de dados de **lista de tarefas**. Esse nome também foi que você colocar no final do `mongoose_auth_local` URL de conexão. Você não precisa criar esse banco de dados antecipadamente no MongoDB. Ele cria o banco de dados para você na primeira execução do seu aplicativo de servidor.

Depois que você informa ao servidor de banco de dados que MongoDB usar, você precisará escrever código adicional para criar o modelo e o esquema de suas tarefas de servidor.

### <a name="expand-the-model"></a>Expanda o modelo

Este modelo de esquema é simple. Você pode expandi-lo conforme necessário.

`owner`: Quem está atribuído à tarefa. Esse objeto é uma **cadeia de caracteres**.  

`Text`: A tarefa em si. Esse objeto é uma **cadeia de caracteres**.

`date`: A data em que a tarefa deva ser concluída. Esse objeto é um **datetime**.

`completed`: Se a tarefa está concluída. Esse objeto é um **booliano**.

### <a name="create-the-schema-in-the-code"></a>Criar o esquema no código

Na linha de comando, altere o diretório para `azuread`, se ele não ainda esteja lá:

`cd azuread`

Abrir o `server.js` arquivo em um editor. Adicione as seguintes informações abaixo da entrada de configuração:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Você primeiro criar o esquema e, em seguida, você cria um objeto de modelo que você usa para armazenar os dados por todo o código, quando você define suas **rotas**.

## <a name="add-routes-for-your-rest-api-task-server"></a>Adicionar rotas de seu servidor de tarefa da API REST

Agora que você tem um modelo de banco de dados para trabalhar com, adicione as rotas que você usa para o seu servidor de API REST.

### <a name="about-routes-in-restify"></a>Sobre rotas em Restify

Rotas funcionam no Restify da mesma forma que eles funcionam quando usarem pilha Express. Definir rotas usando o URI que você espera que os aplicativos de cliente para chamar. 

Um típico padrão para uma rota de Restify é:

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

Restify e Express pode fornecer funcionalidade muito mais profunda, como definir tipos de aplicativo e fazendo roteamento complexas entre diferentes pontos de extremidade. Para os fins deste tutorial, vamos manter essas rotas simples.

#### <a name="add-default-routes-to-your-server"></a>Adicionar rotas padrão ao seu servidor

Agora você pode adicionar as rotas CRUD básicas da **lista** e **criar** para nosso API REST. Outras rotas podem ser encontradas na `complete` ramificação da amostra.

Na linha de comando, altere o diretório para `azuread`, se ele não ainda esteja lá:

`cd azuread`

Abrir o `server.js` arquivo em um editor. Veja abaixo as entradas de banco de dados feitas acima adicione as seguintes informações:

```Javascript
/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}
```

```Javascript
/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err)
            return next(err);

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Add one!");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}
```


#### <a name="add-error-handling-for-the-routes"></a>Adicionar tratamento de erro para as rotas

Adicione alguns tratamento de erro para que você possa comunicar os problemas encontrados volta para o cliente de forma que ele pode entender.

Adicione o seguinte código:

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="create-your-server"></a>Criar seu servidor

Agora você definiu seu banco de dados e coloque suas rotas no lugar. A última coisa a fazer é adicionar a instância do servidor que gerencia suas chamadas.

Restify e Express fornecer personalização profunda para um servidor de API REST, mas podemos usar a configuração mais básica aqui. 

```Javascript

**
 * Our Server
 */


var server = restify.createServer({
    name: "Microsoft Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>Adicione as rotas no servidor (sem autenticação)

```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

``` 

## <a name="add-authentication-to-your-rest-api-server"></a>Adicione autenticação ao seu servidor de API REST

Agora que você tem um servidor de API REST em execução, você pode torná-la útil contra Azure AD.

Na linha de comando, altere o diretório para `azuread`, se ele não ainda esteja lá:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Use o OIDCBearerStrategy que está incluído no passport azure-ad


> [AZURE.TIP]
Quando você escreve APIs, você deve sempre vincular os dados para algo exclusivo do token que o usuário não pode simular. Quando o servidor armazena itens ToDo, isso não acordo com a **identificação de objeto** do usuário no token (chamado por meio de token.oid), que vai no campo "proprietário". Esse valor garante que somente este usuário pode acessar seus próprios itens de ToDo. Não há nenhuma exposição na API do "proprietário", para que um usuário externo pode solicitar dos outros itens ToDo, mesmo se eles são autenticados.

Em seguida, use a estratégia de portador que vem com `passport-azure-ad`.

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var oidcStrategy = new OIDCBearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

Passport usa o mesmo padrão para todas as suas estratégias. Você passar um `function()` com `token` e `done` como parâmetros. A estratégia vem novamente após a atualização tiver sido concluída todo seu trabalho. Você deve armazenar o usuário e salve o token para que você não precisa perguntá-lo novamente.

> [AZURE.IMPORTANT]
O código acima leva qualquer usuário que acontece se autenticar ao seu servidor. Esse processo é conhecido como obter. Em servidores de produção, não deixe de qualquer acesso de usuários a API sem precisar primeiro-los passar por um processo de registro. Esse processo é geralmente o padrão que você vê nos aplicativos do consumidor que permitem que você registrar usando o Facebook, mas, em seguida, pedimos que você preencha informações adicionais. Se este programa não era um programa de linha, podemos poderia ter extraídos o email do objeto de token que é retornado e, em seguida, terá usuários preencher informações adicionais. Porque este é um exemplo, podemos adicioná-las a um banco de dados na memória.



## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>Executar o aplicativo de servidor confirmar que ele rejeita você

Você pode usar `curl` para ver se você possui agora OAuth2 proteção contra seus pontos de extremidade. Os cabeçalhos retornados devem ser suficiente para dizer que você está no caminho certo.

Certifique-se de que sua instância MongoDB está em execução:

    $sudo mongodb

Mude para o diretório e executar o servidor:

    $ cd azuread
    $ node server.js

Em uma nova janela terminal, executar`curl`

Tente uma POSTAGEM básica:

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Um erro 401 é a resposta desejada. Indica que a camada de Passport está tentando redirecionar para o ponto de extremidade de autorizar.


## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>Agora você tem um serviço de API REST que usa OAuth2

Você tiver implementado uma API REST usando Restify e OAuth! Agora, você tem código suficiente para que você pode continuar a desenvolver o seu serviço e desenvolver neste exemplo. Você tenha sido quanto você pode com este servidor sem usar um cliente compatível com o OAuth2. Para o próximo passo use um treinamento adicional como o nosso passo a passo de [conectar a uma web API usando iOS com B2C](active-directory-b2c-devquickstarts-ios.md) .


## <a name="next-steps"></a>Próximas etapas

Agora você pode mover tópicos mais avançados, tais como:

[Conectar a uma web API usando iOS com B2C](active-directory-b2c-devquickstarts-ios.md)