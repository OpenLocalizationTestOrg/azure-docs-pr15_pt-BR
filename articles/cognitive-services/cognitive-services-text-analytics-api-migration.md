<properties
    pageTitle="Atualização para a versão 2 a análise de texto API | Microsoft Azure"
    description="Análise de texto - atualização para a versão 2 de aprendizado de máquina Azure"
    services="cognitive-services"
    documentationCenter=""
    authors="onewth"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="onewth"/>

# <a name="upgrading-to-version-2-of-the-text-analytics-api"></a>Atualização para a análise de texto API a versão 2 #

Este guia o guiará pelo processo de atualizar seu código de uso da [primeira versão da API](../machine-learning/machine-learning-apps-text-analytics.md) usando a segunda versão. 

Se você não tiver usado a API e gostaria de saber mais, você pode **[saber mais sobre a API aqui](//go.microsoft.com/fwlink/?LinkID=759711)** ou **[siga o Quick Start Guide](//go.microsoft.com/fwlink/?LinkID=760860)**. Para referência técnica, consulte a **[Definição de API](//go.microsoft.com/fwlink/?LinkID=759346)**.

### <a name="part-1-get-a-new-key"></a>Parte 1. Obter uma nova chave ###

Primeiro, você precisará obter uma nova chave de API do **Azure Portal**:

1. Navegue até o serviço de análise de texto por meio da [Galeria de inteligência de Cortana](//gallery.cortanaintelligence.com/MachineLearningAPI/Text-Analytics-2). Aqui, você também encontrará links para as amostras de código e documentação.

1. Clique em **inscrever-se**. Este link levará você para o portal de gerenciamento do Azure, onde você pode se inscrever no serviço.

1. Selecione um plano. Você pode selecionar o **nível gratuito para 5.000 transações/mês**. Assim como um plano gratuito, você não será cobrado por usar o serviço. Você precisará fazer o login à sua assinatura do Azure. 

1. Depois de você se inscrever para a análise de texto, você terá uma **Chave API**. Copie esta tecla, como ele será necessário ao usar os serviços de API.

### <a name="part-2-update-the-headers"></a>Parte 2. Atualizar os cabeçalhos ###

Atualize os valores de cabeçalho enviado conforme mostrado abaixo. Observe que a chave da conta já não está codificada.

**Versão 1**

    Authorization: Basic base64encode(<your Data Market account key>)
    Accept: application/json

**Versão 2**

    Content-Type: application/json
    Accept: application/json
    Ocp-Apim-Subscription-Key: <your Azure Portal account key>


### <a name="part-3-update-the-base-url"></a>Parte 3. Atualizar a URL base ###

**Versão 1**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/

**Versão 2**

    https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/

### <a name="part-4a-update-the-formats-for-sentiment-key-phrases-and-languages"></a>Parte 4a. Atualizar os formatos de sentimento, palavras-chave e idiomas ###

#### <a name="endpoints"></a>Pontos de extremidade ####

OBTER pontos de extremidade tiveram sido substituídos, portanto, todas as entradas devem ser enviada como uma solicitação de POSTAGEM. Atualize os pontos de extremidade para aqueles que mostrado abaixo.

| |Único ponto de extremidade de versão 1|Ponto de extremidade de lote de versão 1|Ponto de extremidade de versão 2|
|---|---|---|---|
|Tipo de chamada|Obter|Postar|Postar|
|Sentimento|```GetSentiment```|```GetSentimentBatch```|```sentiment```|
|Palavras-chave|```GetKeyPhrases```|```GetKeyPhrasesBatch```|```keyPhrases```|
|Idiomas|```GetLanguage```|```GetLanguageBatch```|```languages```|

#### <a name="input-formats"></a>Formatos de entrada ####

Observação que esse formato de POSTAGEM somente agora é aceito, portanto você deve reformatar qualquer entrada que anteriormente usado os pontos de extremidade de único documento adequadamente. Entradas não diferenciam maiusculas de minúsculas.

**Versão 1 (em lotes)**

    {
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Versão 2**

    {
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="output-from-sentiment"></a>Saída do sentimento ####

**Versão 1**

    {
      "SentimentBatch":[{
        "Id":"string",
        "Score":"double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versão 2**

    {
      "documents":[{
        "id":"string",
        "score":"double"
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-key-phrases"></a>Saída de palavras-chave ####

**Versão 1**

    {
      "KeyPhrasesBatch":[{
        "Id":"string",
        "KeyPhrases":["string"]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versão 2**

    {
      "documents":[{
        "id":"string",
        "keyPhrases":["string"]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-languages"></a>Saída de idiomas ####


**Versão 1**

    {
      "LanguageBatch":[{
        "id":"string",
        "detectedLanguages": [{
          "Score":"double"
          "Name":"string",
          "Iso6391Name":"string"
        }]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versão 2**

    {
      "documents":[{
        "id":"string",
        "detectedLanguages": [{
          "score":"double"
          "name":"string",
          "iso6391Name":"string"
        }]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }


### <a name="part-4b-update-the-formats-for-topics"></a>Parte 4b. Atualizar os formatos de tópicos ###

#### <a name="endpoints"></a>Pontos de extremidade ####

| |Ponto de extremidade de versão 1 | Ponto de extremidade de versão 2|
|---|---|---|
|Enviar para a detecção de tópico (POST)|```StartTopicDetection```|```topics```|
|Buscar os resultados de tópico (GET)|```GetTopicDetectionResult?JobId=<jobId>```|```operations/<operationId>```|

#### <a name="input-formats"></a>Formatos de entrada ####

**Versão 1**

    {
      "StopWords": [
        "string"
      ],
      "StopPhrases": [
        "string"
      ], 
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Versão 2**

    {
      "stopWords": [
        "string"
      ],
      "stopPhrases": [
        "string"
      ],
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="submission-results"></a>Resultados de envio ####

**Versão 1 (POST)**

Anteriormente, quando o trabalho concluído, você deve receber a seguinte saída JSON, onde a jobId poderia ser anexada a uma URL para buscar a saída.

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

**Versão 2 (POST)**

A resposta agora incluirá um valor de cabeçalho da seguinte maneira, onde `operation-location` é usado como o ponto de extremidade para pesquisar para os resultados:

    'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

#### <a name="operation-results"></a>Resultados da operação ####

**Versão 1 (GET)**

    {
      "TopicInfo" : [{
        "TopicId" : "string"
        "Score" : "double"
        "KeyPhrase" : "string"
      }],
      "TopicAssignment" : [{
        "Id" : "string",
        "TopicId" : "string",
        "Distance" : "double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versão 2 (GET)**

Como antes, **pesquisam periodicamente a saída** (período sugerido é cada minuto) até que a saída será retornado. 

Quando a API de tópicos terminou, uma leitura de status `succeeded` será retornado. Isso incluirão os resultados de saída no formato mostrado abaixo:

    {
        "status": "succeeded",
        "createdDateTime": "string",
        "operationType": "topics",
        "processingResult": {
            "topics" : [{
            "id" : "string"
            "score" : "double"
            "keyPhrase" : "string"
          }],
          "topicAssignments" : [{
            "topicId" : "string",
            "documentId" : "string",
            "distance" : "double"
          }],
          "errors" : [{
              "id":"string",
              "message":"string"
          }]
        }
    }

### <a name="part-5-test-it"></a>Parte 5. Testá-lo! ###

Agora você deve estar pronto! Teste seu código com uma pequena amostra para garantir que você possa processar com êxito seus dados.
