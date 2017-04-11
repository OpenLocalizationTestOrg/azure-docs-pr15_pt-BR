<properties 
    pageTitle="Aplicativo de aprendizado de máquina: serviço de detecção de anomalias | Microsoft Azure" 
    description="API de detecção de anomalias é um exemplo construído com aprendizado de máquina do Microsoft Azure que detecta problemas em dados da série de tempo com valores numéricos que estiverem uniformemente espaçados no tempo." 
    services="machine-learning" 
    documentationCenter="" 
    authors="alokkirpal" 
    manager="jhubbard"
    editor="cgronlun" /> 

<tags 
    ms.service="machine-learning" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="multiple" 
    ms.date="10/11/2016" 
    ms.author="alokkirpal"/>


# <a name="machine-learning-anomaly-detection-service"></a>Serviço de detecção de anomalias de aprendizado de máquina#

##<a name="overview"></a>Visão geral

[API de detecção de anomalias](https://datamarket.azure.com/dataset/aml_labs/anomalydetection) é um exemplo construído com aprendizado de máquina do Azure que detecta problemas em dados da série de tempo com valores numéricos que estiverem uniformemente espaçados no tempo. 

Esta API pode detectar os seguintes tipos de padrões anômalos nos dados da série de tempo:

* **Positivos e negativos tendências**: por exemplo, quando monitorar o uso de memória em computação uma tendência para cima pode ser de interesse como ele pode estar indicativo de perda de memória,

* **Alterações no intervalo dinâmico de valores**: por exemplo, ao monitorar as exceções geradas por um serviço de nuvem, quaisquer alterações no intervalo dinâmico de valores podem indicar instabilidade na integridade do serviço, e

* **Altos e baixos**: por exemplo, ao monitorar o número de falhas de login em um serviço ou o número de check-outs em um site de comércio, picos ou queda pode indicar comportamento anormal.

Estas detectores de aprendizado de máquina acompanhar essas alterações nos valores ao longo do tempo e relatório de alterações em andamento em seus valores como pontuações anomalia. Eles não exigem adhoc limite ajuste e suas pontuações podem ser usadas para controlar os falsos positivos. A detecção de anomalia API é útil em várias situações como o monitoramento de serviço controlando KPIs ao longo do tempo, uso monitoramento por meio de métricas como o número de pesquisas, números de cliques, monitoramento do desempenho através de contadores como memória, CPU, arquivo lê etc ao longo do tempo.

A oferta de detecção de anomalias vem com ferramentas úteis para você começar. 

* O [aplicativo web](http://anomalydetection-aml.azurewebsites.net/) ajuda você a avaliar e visualizar os resultados da detecção de anomalias APIs em seus dados. 

* O [código de exemplo](http://adresultparser.codeplex.com/) mostra como acessar a API e analisar os resultados em c# programaticamente.

>[AZURE.NOTE]
>Tente **solução de obtenção de informações de detecção de TI** da plataforma [essa API](https://datamarket.azure.com/dataset/aml_labs/anomalydetection)
>
>Para obter esta solução de ponta a ponta implantada à sua assinatura do Azure <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank"> **Comece aqui >**</a>


##<a name="api-definition"></a>Definição de API

O serviço fornece uma API baseada em REST por HTTPS que pode ser consumida de maneiras diferentes, incluindo uma web ou aplicativo móvel, R, Python, Excel, etc.  Você enviar seus dados de série de tempo para esse serviço por meio de uma chamada de API REST e ele executa uma combinação dos tipos de três anomalia descrito acima. O serviço é executado na plataforma de aprendizado de máquina do Azure, que escalas perfeitamente às suas necessidades de negócios e fornece SLAs.

###<a name="headers"></a>Cabeçalhos
Certifique-se de que você incluir os cabeçalhos corretos em sua solicitação, que deve ser a seguinte:

    Authorization: Basic <creds>
    Accept: application/json
               
    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Você pode encontrar sua chave da conta da sua conta no [Mercado de dados do Azure](https://datamarket.azure.com/account/keys). 

###<a name="score-api"></a>API de pontuação

A API de pontuação é usada para executar a detecção de anomalia nos dados da série de tempo não sazonal. A API executa um número de detectores anomalia nos dados e retorna suas pontuações anomalia. A figura a seguir mostra um exemplo de anomalias que a API de pontuação pode detectar. Esta série de tempo tem 2 mudanças em níveis distintas e 3 picos. Os pontos vermelhos mostram a hora em que a alteração de nível for detectada, enquanto os pontos pretos mostram os picos detectados.

![API de pontuação][1]
    
**URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/Score 

**Corpo da solicitação de exemplo**

Na solicitação de abaixo, podemos enviar uma série de tempo (mostrada truncada) com pontos de dados de 1/3/2016 por meio de 3/10/2016 e parâmetros de detectores especial à API para detectar se qualquer um desses pontos de dados são anômalos. 

    {
      "data": [
        [ "9/21/2014 11:05:00 AM", "3" ],
        [ "9/21/2014 11:10:00 AM", "9.09" ],
        [ "9/21/2014 11:15:00 AM", "0" ]
      ],
      "params": {
        "tspikedetector.sensitivity": "3",
        "zspikedetector.sensitivity": "3",
        "trenddetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "postprocess.tailRows": "2"
      }
    }

A resposta a esta será: 

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
      "ADOutput":"{
        "ColumnNames":["Time","Data","TSpike","ZSpike","rpscore","rpalert","tscore","talert"],
        "ColumnTypes":["DateTime","Double","Double","Double","Double","Int32","Double","Int32"],
        "Values":[
          ["9/21/2014 11:10:00 AM","9.09","0","0","-1.07030497733224","0","-0.884548154298423","0"],
          ["9/21/2014 11:15:00 AM","0","0","0","-1.05186237440962","0","-1.173800281031","0"]
        ]
      }"
    }

###<a name="scorewithseasonality-api"></a>ScoreWithSeasonality API

A API ScoreWithSeasonality é usada para executar a detecção de anomalias na série de tempo que têm padrões de sazonais. Essa API é útil para detectar desvios em padrões sazonais.  

A figura a seguir mostra um exemplo de anomalias detectado em uma série de tempo sazonal. A série temporal tem um especial (o ponto 1º preto), dois queda (o ponto preto 2a e um no final) e um alterar nível (ponto vermelho). Observe que os dois o dip no meio da série de tempo e a alteração de nível só são discernível depois sazonais componentes são removidas da série.

![Sazonalidade API][2]

**URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/ScoreWithSeasonality 

**Corpo da solicitação de exemplo**

Na solicitação de abaixo, podemos enviar uma série de tempo (mostrada truncada) com parâmetros e pontos de dados de 1/3/2016 por meio de 3/10/2016 à API para detectar se qualquer um desses pontos de dados são anômalos. 

    {
      "data": [
        [ "9/21/2014 11:10:00 AM", "9" ],
        [ "9/21/2014 11:15:00 AM", "12" ],
        [ "9/21/2014 11:20:00 AM", "10" ]
      ],
      "params": {
        "preprocess.aggregationInterval": "0",
        "preprocess.aggregationFunc": "mean",
        "preprocess.replaceMissing": "lkv",
        "postprocess.tailRows": "1",
        "zspikedetector.sensitivity": "3",
        "tspikedetector.sensitivity": "3",
        "upleveldetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "trenddetector.sensitivity": "3.25",
        "detectors.historywindow": "500",
        "seasonality.enable": "true",
        "seasonality.transform": "deseason",
        "seasonality.numSeasonality": "1"
      }
    }

A resposta a esta será: 

    {
        "odata.metadata": "https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
        "ADOutput": "{
            "ColumnNames":["Time","OriginalData","ProcessedData","TSpike","ZSpike","PScore","PAlert","RPScore","RPAlert","TScore","TAlert"],
            "ColumnTypes":["DateTime","Double","Double","Double","Double","Double","Int32","Double","Int32","Double","Int32"],
            "Values":[
                ["9/21/201411: 20: 00AM","10","10","0","0","-1.30229513613974","0","-1.30229513613974","0","-1.173800281031","0"]
            ]
        }"
    }

###<a name="detectors"></a>Detectores

A detecção de anomalias API suporta detectores em 3 grandes categorias. Para obter detalhes sobre parâmetros de entrada específicos e saídas para cada detector podem ser encontradas na tabela a seguir.

|Categoria de detector|Detector|Descrição|Parâmetros de entrada|Saídas
|---|---|---|---|---|
|Detectores de AutoTexto especial|Detector de TSpike|Detectar picos e queda com base em muito os valores que são de primeira e a terceira Quartis|*tspikedetector.sensitivity:* leva valor inteiro no intervalo padrão de 1 a 10,: 3; Valores mais altos capturará mais valores radicais tornando menos confidencial|TSpike: valores binários – '1' se um especial/dip for detectado, '0' caso contrário|
||Detector de ZSpike|Detectar picos e queda com base em quanto os datapoints são da média|*zspikedetector.sensitivity:* levar valor inteiro no intervalo padrão de 1 a 10,: 3; Valores mais altos capturará mais valores extremos, tornando-o menos confidencial|ZSpike: valores binários – '1' se um especial/dip for detectado, '0' caso contrário|
|Detector de tendência lenta|Detector de tendência lenta|Detectar lenta tendência positiva acordo com as a sensibilidade do conjunto|*trenddetector.sensitivity:* limite na classificação detector (padrão: 3,25, 3,25 – 5 é um intervalo razoável para selecionar isso; Quanto maior a menos confidenciais)|TScore: flutuantes número que representa a pontuação de anomalia na tendência|
|Detectores de alteração de nível|Alteração de nível unidirecional Detector|Detectar nível para cima mudar de acordo com as a sensibilidade do conjunto|*upleveldetector.sensitivity:* limite na classificação detector (padrão: 3,25, 3,25 – 5 é um intervalo razoável para selecionar isso; Quanto maior a menos confidenciais)|PScore: flutuantes número que representa a pontuação de anomalia em para cima alteração de nível|
||Detector de alteração de nível de bidirecional|Detectar alteração de nível para cima e para baixo de acordo com a confidencialidade de conjunto|*bileveldetector.sensitivity:* limite na classificação detector (padrão: 3,25, 3,25 – 5 é um intervalo razoável para selecionar isso; Quanto maior a menos confidenciais)|RPScore: flutuantes número anomalia representa pontuação em para cima e para baixo nível alterar

###<a name="parameters"></a>Parâmetros

Informações mais detalhadas sobre esses parâmetros de entrada estão listadas na tabela a seguir:

|Parâmetros de entrada|Descrição|Configuração padrão|Tipo|Intervalo válido|Intervalo sugerido|
|---|---|---|---|---|---|
|preprocess.aggregationInterval|Intervalo de agregação em segundos para agregar entrada série temporal|0 (nenhuma agregação é executada)|número inteiro|0: ignorar agregação, > 0 caso contrário|5 minutos para 1 dia, série temporal dependentes
|preprocess.aggregationFunc|Função usada para agregar dados para o AggregationInterval especificado|Média|enumerados|Média, soma, comprimento|N/D|
|preprocess.replaceMissing|Valores usados para impute dados ausentes|lkv (conhecido último valor)|enumerados|zero, lkv, média|N/D|
|detectors.historyWindow|Histórico (em número de pontos de dados) usado para cálculo de pontuação de anomalia|500|número inteiro|10-2000|Série temporal dependentes|
|upleveldetector.Sensitivity|Sensibilidade para nível para cima alterar detector. |3,25|duplo|Nenhum|3,25-5(Lesser values mean more sensitive)|
|bileveldetector.Sensitivity|Sensibilidade para detector de alteração de nível de bidirecional. |3,25|duplo|Nenhum|3,25-5(Lesser values mean more sensitive)|
|trenddetector.Sensitivity|Sensibilidade para detector de tendência positivo. |3,25|duplo|Nenhum|3,25-5(Lesser values mean more sensitive)|
|tspikedetector.Sensitivity |Sensibilidade para Detector TSpike|3|número inteiro|1-10|3-5(Lesser values mean more sensitive)|
|zspikedetector.Sensitivity |Sensibilidade para Detector ZSpike|3|número inteiro|1-10 |3-5(Lesser values mean more sensitive)|
|seasonality.Enable|Se a análise de sazonalidade é para ser executado|verdadeiro|booliano|verdadeiro, FALSO|Série temporal dependentes|
|seasonality.numSeasonality |Número máximo de ciclos periódicos para ser detectados|1|número inteiro|1, 2|1-2|
|seasonality.Transform |Se sazonal (e) componentes de tendência deverão ser removidos antes de aplicar a detecção de anomalia|deseason|enumerados|Nenhum, deseason, deseasontrend|N/D|
|postprocess.tailRows |Número dos pontos de dados mais recentes sejam mantidas nos resultados da saída|0|número inteiro|0 (manter todos os pontos de dados), ou especificar o número de pontos para manter nos resultados|N/D|

###<a name="output"></a>Saída
API executa detectores de todos os seus dados da série de tempo e retorna pontuações anomalia e indicadores de binário especial para cada ponto no tempo. A tabela a seguir lista saídas da API. 

|Saídas|Descrição|
|---|---|
|Tempo|Carimbos de dados brutos ou dados agregados (e/ou) imputed se agregação (e/ou) ausente imputation de dados é aplicado|
|OriginalData|Valores de dados brutos ou dados agregados (e/ou) imputed se agregação (e/ou) ausente imputation de dados é aplicado|
|ProcessedData|Um dos seguintes: <ul><li>Ajustados periodicamente série temporal se sazonalidade significativa foi detectado e deseason opção selecionada;</li><li>periodicamente ajustada e detrended série temporal se sazonalidade significativa foi detectada e a opção de deseasontrend selecionada</li><li>Caso contrário, esta é a mesma que OriginalData</li>|
|TSpike|Indicador binário para indicar se um pico é detectado pelo Detector TSpike|
|ZSpike|Indicador binário para indicar se um pico é detectado pelo Detector ZSpike|
|Pscore|Um número flutuante que representa a pontuação de anomalia no nível para cima alterar|
|Palert|valor de 1/0 indicando que há um nível para cima alterar anomalias com base na sensibilidade de entrada|
|RPScore|Uma anomalia flutuante de número que representam pontuação na alteração de nível de bidirecional|
|RPAlert|valor de 1/0 indicando que há um nível de bidirecional alterar anomalias com base na sensibilidade de entrada|
|TScore|Uma anomalia flutuante de número que representam pontuação em tendência positiva|
|TAlert|valor de 1/0 indicando lá é uma anomalia tendência positivos com base na sensibilidade de entrada|


Esta saída pode ser analisada usando um [Analisador simple](https://adresultparser.codeplex.com/) - possui código de exemplo que mostra como conectar-se à API e analisar a saída. Os problemas detectados podem ser visualizados em um painel e/ou passados para humanos especialistas para ações corretivas ou integrados ao tíquetes sistemas.


[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-score.png
[2]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-seasonal.png

 

 
