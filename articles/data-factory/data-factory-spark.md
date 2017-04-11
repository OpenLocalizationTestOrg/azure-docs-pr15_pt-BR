<properties 
    pageTitle="Chamar Spark programas da fábrica de dados do Azure" 
    description="Aprenda a chamar Spark programas de uma fábrica de dados Azure usando a atividade de MapReduce." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="spelluru"/>

# <a name="invoke-spark-programs-from-data-factory"></a>Chamar Spark programas da fábrica de dados
## <a name="introduction"></a>Introdução
Você pode usar a atividade de MapReduce em um pipeline de fábrica de dados para executar Spark programas em seu cluster HDInsight Spark. Consulte o artigo de [Atividade MapReduce](data-factory-map-reduce.md) para obter informações detalhadas sobre como usar a atividade antes de ler este artigo. 

## <a name="spark-sample-on-github"></a>Amostra de Spark no GitHub
O [Spark - amostra de dados fábrica no GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) mostra como usar MapReduce atividade para chamar um programa Spark. O programa spark apenas copia dados de um contêiner de Blob do Azure para outro. 

## <a name="data-factory-entities"></a>Entidades de fábrica de dados
A pasta **Spark-AAD/src/ADFJsons** contém arquivos para entidades de fábrica de dados (serviços vinculados, conjuntos de dados, pipeline).  

Há dois **Serviços vinculados** neste exemplo: armazenamento do Azure e Azure HDInsight. Especifique seu nome de armazenamento do Azure e valores-chave na **StorageLinkedService.json** e clusterUri, nome de usuário e senha no **HDInsightLinkedService.json**.

Há dois **conjuntos de dados** neste exemplo: **input.json** e **output.json**. Esses arquivos estão localizados na pasta de **conjuntos de dados** .  Esses arquivos representam conjuntos de dados de entrada e saídos para a atividade de MapReduce

Localize o pipeline de exemplo na pasta **ADFJsons/Pipeline** . Examine um pipeline para entender como invocar um programa Spark utilizando a atividade de MapReduce. 

A atividade de MapReduce está configurada para chamar **com.adf.sparklauncher.jar** no contêiner de **adflibs** em seu armazenamento Azure (especificado na StorageLinkedService.json). O código-fonte para este programa está em Spark-AAD/src/principal/java/com/AAD/pasta e ele chama enviar spark e executar Spark trabalhos. 

> [AZURE.IMPORTANT] 
> Ler [Leiame. TXT](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.txt) para obter as informações mais recentes e adicionais antes de usar a amostra. 
>  
> Use seu próprio cluster HDInsight Spark com essa abordagem para chamar Spark programas usando a atividade de MapReduce. Usar um cluster de HDInsight sob demanda não é suportada.   


## <a name="see-also"></a>Consulte também
- [Atividade de seção](data-factory-hive-activity.md)
- [Atividade de porco](data-factory-pig-activity.md)
- [Atividade de MapReduce](data-factory-map-reduce.md)
- [Atividade de Streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
- [Chamar scripts de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
