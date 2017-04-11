<properties 
    pageTitle="Atividade de Streaming de Hadoop" 
    description="Saiba como você pode usar a atividade de Streaming de Hadoop em uma fábrica de dados do Azure para executar programas Hadoop Streaming em um cluster de HDInsight próprio em – demanda/sua." 
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
    ms.date="09/20/2016" 
    ms.author="shlo"/>

# <a name="hadoop-streaming-activity"></a>Atividade de Streaming de Hadoop
> [AZURE.SELECTOR]
[Seção](data-factory-hive-activity.md)  
[Porco](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Fluxo de Hadoop](data-factory-hadoop-streaming-activity.md)
[Aprendizado de máquina](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimento armazenado](data-factory-stored-proc-activity.md)
[Dados Lucerne Analytics U-SQL](data-factory-usql-activity.md)
[.NET personalizado](data-factory-use-custom-activities.md)

Você pode usar a atividade de HDInsightStreamingActivity invocar um trabalho Hadoop Streaming de um pipeline de fábrica de dados do Azure. O trecho JSON a seguir mostra a sintaxe para usar o HDInsightStreamingActivity em um arquivo JSON pipeline. 

A atividade de Streaming de HDInsight em um Data Factory [pipeline](data-factory-create-pipelines.md) executa programas Hadoop Streaming em [sua própria](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster baseado no Windows/Linux HDInsight. Este artigo se baseia o artigo de [atividades de transformação de dados](data-factory-data-transformation-activities.md) , que apresenta uma visão geral de transformação de dados e as atividades de transformação com suporte.

## <a name="json-sample"></a>Amostra JSON
Cluster HDInsight é preenchido automaticamente com dados (davinci.txt) e programas de exemplo (wc.exe e cat.exe). Por padrão, o nome do contêiner que é usado pelo cluster HDInsight é o nome do próprio cluster. Por exemplo, se o nome do seu cluster for myhdicluster, o nome do contêiner de blob associado seria myhdicluster. 

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<nameofthecluster>/example/apps/wc.exe",
                            "<nameofthecluster>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService",
                        "getDebugInfo": "Failure"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

Observe os seguintes pontos:

1. Defina o **linkedServiceName** para o nome do serviço vinculado que aponta para o seu cluster HDInsight no qual o trabalho mapreduce streaming é executado.
2. Defina o tipo da atividade para **HDInsightStreaming**.
3. Para a propriedade **mapeador** , especifique o nome do mapper executável. No exemplo, cat.exe é o mapeador executável.
4. Para a propriedade **Redutor** , especifique o nome do Redutor executável. No exemplo, wc.exe é o Redutor executável.
5. Para a propriedade de tipo de **entrada** , especifique o arquivo de entrada (incluindo o local) para o mapeador. No exemplo: "wasb://adfsample@ <account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt ": adfsample é o contêiner de blob, dados/exemplo/Gutenberg é a pasta e davinci.txt é o blob.
6. Para a propriedade de tipo de **saída** , especifique o arquivo de saída (incluindo o local) para o Redutor. A saída do trabalho Hadoop Streaming escrita para o local especificado para esta propriedade.
7. Na seção **filePaths** , especifique os caminhos para os executáveis mapeador e Redutor. No exemplo: "adfsample/example/apps/wc.exe", adfsample é o contêiner de blob, exemplo/aplicativos é a pasta e wc.exe é o arquivo executável.
8. Para a propriedade **fileLinkedService** , especifique o serviço de armazenamento do Azure vinculado que representa o armazenamento do Azure que contém os arquivos especificados na seção filePaths.
9. Para a propriedade de **argumentos** , especifique os argumentos para o fluxo de trabalho.
10. A propriedade **getDebugInfo** é um elemento opcional. Quando ela é definida como falha, os logs são baixados somente em caso de falha. Quando ela está definida como todos, logs sempre são baixados independentemente do status de execução.

> [AZURE.NOTE] Como mostrado no exemplo, especifique um conjunto de dados de saída para a atividade de Streaming de Hadoop para a propriedade **saídas** . Este conjunto de dados é apenas um dataset fictício que é necessária para direcionam o cronograma de pipeline. Você não precisa especificar qualquer conjunto de dados de entrada para a atividade para a propriedade de **entradas** .  

    
## <a name="example"></a>Exemplo
O pipeline nesta explicação executa o programa de mapa/redução de streaming de contagem de palavras em seu cluster Azurehdinsight. 

### <a name="linked-services"></a>Serviços de vinculadas

#### <a name="azure-storage-linked-service"></a>Serviço de armazenamento vinculado do Azure
Primeiro, crie um serviço vinculado para vincular o armazenamento do Azure que é usado pelo cluster Azurehdinsight à fábrica dados Azure. Se você copiar/colar o código a seguir, não se esqueça de substituir o nome da conta e chave da conta com o nome e a chave do seu armazenamento do Azure. 

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
Em seguida, você cria um serviço vinculado para vincular o seu cluster Azurehdinsight à fábrica dados Azure. Se você copiar/colar o código a seguir, substitua o nome de cluster HDInsight com o nome do seu cluster HDInsight e altere os valores de nome e senha do usuário. 
    
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
O pipeline neste exemplo não terá qualquer entradas. Você pode especificar um conjunto de dados de saída para a atividade de Streaming de HDInsight. Este conjunto de dados é apenas um dataset fictício que é necessária para direcionam o cronograma de pipeline. 

    {
        "name": "StreamingOutputDataset",
        "properties": {
            "published": false,
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/streamingdata/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                },
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="pipeline"></a>Pipeline

O pipeline neste exemplo tem apenas uma atividade do tipo: **HDInsightStreaming**. 

Cluster HDInsight é preenchido automaticamente com dados (davinci.txt) e programas de exemplo (wc.exe e cat.exe). Por padrão, o nome do contêiner que é usado pelo cluster HDInsight é o nome do próprio cluster. Por exemplo, se o nome do seu cluster for myhdicluster, o nome do contêiner de blob associado seria myhdicluster.  

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<blobcontainer>/example/apps/wc.exe",
                            "<blobcontainer>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

## <a name="see-also"></a>Consulte também
- [Atividade de seção](data-factory-hive-activity.md)
- [Atividade de porco](data-factory-pig-activity.md)
- [Atividade de MapReduce](data-factory-map-reduce.md)
- [Chamar Spark programas](data-factory-spark.md)
- [Chamar scripts de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

