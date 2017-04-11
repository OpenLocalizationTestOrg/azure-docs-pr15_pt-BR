<properties 
    pageTitle="Chamar programa MapReduce da fábrica de dados do Azure" 
    description="Saiba como processar dados executando programas de MapReduce em um cluster de Azurehdinsight de uma fábrica de dados do Azure." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="invoke-mapreduce-programs-from-data-factory"></a>Chamar MapReduce programas da fábrica de dados
> [AZURE.SELECTOR]
[Seção](data-factory-hive-activity.md)  
[Porco](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Fluxo de Hadoop](data-factory-hadoop-streaming-activity.md)
[Aprendizado de máquina](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimento armazenado](data-factory-stored-proc-activity.md)
[Dados Lucerne Analytics U-SQL](data-factory-usql-activity.md)
[.NET personalizado](data-factory-use-custom-activities.md)

A atividade de HDInsight MapReduce em um Data Factory [pipeline](data-factory-create-pipelines.md) executa programas MapReduce em [sua própria](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster baseado no Windows/Linux HDInsight. Este artigo se baseia o artigo de [atividades de transformação de dados](data-factory-data-transformation-activities.md) , que apresenta uma visão geral de transformação de dados e as atividades de transformação com suporte.

## <a name="introduction"></a>Introdução 
Um pipeline em uma fábrica Azure dados processa dados nos serviços do armazenamento vinculado usando os serviços de computação vinculadas. Ele contém uma sequência de atividades onde cada atividade executa uma operação de processamento específico. Este artigo descreve como usar a atividade de MapReduce HDInsight.
 
Consulte [porco](data-factory-pig-activity.md) e a [seção](data-factory-hive-activity.md) para obter detalhes sobre como executar scripts de porco/seção em um cluster baseado no Windows/Linux HDInsight de um pipeline usando atividades HDInsight porco e seção. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON para HDInsight MapReduce atividade 

Na definição de JSON para a atividade de HDInsight: 
 
1. Defina o **tipo** da **atividade** ao **HDInsight**.
3. Especifique o nome da classe de propriedade de **nome da classe** .
4. Especifique o caminho para o arquivo JAR incluindo o nome de arquivo para **jarFilePath** propriedade.
5. Especifica o serviço vinculado que se refere ao armazenamento de Blob do Azure que contém o arquivo JAR **jarLinkedService** propriedade.   
6. Especifique quaisquer argumentos para o programa MapReduce na seção **argumentos** . Durante a execução, você vê alguns argumentos extras (por exemplo: mapreduce.job.tags) do framework MapReduce. Para diferenciar seus argumentos com os argumentos MapReduce, considere usar opção e valor como argumentos, conforme mostrado no exemplo a seguir (- s, - entrada, --saída etc., são imediatamente seguidas por seus valores de opções).

        {
            "name": "MahoutMapReduceSamplePipeline",
            "properties": {
                "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
                "activities": [
                    {
                        "type": "HDInsightMapReduce",
                        "typeProperties": {
                            "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                            "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                            "jarLinkedService": "StorageLinkedService",
                            "arguments": [
                                "-s",
                                "SIMILARITY_LOGLIKELIHOOD",
                                "--input",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                                "--output",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                                "--maxSimilaritiesPerItem",
                                "500",
                                "--tempDir",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                            ]
                        },
                        "inputs": [
                            {
                                "name": "MahoutInput"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "MahoutOutput"
                            }
                        ],
                        "policy": {
                            "timeout": "01:00:00",
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        },
                        "name": "MahoutActivity",
                        "description": "Custom Map Reduce to generate Mahout result",
                        "linkedServiceName": "HDInsightLinkedService"
                    }
                ],
                "start": "2014-01-03T00:00:00Z",
                "end": "2014-01-04T00:00:00Z",
                "isPaused": false,
                "hubName": "mrfactory_hub",
                "pipelineMode": "Scheduled"
            }
        }
    
    

Você pode usar a atividade de MapReduce HDInsight para executar qualquer arquivo de jar MapReduce em um cluster de HDInsight. Na definição a seguir amostra JSON de um canal, a atividade de HDInsight está configurada para executar um arquivo JAR Mahout.

## <a name="sample-on-github"></a>Amostra no GitHub
Você pode baixar um exemplo para usar a atividade de MapReduce HDInsight do: [Exemplos de fábrica de dados no GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Executar o programa de contagem de palavras
O pipeline neste exemplo executa o programa de mapa/redução de contagem de palavras em seu cluster Azurehdinsight.   

### <a name="linked-services"></a>Serviços de vinculadas
Primeiro, crie um serviço vinculado para vincular o armazenamento do Azure que é usado pelo cluster Azurehdinsight à fábrica dados Azure. Se você copiar/colar o código a seguir, não se esqueça de substituir o **nome da conta** e **chave da conta** com o nome e a chave do seu armazenamento do Azure. 

#### <a name="azure-storage-linked-service"></a>Serviço de armazenamento vinculado do Azure

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
            }
        }
    }

#### <a name="azure-hdinsight-linked-service"></a>Serviço do Azure HDInsight vinculado
Em seguida, você cria um serviço vinculado para vincular o seu cluster Azurehdinsight à fábrica dados Azure. Se você copiar/colar o código a seguir, substitua **o nome de cluster HDInsight** com o nome do seu cluster HDInsight e altere os valores de nome e senha do usuário.   

    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }

### <a name="datasets"></a>Conjuntos de dados

#### <a name="output-dataset"></a>Conjunto de dados de saída
O pipeline neste exemplo não terá qualquer entradas. Você pode especificar um conjunto de dados de saída para a atividade de MapReduce HDInsight. Este conjunto de dados é apenas um dataset fictício que é necessária para direcionam o cronograma de pipeline.  

    {
        "name": "MROutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "WordCountOutput1.txt",
                "folderPath": "example/data/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="pipeline"></a>Pipeline
O pipeline neste exemplo tem apenas uma atividade do tipo: HDInsightMapReduce. Algumas das propriedades importantes no JSON são: 

Propriedade | Anotações
:-------- | :-----
tipo | O tipo deve ser definido como **HDInsightMapReduce**. 
nome da classe | Nome da classe é: **wordcount**
jarFilePath | Caminho para o arquivo jar contendo a classe. Se você copiar/colar o código a seguir, não se esqueça de alterar o nome do cluster. 
jarLinkedService | Serviço de armazenamento vinculado Azure que contém o arquivo jar. Esse serviço vinculado refere-se ao armazenamento que está associado ao cluster HDInsight. 
argumentos | O programa wordcount leva dois argumentos, uma entrada e uma saída. O arquivo de entrada é o arquivo de davinci.txt.
frequência/intervalo | Os valores para essas propriedades corresponder o conjunto de dados de saída. 
linkedServiceName | refere-se para o serviço de HDInsight vinculado que você criou anteriormente.   

    {
        "name": "MRSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run the Word Count Program",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "wordcount",
                        "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "/example/data/gutenberg/davinci.txt",
                            "/example/data/WordCountOutput1"
                        ]
                    },
                    "outputs": [
                        {
                            "name": "MROutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "MRActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-03T00:00:00Z",
            "end": "2014-01-04T00:00:00Z"
        }
    }

## <a name="run-spark-programs"></a>Executar programas Spark
Você pode usar MapReduce atividade para executar Spark programas em seu cluster HDInsight Spark. Consulte [programas de invocar Spark da fábrica de dados do Azure](data-factory-spark.md) para obter detalhes.  

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 
## <a name="see-also"></a>Consulte também
- [Atividade de seção](data-factory-hive-activity.md)
- [Atividade de porco](data-factory-pig-activity.md)
- [Atividade de Streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
- [Chamar Spark programas](data-factory-spark.md)
- [Chamar scripts de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
