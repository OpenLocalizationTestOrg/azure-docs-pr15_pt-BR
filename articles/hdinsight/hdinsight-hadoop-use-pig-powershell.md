<properties
   pageTitle="Usar Hadoop porco com PowerShell no HDInsight | Microsoft Azure"
   description="Saiba como enviar trabalhos de porco para um cluster de Hadoop em HDInsight usando o PowerShell do Azure."
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
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-using-powershell"></a>Executar trabalhos de porco usando o PowerShell

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este documento fornece um exemplo do uso do PowerShell do Azure para enviar trabalhos de porco a um Hadoop em cluster HDInsight. Porco permite que você escrever MapReduce trabalhos usando um idioma (porco latino), modela transformações de dados, em vez de mapear e reduzir funções.

> [AZURE.NOTE] Este documento não fornece uma descrição detalhada do que fazem as instruções de porco latino usadas nos exemplos. Para obter informações sobre o latino porco usados neste exemplo, consulte [Usar porco com Hadoop em HDInsight](hdinsight-use-pig.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Uma estação de trabalho com o PowerShell do Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


##<a id="powershell"></a>Executar trabalhos de porco usando o PowerShell

Azure PowerShell fornece *cmdlets* que permitem que você executar remotamente trabalhos de porco no HDInsight. Internamente, isso é feito usando o resto chamadas [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente chamado Templeton) executado no cluster HDInsight.

Os seguintes cmdlets são usados quando executando trabalhos de porco em um cluster de HDInsight remoto:

* **AzureRmAccount de login**: autentica Azure PowerShell à sua assinatura do Azure

* **Novo AzureRmHDInsightPigJobDefinition**: cria uma nova *definição de trabalho* usando as instruções de porco latino especificadas

* **Início-AzureRmHDInsightJob**: envia a definição de trabalho ao HDInsight, inicia o trabalho e, em seguida, retorna um objeto de *trabalho* que pode ser usado para verificar o status do trabalho

* **Espera-AzureRmHDInsightJob**: usa o objeto de trabalho para verificar o status do trabalho. Ela esperará até que o trabalho concluído ou o tempo de espera foi excedido.

* **Get-AzureRmHDInsightJobOutput**: usado para recuperar a saída do trabalho

As etapas a seguir demonstram como usar esses cmdlets para executar um trabalho no seu cluster HDInsight.

1. Usando um editor, salve o código a seguir como **pigjob.ps1**. Você deve substituir **CLUSTERNAME** com o nome do seu cluster HDInsight.

        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get credentials for the admin/HTTPs account
        $creds = Get-Credential

        #Specify the cluster name
        $clusterName = "CLUSTERNAME"
        
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
        $container = $clusterInfo.DefaultStorageContainer
        $storageAccountKey = (Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Store the Pig Latin into $QueryString
        $QueryString =  "LOGS = LOAD 'wasbs:///example/data/sample.log';" +
        "LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
        "RESULT = order FREQUENCIES by COUNT desc;" +
        "DUMP RESULT;"


        #Create a new HDInsight Pig Job definition
        $pigJobDefinition = New-AzureRmHDInsightPigJobDefinition `
            -Query $QueryString `
            -Arguments "-w"

        # Start the Pig job on the HDInsight cluster
        Write-Host "Start the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $pigJobDefinition `
            -HttpCredential $creds

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds

        # Display the output of the Pig job.
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds

2. Abra um novo prompt de comando do Windows PowerShell. Altere diretórios para o local do arquivo **pigjob.ps1** e, em seguida, use o seguinte comando para executar o script:

        .\pigjob.ps1
        
    Primeiro você será solicitado a fazer o login à sua assinatura do Azure. Em seguida, você será solicitado para o nome da conta HTTPs/administrador e a senha para o cluster HDInsight.

7. Quando o trabalho for concluído, ele deve retornar informações similar ao seguinte:

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="troubleshooting"></a>Solução de problemas

Se nenhuma informação é retornada quando o trabalho for concluído, um erro pode ter ocorrido durante o processamento. Para exibir informações de erro para este trabalho, adicione o comando a seguir para o final do arquivo **pigjob.ps1** , salvá-lo e executá-la novamente.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Isso retornará as informações que foi escritas para STDERR no servidor quando você executou o trabalho e ela pode ajudar a determinar por que o trabalho está falhando.

##<a id="summary"></a>Resumo

Como você pode ver, o Azure PowerShell fornece uma maneira fácil de executar porco trabalhos em um cluster de HDInsight, monitorar o status do trabalho e recuperar a saída.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre porco em HDInsight:

* [Usar porco com Hadoop em HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop em HDInsight:

* [Use a seção com Hadoop em HDInsight](hdinsight-use-hive.md)

* [Usar MapReduce com Hadoop em HDInsight](hdinsight-use-mapreduce.md)
