<properties 
    pageTitle="Analisar os dados de atrasos de voos com seção em baseados em Linux HDInsight | Microsoft Azure" 
    description="Saiba como usar a seção para analisar dados de voos em HDInsight baseado em Linux, em seguida, exporte os dados para o banco de dados do SQL usando Sqoop." 
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

#<a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Analisar os dados de atrasos de voos usando a seção no HDInsight

Aprenda a analisar os dados de atrasos de voos usando seção em HDInsight baseado em Linux depois exportar os dados para o banco de dados do SQL Azure usando Sqoop.

> [AZURE.NOTE] Enquanto partes individuais deste documento podem ser usadas com clusters baseados no Windows HDInsight (Python e seção por exemplo), várias etapas são específicas para clusters baseados em Linux. Para obter etapas que funcionam com um cluster baseado no Windows, consulte [dados de atrasos de voos de análise usando a seção no HDInsight](hdinsight-analyze-flight-delay-data.md)

###<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __HDInsight um cluster__. Para obter etapas sobre como criar um novo cluster baseado em Linux HDInsight, consulte [começar a usar o Hadoop com seção no HDInsight no Linux](hdinsight-hadoop-linux-tutorial-get-started.md) .

- __Banco de dados do SQL azure__. Você usará um banco de dados do SQL Azure como um armazenamento de dados de destino. Se você não tiver um banco de dados do SQL já, consulte [tutorial do banco de dados SQL: criar um banco de dados do SQL em minutos](../sql-database/sql-database-get-started.md).

- __CLI azure__. Se você não instalou o CLI Azure, consulte [instalar e configurar o CLI do Azure](../xplat-cli-install.md) para obter mais etapas.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]


##<a name="download-the-flight-data"></a>Baixar os dados de voos

1. Navegue até a [pesquisa e administração de tecnologia inovadora, gabinete de estatísticas de transporte][rita-website].
2. Na página, selecione os seguintes valores:

  	| Nome | Valor |
  	| ---- | ---- |
  	| Ano de filtro | 2013 |
  	| Filtrar período | Janeiro |
  	| Campos | Ano, Datavoo, UniqueCarrier, Carrier, FlightNum, OriginAirportID, origem, OriginCityName, OriginState, DestAirportID, destino, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. Limpar todos os outros campos |

3. Clique em **Baixar**. 

##<a name="upload-the-data"></a>Carregar os dados

1. Use o seguinte comando para carregar o arquivo zip no nó HDInsight cluster principal:

        scp FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Substitua o __nome do arquivo__ com o nome do arquivo zip. Substitua o __nome de usuário__ por login SSH para o cluster HDInsight. Substitua o nome do cluster HDInsight CLUSTERNAME.
    
    > [AZURE.NOTE] Se você usar uma senha para autenticar o logon SSH, você será solicitado a senha. Se você usou uma chave pública, você pode precisar usar o `-i` parâmetro e especifique o caminho para a chave privada correspondente. Por exemplo `scp -i ~/.ssh/id_rsa FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Após a conclusão do carregamento, conectar-se ao cluster usando SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    Para obter mais informações sobre como usar SSH com baseado em Linux HDInsight, consulte os seguintes artigos:
    
    * [Use SSH com baseado em Linux Hadoop em HDInsight Linux, Unix ou dos X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
3. Uma vez conectado, use o seguinte para descompactar o arquivo. zip:

        unzip FILENAME.zip
    
    Isso irá extrair um arquivo. csv que é de aproximadamente 60MB no tamanho.
    
4. Use o seguinte para criar um novo diretório WASB (o armazenamento de dados distribuídos usado pelo HDInsight) e copie o arquivo:

    HDFS dfs - mkdir -p /tutorials/flightdelays/data hdfs dfs-colocar FILENAME.csv/tutoriais/flightdelays/dados /
    
##<a name="create-and-run-the-hiveql"></a>Criar e executar o HiveQL

Use as etapas a seguir para importar dados do arquivo CSV para uma tabela de seção denominada __atrasos__.

1. Use o seguinte para criar e editar um novo arquivo chamado __flightdelays.hql__:

        nano flightdelays.hql
        
    Use o seguinte como o conteúdo deste arquivo:
    
        DROP TABLE delays_raw;
        -- Creates an external table over the csv file
        CREATE EXTERNAL TABLE delays_raw (
            YEAR string,
            FL_DATE string,
            UNIQUE_CARRIER string,
            CARRIER string,
            FL_NUM string,
            ORIGIN_AIRPORT_ID string,
            ORIGIN string,
            ORIGIN_CITY_NAME string,
            ORIGIN_CITY_NAME_TEMP string,
            ORIGIN_STATE_ABR string,
            DEST_AIRPORT_ID string,
            DEST string,
            DEST_CITY_NAME string,
            DEST_CITY_NAME_TEMP string,
            DEST_STATE_ABR string,
            DEP_DELAY_NEW float,
            ARR_DELAY_NEW float,
            CARRIER_DELAY float,
            WEATHER_DELAY float,
            NAS_DELAY float,
            SECURITY_DELAY float,
            LATE_AIRCRAFT_DELAY float)
        -- The following lines describe the format and location of the file
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE
        LOCATION '/tutorials/flightdelays/data';
        
        -- Drop the delays table if it exists
        DROP TABLE delays;
        -- Create the delays table and populate it with data
        -- pulled in from the CSV file (via the external table defined previously)
        CREATE TABLE delays AS
        SELECT YEAR AS year,
            FL_DATE AS flight_date,
            substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
            substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
            substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
            ORIGIN_AIRPORT_ID AS origin_airport_id,
            substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
            substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
            substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
            DEST_AIRPORT_ID AS dest_airport_id,
            substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
            substring(DEST_CITY_NAME,2) AS dest_city_name,
            substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
            DEP_DELAY_NEW AS dep_delay_new,
            ARR_DELAY_NEW AS arr_delay_new,
            CARRIER_DELAY AS carrier_delay,
            WEATHER_DELAY AS weather_delay,
            NAS_DELAY AS nas_delay,
            SECURITY_DELAY AS security_delay,
            LATE_AIRCRAFT_DELAY AS late_aircraft_delay
        FROM delays_raw;
        
2. Use __Ctrl + X__e __Y__ para salvar o arquivo.

3. Use o seguinte para iniciar a seção e execute o arquivo __flightdelays.hql__ :

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -f flightdelays.hql
        
    > [AZURE.NOTE] Neste exemplo, `localhost` é usado, pois você está conectado ao nó principal do cluster HDInsight, que é onde HiveServer2 está sendo executado.

4. Use o comando a seguir para abrir uma sessão de Beeline interativa:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

5. Quando você receber o `jdbc:hive2://localhost:10001/>` solicitar, use o seguinte para recuperar dados dos dados de atraso voos importados.

        INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        SELECT regexp_replace(origin_city_name, '''', ''),
            avg(weather_delay)
        FROM delays
        WHERE weather_delay IS NOT NULL
        GROUP BY origin_city_name;

    Isso recuperar uma lista de cidades que passou por atrasos de clima, juntamente com o tempo de atraso médio e salvá-lo para `/tutorials/flightdelays/output`. Posteriormente, Sqoop lerá os dados deste local e exportá-lo para Azure SQL Database.

6. Para sair do Beeline, digite `!quit` no prompt.

## <a name="create-a-sql-database"></a>Criar um banco de dados do SQL

Se você já tiver um banco de dados do SQL, você deve obter o nome do servidor. Você pode encontrar isso no [Portal do Azure](https://portal.azure.com) selecionando __Bancos de dados SQL__e, em seguida, filtrando o nome do banco de dados que deseja usar. O nome do servidor está listado na coluna __servidor__ .

Se você não tiver um banco de dados do SQL, use as informações na [tutorial do banco de dados SQL: criar um banco de dados do SQL em minutos](../sql-database/sql-database-get-started.md) para criar uma. Você precisará salvar o nome do servidor usado para o banco de dados.

##<a name="create-a-sql-database-table"></a>Criar uma tabela de banco de dados SQL

> [AZURE.NOTE] Há várias maneiras para se conectar ao banco de dados SQL para criar uma tabela. As etapas seguintes usam [FreeTDS](http://www.freetds.org/) do cluster HDInsight.

1. Usar SSH para se conectar ao cluster baseado em Linux HDInsight e execute as etapas a seguir da sessão SSH.

3. Use o comando a seguir para instalar o FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Quando FreeTDS tiver sido instalado, use o seguinte comando para se conectar ao servidor de banco de dados SQL. Substitua o nome do servidor de banco de dados SQL __nomedoservidor__ . Substitua __adminLogin__ e __adminPassword__ login do banco de dados do SQL. Substitua __databaseName__ com o nome do banco de dados.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>

    Você receberá saída similar ao seguinte:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. No `1>` solicitar, insira as seguintes linhas:

        CREATE TABLE [dbo].[delays](
        [origin_city_name] [nvarchar](50) NOT NULL,
        [weather_delay] float,
        CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
        ([origin_city_name] ASC))
        GO

    Quando o `GO` instrução é inserida, as instruções anterior serão avaliadas. Isso criará uma nova tabela denominada __atrasos__, com um índice de cluster (obrigatório pelo banco de dados do SQL).

    Use o seguinte para verificar que a tabela foi criada:

        SELECT * FROM information_schema.tables
        GO

    Você deve ver o resultado similar ao seguinte:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        databaseName       dbo     delays      BASE TABLE

8. Insira `exit` na `1>` aviso sair do utilitário tsql.
    
##<a name="export-data-with-sqoop"></a>Exportar dados com Sqoop

2. Use o seguinte comando para verificar que Sqoop pode ver seu banco de dados do SQL:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Isso deve retornar uma lista de bancos de dados, incluindo o banco de dados que você criou a tabela de atrasos no anteriormente.

3. Use o comando a seguir para exportar dados do hivesampletable à tabela mobiledata:

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir 'wasbs:///tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1

    Isso instrui Sqoop para se conectar ao banco de dados SQL no banco de dados que contém a tabela de atrasos e exportar dados do wasbs: / / / tutoriais/flightdelays/saída (onde estamos armazenados a saída da consulta seção anterior,) para a tabela de atrasos.

4. Após concluir o comando, use o seguinte para se conectar ao banco de dados usando TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Uma vez conectado, use as instruções a seguir para verificar que os dados foram exportados para a tabela de mobiledata:
    
        SELECT * FROM delays
        GO

    Você deve ver uma listagem dos dados na tabela. Tipo de `exit` para sair do utilitário tsql.

##<a id="nextsteps"></a>Próximas etapas

Agora você compreender como carregar um arquivo para armazenamento de Blob do Azure, como preencher uma tabela de seção usando os dados do armazenamento de Blob do Azure, como executar consultas de seção e como usar Sqoop para exportar dados do HDFS para um banco de dados do SQL Azure. Para saber mais, consulte os seguintes artigos:

* [Introdução ao HDInsight][hdinsight-get-started]
* [Use a seção com HDInsight][hdinsight-use-hive]
* [Usar Oozie com HDInsight][hdinsight-use-oozie]
* [Usar Sqoop com HDInsight][hdinsight-use-sqoop]
* [Usar porco com HDInsight][hdinsight-use-pig]
* [Desenvolver programas Java MapReduce para HDInsight][hdinsight-develop-mapreduce]
* [Desenvolver Hadoop Python streaming programas para HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx


 
