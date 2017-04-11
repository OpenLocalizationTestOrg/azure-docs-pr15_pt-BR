<properties
    pageTitle="Azure ligações HTTP de funções e webhook | Microsoft Azure"
    description="Compreenda como usar disparadores HTTP e webhook e ligações em funções do Azure."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure funções, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande"/>

# <a name="azure-functions-http-and-webhook-bindings"></a>Azure ligações HTTP de funções e webhook

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e o código de disparadores HTTP e webhook e ligações em funções do Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a name="functionjson-for-http-and-webhook-bindings"></a>Function.JSON para ligações HTTP e webhook

O arquivo *function.json* fornece propriedades que pertencem de solicitação e resposta.

Propriedades para a solicitação HTTP:

- `name`: Nome variável usado no código de função para o objeto de solicitação (ou o corpo da solicitação no caso de funções node).
- `type`: Deve ser definida como *httpTrigger*.
- `direction`: Deve ser definida como *no*. 
- `webHookType`: Para WebHook disparadores, valores válidos são *github*, *margem de atraso*e *genericJson*. Para um gatilho HTTP que não é um WebHook, defina esta propriedade como uma cadeia de caracteres vazia. Para obter mais informações sobre WebHooks, consulte a seção a seguir [WebHook disparadores](#webhook-triggers) .
- `authLevel`: Não se aplica a WebHook disparadores. Defina como "função" para exigir a chave API, "anônima" para soltar a API principal requisito ou "administrador" para exigir a chave API mestre. Consulte [chaves de API](#apikeys) abaixo para obter mais informações.

Propriedades para a resposta HTTP:

- `name`: Nome variável usado no código de função para o objeto de resposta.
- `type`: Deve ser definida para *http*.
- `direction`: Deve ser definida como *check-out*. 
 
Exemplo *function.json*:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="webhook-triggers"></a>Gatilhos de WebHook

Um gatilho de WebHook é um gatilho HTTP que tem os seguintes recursos projetados para WebHooks:

* Para provedores de WebHook específico (atualmente GitHub e atraso são suportados), o tempo de execução de funções valida a assinatura do provedor.
* Para as funções do Node, o tempo de execução de funções oferece o corpo da solicitação em vez do objeto de solicitação. Não há nenhum tratamento especial para c# funções, porque você controlar o que é fornecido, especificando o tipo de parâmetro. Se você especificar `HttpRequestMessage` você obtém o objeto de solicitação. Se você especificar um tipo POCO, o tempo de execução de funções tenta analisar um objeto JSON no corpo da solicitação para preencher as propriedades do objeto.
* Para disparar um WebHook função a solicitação HTTP deve incluir uma chave API. Para disparadores não WebHook HTTP, esse requisito é opcional.

Para obter informações sobre como configurar um GitHub WebHook, consulte [GitHub Developer - criando WebHooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409).

## <a name="url-to-trigger-the-function"></a>URL para a função do disparador

Para disparar uma função, você pode enviar uma solicitação HTTP para uma URL que é uma combinação da URL do aplicativo de função e o nome da função:

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

## <a name="api-keys"></a>Teclas de API

Por padrão, uma chave API deve ser incluída com uma solicitação HTTP para acionar uma função de HTTP ou WebHook. A chave pode ser incluída em uma variável de cadeia de caracteres de consulta denominada `code`, ou pode ser incluído em um `x-functions-key` cabeçalho HTTP. Para não-WebHook funções, você pode indicar que uma chave API não é necessário definindo a `authLevel` propriedade como "anônimo" no arquivo *function.json* .

Você pode encontrar valores-chave API na pasta *D:\home\data\Functions\secrets* no sistema de arquivos do aplicativo função.  A chave mestre e uma tecla de função são definidas no arquivo *host.json* , conforme mostrado neste exemplo. 

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

A tecla de função de *host.json* pode ser usada para qualquer função do disparador, mas não acionaram uma função desativado. A chave mestra pode ser usada para disparar qualquer função e acionará uma função, mesmo se ele estiver desativado. Você pode configurar uma função para exigir a chave mestra definindo a `authLevel` propriedade para "administrador". 

Se a pasta de *segredos* contiver um arquivo JSON com o mesmo nome de uma função, o `key` propriedade nesse arquivo também pode ser usada para acionar a função e esta chave só funcionará com a função que ele se refere. Por exemplo, a chave de API para uma função chamada `HttpTrigger` especificado no *HttpTrigger.json* na pasta *segredos* . Aqui está um exemplo:

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] Quando você estiver configurando um disparador WebHook, não compartilhe a chave mestre com o provedor de WebHook. Use uma chave que só funcionam com a função que processa o WebHook.  A chave mestra pode ser usada para disparar qualquer função, mesmo desativados funções.

## <a name="example-c-code-for-an-http-trigger-function"></a>Código de exemplo c# para uma função de gatilho HTTP 

O código de exemplo procura um `name` parâmetro na cadeia de caracteres de consulta ou o corpo da solicitação HTTP.

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

## <a name="example-f-code-for-an-http-trigger-function"></a>Código de exemplo F # para uma função de gatilho HTTP

O código de exemplo procura um `name` parâmetro na cadeia de caracteres de consulta ou o corpo da solicitação HTTP.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Você precisará de um `project.json` arquivo que usa NuGet para fazer referência a `FSharp.Interop.Dynamic` e `Dynamitey` conjuntos, assim:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

Isso usará NuGet para buscar suas dependências e fará referência-los no script.

## <a name="example-nodejs-code-for-an-http-trigger-function"></a>Exemplo de código Node para uma função de gatilho HTTP 

Este código de exemplo procura um `name` parâmetro na cadeia de caracteres de consulta ou o corpo da solicitação HTTP.

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

## <a name="example-c-code-for-a-github-webhook-function"></a>Código de exemplo c# para uma função de GitHub WebHook 

Este exemplo de código logs GitHub comentários de problema.

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

## <a name="example-f-code-for-a-github-webhook-function"></a>Código de exemplo F # para uma função de GitHub WebHook

Este exemplo de código logs GitHub comentários de problema.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

## <a name="example-nodejs-code-for-a-github-webhook-function"></a>Exemplo de código Node para uma função de GitHub WebHook 

Este exemplo de código logs GitHub comentários de problema.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
