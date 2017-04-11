<properties
    pageTitle="Azure disparadores de funções e ligações para o armazenamento do Azure | Microsoft Azure"
    description="Compreenda como usar disparadores de armazenamento do Azure e ligações em funções do Azure."
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
    ms.author="chrande"/>

# <a name="azure-functions-triggers-and-bindings-for-azure-storage"></a>Azure disparadores de funções e ligações para o armazenamento do Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e gatilhos de armazenamento do Azure de código e ligações em funções do Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="storagequeuetrigger"></a>Azure gatilho de fila do armazenamento

#### <a name="functionjson-for-storage-queue-trigger"></a>Function.JSON para disparador de fila do armazenamento

O arquivo de *function.json* Especifica as seguintes propriedades.

- `name`: O nome da variável usado no código de função para fila ou a mensagem de fila. 
- `queueName`: O nome da fila para pesquisar. Para regras de nomenclatura de fila, consulte [filas de nomenclatura e metadados](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: O nome da configuração do aplicativo que contém uma cadeia de conexão de armazenamento. Se você deixar `connection` vazio, o disparador funcionarão com a cadeia de conexão de armazenamento padrão para o aplicativo de função, que é especificada pela configuração do aplicativo AzureWebJobsStorage.
- `type`: Deve ser definida como *queueTrigger*.
- `direction`: Deve ser definida como *no*. 

Exemplo *function.json* para um disparador de fila do armazenamento:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

#### <a name="queue-trigger-supported-types"></a>Gatilho de fila tipos com suporte

A mensagem de fila pode ser desserializada a qualquer um dos seguintes tipos:

* Objeto (a partir do JSON)
* Cadeia de caracteres
* Matriz de bytes 
* `CloudQueueMessage`(C#) 

#### <a name="queue-trigger-metadata"></a>Fila disparador metadados

Você pode obter metadados de fila em sua função usando esses nomes de variável:

* expirationTime
* insertionTime
* nextVisibleTime
* ID
* popReceipt
* dequeueCount
* queueTrigger (outra maneira de recuperar o texto da mensagem fila como uma cadeia de caracteres)

Este exemplo de código c# recupera e logs fila metadados:

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### <a name="handling-poison-queue-messages"></a>Manipulação de mensagens suspeitas fila

Mensagens cujo conteúdo faz com que uma função falha são chamadas *mensagens suspeitas*. Quando a função falhar, a mensagem de fila não é excluída e eventualmente é selecionada novamente, causando o ciclo seja repetido. O SDK pode interromper o ciclo automaticamente após um número limitado de iterações ou você pode fazê-lo manualmente.

O SDK chamará uma função até 5 vezes para processar uma mensagem de fila. Se o quinto experimentar falhar, a mensagem é movida para uma fila de suspeita.

Fila de suspeitas é chamada *{originalqueuename}*-suspeita. Você pode escrever uma função para processar mensagens de fila de suspeitas log-las ou enviando uma notificação que atenção manual é necessária. 

Se você deseja lidar com mensagens suspeitas manualmente, você pode obter o número de vezes que uma mensagem foi separada para cima para processamento marcando `dequeueCount`.

## <a id="storagequeueoutput"></a>Ligação de saída do Azure fila do armazenamento

#### <a name="functionjson-for-storage-queue-output-binding"></a>ligação de saída de Function.JSON para fila do armazenamento

O arquivo de *function.json* Especifica as seguintes propriedades.

- `name`: O nome da variável usado no código de função para fila ou a mensagem de fila. 
- `queueName`: O nome da fila. Para regras de nomenclatura de fila, consulte [filas de nomenclatura e metadados](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: O nome da configuração do aplicativo que contém uma cadeia de conexão de armazenamento. Se você deixar `connection` vazio, o disparador funcionarão com a cadeia de conexão de armazenamento padrão para o aplicativo de função, que é especificada pela configuração do aplicativo AzureWebJobsStorage.
- `type`: Deve ser definida como *fila*.
- `direction`: Deve ser definida como *check-out*. 

Exemplo *function.json* para uma fila do armazenamento de saída encadernação que usa um disparador fila e grava uma mensagem de fila:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="queue-output-binding-supported-types"></a>Tipos de vinculação de saída com suporte de fila

O `queue` vinculação pode serializar os seguintes tipos de uma mensagem de fila:

* Objeto (`out T` em c#, cria uma mensagem com um objeto nulo se o parâmetro é nulo quando terminar a função)
* Cadeia de caracteres (`out string` em c#, cria a mensagem de fila se o valor do parâmetro for nulos quando terminar a função)
* Matriz de bytes (`out byte[]` em c#, funciona como cadeia de caracteres) 
* `out CloudQueueMessage`(C#, funciona como cadeia de caracteres) 

Em c# você também pode vincular a `ICollector<T>` ou `IAsyncCollector<T>` onde `T` é um dos tipos com suporte.

#### <a name="queue-output-binding-code-examples"></a>Exemplos de código de vinculação de saída de fila

Este exemplo de código c# grava uma mensagem de fila de saída único para cada mensagem de fila de entrada.

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

Este exemplo de código c# grava várias mensagens usando `ICollector<T>` (use `IAsyncCollector<T>` em uma função de assíncrono):

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

## <a id="storageblobtrigger"></a>Azure gatilho de blob Storage

#### <a name="functionjson-for-storage-blob-trigger"></a>Function.JSON para disparador de blob storage

O arquivo de *function.json* Especifica as seguintes propriedades.

- `name`: O nome da variável usado no código de função para o blob. 
- `path`: Um caminho que especifica o contêiner para monitorar e, opcionalmente, um padrão de nome de blob.
- `connection`: O nome da configuração do aplicativo que contém uma cadeia de conexão de armazenamento. Se você deixar `connection` vazio, o disparador funcionarão com a cadeia de conexão de armazenamento padrão para o aplicativo de função, que é especificada pela configuração do aplicativo AzureWebJobsStorage.
- `type`: Deve ser definida como *blobTrigger*.
- `direction`: Deve ser definida como *no*.

Exemplo *function.json* para um disparador de blob de armazenamento aguarda bolhas que são adicionadas ao contêiner de itens de trabalho exemplos:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

#### <a name="blob-trigger-supported-types"></a>Gatilho de blob tipos com suporte

O blob pode ser desserializado a qualquer um dos seguintes tipos em funções de nó ou c#:

* Objeto (a partir do JSON)
* Cadeia de caracteres

Em c# funções você também pode vincular a qualquer um dos seguintes tipos:

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Outros tipos desserializados pelo [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) 

#### <a name="blob-trigger-c-code-example"></a>Blob disparador c# exemplo de código

Este exemplo de código c# registra o conteúdo de cada blob que é adicionado ao contêiner de monitorar.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### <a name="blob-trigger-name-patterns"></a>Padrões de nome de disparador blob

Você pode especificar um padrão de nome de blob no `path` propriedade. Por exemplo:

```json
"path": "input/original-{name}",
```

Este caminho ache um blob denominado *Blob1.txt original* no contêiner de *entrada* e o valor da `name` variável no código de função seria `Blob1`.

Outro exemplo:

```json
"path": "input/{blobname}.{blobextension}",
```

Este caminho também ache um blob denominado *Blob1.txt original*e o valor da `blobname` e `blobextension` variáveis no código de função seria *Blob1 original* e *txt*.

Você pode restringir os tipos de bolhas que disparam a função especificando um padrão com um valor fixo para a extensão de arquivo. Se você definir o `path` *para/exemplos / {nome}. PNG*, *. PNG* somente os blobs no contêiner de *amostras* acionará a função.

Se você precisar especificar um padrão de nome para nomes de blob que tenham chaves no nome, duplo as chaves. Por exemplo, se você quiser localizar blobs no contêiner de *imagens* que têm nomes como esta:

        {20140101}-soundfile.mp3

Use este formulário a `path` propriedade:

        images/{{20140101}}-{name}

No exemplo, o `name` valor variável seria *soundfile.mp3*. 

#### <a name="blob-receipts"></a>Confirmações de blob

O tempo de execução de funções do Azure garante que nenhuma função de disparador blob obtém chamada mais de uma vez para o mesmo blob novo ou atualizado. Ele faz isso mantendo *blob confirmações* para determinar se uma versão de blob determinado foi processada.

Confirmações de blob são armazenadas em um contêiner chamado *azure-webjobs-hosts* na conta de armazenamento do Azure especificada pela cadeia de conexão AzureWebJobsStorage. Uma confirmação de blob tem as seguintes informações:

* A função que foi chamada o blob ("*{nome do aplicativo de função}*. Funções. *{nome de função}*", por exemplo:"functionsf74b96f7. Functions.CopyBlob")
* O nome do contêiner
* O tipo de blob ("BlockBlob" ou "PageBlob")
* O nome de blob
* A ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

Se você quiser forçar reprocessamento de um blob, você pode excluir manualmente a confirmação de blob para blob do contêiner *azure-webjobs-hosts* .

#### <a name="handling-poison-blobs"></a>Manipulando blobs suspeitas

Quando uma função de disparador blob falhar, o SDK chama-lo novamente, caso a falha foi causada por um erro temporário. Se a falha for causada pelo conteúdo do blob, a função falhará sempre que ele tenta processar o blob. Por padrão, o SDK chama uma função até 5 vezes para um determinado blob. Se o quinto experimentar falhar, o SDK adiciona uma mensagem para uma fila denominada *webjobs-blobtrigger-inviabilização*.

A mensagem de fila para blobs suspeitas é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato *{nome do aplicativo de função}*. Funções. *{nome de função}*)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

#### <a name="blob-polling-for-large-containers"></a>Blob sondagem para contêineres grandes

Se o contêiner de blob que o disparador está monitorando contiver mais de 10.000 blobs, as verificações de tempo de execução de funções os arquivos de log para assistir para blobs novas ou alteradas. Esse processo não é em tempo real; uma função não pode ser disparada até vários minutos ou mais após o blob é criado. Além disso, base de [armazenamento logs são criados em um "esforços"](https://msdn.microsoft.com/library/azure/hh343262.aspx) ; Não há nenhuma garantia de que todos os eventos serão capturados. Sob algumas condições, os logs podem ser perdidos. Se as limitações de velocidade e confiabilidade de disparadores blob para contêineres grandes não são aceitáveis para seu aplicativo, o método recomendado é criar uma mensagem de fila quando você cria o blob e use um gatilho de fila em vez de um disparador blob para processar o blob.
 
## <a id="storageblobbindings"></a>Azure blob de armazenamento de entrada e saída ligações

#### <a name="functionjson-for-a-storage-blob-input-or-output-binding"></a>Function.JSON para um blob de armazenamento de entrada ou ligação de saída

O arquivo de *function.json* Especifica as seguintes propriedades.

- `name`: O nome da variável usado no código de função para o blob. 
- `path`: Um caminho que especifica o contêiner para ler o blob de ou gravar o blob para e, opcionalmente, um padrão de nome de blob.
- `connection`: O nome da configuração do aplicativo que contém uma cadeia de conexão de armazenamento. Se você deixar `connection` vazio, a vinculação funcionarão com a cadeia de conexão de armazenamento padrão para o aplicativo de função, que é especificada pela configuração do aplicativo AzureWebJobsStorage.
- `type`: Deve ser definida como *blob*.
- `direction`: Definido como *ou *reduzir** . 

Exemplo *function.json* para um armazenamento de blob entrada ou ligação, usando um gatilho de fila para copiar um blob de saída:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="blob-input-and-output-supported-types"></a>Blob de entrada e saídos tipos compatíveis

O `blob` vinculação pode serializar ou desserializar os seguintes tipos em funções Node ou c#:

* Objeto (`out T` em c# blob de saída: cria um blob como objeto nulo se o valor do parâmetro for nulo quando terminar a função)
* Cadeia de caracteres (`out string` em c# blob de saída: cria um blob somente se o parâmetro de cadeia de caracteres é nulos quando a função retorna)

Em c# funções, você também pode vincular aos seguintes tipos:

* `TextReader`(somente entrada)
* `TextWriter`(somente saída)
* `Stream`
* `CloudBlobStream`(somente saída)
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

#### <a name="blob-output-c-code-example"></a>Blob c# código exemplo de saída

Este exemplo de código c# copia um blob cujo nome é recebido em uma mensagem de fila.

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

## <a id="storagetablesbindings"></a>Tabelas do Azure armazenamento de entrada e saída ligações

#### <a name="functionjson-for-storage-tables"></a>Function.JSON para tabelas de armazenamento

O *function.json* Especifica as seguintes propriedades.

- `name`: O nome da variável usado no código de função para a vinculação de tabela. 
- `tableName`: O nome da tabela.
- `partitionKey`e `rowKey` : usados juntos para ler uma única entidade em uma função c# ou nó ou para escrever uma única entidade em uma função de nó.
- `take`: O número máximo de linhas a serem lidos para tabela de entrada em uma função de nó.
- `filter`: Expressão de filtro OData para tabela de entrada em uma função de nó.
- `connection`: O nome da configuração do aplicativo que contém uma cadeia de conexão de armazenamento. Se você deixar `connection` vazio, a vinculação funcionarão com a cadeia de conexão de armazenamento padrão para o aplicativo de função, que é especificada pela configuração do aplicativo AzureWebJobsStorage.
- `type`: Deve ser definido a *tabela*.
- `direction`: Definido como *ou *reduzir** . 

O exemplo a seguir *function.json* usa um disparador fila para ler uma única linha da tabela. O JSON fornece um valor de chave de partição embutidos e especifica que a chave de linha são originárias da mensagem fila.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="storage-tables-input-and-output-supported-types"></a>Tabelas de armazenamento de entrada e saída tipos com suporte

O `table` vinculação pode serializar ou desserializar objetos em funções Node ou c#. Os objetos terão propriedades RowKey e PartitionKey. 

Em c# funções, você também pode vincular aos seguintes tipos:

* `T`onde `T` implementa`ITableEntity`
* `IQueryable<T>`(somente entrada)
* `ICollector<T>`(somente saída)
* `IAsyncCollector<T>`(somente saída)

#### <a name="storage-tables-binding-scenarios"></a>Cenários de vinculação de tabelas de armazenamento

A vinculação de tabela oferece suporte para os seguintes cenários:

* Ler uma única linha em uma função c# ou nó.

    Definir `partitionKey` e `rowKey`. O `filter` e `take` propriedades não são usadas neste cenário.

* Leia várias linhas em uma função c#.

    O tempo de execução de funções fornece um `IQueryable<T>` objeto vinculado à tabela. Tipo de `T` deve ser derivado de `TableEntity` ou implementar `ITableEntity`. O `partitionKey`, `rowKey`, `filter`, e `take` propriedades não são usadas neste cenário; Você pode usar o `IQueryable` objeto fazer qualquer filtragem obrigatório. 

* Leia várias linhas em uma função de nó.

    Definir o `filter` e `take` propriedades. Não definir `partitionKey` ou `rowKey`.

* Escreva uma ou mais linhas em uma função c#.

    O tempo de execução de funções fornece um `ICollector<T>` ou `IAsyncCollector<T>` vinculado à tabela, onde `T` Especifica o esquema das entidades que você deseja adicionar. Normalmente, digite `T` deriva `TableEntity` ou implementa `ITableEntity`, mas não necessariamente. O `partitionKey`, `rowKey`, `filter`, e `take` propriedades não são usadas neste cenário.

#### <a name="storage-tables-example-read-a-single-table-entity-in-c-or-node"></a>Exemplo de tabelas de armazenamento: ler uma entidade única tabela em c# ou nó

O seguinte exemplo de código em c# funciona com arquivo *function.json* anterior mostrado anteriormente para ler uma única tabela entidade. A mensagem de fila tem o valor de chave de linha e a entidade de tabela é lidos em um tipo que é definido no arquivo *run.csx* . O tipo inclui `PartitionKey` e `RowKey` propriedades e não deriva `TableEntity`. 

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

O seguinte exemplo de código em F # também funciona com *function.json* arquivo anterior para ler uma única tabela entidade.

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

O seguinte exemplo de código de nó também funciona com *function.json* arquivo anterior para ler uma única tabela entidade.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### <a name="storage-tables-example-read-multiple-table-entities-in-c"></a>Exemplo de tabelas de armazenamento: ler várias entidades de tabela em C# 

Os seguintes *function.json* e c# código entidades de leituras de exemplo para uma chave de partição especificado na mensagem de fila.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O código c# adiciona uma referência para o SDK de armazenamento do Azure para que o tipo de entidade pode derivado de `TableEntity`.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### <a name="storage-tables-example-create-table-entities-in-c"></a>Exemplo de tabelas de armazenamento: criar entidades de tabela em C# 

O exemplo a seguir *function.json* e *run.csx* mostra como escrever entidades de tabela em c#.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### <a name="storage-tables-example-create-table-entities-in-f"></a>Exemplo de tabelas de armazenamento: criar entidades de tabela em F#

O exemplo a seguir *function.json* e *run.fsx* mostra como escrever entidades de tabela em F #.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

#### <a name="storage-tables-example-create-a-table-entity-in-node"></a>Exemplo de tabelas de armazenamento: criar uma entidade de tabela no nó

O exemplo a seguir *function.json* e *run.csx* mostra como escrever uma entidade de tabela no nó.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
