Azure fábrica de dados é compatível com as seguintes transformação atividades que podem ser adicionadas a canais ou individualmente ou encadeadas com outra atividade.

Atividade de transformação de dados |  Calcular o ambiente 
:----------------------- | :--------------------
[Seção](../articles/data-factory/data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Porco](../articles/data-factory/data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](../articles/data-factory/data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop Streaming](../articles/data-factory/data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Máquina de atividades de aprendizagem: recursos de atualização e execução em lotes](../articles/data-factory/data-factory-azure-ml-batch-execution-activity.md) | Máquina virtual Azure 
[Procedimento armazenado](../articles/data-factory/data-factory-stored-proc-activity.md) | SQL Azure, SQL Azure Data Warehouse ou do SQL Server |
[Lucerne a análise de dados U-SQL](../articles/data-factory/data-factory-usql-activity.md) | Dados Azure Lucerne Analytics 
[DotNet](../articles/data-factory/data-factory-use-custom-activities.md) | HDInsight [Hadoop] ou lote Azure
   
> [AZURE.NOTE] 
> Você pode usar MapReduce atividade para executar Spark programas em seu cluster HDInsight Spark. Consulte [programas de invocar Spark da fábrica de dados do Azure](../articles/data-factory/data-factory-spark.md) para obter detalhes.
> Você pode criar uma atividade personalizada para executar scripts de R no cluster HDInsight com R instalado. Consulte [Executar R Script usando fábrica de dados do Azure](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample).