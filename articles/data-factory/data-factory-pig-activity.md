<properties 
    pageTitle="Atividade de porco" 
    description="Saiba como você pode usar a atividade de porco em uma fábrica de dados do Azure para executar scripts de porco em um cluster de HDInsight próprio em – demanda/sua." 
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
    ms.date="08/31/2016" 
    ms.author="shlo"/>

# <a name="pig-activity"></a>Atividade de porco
> [AZURE.SELECTOR]
[Seção](data-factory-hive-activity.md)  
[Porco](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Fluxo de Hadoop](data-factory-hadoop-streaming-activity.md)
[Aprendizado de máquina](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimento armazenado](data-factory-stored-proc-activity.md)
[Dados Lucerne Analytics U-SQL](data-factory-usql-activity.md)
[.NET personalizado](data-factory-use-custom-activities.md)

A atividade de HDInsight porco em um Data Factory [pipeline](data-factory-create-pipelines.md) executa consultas de porco em [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster baseado no Windows/Linux HDInsight. Este artigo se baseia o artigo de [atividades de transformação de dados](data-factory-data-transformation-activities.md) , que apresenta uma visão geral de transformação de dados e as atividades de transformação com suporte.

## <a name="syntax"></a>Sintaxe

    {
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "Pig Activity",
                "description": "description",
                "type": "HDInsightPig",
                "inputs": [
                    {
                        "name": "input tables"
                    }
                ],
                "outputs": [
                    {
                        "name": "output tables"
                    }
                ],
                "linkedServiceName": "MyHDInsightLinkedService",
                "typeProperties": {
                    "script": "Pig script",
                    "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                    "defines": {
                        "param1": "param1Value"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                }
            }
        ]
      }
    }

## <a name="syntax-details"></a>Detalhes de sintaxe

Propriedade | Descrição | Necessário
-------- | ----------- | --------
nome | Nome da atividade | Sim
Descrição | Texto que descreve a atividade para que serve | Não
tipo | HDinsightPig | Sim
entradas | Uma ou mais entradas consumidas pela atividade porco | Não
saídas | Uma ou mais saídas produzidas pela atividade porco | Sim
linkedServiceName | Referência ao cluster HDInsight registrado como um serviço vinculado em fábrica de dados | Sim
script | Especificar o embutido de script porco | Não
caminho de script | Armazene o script porco em um armazenamento de blob do Microsoft Azure e forneça o caminho para o arquivo. Use a propriedade 'script' ou 'scriptPath'. Não podem ser usados em conjunto. O nome do arquivo diferencia maiusculas de minúsculas. | Não
Define | Especificar parâmetros como pares chave/valor para referência dentro do script de porco | Não

## <a name="example"></a>Exemplo

Vamos considerar um exemplo de jogos logs analytics onde você deseja identificar o tempo gasto por jogadores jogos iniciado pela sua empresa.
 
O seguinte log de jogo de amostra é um arquivo separado por vírgula (,). Ele contém os seguintes campos – ProfileID, SessionStart, duração, SrcIPAddress e GameType.

    1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
    1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
    1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
    1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
    .....

O **script de porco** para processar dados:

    PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    
    GroupProfile = Group PigSampleIn all;
    
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    
    Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');

Para executar este script porco em um pipeline de fábrica de dados, faça o seguinte:

1. Crie um serviço vinculado para registrar [sua próprias HDInsight cluster de computação](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurar [cluster de computação sob demanda HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vamos chamar esse serviço vinculado **HDInsightLinkedService**.
2.  Crie um [serviço vinculado](data-factory-azure-blob-connector.md) para configurar a conexão ao armazenamento de Blob do Azure hospedam os dados. Vamos chamar esse serviço vinculado **StorageLinkedService**.
3.  Crie [conjuntos de dados](data-factory-create-datasets.md) apontando para a entrada e os dados de saída. Vamos chamar do dataset de entrada **PigSampleIn** e o conjunto de dados de saída **PigSampleOut**.
4.  Copie a consulta porco em um arquivo de armazenamento de Blob do Azure configurado na etapa 2 #. Se o armazenamento do Azure que hospeda os dados é diferente daquela que hospeda o arquivo de consulta, crie um serviço de armazenamento do Azure vinculado separado. Consulte o serviço vinculado na configuração de atividade. Use **scriptPath **para especificar o caminho para o arquivo de script porco e **scriptLinkedService**. 
    
    > [AZURE.NOTE] Você também pode fornecer o embutido de script porco na definição da atividade usando a propriedade de **script** . Entretanto, nós não recomendamos essa abordagem como todos os caracteres especiais nas necessidades de script de escape e pode causar problemas de depuração. A prática recomendada é siga a etapa 4 #.
5. Crie o pipeline com a atividade de HDInsightPig. Esta atividade processa os dados de entrada executando o script de porco em cluster HDInsight.

        {
          "name": "PigActivitySamplePipeline",
          "properties": {
            "activities": [
              {
                "name": "PigActivitySample",
                "type": "HDInsightPig",
                "inputs": [
                  {
                    "name": "PigSampleIn"
                  }
                ],
                "outputs": [
                  {
                    "name": "PigSampleOut"
                  }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                  "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
                  "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                }
              }
            ]
          }
        } 
6. Implante o pipeline. Consulte o artigo [criar canais](data-factory-create-pipelines.md) para obter detalhes. 
7. Monitore o pipeline usando as exibições de monitoramento e gerenciamento de fábrica dados. Consulte [monitoramento e gerenciar dados fábrica canais](data-factory-monitor-manage-pipelines.md) artigo para obter detalhes.

## <a name="specifying-parameters-for-a-pig-script"></a>Especificar parâmetros para um script de porco 

Considere o seguinte exemplo: logs de jogos são incluídos diariamente para o armazenamento de Blob do Azure e armazenados em uma pasta particionada com base na data e hora. Você deseja parametrizar o script porco e passar o local de pasta de entrada dinamicamente durante a execução e também produzir a saída particionada com data e hora.
 
Para usar o script de porco parametrizada, faça o seguinte:

- Defina os parâmetros de **define**.

        {
            "name": "PigActivitySamplePipeline",
            "properties": {
            "activities": [
                {
                    "name": "PigActivitySample",
                    "type": "HDInsightPig",
                    "inputs": [
                        {
                            "name": "PigSampleIn"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "PigSampleOut"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "typeproperties": {
                        "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0: %M}/dayno={0: %d}/',SliceStart)",
                            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
                        }
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    }
                }
            ]
          }
        }  
      
- No Script porco, consulte os parâmetros usando '**$parameterName**', conforme mostrado no exemplo a seguir:

        PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);   
        GroupProfile = Group PigSampleIn all;       
        PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);      
        Store PigSampleOut into '$Output' USING PigStorage (','); 


## <a name="see-also"></a>Consulte também
- [Atividade de seção](data-factory-hive-activity.md)
- [Atividade de MapReduce](data-factory-map-reduce.md)
- [Atividade de Streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
- [Chamar Spark programas](data-factory-spark.md)
- [Chamar scripts de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


