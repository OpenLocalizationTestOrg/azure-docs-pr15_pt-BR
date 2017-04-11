<properties
   pageTitle="Use MapReduce e PowerShell com Hadoop | Microsoft Azure"
   description="Aprenda a usar o PowerShell para executar remotamente MapReduce trabalhos com Hadoop em HDInsight."
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
   ms.date="08/29/2016"
   ms.author="larryfr"/>

# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Executar MapReduce trabalhos com Hadoop em HDInsight usando o PowerShell

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Este documento fornece um exemplo do uso do PowerShell do Azure para executar um trabalho de MapReduce em uma Hadoop em cluster HDInsight.

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

- **Um cluster de Azurehdinsight (Hadoop em HDInsight) (baseado no Windows ou baseado no Linux)**

- **Uma estação de trabalho com o PowerShell do Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##<a id="powershell"></a>Executar um trabalho MapReduce usando o PowerShell do Azure

Azure PowerShell fornece *cmdlets* que permitem que você executar remotamente MapReduce trabalhos em HDInsight. Internamente, isso é feito usando o resto chamadas [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente chamado Templeton) executado no cluster HDInsight.

Os seguintes cmdlets são usados quando executado MapReduce trabalhos em um cluster de HDInsight remoto.

* **AzureRmAccount de login**: autentica Azure PowerShell à sua assinatura do Azure

* **Novo AzureRmHDInsightMapReduceJobDefinition**: cria uma nova *definição de trabalho* usando as informações de MapReduce especificadas

* **Início-AzureRmHDInsightJob**: envia a definição de trabalho ao HDInsight, inicia o trabalho e, em seguida, retorna um objeto de *trabalho* que pode ser usado para verificar o status do trabalho

* **Espera-AzureRmHDInsightJob**: usa o objeto de trabalho para verificar o status do trabalho. Ele espera até que o trabalho for concluído ou o tempo de espera foi excedido.

* **Get-AzureRmHDInsightJobOutput**: usado para recuperar a saída do trabalho

As etapas a seguir demonstram como usar esses cmdlets para executar um trabalho no seu cluster HDInsight.

1. Usando um editor, salve o código a seguir como **mapreducejob.ps1**. Você deve substituir **CLUSTERNAME** com o nome do seu cluster HDInsight.

        #Specify the values
        $clusterName = "CLUSTERNAME"
                
        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Login-AzureRmAccount
        }

        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        #Define the MapReduce job
        #NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
        # -JarFile = the JAR containing the MapReduce application
        # -ClassName = the class of the application
        # -Arguments = The input file, and the output directory
        $wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
            -ClassName "wordcount" `
            -Arguments `
                "wasbs:///example/data/gutenberg/davinci.txt", `
                "wasbs:///example/data/WordCountOutput"

        #Submit the job to the cluster
        Write-Host "Start the MapReduce job..." -ForegroundColor Green
        $wordCountJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $wordCountJobDefinition `
            -HttpCredential $creds

        #Wait for the job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $wordCountJob.JobId `
            -HttpCredential $creds
        # Download the output
        Get-AzureStorageBlobContent `
            -Blob 'example/data/WordCountOutput/part-r-00000' `
            -Container $container `
            -Destination output.txt `
            -Context $context
        # Print the output
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Abra um novo prompt de comando do **PowerShell do Azure** . Altere diretórios para o local do arquivo **mapreducejob.ps1** e, em seguida, use o seguinte comando para executar o script:

        .\mapreducejob.ps1
    
    Quando você executa o script, você pode ser solicitado para autenticar à sua assinatura do Azure. Você também será solicitado a fornecer o nome da conta HTTPS/administrador e a senha para o cluster HDInsight.

3. Quando o trabalho for concluído, você deve receber saída similar ao seguinte:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Essa saída indica que o trabalho concluído com êxito.

    > [AZURE.NOTE] Se o **código_de_saída** for um valor diferente de 0, consulte [solução de problemas](#troubleshooting).

    Este exemplo também armazenará os arquivos baixados para um arquivo de **txt** no diretório que você executar o script do.

###<a name="view-output"></a>Saída do modo de exibição

Abra o arquivo **txt** em um editor de texto para ver as palavras e contagens produzidas pelo trabalho.

> [AZURE.NOTE] Os arquivos de saída de um trabalho MapReduce são imutáveis. Portanto, se você executar novamente neste exemplo, você precisa alterar o nome do arquivo de saída.

##<a id="troubleshooting"></a>Solução de problemas

Se nenhuma informação é retornada quando o trabalho for concluído, um erro pode ter ocorrido durante o processamento. Para exibir informações de erro para este trabalho, adicione o comando a seguir para o final do arquivo **mapreducejob.ps1** , salvá-lo e executá-la novamente.

    # Print the output of the WordCount job.
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Isso retorna as informações que foram gravadas STDERR no servidor quando você executou o trabalho e ela pode ajudar a determinar por que o trabalho está falhando.

##<a id="summary"></a>Resumo

Como você pode ver, o Azure PowerShell fornece uma maneira fácil de executar MapReduce trabalhos em um cluster de HDInsight, monitorar o status do trabalho e recuperar a saída.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre MapReduce trabalhos em HDInsight:

* [Use o MapReduce em HDInsight Hadoop](hdinsight-use-mapreduce.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop em HDInsight:

* [Use a seção com Hadoop em HDInsight](hdinsight-use-hive.md)

* [Usar porco com Hadoop em HDInsight](hdinsight-use-pig.md)
