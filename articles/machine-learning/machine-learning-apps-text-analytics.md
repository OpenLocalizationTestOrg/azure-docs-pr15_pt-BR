<properties
    pageTitle="APIs de aprendizado de máquina: Análise de texto | Microsoft Azure"
    description="APIs de Analytics texto do aprendizado de máquina da Microsoft pode ser usado para analisar o texto não estruturado para análise de sentimento, extração de chave frase, a detecção de idioma e a detecção de tópico."
    services="machine-learning"
    documentationCenter=""
    authors="onewth"
    manager="jhubbard"
    editor="cgronlun"/> 

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="onewth"/>


# <a name="machine-learning-apis-text-analytics-for-sentiment-key-phrase-extraction-language-detection-and-topic-detection"></a>APIs de aprendizado de máquina: Análise de texto para sentimento, chave extração de frase, a detecção de idioma e a detecção de tópico

>[AZURE.NOTE] Este guia destina-se a versão 1 da API. Para versão 2, [**consulte este documento**](../cognitive-services/cognitive-services-text-analytics-quick-start.md). Versão 2 agora é a versão preferencial desta API.

## <a name="overview"></a>Visão geral

A API de análise de texto é um conjunto de texto analytics [serviços web](https://datamarket.azure.com/dataset/amla/text-analytics) criados com o aprendizado de máquina do Azure. A API pode ser usada para analisar o texto não estruturado para tarefas como análise sentimento, extração de chave frase, a detecção de idioma e a detecção de tópico. Nenhum dado de treinamento é necessária para usar essa API: basta trazer seus dados de texto. Essa API usa processamento técnicas avançadas do idioma natural para fornecer os melhores em previsões de classe.

Você pode ver a análise de texto em ação em nosso [site de demonstração](https://text-analytics-demo.azurewebsites.net/), onde você também encontrará [exemplos](https://text-analytics-demo.azurewebsites.net/Home/SampleCode) sobre como implementar a análise de texto em c# e Python.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 

---

## <a name="sentiment-analysis"></a>Análise de sentimento

A API retorna uma pontuação numérica entre 0 e 1. Classificações próximo 1 indicam sentimento positivo, enquanto a pontuação próximo 0 indica sentimento negativo. Pontuação sentimento é gerada usando técnicas de classificação. Os recursos de entrada para o classificador incluem n-gramas, recursos gerados de marcas de parte de fala e objetos incorporados do word. Atualmente, inglês é a única linguagem com suporte.
 
## <a name="key-phrase-extraction"></a>Extração de expressão-chave

A API retorna uma lista de strings indicando os principais pontos de discussão no texto da entrada. Nós utilizam técnicas de ferramentas de processamento de linguagem Natural sofisticadas do Microsoft Office. Atualmente, inglês é a única linguagem com suporte.

## <a name="language-detection"></a>Detecção de idioma

A API retorna o idioma detectado e uma pontuação numérica entre 0 e 1. Classificações próximo 1 indicam certeza de 100% que o idioma identificado é verdadeiro. Um total de 120 idiomas são suportados.

## <a name="topic-detection"></a>Detecção de tópico

Esta é uma API lançada recentemente que retorna o início detectado tópicos para uma lista de enviado registros de texto. Um tópico é identificado com uma chave frase, que pode ser um ou mais palavras relacionadas. Esta API exige um mínimo de 100 registros de texto a ser enviado, mas foi projetado para detectar tópicos por centenas a milhares de registros. Observe que essa API cobra 1 transação por registro de texto enviada. A API foi projetada para funcionar bem em breve, humano texto escrito como revisões e comentários do usuário.

---

## <a name="api-definition"></a>Definição de API

### <a name="headers"></a>Cabeçalhos

Certifique-se de que você incluir os cabeçalhos corretos em sua solicitação, que deve ser a seguinte:

    Authorization: Basic <creds>
    Accept: application/json
               
    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Você pode encontrar sua chave da conta da sua conta no [Mercado de dados do Azure](https://datamarket.azure.com/account/keys). Observe que no momento, somente JSON é aceito para formatos de entrada e saídos. Não há suporte para XML.

---

## <a name="single-response-apis"></a>APIs única resposta

### <a name="getsentiment"></a>GetSentiment

**URL** 

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Exemplo de solicitação**

Na chamada abaixo, solicitamos análise sentimento para a frase "Hello World":

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Isso retornará uma resposta da seguinte maneira:

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
        "Score":1.0
    }

---

### <a name="getkeyphrases"></a>GetKeyPhrases

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Exemplo de solicitação**

Na chamada abaixo, solicitamos que as frases-chave encontrados no texto "Era um hotel maravilhoso para ficar, com casa exclusiva e equipe amigável":

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
    Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Isso retornará uma resposta da seguinte maneira:

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
      "KeyPhrases":[
        "wonderful hotel",
        "unique decor",
        "friendly staff"
      ]
    }
 
---

### <a name="getlanguage"></a>GetLanguage

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**Exemplo de solicitação**

Na chamada GET abaixo, podemos está solicitando o sentimento para as frases-chave no texto *Olá, mundo*

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
    Text=Hello+World

Isso retornará uma resposta da seguinte maneira:

    {
      "UnknownLanguage": false,
      "DetectedLanguages": [{
        "Name": "English",
        "Iso6391Name": "en",
        "Score": 1.0
      }]
    }

**Parâmetros opcionais**

`NumberOfLanguagesToDetect`é um parâmetro opcional. O padrão é 1.

---

## <a name="batch-apis"></a>APIs de lote

O serviço de análise de texto permite que você faça sentimento e extrações de chave frase no modo em lotes. Observe que cada um dos registros totalizados contagens como uma transação. Como exemplo, se você solicitar sentimento para 1000 registros em uma única chamada, transações de 1000 serão deduzidas.

Observe que as identificações inseridas no sistema são as IDs retornadas pelo sistema. O serviço da web não verifica que essas IDs são exclusivos. É responsabilidade do chamador para verificar exclusividade. 


### <a name="getsentimentbatch"></a>GetSentimentBatch

**URL** 

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Exemplo de solicitação**

Durante a chamada POST abaixo, solicitamos para as opiniões das frases "Olá, mundo", "Hello Foo World" e "Hello meu World" no corpo da solicitação:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Corpo da solicitação:

    {"Inputs":
    [
        {"Id":"1","Text":"hello world"},
        {"Id":"2","Text":"hello foo world"},
        {"Id":"3","Text":"hello my world"},
    ]}

Na resposta abaixo, você obtém a lista de resultados associada ao seu texto Ids:

    {
      "odata.metadata":"<url>", 
      "SentimentBatch":
      [
        {"Score":0.9549767,"Id":"1"},
        {"Score":0.7767222,"Id":"2"},
        {"Score":0.8988889,"Id":"3"}
      ],  
      "Errors":[]
    }


---

### <a name="getkeyphrasesbatch"></a>GetKeyPhrasesBatch

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Exemplo de solicitação**

Neste exemplo, solicitamos para a lista de opiniões para as frases-chave em textos os seguintes: 

* "Era um hotel maravilhoso para ficar, com casa exclusiva e amigável equipe"
* "Era uma conferência de compilação incríveis, com muito interessante fala"
* "O tráfego era terrível, eu gasto três horas indo para o aeroporto"

Essa solicitação é feita como uma chamada de POSTAGEM para o ponto de extremidade:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Corpo da solicitação:

    {"Inputs":
    [
        {"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
        {"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
        {"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
    ]}

Na resposta abaixo, você obtém a lista de palavras-chave associado ao seu texto Ids:

    { "odata.metadata":"<url>",
        "KeyPhrasesBatch":
        [
           {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
           {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
           {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
        ],
        "Errors":[]
    }

---

### GetLanguageBatch

In the POST call below, we are requesting language detection for two text inputs:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

Request body:

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

This returns the following response, where English is detected in the first input and French in the second input:

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "Inglês",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "Francês",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

---

## <a name="topic-detection-apis"></a>APIs de detecção de tópico

Esta é uma API lançada recentemente que retorna o início detectado tópicos para uma lista de enviado registros de texto. Um tópico é identificado com uma chave frase, que pode ser um ou mais palavras relacionadas. Observe que essa API cobra 1 transação por registro de texto enviada.

Esta API exige um mínimo de 100 registros de texto a ser enviado, mas foi projetado para detectar tópicos por centenas a milhares de registros.


### <a name="topics--submit-job"></a>Tópicos – enviar trabalho

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection

**Exemplo de solicitação**


Durante a chamada POST abaixo, solicitamos tópicos para um conjunto de 100 artigos, onde os artigos de entrada primeiro e últimos são mostrados e dois StopPhrases estão incluídas.

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection HTTP/1.1

Corpo da solicitação:

    {"Inputs":[
        {"Id":"1","Text":"I loved the food at this restaurant"},
        ...,
        {"Id":"100","Text":"I hated the decor"}
    ],
    "StopPhrases":[
        "restaurant", “visitor"
    ]}

Na resposta abaixo, você pode obter a JobId para o trabalho enviado:

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

Uma lista de palavra única ou várias frases do word que não devem ser retornados como tópicos. Pode ser usado para filtrar tópicos muito genéricos. Por exemplo, em um conjunto de dados sobre avaliações de hotel, "hotel" e "hostel" pode ser parada sensata frases.  

### <a name="topics--poll-for-job-results"></a>Tópicos – sondagem para resultados de trabalho

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult

**Exemplo de solicitação**

Passe o JobId retornado da etapa 'Enviar trabalho' para buscar os resultados. Recomendamos que você chamar esse ponto de extremidade cada minuto até Status = 'Concluída' na resposta. Levará cerca de 10 minutos para um trabalho concluída ou mais para trabalhos com milhares de registros.

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult?JobId=<JobId>


Enquanto ele está sendo processada, a resposta será da seguinte maneira:

    {
        "odata.metadata":"<url>",
        "Status":"Running",
        "TopicInfo":[],
        "TopicAssignment":[],
        "Errors":[]
    }


A API retorna o resultado no formato JSON no seguinte formato:

    {
        "odata.metadata":"<url>",
        "Status":"Finished",
        "TopicInfo":[
        {
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Score":8.0,
            "KeyPhrase":"food"
        },
        ...
        {
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Score":6.0,
            "KeyPhrase":"decor"
            }
        ],
        "TopicAssignment":[
        {
            "Id":"1",
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Distance":0.7809
        },
        ...
        {
            "Id":"100",
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Distance":0.8034
        }
        ],
        "Errors":[]


As propriedades para cada parte da resposta são da seguinte maneira:

**Propriedades de TopicInfo**

| Chave | Descrição |
|:-----|:----|
| TopicId | Um identificador exclusivo para cada tópico. |
| Pontuação | Contagem de registros atribuída ao tópico. |
| KeyPhrase | Uma palavra ou frase para o tópico de resumir. Pode ser 1 ou várias palavras. |

**Propriedades de TopicAssignment**

| Chave | Descrição |
|:-----|:----|
| ID | Identificador do registro. Equivale à identificação incluída na entrada. |
| TopicId | A identificação de tópico que o registro foi atribuído ao. |
| Distância | Confiança que o registro pertence ao tópico. Distância mais próximo a zero indica confiança maior. |
