<properties 
    pageTitle="Análises - a ferramenta de pesquisa avançada de obtenção de informações de aplicativo | Microsoft Azure" 
    description="Visão geral de análise, a ferramenta de pesquisa de diagnóstico poderosos de obtenção de informações do aplicativo. " 
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
    ms.date="07/26/2016" 
    ms.author="awills"/>


# <a name="analytics-in-application-insights"></a>Análise de obtenção de informações de aplicativo


[Análise](app-insights-analytics.md) é o recurso de pesquisa avançada de [Obtenção de informações do aplicativo](app-insights-overview.md). Estas páginas descrevem a lanquage de consulta de análise. 

* **[Assista ao vídeo introdutório](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Unidade de teste Analytics em nossos dados simulados](https://analytics.applicationinsights.io/demo)** se seu aplicativo não estiver enviando dados de obtenção de informações do aplicativo ainda.

## <a name="queries-in-analytics"></a>Consultas no Analytics
 
Uma consulta típica é uma tabela de *origem* seguida por uma série de *operadores* separados por `|`. 

Por exemplo, vamos descobrir qual horário do dia cidadãos de Hyderabad tente nosso aplicativo web. E, enquanto estiver lá, vamos ver quais códigos de resultado são retornados às suas solicitações HTTP. 

```AIQL

    requests      // Table of events that log HTTP requests.
  	| where timestamp > ago(7d) and client_City == "Hyderabad"
  	| summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
  	| extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

Podemos contar endereços IP do cliente distintos, agrupando-os por hora do dia nos últimos 7 dias. 

Vamos exibir os resultados com a apresentação de gráfico de barras, escolhendo empilhar os resultados de códigos de resposta diferente:

![Escolha o gráfico de barras, x e y eixos e segmentação](./media/app-insights-analytics/020.png)

Parece que o nosso aplicativo está mais popular na hora do almoço e canteiro-tempo em Hyderabad. (E podemos deve investigar esses 500 códigos).


Também existem operações de estatísticas poderosas:

![](./media/app-insights-analytics/025.png)


O idioma tem muitos recursos atraentes:

* [Filtro de](app-insights-analytics-reference.md#where-operator) telemetria seu aplicativo bruto por todos os campos, incluindo suas propriedades personalizadas e métricas.
* [Ingressar em](app-insights-analytics-reference.md#join-operator) várias tabelas – correlacionar solicitações com modos de exibição de página, chamadas de dependência, exceções e log rastreia.
* Estatística poderosos [agregações](app-insights-analytics-reference.md#aggregations).
* Tão potente quanto SQL, mas muito mais fácil para consultas complexas: em vez de instruções de aninhamento, você conduza os dados de uma operação de ensino fundamental para o próximo.
* Visualizações de imediatas e eficientes.







## <a name="connect-to-your-application-insights-data"></a>Conectar aos dados ideias de aplicativo


Abra a análise da [lâmina de visão geral](app-insights-dashboards.md) do seu aplicativo em ideias de aplicativo: 

![Abra portal.azure.com, abra o recurso de obtenção de informações do aplicativo e clique em análise.](./media/app-insights-analytics/001.png)


## <a name="limits"></a>Limites

No momento, os resultados da consulta limitam-se apenas uma semana dos últimos dados.



[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


## <a name="next-steps"></a>Próximas etapas


* Recomendamos que você iniciar o [tour do idioma](app-insights-analytics-tour.md).