<properties
   pageTitle="Use a seção Hadoop com PowerShell no HDInsight | Microsoft Azure"
   description="Usar o PowerShell para executar consultas de seção em Hadoop em HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/07/2016"
   ms.author="larryfr"/>

#<a name="run-hive-queries-using-powershell"></a>Executar consultas de seção usando o PowerShell

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Este documento fornece um exemplo do uso do PowerShell do Azure no modo de grupo de recursos do Azure para executar consultas de seção em um Hadoop em cluster HDInsight.

> [AZURE.NOTE] Este documento não fornece uma descrição detalhada do que fazem as instruções de HiveQL que são usadas nos exemplos. Para obter informações sobre a HiveQL que é usada neste exemplo, consulte a [Seção uso com Hadoop em HDInsight](hdinsight-use-hive.md).


**Pré-requisitos**

Para concluir as etapas neste artigo, você precisará do seguinte.

- **Um cluster de Azurehdinsight (Hadoop em HDInsight) (baseado no Windows ou baseado no Linux)**
- **Uma estação de trabalho com o PowerShell do Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##<a name="run-hive-queries-using-azure-powershell"></a>Executar consultas de seção usando o PowerShell do Azure

Azure PowerShell fornece *cmdlets* que permitem remotamente executar consultas de seção em HDInsight. Internamente, isso é feito usando chamadas REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente chamado Templeton) executado no cluster HDInsight.

Os seguintes cmdlets são usados ao executar consultas de seção em um cluster de HDInsight remoto:

* **Adicionar AzureRmAccount**: autentica Azure PowerShell à sua assinatura do Azure

* **Novo AzureRmHDInsightHiveJobDefinition**: cria uma nova *definição de trabalho* usando as instruções de HiveQL especificadas

* **Início-AzureRmHDInsightJob**: envia a definição de trabalho ao HDInsight, inicia o trabalho e, em seguida, retorna um objeto de *trabalho* que pode ser usado para verificar o status do trabalho

* **Espera-AzureRmHDInsightJob**: usa o objeto de trabalho para verificar o status do trabalho. Ela esperará até que o trabalho for concluído ou o tempo de espera é excedido.

* **Get-AzureRmHDInsightJobOutput**: usado para recuperar a saída do trabalho

* **Chamar AzureRmHDInsightHiveJob**: usado para executar declarações de HiveQL. Isso bloqueará a consulta é concluída, em seguida, retorna os resultados

* **Use-AzureRmHDInsightCluster**: define o cluster atual para usar para o comando de **Chamar AzureRmHDInsightHiveJob**

As etapas a seguir demonstram como usar esses cmdlets para executar um trabalho no seu cluster HDInsight:

1. Usando um editor, salve o código a seguir como **hivejob.ps1**. Você deve substituir **CLUSTERNAME** com o nome do seu cluster HDInsight.

        #Specify the values
        $clusterName = "CLUSTERNAME"
        $creds=Get-Credential

        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Add-AzureRmAccount
        }

        #HiveQL
        #Note: set hive.execution.engine=tez; is not required for
        #      Linux-based HDInsight
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

        #Create an HDInsight Hive job definition
        $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

        #Submit the job to the cluster
        Write-Host "Start the Hive job..." -ForegroundColor Green

        $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds

        #Wait for the Hive job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Print the output
        Write-Host "Display the standard output..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $hiveJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Abra um novo prompt de comando do **PowerShell do Azure** . Altere diretórios para o local do arquivo **hivejob.ps1** e, em seguida, use o seguinte comando para executar o script:

        .\hivejob.ps1

    Quando o script é executado, você será solicitado a inserir as credenciais de conta de administrador/HTTPS para seu cluster. Você também pode ser solicitado a login à sua assinatura do Azure.
    
7. Quando o trabalho for concluído, ele deve retornar informações similar ao seguinte:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Conforme mencionado anteriormente, **Chamar seção** pode ser usado para executar uma consulta e esperar a resposta. Use os seguintes comandos e substituir **CLUSTERNAME** com o nome do seu cluster:

        Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
        Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query $queryString

    A saída será parecida com o seguinte:

        2012-02-03  18:35:34    SampleClass0    [ERROR] incorrect   id
        2012-02-03  18:55:54    SampleClass1    [ERROR] incorrect   id
        2012-02-03  19:25:27    SampleClass4    [ERROR] incorrect   id

    > [AZURE.NOTE] Para consultas de HiveQL mais tempo, você pode usar o cmdlet do PowerShell do Azure **Aqui cadeias de caracteres** ou arquivos de script de HiveQL. O trecho a seguir mostra como usar o cmdlet a **Seção de chamar** para executar um arquivo de script HiveQL. O arquivo de script HiveQL deve ser carregado wasbs: / /.
    >
    > `Invoke-AzureRmHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
    >
    > Para obter mais informações sobre **Cadeias de caracteres de aqui**, consulte <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Usando o Windows PowerShell aqui cadeias de caracteres</a>.

##<a name="troubleshooting"></a>Solução de problemas

Se nenhuma informação é retornada quando o trabalho for concluído, um erro pode ter ocorrido durante o processamento. Para exibir informações de erro para este trabalho, adicione o seguinte ao final do arquivo **hivejob.ps1** , salvá-lo e executá-la novamente.

    # Print the output of the Hive job.
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Isso retorna as informações que são gravadas em STDERR no servidor quando você executou o trabalho, e ele pode ajudar a determinar por que o trabalho está falhando.

##<a name="summary"></a>Resumo

Como você pode ver, o Azure PowerShell fornece uma maneira fácil de executar consultas de seção em um cluster de HDInsight, monitorar o status do trabalho e recuperar a saída.

##<a name="next-steps"></a>Próximas etapas

Para obter informações gerais sobre a seção no HDInsight:

* [Use a seção com Hadoop em HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop em HDInsight:

* [Usar porco com Hadoop em HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce com Hadoop em HDInsight](hdinsight-use-mapreduce.md)
