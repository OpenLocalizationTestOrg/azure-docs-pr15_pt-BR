<properties 
    pageTitle="Atividade de seção" 
    description="Saiba como você pode usar a atividade de seção em uma fábrica de dados do Azure para executar consultas de seção em um cluster de HDInsight próprio em – demanda/sua." 
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
    ms.date="10/11/2016" 
    ms.author="shlo"/>

# <a name="hive-activity"></a>Atividade de seção
> [AZURE.SELECTOR]
[Seção](data-factory-hive-activity.md)  
[Porco](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Fluxo de Hadoop](data-factory-hadoop-streaming-activity.md)
[Aprendizado de máquina](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimento armazenado](data-factory-stored-proc-activity.md)
[Dados Lucerne Analytics U-SQL](data-factory-usql-activity.md)
[.NET personalizado](data-factory-use-custom-activities.md)

A atividade de HDInsight seção em um Data Factory [pipeline](data-factory-create-pipelines.md) executa consultas de seção em [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster baseado no Windows/Linux HDInsight. Este artigo se baseia o artigo de [atividades de transformação de dados](data-factory-data-transformation-activities.md) , que apresenta uma visão geral de transformação de dados e as atividades de transformação com suporte.

## <a name="syntax"></a>Sintaxe

    {
        "name": "Hive Activity",
        "description": "description",
        "type": "HDInsightHive",
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
          "script": "Hive script",
          "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
       "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
    }
    
## <a name="syntax-details"></a>Detalhes de sintaxe

Propriedade | Descrição | Necessário
-------- | ----------- | --------
nome | Nome da atividade | Sim
Descrição | Texto que descreve a atividade para que serve | Não
tipo | HDinsightHive | Sim
entradas | Entradas consumidas pela atividade de seção | Não
saídas | Saídas produzidas pela atividade de seção | Sim 
linkedServiceName | Referência ao cluster HDInsight registrado como um serviço vinculado em fábrica de dados | Sim 
script | Especifique o seção script embutido | Não
caminho de script | Armazenar o script de seção em um armazenamento de blob do Microsoft Azure e forneça o caminho para o arquivo. Use a propriedade 'script' ou 'scriptPath'. Não podem ser usados em conjunto. O nome do arquivo diferencia maiusculas de minúsculas. | Não 
Define | Especificar parâmetros como pares chave/valor para referência dentro do script de seção usando 'hiveconf'  | Não

## <a name="example"></a>Exemplo

Vamos considerar um exemplo de jogos logs analytics onde você deseja identificar o tempo gasto pelos usuários jogos iniciado pela sua empresa. 

O seguinte log é um log de jogo de amostra, vírgula (`,`) separados e contém os seguintes campos – ProfileID, SessionStart, duração, SrcIPAddress e GameType.

    1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
    1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
    1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
    1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
    .....

A **seção de script** para processar dados:

    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID       string, 
        SessionStart    string, 
        Duration        int, 
        SrcIPAddress    string, 
        GameType        string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 
    
    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (   
        ProfileID   string, 
        Duration    int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';
    
    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID

Para executar este script de seção em um pipeline de fábrica de dados, você precisa fazer o seguinte

1. Crie um serviço vinculado para registrar [sua próprias HDInsight cluster de computação](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurar [cluster de computação sob demanda HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vamos chamar esse serviço vinculado "HDInsightLinkedService".
2. Crie um [serviço vinculado](data-factory-azure-blob-connector.md) para configurar a conexão ao armazenamento de Blob do Azure hospedam os dados. Vamos chamar esse serviço vinculado "StorageLinkedService"
3. Crie [conjuntos de dados](data-factory-create-datasets.md) apontando para a entrada e os dados de saída. Vamos chamar do dataset de entrada "HiveSampleIn" e o dataset de saída "HiveSampleOut"
4. Copiar a consulta de seção como um arquivo para o armazenamento de Blob do Azure configurado na etapa 2 #. Se o armazenamento para os dados de hospedagem for diferente do que o arquivo de consulta de hospedagem, crie um serviço de armazenamento do Azure vinculado separado e se referir a ela na atividade. Use **scriptPath **para especificar o caminho para o arquivo de consulta de seção e **scriptLinkedService** para especificar o armazenamento do Azure que contém o arquivo de script. 

    > [AZURE.NOTE] Você também pode fornecer o embutido de script de seção na definição da atividade usando a propriedade de **script** . Não recomendamos essa abordagem como todos os caracteres especiais no script dentro do documento JSON precisa haver escape e pode causar problemas de depuração. A prática recomendada é siga a etapa 4 #.
5.  Crie um pipeline com a atividade de HDInsightHive. Os dados de processos/transformações a atividade.

        {
          "name": "HiveActivitySamplePipeline",
          "properties": {
            "activities": [
              {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                  {
                    "name": "HiveSampleIn"
                  }
                ],
                "outputs": [
                  {
                    "name": "HiveSampleOut"
                  }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                  "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                  "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
              }
            ]
          }
        }

6.  Implante o pipeline. Consulte o artigo [criar canais](data-factory-create-pipelines.md) para obter detalhes. 
7.  Monitore o pipeline usando as exibições de monitoramento e gerenciamento de fábrica dados. Consulte [monitoramento e gerenciar dados fábrica canais](data-factory-monitor-manage-pipelines.md) artigo para obter detalhes. 


## <a name="specifying-parameters-for-a-hive-script"></a>Especificar parâmetros para um script de seção  
Neste exemplo, logs de jogos são incluídos diariamente para o armazenamento de Blob do Azure em são armazenados em uma pasta particionada com data e hora. Você deseja parametrizar o script de seção e passar o local de pasta de entrada dinamicamente durante a execução e também produzir a saída particionada com data e hora.

Para usar parametrizada script de seção, faça o seguinte

- Defina os parâmetros de **define**.

        {
            "name": "HiveActivitySamplePipeline",
            "properties": {
            "activities": [
                {
                    "name": "HiveActivitySample",
                    "type": "HDInsightHive",
                    "inputs": [
                        {
                            "name": "HiveSampleIn"
                          }
                    ],
                    "outputs": [
                        {
                            "name": "HiveSampleOut"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "typeproperties": {
                        "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
                            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        }
                    }
                }
            ]
          }
        }

- No Script seção, consulte o parâmetro usando **${hiveconf:parameterName}**. 

        DROP TABLE IF EXISTS HiveSampleIn; 
        CREATE EXTERNAL TABLE HiveSampleIn 
        (
            ProfileID   string, 
            SessionStart    string, 
            Duration    int, 
            SrcIPAddress    string, 
            GameType    string
        ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 
        
        DROP TABLE IF EXISTS HiveSampleOut; 
        CREATE EXTERNAL TABLE HiveSampleOut 
        (
            ProfileID   string, 
            Duration    int
        ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';
        
        INSERT OVERWRITE TABLE HiveSampleOut
        Select 
            ProfileID,
            SUM(Duration)
        FROM HiveSampleIn Group by ProfileID


## <a name="see-also"></a>Consulte também
- [Atividade de porco](data-factory-pig-activity.md)
- [Atividade de MapReduce](data-factory-map-reduce.md)
- [Atividade de Streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
- [Chamar Spark programas](data-factory-spark.md)
- [Chamar scripts de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)









