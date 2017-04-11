<properties 
    pageTitle="Modelo de dados de obtenção de informações de aplicativo" 
    description="Descreve as propriedades exportados de exportação contínua em JSON e usados como filtros." 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/21/2016" 
    ms.author="awills"/>

# <a name="application-insights-export-data-model"></a>Modelo de dados de exportação de obtenção de informações de aplicativo

Esta tabela lista as propriedades de telemetria enviado de SDK do [Aplicativo ideias](app-insights-overview.md) para o portal. Você verá essas propriedades na saída de dados de [Exportar contínuo](app-insights-export-telemetry.md).
Também aparecem em filtros de propriedade no [Explorador de métrica](app-insights-metrics-explorer.md) e [Diagnóstico de pesquisa](app-insights-diagnostic-search.md).

Pontos de nota:

* `[0]`nessas tabelas indica um ponto no caminho onde você precisa inserir um índice; mas nem sempre é 0.
* As durações de tempo são em décimos de microssegundos, portanto 10000000 = = 1 segundo.
* Datas e horas são UTC e recebem no formato ISO`yyyy-MM-DDThh:mm:ss.sssZ`

Há vários [exemplos](app-insights-export-telemetry.md#code-samples) que ilustram como usá-las.



## <a name="example"></a>Exemplo

    // A server report about an HTTP request
    {
    "request": [ 
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": "" 
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events 
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0", 
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0, 
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }




## <a name="context"></a>Contexto

Todos os tipos de telemetria acompanhados de uma seção de contexto. Nem todos esses campos são transmitidos com cada ponto de dados.



|Caminho|Tipo|Anotações|
|---|---|---|
| Context.Custom.Dimensions [0]  | [objeto]  | Pares de cadeia de caracteres de chave-valor definido pelo parâmetro de propriedades personalizadas. Chave o tamanho máximo 100, comprimento máximo de valores 1024. Mais de 100 valores exclusivos, a propriedade pode ser pesquisada, mas não pode ser usada para segmentação. Chaves de máximo 200 por ikey.  |
| Context.Custom.Metrics [0]  | [] do objeto  | Pares de chave-valor definido pelo parâmetro de medidas personalizadas e pelo TrackMetrics. Comprimento máximo chave 100, valores podem ser numéricos. |
| context.data.eventTime | cadeia de caracteres | UTC |
| context.data.isSynthetic | booliano | Solicitação parece vir de um teste web ou bot. |
| context.data.samplingRate | número | Porcentagem de telemetria gerada pelo SDK que é enviado ao portal. Intervalo 0,0 100,0.|
| Context.Device | objeto | Dispositivo cliente |
| Context.Device.browser | cadeia de caracteres | IE, Chrome,... |
| context.device.browserVersion | cadeia de caracteres | Chrome 48,0,... |
| context.device.deviceModel | cadeia de caracteres | |
| context.device.deviceName | cadeia de caracteres | |
| Context.Device.ID | cadeia de caracteres | |
| Context.Device.Locale | cadeia de caracteres | en-GB, de-DE,... |
| Context.Device.Network | cadeia de caracteres | |
| context.device.oemName | cadeia de caracteres | |
| context.device.osVersion | cadeia de caracteres | Sistema operacional de host |
| context.device.roleInstance | cadeia de caracteres | ID do host do servidor |
| context.device.roleName | cadeia de caracteres | |
| Context.Device.Type | cadeia de caracteres | PC, navegador,... |
| Context.Location | objeto | Derivado de clientip. |
| Context.Location.City | cadeia de caracteres | Derivado clientip, se conhecido  |
| Context.Location.ClientIP | cadeia de caracteres | Último octógono é anonymized como 0. |
| Context.Location.Continent | cadeia de caracteres | |
| Context.Location.Country | cadeia de caracteres | |
| Context.Location.province | cadeia de caracteres | Estado ou província |
| Context.Operation.ID | cadeia de caracteres | Itens que possuem a mesma identificação de operação são mostrados como itens relacionados no portal. Geralmente, o id de solicitação. |
| Context.Operation.Name | cadeia de caracteres | URL ou solicitação de nome |
| context.operation.parentId | cadeia de caracteres | Permite aninhados itens relacionados. |
| Context.Session.ID | cadeia de caracteres | ID de um grupo de operações da mesma fonte. Um período de 30 minutos sem uma operação sinaliza o fim de uma sessão. |
| context.session.isFirst | booliano | |
| context.user.accountAcquisitionDate | cadeia de caracteres | |
| context.user.anonAcquisitionDate | cadeia de caracteres | |
| context.user.anonId | cadeia de caracteres | |
| context.user.authAcquisitionDate | cadeia de caracteres | [Usuário autenticado](app-insights-api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated | booliano | |
| internal.data.documentVersion | cadeia de caracteres | |
| Internal.Data.ID | cadeia de caracteres | |



## <a name="events"></a>Eventos

Eventos personalizados gerados pelo [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event). 


|Caminho|Tipo|Anotações|
|---|---|---|
| Contagem de eventos [0] | número inteiro | 100 / (taxa de[amostragem](app-insights-sampling.md) ). Por exemplo 4 =&gt; 25%. |
| nome do evento [0] | cadeia de caracteres | Nome do evento.  Comprimento máximo de 250. |
| url do evento [0] | cadeia de caracteres | |
| urlData.base de evento [0] | cadeia de caracteres | |
| urlData.host de evento [0] | cadeia de caracteres | |

## <a name="exceptions"></a>Exceções

[Exceções](app-insights-asp-net-exceptions.md) no servidor e no navegador de relatórios. 


|Caminho|Tipo|Anotações|
|---|---|---|
| montagem de basicException [0] | cadeia de caracteres | |
| Contagem de basicException [0] | número inteiro | 100 / (taxa de[amostragem](app-insights-sampling.md) ). Por exemplo 4 =&gt; 25%. |
| exceptionGroup basicException [0] | cadeia de caracteres | |
| basicException [0] exceptionType | cadeia de caracteres | |cadeia de caracteres | |
| failedUserCodeMethod basicException [0] | cadeia de caracteres | |
| failedUserCodeAssembly basicException [0] | cadeia de caracteres | |
| handledAt basicException [0] | cadeia de caracteres | |
| hasFullStack basicException [0] | booliano | |
| identificação de basicException [0] | cadeia de caracteres | |
| método de basicException [0] | cadeia de caracteres | |
| mensagem de basicException [0] | cadeia de caracteres | Mensagem de exceção. Comprimento máximo de 10k.|
| outerExceptionMessage basicException [0] | cadeia de caracteres | |
| outerExceptionThrownAtAssembly basicException [0] | cadeia de caracteres | |
| outerExceptionThrownAtMethod basicException [0] | cadeia de caracteres | |
| outerExceptionType basicException [0] | cadeia de caracteres | |
| outerId basicException [0] | cadeia de caracteres | |
| montagem de parsedStack [0] basicException [0] | cadeia de caracteres | |
| basicException [0] parsedStack [0] fileName | cadeia de caracteres | |
| nível de parsedStack [0] basicException [0] | número inteiro | |
| linha de parsedStack [0] basicException [0] | número inteiro | |
| método de parsedStack [0] basicException [0] | cadeia de caracteres | |
| pilha de basicException [0] | cadeia de caracteres | Comprimento máximo de 10k|
| typeName basicException [0] | cadeia de caracteres | |



## <a name="trace-messages"></a>Rastrear mensagens

Enviado por [TrackTrace](app-insights-api-custom-events-metrics.md#track-trace)e pelo [registro em log adaptadores](app-insights-asp-net-trace-logs.md).


|Caminho|Tipo|Anotações|
|---|---|---|
| mensagem [0] loggerName | cadeia de caracteres ||
| parâmetros de mensagem [0] | cadeia de caracteres ||
| mensagem [0] bruta | cadeia de caracteres | A mensagem de log, comprimento máximo 10k. |
| mensagem [0] severityLevel | cadeia de caracteres | |



## <a name="remote-dependency"></a>Dependência remota

Enviado por TrackDependency. Usado para o desempenho do relatório e o uso de [chamadas para dependências](app-insights-asp-net-dependencies.md) no servidor e AJAX chama no navegador.

|Caminho|Tipo|Anotações|
|---|---|---|
| remoteDependency [0] assíncrono | booliano | |
| baseName remoteDependency [0] | cadeia de caracteres |  |
| commandName remoteDependency [0] | cadeia de caracteres | Por exemplo "home/índice" |
| Contagem de remoteDependency [0] | número inteiro | 100 / (taxa de[amostragem](app-insights-sampling.md) ). Por exemplo 4 =&gt; 25%. |
| dependencyTypeName remoteDependency [0] | cadeia de caracteres | HTTP, SQL,... |
| durationMetric.value remoteDependency [0] | número | Hora da chamada para conclusão de resposta por dependência |
| identificação de remoteDependency [0] | cadeia de caracteres | |
| nome de remoteDependency [0] | cadeia de caracteres | URL. Comprimento máximo de 250.|
| resultCode remoteDependency [0] | cadeia de caracteres | de dependência HTTP |
| sucesso remoteDependency [0] | booliano | |
| tipo de remoteDependency [0] | cadeia de caracteres | Http, Sql,... |
| url de remoteDependency [0] | cadeia de caracteres |  Comprimento máximo de 2000 |
| urlData.base remoteDependency [0] | cadeia de caracteres | Comprimento máximo de 2000 |
| urlData.hashTag remoteDependency [0] | cadeia de caracteres | |
| urlData.host remoteDependency [0] | cadeia de caracteres | Comprimento máximo 200 |


## <a name="requests"></a>Solicitações

Enviado por [TrackRequest](app-insights-api-custom-events-metrics.md#track-request). Módulos padrão usam para tempo de resposta do servidor de relatórios, medido no servidor. 


|Caminho|Tipo|Anotações|
|---|---|---|
| Contagem de solicitação [0] | número inteiro | 100 / (taxa de[amostragem](app-insights-sampling.md) ). Por exemplo: 4 =&gt; 25%. |
| Solicitar durationMetric.value [0] | número | Hora da solicitação chegar à resposta. 1e7 = = 1s |
| id da solicitação [0] | cadeia de caracteres | Id da operação |
| nome da solicitação [0] | cadeia de caracteres | GET/POST + url base.  Comprimento máximo de 250 |
| Solicitar responseCode [0] | número inteiro | Resposta HTTP enviada ao cliente |
| Solicitar sucesso [0] | booliano | Padrão = = (responseCode &lt; 400) |
| url da solicitação [0] | cadeia de caracteres | Não incluindo host |
| Solicitar urlData.base [0] | cadeia de caracteres | |
| Solicitar urlData.hashTag [0] | cadeia de caracteres |  |
| Solicitar urlData.host [0] | cadeia de caracteres | |


## <a name="page-view-performance"></a>Desempenho do modo de exibição de página

Enviada pelo navegador. Avalia o tempo para processar uma página, de usuário que inicia a solicitação para exibir concluída (excluindo chamadas de AJAX assíncronas).

Valores de contexto mostram a versão do navegador e sistema operacional cliente. 


|Caminho|Tipo|Anotações|
|---|---|---|
| clientProcess.value no desempenhodo cliente [0] | número inteiro | Hora de término de receber o código HTML para exibir a página. |
| nome no desempenhodo cliente [0] | cadeia de caracteres | |
| networkConnection.value no desempenhodo cliente [0] | número inteiro | Tempo necessário para estabelecer uma conexão de rede. |
| receiveRequest.value no desempenhodo cliente [0] | número inteiro | Hora de término de enviar a solicitação para receber o código HTML na resposta. |
| sendRequest.value no desempenhodo cliente [0] | número inteiro | Tempo de dado para enviar a solicitação HTTP. |
| total.value no desempenhodo cliente [0] | número inteiro | Tempo comecem a enviar a solicitação para exibir a página. |
| url no desempenhodo cliente [0] | cadeia de caracteres | URL dessa solicitação |
| urlData.base no desempenhodo cliente [0] | cadeia de caracteres | |
| urlData.hashTag no desempenhodo cliente [0] | cadeia de caracteres | |
| urlData.host no desempenhodo cliente [0] | cadeia de caracteres | |
| urlData.protocol no desempenhodo cliente [0] | cadeia de caracteres | |

## <a name="page-views"></a>Modos de exibição de página

Enviado por trackPageView() ou [stopTrackPage](app-insights-api-custom-events-metrics.md#page-view)

|Caminho|Tipo|Anotações|
|---|---|---|
| Exibir a contagem de [0] | número inteiro | 100 / (taxa de[amostragem](app-insights-sampling.md) ). Por exemplo 4 =&gt; 25%. |
| Exibir durationMetric.value [0] | número inteiro | Valor Opcionalmente defina trackPageView() ou através de startTrackPage() - stopTrackPage(). Não é o mesmo como valores de nodesempenho do cliente. |
| nome de exibição [0] | cadeia de caracteres | Título da página.  Comprimento máximo de 250 |
| url de modo de exibição [0] | cadeia de caracteres | |
| Exibir urlData.base [0] | cadeia de caracteres | |
| Exibir urlData.hashTag [0] | cadeia de caracteres | |
| Exibir urlData.host [0] | cadeia de caracteres | |



## <a name="availability"></a>Disponibilidade

[Testes de web de disponibilidade](app-insights-monitor-web-app-availability.md)de relatórios.

|Caminho|Tipo|Anotações|
|---|---|---|
| disponibilidade [0] availabilityMetric.name | cadeia de caracteres | disponibilidade |
| disponibilidade [0] availabilityMetric.value | número |1.0 ou 0,0 |
| Contagem de disponibilidade [0] | número inteiro | 100 / (taxa de[amostragem](app-insights-sampling.md) ). Por exemplo 4 =&gt; 25%. |
| disponibilidade [0] dataSizeMetric.name | cadeia de caracteres | |
| disponibilidade [0] dataSizeMetric.value | número inteiro | |
| disponibilidade [0] durationMetric.name | cadeia de caracteres | |
| disponibilidade [0] durationMetric.value | número | Duração de teste. 1e7 = = 1s |
| mensagem de disponibilidade [0] | cadeia de caracteres | Falha de diagnóstico |
| resultado de disponibilidade [0] | cadeia de caracteres | Aprovado/reprovado |
| disponibilidade [0] runLocation | cadeia de caracteres | Fonte de geográfica de req http |
| testName disponibilidade [0] | cadeia de caracteres | |
| disponibilidade [0] testRunId | cadeia de caracteres | |
| disponibilidade [0] testTimestamp | cadeia de caracteres | |




## <a name="metrics"></a>Métricas

Gerado pelo TrackMetric().

O valor de métrica é encontrado na context.custom.metrics[0]

Por exemplo:

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>Sobre valores métricas

Valores de métrica, tanto em relatórios métricos e em outro lugar, são relatados com uma estrutura de objeto padrão. Por exemplo:

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Atualmente - apesar isso pode mudar no futuro - em todos os valores relatados de módulos SDK padrão, `count==1` e somente o `name` e `value` campos são úteis. O único caso onde eles seriam diferentes seria se você escrever seus próprio chamadas TrackMetric no qual você definir os outros parâmetros. 

A finalidade dos outros campos é permitir que as métricas sejam agregados no SDK, para reduzir o tráfego para o portal. Por exemplo, você poderia média várias leituras sucessivas antes de enviar cada relatório de métricas. Em seguida, você poderia calcular min, max, desvio padrão de valor agregado (soma ou média) e definir contagem para o número de leituras representado pelo relatório. 

Nas tabelas acima, podemos ter omitido a contagem de campos raramente usados, min, max, DESVPAD e sampledValue.

Em vez de métricas previamente agregação, você pode usar [amostragem](app-insights-sampling.md) se você precisar reduzir o volume de telemetria.


### <a name="durations"></a>Durações

Exceto onde seja observado o contrário, durações são representadas em décimos de um microssegundos, para que 10000000.0 significa 1 segundo.



## <a name="see-also"></a>Consulte também

* [Obtenção de informações de aplicativo](app-insights-overview.md) 
* [Exportar contínuo](app-insights-export-telemetry.md)
* [Exemplos de código](app-insights-export-telemetry.md#code-samples)


