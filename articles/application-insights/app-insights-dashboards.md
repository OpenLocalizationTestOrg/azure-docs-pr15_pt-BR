<properties
    pageTitle="Painéis e navegação no portal do aplicativo ideias | Microsoft Azure"
    description="Crie modos de exibição de sua chaves gráficos APM e consultas."
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
    ms.date="10/18/2016"
    ms.author="awills"/>

# <a name="navigation-and-dashboards-in-the-application-insights-portal"></a>Navegação e painéis no portal de obtenção de informações de aplicativo

Depois que você tiver [configurado a obtenção de informações de aplicativo em seu projeto](app-insights-overview.md), dados de telemetria sobre desempenho e o uso de seu aplicativo serão exibido no recurso de obtenção de informações de aplicativo do seu projeto no [portal do Azure](https://portal.azure.com).


## <a name="find-your-telemetry"></a>Localizar seu Telemetria

Entre [portal do Azure](https://portal.azure.com) e navegue para o recurso de obtenção de informações do aplicativo que você criou para seu aplicativo.

![Clique em Procurar, selecione a obtenção de informações do aplicativo, em seguida, seu aplicativo.](./media/app-insights-dashboards/00-start.png)

A lâmina de visão geral (página) para o aplicativo mostra um resumo das principais métricas diagnósticos do aplicativo e é um gateway para os outros recursos do portal.


![Principais rotas para exibir seu Telemetria](./media/app-insights-dashboards/010-oview.png)

Você pode personalizar qualquer um dos outros gráficos e fixá-los a um painel. Dessa forma, você pode reunir os gráficos de chave de diferentes aplicativos.


## <a name="dashboards"></a>Painéis

A primeira coisa que você vê depois de entrar no [portal do Microsoft Azure](https://portal.azure.com) é um painel. Aqui você pode reunir os gráficos que são mais importantes para você em todos os seus recursos Azure, incluindo telemetria de [Ideias de aplicativo do Visual Studio](app-insights-overview.md).
 

![Um painel personalizado.](./media/app-insights-dashboards/31.png)


1. **Navegar para recursos específicos** , como o seu aplicativo no aplicativo ideias: usar a barra à esquerda.
2. **Retorne ao painel atual**ou alternar para outros modos de exibição recentes: Use o menu suspenso no canto superior esquerdo.
3. **Painéis de mudança**: Use o menu suspenso no título do painel
4. **Criar, editar e compartilhar painéis** na barra de painel.
5. **Editar painel**: passe o mouse sobre um bloco e use sua barra superior para mover, personalizar ou removê-lo.


## <a name="add-to-a-dashboard"></a>Adicionar a um painel

Quando você estiver olhando uma lâmina ou conjunto de gráficos que é particularmente interessante, você pode fixar uma cópia no painel. Você verá próxima vez que você retorne lá.

![Para fixar um gráfico, passe o mouse sobre ele e clique em "..." no cabeçalho.](./media/app-insights-dashboards/33.png)

1. Gráfico de pino ao painel. Uma cópia do gráfico aparece no painel.
2. Fixar a lâmina inteira ao painel - aparece no painel como um bloco que pode ser clicadas.
3. Clique no canto superior esquerdo para retornar ao painel de dados atual. Em seguida, você pode usar o menu suspenso para retornar ao modo de exibição atual.

Observe que os gráficos são agrupados em blocos: um bloco pode conter mais de um gráfico. Fixar o bloco inteiro ao painel.

### <a name="pin-any-query-in-analytics"></a>Fixar qualquer consulta do Analytics

Você também pode [Fixar Analytics](app-insights-analytics-using.md#pin-to-dashboard) gráficos a um painel [compartilhado](#share-dashboards-with-your-team) . Isso permite que você adicione gráficos de qualquer consulta aleatório junto com as métricas padrão. (Há um custo para este recurso.)

## <a name="adjust-a-tile-on-the-dashboard"></a>Ajustar um bloco no painel

Depois de um bloco no painel, você pode ajustá-lo.

![Passe o mouse sobre um gráfico para editá-lo.](./media/app-insights-dashboards/36.png)

1. Adicione um gráfico para a peça. 
2. Defina métrica, agrupar por dimensão e estilo (tabela, gráfico) de um gráfico.
3. Arraste o diagrama para ampliar; Clique no botão Desfazer para redefinir o período de tempo; definir propriedades de filtro para os gráficos no bloco.
4. Definir o título de peças.

Blocos fixados de blades explorer métrica tem mais opções de edição que blocos fixos de uma lâmina de visão geral.

O bloco original que você fixados não é afetado pela suas edições.


## <a name="switch-between-dashboards"></a>Alternar entre painéis

Você pode salvar mais de um painel e alternar entre eles. Quando você fixa um gráfico ou blade, são adicionados ao painel de dados atual.

![Para alternar entre painéis, clique em Painel de controle e selecione um painel salvo. Para criar e salvar um novo painel, clique em novo. Para reorganizar, clique em Editar.](./media/app-insights-dashboards/32.png)

Por exemplo, você pode ter um painel de controle para exibir em tela inteira na sala de equipe e outra para desenvolvimento geral.


No painel um blade aparece como um bloco: clicar nele para acessar lâmina. Um gráfico replica o gráfico em seu local original.

![Clique em um bloco para abrir a lâmina que ele representa](./media/app-insights-dashboards/35.png)


## <a name="share-dashboards"></a>Compartilhar painéis

Quando você tiver criado um painel, você pode compartilhá-la com outros usuários.

![No cabeçalho do painel de controle, clique em compartilhar](./media/app-insights-dashboards/41.png)

Saiba mais sobre [funções e controle de acesso](app-insights-resources-roles-access-control.md).

## <a name="app-navigation"></a>Navegação de aplicativo

A lâmina de visão geral é o gateway para obter mais informações sobre o aplicativo.

* **Qualquer gráfico ou bloco** - clique em qualquer lado a lado ou do gráfico para ver mais detalhes sobre o que é exibido.

### <a name="overview-blade-buttons"></a>Botões de blade de visão geral


![Barra de navegação superior de blade de visão geral](./media/app-insights-dashboards/app-overview-top-nav.png)


* [**Métricas Explorer**](app-insights-metrics-explorer.md) - criar seus próprios gráficos de desempenho e uso.
* [**Pesquisa**](app-insights-diagnostic-search.md) - investigar instâncias específicas de eventos como solicitações, exceções, ou rastreamentos de log.
* [**Análise**](app-insights-analytics.md) - consultas avançadas sobre sua telemetria.
* **Intervalo de tempo** - ajustar o intervalo exibido por todos os gráficos na lâmina.
* **Excluir** - excluir o recurso de obtenção de informações do aplicativo para este aplicativo. Você deve também remova os pacotes de obtenção de informações de aplicativo do seu código de aplicativo, ou edite a [chave de instrumentação](app-insights-create-new-resource.md#copy-the-instrumentation-key) em seu aplicativo para direcionar telemetria para um recurso de obtenção de informações de aplicativo diferente.

### <a name="essentials-tab"></a>Guia de Essentials

* [Chave de instrumentação](app-insights-create-new-resource.md#copy-the-instrumentation-key) - identifica este recurso do aplicativo. 
* Preços - tornar recursos caps de volume disponíveis e definir.


### <a name="app-navigation-bar"></a>Barra de navegação do aplicativo


![Barra de navegação à esquerda](./media/app-insights-dashboards/app-left-nav-bar.png)

* **Visão geral** - retornar para a lâmina de visão geral do aplicativo.
* **Log de atividade** - alertas e eventos administrativos Azure.
* [**Controle de acesso**](app-insights-resources-roles-access-control.md) - fornecer acesso aos membros da equipe e outras pessoas.
* [**Marcas**](../resource-group-using-tags.md) - usar marcas para agrupar seu aplicativo com outras pessoas.

INVESTIGAR

* [**Mapa de aplicativo**](app-insights-app-map.md) - Active mapa mostrando os componentes de seu aplicativo, deriva as informações de dependência.
* [**Diagnóstico proativo**](app-insights-proactive-diagnostics.md) - alertas de desempenho recentes de revisão.
* [**Fluxo ao vivo**](app-insights-metrics-explorer.md#live-stream) - um conjunto de métricas de quase instantânea, útil ao implantar uma nova compilação de fixo ou depuração.
* [**Disponibilidade / testes de Web**](app-insights-monitor-web-app-availability.md) -enviar solicitações de normais para seu aplicativo web em torno do world.* 
* [**Falhas, desempenho**](app-insights-web-monitor-performance.md) - exceções, taxas de falha e tempos de resposta para solicitações para o seu aplicativo e para solicitações de seu aplicativo para [dependências](app-insights-asp-net-dependencies.md).
* [**Desempenho**](app-insights-web-monitor-performance.md) - tempo de resposta, tempos de resposta de dependência. 
* [Servidores](app-insights-web-monitor-performance.md) - contadores de desempenho. Disponível se você [instalar o Monitor de Status](app-insights-monitor-performance-live-website-now.md).

* **Navegador** - modo de exibição de página e desempenho de AJAX. Disponível se você [instrumentar suas páginas da web](app-insights-javascript.md).
* **Uso** - modo de exibição de página, usuário e sessão de conta. Disponível se você [instrumentar suas páginas da web](app-insights-javascript.md).

CONFIGURAR

* **Introdução** - tutorial embutida.
* **Propriedades** - chave de instrumentação, assinatura e identificação do recurso.
* [Alertas](app-insights-alerts.md) - configuração de alerta métrica.
* [Exportar contínuo](app-insights-export-telemetry.md) - configurar a exportação de telemetria para armazenamento do Azure.
* [Teste de desempenho](app-insights-monitor-web-app-availability.md#performance-tests) - configure uma carga sintética em seu site.
* [Cota de preços](app-insights-pricing.md) e [amostragem de inclusão](app-insights-sampling.md).
* **Acesso de API** - criar [anotações de lançamento](app-insights-annotations.md) e para a API de acesso de dados.
* [**Itens de trabalho**](app-insights-diagnostic-search.md#create-work-item) - conectar a um sistema de controle para que você possa criar bugs ao inspecionar telemetria de trabalho.

Configurações


* [**Bloqueia**](..\resource-group-lock-resources.md) - bloquear recursos do Azure
* [**Script de automação**](app-insights-powershell.md) - exportar uma definição do recurso Azure para que você possa usá-lo como um modelo para criar novos recursos.

SUPORTE

* **Solicitação de suporte** - exige uma assinatura paga. Consulte também [Obtendo ajuda](app-insights-get-dev-support.md).

## <a name="whats-next"></a>O que vem a seguir?

||
|---|---
|[Explorador de métricas](app-insights-metrics-explorer.md)<br/>Métricas de filtro e segmento|![Exemplo de pesquisa](./media/app-insights-dashboards/64.png)
|[Pesquisa de diagnóstico](app-insights-diagnostic-search.md)<br/>Localizar e inspecionar eventos, eventos relacionados e criar bugs |![Exemplo de pesquisa](./media/app-insights-dashboards/61.png)
|[Análise](app-insights-analytics.md)<br/>Linguagem de consulta avançada| ![Exemplo de pesquisa](./media/app-insights-dashboards/63.png)





