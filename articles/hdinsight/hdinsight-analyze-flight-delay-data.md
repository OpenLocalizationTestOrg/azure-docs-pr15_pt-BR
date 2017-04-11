<properties
    pageTitle="Analisar os dados de atrasos de voos com Hadoop em HDInsight | Microsoft Azure"
    description="Saiba como usar um script do Windows PowerShell para criar um cluster de HDInsight, execute um trabalho de seção, executar um trabalho Sqoop e excluir o cluster."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

#<a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Analisar os dados de atrasos de voos usando a seção no HDInsight

Seção fornece um meio de trabalhos em execução Hadoop MapReduce por meio de uma linguagem de script do tipo SQL chamada * [HiveQL][hadoop-hiveql]*, que podem ser aplicadas em direção Resumindo, consultando e analisar grandes volumes de dados.

> [AZURE.NOTE] As etapas neste documento exigem um cluster baseado no Windows HDInsight. Para obter etapas que funcionam com um cluster baseado em Linux, consulte [os dados de atrasos de voos de análise usando a seção no HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md).

Um dos principais benefícios do Azure HDInsight é a separação de computação e de armazenamento de dados. HDInsight usa o armazenamento de Blob do Azure para armazenamento de dados. Um trabalho típico envolve três partes:

1. **Armazenar dados de armazenamento de Blob do Azure.**  Por exemplo, tempo dados, dados, logs da web e nesse caso, os dados de atrasos de voos são salvas no armazenamento de Blob do Azure.
2. **Execute trabalhos.** Quando é hora de processar os dados, executar um script do Windows PowerShell (ou um aplicativo cliente) para criar um cluster de HDInsight, executar trabalhos e excluir o cluster. Os trabalhos salvar dados de saída ao armazenamento de Blob do Azure. Os dados de saída são mantidos mesmo após o cluster é excluído. Dessa forma, você paga apenas o que você tiver consumidas.
3. **Recuperar a saída do armazenamento de Blob do Azure**, ou neste tutorial, exporte os dados para um banco de dados do SQL Azure.

O diagrama a seguir ilustra o cenário e a estrutura deste tutorial:

![HDI. FlightDelays.flow][img-hdi-flightdelays-flow]

**Observação**: os números no diagrama correspondem aos títulos de seção. **M** significa do processo principal. **A** representa o conteúdo no apêndice.

Parte principal do tutorial mostra como usar um script do Windows PowerShell para executar as seguintes tarefas:

- Crie um cluster de HDInsight.
- Execute um trabalho de seção no cluster para calcular a médio atrasos em aeroportos. Os dados de atraso voos são armazenados em uma conta de armazenamento de Blob do Azure.
- Execute um trabalho de Sqoop para exportar a saída de trabalho de seção em um banco de dados do SQL Azure.
- Exclua o cluster HDInsight.

Os apêndices, você encontrará as instruções para carregar os dados de atrasos de voos, criando/upload de uma cadeia de caracteres de consulta de seção e preparando o banco de dados do SQL Azure para o trabalho de Sqoop.

> [AZURE.NOTE] As etapas neste documento são específicas para clusters HDInsight baseados no Windows. Para obter etapas que funcionam com um cluster baseado em Linux, consulte [dados de atrasos de voos de análise usando a seção no HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md)

###<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter os seguintes itens:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Uma estação de trabalho com o PowerShell do Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

**Arquivos usados neste tutorial**

Este tutorial usa o desempenho em tempo de dados de voos de companhias de [pesquisa e administração de tecnologia inovadora, gabinete de estatísticas de transporte ou RITA] [rita-website]. Uma cópia dos dados é carregada em um contêiner de armazenamento de Blob do Azure com a permissão de acesso do Blob pública. Parte de seu script PowerShell copia os dados do contêiner blob pública ao contêiner de blob padrão do cluster. O script HiveQL também é copiado para o mesmo contêiner de Blob. Se você quiser saber como obter/carregar os dados para sua própria conta de armazenamento e como criar/carregar o arquivo de script HiveQL, consulte [Apêndice A](#appendix-a) e [Apêndice B](#appendix-b).

A tabela a seguir lista os arquivos usados neste tutorial:

<table border="1">
<tr><th>Arquivos</th><th>Descrição</th></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>O arquivo de script HiveQL usado pelo trabalho de seção. Este script foi carregado para uma conta de armazenamento de Blob do Azure com o acesso público. <a href="#appendix-b">Apêndice B</a> tem instruções sobre como preparar e carregar este arquivo para sua própria conta de armazenamento de Blob do Azure.</td></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Dados de entrada para o trabalho de seção. Os dados são carregados em uma conta de armazenamento de Blob do Azure com o acesso público. <a href="#appendix-a">Apêndice A</a> possui instruções sobre como obter os dados e carregando os dados para sua própria conta de armazenamento de Blob do Azure.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>O caminho de saída para o trabalho de seção. O recipiente padrão é usado para armazenar os dados de saída.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>A pasta de status de trabalho de seção no contêiner de padrão.</td></tr>
</table>


##<a name="create-cluster-and-run-hivesqoop-jobs"></a>Criar cluster e executar trabalhos de seção/Sqoop

Hadoop MapReduce é processamento em lotes. A maneira mais econômica para executar um trabalho de seção é criar um cluster para o trabalho e excluir o trabalho depois que o trabalho está concluído. O seguinte script abrange todo o processo. Para obter mais informações sobre como criar um cluster de HDInsight e executar trabalhos de seção, consulte [criar Hadoop clusters no HDInsight] [ hdinsight-provision] e [Usar seção com HDInsight][hdinsight-use-hive].

**Executar as consultas de seção pelo PowerShell do Azure**

1. Crie um banco de dados do SQL Azure e a tabela para a saída de trabalho Sqoop usando as instruções no [Apêndice C](#appendix-c).
3. Abra o Windows PowerShell ISE e execute o seguinte script:

        $subscriptionID = "<Azure Subscription ID>"
        $nameToken = "<Enter an Alias>" 
        
        ###########################################
        # You must configure the follwing variables
        # for an existing Azure SQL Database
        ###########################################
        $existingSqlDatabaseServerName = "<Azure SQL Database Server>"
        $existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
        $existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
        $existingSqlDatabaseName = "<Azure SQL Database name>"
        
        $localFolder = "E:\Tutorials\Downloads\" # A temp location for copying files. 
        $azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different
        
        ###########################################
        # (Optional) configure the following variables
        ###########################################
        
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $location = "EAST US 2"
        
        $HDInsightClusterName = $namePrefix + "hdi"
        $httpUserName = "admin"
        $httpPassword = "<Enter the Password>"
        
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $HDInsightClusterName # use the cluster name
        
        $existingSqlDatabaseTableName = "AvgDelays"
        $sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"
        
        $hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql" 
        
        $jobStatusFolder = "/tutorials/flightdelays/jobstatus"
        
        ###########################################
        # Login
        ###########################################
        try{
            $acct = Get-AzureRmSubscription
        }
        catch{
            Login-AzureRmAccount
        }
        Select-AzureRmSubscription -SubscriptionID $subscriptionID
        
        ###########################################
        # Create a new HDInsight cluster
        ###########################################
        
        # Create ARM group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
        
        # Create the default storage account
        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS
        
        # Create the default Blob container
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
        $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 
        New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext 
        
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
            -DefaultStorageContainer $existingDefaultBlobContainerName 
        
        
        ###########################################
        # Prepare the HiveQL script and source data
        ###########################################
        
        # Create the temp location  
        New-Item -Path $localFolder -ItemType Directory -Force 
        
        # Download the sample file from Azure Blob storage
        $context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
        $blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
        #$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder
        
        # Upload data to default container
        
        $azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"
        
        Invoke-Expression -Command:$azcopycmd
        
        ###########################################
        # Submit the Hive job
        ###########################################
        Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
        $response = Invoke-AzureRmHDInsightHiveJob `
                        -Files $hqlScriptFile `
                        -DefaultContainer $defaultBlobContainerName `
                        -DefaultStorageAccountName $defaultStorageAccountName `
                        -DefaultStorageAccountKey $defaultStorageAccountKey `
                        -StatusFolder $jobStatusFolder 
        
        write-Host $response
        
        
        ###########################################
        # Submit the Sqoop job
        ###########################################
        $exportDir = "wasbs://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
        
        $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
                        -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
        $sqoopJob = Start-AzureRmHDInsightJob `
                        -ResourceGroupName $resourceGroupName `
                        -ClusterName $hdinsightClusterName `
                        -HttpCredential $httpCredential `
                        -JobDefinition $sqoopDef #-Debug -Verbose
        
        Wait-AzureRmHDInsightJob `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $HDInsightClusterName `
            -HttpCredential $httpCredential `
            -WaitTimeoutInSeconds 3600 `
            -Job $sqoopJob.JobId
        
        
        Get-AzureRmHDInsightJobOutput `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -HttpCredential $httpCredential `
            -DefaultContainer $existingDefaultBlobContainerName `
            -DefaultStorageAccountName $defaultStorageAccountName `
            -DefaultStorageAccountKey $defaultStorageAccountKey `
            -JobId $sqoopJob.JobId `
            -DisplayOutputType StandardError
        
        ###########################################
        # Delete the cluster
        ###########################################
        Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName

6. Conectar ao seu banco de dados do SQL e ver os atrasos de voos médio por cidade na tabela AvgDelays:

    ![HDI. FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>Apêndice A - os dados de atrasos de voos de carregamento ao armazenamento de Blob do Azure
Carregando o arquivo de dados e os arquivos de script HiveQL (consulte [Apêndice B](#appendix-b)) exige algum planejamento. A ideia é para armazenar os arquivos de dados e o arquivo de HiveQL antes de criar um cluster de HDInsight e executando o trabalho de seção. Você tem duas opções:

- **Use a mesma conta de armazenamento do Azure que será usada pelo cluster HDInsight como o sistema de arquivo padrão.** Porque o cluster HDInsight terão a tecla de acesso da conta de armazenamento, você não precisa faça quaisquer alterações adicionais.
- **Use uma conta diferente de armazenamento do Azure HDInsight cluster padrão do sistema de arquivos.** Se esse for o caso, você deve modificar a parte de criação do script do Windows PowerShell encontrado em [cluster HDInsight criar e executar trabalhos de seção/Sqoop](#runjob) para vincular a conta de armazenamento como uma conta de armazenamento adicional. Para obter instruções, consulte [criar Hadoop clusters no HDInsight][hdinsight-provision]. Cluster HDInsight, em seguida, sabe a tecla de acesso para a conta de armazenamento.

>[AZURE.NOTE] O caminho de armazenamento de Blob para o arquivo de dados é difícil codificado no arquivo de script HiveQL. Você deve atualizá-lo adequadamente.

**Para baixar os dados de voos**

1. Navegue até a [pesquisa e administração de tecnologia inovadora, gabinete de estatísticas de transporte][rita-website].
2. Na página, selecione os seguintes valores:

    <table border="1">
    <tr><th>Nome</th><th>Valor</th></tr>
    <tr><td>Ano de filtro</td><td>2013 </td></tr>
    <tr><td>Filtrar período</td><td>Janeiro</td></tr>
    <tr><td>Campos</td><td>*Ano*, *Datavoo*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *origem*, *OriginCityName*, *OriginState*, *DestAirportID*, *destino*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (Limpar todos os outros campos)</td></tr>
    </table>

3. Clique em **Baixar**.
4. Descompacte o arquivo para a pasta **C:\Tutorials\FlightDelay\2013Data** . Cada arquivo é um arquivo CSV e é aproximadamente 60GB de tamanho.
5.  Renomeie o arquivo para o nome do mês que ele contém dados. Por exemplo, o arquivo que contém os dados de janeiro seria nomeado *January.csv*.
6. Repita as etapas 2 e 5 para baixar um arquivo para cada um dos 12 meses em 2013. Você precisará mínimo de um arquivo para executar o tutorial.  

**Para carregar os dados de atrasos de voos ao armazenamento de Blob do Azure**

1. Prepare os parâmetros:

    <table border="1">
    <tr><th>Nome da variável</th><th>Anotações</th></tr>
    <tr><td>$storageAccountName</td><td>A conta de armazenamento do Azure onde você deseja carregar os dados.</td></tr>
    <tr><td>$blobContainerName</td><td>O contêiner de Blob onde você deseja carregar os dados.</td></tr>
    </table>
2. Abra o PowerShell ISE Azure.
3. Cole o seguinte script no painel script:

        [CmdletBinding()]
        Param(

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #Region - Variables
        $localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
        $destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
        #EndRegion
        
        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #Region - Validate user input
        Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
        # Validate the Storage account
        if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }
        else{
            $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
        }
        
        # Validate the container
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion
        
        #Region - Copy the file from local workstation to Azure Blob storage  
        if (test-path -Path $localFolder)
        {
            foreach ($item in Get-ChildItem -Path $localFolder){
                $fileName = "$localFolder\$item"
                $blobName = "$destFolder/$item"
        
                Write-Host "Copying $fileName to $blobName" -ForegroundColor Green
        
                Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
            }
        }
        else
        {
            Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
        }
        
        # List the uploaded files on HDInsight
        Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
        #EndRegion




4. Pressione **F5** para executar o script.

Se você optar por usar um método diferente para carregar os arquivos, verifique se que o caminho do arquivo é flightdelay/tutoriais/dados. A sintaxe para acessar os arquivos é:

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

Os tutoriais/flightdelay/dados do caminho é a pasta virtual que criou quando você carregou os arquivos. Verifique se há 12 arquivos, um para cada mês.

>[AZURE.NOTE] Você deve atualizar a consulta de ramificação a leitura de um novo local.

> Você deve configurar a permissão de acesso do contêiner seja público ou vincular a conta de armazenamento ao cluster HDInsight. Caso contrário, a cadeia de caracteres de consulta de seção não poderão acessar os arquivos de dados.

---
##<a id="appendix-b"></a>Apêndice B - criar e carregar um script de HiveQL

Usando o PowerShell do Azure, você pode executar várias instruções de HiveQL um por vez ou empacotar a instrução HiveQL em um arquivo de script. Esta seção mostra como criar um script de HiveQL e carregar o script ao armazenamento de Blob do Azure usando o PowerShell do Azure. Seção requer os scripts HiveQL sejam armazenados no armazenamento de Blob do Azure.

O script HiveQL executará o seguinte:

1. **Soltar a tabela de delays_raw**, caso a tabela já existe.
2. **Criar a tabela de ramificação externa delays_raw** apontando para o local de armazenamento de Blob com os arquivos de atraso voos. Essa consulta Especifica que os campos são delimitados por "," e que as linhas são encerradas pelo "\n". Isso apresenta um problema quando valores de campo contêm vírgulas porque a seção não pode diferenciar uma vírgula que é um delimitador de campo e um que faz parte de um valor de campo (que é o caso em valores de campo para origem\_Cidade\_nome e destino\_Cidade\_nome). Para resolver isso, a consulta cria colunas TEMP para manter os dados que incorretamente são divididos em colunas.  
3. **Soltar a tabela de atrasos**, caso a tabela já existe.
4. **Criar a tabela de atrasos**. É útil limpar os dados antes de processamento posterior. Essa consulta cria uma nova tabela, *atrasos*, da tabela delays_raw. Observe que as colunas TEMP (conforme mencionado anteriormente) não são copiadas e que a função **subcadeia de caracteres** é usada para remover as aspas de dados.
5. **Calcule o atraso médio clima e grupos os resultados por nome de cidade.** Ele também produzirá os resultados ao armazenamento de Blob. Observe que a consulta removerá apóstrofos de dados e excluirá linhas onde o valor de **weather_delay** é nulo. Isso é necessário porque Sqoop, usado posteriormente neste tutorial, não processa esses valores normalmente por padrão.

Para obter uma lista completa dos comandos HiveQL, consulte a [Seção linguagem de definição de dados][hadoop-hiveql]. Cada comando HiveQL deve terminar com um ponto e vírgula.

**Para criar um arquivo de script HiveQL**

1. Prepare os parâmetros:

    <table border="1">
    <tr><th>Nome da variável</th><th>Anotações</th></tr>
    <tr><td>$storageAccountName</td><td>A conta de armazenamento do Azure onde você deseja carregar o script HiveQL.</td></tr>
    <tr><td>$blobContainerName</td><td>O contêiner de Blob onde você deseja carregar o script HiveQL.</td></tr>
    </table>
2. Abra o PowerShell ISE Azure.

3. Copie e cole o seguinte script no painel script:

        [CmdletBinding()]
        Param(

            # Azure Blob storage variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #region - Define variables
        # Treat all errors as terminating
        $ErrorActionPreference = "Stop"
        
        # The HiveQL script file is exported as this file before it's uploaded to Blob storage
        $hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"
        
        # The HiveQL script file will be uploaded to Blob storage as this blob name
        $hqlBlobName = "tutorials/flightdelay/flightdelays.hql"
        
        # These two constants are used by the HiveQL script file
        #$srcDataFolder = "tutorials/flightdelay/data"
        $dstDataFolder = "/tutorials/flightdelay/output"
        #endregion

        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #Region - Validate user input
        Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
        # Validate the Storage account
        if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }
        else{
            $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
        }
        
        # Validate the container
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion
        
        #region - Validate the file and file path
        
        # Check if a file with the same file name already exists on the workstation
        Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
        if (test-path $hqlLocalFileName){
        
            $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
        
            if ($isDelete.ToLower() -ne "y")
            {
                Exit
            }
        }
        
        # Create the folder if it doesn't exist
        $folder = split-path $hqlLocalFileName
        if (-not (test-path $folder))
        {
            Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green
        
            new-item $folder -ItemType directory  
        }
        #end region
        
        #region - Write the Hive script into a local file
        Write-Host "`nWriting the Hive script into a file on your workstation ..." `
                    -ForegroundColor Green
        
        $hqlDropDelaysRaw = "DROP TABLE delays_raw;"
        
        $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
                "YEAR string, " +
                "FL_DATE string, " +
                "UNIQUE_CARRIER string, " +
                "CARRIER string, " +
                "FL_NUM string, " +
                "ORIGIN_AIRPORT_ID string, " +
                "ORIGIN string, " +
                "ORIGIN_CITY_NAME string, " +
                "ORIGIN_CITY_NAME_TEMP string, " +
                "ORIGIN_STATE_ABR string, " +
                "DEST_AIRPORT_ID string, " +
                "DEST string, " +
                "DEST_CITY_NAME string, " +
                "DEST_CITY_NAME_TEMP string, " +
                "DEST_STATE_ABR string, " +
                "DEP_DELAY_NEW float, " +
                "ARR_DELAY_NEW float, " +
                "CARRIER_DELAY float, " +
                "WEATHER_DELAY float, " +
                "NAS_DELAY float, " +
                "SECURITY_DELAY float, " +
                "LATE_AIRCRAFT_DELAY float) " +
            "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
            "LINES TERMINATED BY '\n' " +
            "STORED AS TEXTFILE " +
            "LOCATION 'wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';"
        
        $hqlDropDelays = "DROP TABLE delays;"
        
        $hqlCreateDelays = "CREATE TABLE delays AS " +
            "SELECT YEAR AS year, " +
                "FL_DATE AS flight_date, " +
                "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
                "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
                "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
                "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
                "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
                "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
                "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
                "DEST_AIRPORT_ID AS dest_airport_id, " +
                "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
                "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
                "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
                "DEP_DELAY_NEW AS dep_delay_new, " +
                "ARR_DELAY_NEW AS arr_delay_new, " +
                "CARRIER_DELAY AS carrier_delay, " +
                "WEATHER_DELAY AS weather_delay, " +
                "NAS_DELAY AS nas_delay, " +
                "SECURITY_DELAY AS security_delay, " +
                "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
            "FROM delays_raw;"
        
        $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
            "SELECT regexp_replace(origin_city_name, '''', ''), " +
                "avg(weather_delay) " +
            "FROM delays " +
            "WHERE weather_delay IS NOT NULL " +
            "GROUP BY origin_city_name;"
        
        $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal
        
        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force
        #endregion
        
        #region - Upload the Hive script to the default Blob container
        Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green
        
        # Create a storage context object
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        # Upload the file from local workstation to Blob storage
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
        #endregion
        
        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    Aqui estão as variáveis usadas no script:

    - **$hqlLocalFileName** - o script salva o arquivo de script HiveQL localmente antes de carregá-lo ao armazenamento de Blob. Este é o nome do arquivo. O valor padrão é <u>C:\tutorials\flightdelay\flightdelays.hql</u>.
    - **$hqlBlobName** - este é o nome da arquivo de script do HiveQL blob usado no armazenamento de Blob do Azure. O valor padrão é tutorials/flightdelay/flightdelays.hql. Como o arquivo será gravado diretamente para o armazenamento de Blob do Azure, não há uma "/" no início do nome do blob. Se quiser acessar o arquivo do armazenamento de Blob, você precisará adicionar uma "/" no início do nome do arquivo.
    - **$srcDataFolder** e **$dstDataFolder** - = "tutoriais/flightdelay/dados" = "flightdelay/tutoriais/saída"


---
##<a id="appendix-c"></a>Apêndice C - preparar um banco de dados do SQL Azure para a saída de trabalho Sqoop
**Para preparar o banco de dados do SQL (mesclar com o script Sqoop)**

1. Prepare os parâmetros:

    <table border="1">
    <tr><th>Nome da variável</th><th>Anotações</th></tr>
    <tr><td>$sqlDatabaseServerName</td><td>O nome do servidor de banco de dados do SQL Azure. Digite nada para criar um novo servidor.</td></tr>
    <tr><td>$sqlDatabaseUsername</td><td>O nome de logon para o servidor de banco de dados do SQL Azure. Se $sqlDatabaseServerName for um servidor existente, o logon e a senha de logon são usadas para autenticar com o servidor. Caso contrário, eles são usados para criar um novo servidor.</td></tr>
    <tr><td>$sqlDatabasePassword</td><td>A senha de logon para o servidor de banco de dados do SQL Azure.</td></tr>
    <tr><td>$sqlDatabaseLocation</td><td>Esse valor é usado apenas quando você estiver criando um novo servidor de banco de dados Azure.</td></tr>
    <tr><td>$sqlDatabaseName</td><td>Banco de dados SQL usado para criar a tabela de AvgDelays para o trabalho de Sqoop. Deixar em branco, você criará um banco de dados chamado HDISqoop. O nome da tabela para a saída de trabalho Sqoop é AvgDelays. </td></tr>
    </table>
2. Abra o PowerShell ISE Azure.
3. Copie e cole o seguinte script no painel script:

        [CmdletBinding()]
        Param(
        
            # Azure Resource group variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
            [String]$resourceGroupName,
        
            # SQL database server variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
            [String]$sqlDatabaseServer, 
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database admin user.")]
            [String]$sqlDatabaseLogin,
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database admin user password.")]
            [String]$sqlDatabasePassword,
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the region to create the Database in.")]
            [String]$sqlDatabaseLocation,   #For example, West US.
        
            # SQL database variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
            [String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
        )
        
        # Treat all errors as terminating
        $ErrorActionPreference = "Stop"
        
        #region - Constants and variables
        
        # IP address REST service used for retrieving external IP address and creating firewall rules
        [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
        [String]$fireWallRuleName = "FlightDelay"
        
        # SQL database variables
        [String]$sqlDatabaseMaxSizeGB = 10
        
        #SQL query string for creating AvgDelays table
        [String]$sqlDatabaseTableName = "AvgDelays"
        [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [origin_city_name] [nvarchar](50) NOT NULL,
                    [weather_delay] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
                (
                    [origin_city_name] ASC
                )
                )"
        #endregion
        
        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #region - Create and validate Azure resouce group
        try{
            Get-AzureRmResourceGroup -Name $resourceGroupName
        }
        catch{
            New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
        }
        
        #EndRegion
        
        #region - Create and validate Azure SQL database server
        try{
            Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
        catch{
            Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
        
            $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
            $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
        
            $sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
            Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan
        
            Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
            $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
            New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress
        
            #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
            New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
        }
        
        #endregion
        
        #region - Create and validate Azure SQL database
        
        try {
            Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
        }
        catch {
            Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
            New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
        }
        
        #endregion
        
        #region -  Execute an SQL command to create the AvgDelays table
        
        Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()
        
        $conn.close()
        
        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    >[AZURE.NOTE] O script usa um serviço de transferência (REST) representational estado, http://bot.whatismyipaddress.com, para recuperar o endereço IP externo. O endereço IP é usado para criar uma regra de firewall para seu servidor de banco de dados SQL.  

    Aqui estão algumas variáveis usados no script:

    - **$ipAddressRestService** - o valor padrão é http://bot.whatismyipaddress.com. É um endereço IP público serviço REST para obter o endereço IP externo. Se desejar, você pode usar outros serviços. O endereço IP externo recuperado por meio do serviço será usado para criar uma regra de firewall para seu servidor de banco de dados do SQL Azure, para que você possa acessar o banco de dados de sua estação de trabalho (usando um script do Windows PowerShell).
    - **$fireWallRuleName** - esse é o nome da regra de firewall para o servidor de banco de dados do SQL Azure. O nome padrão é <u>FlightDelay</u>. Você pode renomeá-lo se desejar.
    - **$sqlDatabaseMaxSizeGB** - esse valor é usado apenas quando você estiver criando um novo servidor de banco de dados do SQL Azure. O valor padrão é 10GB. 10GB é suficiente para este tutorial.
    - **$sqlDatabaseName** - esse valor é usado apenas quando você estiver criando um novo banco de dados do SQL Azure. O valor padrão é HDISqoop. Se você renomeá-la, você deverá atualizar o script Sqoop Windows PowerShell adequadamente.

4. Pressione **F5** para executar o script.
5. Valide a saída de script. Certificar-se de que o script foi executado com êxito.

##<a id="nextsteps"></a>Próximas etapas
Agora você compreender como carregar um arquivo para armazenamento de Blob do Azure, como preencher uma tabela de seção usando os dados do armazenamento de Blob do Azure, como executar consultas de seção e como usar Sqoop para exportar dados do HDFS para um banco de dados do SQL Azure. Para saber mais, consulte os seguintes artigos:

* [Introdução ao HDInsight][hdinsight-get-started]
* [Use a seção com HDInsight][hdinsight-use-hive]
* [Usar Oozie com HDInsight][hdinsight-use-oozie]
* [Usar Sqoop com HDInsight][hdinsight-use-sqoop]
* [Usar porco com HDInsight][hdinsight-use-pig]
* [Desenvolver programas Java MapReduce para HDInsight][hdinsight-develop-mapreduce]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[powershell-install-configure]: powershell-install-configure.md

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
