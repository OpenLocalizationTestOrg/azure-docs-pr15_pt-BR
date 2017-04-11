<properties 
    pageTitle="Obtenção de informações de aplicativo para Core do ASP.NET" 
    description="Monitorar aplicativos web para disponibilidade, desempenho e uso." 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="awills"/>

# <a name="application-insights-for-aspnet-core"></a>Obtenção de informações de aplicativo para Core do ASP.NET

[Ideias de aplicativo do Visual Studio](app-insights-overview.md) permite monitorar o seu aplicativo web para disponibilidade, desempenho e uso. Com os comentários que você obtenha sobre o desempenho e a eficiência do seu aplicativo por aí, você pode fazer escolhas informadas sobre a direção do design em cada ciclo de vida de desenvolvimento.

![Exemplo](./media/app-insights-asp-net-core/sample.png)

Você precisará de uma assinatura com o [Microsoft Azure](http://azure.com). Entrar com uma conta da Microsoft, que você pode ter para Windows, XBox Live ou outros serviços de nuvem da Microsoft. Sua equipe pode ter uma assinatura organizacional do Azure: peça ao proprietário para adicioná-lo a ele usando sua conta da Microsoft.


## <a name="getting-started"></a>Guia de Introdução

Siga o [guia de Introdução](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started).

## <a name="using-application-insights"></a>Usando a obtenção de informações de aplicativo

Entre no [portal do Microsoft Azure](https://portal.azure.com) e navegue até o recurso que você criou para monitorar seu aplicativo.

Em uma janela separada do navegador, use seu aplicativo por um tempo. Você verá dados apareçam nos gráficos de obtenção de informações do aplicativo. (Talvez seja necessário clicar em Atualizar.) Haverá apenas uma pequena quantidade de dados enquanto você está desenvolvendo, mas esses gráficos realmente vêm ativos quando você publica seu aplicativo e tiver muitos usuários. 

Na página Visão geral mostra os gráficos de desempenho que provavelmente se interessarão na: tempo de resposta do servidor, tempo de carregamento de página e contagens de solicitações com falha. Clique em qualquer gráfico para ver mais gráficos e dados.

Modos de exibição no portal se encaixam em duas categorias principais:

* [Métricas Explorer](app-insights-metrics-explorer.md) mostra gráficos e tabelas de métricas e contagens, como tempos de resposta, taxas de falha ou métricas você mesmo cria com a [API](app-insights-api-custom-events-metrics.md). Filtrar e os dados de segmento por valores de propriedade para obter uma melhor compreensão de seu aplicativo e seus usuários.
* [Search Explorer](app-insights-diagnostic-search.md) lista eventos individuais, como solicitações específicas, exceções, rastreamentos de log ou eventos que você criou por conta própria com a [API](app-insights-api-custom-events-metrics.md). Filtrar e pesquisar os eventos e navegar entre os eventos relacionados ao investigar problemas.
* [Análise](app-insights-analytics.md) permite executar consultas SQL sobre sua telemetria e é uma ferramenta poderosa de diagnóstico e analítica.

## <a name="alerts"></a>Alertas

* Você obtém automaticamente [alertas de diagnósticos proativos](app-insights-proactive-diagnostics.md) que informar você sobre alterações anormais em taxas de falha e outras métricas.
* Configurar [testes de disponibilidade](app-insights-monitor-web-app-availability.md) para testar seu site continuamente de locais no mundo inteiro e obtenha emails assim que qualquer teste falhar.
* Configure [alertas métricas](app-insights-monitor-web-app-availability.md) saber se métricas como tempos de resposta ou taxas de exceção vá fora dos limites aceitáveis.

## <a name="get-more-telemetry"></a>Obtenha mais Telemetria

* [Adicionar telemetria às páginas da web](app-insights-javascript.md) para monitorar o uso de página e o desempenho.
* [Dependências de monitor](app-insights-dependencies.md) para ver se o resto, SQL ou outros recursos externos são desacelerando você.
* [Usar a API](app-insights-api-custom-events-metrics.md) para enviar sua própria eventos e métricas para uma exibição mais detalhada de desempenho e o uso de seu aplicativo.
* [Testes de disponibilidade](app-insights-monitor-web-app-availability.md) Verifique seu aplicativo constantemente de todo o mundo. 


## <a name="open-source"></a>Abrir origem

[Ler e contribuam com o código](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)


