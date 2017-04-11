<properties
    pageTitle="Analisar dados de Twitter com Hadoop em HDInsight | Microsoft Azure"
    description="Aprenda a usar seção para analisar dados de Twitter em Hadoop em HDInsight para localizar a frequência de uso de uma determinada palavra."
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

# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analisar dados de Twitter usando a seção no HDInsight

Sites sociais são um dos principais motivadores para adoção de grandes de dados. APIs públicas fornecidos pelo sites como Twitter são uma fonte útil de dados para analisar e Noções básicas sobre tendências populares. Neste tutorial, você irá obter tweets usando um Twitter streaming API e use Apache seção em Azurehdinsight para obter uma lista de usuários do Twitter quem enviou as maioria dos tweets que continha uma determinada palavra.

> [AZURE.NOTE] As etapas neste documento exigem um cluster baseado no Windows HDInsight. Para obter etapas específicas a um cluster baseado em Linux, consulte [Twitter analisar dados usando a seção no HDInsight (Linux)](hdinsight-analyze-twitter-data-linux.md).


##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma estação de trabalho** com o Azure PowerShell instalado e configurado. 

    Para executar scripts do Windows PowerShell, você deve executar o Azure PowerShell como administrador e defina a política de execução para *RemoteSigned*. Consulte [scripts do Windows PowerShell executar][powershell-script].

    Antes de executar scripts do Windows PowerShell, verifique se que você está conectado à sua assinatura do Azure usando o seguinte cmdlet:

        Login-AzureRmAccount

    Se você tiver várias assinaturas Azure, use o cmdlet a seguir para configurar a assinatura atual:

        Select-AzureRmSubscription -SubscriptionID <Azure Subscription ID>

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Azurehdinsight um cluster**. Para obter instruções sobre cluster de provisionamento, consulte [começar a usar o HDInsight] [ hdinsight-get-started] ou [clusters provisionar HDInsight] [hdinsight-provision]. Você precisará do nome do cluster posteriormente no tutorial.

A tabela a seguir lista os arquivos usados neste tutorial:

Arquivos|Descrição
---|---
/Tutorials/Twitter/data/TWEETS.txt|Os dados de origem para o trabalho de seção.
/Tutorials/Twitter/Output|A pasta de saída para o trabalho de seção. O nome do arquivo de saída do trabalho de seção de padrão é **000000_0**.
Tutorials/Twitter/Twitter.HQL|O arquivo de script HiveQL.
/Tutorials/Twitter/JobStatus|O status do trabalho de Hadoop.


##<a name="get-twitter-feed"></a>Twitter obter feed

Neste tutorial, você usará o [Twitter APIs streaming][twitter-streaming-api]. Específicos do Twitter streaming API você usará [é/filtro de status][twitter-statuses-filter].

>[AZURE.NOTE] Um arquivo contendo 10.000 tweets e o arquivo de script da seção (abordados na próxima seção) tenham sido carregados em um contêiner de Blob público. Você pode pular esta seção se você quiser usar os arquivos carregados.

[TWEETS dados](https://dev.twitter.com/docs/platform-objects/tweets) são armazenados no formato de notação JSON (JavaScript Object) que contém uma estrutura aninhada complexa. Em vez de gravar várias linhas de código usando uma linguagem de programação convencional, você pode transformar essa estrutura aninhada em uma tabela de ramificação, para que ele pode ser consultado por uma Structured Query Language (SQL)-como idioma chamado HiveQL.

Twitter usa OAuth para fornecer acesso autorizado à sua API. OAuth é um protocolo de autenticação que permite aos usuários aprovar aplicativos para agir em seu nome sem compartilhar sua senha. Mais informações podem ser encontradas no [oauth.net](http://oauth.net/) ou no [Guia do Iniciante para OAuth](http://hueniverse.com/oauth/) excelente de Hueniverse.

A primeira etapa para usar OAuth é criar um novo aplicativo no site do desenvolvedor do Twitter.

**Para criar um aplicativo do Twitter**

1. Entrar no [https://apps.twitter.com/](https://apps.twitter.com/). Clique no link **Inscreva-se agora** se você não tiver uma conta do Twitter.
2. Clique em **Criar novo aplicativo**.
3. Insira o **nome**, **Descrição**, **site**. Você pode tornar uma URL para o campo **site** . A tabela a seguir mostra alguns valores de exemplo para usar:

    Campo|Valor
    ---|---
    Nome|MyHDInsightApp
    Descrição|MyHDInsightApp
    Site|http://www.myhdinsightapp.com

4. Marque **Sim, concordo**e clique em **criar seu aplicativo do Twitter**.
5. Clique na guia **permissões** . A permissão padrão é **somente leitura**. Isso é suficiente para este tutorial.
6. Clique na guia **chaves e Tokens de acesso** .
7. Clique em **Criar minha token de acesso**.
8. Clique em **Teste OAuth** no canto superior direito da página.
9. Anote a **chave do consumidor**, **segredo do consumidor**, **token de acesso**e **segredo de token de acesso**. Você precisará os valores mais tarde no tutorial.

Neste tutorial, você usará o Windows PowerShell para fazer com que o serviço web de chamadas. Para uma amostra .NET c#, consulte [análise em tempo real Twitter sentimento com HBase em HDInsight][hdinsight-hbase-twitter-sentiment]. A ferramenta popular para fazer chamadas de serviço web é [*Curl*][curl]. Ondulação pode ser obtida [aqui][curl-download].

>[AZURE.NOTE] Quando você usa o comando ondulação no Windows, use aspas duplas em vez de aspas simples para os valores de opção.

**Para obter tweets**

1. Abra o Windows PowerShell script ambiente integrado (ISE). (Na tela inicial do Windows 8, digite **PowerShell_ISE** e clique em **Windows PowerShell ISE**. Consulte [Iniciar o Windows PowerShell no Windows 8 e Windows][powershell-start].)

2. Copie o seguinte script no painel script:

        #region - variables and constants
        $clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name

        # Enter the OAuth information for your Twitter application
        $oauth_consumer_key = "<TwitterAppConsumerKey>";
        $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
        $oauth_token = "<TwitterAppAccessToken>";
        $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

        $destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob.

        $trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
        $track = [System.Uri]::EscapeDataString($trackString);
        $lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
        #endregion

        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Login-AzureRmAccount
        #endregion

        #region - Create a block blob object for writing tweets into Blob storage
        Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
        $myCluster = Get-AzureRmHDInsightCluster -Name $clusterName
        $resourceGroupName = $myCluster.ResourceGroup
        $storageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
        $containerName = $myCluster.DefaultStorageContainer
        Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
        Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

        Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
        $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
        Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow

        Write-Host "Create block blob object ..." -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($containerName)
        $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
        #end region

        # region - Format OAuth strings
        Write-Host "Format oauth strings ..." -ForegroundColor Green
        $oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
        $ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
        $oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

        $signature = "POST&";
        $signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
        $signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
        $signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
        $signature += [System.Uri]::EscapeDataString("track=" + $track);

        $signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

        $hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
        $hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
        $oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

        $oauth_authorization = 'OAuth ';
        $oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
        $oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
        $oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
        $oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
        $oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
        $oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
        $oauth_authorization += 'oauth_version="1.0"';

        $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track);
        #endregion

        #region - Read tweets
        Write-Host "Create HTTP web request ..." -ForegroundColor Green
        [System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
        $request.Method = "POST";
        $request.Headers.Add("Authorization", $oauth_authorization);
        $request.ContentType = "application/x-www-form-urlencoded";
        $body = $request.GetRequestStream();

        $body.write($post_body, 0, $post_body.length);
        $body.flush();
        $body.close();
        $response = $request.GetResponse() ;

        Write-Host "Start stream reading ..." -ForegroundColor Green

        Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream

        $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

        $inrec = $sReader.ReadLine()
        $count = 0
        while (($inrec -ne $null) -and ($count -le $lineMax))
        {
            if ($inrec -ne "")
            {
                Write-Host "`n`t $count tweets received." -ForegroundColor Yellow

                $writeStream.WriteLine($inrec)
                $count ++
            }

            $inrec=$sReader.ReadLine()
        }
        #endregion

        #region - Write tweets to Blob storage
        Write-Host "Write to the destination blob ..." -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $destBlob.UploadFromStream($memStream)

        $sReader.close()
        #endregion

        Write-Host "Completed!" -ForegroundColor Green

3. Defina o primeiro variáveis de cinco a oito no script:


    Variável|Descrição
    ---|---
    $clusterName|Este é o nome do cluster HDInsight onde você deseja executar o aplicativo.
    $oauth_consumer_key|Este é o aplicativo Twitter **chave do consumidor** que você anotou anteriormente quando você criou o aplicativo do Twitter.
    $oauth_consumer_secret|Este é o aplicativo Twitter **segredo do consumidor** , que você anotou anteriormente.
    $oauth_token|Este é o aplicativo Twitter **token de acesso** , que você anotou anteriormente.
    $oauth_token_secret|Este é o aplicativo Twitter **segredo de token de acesso** , que você anotou anteriormente.
    $destBlobName|Este é o nome do blob de saída. O valor padrão é **tutorials/twitter/data/tweets.txt**. Se você alterar o valor padrão, você precisará atualizar os scripts do Windows PowerShell adequadamente.
    $trackString|O serviço web retornará tweets relacionados a essas palavras-chave. O valor padrão é **Azure, nuvem, HDInsight**. Se você alterar o valor padrão, você atualizará os scripts do Windows PowerShell adequadamente.
    $lineMax|O valor determina quantos tweets lerá o script. Ele leva cerca de três minutos para ler 100 tweets. Você pode definir um número maior, mas levará mais tempo de download.

5. Pressione **F5** para executar o script. Se você enfrentar problemas, como alternativa, selecione todas as linhas e, em seguida, pressione **F8**.
6. Você deverá ver "Complete!" no final da saída. Quaisquer mensagens de erro serão exibidas em vermelho.

Como um procedimento de validação, você pode verificar o arquivo de saída, **/tutorials/twitter/data/tweets.txt**, seu armazenamento de Blob do Azure usando um navegador de armazenamento do Azure ou Azure PowerShell. Para um script do Windows PowerShell de amostra para listar arquivos, consulte o [armazenamento de Blob de uso com HDInsight][hdinsight-storage-powershell].



##<a name="create-hiveql-script"></a>Criar script de HiveQL

Usando o PowerShell do Azure, você pode executar várias instruções de HiveQL um por vez ou empacotar a instrução HiveQL em um arquivo de script. Neste tutorial, você irá criar um script de HiveQL. O arquivo de script deve ser carregado ao armazenamento de Blob do Azure. Na próxima seção, você executará o arquivo de script usando o PowerShell do Azure.

>[AZURE.NOTE] O arquivo de script da seção e um arquivo que contém 10.000 tweets tenham sido carregados em um contêiner de Blob público. Você pode pular esta seção se você quiser usar os arquivos carregados.

O script HiveQL executará o seguinte:

1. **Soltar a tabela de tweets_raw** caso a tabela já existe.
2. **Criar a tabela de seção tweets_raw**. Essa seção temporária tabela estruturada mantém os dados para posterior extrair, transformar e carregar o processamento de (ETL). Para obter informações sobre partições, consulte a [seção tutorial][apache-hive-tutorial].  
3. **Carregar dados** da pasta de origem, /tutorials/twitter/data. O conjunto de dados grande tweets no formato JSON aninhado agora tem foram transformado em uma estrutura de tabela de seção temporária.
3. **Solte a tabela tweets** caso a tabela já existe.
4. **Criar a tabela tweets**. Antes da consulta contra o dataset tweets usando ramificação, você precisa executar outro processo ETL. Esse processo ETL define um esquema de tabela mais detalhado para os dados que você armazenou na tabela "twitter_raw".  
5. **Inserir tabela de substituição**. Este script de seção complexo iniciará um conjunto de trabalhos MapReduce longos pelo cluster Hadoop. Dependendo do seu conjunto de dados e o tamanho do cluster, isso pode levar cerca de 10 minutos.
6. **Inserir substituir o diretório**. Executar uma consulta e o conjunto de dados para um arquivo de saída. Essa consulta retornará uma lista de usuários do Twitter quem enviou tweets maioria dos que contêm a palavra "Azure".

**Para criar um script de seção e carregue-o no Azure**

1. Abra o Windows PowerShell ISE.
2. Copie o seguinte script no painel script:

        #region - variables and constants
        $clusterName = "<Existing HDInsight Cluster Name>" # Enter your HDInsight cluster name
        $subscriptionID = "<Azure Subscription ID>"
        
        $sourceDataPath = "/tutorials/twitter/data"
        $outputPath = "/tutorials/twitter/output"
        $hqlScriptFile = "tutorials/twitter/twitter.hql"
        
        $hqlStatements = @"
        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        
        DROP TABLE tweets_raw;
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '$sourceDataPath';
        
        DROP TABLE tweets;
        CREATE TABLE tweets
        (
            id BIGINT,
            created_at STRING,
            created_at_date STRING,
            created_at_year STRING,
            created_at_month STRING,
            created_at_day STRING,
            created_at_time STRING,
            in_reply_to_user_id_str STRING,
            text STRING,
            contributors STRING,
            retweeted STRING,
            truncated STRING,
            coordinates STRING,
            source STRING,
            retweet_count INT,
            url STRING,
            hashtags array<STRING>,
            user_mentions array<STRING>,
            first_hashtag STRING,
            first_user_mention STRING,
            screen_name STRING,
            name STRING,
            followers_count INT,
            listed_count INT,
            friends_count INT,
            lang STRING,
            user_location STRING,
            time_zone STRING,
            profile_image_url STRING,
            json_response STRING
        );
        
        FROM tweets_raw
        INSERT OVERWRITE TABLE tweets
        SELECT
            cast(get_json_object(json_response, '$.id_str') as BIGINT),
            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
            substr (get_json_object(json_response, '$.created_at'),27,4)),
            substr (get_json_object(json_response, '$.created_at'),27,4),
            case substr (get_json_object(json_response, '$.created_at'),5,3)
                when "Jan" then "01"
                when "Feb" then "02"
                when "Mar" then "03"
                when "Apr" then "04"
                when "May" then "05"
                when "Jun" then "06"
                when "Jul" then "07"
                when "Aug" then "08"
                when "Sep" then "09"
                when "Oct" then "10"
                when "Nov" then "11"
                when "Dec" then "12" end,
            substr (get_json_object(json_response, '$.created_at'),9,2),
            substr (get_json_object(json_response, '$.created_at'),12,8),
            get_json_object(json_response, '$.in_reply_to_user_id_str'),
            get_json_object(json_response, '$.text'),
            get_json_object(json_response, '$.contributors'),
            get_json_object(json_response, '$.retweeted'),
            get_json_object(json_response, '$.truncated'),
            get_json_object(json_response, '$.coordinates'),
            get_json_object(json_response, '$.source'),
            cast (get_json_object(json_response, '$.retweet_count') as INT),
            get_json_object(json_response, '$.entities.display_url'),
            array(
                trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
            array(
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            get_json_object(json_response, '$.user.screen_name'),
            get_json_object(json_response, '$.user.name'),
            cast (get_json_object(json_response, '$.user.followers_count') as INT),
            cast (get_json_object(json_response, '$.user.listed_count') as INT),
            cast (get_json_object(json_response, '$.user.friends_count') as INT),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response
        WHERE (length(json_response) > 500);
        
        INSERT OVERWRITE DIRECTORY '$outputPath'
        SELECT name, screen_name, count(1) as cc
            FROM tweets
            WHERE text like "%Azure%"
            GROUP BY name,screen_name
            ORDER BY cc DESC LIMIT 10;
        "@
        #endregion
        
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        
        Try{
            Get-AzureRmSubscription
        }
        Catch{
            Login-AzureRmAccount
        }
        
        Select-AzureRmSubscription -SubscriptionId $subscriptionID
        
        #endregion
        
        #region - Create a block blob object for writing the Hive script file
        Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
        $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroupName = $myCluster.ResourceGroup
        $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
        $defaultBlobContainerName = $myCluster.DefaultStorageContainer
        Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
        Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow
        
        Write-Host "Define the connection string ..." -ForegroundColor Green
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
        
        Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
        $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)
        
        Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream
        $writeStream.Writeline($hqlStatements)
        #endregion
        
        #region - Write the Hive script file to Blob storage
        Write-Host "Write to the destination blob ... " -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $hqlScriptBlob.UploadFromStream($memStream)
        #endregion
        
        Write-Host "Completed!" -ForegroundColor Green

        

4. Defina as primeiras duas variáveis no script:

    Variável|Descrição
    ---|---
    $clusterName|Insira o nome do cluster HDInsight onde você deseja executar o aplicativo.
    $subscriptionID|Digite sua ID do Azure assinatura.
    $sourceDataPath|O local de armazenamento de Blob do Azure onde as consultas de seção lerá os dados de. Você não precisa alterar essa variável.
    $outputPath|O local de armazenamento de Blob do Azure onde as consultas de seção produzirá os resultados. Você não precisa alterar essa variável.
    $hqlScriptFile|O local e o nome de arquivo do arquivo de script HiveQL. Você não precisa alterar essa variável.

5. Pressione **F5** para executar o script. Se você enfrentar problemas, como alternativa, selecione todas as linhas e, em seguida, pressione **F8**.
6. Você deverá ver "Complete!" no final da saída. Quaisquer mensagens de erro serão exibidas em vermelho.

Como um procedimento de validação, você pode verificar o arquivo de saída, **/tutorials/twitter/twitter.hql**, seu armazenamento de Blob do Azure usando um navegador de armazenamento do Azure ou Azure PowerShell. Para um script do Windows PowerShell de amostra para listar arquivos, consulte o [armazenamento de Blob de uso com HDInsight][hdinsight-storage-powershell].  


##<a name="process-twitter-data-by-using-hive"></a>Processar dados do Twitter usando seção

Você concluiu todo o trabalho de preparação. Agora, você pode chamar o script de seção e verificar os resultados.

### <a name="submit-a-hive-job"></a>Enviar um trabalho de seção

Use o seguinte script do Windows PowerShell para executar o script de seção. Você precisará definir a primeira variável.

>[AZURE.NOTE] Para usar os tweets e o script HiveQL carregado nas duas últimas seções, defina $hqlScriptFile como "/ tutorials/twitter/twitter.hql". Para usar os que tenham sido carregados para um blob pública para você, defina $hqlScriptFile para "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql".

    #region variables and constants
    $clusterName = "<Existing Azure HDInsight Cluster Name>"
    $httpUserName = "admin"
    $httpUserPassword = "<HDInsight Cluster HTTP User Password>"
    
    #use one of the following
    $hqlScriptFile = "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
    $hqlScriptFile = "/tutorials/twitter/twitter.hql"
    
    $statusFolder = "/tutorials/twitter/jobstatus"
    #endregion
    
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    
    
    #region - Invoke Hive
    Write-Host "Invoke Hive ... " -ForegroundColor Green
    
    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpUserPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
    
    Use-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $httpCredential 
    $response = Invoke-AzureRmHDInsightHiveJob -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -file $hqlScriptFile -StatusFolder $statusFolder #-OutVariable $outVariable
    
    Write-Host "Display the standard error log ... " -ForegroundColor Green
    $jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
    Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $jobID -DefaultContainer $defaultBlobContainerName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -HttpCredential $httpCredential
    #endregion

### <a name="check-the-results"></a>Verificar os resultados

Use o seguinte script do Windows PowerShell para verificar a saída de trabalho de seção. Você precisará definir as primeiras duas variáveis.

    #region variables and constants
    $clusterName = "<Existing Azure HDInsight Cluster Name>"
    
    $blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
    #engregion
    
    #region - Create an Azure storage context object
    Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    
    Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow
    
    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey  
    #endregion
    
    #region - Download blob and display blob
    Write-Host "Download the blob ..." -ForegroundColor Green
    cd $HOME
    Get-AzureStorageBlobContent -Container $defaultBlobContainerName -Blob $blob -Context $storageContext -Force
    
    Write-Host "Display the output ..." -ForegroundColor Green
    Write-Host "==================================" -ForegroundColor Green
    cat "./$blob"
    Write-Host "==================================" -ForegroundColor Green
    #end region

> [AZURE.NOTE] A tabela de seção usa \001 como delimitador de campo. O delimitador não estiver visível na saída.

Depois que os resultados da análise tiverem sido colocados em armazenamento de Blob do Azure, você pode exportar os dados para um servidor de banco de dados/SQL Azure SQL, exportar os dados para o Excel usando o Power Query ou conectar seu aplicativo aos dados por meio do Driver ODBC seção. Para obter mais informações, consulte [Usar Sqoop com HDInsight][hdinsight-use-sqoop], [analisar dados de atrasos de voos usando HDInsight][hdinsight-analyze-flight-delay-data], [O Excel se conectar ao HDInsight com o Power Query][hdinsight-power-query]e o [Excel se conectar ao HDInsight com o Driver ODBC de seção do Microsoft][hdinsight-hive-odbc].

##<a name="next-steps"></a>Próximas etapas

Neste tutorial, podemos viu como transformar um conjunto de dados não estruturado JSON em uma tabela de seção estruturada para consultar, explorar e analisar dados do Twitter usando HDInsight no Azure. Para saber mais, consulte:

- [Introdução ao HDInsight][hdinsight-get-started]
- [Analisar sentimento Twitter em tempo real com HBase em HDInsight][hdinsight-hbase-twitter-sentiment]
- [Analisar dados de atrasos de voos usando HDInsight][hdinsight-analyze-flight-delay-data]
- [Conectar o Excel ao HDInsight com o Power Query][hdinsight-power-query]
- [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft seção][hdinsight-hive-odbc]
- [Usar Sqoop com HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
