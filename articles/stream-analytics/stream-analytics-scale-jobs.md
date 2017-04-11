<properties
    pageTitle="Dimensionar trabalhos de análise de fluxo para aumentar a produtividade | Microsoft Azure"
    description="Aprenda a escala trabalhos de análise de fluxo por configurar partições entradas, ajuste a definição de consulta e configurando unidades de fluxo de trabalho."
    keywords="dados streaming, streaming processamento de dados, ajuste a análise"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="scale-azure-stream-analytics-jobs-to-increase-stream-data-processing-throughput"></a>Dimensionar trabalhos de análise de fluxo Azure para aumentar a produtividade de processamento de dados de fluxo

Saiba como ajustar trabalhos de análise e calcular *streaming unidades* para análise de fluxo, como escalar trabalhos de análise de fluxo configurando partições de entrada, ajuste a definição de consulta de análise e definindo unidades de fluxo de trabalho. 

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Quais são as partes de um trabalho de análise de fluxo?
Uma definição de análise de fluxo de trabalho inclui entradas, uma consulta e saída. Entradas são de onde o trabalho lê o fluxo de dados, a consulta é usada para transformar o fluxo de entrada de dados e a saída é onde o trabalho envia os resultados da tarefa.  

Um trabalho requer pelo menos uma fonte de entrada para o fluxo de dados. A fonte de entrada de fluxo de dados pode ser armazenada em um Hub de evento do Azure serviço barramento ou em armazenamento de Blob do Azure. Para obter mais informações, consulte [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md) e [começar a usar a análise de fluxo Azure](stream-analytics-get-started.md).

## <a name="configuring-streaming-units"></a>Configurando unidades de streaming
Unidades de streaming (SUs) representa os recursos e a capacidade de computação necessária para executar um trabalho de análise de fluxo de Azure. SUs oferece uma maneira de descrever o evento relativo com base em uma medida combinada de CPU, memória, de capacidade de processamento e ler e gravar taxas. Cada unidade de streaming corresponde a aproximadamente 1MB/segundo de transferência. 

Escolhendo quantos SUs são necessárias para um determinado trabalho depende da configuração de partição para as entradas e a consulta definida para o trabalho. Você pode selecionar até sua cota de streaming unidades para um trabalho usando o Portal de clássico do Azure. Cada assinatura Azure por padrão tem uma cota de até 50 unidades streaming para todos os trabalhos de análise em uma região específica. Para aumentar o streaming unidades para as suas assinaturas, contate o [Suporte da Microsoft](http://support.microsoft.com).

O número de unidades streaming que um trabalho pode utilizar depende da configuração de partição para as entradas e a consulta definida para o trabalho. Observe também que um valor válido para as unidades de fluxo deve ser usado. Os valores válidos iniciarem em 1, 3, 6 e, em seguida, para cima em incrementos de 6, conforme mostrado abaixo.

![Escala de unidades de fluxo de análise de fluxo Azure][img.stream.analytics.streaming.units.scale]

Este artigo mostrará como calcular e ajustar a consulta para aumentar a produtividade para trabalhos de análise.

## <a name="embarrassingly-parallel-job"></a>Trabalho paralelo
O trabalho paralelo é o cenário mais flexível que temos em análise de fluxo de Azure. Ele se conecta a uma partição de entrada para uma instância da consulta para uma partição de saída. Atingir esse paralelismo requer algumas coisas:

1.  Se sua lógica de consulta depende da mesma chave que estão sendo processada pela mesma instância de consulta, você deve garantir que os eventos ir para a mesma partição de sua entrada. No caso de Hubs de evento, isso significa que os dados de evento precisam ter **PartitionKey** definido ou você pode usar remetentes particionadas. Para Blob, isso significa que os eventos são enviados para a mesma pasta de partição. Se sua lógica de consulta não exige a mesma chave sejam processados pela mesma instância de consulta, você pode ignorar esse requisito. Um exemplo disso seria uma consulta de filtro/de projeto de selecione simples.  
2.  Depois que os dados são dispostos como ela precisa estar no lado de entrada, precisamos garantir que sua consulta é particionada. Isso requer que você use **Partição por** em todas as etapas. Várias etapas são permitidas, mas todos eles devem ser particionados pela mesma chave. Outra coisa observar é que, atualmente, a chave de partição precisa ser definida como **PartitionId** para ter um trabalho totalmente paralelo.  
3.  Somente os Hubs de evento e Blob suportam particionada de saída. Para saída de Hubs de evento, você precisa configurar o campo **PartitionKey** para ser **PartitionId**. BLOB, você não precisa fazer mais nada.  
4.  Outra coisa observar, o número de partições entradas deve ser igual o número de partições de saída. Saída do blob atualmente não dá suporte a partições, mas isso é okey porque ele herdarão o esquema de partição da consulta upstream. Exemplos de valores de partição que permitem a um trabalho totalmente paralelo:  
    1.  8 Hubs de evento entrada partições e partições de saída de 8 Hubs de evento
    2.  8 Hubs de evento entrada partições e saída de Blob  
    3.  8 partições de blob entradas e saída de Blob  
    4.  8 partições de entrada de blob e 8 Hubs de evento partições de saída  

Aqui estão alguns cenários de exemplo que são paralelos.

### <a name="simple-query"></a>Consulta simples
Hub de eventos de entrada – Hubs de evento com 8 partições saída – com 8 partições

**Consulta:**

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

Essa consulta é um filtro simples e assim, não precisamos preocupar partição a entrada que enviamos para Hubs de evento. Você notará que a consulta tem **Partição por** de **PartitionId**, por isso, podemos atender requisito #2 acima. Para a saída, precisamos configurar a saída de Hubs de evento no trabalho para que o campo **PartitionKey** definido como **PartitionId**. Uma última verificação, partições entradas = = partições de saída. Essa topologia é paralela.

### <a name="query-with-grouping-key"></a>Consulta com chave de agrupamento
Entrada – Hubs de evento com 8 partições saída – Blob

**Consulta:**

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Esta consulta tem uma chave de agrupamento e assim, a mesma chave precisa ser processada pela mesma instância de consulta. Isso significa que precisamos enviar nossos eventos para eventos Hubs de maneira particionada. Qual tecla podemos preocupar? **PartitionId** é um conceito de lógica de trabalho, a chave real que nos interessa é **TollBoothId**. Isso significa que deve ser definido a **PartitionKey** dos dados do evento enviamos para Hubs de evento seja o **TollBoothId** do evento. A consulta tem **Partição por** de **PartitionId**, então estamos boas lá. Para a saída, pois é Blob, podemos não precisa se preocupar sobre a configuração **PartitionKey**. Para requisito #4, novamente, isso é Blob, então nós não precisa se preocupar com isso. Essa topologia é paralela.

### <a name="multi-step-query-with-grouping-key"></a>Consulta de etapa de multi com chave de agrupamento ###
Hub de eventos de entrada – Hub de evento com 8 partições saída – com 8 partições

**Consulta:**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Esta consulta tem uma chave de agrupamento e assim, a mesma chave precisa ser processada pela mesma instância de consulta. Podemos usar a mesma estratégia como a consulta anterior. A consulta tem várias etapas. Cada etapa tem **Partição por** de **PartitionId**? Sim, então não podemos boas. Para a saída, precisamos para definir a **PartitionKey** **PartitionId** como discutido acima e também pode ver que ela tem o mesmo número de partições como a entrada. Essa topologia é paralela.


## <a name="example-scenarios-that-are-not-embarrassingly-parallel"></a>Cenários de exemplo que não são paralelo

### <a name="mismatched-partition-count"></a>Contagem de partição incompatível ###
Hub de eventos de entrada – Hubs de evento com 8 partições saída – com 32 partições

Não importa o que a consulta é nesse caso porque a entrada partição contagem! = contagem de partição de saída.

### <a name="not-using-event-hubs-or-blobs-as-output"></a>Não usando Hubs de evento ou Blobs como saída
Entrada – Hubs de evento com 8 partições saída – PowerBI

Saída do PowerBI atualmente não dá suporte a partição.

### <a name="multi-step-query-with-different-partition-by-values"></a>Consulta de etapa de multi com valores de partição por diferentes
Hub de eventos de entrada – Hub de evento com 8 partições saída – com 8 partições

**Consulta:**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    
Como você pode ver, a segunda etapa usa **TollBoothId** como a chave de partição. Isso não é a mesma que a primeira etapa e, portanto, exigirão que podemos fazer uma ordem aleatória. 

Estes são alguns exemplos e counterexamples de trabalhos de análise de fluxo que poderão alcançar uma topologia paralela e com ele a possibilidade de escala máxima. Para trabalhos que não correspondam a um desses perfis, atualizações futuras detalha como maximally escalar alguns dos outros cenários fluxo Analytics canônicos serão feitas.

Para fazer agora usar a orientação geral abaixo:

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Calcular o máximo streaming unidades de um trabalho
O número total de unidades de streaming que podem ser usados por um trabalho de análise de fluxo depende do número de etapas da consulta definida para o trabalho e o número de partições para cada etapa.

### <a name="steps-in-a-query"></a>Etapas em uma consulta
Uma consulta pode ter uma ou várias etapas. Cada etapa é uma subconsulta definida usando a palavra-chave **WITH** . A consulta somente que estiver fora a palavra-chave **WITH** também é contada como uma etapa, por exemplo, a instrução **SELECT** na consulta a seguir:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

A consulta anterior tem duas etapas.

> [AZURE.NOTE] Este exemplo de consulta será explicado posteriormente neste artigo.

### <a name="partition-a-step"></a>Partição uma etapa

Partição uma etapa requer as seguintes condições:

- A fonte de entrada deve ser particionada. Para obter mais informações, consulte o [Guia de programação de Hubs de evento](../event-hubs/event-hubs-programming-guide.md).
- A instrução **SELECT** da consulta deve ler a partir de uma fonte de entrada particionada.
- A consulta dentro da etapa deve ter a palavra-chave **Partição por**

Quando uma consulta é particionada, os eventos de entrada será partição separada processados e agregados em grupos e eventos de saídas são gerados para cada um dos grupos. Se uma agregação combinada é desejável, você deve criar uma segunda etapa não particionados para agregar.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Calcular max streaming unidades para um trabalho

Todas as etapas não particionados juntos podem dimensionar até seis unidades streaming para um trabalho de análise de fluxo. Para adicionar adicionais de streaming unidades, uma etapa deve ser particionada. Cada partição pode ter seis unidades streaming.

<table border="1">
<tr><th>Consulta de um trabalho</th><th>Max streaming unidades para o trabalho</th></td>

<tr><td>
<ul>
<li>A consulta contém uma etapa.</li>
<li>A etapa não está particionada.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>O fluxo de dados de entrada é dividido pela 3.</li>
<li>A consulta contém uma etapa.</li>
<li>A etapa é particionada.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>A consulta contém duas etapas.</li>
<li>Nenhuma das etapas é particionada.</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>A entrada de fluxo de dados é dividida pela 3.</li>
<li>A consulta contém duas etapas. Na etapa de entrada é particionada e a segunda etapa não é.</li>
<li>A instrução SELECT lê a partir da entrada particionada.</li>
</ul>
</td>
<td>24 (18 para etapas particionadas) + 6 para obter etapas não particionado</td></tr>
</table>

### <a name="examples-of-scale"></a>Exemplos de escala
A consulta a seguir calcula o número de carros percorrer uma estação de Chamada Tarifada com três tollbooths dentro de uma janela de três minutos. Essa consulta pode ser dimensionada até seis unidades de streaming.

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Para usar mais unidades de streaming a consulta, os dois o fluxo de dados entrada e a consulta deve ser particionada. Considerando que a partição de fluxo de dados é definida como 3, a seguinte consulta modificada pode ser dimensionada até 18 unidades streaming:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Quando uma consulta é particionada, os eventos de entrada são processados e agregados em grupos de partição separada. Eventos de saída também são gerados para cada um dos grupos. Partição pode causar alguns resultados inesperados quando o campo **Agrupar por** não é a chave de partição na entrada de fluxo de dados. Por exemplo, o campo **TollBoothId** na consulta de exemplo anterior não é a chave de partição de Input1. Os dados do pedágio #1 podem ser distribuídos em várias partições.

Cada uma das partições Input1 será processada separadamente pelo fluxo Analytics e vários registros da contagem de carro-passagem para o mesmo pedágio na mesma janela queda serão criados. Se a chave da partição de entrada não pode ser alterada, esse problema pode ser corrigido adicionando uma etapa não partição adicional, por exemplo:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Essa consulta pode ser dimensionada para 24 streaming unidades.

>[AZURE.NOTE] Se você estiver ingressando em dois fluxos, certifique-se de que os fluxos são particionados pela chave partição da coluna que você faça junções e você tiver o mesmo número de partições em ambos os fluxos.


## <a name="configure-stream-analytics-job-partition"></a>Configurar a análise de fluxo partição de trabalho

**Para ajustar uma unidade de streaming para um trabalho**

1. Entrar no [portal de gerenciamento](https://manage.windowsazure.com).
2. No painel esquerdo, clique em **Análise de fluxo** .
3. Clique no trabalho de análise de fluxo que você deseja dimensionar.
4. Clique em **escala** na parte superior da página.

![Escala de unidades de fluxo de análise de fluxo Azure][img.stream.analytics.streaming.units.scale]

No Portal do Azure, configurações de escala podem ser acessadas em configurações:

![Configuração do trabalho de análise de fluxo de Portal Azure][img.stream.analytics.preview.portal.settings.scale]

## <a name="monitor-job-performance"></a>Monitorar o desempenho do trabalho

Usando o portal de gerenciamento, você pode controlar a taxa de transferência de um trabalho em eventos/segundo:

![A análise de fluxo Azure monitorar trabalhos][img.stream.analytics.monitor.job]

Calcule a taxa de transferência esperada da carga de trabalho em eventos/segundo. Se a taxa de transferência for menor que o esperado, ajuste a partição de entrada, ajustar a consulta e adicionar unidades streaming adicionais para seu trabalho.

## <a name="stream-analytics-throughput-at-scale---raspberry-pi-scenario"></a>Fluxo Analytics produtividade em escala - cenário de framboesa Pi


Para compreender como os trabalhos de análise de fluxo dimensionar em um cenário típico em termos de produtividade de processamento em várias unidades de Streaming, aqui está um experimento que envia dados (clientes) hub de evento, processa e envia o alerta ou estatísticas como uma saída para outro Hub de evento.

O cliente está enviando dados sintetizada para Hubs de evento no formato JSON para a análise de fluxo e a saída de dados também está no formato JSON.  Veja aqui como os dados de exemplo ficaria semelhante –  

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

Consulta: "enviar um alerta quando light é desligada"  

    SELECT AVG(lght),
     “LightOff” as AlertText
    FROM input TIMESTAMP
    BY devicetime
     WHERE
        lght< 0.05 GROUP BY TumblingWindow(second, 1)

Medir a produtividade: Produtividade neste contexto é a quantidade de dados de entrada processados pelo fluxo Analytics em um determinado período de tempo (10 minutos). Para obter melhor taxa de transferência de processamento para os dados de entrada, os dois o fluxo de dados entrada e a consulta deve ser particionada. Também **Count ()**é incluída na consulta para medir processados quantos eventos de entrada. Para garantir que o trabalho não está aguardando simplesmente para eventos de entrada chegar, cada partição do Hub evento entrada foi pré-carregado com dados de entrada suficientes (cerca de 300MB).  

Abaixo estão os resultados com aumento do número de unidades de Streaming e contagens de partição correspondente em Hubs de evento.  

<table border="1">
<tr><th>Partições de entrada</th><th>Partições de saída</th><th>Unidades de streaming</th><th>Produtividade contínua
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06 MB/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06 MB/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32 MB/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67 MB/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27 MB/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69 MB/s</td>
</tr>
</table>

![IMG.Stream.Analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [Fórum de análise de fluxo de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 
