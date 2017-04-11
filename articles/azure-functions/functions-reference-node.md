<properties
    pageTitle="Referência do desenvolvedor funções NodeJS Azure | Microsoft Azure"
    description="Compreenda como desenvolver funções do Azure usando NodeJS."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure funções, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"/>

<tags
    ms.service="functions"
    ms.devlang="nodejs"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-nodejs-developer-reference"></a>Referência do desenvolvedor funções NodeJS Azure

> [AZURE.SELECTOR]
- [C# script](../articles/azure-functions/functions-reference-csharp.md)
- [F # script](../articles/azure-functions/functions-reference-fsharp.md)
- [Node](../articles/azure-functions/functions-reference-node.md)

A experiência de nó/JavaScript para funções do Azure facilita a exportar uma função que é passada um `context` objeto para se comunicar com o tempo de execução e para receber e enviar dados por meio de ligações.

Este artigo pressupõe que você já leu a [referência do desenvolvedor de funções do Azure](functions-reference.md).

## <a name="exporting-a-function"></a>Exportar uma função

Todas as funções de JavaScript devem exportar um único `function` via `module.exports` para o tempo de execução localizar a função e executá-lo. Esta função sempre deve incluir um `context` objeto.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Associações de `direction === "in"` passadas como argumentos de função, o que significa que você pode usar [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) dinamicamente lidar com novas entradas (por exemplo, usando `arguments.length` para iteração em todas as suas entradas). Esta funcionalidade é muito conveniente se tiver apenas um disparador com nenhum entradas adicionais, como você previsíveis pode acessar seus dados de disparador sem fazer referência seu `context` objeto.

Os argumentos sempre são passados para a função na ordem em que ocorrem em *function.json*, mesmo se você não especificá-los na instrução exportações. Por exemplo, se você tiver `function(context, a, b)` e altere-a para `function(context, a)`, você ainda pode acessar o valor de `b` no código de função fazendo referência a `arguments[3]`.

Todas as ligações, independentemente de direção, também são passadas ao longo do `context` objeto (veja abaixo). 

## <a name="context-object"></a>objeto de contexto

O runtime usa um `context` objeto para passar dados de e para sua função e permitem que você se comunicar com o tempo de execução.

O objeto de contexto é sempre o primeiro parâmetro para uma função e sempre devem ser incluído porque tem métodos como `context.done` e `context.log` que são necessárias para usar corretamente o tempo de execução. Você pode nomear o objeto que você quiser (ou seja, `ctx` ou `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

## <a name="contextbindings"></a>Context.Bindings

O `context.bindings` objeto reúne todos os dados de entrada e saídos. Os dados são adicionados para a `context.bindings` objeto o `name` propriedade da associação. Por exemplo, devido a seguinte definição de vinculação no *function.json*, você pode acessar o conteúdo da fila via `context.bindings.myInput`. 

```json
    {
        "type":"queue",
        "direction":"in",
        "name":"myInput"
        ...
    }
```

```javascript
// myInput contains the input data which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

## `context.done([err],[propertyBag])`

O `context.done` função informa o tempo de execução que terminar em execução. Isso é importante chamar quando terminar com a função; Se não estiver, o tempo de execução ainda nunca saberão que sua função concluído. 

O `context.done` função permite que você passe novamente um erro definidas pelo usuário para o tempo de execução, bem como um conjunto de propriedades de propriedades que substituirá as propriedades na `context.bindings` objeto.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

## <a name="contextlogmessage"></a>Context.log(Message)

O `context.log` método permite que você instruções de log que são correlacionadas juntos para fins de log de saída. Se você usar `console.log`, suas mensagens mostrará somente para o log de nível de processo, que não é tão útil.

```javascript
/* You can use context.log to log output specific to this 
function. You can access your bindings via context.bindings */
context.log({hello: 'world'}); // logs: { 'hello': 'world' } 
```

O `context.log` método dá suporte para o mesmo formato de parâmetro que suporta o nó [util.format método](https://nodejs.org/api/util.html#util_util_format_format) . Portanto, por exemplo, código assim:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

pode ser escrito assim:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

## <a name="http-triggers-contextreq-and-contextres"></a>Gatilhos de HTTP: context.req e context.res

No caso de disparadores de HTTP, porque ele é um padrão comum usar `req` e `res` para os objetos de solicitação e resposta HTTP, podemos decidiu tornam mais fácil acessar aqueles no objeto do contexto, em vez de forçar você a usar o total `context.bindings.name` padrão.

```javascript
// You can access your http request off of the context ...
if(context.req.body.emoji === ':pizza:') context.log('Yay!');
// and also set your http response
context.res = { status: 202, body: 'You successfully ordered more coffee!' };   
```

## <a name="node-version--package-management"></a>Gerenciamento de pacote & versão de nó

A versão de nó está bloqueada em `5.9.1`. Estamos pesquisando uma maneira de adicionar suporte para mais versões e facilitando configurável.

Você pode incluir pacotes em sua função carregar um arquivo de *package.json* pasta da sua função no sistema de arquivos do aplicativo de função. Arquivo de carregar instruções, consulte a seção de **como atualizar arquivos de aplicativo de função** do [tópico de referência do desenvolvedor de funções do Azure](functions-reference.md#fileupdate). 

Você também pode usar `npm install` na interface de linha de comando do aplicativo função SCM (Kudu):

1. Navegue até: `https://<function_app_name>.scm.azurewebsites.net`.

2. Clique em **Depurar Console > CMD**.

3. Navegue até `D:\home\site\wwwroot\<function_name>`.

4. Executar `npm install`.

Depois que os pacotes necessários estão instalados, importá-las à sua função no maneiras normais (isto é, por meio de `require('packagename')`)

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v5.9.1'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

## <a name="environment-variables"></a>Variáveis de ambiente

Para obter uma variável de ambiente ou um aplicativo configurando valor, use `process.env`, conforme mostrado no exemplo de código a seguir:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    
    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

## <a name="typescriptcoffeescript-support"></a>Suporte de typeScript/CoffeeScript

Não estiver lá, ainda, qualquer suporte direto para automático compilação TypeScript/CoffeeScript por meio de tempo de execução, portanto que faria todos precisam ser manipulados fora do runtime, no momento da implantação. 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes recursos:

* [Referência do desenvolvedor funções Azure](functions-reference.md)
* [Referência de desenvolvedor do Azure funções c#](functions-reference-csharp.md)
* [Referência de desenvolvedor do Azure funções F #](functions-reference-fsharp.md)
* [Ligações e azure disparadores de funções](functions-triggers-bindings.md)
