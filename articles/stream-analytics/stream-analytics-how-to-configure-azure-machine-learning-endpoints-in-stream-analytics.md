<properties 
    pageTitle="Como configurar pontos de extremidade de aprendizado de máquina do Azure no fluxo Analytics | Microsoft Azure" 
    description="Funções definidas pelo usuário de linguagem de máquina no fluxo de análise"
    keywords=""
    documentationCenter=""
    services="stream-analytics"
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
    ms.author="jeffstok"
/>

# <a name="machine-learning-integration-in-stream-analytics"></a>Integração de aprendizado em análise de fluxo de máquina

Análise de fluxo é compatível com as funções definidas pelo usuário que chamar a atenção para pontos de extremidade de aprendizado de máquina do Azure. Suporte a API REST para esse recurso é detalhado na [biblioteca de API de REST de análise do fluxo](https://msdn.microsoft.com/library/azure/dn835031.aspx). Este artigo fornece informações complementares exigia uma implementação bem-sucedida desse recurso em análise de fluxo. Um tutorial também foi lançado e está disponível [aqui](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Visão geral: Terminologia de aprendizado de máquina Azure

Aprendizado de máquina do Microsoft Azure fornece uma ferramenta de colaboração, arrastar e soltar que você pode usar para criar, testar e implantar soluções de análise de previsão em seus dados. Esta ferramenta é chamada do *Azure Studio de aprendizado de máquina*. O studio é usado para interagir com os recursos de aprendizado de máquina e facilmente construir, testar e iteramos sobre o seu design. Esses recursos e suas definições estão abaixo.

- **Espaço de trabalho**: O *espaço de trabalho* é um contêiner que armazena todos os outros recursos de aprendizado de máquina juntos em um contêiner para gerenciamento e controle.
- **Experimento**: *experiências* são criados por cientistas dados utilizar conjuntos de dados e treinar um modelo de aprendizado de máquina.
- **Ponto de extremidade**: *pontos de extremidade* são o objeto de aprendizado de máquina do Azure usado para aproveitar recursos como entrada, aplicar um modelo de aprendizado de máquina especificada e retornar marcou saída.
- **Pontuação Serviçoweb**: um *serviço Web de pontuação* é um conjunto de pontos de extremidade conforme mencionado acima.

Cada ponto de extremidade tem apis para execução de lote e execução síncrona. Análise de fluxo usa execução síncrona. O serviço específico é chamado de um [Serviço de solicitação/resposta](../machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs) no AzureML studio.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Os recursos necessários para trabalhos de análise de fluxo de aprendizado de máquina

Para fins de análise de fluxo de processamento de trabalho, um ponto de extremidade de solicitação/resposta, um [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md#get-an-azure-machine-learning-authorization-key)e uma definição de swagger são todos necessários para execução com êxito. Análise de fluxo tem um ponto de extremidade adicional que constrói a url do ponto de extremidade de swagger, pesquise a interface e retorna uma definição de UDF padrão para o usuário.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Configurar um fluxo de análise e UDF via API REST de aprendizado de máquina

Usando APIs REST, você pode configurar o seu trabalho para chamar funções de linguagem de máquina do Azure. As etapas são da seguinte maneira:

1. Criar uma a análise de fluxo de trabalho
2. Definir uma entrada
3. Definir uma saída
4. Criar uma função definida pelo usuário (UDF)
5. Escrever uma transformação de análise de fluxo que chama o UDF
6. Iniciar o trabalho

## <a name="creating-a-udf-with-basic-properties"></a>Criando um UDF com propriedades básicas

Como exemplo, o código de exemplo a seguir cria uma UDF escalar denominada *newudf* que vincula a um ponto de extremidade de aprendizado de máquina do Azure. Observe que o *ponto de extremidade* (serviço URI) pode ser encontradas na página de Ajuda API do serviço escolhido e os *apiKey* pode ser encontrada na página serviços de principal.

````
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>  
````

Corpo da solicitação de exemplo:  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
````

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Ponto de extremidade de RetrieveDefaultDefinition de chamada para padrão UDF

Depois que o esqueleto UDF é criado a definição completa do UDF é necessária. O ponto de extremidade de RetreiveDefaultDefinition ajuda você a começar a definição padrão para uma função escalar que está vinculada a um ponto de extremidade de aprendizado de máquina do Azure. A carga abaixo requer que você obtenha a definição de UDF padrão para uma função escalar que está vinculada a um ponto de extremidade de aprendizado de máquina do Azure. Ele não especifica o ponto de extremidade real como ele já foi fornecido durante a solicitação de colocar. Análise de fluxo chama o ponto de extremidade fornecido na solicitação se ele for fornecido explicitamente. Caso contrário, ele usa aquele originalmente referenciado. Aqui demorará o UDF uma única string parâmetro (uma sentença) e retorna um único saído do tipo cadeia de caracteres que indica o rótulo "sentimento" para essa frase.

````
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
````

Corpo da solicitação de exemplo:  

````
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
````

Um exemplo de saída deste procure algo gostaria abaixo.  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="patch-udf-with-the-response"></a>Patch UDF com a resposta 

Agora o UDF deve ser corrigido com a resposta anterior, conforme mostrado abaixo.

````
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

Corpo da solicitação (saída de RetrieveDefaultDefinition):

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementar transformação de análise de fluxo para chamar o UDF

Agora o UDF (aqui chamado scoreTweet) de consulta para cada evento de entrada e gravar uma saída de uma resposta para o evento.  

````
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
````


## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [Fórum de análise de fluxo do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
