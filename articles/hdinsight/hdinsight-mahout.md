<properties
    pageTitle="Gerar recomendações usando Mahout e baseado no WIndows HDInsight | Microsoft Azure"
    description="Saiba como usar a biblioteca de aprendizado de máquina Apache Mahout para gerar recomendações de filme com baseado no Windows HDInsight (Hadoop)."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

#<a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight"></a>Gerar recomendações de filme usando Apache Mahout com Hadoop em HDInsight

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Saiba como usar a biblioteca com Azurehdinsight de aprendizado de máquina [Apache Mahout](http://mahout.apache.org) para gerar recomendações de filme.

> [AZURE.NOTE] As etapas neste documento exigem um cliente do Windows e um cluster baseado no Windows HDInsight. Para obter informações sobre como usar Mahout um Linux, OS X ou cliente Unix, com um cluster baseado no Linux HDInsight, consulte [recomendações de filme gerar usando Apache Mahout com baseado em Linux Hadoop em HDInsight](hdinsight-hadoop-mahout-linux-mac.md)


##<a name="learn"></a>O que você aprenderá

Mahout é um [aprendizado de máquina] [ ml] biblioteca para Apache Hadoop. Mahout contém algoritmos para processar dados, como filtragem, classificação e agrupamento. Neste artigo, você vai usar um mecanismo de recomendação para gerar recomendações de filme que são baseadas no filmes que seus amigos viu. Você também aprenderá como realizar classificações com uma floresta de decisão. Isto irá ensinam a seguir:

* Como executar trabalhos de Mahout usando o Windows PowerShell

* Como executar trabalhos de Mahout da linha de comando Hadoop

* Como instalar Mahout em clusters HDInsight 3.0 e HDInsight 2.0

    > [AZURE.NOTE] Mahout é fornecido com a versão 3.1 HDInsight dos clusters. Se você estiver usando uma versão anterior do HDInsight, consulte [Instalar Mahout](#install) antes de continuar.

##<a name="prerequisites"></a>pré-requisitos

- **Cluster baseado em um Windows Hadoop em HDInsight**. Para obter informações sobre a criação de um, consulte [Introdução ao uso Hadoop em HDInsight][getstarted]
- **Uma estação de trabalho com o PowerShell do Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


##<a name="recommendations"></a>Gerar recomendações usando o Windows PowerShell

> [AZURE.NOTE] Embora o trabalho usados nesta seção funciona usando o Windows PowerShell, muitas das classes fornecidas com Mahout atualmente não funcionam com o Windows PowerShell, e eles devem ser executados usando a linha de comando do Hadoop. Para obter uma lista de classes que não funcionam com o Windows PowerShell, consulte a seção de [solução de problemas](#troubleshooting) .
>
> Para obter um exemplo de como usar a linha de comando Hadoop para executar trabalhos de Mahout, consulte [dados de classificação usando a linha de comando do Hadoop](#classify).

Uma das funções fornecidos pelo Mahout é um mecanismo de recomendação. Este mecanismo aceita dados no formato de `userID`, `itemId`, e `prefValue` (a preferência de usuários para o item). Mahout pode executar análise de ocorrência co para determinar: _usuários que tenham uma preferência de um item também tem uma preferência para esses outros itens_. Mahout determinará usuários com preferências de tipo de item, que podem ser usados para fazer recomendações.

Este é um exemplo muito simple que usa filmes:

* __Co ocorrência__: Jorge, Alice e Paulo curtiu _estrela conflitos_, _O Empire greves Back_e _retornar do Jedi_. Mahout determina que os usuários que desejarem qualquer um desses filmes também como os outros dois.

* __Co ocorrência__: Paulo e Alice também curtiram _A ameaça fantasma_, _ataque dos Clones_e _Vingança da Sith_. Mahout determina que os usuários que curtiram os três filmes anterior também como esses três.

* __Recomendação de similaridade__: Jorge porque curtiu os três primeiros filmes, Mahout examina filmes que outras pessoas com as preferências de semelhantes gostou, mas José não viu (curtiu/classificado). Nesse caso, Mahout recomenda _O fantasma ameaça_, _ataque dos Clones_e _Vingança da Sith_.

###<a name="understanding-the-data"></a>Noções básicas sobre os dados

Convenientemente, [Pesquisa de GroupLens] [ movielens] fornece dados de classificação para filmes em um formato compatível com Mahout. Esses dados estão disponíveis no armazenamento de padrão do seu cluster em `/HdiSamples/MahoutMovieData`.

Existem dois arquivos, `moviedb.txt` (informações sobre os filmes,) e `user-ratings.txt`. O arquivo de ratings.txt de usuário é usado durante a análise, enquanto moviedb.txt é usado para fornecer informações de texto amigável quando exibindo os resultados da análise.

Os dados contidos em ratings.txt de usuário tem uma estrutura de `userID`, `movieID`, `userRating`, e `timestamp`, que informa como altamente cada usuário classificada como um filme. Aqui está um exemplo de dados:


    196 242 3   881250949
    186 302 3   891717742
    22  377 1   878887116
    244 51  2   880606923
    166 346 1   886397596

###<a name="run-the-job"></a>Executar o trabalho

Use o seguinte script do Windows PowerShell para executar um trabalho que usa o mecanismo de recomendação de Mahout com os dados de filme:

    # The HDInsight cluster name.
    $clusterName = "the cluster name"
    
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
            
    # NOTE: The version number in the file path
    # may change in future versions of HDInsight.
    $jarFile =  "file:///C:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar"
    #
    # If you are using an earlier version of HDInsight,
    # set $jarFile to the jar file you
    # uploaded.
    # For example,
    # $jarFile = "wasbs:///example/jars/mahout-core-0.9-job.jar"

    # The arguments for this job
    # * input - the path to the data uploaded to HDInsight
    # * output - the path to store output data
    # * tempDir - the directory for temp files
    $jobArguments = "--similarityClassname", "recommenditembased", `
                    "-s", "SIMILARITY_COOCCURRENCE", `
                    "--input", "wasbs:///HdiSamples/MahoutMovieData/user-ratings.txt",
                    "--output", "wasbs:///example/out",
                    "--tempDir", "wasbs:///example/temp"

    # Create the job definition
    $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
      -JarFile $jarFile `
      -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
      -Arguments $jobArguments

    # Start the job
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    # Wait on the job to complete
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
            -Blob example/out/part-r-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
            
    # Write out any error information
    Write-Host "STDERR"
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

> [AZURE.NOTE] Mahout trabalhos não remova dados temporários criados durante o processamento da tarefa. O `--tempDir` parâmetro for especificado no trabalho de exemplo para isolar os arquivos temporários em um diretório específico.

O trabalho de Mahout não retornar a saída para STDOUT. Em vez disso, ele armazena no diretório de saída especificado como __parte-r-00000__. O script downloads este arquivo para __txt__ no diretório atual na estação de trabalho.

A seguir é um exemplo do conteúdo deste arquivo:

    1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

A primeira coluna é o `userID`. Os valores contidos em ' [' e ']' são `movieId`:`recommendationScore`.

###<a name="view-the-output"></a>Exibir a saída

Embora a saída gerada possa estar Okey para uso em um aplicativo, não é muito legível. O `moviedb.txt` do servidor podem ser usados para resolver o `movieId` a um filme nome, mas você deve primeiro baixar ele e o arquivo de classificação do servidor usando o seguinte script:

    # The HDInsight cluster name.
    $clusterName = "the cluster name"
    
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
    #Download the files
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/moviedb.txt" `
    -Container $container `
    -Destination moviedb.txt `
    -Context $context
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/user-ratings.txt" `
    -Container $container `
    -Destination user-ratings.txt `
    -Context $context

Depois que você baixou os arquivos, use o seguinte script do PowerShell para exibir recomendações com nomes de filme:

    <#
    .SYNOPSIS
        Displays recommendations for movies.
    .DESCRIPTION
        Displays recommendations generated by Mahout
        with HDInsight example in a human readable format.
    .EXAMPLE
        .\Show-Recommendation -userId 4
            -userDataFile "user-ratings.txt"
            -movieFile "moviedb.txt"
            -recommendationFile "output.txt"
    #>

    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
        #The user ID
        [Parameter(Mandatory = $true)]
        [String]$userId,

        [Parameter(Mandatory = $true)]
        [String]$userDataFile,

        [Parameter(Mandatory = $true)]
        [String]$movieFile,

        [Parameter(Mandatory = $true)]
        [String]$recommendationFile
    )
    # Read movie ID & description into hash table
    Write-Host "Reading movies descriptions" -ForegroundColor Green
    $movieById = @{}
    foreach($line in Get-Content $movieFile)
    {
        $tokens = $line.Split("|")
        $movieById[$tokens[0]] = $tokens[1]
    }
    # Load movies user has already seen (rated)
    # into a hash table
    Write-Host "Reading rated movies" -ForegroundColor Green
    $ratedMovieIds = @{}
    foreach($line in Get-Content $userDataFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            # Resolve the ID to the movie name
            $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
        }
    }
    # Read recommendations generated by Mahout
    Write-Host "Reading recommendations" -ForegroundColor Green
    $recommendations = @{}
    foreach($line in get-content $recommendationFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            #Trim leading/treailing [] and split at ,
            $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
            foreach($movieIdAndScore in $movieIdAndScores)
            {
                #Split at : and store title and score in a hash table
                $idAndScore = $movieIdAndScore.Split(":")
                $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
            }
            break
        }
    }

    Write-Host "Rated movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                   @{Expression={$_.Value};Label="Rating"}
    $ratedMovieIds | format-table $ratedFormat
    Write-Host "---------------------------" -ForegroundColor Green

    write-host "Recommended movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                            @{Expression={$_.Value};Label="Score"}
    $recommendations | format-table $recommendationFormat

A seguir é um exemplo da execução do script:

    PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt

A saída deve aparecer semelhante ao seguinte:

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237  

##<a name="classify"></a>Classificar dados usando a linha de comando do Hadoop

Um dos métodos classificação disponíveis com Mahout é criar uma [floresta aleatória][forest]. Este é um processo de etapa de vários que envolve usando dados de treinamento para gerar árvores de decisão, que são usadas para classificar os dados. Usa a classe de __org.apache.mahout.classifier.df.tools.Describe__ fornecida pelo Mahout. No momento deve ser executado usando a linha de comando do Hadoop.

###<a name="load-the-data"></a>Carregar os dados

1. Baixe os arquivos a seguir o NSL KDD no [conjunto](http://nsl.cs.unb.ca/NSL-KDD/)de dados.

  * [KDDTrain +. ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff): o arquivo de treinamento

  * [KDDTest +. ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff): os dados de teste

2. Abrir cada arquivo e remova as linhas na parte superior que começam com '@', e, em seguida, salve os arquivos. Se elas não são removidas, você receberá mensagens de erro ao usar esses dados com Mahout.

2. Carregue os arquivos de __dados do exemplo__. Você pode fazer isso usando o script a seguir. Substitua o nome do cluster HDInsight __CLUSTERNAME__ . Substitua o nome do arquivo com o nome fo o arquivo a ser carregado.

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName="CLUSTERNAME"
        $fileToUpload="FILENAME"
        $blobPath="example/data/FILENAME"
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
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context

###<a name="run-the-job"></a>Executar o trabalho

1. Este trabalho requer a linha de comando do Hadoop. Habilitar a área de trabalho remota para o cluster HDInsight, em seguida, conectá-la seguindo as instruções em [conectar ao HDInsight clusters usando o RDP](hdinsight-administer-use-management-portal.md#rdp).

3. Depois de conectar, use o ícone de __linha de comando do Hadoop__ para abrir a linha de comando do Hadoop:

    ![Hadoop cli][hadoopcli]

3. Use o seguinte comando para gerar o descritor de arquivo (__KDDTrain +. Info__), que usa Mahout.

        hadoop jar "c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasbs:///example/data/KDDTrain+.arff" -f "wasbs:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

    O `N 3 C 2 N C 4 N C 8 N 2 C 19 N L` descreve os atributos dos dados no arquivo. Por exemplo, L indica um rótulo.

4. Crie uma floresta de árvores de decisão usando o seguinte comando:

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasbs:///example/data/KDDTrain+.arff -ds wasbs:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    A saída dessa operação é armazenada no diretório __nsl-floresta__ , que está localizado no armazenamento para o seu cluster HDInsight em _ wasbs://user/&lt;nome de usuário > / nsl-floresta/nsl-forest.seq. O &lt;nome de usuário > é o nome de usuário que você usou para sua sessão de área de trabalho remota. Este arquivo não é legível por humanos.

5. Teste da floresta classificando dataset __KDDTest + .arff__ . Use o seguinte comando:

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasbs:///example/data/KDDTest+.arff -ds wasbs:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasbs:///example/data/predictions

    Esse comando retorna informações resumidas sobre o processo de classificação similar ao seguinte:

        14/07/02 14:29:28 INFO mapreduce.TestForest:

        =======================================================
        Summary
        -------------------------------------------------------
        Correctly Classified Instances          :      17560       77.8921%
        Incorrectly Classified Instances        :       4984       22.1079%
        Total Classified Instances              :      22544

        =======================================================
        Confusion Matrix
        -------------------------------------------------------
        a       b       <--Classified as
        9437    274      |  9711        a     = normal
        4710    8123     |  12833       b     = anomaly

        =======================================================
        Statistics
        -------------------------------------------------------
        Kappa                                       0.5728
        Accuracy                                   77.8921%
        Reliability                                53.4921%
        Reliability (standard deviation)            0.4933

  Este trabalho também produz um arquivo localizado em __wasbs:///example/data/predictions/KDDTest+.arff.out__. No entanto, este arquivo não é legível por humanos.

> [AZURE.NOTE] Mahout trabalhos não substituem os arquivos. Se você quiser executar esses trabalhos novamente, você deve excluir os arquivos que foram criados pelos trabalhos anteriores.

##<a name="troubleshooting"></a>Solução de problemas

###<a name="install"></a>Instalar o Mahout

Mahout é instalado em HDInsight 3.1 clusters, e ele pode ser instalado manualmente em HDInsight 3.0 ou HDInsight 2.1 clusters, usando as seguintes etapas:

1. A versão do Mahout usar depende da versão de HDInsight do seu cluster. Você pode encontrar a versão de cluster exibindo as propriedades para o cluster no portal do Azure.

  * __Para HDInsight 2.1__, você pode baixar um arquivo de Java Archive (JAR) que contém [Mahout 0,9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar).

  * __Para o HDInsight 3.0__, você deve [criar Mahout da origem] [ build] e especificar a versão de Hadoop fornecida pelo HDInsight. Instalar os pré-requisitos listados na página de compilação, baixe a fonte e, em seguida, use o seguinte comando para criar os arquivos de jar Mahout:

            mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

        After the build completes, you can find the JAR file at __mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__.

        > [AZURE.NOTE] Quando Mahout 1.0 é lançado, você deve ser capaz de usar os pacotes pré-criadas com HDInsight 3.0.

2. Carregar o arquivo jar no __exemplo/jars__ no armazenamento padrão para o seu cluster. Substitua CLUSTERNAME no script a seguir com o nome do seu cluster HDInsight e FILENAME com o caminho para o arquivo __mahout-coure-0,9-job.jar__ ..

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName = "CLUSTERNAME"
        $fileToUpload = "FILENAME"
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
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/jars/mahout-core-0.9-job.jar" `
            -Container $container `
            -Context $context

###<a name="cannot-overwrite-files"></a>Não é possível substituir arquivos

Faça trabalhos de mahout não limpar os arquivos temporários que foram criados durante o processamento. Além disso, os trabalhos não substituirá um arquivo de saída existente.

Para evitar erros durante a execução de trabalhos de Mahout, excluir arquivos temporários e de saída entre as execuções ou usar nomes de diretório temporário e saída exclusivo.

###<a name="cannot-find-the-jar-file"></a>Não consigo encontrar o arquivo JAR

HDInsight 3.1 clusters incluem Mahout. O nome de arquivo e caminho incluir o número de versão de Mahout que esteja instalada em cluster. O script de exemplo do Windows PowerShell neste tutorial usa um caminho que é válido a partir de novembro de 2015, mas o número da versão será alterado no futuro atualizações para HDInsight. Para determinar o caminho atual para o arquivo JAR Mahout para o seu cluster, use o seguinte comando do Windows PowerShell e, em seguida, modifique o script para fazer referência o caminho do arquivo retornado:

    Use-AzureRmHDInsightCluster -ClusterName $clusterName
    #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "wasbs:///example/statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'

###<a name="nopowershell"></a>Classes que não funcionam com o Windows PowerShell

Trabalhos de mahout que usam as classes a seguir retornam uma variedade de mensagens de erro se forem usados no Windows PowerShell:

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Para executar trabalhos que usam essas classes, conectar-se ao cluster HDInsight e executar os trabalhos usando a linha de comando do Hadoop. Ver os [dados de classificação usando a linha de comando do Hadoop](#classify) para obter um exemplo.

##<a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como usar Mahout, descubra outras maneiras de trabalhar com dados em HDInsight:

* [Seção com HDInsight](hdinsight-use-hive.md)
* [Porco com HDInsight](hdinsight-use-pig.md)
* [MapReduce com HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: ../powershell-install-configure.md
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 
