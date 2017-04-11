<properties
    pageTitle="Referência do desenvolvedor funções Azure | Microsoft Azure"
    description="Compreenda como desenvolver funções do Azure usando c#."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure funções, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"/>

<tags
    ms.service="functions"
    ms.devlang="dotnet"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-c-developer-reference"></a>Referência de desenvolvedor do Azure funções c#

> [AZURE.SELECTOR]
- [C# script](../articles/azure-functions/functions-reference-csharp.md)
- [F # script](../articles/azure-functions/functions-reference-fsharp.md)
- [Node](../articles/azure-functions/functions-reference-node.md)
 
A experiência c# para funções do Azure baseia-se no Azure WebJobs SDK. Dados fluirá para sua função c# por meio de argumentos do método. Nomes de argumentos são especificados no `function.json`, e há nomes predefinidos para acessar itens como os tokens de agente de log e o cancelamento de função.

Este artigo pressupõe que você já leu a [referência do desenvolvedor de funções do Azure](functions-reference.md).

## <a name="how-csx-works"></a>Como funciona o .csx

O `.csx` formato permite escrever menos "clichê" e se concentrar em escrever apenas uma função c#. Para funções do Azure, você apenas incluir qualquer referências de assembly e namespaces você precisa de até superior, como de costume, e em vez de disposição tudo em um namespace e classe, você pode definir apenas seu `Run` método. Se você precisa incluir qualquer classes, por exemplo para definir POCO objetos, você pode incluir uma classe dentro do mesmo arquivo.

## <a name="binding-to-arguments"></a>Vinculando a argumentos

As várias ligações são vinculadas a uma função c# via o `name` propriedade na configuração *function.json* . Cada ligação tem seus próprio tipos compatíveis que é documentadas por associação; Por exemplo, um disparador blob pode oferecer suporte a uma cadeia de caracteres, um POCO ou vários outros tipos. Você pode usar o tipo que melhor atenda às suas necessidades. 

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

## <a name="logging"></a>Registro em log

Para fazer logon saída os logs streaming em c#, você pode incluir um `TraceWriter` digitado argumento. Recomendamos que você o nome `log`. Recomendamos que você evite `Console.Write` em funções do Azure.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

## <a name="async"></a>Assíncrono

Para fazer uma função assíncrona, use o `async` palavra-chave e retornar um `Task` objeto.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName, 
        Stream blobInput,
        Stream blobOutput)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="cancellation-token"></a>Token de cancelamento

Em alguns casos, você pode ter operações que são sensíveis a sendo desligado. Embora seja sempre melhor escrever código que pode manipular está travando, em casos onde você deseja lidar com desligamento solicitações, definir um [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) digitado argumento.  A `CancellationToken` serão fornecidos se um desligamento de host é disparado. 

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
        string blobName, 
        Stream blobInput,
        Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="importing-namespaces"></a>Importar namespaces

Se você precisar importar namespaces, você pode fazer assim como habitual, com a `using` cláusula.

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Os seguintes namespaces são automaticamente importados e, portanto, são opcionais:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Referenciando montagens externas

Para conjuntos de framework, adicione referências usando o `#r "AssemblyName"` diretiva.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Os seguintes módulos são adicionados automaticamente pelas funções do Azure ambiente de hospedagem:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Além disso, os seguintes módulos são especiais minúsculas e podem ser referenciados por simplename (por exemplo, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

Se você precisar fazer referência a um assembly particular, você poderá carregar o arquivo de assembly em um `bin` pasta relativo sua função e referência-lo usando o arquivo (por exemplo, nome  `#r "MyAssembly.dll"`). Para obter informações sobre como carregar arquivos em sua pasta de função, consulte a seção a seguir sobre gerenciamento de pacote.

## <a name="package-management"></a>Gerenciamento de pacote

Para usar os pacotes do NuGet em uma função c#, carregar um arquivo de *project.json* para a pasta da função no sistema de arquivos do aplicativo de função. Aqui está um exemplo de arquivo de *project.json* que adiciona uma referência a Microsoft.ProjectOxford.Face versão 1.1.0:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Somente a 4.6 do .NET Framework é suportado, então certifique-se de que seu arquivo de *project.json* especifica `net46` conforme mostrado aqui.

Quando você carrega um arquivo de *project.json* , o tempo de execução obtém os pacotes e adiciona automaticamente referências para os conjuntos de pacote. Você não precisa adicionar `#r "AssemblyName"` diretivas. Basta adicionar necessário `using` instruções para o seu arquivo de *run.csx* para usar os tipos definidos nos pacotes NuGet.


### <a name="how-to-upload-a-projectjson-file"></a>Como carregar um arquivo de project.json

1. Comece verificando se seu aplicativo de função está em execução, que pode ser feito abrindo sua função no portal do Azure. 

    Isso também permite acesso aos logs de streaming onde saída de instalação do pacote será exibida. 

2. Para carregar um arquivo de project.json, use um dos métodos descritos na seção **como atualizar arquivos de aplicativo de função** do [tópico de referência do desenvolvedor de funções do Azure](functions-reference.md#fileupdate). 

3. Após o arquivo *project.json* é carregado, você verá saída parecida com o exemplo a seguir em sua função do streaming log:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261 
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189 
2016-04-04T19:02:57.189 
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Variáveis de ambiente

Para obter uma variável de ambiente ou um aplicativo configurando valor, use `System.Environment.GetEnvironmentVariable`, conforme mostrado no exemplo de código a seguir:

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " + 
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

## <a name="reusing-csx-code"></a>Reutilizar o código de .csx

Você pode usar as classes e métodos definidos em outros arquivos de *.csx* no seu arquivo de *run.csx* . Para fazer isso, use `#load` diretivas no seu arquivo de *run.csx* , conforme mostrado no exemplo a seguir.

Exemplo *run.csx*:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}"); 
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Exemplo *mylogger.csx*:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext); 
}
```

Você pode usar um caminho relativo com o `#load` diretiva:

* `#load "mylogger.csx"`carrega um arquivo localizado na pasta de função.

* `#load "loadedfiles\mylogger.csx"`carrega um arquivo localizado em uma pasta na pasta de função.

* `#load "..\shared\mylogger.csx"`carrega um arquivo localizado em uma pasta no mesmo nível como a pasta de função, ou seja, diretamente em *wwwroot*.
 
O `#load` diretiva funciona somente com os arquivos de *.csx* (c# script), e não com arquivos *. cs* . 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes recursos:

* [Referência do desenvolvedor funções Azure](functions-reference.md)
* [Referência de desenvolvedor do Azure funções F #](functions-reference-fsharp.md)
* [Referência do desenvolvedor funções NodeJS Azure](functions-reference-node.md)
* [Ligações e azure disparadores de funções](functions-triggers-bindings.md)

