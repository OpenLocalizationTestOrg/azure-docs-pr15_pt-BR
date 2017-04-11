<properties
    pageTitle="Dimensionar seu trabalho de análise de fluxo com funções de aprendizado de máquina do Azure | Microsoft Azure"
    description="Aprenda a dimensionar corretamente trabalhos de análise de fluxo (partição, quantidade de SO e mais) ao usar funções de aprendizado de máquina do Azure."
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>Dimensionar seu trabalho de análise de fluxo com funções de aprendizado de máquina do Azure

Muitas vezes é muito fácil configurar a análise de fluxo de trabalho e executar alguns dados de exemplo através dele. O que podemos fazer quando é necessário para executar o trabalho mesmo com o maior volume de dados? Ele exige noções básicas de como configurar o trabalho de análise de fluxo para que ela será dimensionada. Neste documento, nos concentraremos nos aspectos especiais de dimensionamento trabalhos de análise de fluxo com funções de aprendizado de máquina. Para obter informações sobre como dimensionar trabalhos de análise de fluxo em geral consulte o artigo [trabalhos de escala](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>O que é uma função de aprendizado de máquina do Azure em análise de fluxo?

Em análise de fluxo de uma função de aprendizado de máquina pode ser usada como uma chamada de função regular na linguagem de consulta de análise do fluxo. Entretanto, por trás a cena, as chamadas de função são realmente solicitações de serviço da Web de aprendizagem do Azure máquina. Serviços de web de aprendizado de máquina oferecem suporte a "lotes" várias linhas, que é chamado mini lote, na mesma web service API chamada, para melhorar a produtividade geral. Consulte os artigos a seguir para obter mais detalhes. [Funções de aprendizado de máquina do Azure no fluxo de análise](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/) e [Serviços de Web de aprendizado de máquina Azure](machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs).

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurar um trabalho de análise de fluxo com funções de aprendizado de máquina

Ao configurar uma função de aprendizado de máquina para trabalho de análise de fluxo, há dois parâmetros a considerar, o tamanho do lote das chamadas de função de aprendizado de máquina e as provisionada para o trabalho de análise de fluxo de unidades de Streaming (SUs). Para determinar os valores adequados para esses, primeiro uma decisão deve ser feita entre latência e taxa de transferência, ou seja, latência do trabalho de análise de fluxo e taxa de transferência de cada SO. SUs sempre podem ser adicionado a um trabalho para aumentar a produtividade de uma consulta de análise de fluxo bem particionada, embora SUs adicional aumenta o custo de executar o trabalho.

Portanto é importante determinar a *tolerância* de latência no executando uma a análise de fluxo de trabalho. Latência adicional executem solicitações de serviço de aprendizado de máquina do Azure naturalmente aumentará com tamanho de lote, que será composto a latência do trabalho a análise de fluxo. Por outro lado, o aumento do tamanho do lote permite que o trabalho de análise de fluxo processar *mais eventos com a *mesmo número * das solicitações de serviço web de aprendizado de máquina. Geralmente o aumento de latência de serviço da web de aprendizado de máquina é sub linear para o aumento do tamanho do lote, portanto é importante levar em consideração o tamanho do lote mais eficiente de custo para um serviço da web de aprendizado de máquina em qualquer determinada situação. O tamanho de lote padrão para as solicitações de serviço da web é 1000 e pode ser modificado por usando a [API do fluxo Analytics REST](https://msdn.microsoft.com/library/mt653706.aspx "API de REST de análise de fluxo") ou do [cliente do PowerShell para a análise de fluxo de](stream-analytics-monitor-and-manage-jobs-use-powershell.md "cliente do PowerShell para análise do fluxo").

Depois que um tamanho de lote tiver sido determinado, a quantidade de unidades de Streaming (SUs) pode ser determinada, com base no número de eventos que a função precisa processar por segundo. Para obter mais informações sobre unidades de Streaming, consulte o artigo [trabalhos de escala de análise de fluxo](stream-analytics-scale-jobs.md#configuring-streaming-units).

Em geral, há 20 conexões simultâneas para o serviço da web de aprendizado de máquina para cada 6 SUs, exceto que trabalhos de SO 1 e 3 trabalhos de SO obterá 20 conexões simultâneas também.  Por exemplo, se a taxa de dados de entrada é 200.000 eventos por segundo e o tamanho do lote é da esquerda para o padrão de 1000 a latência de serviço web resultante com lotes mini de eventos de 1000 é 200 ms. Isso significa que cada conexão pode fazer solicitações de 5 ao serviço da web de aprendizado de máquina em um segundo. Com 20 conexões, o trabalho de análise de fluxo pode processar 20.000 em 200 MS e eventos portanto 100.000 em um segundo. Portanto para processar 200.000 eventos por segundo, o trabalho de análise de fluxo precisa 40 conexões simultâneas, que vem check-out 12 SUs. O diagrama a seguir ilustra as solicitações do trabalho de análise de fluxo ao ponto de extremidade do serviço web de aprendizado de máquina – a cada 6 SUs tem 20 conexões simultâneas ao serviço da web de aprendizado de máquina em máx.

![Análise de fluxo de escala com exemplo de trabalho de 2 de funções de aprendizado de máquina] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Análise de fluxo de escala com exemplo de trabalho de 2 de funções de aprendizado de máquina")

Em geral, ***B*** por tamanho de lote, ***L*** para a latência de serviço web em tamanho do lote B em milissegundos, a taxa de transferência de um trabalho de análise de fluxo com ***N*** SUs é:

![Dimensionar a análise de fluxo com a fórmula de funções de aprendizado de máquina] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Dimensionar a análise de fluxo com a fórmula de funções de aprendizado de máquina")

Uma consideração adicional pode ser o 'máximo de chamadas simultâneas' no lado de serviço da web de aprendizado de máquina, recomenda-se defina o valor máximo (200 atualmente).

Para obter mais informações sobre esta Revise configuração o [artigo de dimensionamento para serviços de Web de aprendizado de máquina](../machine-learning/machine-learning-scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Exemplo – sentimento análise

O exemplo a seguir inclui um trabalho de análise de fluxo com a análise de sentimento função de aprendizado de máquina, conforme descrito no [tutorial de integração de aprendizado de máquina de análise do fluxo](stream-analytics-machine-learning-integration-tutorial.md).

A consulta é uma consulta totalmente particionada simples seguida pela função **sentimento** , conforme mostrado abaixo:

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery

Considere o seguinte cenário; com uma taxa de transferência de 10.000 tweets por segundo um trabalho de análise de fluxo deve ser criado para executar análise de sentimento dos tweets (eventos). Usando o 1 SO, esse trabalho de análise de fluxo poderá lidar com o tráfego? Usando o tamanho de lote padrão de 1000 o trabalho deve ser capaz de manter-se com a entrada. Ainda mais a função de aprendizado de máquina adicionada deve gerar não mais do que um segundo de latência, que é geral latência padrão da análise sentimento serviço da web de aprendizado de máquina (com um tamanho de lote padrão de 1000). Latência de ponta a ponta ou **Geral** do trabalho a análise de fluxo normalmente seria alguns segundos. Dê uma olhada mais detalhada nesse trabalho de análise de fluxo, *especialmente* a chamadas de função de aprendizado de máquina. Tendo o tamanho do lote como 1000, uma taxa de transferência de 10.000 eventos levará cerca de 10 solicitações de serviço web. Mesmo com 1 SO, há suficiente conexões simultâneas para acomodar o tráfego de entrada.

Mas e se a taxa de evento de entrada aumenta por 100 x e agora o trabalho de análise de fluxo precisa processar 1.000.000 tweets por segundo? Há duas opções:

1.  Aumentar o tamanho do lote, ou
2.  Partição o fluxo de entrada para processar os eventos em paralelo

Com a primeira opção, o trabalho **latência** aumentará.

Com a segunda opção, SUs mais precisa ser provisionado e, portanto, gerar mais simultâneas solicitações de serviço web de aprendizado de máquina. Isso significa que o trabalho **custo** aumentará.


Assuma que a latência da análise sentimento serviço da web de aprendizado de máquina é 200 ms para lotes de eventos de 1000 ou abaixo, 250ms para lotes de evento de 5.000, 300 ms para lotes de evento de 10.000 ou 500 ms para lotes de 25.000 evento.

1. Usando a primeira opção, (**não** provisionamento SUs mais), o tamanho do lote pode ser aumentado para **25.000**. Isso alternadamente permitirá o trabalho processar 1.000.000 eventos com 20 conexões simultâneas ao serviço da web de aprendizado de máquina (com uma latência de 500 ms por chamada). Portanto a latência adicional do trabalho fluxo Analytics devido as solicitações de função sentimento contra as solicitações de serviço de web de aprendizado de máquina poderia ser aumentada de **200 MS** para **500 ms**. No entanto, observe que em lote tamanho **não pode** ser maior indefinidamente como os serviços da web de aprendizado de máquina requer o tamanho de carga de uma solicitação seja 4 MB ou menor tempo limite de solicitações de serviço web após 100 segundos de operação.
2. Usando a segunda opção, o tamanho do lote for deixado 1000, com 200 MS latência de serviço da web, cada 20 conexões simultâneas ao serviço da web poderão processar 1000 eventos de *20* 5 = 100,000 por segundo. Portanto, para processar 1.000.000 eventos por segundo, o trabalho seria necessário 60 SUs. Em comparação com a primeira opção, o trabalho de análise de fluxo tornaria mais solicitações de lote serviço da web, por sua vez gerando um aumento de custo.

A seguir é uma tabela de produtividade do trabalho a análise de fluxo para SUs diferentes e tamanhos de lote (em número de eventos por segundo).

| tamanho do lote (latência ML)  | 500 (200 ms) | 1.000 (200 ms) | 5.000 (250ms) | 10.000 (300 ms) | 25.000 (500 ms) |
|--------|-------------------------|---------------|---------------|----------------|----------------|
| **1 SO** | 2.500 | 5.000 | 20.000 | 30.000 | 50.000 |
| **3 SUs** | 2.500 | 5.000 | 20.000 | 30.000 | 50.000 |
| **6 SUs** | 2.500 | 5.000 | 20.000 | 30.000 | 50.000 |
| **12 SUs** | 5.000 | 10.000 | 40.000 | 60.000 | 100,000 |
| **18 SUs** | 7.500 | 15.000 | 60.000 | 90.000 | 150.000 |
| **24 SUs** | 10.000 | 20.000 | 80.000 | 120.000 | 200.000 |
| **…** | … | … | … | … | … |
| **60 SUs** | 25.000 | 50.000 | 200.000 | 300.000 | 500.000 |

Agora, você já deve ter uma boa compreensão de como funções de aprendizado de máquina em análise de fluxo de trabalho. Você provavelmente também compreender que cada "recepção" e dados de análise de fluxo trabalhos "recepção" de fontes de dados retorna um lote de eventos para o trabalho de análise de fluxo processar. Como esse impacto de modelo de pegar o aprendizado de máquina web a solicitações de serviço?

Normalmente, o tamanho do lote que definimos para funções de aprendizado de máquina não exatamente será divisível pelo número de eventos retornados por cada análise de fluxo de trabalho "recepção". Quando isso ocorre, que o serviço da web de aprendizado de máquina será chamado com lotes "parciais". Isso é feito para não provoca sobrecarga de latência de trabalho adicional de eventos unindo pegar a recepção.

## <a name="new-function-related-monitoring-metrics"></a>Novas métricas de monitoramento relacionados à função

Na área Monitor de um trabalho de análise de fluxo, três métricas relacionadas à função adicionais foram adicionadas. Eles são solicitações de função, eventos de função e solicitações de função falha, conforme mostrado na imagem abaixo.

![Dimensionar a análise de fluxo com métricas de funções de aprendizado de máquina] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Dimensionar a análise de fluxo com métricas de funções de aprendizado de máquina")

São definidos da seguinte maneira:

**Função solicitações**: O número de solicitações de função.

**Função eventos**: os eventos de número nas solicitações de função.

**Solicitações de função falhou**: O número de solicitações de função falha.

## <a name="key-takeaways"></a>Principais argumentos  

Para resumir os principais pontos, para dimensionar um trabalho de análise de fluxo com funções de aprendizado de máquina, os seguintes itens devem ser considerados:

1.  A taxa de eventos de entrada
2.  A latência tolerated para o trabalho em execução de análises de fluxo (e, portanto, o tamanho do lote das solicitações de serviço web de aprendizado de máquina)
3.  O SUs de análise de fluxo provisionado e o número de solicitações de serviço web de aprendizado de máquina (os custos de relacionados a função adicionais)

Uma consulta de análise de fluxo totalmente particionada foi usada como um exemplo. Se for necessária uma consulta mais complexa o [Fórum de análise de fluxo de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) é um ótimo recurso para obter ajuda adicional com a equipe de análise de fluxo de.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a análise de fluxo, consulte:

- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
