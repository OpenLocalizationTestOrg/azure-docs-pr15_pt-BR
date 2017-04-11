<properties 
    pageTitle="Filtragem e pré-processamento no SDK de obtenção de informações do aplicativo | Microsoft Azure" 
    description="Escreva processadores de telemetria e inicializadores de telemetria para o SDK para filtrar ou adicionar propriedades aos dados antes da telemetria é enviada para o portal de obtenção de informações do aplicativo." 
    services="application-insights"
    documentationCenter="" 
    authors="beckylino" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="borooji"/>

# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtragem e pré-processamento telemetria no SDK de obtenção de informações do aplicativo

*Obtenção de informações de aplicativo está no modo de visualização.*

Você pode escrever e configurar plug-ins para o SDK do aplicativo ideias personalizar como telemetria é capturada e processada antes que ela é enviada para o serviço de obtenção de informações do aplicativo. 

Atualmente, esses recursos estão disponíveis para o SDK do ASP.NET.

* [Amostragem](app-insights-sampling.md) reduz o volume de telemetria sem afetar as estatísticas. Ele mantém juntos relacionados a pontos de dados para que você possa navegar entre elas quando diagnosticar um problema. No portal do, a contagem total é multiplicadas para compensar a amostragem.
* [Filtragem com processadores de telemetria](#filtering) permite que você selecione ou modifique telemetria no SDK antes que ela é enviada para o servidor. Por exemplo, você pode reduzir o volume de telemetria excluindo solicitações de robôs. Mas filtragem é uma abordagem mais básica para reduzir o tráfego de amostragem. Ele permite ter mais controle sobre o que é transmitido, mas você precisa estar ciente de que ele afeta as estatísticas - por exemplo, se você filtrar todas as solicitações bem-sucedido.
* [Os inicializadores de telemetria adicionar propriedades](#add-properties) para qualquer telemetria enviada a partir do seu aplicativo, incluindo telemetria de módulos padrão. Por exemplo, você pode adicionar valores calculados; ou números de versão pelo qual filtrar os dados no portal.
* [A API do SDK](app-insights-api-custom-events-metrics.md) é usado para enviar métricas e eventos personalizados.


Antes de começar:

* Instale o [aplicativo ideias SDK para ASP.NET v2](app-insights-asp-net.md) em seu aplicativo. 


<a name="filtering"></a>
## <a name="filtering-itelemetryprocessor"></a>Filtragem: ITelemetryProcessor

Essa técnica oferece controle mais direto sobre o que é incluído ou excluído do fluxo de telemetria. Você pode usá-lo em conjunto com amostragem, ou separadamente.

Para filtrar telemetria, você escreve um processador de telemetria e registre-se com o SDK. Todos os telemetria percorre seu processador e você pode optar por solte-a partir do fluxo ou adicionar propriedades. Isso inclui telemetria dos módulos padrão como o coletor de solicitação HTTP e a coleta de dependência, bem como telemetria que você tenha escrito por conta própria. Por exemplo, você pode filtrar telemetria sobre solicitações de robôs ou chamadas de dependência bem-sucedido. 

> [AZURE.WARNING] Filtragem a telemetria enviada a partir do SDK usando processadores pode inclinar as estatísticas que você vê no portal e dificultar acompanhar itens relacionados.
> 
> Em vez disso, considere o uso de [amostragem](app-insights-sampling.md).

### <a name="create-a-telemetry-processor"></a>Criar um processador de telemetria

1. Verifique se o SDK de ideias de aplicativo em seu projeto está versão 2.0.0 ou posterior. Clique com botão direito seu projeto no Visual Studio Solution Explorer e escolha gerenciar pacotes do NuGet. No Gerenciador de pacote do NuGet, verifique Microsoft.ApplicationInsights.Web.

1. Para criar um filtro, implemente ITelemetryProcessor. Este é outro ponto de extensibilidade como módulo de telemetria inicializador de telemetria e canal de telemetria. 

    Observe que processadores de telemetria construir uma cadeia de processamento. Quando você criar uma instância de um processador de telemetria, você passa um link para o próximo processador na cadeia. Quando um ponto de dados de telemetria é passado para o método de processo, ela faz seu trabalho e, em seguida, chama o próximo processador de telemetria na cadeia.

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {
        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return 
            if (!OKtoSend(item)) { return; }
            // Modify the item if required 
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }
    

    ```
2. Inserir isso no ApplicationInsights.config: 

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Essa é a mesma seção onde você inicializar um filtro de amostragem).

Você pode passar valores de cadeia de caracteres do arquivo. config, fornecendo públicas propriedades nomeadas em sua classe. 

> [AZURE.WARNING] Tome cuidado para coincidir com o nome do tipo e quaisquer nomes de propriedade no arquivo. config para os nomes de classe e propriedade no código. Se o arquivo. config fizer referência a um tipo de inexistente ou propriedade, o SDK silenciosamente pode falhar ao enviar qualquer telemetria.

 
**Como alternativa,** você pode inicializar o filtro no código. Em uma classe de inicialização adequado - por exemplo AppStart em Global.asax.cs - inserir seu processador na cadeia:

```C#

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

TelemetryClients criadas depois desse ponto usará seus processadores.

### <a name="example-filters"></a>Filtros de exemplo

#### <a name="synthetic-requests"></a>Solicitações de sintéticas

Filtre testes de robôs e da web. Embora métricas Explorer oferece a opção para filtrar sintéticos fontes, esta opção reduz o tráfego filtrando-los no SDK.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else: 
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Falha de autenticação

Filtre solicitações com uma resposta "401". 

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain: 
        return;
    }
    // Send everything else: 
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrar chamadas de dependência fast remoto

Se você quiser apenas diagnosticar chamadas que são lentas, filtre aqueles fast. 

> [AZURE.NOTE] Isto irá inclinar as estatísticas que você vê no portal. Gráfico de dependência parecerá como se as chamadas de dependência são todas as falhas.

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;
            
    if (request != null && request.Duration.Milliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### <a name="diagnose-dependency-issues"></a>Diagnosticar problemas de dependência

[Este blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) descreve um projeto para diagnosticar problemas de dependência enviando automaticamente ping normal para dependências.


<a name="add-properties"></a>
## <a name="add-properties-itelemetryinitializer"></a>Adicionar propriedades: ITelemetryInitializer

Use inicializadores de telemetria para definir propriedades globais que são enviadas com todas as Telemetria; e para substituir selecionado comportamento dos módulos telemetria padrão. 

Por exemplo, a obtenção de informações de aplicativo para pacote da Web coleta telemetria sobre solicitações HTTP. Por padrão, ele sinaliza como falha qualquer solicitação com um código de resposta > = 400. Mas se você quiser trate 400 como um sucesso, você pode fornecer um inicializador de telemetria que define a propriedade de sucesso.

Se você fornecer um inicializador de telemetria, ele é chamado sempre que qualquer um dos métodos Track*() é chamado. Isso inclui métodos chamados pelos módulos telemetria padrão. Por convenção, esses módulos não defina qualquer propriedade que já foi definida por um inicializador. 

**Definir seu inicializador**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK 
       * behavior of treating response codes >= 400 as failed requests
       * 
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Carregar seu inicializador**

Em ApplicationInsights.config:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*Como alternativa,* você pode criar uma instância o inicializador no código, por exemplo, em Global.aspx.cs:


```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Ver mais deste exemplo.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>
### <a name="javascript-telemetry-initializers"></a>Inicializadores de telemetria do JavaScript

*JavaScript*

Inserir um inicializador de telemetria logo após o código de inicialização obtido a partir do portal: 

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Para obter um resumo das propriedades disponíveis na telemetryItem não-personalizado, consulte o [modelo de dados](app-insights-export-data-model.md#lttelemetrytypegt).

Você pode adicionar quantas inicializadores como desejar. 


## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor e ITelemetryInitializer

Qual é a diferença entre os processadores de telemetria e inicializadores de telemetria?

* Há algumas sobreposições em que você pode fazer com eles: ambos podem ser usados para adicionar propriedades a telemetria.
* Sempre executar TelemetryInitializers antes de TelemetryProcessors.
* TelemetryProcessors permitem que você substituir ou descartar um item de telemetria completamente.
* TelemetryProcessors não processar telemetria de contador de desempenho.



## <a name="persistence-channel"></a>Canal de persistência 

Se seu aplicativo é executado em que a conexão de internet não está sempre disponível ou lenta, considere usar o canal de persistência em vez de canal de memória padrão. 

O canal de na memória padrão perde qualquer telemetria que não foi enviada pelo tempo fecha o aplicativo. Embora você possa usar `Flush()` para tentar enviar dados restantes no buffer, ele ainda perde dados se não há nenhuma conexão de internet, ou se o aplicativo for desligado antes de transmissão é concluída.

Por outro lado, o canal de persistência buffers telemetria em um arquivo, antes de enviá-lo para o portal. `Flush()`garante que os dados são armazenados no arquivo. Se os dados não são enviados pelo tempo fecha o aplicativo, ela permanecerá no arquivo. Quando o aplicativo for reiniciado, os dados serão enviados, em seguida, se houver uma conexão de internet. Dados acumulam no arquivo de desde que é necessário até que uma conexão esteja disponível. 

### <a name="to-use-the-persistence-channel"></a>Para usar o canal de persistência

1. Importe o pacote do NuGet [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel/1.2.3).
2. Inclua este código em seu aplicativo, em um local de inicialização adequado:
 
    ```C# 

      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.Extensibility;
      ...

      // Set up 
      TelemetryConfiguration.Active.InstrumentationKey = "YOUR INSTRUMENTATION KEY";
 
      TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();
    
    ``` 
3. Use `telemetryClient.Flush()` antes fecha o aplicativo, para garantir que dados são enviados para o portal ou salvas no arquivo.

    Observe que Flush é sincronizada para o canal de persistência, mas assíncrono para outros canais.

 
O canal de persistência é otimizado para cenários de dispositivos, onde o número de eventos produzidos pelo aplicativo é relativamente pequeno e a conexão com frequência é confiável. Esse canal será gravar eventos no disco para o armazenamento confiável primeiro e, em seguida, tente enviá-lo. 

#### <a name="example"></a>Exemplo

Digamos que você deseja monitorar exceções não tratadas. Você assina o `UnhandledException` evento. Em retorno de chamada, você incluir uma chamada para esvaziar para certificar-se de que a telemetria é mantida.
 
```C# 

AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException; 
 
... 
 
private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e) 
{ 
    ExceptionTelemetry excTelemetry = new ExceptionTelemetry((Exception)e.ExceptionObject); 
    excTelemetry.SeverityLevel = SeverityLevel.Critical; 
    excTelemetry.HandledAt = ExceptionHandledAt.Unhandled; 
 
    telemetryClient.TrackException(excTelemetry); 
 
    telemetryClient.Flush(); 
} 

``` 

Quando o aplicativo desliga, você verá um arquivo em `%LocalAppData%\Microsoft\ApplicationInsights\`, que contém os eventos compactados. 
 
Próxima vez que iniciar este aplicativo, o canal pegar esse arquivo e entregá-lo telemetria a obtenção de informações de aplicativo se puder.

#### <a name="test-example"></a>Exemplo de teste

```C#

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            // Send data from the last time the app ran
            System.Threading.Thread.Sleep(5 * 1000);

            // Set up persistence channel

            TelemetryConfiguration.Active.InstrumentationKey = "YOUR KEY";
            TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();

            // Send some data

            var telemetry = new TelemetryClient();

            for (var i = 0; i < 100; i++)
            {
                var e1 = new Microsoft.ApplicationInsights.DataContracts.EventTelemetry("persistenceTest");
                e1.Properties["i"] = "" + i;
                telemetry.TrackEvent(e1);
            }

            // Make sure it's persisted before we close
            telemetry.Flush();
        }
    }
}

```


O código do canal persistência está no [github](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/v1.2.3/src/TelemetryChannels/PersistenceChannel). 


## <a name="reference-docs"></a>Documentos de referência

* [Visão geral da API](app-insights-api-custom-events-metrics.md)

* [Referência do ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)


## <a name="sdk-code"></a>Código SDK

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [SDK de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="next"></a>Próximas etapas


* [Eventos de pesquisa e logs][diagnostic]
* [Amostragem](app-insights-sampling.md)
* [Solução de problemas][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 
