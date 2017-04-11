Há alguns limites no número de métricas e eventos por aplicativo (ou seja, por chave de instrumentação). 

Limites dependem o [nível de preço](https://azure.microsoft.com/pricing/details/application-insights/) que você escolher.

**Recurso** | **Limite padrão** | **Limite máximo**
-------- | ------------- | -------------
Pontos de dados de sessão<sup>1, 2</sup> por mês | ilimitado | 
Pontos de dados total por mês para solicitação, eventos, dependência, rastreamento, exceção e modo de exibição de página | 5 milhões | 50 milhões<sup>3</sup>
Taxa de [rastreamento e o Log](../articles/application-insights/app-insights-search-diagnostic-logs.md) de dados | ponto de distribuição 200/s | ponto de distribuição 500/s
Taxa de dados de [exceção](../articles/application-insights/app-insights-asp-net-exceptions.md) | ponto de distribuição de 50/s | ponto de distribuição de 50/s
Taxa de total de dados de solicitação, eventos, dependência e telemetria do modo de exibição de página | ponto de distribuição 200/s | ponto de distribuição 500/s
Retenção de dados bruto para [pesquisa](../articles/application-insights/app-insights-diagnostic-search.md) e [análise](../articles/application-insights/app-insights-analytics.md) | 7 dias
Retenção de dados agregados para [explorer métricas](../articles/application-insights/app-insights-metrics-explorer.md) | 90 dias
Contagem de nome de [propriedade](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) | 100 |
Comprimento do nome de propriedade | 150 | 
Comprimento do valor de propriedade | 8192 | 
Comprimento da mensagem de rastreamento e de exceção | 10000 |
Contagem de nome de [métrica](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) | 100 |
Comprimento do nome métrica |  150 | 
[Testes de disponibilidade](../articles/application-insights/app-insights-monitor-web-app-availability.md) | 10 | 

<sup>1</sup> um ponto de dados é um valor de métrica individual ou evento, com propriedades anexadas e medidas.

<sup>2</sup> A sessão ponto de dados faz o início ou fim de uma sessão e logs de identidade do usuário.

<sup>3</sup> você pode adquirir capacidade adicional além 50 milhões.
 
[Sobre preços e cotas em ideias de aplicativo](../articles/application-insights/app-insights-pricing.md)
