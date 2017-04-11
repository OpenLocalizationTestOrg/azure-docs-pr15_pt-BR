<properties
    pageTitle="Azure AD v 2.0 NodeJS Web API | Microsoft Azure"
    description="Como criar uma API de Web NodeJS aceita tokens de ambas as Account pessoal da Microsoft e contas de trabalho ou da escola."
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
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="secure-a-web-api-using-nodejs"></a>Proteger uma API Web usando Node

> [AZURE.NOTE]
    Nem todos os cenários do Active Directory do Azure e recursos são compatíveis com o ponto de extremidade de v 2.0.  Para determinar se você deve usar o ponto de extremidade de v 2.0, leia sobre as [limitações da versão 2.0](active-directory-v2-limitations.md).

Com o Active Directory do Azure o ponto de extremidade de v 2.0, você pode proteger um API de Web usando tokens de acesso [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) , permitindo que os usuários com pessoal conta da Microsoft e o trabalho ou escola contas para o access com segurança sua API da Web.

**Passport** é middleware de autenticação para Node. Muito flexível e modular, Passport pode ser atualizam descartado para qualquer expressas ou Resitify aplicativo web. Um conjunto abrangente de estratégias de suporte à autenticação usando um nome de usuário e senha, Facebook, Twitter e muito mais. Podemos desenvolveu uma estratégia Microsoft Azure Active Directory. Podemos irá instalar este módulo e adicione o Active Directory do Microsoft Azure `passport-azure-ad` plug-in.

## <a name="download"></a>Baixar
O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs).  Para acompanhar, você pode [Baixar esqueleto do aplicativo como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip) ou clonar a estrutura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

O aplicativo concluído é fornecido no final deste tutorial também.


## <a name="1-register-an-app"></a>1. registrar um aplicativo
Criar um novo aplicativo no [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou siga estas [etapas detalhadas](active-directory-v2-app-registration.md).  Certifique-se de:

- Copiar a **Id de aplicativo** atribuída ao seu aplicativo, você precisará dela em breve.
- Adicione **a plataforma para o aplicativo** .
- Copiar até o **URI redirecionar** a partir do portal. Você deve usar o valor padrão de `urn:ietf:wg:oauth:2.0:oob`.


## <a name="2-download-nodejs-for-your-platform"></a>2: baixar Node para sua plataforma
Para usar com êxito neste exemplo, você deve ter uma instalação funcional do Node.

Instale o Node de [http://nodejs.org](http://nodejs.org).

## <a name="3-install-mongodb-on-to-your-platform"></a>3: instalar MongoDB-lo sua plataforma

Para usar com êxito neste exemplo, você deve ter uma instalação funcional do MongoDB. Usaremos MongoDB para tornar nosso persistant API REST nas instâncias do servidor.

Instale o MongoDB de [http://mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Este passo a passo supõe que você usa os pontos de extremidade de instalação e servidor para MongoDB, que, no momento da redação deste artigo, é: mongodb://localhost

## <a name="4-install-the-restify-modules-in-to-your-web-api"></a>4: instalar os módulos Restify à sua Web API

Usaremos Resitfy para construir nosso API REST. Restify é uma estrutura de aplicativo Node mínimo e flexível derivada de Express que tem um conjunto robusto de recursos para a criação de APIs REST sobre conectar.

### <a name="install-restify"></a>Restify de instalação

Na linha de comando, altere diretórios para o diretório azuread. Se o diretório **azuread** não existir, crie-a.

`cd azuread`- ou -`mkdir azuread;`

Digite o seguinte comando:

`npm install restify`

Este comando instala Restify.

#### <a name="did-you-get-an-error"></a>Você obteve um erro?

Ao usar npm em alguns sistemas operacionais, você poderá receber um erro de erro: EPERM, chmod '/ usr/local/bin / …' e uma solicitação para tentar executar a conta como administrador. Nesse caso, use o comando sudo para executar npm em um nível de privilégio superior.

#### <a name="did-you-get-an-error-regarding-dtrace"></a>Você obteve um erro com relação ao DTrace?

Você pode ver algo parecido com isto ao instalar o Restify:

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


Restify fornece um mecanismo poderoso para rastrear chamadas REST usando o DTrace. No entanto, muitos sistemas operacionais não têm DTrace disponível. Você pode ignorar com segurança esses erros.


A saída deste comando deve aparecer semelhante ao seguinte:


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
    └── bunyan@0.22.0(mv@0.0.5)


## <a name="5-install-passportjs-into-your-web-api"></a>5: instalar Passport.js para sua Web API

[Passport](http://passportjs.org/) é middleware de autenticação para Node. Muito flexível e modular, Passport pode ser atualizam descartado para qualquer expressas ou Resitify aplicativo web. Um conjunto abrangente de estratégias de suporte à autenticação usando um nome de usuário e senha, Facebook, Twitter e muito mais. Podemos desenvolveu uma estratégia para Azure Active Directory. Abordaremos instalar este módulo e adicione a estratégia de Active Directory do Azure plug-in.

Na linha de comando, altere diretórios para o diretório azuread.

Digite o seguinte comando para instalar o passport.js

`npm install passport`

A saída do comando deve aparecer semelhante ao seguinte:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6: Adicionar Passport Azure-AD à sua Web API

Em seguida, adicionaremos a estratégia de OAuth, usando o passport-azuread, um conjunto de estratégias que conectam Azure Active Directory com Passport. Usaremos essa estratégia para Tokens de portador neste exemplo API Rest.

> [AZURE.NOTE] Embora OAuth2 fornece uma estrutura em que qualquer tipo de token conhecido pode ser emitido, somente determinados tipos de token ganharam ampla uso. Para proteger os pontos de extremidade, que tem acabou sendo Tokens de portador. Tokens de portador são mais amplamente emitido tipo de token de OAuth2 e muitas implementações presumem que tokens de portador são o único tipo de token emitido.

Na linha de comando, altere diretórios para o diretório azuread

Digite o seguinte comando para instalar o módulo de passport azure-ad Passport.js:

`npm install passport-azure-ad`

A saída do comando deve aparecer semelhante ao seguinte:

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

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7: Adicionar módulos MongoDB à sua Web API

Usaremos MongoDB como nosso armazenamento de dados por esse motivo, precisamos instalar ambas as o amplamente usado plug-in para gerenciar modelos e esquemas chamado Mongoose, bem como o driver de banco de dados para MongoDB, também chamado MongoDB.


* `npm install mongoose`
* `npm install mongodb`

## <a name="8-install-additional-modules"></a>8: instalar módulos adicionais

Em seguida, podemos vai instalar os módulos necessários restantes.


Na linha de comando, altere diretórios para a pasta **azuread** se não estiver lá:

`cd azuread`


Digite os seguintes comandos para instalar os seguintes módulos no diretório node_modules:

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## <a name="9-create-a-serverjs-with-your-dependencies"></a>9: criar um Server. js com suas dependências

O arquivo Server. js fornecerá a maioria dos nossa funcionalidade para nosso servidor de Web API. Vamos adicionar na maioria das nosso código para esse arquivo. Para fins de produção você faria refatorar a funcionalidade aos arquivos menores, como rotas separadas e controladores. Nesta demonstração, usaremos Server. js para essa funcionalidade.

Na linha de comando, altere diretórios para a pasta **azuread** se não estiver lá:

`cd azuread`

Criar uma `server.js` no nosso editor favorito e adicione as seguintes informações:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
```

Salve o arquivo. Podemos retornará-lo em breve.

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10: criar um arquivo de configuração para armazenar suas configurações do Azure AD

Este arquivo de código passa os parâmetros de configuração de seu Portal do Azure Active Directory para Passport.js. Você criou esses valores de configuração quando você adicionou a API da Web para o portal na primeira parte da explicação. Explicaremos o que colocar os valores desses parâmetros após copiar o código.


Na linha de comando, altere diretórios para a pasta **azuread** se não estiver lá:

`cd azuread`

Criar uma `config.js` no nosso editor favorito e adicione as seguintes informações:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
issuer: 'https://sts.windows.net/**<your application id>**/',
audience: '<your redirect URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For using Microsoft you should never need to change this.
};

```



### <a name="required-values"></a>Valores necessários

*IdentityMetadata*: Este é qual será a aparência da passport azure-ad para seus dados de configuração para IdP, bem como as teclas validar os tokens JWT. Você provavelmente não deseja alterar isso se usando o Active Directory do Azure.

*público*: seu URI de redirecionamento a partir do portal.

> [AZURE.NOTE]
Podemos implementar o nossas teclas intervalos frequentes. Certifique-se de que você sempre recebendo da URL "openid_keys" e que o aplicativo pode acessar a internet.


## <a name="11-add-configuration-to-your-serverjs-file"></a>11: Adicionar configuração ao arquivo Server. js

Precisamos leia estes valores do arquivo de configuração que você acabou de criar em nosso aplicativo. Para fazer isso, podemos simplesmente adicionar o arquivo. config como um recurso necessário em nosso aplicativo e defina as variáveis globais no documento config.js

Na linha de comando, altere diretórios para a pasta **azuread** se não estiver lá:

`cd azuread`

Abrir sua `server.js` no nosso editor favorito e adicione as seguintes informações:

```Javascript
var config = require('./config');
```
Em seguida, adicione uma nova seção `server.js` com o seguinte código:

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
identityMetadata: config.creds.identityMetadata,
issuer: config.creds.issuer,
audience: config.creds.audience
};
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="step-12-add-the-mongodb-model-and-schema-information-using-moongoose"></a>Etapa 12: Adicionar o modelo de MongoDB e informações de esquema usando Moongoose

Agora todos os essa preparação vai iniciar pagar como podemos terminar esses três arquivos juntos para um serviço de API REST.

Para este passo a passo usaremos MongoDB para armazenar nossas tarefas conforme discutido na ***etapa 4***.

Se você se lembrar do arquivo config.js criado na etapa 11, chamamos nossa *lista de tarefas*do banco de dados, como que estava que podemos colocar no final da nossa URL de conexão mogoose_auth_local. Você não precisa criar esse banco de dados antecipadamente no MongoDB, ele irá criar isso para nós na primeira execução do nosso aplicativo de servidor (presumindo que ele ainda não existir).

Agora que dissemos o servidor de banco de dados que MongoDB podemos gostaria de usar, precisamos escrever código adicional para criar o modelo e o esquema de tarefas do nosso servidor.

#### <a name="discussion-of-the-model"></a>Discussão do modelo

Nosso modelo de esquema é muito simple e expandi-lo conforme necessário.

NAME - o nome de quem está atribuído à tarefa. Uma ***cadeia de caracteres***

TAREFA - a tarefa em si. Uma ***cadeia de caracteres***

Data - a data em que a tarefa deva ser concluída. Um ***DATETIME***

CONCLUÍDO - se a tarefa é concluída ou não. ***BOOLIANA***

#### <a name="creating-the-schema-in-the-code"></a>Criar o esquema no código


Na linha de comando, altere diretórios para a pasta **azuread** se não estiver lá:

`cd azuread`

Abrir sua `server.js` no nosso editor favorito e adicione as seguintes informações abaixo da entrada de configuração:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Isso conectar ao servidor MongoDB e devolver um objeto de esquema conosco.

#### <a name="using-the-schema-create-our-model-in-the-code"></a>Usando o esquema, criar nosso modelo no código

Abaixo do código escrito acima, adicione o seguinte código:

```Javascript
// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Como você pode ver do código, podemos criar nossa esquema e crie um objeto de modelo que usaremos para armazenar nossos dados por todo o código quando definimos nosso ***rotas***.

## <a name="step-13-add-our-routes-for-our-task-rest-api-server"></a>Etapa 13: Adicionar nossas rotas de nosso servidor API REST de tarefa

Agora que temos um modelo de banco de dados para trabalhar com, vamos adicionar as rotas que usaremos para nosso servidor API REST.

### <a name="about-routes-in-restify"></a>Sobre rotas em Restify

Rotas funcionam no Restify da mesma maneira exata funcionam usando a pilha Express. Você define rotas usando o URI que você espera que os aplicativos de cliente para chamar. Geralmente, você definir suas rotas em um arquivo separado. Para nossos propósitos, podemos colocará nossas rotas no arquivo Server. js. Recomendamos que você considerar estes para seu próprio arquivo para uso de produção.

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


Este é o padrão no nível mais básico. Resitfy (e Express) fornecem muito mais profunda functionaltiy como definir tipos de aplicativo e fazendo roteamento complexas entre diferentes pontos de extremidade. Para nossos propósitos, podemos manterão essas rotas muito simples.

#### <a name="add-default-routes-to-our-server"></a>Adicionar rotas padrão ao nosso servidor

Agora, podemos irá adicionar as rotas CRUD básicas de atualização de criar, recuperar e excluir.

Na linha de comando, altere diretórios para a pasta **azuread** se não estiver lá:

`cd azuread`

Abrir sua `server.js` no nosso editor favorito e adicione as seguintes informações abaixo as entradas de banco de dados feitas acima:

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
if (!req.params.task) {
req.log.warn({
params: p
}, 'createTodo: missing task');
next(new MissingTaskError());
return;
}
_task.owner = owner;
_task.task = req.params.task;
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
// Delete a task by name
function removeTask(req, res, next) {
Task.remove({
task: req.params.task,
owner: owner
}, function(err) {
if (err) {
req.log.warn(err,
'removeTask: unable to delete %s',
req.params.task);
next(err);
} else {
log.info('Deleted task:', req.params.task);
res.send(204);
next();
}
});
}
// Delete all tasks
function removeAll(req, res, next) {
Task.remove();
res.send(204);
return next();
}
// Get a specific task based on name
function getTask(req, res, next) {
log.info('getTask was called for: ', owner);
Task.find({
owner: owner
}, function(err, data) {
if (err) {
req.log.warn(err, 'get: unable to read %s', owner);
next(err);
return;
}
res.json(data);
});
return next();
}
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

### <a name="add-some-error-handling-for-the-routes"></a>Adicionar alguns tratamento de erro para as rotas

Faz sentido para adicionar alguns tratamento de erro para nós possam se comunicar volta para o cliente o problema que encontramos de forma que ele pode entender.

Adicione o seguinte código sob o código que você escreveu acima:

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


## <a name="step-14-create-your-server"></a>Etapa 14: Crie seu servidor!

Temos nosso banco de dados definido, temos nossas rotas no lugar e a última coisa a fazer é adicionar nossa instância do servidor que gerenciará nossas chamadas.

Restify (e Express) tiver muitas profunda personalização, você pode fazer para um servidor de API REST, mas novamente usaremos a configuração mais básica para nossas finalidades.

```Javascript
/**
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
}));
```
## <a name="15-adding-the-routes-without-authentication-for-now"></a>15: adicionar as rotas (sem autenticação por agora)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers are protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the pasport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiement removing API protection
/* by removing the passport.authenticate() method like so:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## <a name="16-before-we-add-oauth-support-lets-run-the-server"></a>16: antes de adicionar suporte OAuth, vamos executar o servidor.

Testar seu servidor antes de adicionar autenticação

A maneira mais fácil de fazer isso é usando ondulação em uma linha de comando. Antes de fazer isso, precisamos de um utilitário simples que nos permite analisar saída como JSON. Para fazer isso, instale a ferramenta de json como usá-lo em todos os exemplos abaixo.

`$npm install -g jsontool`

Isso instala a ferramenta JSON globalmente. Agora que estamos já realizado que – vamos reproduzir com o servidor:

Primeiro, certifique-se de que seu isntance monogoDB está sendo executado..

`$sudo mongod`

Em seguida, mude para o diretório e iniciar curling..

`$ cd azuread`
`$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 2.0OK
Connection: close
Content-Type: application/json
Content-Length: 171
Date: Tue, 14 Jul 2015 05:43:38 GMT
[
"GET /",
"POST /tasks/:owner/:task",
"POST /tasks (for JSON body)",
"GET /tasks",
"PUT /tasks/:owner",
"GET /tasks/:owner",
"DELETE /tasks/:owner/:task"
]
```

Em seguida, podemos adicionar uma tarefa desta forma:

`$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

A resposta deve ser:

```Shell
HTTP/1.1 201 Created
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Content-Type: application/x-www-form-urlencoded
Content-Length: 5
Date: Tue, 04 Feb 2014 01:02:26 GMT
Hello
```
E podemos pode listar tarefas para Brandon desta forma:

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Se tudo isso funciona, estamos prontos para adicionar OAuth ao servidor API REST.

**Você tiver um servidor de API REST com MongoDB!**

## <a name="17-add-authentication-to-our-rest-api-server"></a>17: Adicione autenticação ao nosso servidor de API REST

Agora que temos uma API REST em execução (parabenização, btw!) Vamos torná-lo útil contra Azure AD.

Na linha de comando, altere diretórios para a pasta **azuread** se não estiver lá:

`cd azuread`

### <a name="1-use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>1: use o oidcbearerstrategy que está incluído no passport azure-ad

Até agora, criamos um servidor restante TODO típico sem qualquer tipo de autorização. Isso é onde começaremos juntando que.

Primeiro, precisamos indicar que queremos usar Passport. Coloque esse direito após a configuração do servidor:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
Ao escrever APIs você deve sempre vincular os dados para algo exclusivo do token que o usuário não pode simular. Quando este servidor armazena itens TODO, armazena-os com base na ID da assinatura do usuário no token (chamado por meio de token.sub) qual podemos colocar no campo "proprietário". Isso garante que somente esse usuário pode acessar seus TODOs e ninguém pode acessar os TODOs inseridos. Não há nenhuma exposição na API do "proprietário" para que um usuário externo pode solicitar TODOs dos outros, mesmo se eles são autenticados.

Em seguida, vamos usar a estratégia de abrir ID conectar portador que vem com o passport azure-ad. Basta procurar o código agora, eu explique em breve. Coloca isso depois que você pated acima:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementor.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
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
log.info('User was added automatically as they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Passport usa um padrão semelhante para todos é estratégias (Twitter, Facebook, etc.) que todos os autores de estratégia adicional. Examinando a estratégia verá que podemos passar uma função que tenha um token e concluído como os parâmetros. A estratégia obedientemente retornarão conosco depois que ela faz tudo-lo do trabalho. Depois que ela faz será queremos armazenar o usuário e guardar o token, portanto, não precisamos perguntá-lo novamente.

> [AZURE.IMPORTANT]
O código acima leva qualquer usuário que acontece se autenticar com nosso servidor. Isso é conhecido como registro automático. Em servidores de produção não quiser permitir que qualquer pessoa sem precisar primeiro-los passar por um processo de registro que você decida. Isso geralmente é o padrão que você vê nos aplicativos do consumidor que permitem que você registrar com o Facebook, mas, em seguida, pedimos que você preencha informações adicionais. Se isso não era um programa de linha de comando, podemos poderia ter apenas extraídos o email do objeto de token que é retornado e pedir que ele preencha informações adicionais. Como este é um servidor de teste nós simplesmente adicioná-los ao banco de dados na memória.

### <a name="2-finally-protect-some-endpoints"></a>2. Finalmente, proteger alguns pontos de extremidade

Você pode proteger pontos de extremidade, especificando a chamada passport.authenticate() com o protocolo que deseja usar.

Vamos Editar nosso roteiro em nosso código de servidor para fazer algo mais interessante:

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again-and-ensure-it-rejects-you"></a>18: executar o aplicativo de servidor novamente e certifique-se de que ele rejeita você

Vamos usar `curl` novamente para ver se agora temos OAuth2 proteção contra nossos pontos de extremidade. Faremos isso antes executado qualquer um dos nossos clientes SDKs em relação a esse ponto de extremidade. Os cabeçalhos retornados devem ser suficiente para nos dizer que estamos no caminho certo.

Primeiro, certifique-se de que seu isntance monogoDB está sendo executado..

    $sudo mongod

Em seguida, mude para o diretório e iniciar curling..

    $ cd azuread
    $ node server.js

Tente uma POSTAGEM básica:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Um 401 é a resposta que você está procurando aqui, como que indica que a camada de Passport está tentando redirecionar para o ponto de extremidade de autorizar, que é exatamente o que você deseja.


## <a name="congratulations-you-have-a-rest-api-service-using-oauth2"></a>Parabéns! Você tem um serviço de API REST usando OAuth2!

Você já deu quanto você pode com este servidor sem usar um cliente compatível com OAuth2. Você precisará percorrer uma explicação adicional.

Se você apenas estava procurando informações sobre como implementar um API REST usando Restify e OAuth2, você tem mais que suficiente código para manter desenvolvendo seu serviço e aprender como construir sobre este exemplo.

## <a name="next-steps"></a>Próximas etapas

Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um. zip aqui](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Agora, você pode mover em tópicos mais avançados.  Talvez você queira experimentar:

[Proteger um Node web app usando o ponto de extremidade de v 2.0 >>](active-directory-v2-devquickstarts-node-web.md)

Para obter recursos adicionais, consulte:
- [O guia do desenvolvedor v 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Marca de StackOverflow "azure-active directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para nossos produtos

Recomendamos que você obtenha as notificações de quando ocorrências de segurança ocorrerem visitando [esta página](https://technet.microsoft.com/security/dd252948) e assinar alertas de segurança comunicado.
