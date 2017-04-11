<properties
    pageTitle="Analisar dados de Twitter com Apache seção em HDInsight | Microsoft Azure"
    description="Saiba como usar Python para armazenar Tweets que contêm palavras-chave específicas e usam seção e Hadoop em HDInsight para transformar os dados processados do TWitter em uma tabela de seção pesquisável."
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
    ms.date="09/27/2016"
    ms.author="larryfr"/>

# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analisar dados de Twitter usando a seção no HDInsight

Neste documento, você obterá tweets usando um Twitter streaming API e, em seguida, use Apache seção em um cluster baseado no Linux HDInsight ao processo o JSON de dados formatados. O resultado será uma lista de usuários do Twitter quem enviou as maioria dos tweets que continha uma determinada palavra.

> [AZURE.NOTE] Enquanto partes individuais deste documento podem ser usadas com clusters baseados no Windows HDInsight (Python por exemplo), várias etapas se baseiam usando um cluster de HDInsight baseados em Linux. Para obter etapas específicas para um cluster baseado no Windows, consulte [dados analisar Twitter usando a seção no HDInsight](hdinsight-analyze-twitter-data.md).

###<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- Um __cluster de Azurehdinsight baseados em Linux__. Para obter informações sobre como criar um cluster, consulte [Introdução ao HDInsight baseados em Linux](hdinsight-hadoop-linux-tutorial-get-started.md) para obter etapas sobre como criar um cluster.

- Um __cliente SSH__. Para obter mais informações sobre como usar SSH com baseado em Linux HDInsight, consulte os seguintes artigos:

    * [Use SSH com baseado em Linux Hadoop em HDInsight Linux, Unix ou dos X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

- __Python__ e [pip](https://pypi.python.org/pypi/pip)

##<a name="get-a-twitter-feed"></a>Obter um Twitter feed

Twitter permite recuperar os [dados para cada tweet](https://dev.twitter.com/docs/platform-objects/tweets) como um documento de JavaScript Object Notation (JSON) por meio de uma API REST. [OAuth](http://oauth.net) é necessária para autenticação à API. Você também deve criar um _Aplicativo do Twitter_ que contém as configurações usadas para acessar a API.

###<a name="create-a-twitter-application"></a>Criar um aplicativo do Twitter

1. Usando um navegador da web, entre [https://apps.twitter.com/](https://apps.twitter.com/). Clique no link **Inscreva-se agora** se você não tiver uma conta do Twitter.
2. Clique em **Criar novo aplicativo**.
3. Insira o **nome**, **Descrição**, **site**. Você pode tornar uma URL para o campo **site** . A tabela a seguir mostra alguns valores de exemplo para usar:

  	| Campo | Valor |
  	|:----- |:----- |
  	| Nome  | MyHDInsightApp |
  	| Descrição | MyHDInsightApp |
  	| Site | http://www.myhdinsightapp.com |
    
4. Marque **Sim, concordo**e clique em **criar seu aplicativo do Twitter**.
5. Clique na guia **permissões** . A permissão padrão é **somente leitura**. Isso é suficiente para este tutorial.
6. Clique na guia **chaves e Tokens de acesso** .
7. Clique em **Criar minha token de acesso**.
8. Clique em **Teste OAuth** no canto superior direito da página.
9. Anote a **chave do consumidor**, **segredo do consumidor**, **token de acesso**e **segredo de token de acesso**. Você precisará os valores mais tarde.

>[AZURE.NOTE] Quando você usa o comando ondulação no Windows, use aspas duplas em vez de aspas simples para os valores de opção.

###<a name="download-tweets"></a>Baixar tweets

O seguinte código Python será baixar 10.000 tweets do Twitter e salvá-los em um arquivo denominado __tweets.txt__.

> [AZURE.NOTE] As etapas a seguir são executadas no cluster HDInsight, pois Python já está instalada.

1. Conectar-se ao cluster HDInsight usando SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    Se você usou uma senha para proteger sua conta de usuário SSH, você será solicitado para inseri-la. Se você usou uma chave pública, você talvez precise usar o `-i` parâmetro para especificar a chave privada correspondente. Por exemplo, `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
        
    Para obter mais informações sobre como usar SSH com baseado em Linux HDInsight, consulte os seguintes artigos:
    
    * [Use SSH com baseado em Linux Hadoop em HDInsight Linux, Unix ou dos X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows)
    
2. Por padrão, o utilitário de __pip__ não está instalado no nó principal HDInsight. Use o seguinte para instalar e atualize esse utilitário:

        sudo apt-get install python-pip
        sudo pip install --upgrade pip

3. O código para baixar tweets depende de [Tweepy](http://www.tweepy.org/) e a [barra de progresso](https://pypi.python.org/pypi/progressbar/2.2). Para instalar esses, use o seguinte comando:

        sudo apt-get install python-dev libffi-dev libssl-dev
        sudo apt-get remove python-openssl
        sudo pip install tweepy progressbar pyOpenSSL requests[security]
        
    > [AZURE.NOTE] Os bits sobre como remover python-openssl, instalando python desenvolvimento, desenvolvimento de libffi, desenvolvimento de libssl, pyOpenSSL e solicitações [segurança] é evitar um aviso de InsecurePlatform ao conectar com o Twitter via SSL do Python.
    >
    > Tweepy v3.2.0 é usado para evitar [um erro](https://github.com/tweepy/tweepy/issues/576) que podem ocorrer quando tweets de processamento.

4. Use o seguinte comando para criar um novo arquivo chamado __gettweets.py__:

        nano gettweets.py

5. Use o seguinte como o conteúdo do arquivo __gettweets.py__ . Substituir as informações de espaço reservado para __consumidor\_segredo__, __consumidor\_chave__, __acesso /\_token__, e __acesso\_token\_segredo__ com as informações do seu aplicativo do Twitter.

        #!/usr/bin/python

        from tweepy import Stream, OAuthHandler
        from tweepy.streaming import StreamListener
        from progressbar import ProgressBar, Percentage, Bar
        import json
        import sys

        #Twitter app information
        consumer_secret='Your consumer secret'
        consumer_key='Your consumer key'
        access_token='Your access token'
        access_token_secret='Your access token secret'

        #The number of tweets we want to get
        max_tweets=10000

        #Create the listener class that will receive and save tweets
        class listener(StreamListener):
            #On init, set the counter to zero and create a progress bar
            def __init__(self, api=None):
                self.num_tweets = 0
                self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

            #When data is received, do this
            def on_data(self, data):
                #Append the tweet to the 'tweets.txt' file
                with open('tweets.txt', 'a') as tweet_file:
                    tweet_file.write(data)
                    #Increment the number of tweets
                    self.num_tweets += 1
                    #Check to see if we have hit max_tweets and exit if so
                    if self.num_tweets >= max_tweets:
                        self.pbar.finish()
                        sys.exit(0)
                    else:
                        #increment the progress bar
                        self.pbar.update(self.num_tweets)
                return True

            #Handle any errors that may occur
            def on_error(self, status):
                print status

        #Get the OAuth token
        auth = OAuthHandler(consumer_key, consumer_secret)
        auth.set_access_token(access_token, access_token_secret)
        #Use the listener class for stream processing
        twitterStream = Stream(auth, listener())
        #Filter for these topics
        twitterStream.filter(track=["azure","cloud","hdinsight"])

6. Use __Ctrl + X__e __Y__ para salvar o arquivo.

7. Use o seguinte comando para executar o arquivo e baixar tweets:

        python gettweets.py

    Um indicador de progresso deve aparecer e contar até 100% enquanto os tweets são baixadas e salvo no arquivo.

    > [AZURE.NOTE] Se ele estiver demorando muito tempo para a barra de andamento Avançar, você deve alterar o filtro para acompanhar tendências tópicos; Quando houver muitas tweets sobre o tópico que você está filtrando, você pode obter rapidamente os 10000 tweets necessárias.

###<a name="upload-the-data"></a>Carregar os dados

Para carregar os dados WASB (o sistema de arquivos distribuídos usado pelo HDInsight), use os seguintes comandos:

    hdfs dfs -mkdir -p /tutorials/twitter/data
    hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt

Isso armazena os dados em um local que podem acessar todos os nós no cluster.

##<a name="run-the-hiveql-job"></a>Executar o trabalho HiveQL


1. Use o seguinte comando para criar um arquivo que contém instruções de HiveQL:

        nano twitter.hql
    
    Use o seguinte como o conteúdo do arquivo:

        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        -- Drop table, if it exists
        DROP TABLE tweets_raw;
        -- Create it, pointing toward the tweets logged from Twitter
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
        -- Drop and recreate the destination table
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
        -- Select tweets from the imported data, parse the JSON,
        -- and insert into the tweets table
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
        
        
3. Pressione __Ctrl + X__e, em seguida, pressione __s__ para salvar o arquivo.

4. Use o seguinte comando para executar o HiveQL contida no arquivo:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i twitter.hql
        
    Isso carregará o shell de seção, execute o HiveQL no arquivo __twitter.hql__ e finalmente retornar um `jdbc:hive2//localhost:10001/>` prompt.
    
5. No prompt do beeline, use o seguinte para verificar que você pode selecionar dados da tabela __tweets__ criada pela HiveQL no arquivo __twitter.hql__ :
        
        SELECT name, screen_name, count(1) as cc
            FROM tweets
            WHERE text like "%Azure%"
            GROUP BY name,screen_name
            ORDER BY cc DESC LIMIT 10;

    Isso retornará um máximo de 10 tweets que contêm a palavra __Azure__ no texto da mensagem.

##<a name="next-steps"></a>Próximas etapas

Neste tutorial, podemos viu como transformar um conjunto de dados não estruturado JSON em uma tabela de seção estruturada para consultar, explorar e analisar dados do Twitter usando HDInsight no Azure. Para saber mais, consulte:

- [Introdução ao HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Analisar dados de atrasos de voos usando HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
