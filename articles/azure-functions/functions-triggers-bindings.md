<properties
    pageTitle="Azure disparadores de funções e ligações | Microsoft Azure"
    description="Compreenda como usar disparadores e ligações em funções do Azure."
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
    ms.date="10/27/2016"
    ms.author="chrande"/>

# <a name="azure-functions-triggers-and-bindings-developer-reference"></a>Azure referência do desenvolvedor disparadores e ligações funções


Este tópico fornece referência geral para disparadores e ligações. Ele inclui alguns dos recursos de ligação avançada e sintaxe compatível com todos os tipos de vinculação.  

Se você estiver procurando por informações detalhadas ao redor de um tipo específico de disparador ou ligação de codificação e configurando, convém clique em um dos disparadores ou associações listadas abaixo, em vez disso:

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)] 

Estes artigos presumem que você leu a [referência do desenvolvedor de funções do Azure](functions-reference.md)e os artigos de referência do desenvolvedor [c#](functions-reference-csharp.md), [F #](functions-reference-fsharp.md)ou [Node](functions-reference-node.md) .



## <a name="overview"></a>Visão geral

Disparadores são usadas para acionar seu código personalizado de respostas de evento. Eles permitem que você responder a eventos em plataforma Windows Azure ou local. Ligações representam os dados necessários do meta usados para conectar seu código à desejada disparadores ou entrada associada ou dados de saída.

Para obter uma ideia melhor das ligações diferentes que você pode integrar com seu aplicativo do Azure função, consulte a tabela a seguir.

[AZURE.INCLUDE [dynamic compute](../../includes/functions-bindings.md)]  

Para entender melhor dispara e ligações em geral, suponha que você queira executar algumas código ao processo descartado de um novo item em uma fila do armazenamento do Azure. Funções Azure fornece um gatilho de fila do Azure para dar suporte a isso. Necessárias, as seguintes informações para monitorar a fila:
 
- A conta de armazenamento onde existe fila.
- O nome da fila.
- Um nome de variável que seu código usaria para se referir ao novo item que foi descartado na fila.  
 
Um disparador fila vinculação contém essas informações para uma função Azure. O arquivo de *function.json* para cada função contém todas as ligações relacionadas.  Aqui está um exemplo *function.json* contendo uma fila disparar vinculação. 

    {
      "bindings": [
        {
          "name": "myNewUserQueueItem",
          "type": "queueTrigger",
          "direction": "in",
          "queueName": "queue-newusers",
          "connection": "MY_STORAGE_ACCT_APP_SETTING"
        }
      ],
      "disabled": false
    }

Seu código pode enviar diferentes tipos de saída dependendo de como o novo item de fila é processado. Por exemplo, talvez você queira escrever um novo registro a uma tabela de armazenamento do Azure.  Para fazer isso, você pode configurar uma associação de saída a uma tabela de armazenamento do Azure. Aqui está um exemplo *function.json* que inclui uma ligação de saída de tabela de armazenamento que pode ser usada com um gatilho de fila. 


    {
      "bindings": [
        {
          "name": "myNewUserQueueItem",
          "type": "queueTrigger",
          "direction": "in",
          "queueName": "queue-newusers",
          "connection": "MY_STORAGE_ACCT_APP_SETTING"
        },
        {
          "type": "table",
          "name": "myNewUserTableBinding",
          "tableName": "newUserTable",
          "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING",
          "direction": "out"
        }
      ],
      "disabled": false
    }


A seguinte função c# responde a um novo item que está sendo cancelado na fila e grava uma nova entrada de usuário em uma tabela de armazenamento do Azure.

    #r "Newtonsoft.Json"

    using System;
    using Newtonsoft.Json;

    public static async Task Run(string myNewUserQueueItem, IAsyncCollector<Person> myNewUserTableBinding, 
                                    TraceWriter log)
    {
        // In this example the queue item is a JSON string representing an order that contains the name, 
        // address and mobile number of the new customer.
        dynamic order = JsonConvert.DeserializeObject(myNewUserQueueItem);
    
        await myNewUserTableBinding.AddAsync(
            new Person() { 
                PartitionKey = "Test", 
                RowKey = Guid.NewGuid().ToString(), 
                Name = order.name,
                Address = order.address,
                MobileNumber = order.mobileNumber }
            );
    }
    
    public class Person
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Name { get; set; }
        public string Address { get; set; }
        public string MobileNumber { get; set; }
    }

Para obter mais exemplos de código e obter informações mais específicas sobre tipos de armazenamento do Azure que são suportados, consulte [funções do Azure disparadores e ligações para o armazenamento do Azure](functions-bindings-storage.md).


Para usar os recursos de vinculação mais avançados no portal do Azure, clique na opção de **editor avançado** na guia **integrar** da sua função. O editor avançado permite que você edite o *function.json* diretamente no portal.

## <a name="dynamic-parameter-binding"></a>Vinculação de parâmetro dinâmico 

Em vez de estática definição de configuração para as propriedades de vinculação de saída, você pode definir as configurações para ser vinculado dinamicamente aos dados que faz parte de associação de entrada do seu disparador. Considere um cenário onde novos pedidos são processados usando uma fila do armazenamento do Azure. Cada novo item de fila é uma cadeia de JSON que contém pelo menos as seguintes propriedades:

    {
      name : "Customer Name",
      address : "Customer's Address".
      mobileNumber : "Customer's mobile number."
    }

Talvez você queira enviar uma mensagem de texto SMS usando sua conta de Twilio como uma atualização que recebeu a ordem de cliente.  Você pode configurar o `body` e `to` ligação deve ser vinculado dinamicamente de saída de campo do seu Twilio o `name` e `mobileNumber` que estavam parte da entrada da seguinte maneira.

    {
      "name": "myNewOrderItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newOrders",
      "connection": "orders_STORAGE"
    },
    {
      "type": "twilioSms",
      "name": "message",
      "accountSid": "TwilioAccountSid",
      "authToken": "TwilioAuthToken",
      "to": "{mobileNumber}",
      "from": "+XXXYYYZZZZ",
      "body": "Thank you {name}, your order was received",
      "direction": "out"
    },
 
Agora seu código de função só precisa inicializar o parâmetro de saída da seguinte maneira. Durante a execução as propriedades de saída serão vinculadas para os dados de entrada desejados.

C#

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message variable.
    message = new SMSMessage();

    // When using dynamic parameter binding no need to set this in code.
    // message.body = msg;
    // message.to = myNewOrderItem.mobileNumber

Node

    context.bindings.message = {
        // When using dynamic parameter binding no need to set this in code.
        //body : msg,
        //to : myNewOrderItem.mobileNumber
    };




## <a name="random-guids"></a>GUIDs aleatórios

Funções Azure fornece uma sintaxe para gerar GUIDs aleatórios com suas ligações. A seguinte sintaxe de vinculação gravará a saída para um novo BLOB com um nome exclusivo em um contêiner de armazenamento do Azure: 

    {
      "type": "blob",
      "name": "blobOutput",
      "direction": "out",
      "path": "my-output-container/{rand-guid}"
    }



## <a name="returning-a-single-output"></a>Retornando um único resultado

Em casos onde seu código de função retorna um único resultado, você pode usar uma associação de saída denominada `$return` para reter uma assinatura de função mais natural em seu código. Isso só pode ser usado com idiomas que oferecem suporte a um valor de retorno (c#, Node, F #). A vinculação seria semelhante à seguinte blob saída ligação que é usada com um gatilho de fila.

    {
      "bindings": [
        {
          "type": "queueTrigger",
          "name": "input",
          "direction": "in",
          "queueName": "test-input-node"
        },
        {
          "type": "blob",
          "name": "$return",
          "direction": "out",
          "path": "test-output-node/{id}"
        }
      ]
    }


O código c# a seguir retorna a saída mais naturalmente sem usar um `out` parâmetro na assinatura função.


    public static string Run(WorkItem input, TraceWriter log)
    {
        string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
        log.Info($"C# script processed queue message. Item={json}");
        return json;
    }

    // Async example
    public static Task<string> Run(WorkItem input, TraceWriter log)
    {
        string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
        log.Info($"C# script processed queue message. Item={json}");
        return json;
    }


Essa mesma abordagem é demonstrou abaixo com Node.

    module.exports = function (context, input) {
        var json = JSON.stringify(input);
        context.log('Node.js script processed queue message', json);
        context.done(null, json);
    }

F # exemplo abaixo.

    let Run(input: WorkItem, log: TraceWriter) =
        let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
        log.Info(sprintf "F# script processed queue message '%s'" json)
        json

Isso também pode ser usado com vários parâmetros de saída ao designar um único resultado com `$return`.


## <a name="route-support"></a>Suporte de roteiro

Por padrão quando você cria uma função para um gatilho HTTP, ou WebHook, a função é endereçamento com uma rota do formulário:

    http://<yourapp>.azurewebsites.net/api/<funcname> 

Você pode personalizar essa rota usando opcional `route` vinculação de entrada do propriedade no acionador HTTP. Como exemplo, o seguinte arquivo de *function.json* define uma `route` propriedade para um gatilho de HTTP:

    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [ "get" ],
          "route": "products/{category:alpha}/{id:int?}"
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }

Usando essa configuração, a função agora é endereçamento com a seguinte rota em vez do roteiro original.

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

Isso permite que o código de função dar suporte a dois parâmetros no endereço, `category` e `id`. Você pode usar qualquer [Web API rota restrição](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com seus parâmetros. O seguinte código de função em c# faz uso de ambos os parâmetros.

    public static Task<HttpResponseMessage> Run(HttpRequestMessage request, string category, int? id, 
                                                    TraceWriter log)
    {
        if (id == null)
           return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
        else
           return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
    }

Aqui está o código de função node para usar os mesmos parâmetros de rota.

    module.exports = function (context, req) {

        var category = context.bindingData.category;
        var id = context.bindingData.id;
    
        if (!id) {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "All " + category + " items were requested."
            };
        }
        else {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: category + " item with id = " + id + " was requested."
            };
        }
    
        context.done();
    } 

Por padrão, todas as rotas de função são prefixadas com a *api*. Você também pode personalizar ou remover o prefixo usando o `http.routePrefix` propriedade no seu arquivo de *host.json* . O exemplo a seguir remove o prefixo da rota *api* usando uma cadeia de caracteres vazia para o prefixo no arquivo *host.json* .

    {
      "http": {
        "routePrefix": ""
      }
    }

Para obter informações detalhadas sobre como atualizar o arquivo *host.json* para sua função, consulte [como atualizar aplicativo de função arquivos](functions-reference.md#fileupdate). 

Adicionando essa configuração, a função agora é endereçamento com a rota a seguir:

    http://<yourapp>.azurewebsites.net/products/electronics/357

Para obter informações sobre outras propriedades que você pode configurar no seu arquivo de *host.json* , consulte [referência de host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).





## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes recursos:

* [Testando uma função](functions-test-a-function.md)
* [Escala de uma função](functions-scale.md)
