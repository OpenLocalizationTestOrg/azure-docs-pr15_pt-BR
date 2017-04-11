<properties
   pageTitle="Otimizar suas consultas de seção para execução mais rápida no HDInsight | Microsoft Azure"
   description="Saiba como otimizar suas consultas de seção para Hadoop em HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/28/2015"
   ms.author="rashimg"/>


# <a name="optimize-hive-queries-for-hadoop-in-hdinsight"></a>Otimizar consultas de seção para Hadoop em HDInsight

Por padrão, Hadoop clusters não são otimizados para desempenho. Este artigo aborda algumas dos métodos de otimização de desempenho seção mais comuns que você pode aplicar às nossas consultas.

##<a name="scale-out-worker-nodes"></a>Dimensionar nós de operador

Aumentar o número de nós de trabalho em um cluster pode aproveitar mais mapeadores e reducers para ser executado em paralelo. Há duas maneiras que você pode aumentar a escala em HDInsight:

- No momento provisionar, você pode especificar o número de nós de trabalhador usando o Portal do Azure, Azure PowerShell ou interface de linha de comando entre plataformas.  Para obter mais informações, consulte [clusters de provisionar HDInsight](hdinsight-provision-clusters.md). A tela a seguir mostram o trabalhador configuração nós no Portal do Azure:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]

- No tempo de execução, você pode também dimensionar um cluster sem recriar um. Isso é mostrado abaixo.
![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Para obter mais detalhes sobre as máquinas virtuais diferentes suportadas pelo HDInsight, consulte [HDInsight preços](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="enable-tez"></a>Habilitar Tez

[Apache Tez](http://hortonworks.com/hadoop/tez/) é um mecanismo de execução alternativo para o mecanismo de MapReduce:

![tez_1][image-hdi-optimize-hive-tez_1]


Tez é mais rápida porque:

- Executar direcionado Dag (mão acíclica Graph) como um único trabalho do mecanismo de MapReduce, o Dag de mão expresso requer cada conjunto de mapeadores ser seguido por um conjunto de reducers. Isso faz com que vários trabalhos MapReduce ser será levado para cada consulta de seção. Tez não possui tal restrição e pode processar Dag mão complexo como um trabalho minimizando sobrecarga de inicialização de trabalho.
- **Grava Avoids desnecessárias** Devido a vários trabalhos sendo girados para a mesma consulta de seção no mecanismo de MapReduce, a saída de cada trabalho é destinada ao HDFS dados intermediários. Como Tez minimiza o número de trabalhos para cada seção consulta é capaz de evitar gravação desnecessária.
- **Atrasos de inicialização minimiza** Tez melhor é capaz minimizar o atraso de inicialização, reduzindo o número de mapeadores que ele precisa iniciar e além de melhorar a otimização em todo.
- **Reutiliza contêineres** Sempre que possível Tez é reutilizar contêineres para garantir que a latência devido a iniciando contêineres é reduzida.
- **Técnicas de otimização contínua** Normalmente otimização foi feita durante a fase de compilação. No entanto, há mais informações sobre as entradas que permitem melhor otimização durante a execução. Tez usa técnicas de otimização contínua que permite que ele otimizar o plano para a fase de tempo de execução.

Para obter mais detalhes sobre esses conceitos, clique [aqui](http://hortonworks.com/hadoop/tez/)

Você pode fazer qualquer seção consulta Tez habilitado por prefixando a consulta com a configuração abaixo:

    set hive.execution.engine=tez;

Para clusters de HDInsight baseados no Windows, Tez deve estar habilitado no momento provisionar. Este é um exemplo de script do PowerShell do Azure para provisionamento um cluster de Hadoop com Tez habilitado:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $clusterName = "[HDInsightClusterName]"
    $location = "[AzureDataCenter]" #i.e. West US
    $dataNodes = 32 # number of worker nodes in the cluster

    $defaultStorageAccountName = "[DefaultStorageAccountName]"
    $defaultStorageContainerName = "[DefaultBlobContainerName]"
    $defaultStorageAccountKey = $defaultStorageAccountKey = Get-AzureStorageKey $defaultStorageAccountName.ToLower() | %{ $_.Primary }

    $hdiUserName = "[HTTPUserName]"
    $hdiPassword = "[HTTPUserPassword]"

    $hdiSecurePassword = ConvertTo-SecureString $hdiPassword -AsPlainText -Force
    $hdiCredential = New-Object System.Management.Automation.PSCredential($hdiUserName, $hdiSecurePassword)

    $hiveConfig = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
    $hiveConfig.Configuration = @{ "hive.execution.engine"="tez" }

    New-AzureHDInsightClusterConfig -ClusterSizeInNodes $dataNodes -HeadNodeVMSize Standard_D14 -DataNodeVMSize Standard_D14 |
    Set-AzureHDInsightDefaultStorage -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" -StorageAccountKey $defaultStorageAccountKey -StorageContainerName $defaultStorageContainerName |
    Add-AzureHDInsightConfigValues -Hive $hiveConfig |
    New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $hdiCredential

    
> [AZURE.NOTE] Clusters de HDInsight baseados em Linux tem Tez habilitado por padrão.
    

## <a name="hive-partitioning"></a>Seção partição

Operação é o gargalo de desempenho principais para executar consultas de seção. O desempenho pode ser melhorado se a quantidade de dados que precisa ser lido pode ser reduzida. Por padrão, a seção consultas digitalizar tabelas de seção inteiras. Isso é ótimo para consultas como tabela examina, porém para consultas que só precisam digitalizar uma pequena quantidade de dados (por exemplo, consultas com filtragem), isso cria uma sobrecarga desnecessária. Seção partição permite que as consultas de seção para acessar apenas a quantidade necessária de dados em tabelas de seção.

Seção partição é implementado por reorganizando os dados processados em novos diretórios com cada partição tendo seu próprio diretório - onde a partição é definida pelo usuário. O diagrama a seguir ilustra partição de uma tabela de seção pela coluna *ano*. Um novo diretório é criado para cada ano.

![partição][image-hdi-optimize-hive-partitioning_1]

Algumas considerações de partição:

- **Eu não under partição** - partição em colunas com apenas alguns valores podem causar partições poucos. Por exemplo, partição no gênero será apenas criar duas partições seja criado (masculino e feminino), portanto, apenas reduzir a latência por um máximo de metade.

- **Não partição excesso** - no outro extremo, criando uma partição em uma coluna com um valor exclusivo (por exemplo, ID de usuário) fará com que várias partições causando muita carga no namenode cluster como ele terão lidar com grande quantidade de diretórios.

- **Evite dados inclinam** - escolher sua chave de partição de maneira sensata, para que todas as partições são mesmo tamanho. Um exemplo é partição no *estado* pode causar o número de registros em Califórnia sejam quase 30 x que Vermont devido a diferença na população.

Para criar uma tabela de partição, use a cláusula *Particionado por* :

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE        STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Depois que a tabela particionada é criada, você pode criar partição estático ou dinâmico partição.

- **Separar estático** significa que você tem dados já sharded nos diretórios apropriados e você pode perguntar seção partições manualmente baseadas no diretório local. Isso é mostrado no trecho de código abaixo.

        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’

        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasbs://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'

- **Partição dinâmico** significa que você deseja seção para criar partições automaticamente para você. Como podemos já criou a tabela de partição da tabela de preparação, tudo o que precisamos fazer é inserir dados para a tabela particionada, conforme mostrado abaixo:

        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
             L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as       L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as       L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as   L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Para obter mais detalhes, consulte [Tabelas particionadas](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

##<a name="use-the-orcfile-format"></a>Usar o formato de ORCFile

Seção dá suporte a formatos de arquivo diferente. Por exemplo:

- **Texto**: Este é o formato de arquivo padrão e funciona com a maioria dos cenários
- **Avro**: funciona bem para cenários de interoperabilidade
- **ORC/Parquet**: são mais adequados para desempenho

Formato ORC (colunas de linha otimizado) é uma maneira altamente eficiente para armazenar dados de seção. Comparado a outros formatos, ORC tem as seguintes vantagens:

- suporte para tipos complexos, incluindo DateTime e tipos complexos e semiestruturados
- até 70% de compactação
- índices cada 10.000 linhas que permitem ignorando linhas
- uma queda significativa no tempo de execução

Para habilitar o formato ORC, você deve primeiro criar uma tabela com a cláusula *armazenados como ORC*:

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT     STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

Em seguida, você inserir dados na tabela ORC da tabela de preparação. Por exemplo:

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
           L_SUPPKEY as L_SUPPKEY,
           L_LINENUMBER as L_LINENUMBER,
           L_QUANTITY as L_QUANTITY, 
           L_EXTENDEDPRICE as L_EXTENDEDPRICE,
           L_DISCOUNT as L_DISCOUNT,
           L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
           L_LINESTATUS as L_LINESTATUS,
           L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;

Você pode ler mais sobre o formato ORC [aqui](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

##<a name="vectorization"></a>Vetorização

Vetorização permite que a seção processar um lote de linhas de 1024 juntos em vez de processamento de uma linha por vez. Isso significa que operações simples terminar com mais rapidez porque menos código interno precisa executar.

Para habilitar vetorização prefixo sua consulta de seção com a seguinte configuração:

    set hive.vectorized.execution.enabled = true;

Para obter mais informações, consulte [Vectorized execução da consulta](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).


##<a name="other-optimization-methods"></a>Outros métodos de otimização

Há mais métodos de otimização que você pode considerar, por exemplo:

- **Seção bucketing:** uma técnica que permite cluster ou segmento grandes conjuntos de dados para otimizar o desempenho de consulta.
- **Otimização da junção:** otimização de execução da consulta da seção planejamento para melhorar a eficiência de junções e reduzir a necessidade de dicas de usuário. Para obter mais informações, consulte [otimização da junção](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
- **aumentar Reducers**

##<a id="nextsteps"></a>Próximas etapas
Neste artigo, você aprendeu vários métodos de otimização de consulta seção comuns. Para saber mais, consulte os seguintes artigos:

- [Use a seção Apache no HDInsight](hdinsight-use-hive.md)
- [Analisar os dados de atrasos de voos usando a seção no HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Analisar dados de Twitter usando a seção no HDInsight](hdinsight-analyze-twitter-data.md)
- [Analisar dados de sensor usando o Console de consulta seção em Hadoop em HDInsight](hdinsight-hive-analyze-sensor-data.md)
- [Use a seção com HDInsight para analisar logs de sites](hdinsight-hive-analyze-website-log.md)


[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
