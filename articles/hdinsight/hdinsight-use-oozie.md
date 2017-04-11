<properties
    pageTitle="Usar Hadoop Oozie em HDInsight | Microsoft Azure"
    description="Use Hadoop Oozie do HDInsight, um serviço de dados grande. Aprenda a definir um fluxo de trabalho Oozie e envie um trabalho Oozie."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jgao"/>


# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-in-hdinsight"></a>Use Oozie com Hadoop para definir e executar um fluxo de trabalho no HDInsight

[AZURE.INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Saiba como usar Apache Oozie para definir um fluxo de trabalho e executar o fluxo de trabalho HDInsight. Para saber mais sobre o coordenador Oozie, consulte [Usar baseada em tempo Hadoop Oozie coordenador com HDInsight][hdinsight-oozie-coordinator-time]. Para saber fábrica de dados do Azure, consulte [usar porco e seção com dados fábrica][azure-data-factory-pig-hive].

Apache Oozie é um sistema de fluxo de trabalho/coordenação que gerencia trabalhos de Hadoop. Ele é integrado com a pilha Hadoop e suporta Hadoop trabalhos para Apache MapReduce, Apache porco, Apache seção e Apache Sqoop. Ele também pode ser usado para agendar trabalhos que são específicos para um sistema, como programas Java ou scripts do shell.

O fluxo de trabalho que você irá implementar seguindo as instruções neste tutorial contém duas ações:

![Diagrama de fluxo de trabalho][img-workflow-diagram]

1. Uma ação de seção executa um script de HiveQL para contar as ocorrências de cada tipo de nível de log em um arquivo de log4j. Cada arquivo log4j consiste em uma linha de campos que contém um campo de [nível de LOG] que mostra o tipo e a gravidade, por exemplo:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    A saída de script de seção é semelhante a:

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    Para obter mais informações sobre ramificação, consulte [Usar seção com HDInsight][hdinsight-use-hive].

2.  Uma ação Sqoop exporta a saída de HiveQL a uma tabela em um banco de dados do SQL Azure. Para saber mais sobre Sqoop, consulte [Usar Hadoop Sqoop com HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE] Para as versões Oozie suportadas em clusters de HDInsight, consulte [Novidades nas versões Hadoop cluster fornecidas pelo HDInsight?] [hdinsight-versions].

###<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma estação de trabalho com o PowerShell do Azure**. 

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
    
    Para executar scripts do Windows PowerShell, você deve executar como administrador e defina a política de execução como *RemoteSigned*. Para obter mais informações, consulte [scripts do Windows PowerShell executar][powershell-script].

##<a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Definir fluxo de trabalho Oozie e o script HiveQL relacionado

Definições de fluxos de trabalho de Oozie são escritas em hPDL (um processo de definição de linguagem XML). O nome de arquivo de fluxo de trabalho padrão é *. XML*. A seguir é o arquivo de fluxo de trabalho que você usará neste tutorial.

    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>

        <action name="RunHiveScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.job.queue.name</name>
                        <value>${queueName}</value>
                    </property>
                </configuration>
                <script>${hiveScript}</script>
                <param>hiveTableName=${hiveTableName}</param>
                <param>hiveDataFolder=${hiveDataFolder}</param>
                <param>hiveOutputFolder=${hiveOutputFolder}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>

        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.compress.map.output</name>
                        <value>true</value>
                    </property>
                </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveOutputFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\001"</arg>
            </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>

        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>

        <end name="end"/>
    </workflow-app>

Há duas ações definidas no fluxo de trabalho. A ação Iniciar a é *RunHiveScript*. Se a ação seja executado com êxito, a próxima ação é *RunSqoopExport*.

O RunHiveScript tem diversas variáveis. Você irá passar os valores quando você enviar o trabalho de Oozie de sua estação de trabalho usando o PowerShell do Azure.

<table border = "1">
<tr><th>Variáveis de fluxo de trabalho</th><th>Descrição</th></tr>
<tr><td>${jobTracker}</td><td>Especifica a URL do controlador Hadoop trabalho. Use <strong>jobtrackerhost:9010</strong> no HDInsight versão 3.0 e 2.1.</td></tr>
<tr><td>${nameNode}</td><td>Especifica a URL do nó nome Hadoop. Usar o endereço do sistema de arquivo padrão, por exemplo, <i>wasbs: / /&lt;containerName&gt;@&lt;storageAccountName&gt;. blob.core.windows.net</i>.</td></tr>
<tr><td>${NomeDaFila}</td><td>Especifica o nome da fila que o trabalho será enviado para. Use o <strong>padrão</strong>.</td></tr>
</table>

<table border = "1">
<tr><th>Variável de ação de seção</th><th>Descrição</th></tr>
<tr><td>${hiveDataFolder}</td><td>Especifica o diretório de origem para o comando da seção Criar tabela.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Especifica a pasta de saída para a instrução inserir SOBRESCREVER.</td></tr>
<tr><td>${hiveTableName}</td><td>Especifica o nome da tabela de seção que referencia os arquivos de dados de log4j.</td></tr>
</table>

<table border = "1">
<tr><th>Variável de ação Sqoop</th><th>Descrição</th></tr>
<tr><td>${sqlDatabaseConnectionString}</td><td>Especifica a cadeia de conexão de banco de dados do SQL Azure.</td></tr>
<tr><td>${sqlDatabaseTableName}</td><td>Especifica a tabela de banco de dados do SQL Azure onde os dados serão exportados.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Especifica a pasta de saída para a política de seção Inserir SOBRESCREVER. Esta é a mesma pasta para a exportação de Sqoop (exportação-dir).</td></tr>
</table>

Para obter mais informações sobre como Oozie fluxo de trabalho e usar ações de fluxo de trabalho, consulte [a documentação do Apache Oozie 4.0] [ apache-oozie-400] (para HDInsight versão 3.0) ou [Apache Oozie 3.3.2 documentação] [ apache-oozie-332] (para HDInsight versão 2.1).


A ação de seção no fluxo de trabalho chama um arquivo de script HiveQL. Esse arquivo de script contém três instruções de HiveQL:

    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

1. **Instrução DROP TABLE do** exclui a tabela de seção de log4j se existir.
2. **Instrução o CREATE TABLE** cria uma tabela de externo de seção de log4j que aponta para o local do arquivo de log log4j. O delimitador de campo é ",". O delimitador de linha padrão é "\n". Uma tabela externa seção é usada para evitar o arquivo de dados que está sendo removido do local original se desejar executar o fluxo de trabalho Oozie várias vezes.
3. **Inserir o SOBRESCREVER instrução** conta as ocorrências de cada tipo de nível de log da tabela de seção log4j e salva a saída para um blob no armazenamento do Azure.


Há três variáveis usados no script:

- ${hiveTableName}
- ${hiveDataFolder}
- ${hiveOutputFolder}

O arquivo de definição de fluxo de trabalho (. XML neste tutorial) passa esses valores para esse script HiveQL em tempo de execução.

O arquivo de fluxo de trabalho e o arquivo de HiveQL são armazenados em um contêiner de blob.  O script do PowerShell que você usará posteriormente neste tutorial copiará os dois arquivos para a conta de armazenamento padrão. 

##<a name="submit-oozie-jobs-using-powershell"></a>Enviar trabalhos Oozie usando o PowerShell

PowerShell Azure atualmente não oferece qualquer cmdlets para definir os trabalhos de Oozie. Você pode usar o cmdlet **Chamar RestMethod** para chamar serviços da web de Oozie. A API de serviços web Oozie é uma API de JSON do resto de HTTP. Para obter mais informações sobre os serviços web do Oozie API, consulte [a documentação do Apache Oozie 4.0] [ apache-oozie-400] (para HDInsight versão 3.0) ou [Apache Oozie 3.3.2 documentação] [ apache-oozie-332] (para HDInsight versão 2.1).

O script do PowerShell nesta seção executa as seguintes etapas:

1. Conecte a Azure.
2. Crie um grupo de recursos Azure. Para obter mais informações, consulte [Usar o PowerShell do Azure com o Gerenciador de recursos do Azure](../powershell-azure-resource-manager.md).
3. Crie um servidor de banco de dados do Azure SQL, um banco de dados do SQL Azure e duas tabelas. Eles são usados pela ação Sqoop no fluxo de trabalho.

    O nome da tabela é *log4jLogCount*.

4. Crie um cluster de HDInsight usado para executar trabalhos de Oozie.

    Para examinar o cluster, você pode usar o portal do Azure ou Azure PowerShell.

5. Copie o arquivo de fluxo de trabalho de oozie e o arquivo de script HiveQL para o sistema de arquivo padrão.

    Os dois arquivos são armazenados em um contêiner de Blob público.
    
    - Copie o script de HiveQL (useoozie.hql) para o armazenamento do Azure (wasbs:///tutorials/useoozie/useoozie.hql).
    - Copie. XML para wasbs:///tutorials/useoozie/workflow.xml.
    - Copie o arquivo de dados (/ example/data/sample.log) para wasbs:///tutorials/useoozie/data/sample.log.
     
6. Envie um trabalho de Oozie.

    Para examinar os resultados de trabalho OOzie, use o Visual Studio ou outras ferramentas para conectar o banco de dados do SQL Azure.

Aqui está o script.  Você pode executar o script do Windows PowerShell ISE. Você só precisa configurar as variáveis de 7 primeiros.

    #region - provide the following values
    
    $subscriptionID = "<Enter your Azure subscription ID>"
    
    # SQL Database server login credentials used for creating and connecting
    $sqlDatabaseLogin = "<Enter SQL Database Login Name>"
    $sqlDatabasePassword = "<Enter SQL Database Login Password>"
    
    # HDInsight cluster HTTP user credential used for creating and connectin
    $httpUserName = "admin"  # The default name is "admin"
    $httpPassword = "<Enter HDInsight Cluster HTTP User Password>"
    
    # Used for creating Azure service names
    $nameToken = "<Enter an Alias>"
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion
    
    #region - variables
    
    # Resource group variables
    $resourceGroupName = $namePrefix + "rg"
    $location = "East US 2" # used by all Azure services defined in this tutorial
    
    # SQL database varialbes
    $sqlDatabaseServerName = $namePrefix + "sqldbserver"
    $sqlDatabaseName = $namePrefix + "sqldb"
    $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $sqlDatabaseMaxSizeGB = 10
    
    # Used for retrieving external IP address and creating firewall rules
    $ipAddressRestService = "http://bot.whatismyipaddress.com"
    $fireWallRuleName = "UseSqoop"
    
    # HDInsight variables
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    #endregion
    
    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"
    
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{
        Login-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionId $subscriptionID
    }
    #endregion
    
    #region - Create Azure resouce group
    Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
    try{
        Get-AzureRmResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    }
    #endregion
    
    #region - Create Azure SQL database server
    Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
    try{
        Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
    
        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $sqlLoginCredentials = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
    
        $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                    -ResourceGroupName $resourceGroupName `
                                    -ServerName $sqlDatabaseServerName `
                                    -SqlAdministratorCredentials $sqlLoginCredentials `
                                    -Location $location).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan
    
        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-workstation" `
            -StartIpAddress $workstationIPAddress `
            -EndIpAddress $workstationIPAddress
    
        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
        #Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-Azureservices" `
            -StartIpAddress "0.0.0.0" `
            -EndIpAddress "0.0.0.0"
    }
    #endregion
    
    #region - Create and validate Azure SQL database
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
    
    try {
        Get-AzureRmSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName
    }
    catch {
        New-AzureRMSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName `
            -Edition "Standard" `
            -RequestedServiceObjectiveName "S1"
    }
    #endregion
    
    #region - Create SQL database tables
    Write-Host "Creating the log4jlogs table  ..." -ForegroundColor Green
    
    $sqlDatabaseTableName = "log4jLogsCount"
    $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
            [Level] [nvarchar](10) NOT NULL,
            [Total] float,
        CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
        (
        [Level] ASC
        )
        )"
    
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = $sqlDatabaseConnectionString
    $conn.Open()
    
    # Create the log4jlogs table and index
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.Connection = $conn
    $cmd.CommandText = $cmdCreateLog4jCountTable
    $cmd.ExecuteNonQuery()
    
    $conn.close()
    #endregion
    
    #region - Create HDInsight cluster
    
    Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green
    
    # Create the default storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    
    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                        -StorageAccountName $defaultStorageAccountName `
                                        -StorageAccountKey $defaultStorageAccountKey 
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext 
    
    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
    
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -Location $location `
        -ClusterType Hadoop `
        -OSType Windows `
        -ClusterSizeInNodes 2 `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultBlobContainerName 
    
    # Validate the cluster
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
    #endregion
    
    #region - copy Oozie workflow and HiveQL files
    
    Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green
    
    # Both files are stored in a public Blob
    $publicBlobContext = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
    
    # WASB folder for storing the Oozie tutorial files.
    $destFolder = "tutorials/useoozie"  # Do NOT use the long path here
    
    Start-CopyAzureStorageBlob `
        -Context $publicBlobContext `
        -SrcContainer "useoozie" `
        -SrcBlob "useooziewf.hql"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -DestBlob "$destFolder/useooziewf.hql" `
        -Force
    
    Start-CopyAzureStorageBlob `
        -Context $publicBlobContext `
        -SrcContainer "useoozie" `
        -SrcBlob "workflow.xml"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -DestBlob "$destFolder/workflow.xml" `
        -Force
    
    #validate the copy
    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/workflow.xml
    
    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/useooziewf.hql
    
    #endregion
    
    #region - copy the sample.log file
    
    Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
    
    Start-CopyAzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -SrcContainer $defaultBlobContainerName `
        -SrcBlob "example/data/sample.log"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -destBlob "$destFolder/data/sample.log" 
    
    #validate the copy
    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/data/sample.log
    
    #endregion
    
    #region - submit Oozie job
    
    $storageUri="wasbs://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net"
    
    $oozieJobName = $namePrefix + "OozieJob"
    
    #Oozie WF variables
    $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
    $waitTimeBetweenOozieJobStatusCheck=10
    
    #Hive action variables
    $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
    $hiveTableName = "log4jlogs"
    $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
    $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"
    
    #Sqoop action variables
    $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    
    $OoziePayload =  @"
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
    
    <property>
        <name>nameNode</name>
        <value>$storageUrI</value>
    </property>
    
    <property>
        <name>jobTracker</name>
        <value>jobtrackerhost:9010</value>
    </property>
    
    <property>
        <name>queueName</name>
        <value>default</value>
    </property>
    
    <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
    </property>
    
    <property>
        <name>hiveScript</name>
        <value>$hiveScript</value>
    </property>
    
    <property>
        <name>hiveTableName</name>
        <value>$hiveTableName</value>
    </property>
    
    <property>
        <name>hiveDataFolder</name>
        <value>$hiveDataFolder</value>
    </property>
    
    <property>
        <name>hiveOutputFolder</name>
        <value>$hiveOutputFolder</value>
    </property>
    
    <property>
        <name>sqlDatabaseConnectionString</name>
        <value>&quot;$sqlDatabaseConnectionString&quot;</value>
    </property>
    
    <property>
        <name>sqlDatabaseTableName</name>
        <value>$SQLDatabaseTableName</value>
    </property>
    
    <property>
        <name>user.name</name>
        <value>$httpUserName</value>
    </property>
    
    <property>
        <name>oozie.wf.application.path</name>
        <value>$oozieWFPath</value>
    </property>
    
    </configuration>
    "@
    
    Write-Host "Checking Oozie server status..." -ForegroundColor Green
    $clusterUriStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/admin/status"
    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $httpCredential -OutVariable $OozieServerStatus
    
    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $oozieServerSatus = $jsonResponse[0].("systemMode")
    Write-Host "Oozie server status is $oozieServerSatus."
    
    # create Oozie job
    Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
    Write-Host "`n--------`n$OoziePayload`n--------"
    $clusterUriCreateJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/jobs"
    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $httpCredential -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug
    
    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $oozieJobId = $jsonResponse[0].("id")
    Write-Host "Oozie job id is $oozieJobId..."
    
    # start Oozie job
    Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
    $clusterUriStartJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $httpCredential | Format-Table -HideTableHeaders #-debug
    
    # get job status
    Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
    Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
    
    Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
    $clusterUriGetJobStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
    $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $JobStatus = $jsonResponse[0].("status")
    
    while($JobStatus -notmatch "SUCCEEDED|KILLED")
    {
        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")
        $jobStatus
    }
    
    Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green
    
    #endregion


**Para executar novamente o tutorial**

Para executar novamente o fluxo de trabalho, você deve excluir o seguinte:

- O arquivo de saída de script da seção
- Os dados na tabela log4jLogsCount

Aqui está um script PowerShell de exemplo que você pode usar:

    $resourceGroupName = "<AzureResourceGroupName>"
    
    $defaultStorageAccountName = "<AzureStorageAccountName>"
    $defaultBlobContainerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServerName = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabasePassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $defaultBlobContainerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()

##<a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como definir um fluxo de trabalho Oozie e como executar um trabalho Oozie usando o PowerShell. Para saber mais, consulte os seguintes artigos:

- [Usar baseada em tempo Oozie coordenador com HDInsight][hdinsight-oozie-coordinator-time]
- [Começar a usar Hadoop com seção no HDInsight para analisar o uso do celular][hdinsight-get-started]
- [Usar o armazenamento de Blob do Azure com HDInsight][hdinsight-storage]
- [Administrar HDInsight usando o PowerShell][hdinsight-admin-powershell]
- [Carregar dados para trabalhos de Hadoop em HDInsight][hdinsight-upload-data]
- [Usar Sqoop com Hadoop em HDInsight][hdinsight-use-sqoop]
- [Use a seção com Hadoop em HDInsight][hdinsight-use-hive]
- [Usar porco com Hadoop em HDInsight][hdinsight-use-pig]
- [Desenvolver programas Java MapReduce para HDInsight][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/data-factory-data-transformation-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: ../sql-database-create-configure.md
[sqldatabase-get-started]: ../sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
