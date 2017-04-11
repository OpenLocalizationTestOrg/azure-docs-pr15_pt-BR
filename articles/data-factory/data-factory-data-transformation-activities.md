<properties 
    pageTitle="Transformação de dados: Dados de processo e transformar | Microsoft Azure" 
    description="Saiba como transformar dados ou dados de processo de fábrica de dados do Azure usando Hadoop, aprendizado de máquina ou Azure dados Lucerne Analytics." 
    keywords="transformação de dados, dados de processo, transformar dados, atividade de transformação"
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
    ms.date="09/23/2016" 
    ms.author="shlo"/>

# <a name="transform-data-in-azure-data-factory"></a>Transformar dados em fábrica de dados do Azure
> [AZURE.SELECTOR]
[Seção](data-factory-hive-activity.md)  
[Porco](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Fluxo de Hadoop](data-factory-hadoop-streaming-activity.md)
[Aprendizado de máquina](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimento armazenado](data-factory-stored-proc-activity.md)
[Dados Lucerne Analytics U-SQL](data-factory-usql-activity.md)
[.NET personalizado](data-factory-use-custom-activities.md)
   

## <a name="overview"></a>Visão geral 
Este artigo explica as atividades de transformação de dados no Azure Factory de dados que você pode usar para transformar e processa seus dados brutos para previsões e ideias. Uma atividade de transformação executa em um ambiente de computação como Azurehdinsight cluster ou um lote de Azure. Ele fornece links para artigos com informações detalhadas sobre cada atividade de transformação.
 
Fábrica de dados é compatível com as seguintes dados transformação atividades que podem ser adicionadas a [canais](data-factory-create-pipelines.md) ou individualmente ou encadeadas com outra atividade.

> [AZURE.NOTE] Para instruções passo a passo com instruções passo a passo, consulte o artigo [criar um pipeline com transformação de seção](data-factory-build-your-first-pipeline.md) .  

## <a name="hdinsight-hive-activity"></a>Atividade de uma seção de HDInsight
A atividade de HDInsight seção em um pipeline de dados fábrica executa consultas de seção no seu próprio ou cluster de baseado no Windows/Linux HDInsight sob demanda. Consulte o artigo de [Atividade de uma seção](data-factory-hive-activity.md) para obter detalhes sobre esta atividade. 

## <a name="hdinsight-pig-activity"></a>Atividade de HDInsight porco
A atividade de HDInsight porco em um pipeline de dados fábrica executa consultas de porco própria ou cluster de baseado no Windows/Linux HDInsight sob demanda. Consulte o artigo de [Atividade de porco](data-factory-pig-activity.md) para obter detalhes sobre esta atividade. 

## <a name="hdinsight-mapreduce-activity"></a>Atividade de HDInsight MapReduce
A atividade de HDInsight MapReduce em um pipeline de dados fábrica executa programas MapReduce própria ou cluster de baseado no Windows/Linux HDInsight sob demanda. Consulte o artigo de [Atividade MapReduce](data-factory-map-reduce.md) para obter detalhes sobre esta atividade.

Você pode usar MapReduce atividade para executar Spark programas em seu cluster HDInsight Spark. Consulte [programas de invocar Spark da fábrica de dados do Azure](data-factory-spark.md) para obter detalhes.

## <a name="hdinsight-streaming-activity"></a>Atividade de Streaming de HDInsight
A atividade de Streaming de HDInsight em um pipeline de dados fábrica executa programas Hadoop Streaming própria ou cluster de baseado no Windows/Linux HDInsight sob demanda. Ver a [atividade de Streaming de HDInsight](data-factory-hadoop-streaming-activity.md) para obter detalhes sobre esta atividade.

## <a name="machine-learning-activities"></a>Atividades de aprendizado de máquina
Azure fábrica de dados permite que você crie facilmente canais que usam um serviço da web de aprendizado de máquina do Azure publicado para análise de previsão. Usando a [Atividade de execução de lote](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) em um pipeline de fábrica de dados do Azure, você pode chamar um serviço da web de aprendizado de máquina para fazer previsões sobre os dados no lote.

Ao longo do tempo, os modelos de previsão no aprendizado de máquina pontuação experiências precisam ser retrained usando novos conjuntos de dados de entrada. Depois de terminar com treinamento, você deseja atualizar o pontuação serviço da web com o modelo de aprendizado de máquina retrained. Você pode usar a [Atividade de recurso Atualizar](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) para atualizar o serviço da web com o modelo de treinamento recentemente.  

Consulte [as atividades de aprendizado de máquina de uso](data-factory-azure-ml-batch-execution-activity.md) para obter detalhes sobre essas atividades de aprendizado de máquina. 

## <a name="stored-procedure-activity"></a>Atividade de procedimento armazenado
Você pode usar a atividade de procedimento armazenado do SQL Server em um pipeline de fábrica de dados para chamar um procedimento armazenado em um dos seguintes repositórios de dados: Azure SQL Database, depósito de dados do SQL Azure, banco de dados do SQL Server em sua empresa ou uma máquina virtual do Azure. Consulte o artigo de [Atividade de procedimento armazenado](data-factory-stored-proc-activity.md) para obter detalhes.  

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade de análise de Lucerne U-SQL de dados
Atividade de U-SQL de análise de dados Lucerne executa um script de U-SQL em um cluster de análise de Lucerne de dados do Azure. Consulte o artigo de [Atividade de U-SQL de análise de dados](data-factory-usql-activity.md) para obter detalhes. 

## <a name="net-custom-activity"></a>Atividade personalizada do .NET
Se você precisar transformar dados de uma forma que não é suportada por Data Factory, você pode criar uma atividade personalizada com sua própria lógica de processamento de dados e usar a atividade no pipeline. Você pode configurar a atividade personalizada do .NET para executar usando um serviço de lote Azure ou um cluster de Azurehdinsight. Consulte o artigo de [atividades personalizadas de uso](data-factory-use-custom-activities.md) para obter detalhes. 

Você pode criar uma atividade personalizada para executar scripts de R em seu cluster HDInsight com R instalado. Consulte [Executar scripts de R usando fábrica de dados do Azure](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Calcular ambientes
Você cria um serviço vinculado para o ambiente de computação e, em seguida, usa o serviço de vinculado ao definir uma atividade de transformação. Há dois tipos de ambientes de computação suportados pela fábrica de dados. 

1. **Sob demanda**: nesse caso, o ambiente de computação é totalmente gerenciado pela fábrica de dados. Ele é criado automaticamente pelo serviço fábrica de dados antes de um trabalho é enviado para processar dados e removido quando o trabalho está concluído. Você pode configurar e controlar configurações granulares do ambiente de computação sob demanda para execução de trabalho, gerenciamento de cluster e ações de inicialização. 
2. **Trazer seu próprio**: nesse caso, você pode registrar seu próprio ambiente de computação (por exemplo HDInsight cluster) como um serviço vinculado em fábrica de dados. O ambiente de computação é gerenciado por você e o serviço de dados Factory usa para executar as atividades. 

Consulte o artigo [Calcular vinculadas serviços](data-factory-compute-linked-services.md) para aprender a calcular serviços suportados pelo Data Factory. 


## <a name="summary"></a>Resumo
Azure Data Factory suporta as seguintes atividades de transformação de dados e os ambientes de computação para as atividades. As atividades de transformação podem ser adicionadas a canais ou individualmente ou encadeadas com outra atividade.

Atividade de transformação de dados |  Calcular o ambiente 
:----------------------- | :--------------------
[Seção](data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Porco](data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop Streaming](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Máquina de atividades de aprendizagem: recursos de atualização e execução em lotes](data-factory-azure-ml-batch-execution-activity.md) | Máquina virtual Azure 
[Procedimento armazenado](data-factory-stored-proc-activity.md) | SQL Azure, SQL Azure Data Warehouse ou do SQL Server |
[Lucerne a análise de dados U-SQL](data-factory-usql-activity.md) | Dados Azure Lucerne Analytics 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] ou lote Azure
   

