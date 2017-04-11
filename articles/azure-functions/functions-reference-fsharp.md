<properties
    pageTitle="Referência de desenvolvedor do Azure funções F # | Microsoft Azure"
    description="Compreenda como desenvolver funções do Azure usando F #."
    services="functions"
    documentationCenter="fsharp"
    authors="sylvanc"
    manager="jbronsk"
    editor=""
    tags=""
    keywords="Azure funções, funções, evento processamento, webhooks, computação dinâmica, sem servidor arquitetura, F#"/>

<tags
    ms.service="functions"
    ms.devlang="fsharp"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="09/09/2016"
    ms.author="syclebsc"/>

# <a name="azure-functions-f-developer-reference"></a>Referência do desenvolvedor do F # de funções Azure

> [AZURE.SELECTOR]
- [C# script](../articles/azure-functions/functions-reference-csharp.md)
- [F # script](../articles/azure-functions/functions-reference-fsharp.md)
- [Node](../articles/azure-functions/functions-reference-node.md)

F # para funções do Azure é uma solução para executar facilmente pequenas partes de código ou "funções," na nuvem. Dados fluirá para sua função F # via argumentos da função. Nomes de argumentos são especificados no `function.json`, e há nomes predefinidos para acessar itens como os tokens de agente de log e o cancelamento de função.

Este artigo pressupõe que você já leu a [referência do desenvolvedor de funções do Azure](functions-reference.md).

## <a name="how-fsx-works"></a>Como funciona o .fsx

Um `.fsx` arquivo é um script F #. Ele pode ser considerado como um projeto F # contida em um único arquivo. O arquivo contém os dois o código para seu programa (neste caso, sua função Azure) e diretivas de gerenciamento de dependências.

Quando você usa um `.fsx` para uma função do Azure, normalmente necessários para conjuntos incluídos automaticamente para você, permitindo que você se concentre no código de função em vez de "clichê".

## <a name="binding-to-arguments"></a>Vinculando a argumentos

Cada ligação oferece suporte a alguns conjunto de argumentos, conforme detalhado na [disparadores de funções do Azure e referência do desenvolvedor de ligações](functions-triggers-bindings.md). Por exemplo, uma das ligações de argumento que dá suporte a um disparador blob é um POCO, que pode ser expressa usando um registro F #. Por exemplo:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Sua função do Azure F # levará um ou mais argumentos. Quando falarmos sobre argumentos de funções do Azure, podemos consulte argumentos de _entrada_ e _saída_ argumentos. Um argumento de entrada é exatamente o que parece: entrada para sua função do Azure F #. Um argumento de _saída_ for dados mutável ou um `byref<>` argumento que serve como uma forma de passar dados de volta _fora_ da sua função.

No exemplo acima, `blob` é um argumento de entrada, e `output` é um argumento de saída. Observe que usamos `byref<>` para `output` (não é necessário adicionar o `[<Out>]` anotação). Usando um `byref<>` tipo permite que sua função alterar qual registro ou objeto o argumento se refere.

Quando um registro de F # é usado como um tipo de entrada, a definição de registro deve ser marcada com `[<CLIMutable>]` para permitir que a estrutura de funções do Azure definir os campos adequadamente antes de passar o registro para sua função. Nos bastidores, `[<CLIMutable>]` gera setters para as propriedades de registro. Por exemplo:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: TraceWriter) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Uma classe F # também pode ser usada para ambos e argumentos. Para uma classe, propriedades geralmente precisará getters e setters. Por exemplo:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Registro em log

Para fazer logon saída seu [streaming logs](../app-service-web/web-sites-streaming-logs-and-console.md) em F #, sua função deve tomar um argumento do tipo `TraceWriter`. Consistência, é recomendável para este argumento é chamado `log`. Por exemplo:

```fsharp
let Run(blob: string, output: byref<string>, log: TraceWriter) =
    log.Verbose(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Assíncrono

O `async` fluxo de trabalho pode ser usado, mas o resultado precisa retornar um `Task`. Isso pode ser feito com `Async.StartAsTask`, por exemplo:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token de cancelamento

Se sua função precisa lidar com desligamento normalmente, você pode dar a ele uma [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) argumento. Isso pode ser combinado com `async`, por exemplo:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importar namespaces

Namespaces pode ser aberta no como de costume:

```fsharp
open System.Net
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Os seguintes namespaces são automaticamente abertos:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Referenciando montagens externas

Da mesma forma, assembly framework referências adicionadas com o `#r "AssemblyName"` diretiva.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
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
* `Microsoft.AspNEt.WebHooks.Common`.

Se você precisar fazer referência a um assembly particular, você poderá carregar o arquivo de assembly em um `bin` pasta relativo sua função e referência-lo usando o arquivo (por exemplo, nome  `#r "MyAssembly.dll"`). Para obter informações sobre como carregar arquivos em sua pasta de função, consulte a seção a seguir sobre gerenciamento de pacote.

## <a name="editor-prelude"></a>Editor Prelude

Um editor compatível com os serviços de compilador F # não será ciente dos namespaces e conjuntos que funções do Azure inclui automaticamente. Assim, ele pode ser útil para incluir uma prelude que ajuda o editor localizar os conjuntos que você está usando e de abrir explicitamente namespaces. Por exemplo:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open Sytem
open Microsoft.Azure.WebJobs.Host

let Run(blob: string, output: byref<string>, log: TraceWriter) =
    ...
```

Quando seu código é executado Azure funções, ele processa a fonte com `COMPILED` definido, para que o prelude editor será ignorada.

## <a name="package-management"></a>Gerenciamento de pacote

Para usar os pacotes do NuGet em uma função F #, adicione uma `project.json` arquivo para a pasta da função no sistema de arquivos do aplicativo de função. Aqui está um exemplo `project.json` arquivo que adiciona uma referência de pacote do NuGet para `Microsoft.ProjectOxford.Face` versão 1.1.0:

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

Somente a 4.6 do .NET Framework é suportado, então certifique-se de que seu `project.json` arquivo especifica `net46` conforme mostrado aqui.

Quando você carregar um `project.json` arquivo, o tempo de execução obtém os pacotes e adiciona automaticamente referências para os conjuntos de pacote. Você não precisa adicionar `#r "AssemblyName"` diretivas. Basta adicionar necessário `open` instruções para sua `.fsx` arquivo.

Talvez você queira colocar automaticamente montagens referências em sua prelude editor, para melhorar a interação do editor com serviços de compilação do F #.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Como adicionar um `project.json` arquivo para sua função do Azure

1. Comece verificando se seu aplicativo de função está em execução, que pode ser feito abrindo sua função no portal do Azure. Isso também permite acesso aos logs de streaming onde saída de instalação do pacote será exibida.

2. Para carregar um `project.json` de arquivos, use um dos métodos descritos como [atualizar arquivos de aplicativo de função](functions-reference.md#fileupdate). Se você estiver usando [Implantação contínuo para funções do Azure](functions-continuous-deployment.md), você pode adicionar uma `project.json` arquivo para sua ramificação preparação para experimentar antes de adicioná-lo à sua ramificação de implantação.

3. Após a `project.json` arquivo é adicionado, você verá a saída semelhante ao exemplo a seguir em sua função do streaming log:

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

Para obter uma variável de ambiente ou um aplicativo configurando valor, use `System.Environment.GetEnvironmentVariable`, por exemplo:

```fsharp
open System.Environment

let Run(timer: TimerInfo, log: TraceWriter) =
    log.Info("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.Info("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Reutilizar o código de .fsx

Você pode usar o código de outros `.fsx` arquivos usando um `#load` diretiva. Por exemplo:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: TraceWriter) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: TraceWriter, text: string) =
    log.Verbose(text);
```

Caminhos fornece para o `#load` são diretiva relativo a localização da sua `.fsx` arquivo.

* `#load "logger.fsx"`carrega um arquivo localizado na pasta de função.

* `#load "package\logger.fsx"`carrega um arquivo localizado na `package` pasta na pasta de função.

* `#load "..\shared\mylogger.fsx"`carrega um arquivo localizado na `shared` pasta no mesmo nível como a pasta de função, ou seja, diretamente em `wwwroot`.

O `#load` diretiva só funciona com `.fsx` arquivos (F # script) e não com `.fs` arquivos.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes recursos:

* [Guia de F #](https://docs.microsoft.com/en-us/dotnet/articles/fsharp/index)
* [Referência do desenvolvedor funções Azure](functions-reference.md)
* [Referência de desenvolvedor do Azure funções c#](functions-reference-csharp.md)
* [Referência do desenvolvedor funções NodeJS Azure](functions-reference-node.md)
* [Ligações e azure disparadores de funções](functions-triggers-bindings.md)
* [Funções Azure teste](functions-test-a-function.md)
* [Funções Azure dimensionamento](functions-scale.md)
