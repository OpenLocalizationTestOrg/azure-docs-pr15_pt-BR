<properties
    pageTitle="Azure ligações de funções DocumentDB | Microsoft Azure"
    description="Compreenda como usar o Azure DocumentDB ligações em funções do Azure."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="funções de Azure, funções, processamento de eventos, computação dinâmico, arquitetura sem servidor"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande; glenga"/>

# <a name="azure-functions-documentdb-bindings"></a>Azure ligações DocumentDB de funções

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e ligações do Azure DocumentDB de código em funções do Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="docdbinput"></a>Vinculação de entrada do Azure DocumentDB

Associações de entrada podem carregar um documento de um conjunto de DocumentDB e passar diretamente para sua vinculação. A id do documento pode ser determinada com base no acionador que chamou a função. Em uma função c#, quaisquer alterações feitas no registro serão automaticamente enviadas voltar à coleção quando a função sai com êxito.

#### <a name="functionjson-for-documentdb-input-binding"></a>Function.JSON DocumentDB associação de entrada

O arquivo *function.json* fornece as seguintes propriedades:

- `name`: Nome variável usado no código de função para o documento.
- `type`: deve estar definida como "documentdb".
- `databaseName`: O banco de dados que contém o documento.
- `collectionName`: A coleção que contém o documento.
- `id`: A Id do documento para recuperar. Essa propriedade oferece suporte a ligações semelhantes a "{queueTrigger}", que usará o valor de cadeia de caracteres da mensagem fila como o documento ID.
- `connection`: Essa cadeia de caracteres deve ser um conjunto de configuração do aplicativo para o ponto de extremidade para a sua conta de DocumentDB. Se você escolher sua conta a partir da guia de integrar, uma nova configuração de aplicativo será criada com um nome que possui o seguinte formato, yourAccount_DOCUMENTDB para você. Se você precisar criar manualmente a configuração do aplicativo, a cadeia de conexão real deve levar o seguinte formulário, AccountEndpoint =<Endpoint for your account>; AccountKey =<Your primary access key>;.
- ' direção: deve estar definida como *"em"*.

Exemplo *function.json*:
 
    {
      "bindings": [
        {
          "name": "document",
          "type": "documentdb",
          "databaseName": "MyDatabase",
          "collectionName": "MyCollection",
          "id" : "{queueTrigger}",
          "connection": "MyAccount_DOCUMENTDB",     
          "direction": "in"
        }
      ],
      "disabled": false
    }

#### <a name="azure-documentdb-input-code-example-for-a-c-queue-trigger"></a>Exemplo de código de entrada de DocumentDB Azure para um gatilho de fila c#
 
Usando o function.json de exemplo acima, a associação de entrada DocumentDB recuperar o documento com a id que corresponde a cadeia de caracteres de mensagem de fila e passe para o parâmetro 'documento'. Se esse documento não for encontrado, o parâmetro 'documento' será nulo. O documento é atualizado com o novo valor de texto quando a função sai.
 
    public static void Run(string myQueueItem, dynamic document)
    {   
        document.text = "This has changed.";
    }

#### <a name="azure-documentdb-input-code-example-for-an-f-queue-trigger"></a>Exemplo de código de entrada de DocumentDB Azure para um gatilho de fila F #

Usando o function.json de exemplo acima, a associação de entrada DocumentDB recuperar o documento com a id que corresponde a cadeia de caracteres de mensagem de fila e passe para o parâmetro 'documento'. Se esse documento não for encontrado, o parâmetro 'documento' será nulo. O documento é atualizado com o novo valor de texto quando a função sai.

    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, document: obj) =
        document?text <- "This has changed."

Você precisará de um `project.json` arquivo que usa NuGet para especificar o `FSharp.Interop.Dynamic` e `Dynamitey` pacotes como dependências de pacote, assim:

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

Isso usará NuGet para buscar suas dependências e fará referência-los no script.

#### <a name="azure-documentdb-input-code-example-for-a-nodejs-queue-trigger"></a>Exemplo de código de entrada de DocumentDB Azure para um gatilho de fila Node
 
Usando o function.json de exemplo acima, a associação de entrada DocumentDB recuperará o documento com a id que corresponde a cadeia de caracteres de mensagem de fila e passar para a `documentIn` propriedade de vinculação. Em funções de Node, documentos atualizados não são enviados para o conjunto. No entanto, você pode passar a associação de entrada diretamente em uma saída de DocumentDB encadernação nomeado `documentOut` oferecer suporte a atualizações. Este exemplo de código atualiza a propriedade de texto do documento entrada e define como o documento de saída.
 
    module.exports = function (context, input) {   
        context.bindings.documentOut = context.bindings.documentIn;
        context.bindings.documentOut.text = "This was updated!";
        context.done();
    };

## <a id="docdboutput"></a>Azure DocumentDB ligações de saída

Suas funções podem escrever JSON ligação de saída de documentos para um banco de dados do Azure DocumentDB usando o **Azure DocumentDB documento** . Para obter mais informações sobre DocumentDB Azure Revise a [Introdução ao DocumentDB](../documentdb/documentdb-introduction.md) e o [tutorial de Introdução](../documentdb/documentdb-get-started.md).

#### <a name="functionjson-for-documentdb-output-binding"></a>ligação de saída de Function.JSON para DocumentDB

O arquivo function.json fornece as seguintes propriedades:

- `name`: Nome variável usado no código de função para o novo documento.
- `type`: deve estar definida como *"documentdb"*.
- `databaseName`: O banco de dados que contém a coleção onde o novo documento será criado.
- `collectionName`: A coleção onde o novo documento será criado.
- `createIfNotExists`: Este é um valor booliano para indicar se o conjunto será criado se ele não existir. O padrão é *Falso*. O motivo para isso é novo coleções são criadas com produtividade reservada, o que tem implicações de preços. Para obter mais detalhes, visite a [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection`: Essa cadeia de caracteres deve ser que uma **Configuração de aplicativo** definido como o ponto de extremidade para a sua conta de DocumentDB. Se você escolher sua conta a partir da guia de **integrar** , uma nova configuração de aplicativo será criada para você com um nome que leva a forma a seguir, `yourAccount_DOCUMENTDB`. Se você precisar criar manualmente a configuração do aplicativo, a cadeia de conexão real deve levar a forma a seguir, `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`. 
- `direction`: deve estar definida como *"-out"*. 
 
Function.json de exemplo:

    {
      "bindings": [
        {
          "name": "document",
          "type": "documentdb",
          "databaseName": "MyDatabase",
          "collectionName": "MyCollection",
          "createIfNotExists": false,
          "connection": "MyAccount_DOCUMENTDB",
          "direction": "out"
        }
      ],
      "disabled": false
    }


#### <a name="azure-documentdb-output-code-example-for-a-nodejs-queue-trigger"></a>Exemplo de código de saída DocumentDB Azure para um disparador de fila Node

    module.exports = function (context, input) {
       
        context.bindings.document = {
            text : "I'm running in a Node function! Data: '" + input + "'"
        }   
     
        context.done();
    };

O documento de saída:

    {
      "text": "I'm running in a Node function! Data: 'example queue data'",
      "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
    }
 

#### <a name="azure-documentdb-output-code-example-for-an-f-queue-trigger"></a>Exemplo de código de saída DocumentDB Azure para um gatilho de fila F #

    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, document: obj) =
        document?text <- (sprintf "I'm running in an F# function! %s" myQueueItem)

#### <a name="azure-documentdb-output-code-example-for-a-c-queue-trigger"></a>Exemplo de código de saída DocumentDB Azure para um disparador de fila c#


    using System;

    public static void Run(string myQueueItem, out object document, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
       
        document = new {
            text = $"I'm running in a C# function! {myQueueItem}"
        };
    }


#### <a name="azure-documentdb-output-code-example-setting-file-name"></a>Azure DocumentDB saída código exemplo configuração nome do arquivo

Se você deseja definir o nome do documento na função, basta definir a `id` valor.  Por exemplo, se JSON conteúdo para um funcionário foi sendo descartado na fila similar ao seguinte:

    {
      "name" : "John Henry",
      "employeeId" : "123456",
      "address" : "A town nearby"
    }

Você pode usar o seguinte código c# em uma função de gatilho de fila: 
    
    #r "Newtonsoft.Json"
    
    using System;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        
        dynamic employee = JObject.Parse(myQueueItem);
        
        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }

Ou o código F # equivalente:

    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
        id: string
        name: string
        employeeId: string
        address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
        log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
        let employee = JObject.Parse(myQueueItem)
        employeeDocument <-
            { id = sprintf "%s-%s" employee?name employee?employeeId
              name = employee?name
              employeeId = employee?id
              address = employee?address }

Exemplo de saída:

    {
      "id": "John Henry-123456",
      "name": "John Henry",
      "employeeId": "123456",
      "address": "A town nearby"
    }

## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
