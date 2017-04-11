<properties
    pageTitle="Guia de início rápido: APIs de análise de texto de aprendizado de máquina | Microsoft Azure"
    description="Análise de texto - guia de início rápido de aprendizado de máquina Azure"
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

# <a name="getting-started-with-the-text-analytics-apis-to-detect-sentiment-key-phrases-topics-and-language"></a>Guia de Introdução com as APIs de análise de texto para detectar sentimento, palavras-chave, tópicos e idioma

<a name="HOLTop"></a>

Este documento descreve como integrado ao seu serviço ou o aplicativo para usar as [APIs de análise de texto](//go.microsoft.com/fwlink/?LinkID=759711).
Você pode usar essas APIs para detectar sentimento, palavras-chave, tópicos e idioma do texto. [Clique aqui para ver uma demonstração interativa da experiência.](//go.microsoft.com/fwlink/?LinkID=759712)

Consulte as [definições de API](//go.microsoft.com/fwlink/?LinkID=759346) para documentação técnica para as APIs.

Este guia destina-se a versão 2 das APIs. Para obter detalhes sobre a versão 1 das APIs, [consulte este documento](../machine-learning/machine-learning-apps-text-analytics.md).

No final deste tutorial, você poderá programaticamente detectar:

- **Sentimento** - é texto positivo ou negativo?

- **Frases de chave** - quais são as pessoas abordando em um único artigo?

- **Tópicos** - quais são as pessoas abordando em muitos artigos?

- **Idiomas** - o que é linguagem texto escrito em?

Observe que essa API cobra 1 transação por documento enviado. Como exemplo, se você solicitar sentimento para documentos de 1000 em uma única chamada, transações de 1000 serão deduzidas.



<a name="Overview"></a>
## <a name="general-overview"></a>Visão geral ##

Este documento é um guia passo a passo. Nosso objetivo é orientá-lo pelas etapas necessárias para treinar um modelo e ponto para os recursos que permitirá que você colocá-lo em produção. Neste exercício levará cerca de 30 minutos.

Para essas tarefas, você precisa de um editor e ligue os pontos de extremidade RESTful no idioma de sua escolha.

Vamos começar!

## <a name="task-1---signing-up-for-the-text-analytics-apis"></a>Tarefa 1: inscrever as APIs de análise de texto ####

Nesta tarefa, você irá se inscrever no serviço de análise de texto.

1. Navegue até **Serviços cognitivas** no [Portal do Azure](//go.microsoft.com/fwlink/?LinkId=761108) e certifique-se de que **A análise de texto** está selecionado como o 'tipo de API'.

1. Selecione um plano. Você pode selecionar o **nível gratuito para 5.000 transações/mês**. Assim como um plano gratuito, você não será cobrado por usar o serviço. Você precisará fazer o login à sua assinatura do Azure. 

1. Preencha os outros campos e criar sua conta.

1. Depois de você se inscrever para a análise de texto, encontre sua **Chave API**. Copie a chave primária, como você precisará ao usar os serviços de API.


## <a name="task-2---detect-sentiment-key-phrases-and-languages"></a>Tarefa 2 - detectar sentimento, palavras-chave e idiomas ####

É fácil detectar sentimento, palavras-chave e idiomas no seu texto. Você obterá os mesmos resultados programaticamente como a [experiência de demonstração](//go.microsoft.com/fwlink/?LinkID=759712) retorna.

>[AZURE.TIP] Para análise de sentimento, recomendamos que você dividir o texto em frases. Isso geralmente leva a uma maior precisão em previsões sentimento.

Observe que os idiomas com suporte são da seguinte maneira:

| Recurso | Códigos de idioma com suporte |
|:-----|:----|
| Sentimento | `en`(Em inglês), `es` (espanhol), `fr` (francês), `pt` (português) |
| Palavras-chave | `en`(Em inglês), `es` (espanhol), `de` (alemão), `ja` (japonês) |


1. Você precisará definir os cabeçalhos à seguinte. Observe que JSON atualmente é o formato de entrada aceito somente para as APIs. Não há suporte para XML.

        Ocp-Apim-Subscription-Key: <your API key>
        Content-Type: application/json
        Accept: application/json

1. Em seguida, formate suas linhas de entrada em JSON. Para sentimento, palavras-chave e idioma, o formato é a mesma. Observe que cada ID deve ser exclusivo e será a identificação retornada pelo sistema. O tamanho máximo de um único documento que pode ser enviado é 10KB e o tamanho máximo total de entrada enviado é 1MB. Não mais de 1.000 documentos podem ser enviados em uma chamada. A limitação de taxa existe a uma taxa de 100 chamadas por minuto - portanto, recomendamos que você enviar grandes quantidades de documentos em uma única chamada. Idioma é um parâmetro opcional que deve ser especificado se Analisando texto de inglês. Um exemplo de entrada é mostrado abaixo, onde o parâmetro opcional `language` para sentimento extração de frase análise ou tecla está incluída:

        {
            "documents": [
                {
                    "language": "en",
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "language": "en",
                    "id": "100",
                    "text": "Final document"
                }
            ]
        }

1. Fazer uma chamada **POST** no sistema com a entrada de sentimento, palavras-chave e idioma. As URLs ficará assim:

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages

1. Essa chamada retornará um JSON formatado resposta com as identificações e propriedades detectadas. Um exemplo de saída para sentimento é mostrado abaixo (com detalhes do erro excluídos). No caso de sentimento, será retornada uma pontuação entre 0 e 1 para cada documento:

        // Sentiment response
        {
            "documents": [
                {
                    "id": "1",
                    "score": "0.934"
                },
                ...
                {
                    "id": "100",
                    "score": "0.002"
                },
            ]
        }

        // Key phrases response
        {
            "documents": [
                {
                    "id": "1",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
                ...
                {
                    "id": "100",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
            ]
        }

        // Languages response
        {
            "documents": [
                {
                    "id": "1",
                    "detectedLanguages": [
                        {
                            "name": "English",
                            "iso6391Name": "en",
                            "score": "1"
                        }
                    ]
                },
                ...
                {
                    "id": "100",
                    "detectedLanguages": [
                        {
                            "name": "French",
                            "iso6391Name": "fr",
                            "score": "0.985"
                        }
                    ]
                }
            ]
        }


## <a name="task-3---detect-topics-in-a-corpus-of-text"></a>Tarefa 3 - detectar tópicos em um corpo de texto ####

Esta é uma API lançada recentemente que retorna o início detectado tópicos para uma lista de enviado registros de texto. Um tópico é identificado com uma chave frase, que pode ser um ou mais palavras relacionadas. A API foi projetada para funcionar bem em breve, humano texto escrito como revisões e comentários do usuário.

Esta API requer **um mínimo de 100 registros de texto** a ser enviada, mas foi projetado para detectar tópicos por centenas a milhares de registros. Quaisquer registros do inglês ou com menos de 3 palavras serão descartadas e, portanto, não será atribuída aos tópicos. Para detecção de tópico, o tamanho máximo de um único documento que pode ser enviado é 30KB e o tamanho máximo total de entrada enviado é 30MB. Detecção de tópico é taxa limitada a 5 envios cada 5 minutos.

Há dois parâmetros de entrada adicionais **opcionais** que podem ajudar a melhorar a qualidade dos resultados:

- **Pare de palavras.**  Estas palavras e seus formulários fechar (por exemplo, plurais) serão excluídos do pipeline de detecção de todo o tópico. Use isso para palavras comuns (por exemplo, "problema", "erro" e "usuário" podem ser as opções adequadas reclamações do cliente sobre software). Cada cadeia de caracteres deve ser uma única palavra.
- **Parar de frases** - essas frases serão excluídos da lista de tópicos retornados. Use este comando para excluir tópicos genéricos que você não deseja ver nos resultados. Por exemplo, "Microsoft" e "Azure" seria tópicos excluir as opções adequadas. Cadeias de caracteres podem conter várias palavras.

Siga estas etapas para detectar tópicos em seu texto.

1. Formate a entrada em JSON. Desta vez, você pode definir palavras de parada e parar frases.

        {
            "documents": [
                {
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "id": "100",
                    "text": "Final document"
                }
            ],
            "stopWords": [
                "issue", "error", "user"
            ],
            "stopPhrases": [
                "Microsoft", "Azure"
            ]
        }

1. Usando os mesmos cabeçalhos, conforme definido na tarefa 2, faça uma **POSTAGEM** ligar para o ponto de extremidade de tópicos:

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/topics

1. Isso retornará um `operation-location` como o cabeçalho na resposta, onde o valor é a URL para consultar os tópicos resultantes:

        'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

1. Consultar retornado `operation-location` periodicamente com uma solicitação **GET** . Uma vez por minuto é recomendado.

        GET https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>

1. O ponto de extremidade retornará uma resposta, incluindo `{"status": "notstarted"}` antes do processamento, `{"status": "running"}` durante o processamento e `{"status": "succeeded"}` com a saída concluída. Em seguida, você pode consumir a saída que será no seguinte formato (Observação detalhes como datas e formato de erro foram excluídas neste exemplo):

        {
            "status": "succeeded",
            "operationProcessingResult": {
                "topics": [
                    {
                        "id": "8b89dd7e-de2b-4a48-94c0-8e7844265196"
                        "score": "5"
                        "keyPhrase": "first topic name"
                    },
                    ...
                    {
                        "id": "359ed9cb-f793-4168-9cde-cd63d24e0d6d"
                        "score": "3"
                        "keyPhrase": "final topic name"
                    }
                ],
                "topicAssignments": [
                    {
                        "topicId": "8b89dd7e-de2b-4a48-94c0-8e7844265196",
                        "documentId": "1",
                        "distance": "0.354"
                    },
                    ...
                    {
                        "topicId": "359ed9cb-f793-4168-9cde-cd63d24e0d6d",
                        "documentId": "55",
                        "distance": "0.758"
                    },            
                ]
            }
        }

Observe que a resposta bem-sucedida tópicos do `operations` ponto de extremidade terá o esquema a seguir:

    {
            "topics" : [{
                "id" : "string",
                "score" : "number",
                "keyPhrase" : "string"
            }],
            "topicAssignments" : [{
                "documentId" : "string",
                "topicId" : "string",
                "distance" : "number"
            }],
            "errors" : [{
                "id" : "string",
                "message" : "string"
            }]
        }

Explicações sobre cada parte dessa resposta é da seguinte maneira:

**tópicos**

| Chave | Descrição |
|:-----|:----|
| ID | Um identificador exclusivo para cada tópico. |
| pontuação | Contagem de documentos atribuída ao tópico. |
| keyPhrase | Uma palavra ou frase para o tópico de resumir. |

**topicAssignments**

| Chave | Descrição |
|:-----|:----|
| documentId | Identificador do documento. Equivale à identificação incluída na entrada. |
| topicId | A identificação de tópico que o documento foi atribuído ao. |
| distância | Pontuação de associação de tópico para documento entre 0 e 1. Quanto menor a distância pontuação é de associação o tópico mais forte. |

**erros**

| Chave | Descrição |
|:-----|:----|
| ID | Identificador exclusivo do documento de entrada o erro se refere a. |
| Mensagem | Mensagem de erro. |

## <a name="next-steps"></a>Próximas etapas ##

Parabéns! Agora você concluiu usando a análise de texto em seus dados. Agora, você talvez queira examinar usando uma ferramenta como [Power BI](//powerbi.microsoft.com) para visualizar seus dados, bem como automatizar ideias para dar uma visualização em tempo real dos seus dados de texto.

Para ver como os recursos de análise de texto, como o sentimento, podem ser usados como parte de um bot, consulte o exemplo de [Bot emocional](http://docs.botframework.com/en-us/bot-intelligence/language/#example-emotional-bot) no site Bot Framework.
