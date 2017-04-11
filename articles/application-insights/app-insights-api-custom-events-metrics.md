<properties 
    pageTitle="API de obtenção de informações de aplicativo para eventos personalizados e métricas | Microsoft Azure" 
    description="Inserir algumas linhas de código em seu aplicativo de dispositivo ou área de trabalho, página da web ou serviço, para controlar o uso e diagnosticar problemas." 
    services="application-insights"
    documentationCenter="" 
    authors="alancameronwills" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/19/2016" 
    ms.author="awills"/>

# <a name="application-insights-api-for-custom-events-and-metrics"></a>API de obtenção de informações de aplicativo para métricas e eventos personalizados 

*Obtenção de informações de aplicativo está no modo de visualização.*

Inserir algumas linhas de código em seu aplicativo para descobrir o que os usuários estão fazendo com ele ou para ajudar a diagnosticar problemas. Você pode enviar telemetria do aplicativos de desktop e dispositivo, clientes da web e servidores web. A telemetria de núcleo de [Ideias de aplicativo do Visual Studio](app-insights-overview.md) API permite que você envie suas próprias versões de telemetria padrão e métricas e eventos personalizados. Essa API é a mesma API que é usada pelos Coletores de dados de obtenção de informações de aplicativo padrão.

## <a name="api-summary"></a>Resumo de API

A API é uniforme em todas as plataformas, além de algumas pequenas variações.

Método | Usado para
---|---
[`TrackPageView`](#page-views) | Páginas, telas, lâminas ou formulários
[`TrackEvent`](#track-event) | Ações de usuário e outros eventos. Usado para controlar o comportamento do usuário ou para monitorar o desempenho.
[`TrackMetric`](#track-metric) | Medidas de desempenho como comprimentos de fila não relacionados a eventos específicos
[`TrackException`](#track-exception)|Exceções do log de diagnóstico. Rastrear onde eles ocorrem em relação a outros eventos e examinar rastreamentos de pilha.
[`TrackRequest`](#track-request)| Faça logon a frequência e a duração de solicitações de servidor para análise de desempenho.
[`TrackTrace`](#track-trace)|Mensagens de log de diagnóstico. Você também pode capturar logs de terceiros 3º.
[`TrackDependency`](#track-dependency)|Faça logon a duração e a frequência de chamadas para componentes externos quais seu aplicativo depende.

Você pode [Anexar métricas e propriedades](#properties) para a maioria dessas chamadas de telemetria. 


## <a name="prep"></a>Antes de começar

Se você ainda não tiver feito essas ainda:

* Adicione o SDK de obtenção de informações do aplicativo ao seu projeto:
 * [Projeto do ASP.NET][greenbrown]
 * [Projeto Java][java] 
 * [JavaScript em cada página da web][client]   

* No seu código de servidor web ou dispositivo, incluem:

    *C#:*`using Microsoft.ApplicationInsights;`

    *VB:*`Imports Microsoft.ApplicationInsights`

    *Java:*`import com.microsoft.applicationinsights.TelemetryClient;`

## <a name="construct-a-telemetryclient"></a>Construir um TelemetryClient

Construir uma instância de TelemetryClient (exceto em JavaScript em páginas da web):

*C#:* 

    private TelemetryClient telemetry = new TelemetryClient();

*VB:* 

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

TelemetryClient é thread-safe.

Recomendamos que você use uma instância de `TelemetryClient` para cada módulo do aplicativo. Por exemplo, você pode ter um `TelemetryClient` no seu serviço da web para relatar solicitações http de entrada e outra em uma classe de middleware para eventos de lógica de negócios do relatório. Você pode definir propriedades como `TelemetryClient.Context.User.Id` para controlar usuários e sessões, ou `TelemetryClient.Context.Device.Id` para identificar a máquina. Esta informação é anexada a todos os eventos enviados por instância.


## <a name="track-event"></a>Evento de controle

No aplicativo ideias, um *evento personalizado* é que você pode exibir ambos no [Explorador de métricas] de ponto de um dados[ metrics] como uma contagem agregada e também como ocorrências individuais na [Pesquisa diagnóstico][diagnostic]. (Ele não está relacionado ao MVC ou outro framework "eventos".) 

Inserir TrackEvent chamadas em seu código para contar com que frequência usuários escolher um determinado recurso, com que frequência eles atingir metas específicas, ou talvez fazer determinados tipos de erro. 

Por exemplo, em um aplicativo de jogo, envie um evento sempre que um usuário ganhará o jogo: 

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*
    
    telemetry.TrackEvent("WinGame");

*VB*


    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");


### <a name="view-your-events-in-the-azure-portal"></a>Exibir seus eventos no portal do Azure

Para ver uma contagem de seus eventos, abra um blade [Métrica Explorer](app-insights-metrics-explorer.md) , adicionar um novo gráfico e selecione eventos.  

![](./media/app-insights-api-custom-events-metrics/01-custom.png)

Para comparar as contagens de eventos diferentes, defina o tipo de gráfico para grade e grupo pelo nome do evento:

![](./media/app-insights-api-custom-events-metrics/07-grid.png)


Na grade, clique em nome de um evento para ver ocorrências individuais desse evento.

![Analisar os eventos](./media/app-insights-api-custom-events-metrics/03-instances.png)

Clique em qualquer ocorrência para ver mais detalhes.

Para focalizar eventos específicos em pesquisa ou métrica Explorer, defina o filtro da lâmina para os nomes de evento que você está interessado:

![Abrir filtros, expanda o nome do evento e selecione um ou mais valores](./media/app-insights-api-custom-events-metrics/06-filter.png)

## <a name="track-metric"></a>Controlar métrica

Use TrackMetric para enviar métricas que não estão associadas a determinados eventos. Por exemplo, você pode monitorar um comprimento de fila em intervalos regulares. 

Métricas são exibidas como gráficos estatísticos no Explorador de métrica, mas ao contrário de eventos, você não pode procurar ocorrências individuais na pesquisa de diagnóstico.

Valores métricas devem ser > = 0 sejam exibidos corretamente.


*JavaScript*

    appInsights.trackMetric("Queue", queue.Length);

*C#*

    telemetry.TrackMetric("Queue", queue.Length);

*VB*

    telemetry.TrackMetric("Queue", queue.Length)

*Java*

    telemetry.trackMetric("Queue", queue.Length);

Na verdade, você pode fazer isso em um thread de plano de fundo:

*C#*

    private void Run() {
     var appInsights = new TelemetryClient();
     while (true) {
      Thread.Sleep(60000);
      appInsights.TrackMetric("Queue", queue.Length);
     }
    }


Para ver os resultados, abra o Explorador de métricas e adicionar um novo gráfico. Configurá-lo para exibir sua métrica.

![Adicionar um novo gráfico ou selecione um gráfico e em Personalizar sua métrica](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

Há alguns [limites no número de métricas](#limits) que você pode usar.

## <a name="page-views"></a>Modos de exibição de página

Em um dispositivo ou página da web app, telemetria do modo de exibição de página é enviada por padrão quando cada tela ou a página for carregada. Mas você pode alterar isso para controlar os modos de exibição de página em momentos adicionais ou diferentes. Por exemplo, em um aplicativo que exibe tabulações ou blades, talvez você queira acompanhar uma "página" sempre que o usuário abre um novo blade. 

![Lente de uso na lâmina de visão geral](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

Dados de usuário e sessão são enviados como propriedades juntamente com modos de exibição de página, para que os gráficos de usuário e sessão torne ativos quando há telemetria do modo de exibição de página.

#### <a name="custom-page-views"></a>Modos de exibição de página personalizado

*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*VB*

    telemetry.TrackPageView("GameReviewPage")


Se você tiver várias guias em diferentes páginas HTML, você pode especificar o URL muito:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

#### <a name="timing-page-views"></a>Modos de exibição de página de intervalo

Por padrão, os horários relatada como "Hora de carga de modo de exibição de página" é medida da quando o navegador envia a solicitação, até que o evento de carregamento de página do navegador é chamado.

Em vez disso, você pode:

* Defina uma duração explícita na chamada [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) .
 * `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`
* Usar o modo de exibição de página Cronometragem chamadas `startTrackPage` e `stopTrackPage`.

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

... 

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

O nome que você usa como o primeiro parâmetro associa as chamadas de início e parada. O padrão é o nome da página atual. 

As durações de carregamento de página resultante exibidas no Explorador de métrica são derivadas do intervalo entre as chamadas de início e parada. Cabe a você qual intervalo de tempo realmente.

## <a name="track-request"></a>Solicitação de controle

Usado pelo servidor SDK para registrar as solicitações de HTTP. 

Você também pode chamá-lo por conta própria se quiser simular solicitações em um contexto em que você não tiver o módulo de serviço web em execução.

*C#*

    // At start of processing this request:

    // Operation Id and Name are attached to all telemetry and help you identify
    // telemetry associated with one request:
    telemetry.Context.Operation.Id = Guid.NewGuid().ToString();
    telemetry.Context.Operation.Name = requestName;
    
    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... process the request ...

    stopwatch.Stop();
    telemetry.TrackRequest(requestName, DateTime.Now,
       stopwatch.Elapsed, 
       "200", true);  // Response code, success



## <a name="operation-context"></a>Contexto de operação

Itens de telemetria podem ser associados juntos, anexando a elas uma identificação de operação comuns. A solicitação padrão controle módulo faz isso para exceções e outros eventos enviados ao processar uma solicitação HTTP. Em [pesquisa](app-insights-diagnostic-search.md) e [análise](app-insights-analytics.md), você pode usar a ID para localizar facilmente todos os eventos associados à solicitação. 

A maneira mais fácil para definir a identificação é definir um contexto de operação usando esse padrão:

    // Establish an operation context and associated telemetry item:
    using (var operation = telemetry.StartOperation<RequestTelemetry>("operationName"))
    {
        // Telemetry sent in here will use the same operation ID.
        ...
        telemetry.TrackEvent(...); // or other Track* calls
        ...
        // Set properties of containing telemetry item - for example:
        operation.Telemetry.ResponseCode = "200";
        
        // Optional: explicitly send telemetry item:
        telemetry.StopOperation(operation);

    } // When operation is disposed, telemetry item is sent.

Além de definir um contexto de operação, `StartOperation` cria um item de telemetria do tipo que você especifica e o envia quando você descartar a operação, ou se você chamar explicitamente `StopOperation`. Se você usar `RequestTelemetry` como o tipo de telemetria, então sua duração é definida como o intervalo de tempo entre o início e parada.

Contextos de operação não podem ser aninhados. Se já houver um contexto de operação, em seguida, sua ID está associada a todos os itens contidos, inclusive o item criado com StartOperation.

Em pesquisa, o contexto de operação é usado para criar a lista de itens relacionados:

![Itens relacionados](./media/app-insights-api-custom-events-metrics/21.png)


## <a name="track-exception"></a>Controlar exceção

Enviar exceções de obtenção de informações do aplicativo: contá [-las][metrics], como uma indicação da frequência de um problema; e [Examinar ocorrências individuais][diagnostic]. Os relatórios incluem os rastreamentos de pilha.

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*JavaScript*

    try
    {
       ...
    }
    catch (ex)
    {
       appInsights.trackException(ex);
    }

SDK do pegar muitas exceções automaticamente, para que você não precise sempre chamar TrackException explicitamente.

* ASP.NET: [escrever código para capturar exceções](app-insights-asp-net-exceptions.md)
* J2EE: [exceções são detectadas automaticamente](app-insights-java-get-started.md#exceptions-and-request-failures)
* JavaScript: Detectada automaticamente. Se você quiser desabilitar coleta automática, adicione uma linha para o trecho de código que você inserir em suas páginas da web:

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```


## <a name="track-trace"></a>Controlar rastreamento 

Use isto para ajudar a diagnosticar problemas enviando uma trilha de navegação estrutural' ' de obtenção de informações do aplicativo. Você pode enviar blocos de dados de diagnóstico e inspecioná-las na [pesquisa diagnóstico][diagnostic]. 

 

[Faça logon adaptadores] [ trace] usar essa API para enviar logs de terceiros para o portal.


*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);


Você pode pesquisar no conteúdo da mensagem, mas (ao contrário de valores de propriedade), você não pode filtrar nele.

O limite de tamanho de `message` é muito maior do que o limite de propriedades.
Uma vantagem de TrackTrace é que você pode colocar dados relativamente longos na mensagem. Por exemplo, você pode codificar dados de POSTAGEM lá.  


Além disso, você pode adicionar um nível de gravidade à sua mensagem. E, como outra telemetria, você poderá adicionar valores de propriedade que você pode usar para ajudar a filtrar ou pesquisar para diferentes conjuntos de rastreamentos. Por exemplo:


    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Isso permita que você, na [pesquisa][diagnostic], para filtrar facilmente todas as mensagens de um nível de gravidade determinado relacionados a um determinado banco de dados.

## <a name="track-dependency"></a>Dependência de controle

Use esta chamada para controlar os tempos de resposta e taxas de sucesso de chamadas para um externo trecho de código. Os resultados aparecem nos gráficos de dependência no portal. 

```C#

            var success = false;
            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

Lembre-se de que o servidor que SDKs incluem um [módulo de dependência](app-insights-dependencies.md) que detecta e rastreia determinada dependência chama automaticamente - por exemplo para bancos de dados e APIs REST. Você precisa instalar um agente no servidor para fazer com que o módulo funcionar. Se você quiser controlar chamadas que não são detectadas pelo controle automatizado, ou se você não quiser instalar o agente, você usaria esta chamada.

Para desativar o módulo de acompanhamento de dependência padrão, [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) de editar e excluir a referência a `DependencyCollector.DependencyTrackingTelemetryModule`.



## <a name="flushing-data"></a>Dados de liberação

Normalmente o SDK envia dados às vezes escolhidos para minimizar o impacto sobre o usuário. No entanto, em alguns casos você talvez queira liberar o buffer - por exemplo, se você estiver usando o SDK em um aplicativo que desliga.

*C#*

    telemetry.Flush();

    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(1000);

Observe que a função é assíncrona para o [canal de telemetria do servidor](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/), mas síncrono se você optar por usar o canal de memória ou [canal persistente](app-insights-api-filtering-sampling.md#persistence-channel).


## <a name="authenticated-users"></a>Usuários autenticados

Em um aplicativo web, os usuários são por padrão identificado por cookie. Um usuário pode ser contado mais de uma vez se acessar seu aplicativo de um computador diferente ou o navegador ou excluir cookies. 

Mas, se os usuários entram em seu aplicativo, você pode obter uma contagem mais precisa, definindo o id de usuário autenticado no código de navegador:

*JavaScript*

```JS
    // Called when my app has identified the user.
    function Authenticated(signInId) {
      var validatedId = signInId.replace(/[,;=| ]+/g, "_");
      appInsights.setAuthenticatedUserContext(validatedId);
      ...
    }
```

Em uma web do ASP.NET aplicativo MVC, por exemplo:

*Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name
                   .Replace("\\", "\\\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

Não é necessário usar o nome do usuário real entrar. Ele só precisa ser uma identificação exclusiva para esse usuário. Não é possível incluir espaços ou qualquer um dos caracteres `,;=|`. 

A id de usuário também é definida em um cookie de sessão e enviada para o servidor. Se o servidor SDK está instalado, a id de usuário autenticado será enviada como parte das propriedades de contexto de telemetria do cliente e servidor, para que você possa filtrar e pesquisar nela.

Se seu aplicativo agrupa os usuários em contas, você também pode passar um identificador da conta (com as mesmas restrições de caractere).


      appInsights.setAuthenticatedUserContext(validatedId, accountId);

No [Explorador de métricas](app-insights-metrics-explorer.md), você pode criar um gráfico que conta **usuários autenticados** e **contas de usuário**. 

Você também pode [Pesquisar] [ diagnostic] para pontos de dados do cliente com nomes de usuário específico e contas.

## <a name="properties"></a>Filtrar, pesquisar e seus dados com propriedades de segmento

Você pode anexar propriedades e medidas seus eventos (e também para métricas, página exibições, exceções e outros dados de telemetria).

**Propriedades** são valores de cadeia de caracteres que você pode usar para filtrar seu telemetria nos relatórios de uso. Por exemplo se seu aplicativo oferece diversos jogos, você vai querer anexar o nome do jogo para cada evento, para que você possa ver quais jogos são mais populares. 

Há um limite de cerca de 1k no tamanho da cadeia de caracteres. (Se você deseja enviar grandes quantidades de dados, use o parâmetro da mensagem de [TrackTrace](#track-trace).)

**Métricas** são valores numéricos que podem ser apresentados graficamente. Por exemplo, talvez você queira ver se há um aumento gradual de pontuações que atingir seu manipuladores. Os gráficos podem ser segmentados pelas propriedades enviadas com o evento, para que você pode obter separada ou empilhados gráficos para jogos diferentes.

Valores métricas devem ser > = 0 sejam exibidos corretamente.


Há alguns [limites no número de propriedades, valores de propriedade e métricas](#limits) que você pode usar.


*JavaScript*

    appInsights.trackEvent
      ("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

    appInsights.trackPageView
        ("page name", "http://fabrikam.com/pageurl.html",
          // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );
          

*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);


*VB*

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)


*Java*
    
    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());
    
    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());
    
    telemetry.trackEvent("WinGame", properties, metrics);


> [AZURE.NOTE] Tome cuidado para não registrar informações pessoalmente identificáveis em Propriedades.

**Se você usou métricas**, abra o Explorador de métrica e selecione a métrica do grupo personalizado:

![Abra o Explorador de métrica, selecione o gráfico e selecione a métrica](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

*Se sua métrica não aparecer, ou se o título personalizado não estiver lá, feche a lâmina de seleção e tentar mais tarde. Às vezes, pode levar uma hora para métricas sejam agregados pelo pipeline de.*

**Se você usou propriedades e métricas**, segmento a métrica pela propriedade:


![Definir o agrupamento, em seguida, selecione a propriedade em Agrupar por](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)



**No diagnóstico pesquisa**, você pode exibir as propriedades e métricas de ocorrências individuais de um evento.


![Selecione uma ocorrência e selecione '…'](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)


Use o campo de pesquisa para ver as ocorrências de eventos com um valor de propriedade particular.


![Digite um termo na pesquisa](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[Saiba mais sobre expressões de pesquisa][diagnostic].

#### <a name="alternative-way-to-set-properties-and-metrics"></a>Maneira alternativa para definir propriedades e métricas

Se for mais conveniente, você pode coletar os parâmetros de um evento em um objeto separado:

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [AZURE.WARNING] Não reutilizar a mesma instância do item de telemetria (`event` neste exemplo) para chamar Track*() várias vezes. Isso pode causar telemetria ser enviada com configuração incorreta.



## <a name="timed"></a>Eventos de tempo

Às vezes você gostaria de quanto tempo leva para realizar alguma ação de gráfico. Por exemplo, você talvez queira saber usuários quanto tempo levará para considera as opções em um jogo. Este é um exemplo útil de usos do parâmetro medida.


*C#*

    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... perform the timed action ...

    stopwatch.Stop();

    var metrics = new Dictionary <string, double>
       {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"signalSource", currentSignalSource.Name}};

    // Send the event:
    telemetry.TrackEvent("SignalProcessed", properties, metrics);



## <a name="defaults"></a>Propriedades padrão de telemetria personalizada

Se você quiser definir valores de propriedade de padrão para alguns dos eventos personalizados que você escreve, você pode defini-las em um TelemetryClient. Eles são anexados a cada item de telemetria enviado a partir do cliente. 

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");
    
*VB*

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...


    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);
    
    gameTelemetry.TrackEvent("WinGame");


    
Chamadas de telemetria individuais podem substituir os valores padrão nos seus dicionários de propriedade.

**Clientes da web para JavaScript**, [use os inicializadores de telemetria do JavaScript](#js-initializer).

**Para adicionar propriedades a todos os telemetria** incluindo os dados de módulos coleção padrão, [implementar `ITelemetryInitializer` ](app-insights-api-filtering-sampling.md#add-properties).


## <a name="sampling-filtering-and-processing-telemetry"></a>Amostragem, filtragem e processamento de telemetria 

Você pode escrever código para processar sua telemetria antes de ser enviado do SDK. O processamento inclui dados enviados a partir dos módulos de telemetria padrão como coleção de solicitação HTTP e coleção de dependência.

* [Adicionar propriedades](app-insights-api-filtering-sampling.md#add-properties) a telemetria implementando `ITelemetryInitializer` - por exemplo, ao adicionar versão números ou valores calculados a partir de outras propriedades. 
* [A filtragem](app-insights-api-filtering-sampling.md#filtering) pode modificar ou descartar telemetria antes de ser enviado do SDK implementando `ITelemetryProcesor`. Controlar o que é enviado ou descartado, mas você precisa levar em conta do efeito no seu métricas. Dependendo de como você descartar itens, você pode perder a capacidade de navegar entre itens relacionados.
* [Amostragem](app-insights-api-filtering-sampling.md#sampling) é um pacote de solução para reduzir o volume dos dados enviados de seu aplicativo do portal. Isso é feito sem afetar as métricas exibidas e sem afetar a capacidade de diagnosticar problemas navegando entre itens relacionados como exceções, solicitações e modos de exibição de página.

[Saiba Mais](app-insights-api-filtering-sampling.md)


## <a name="disabling-telemetry"></a>Desabilitando Telemetria

Para **parar dinamicamente e iniciar** a coleção e transmissão de telemetria:

*C#*

```C#

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

Para **Desabilitar coletores padrão selecionados** - por exemplo, contadores de desempenho, solicitações HTTP ou dependências - exclua ou comente as linhas relevantes no [ApplicationInsights.config][config]. Você pode fazer isso, por exemplo, se você deseja enviar seus próprios dados TrackRequest.

## <a name="debug"></a>Modo de desenvolvedor

Durante a depuração, é útil ter seu telemetria emitida pelo pipeline de forma que você pode ver os resultados imediatamente. Você também obter mensagens adicionais que ajudam você rastrear problemas com a telemetria. Desativá-la em produção, como isso pode reduzir a velocidade de seu aplicativo.


*C#*
    
    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*VB*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="ikey"></a>Definir a chave de instrumentação de telemetria personalizada selecionada

*C#*
    
    var telemetry = new TelemetryClient();
    telemetry.InstrumentationKey = "---my key---";
    // ...


## <a name="dynamic-ikey"></a>Chave de instrumentação dinâmica

Para evitar a mistura de telemetria do ambientes de desenvolvimento, teste e produção, você pode [criar recursos de obtenção de informações de aplicativo separados] [ create] e altere suas chaves dependendo do ambiente.

Em vez de obter a chave de instrumentação do arquivo de configuração, você pode configurá-la em seu código. Defina a chave em um método de inicialização, como global.aspx.cs em um serviço ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey; 



Em páginas da web, talvez você queira defini-la a partir do servidor web estado, ao invés de codificação-literalmente no script. Por exemplo, em uma página da web gerado em um aplicativo do ASP.NET:

*JavaScript no Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="telemetrycontext"></a>TelemetryContext

TelemetryClient tem uma propriedade de contexto, que contém um número de valores que são enviados juntamente com todos os dados de telemetria. Eles normalmente são definidos pelos módulos telemetria padrão, mas você também pode defini-las por conta própria. Por exemplo:

    telemetry.Context.Operation.Name = "MyOperationName";

Se você definir qualquer um desses valores por conta própria, considere a remoção da linha relevante da [ApplicationInsights.config][config], para que seus valores e os valores padrão não fiquem confusos.

* **Componente** Identifica o aplicativo e sua versão
* **Dispositivo** Dados sobre o dispositivo em que o aplicativo é executado (nos aplicativos web, este é o dispositivo de servidor ou cliente do qual a telemetria é enviada)
* **InstrumentationKey** Identifica o recurso de obtenção de informações de aplicativo no Azure onde a telemetria aparecerá. Geralmente selecionada no ApplicationInsights.config
* **Local** Identifica a localização geográfica do dispositivo.
* **Operação** Em aplicativos web, a solicitação HTTP atual. Em outros tipos de aplicativo, você pode definir isso em eventos de grupo juntas.
 * **ID**: um valor gerado que correlaciona eventos diferentes, para que quando você inspecionar qualquer evento no diagnóstico de pesquisa, você pode localizar "itens relacionados"
 * **Nome**: um identificador, geralmente a URL da solicitação HTTP. 
 * **SyntheticSource**: se não nulo ou vazio, essa cadeia de caracteres indica que a fonte da solicitação foi identificado como um teste robô ou web. Por padrão, ele será excluído dos cálculos no Explorador de métricas.
* **Propriedades** Propriedades que são enviadas com todos os dados de telemetria. Pode ser substituído em chamadas controlar * individuais.
* **Sessão** Identifica a sessão do usuário. A Id está definida como um valor gerado, que é alterado quando o usuário não foi ativo por um tempo.
* **Usuário** Informações do usuário. 

## <a name="limits"></a>Limites


[AZURE.INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

*Como evitar atingir o limite de taxa de dados?*

* Use [amostragem](app-insights-sampling.md).

*Quanto tempo dados mantidos?*

* Consulte [privacidade e retenção de dados][data].


## <a name="reference-docs"></a>Documentos de referência

* [Referência do ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)
* [Referência Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Referência de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [SDK do Android](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)


## <a name="sdk-code"></a>Código SDK

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Pacotes do Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [SDK de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)
* [Todas as plataformas](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Perguntas

* *Quais exceções Track_() chamadas podem joga?*
    
    Nenhuma. Você não precisa quebrá-los em cláusulas try-catch. Se o SDK encontrar problemas, ele registrará mensagens que você verá na saída do console depuração, e - se as mensagens receber por meio - na pesquisa de diagnóstico.



* *Existe uma API REST para obter dados a partir do portal?*

    Sim, em breve. Enquanto isso, use [Exportar contínuo](app-insights-export-telemetry.md).

## <a name="next"></a>Próximas etapas


[Eventos de pesquisa e logs][diagnostic]

[Exemplos e orientações passo a passo](app-insights-code-samples.md)

[Solução de problemas][qna]


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

 
