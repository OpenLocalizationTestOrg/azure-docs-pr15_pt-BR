<properties
   pageTitle="Use Beeline para trabalhar com seção em HDInsight (Hadoop) | Microsoft Azure"
   description="Saiba como usar o SSH para se conectar a um cluster de Hadoop no HDInsight e envie a seção consultas interativamente usando Beeline. Beeline é um utilitário para trabalhar com HiveServer2 sobre JDBC."
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
   ms.date="10/10/2016"
   ms.author="larryfr"/>

#<a name="use-hive-with-hadoop-in-hdinsight-with-beeline"></a>Use a seção com Hadoop em HDInsight com Beeline

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Neste artigo, você aprenderá como usar o Secure Shell (SSH) para se conectar a um cluster baseado no Linux HDInsight e envie a seção consultas interativamente usando a ferramenta de linha de comando [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) .

> [AZURE.NOTE] Beeline usa JDBC para se conectar à seção. Para obter mais informações sobre como usar JDBC com seção, consulte [conectar a seção em Azurehdinsight usando o driver JDBC seção](hdinsight-connect-hive-jdbc-driver.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um Hadoop baseado em Linux em cluster HDInsight.

* Um cliente SSH. Mac OS, Unix e Linux devem ter um cliente SSH. Usuários do Windows devem baixar um cliente, como [Acabamento](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Conectar-se com SSH

Conectar-se para o nome de domínio totalmente qualificado (FQDN) do cluster HDInsight usando o comando SSH. O FQDN será o nome que você deu cluster, em seguida, **. azurehdinsight.net**. Por exemplo, o seguinte deve se conectar a um cluster chamado **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se você forneceu uma chave de certificado para autenticação de SSH** quando você criou o cluster HDInsight, será necessário especificar o local da chave particular em seu sistema de cliente:

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se você forneceu uma senha para autenticação de SSH** quando você criou o cluster HDInsight, você precisará fornecer a senha quando solicitado.

Para obter mais informações sobre como usar SSH com HDInsight, consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>Acabamento (clientes baseados no Windows)

Windows não oferece um cliente SSH interno. Recomendamos o uso de **Acabamento**, que pode ser baixado em [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obter mais informações sobre como usar o acabamento, consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="beeline"></a>Use o comando Beeline

1. Uma vez conectado, use o seguinte para iniciar o Beeline:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    Isso irá iniciar o cliente de Beeline e conectar-se a url JDBC. Aqui, `localhost` é usado, pois HiveServer2 é executado em ambos os nós cabeça no cluster e estamos executando Beeline diretamente no headnode principal.
    
    Quando o comando for concluída, você chegarão em uma `jdbc:hive2://localhost:10001/>` prompt.

3. Comandos de beeline geralmente começam com uma `!` de caracteres, por exemplo `!help` exibe a Ajuda. No entanto, o `!` muitas vezes pode ser omitido. Por exemplo, `help` também funcionará.

    Se você exibir a Ajuda, você observará `!sql`, que é usado para executar instruções de HiveQL. No entanto, HiveQL é então comumente usado que você pode omitir anterior `!sql`. As duas instruções a seguintes têm exatamente os mesmos resultados; exibindo as tabelas atualmente disponíveis por meio de seção:
    
        !sql show tables;
        show tables;
    
    Um novo cluster, apenas uma tabela deve estar listada: __hivesampletable__.

4. Use este procedimento para exibir o esquema para o hivesampletable:

        describe hivesampletable;
        
    Isso retornará as seguintes informações:
    
        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Isso exibe as colunas na tabela. Embora nós pode realizar algumas consultas em relação a esses dados, vamos criar em vez disso, uma nova tabela para demonstrar como carregar dados na seção e aplicar um esquema.
    
5. Insira as instruções a seguir para criar uma nova tabela denominada **log4jLogs** usando dados de exemplo fornecidos com o cluster HDInsight:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Essas instruções executam as seguintes ações:

    * **DROP TABLE** - exclui a tabela e o arquivo de dados, caso a tabela já existe.
    * **Criar tabela externa** - cria uma nova tabela 'external' na seção. Tabelas externas só armazenam a definição da tabela na seção. Os dados são deixados no local original.
    * **Formato de linha** - informa seção como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.
    * **Local de arquivo de texto como armazenados** - informa seção onde os dados estão armazenados (o diretório de dados do exemplo) e que ele está armazenado como texto.
    * **Selecione** - seleciona uma contagem de todas as linhas onde coluna **t4** contém o valor **[ERROR]**. Isso deve retornar um valor de **3** como existem três linhas que contêm esse valor.
    * **INPUT__FILE__NAME como '%.log'** - informa seção que estamos só deve retornar dados de arquivos que terminam em. log. Normalmente, você só teria dados com o mesmo esquema dentro da mesma pasta quando consultando com a seção, mas esse arquivo de log de exemplo é armazenado com outros formatos de dados.

    > [AZURE.NOTE] Tabelas externas devem ser usadas quando você espera os dados subjacentes para ser atualizados por uma fonte externa, como um processo de carregamento de dados automatizada ou por outra operação MapReduce, mas quer sempre que as consultas de seção para usar os dados mais recentes.
    >
    > Soltar uma tabela externa faz **não** excluir os dados, somente a definição da tabela.
    
    A saída deste comando deve ser semelhante ao seguinte:
    
        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :
        
        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)
        
        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

4. Para sair do Beeline, use `!quit`.

##<a id="file"></a>Executar um arquivo de HiveQL

Beeline também podem ser usados para executar um arquivo que contém instruções de HiveQL. Use as seguintes etapas para criar um arquivo e, em seguida, executá-lo usando Beeline.

1. Use o seguinte comando para criar um novo arquivo chamado __query.hql__:

        nano query.hql
        
2. Quando abre o editor, use o seguinte como o conteúdo do arquivo. Essa consulta criará uma nova tabela 'interna' denominada **logs de erros**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Essas instruções executam as seguintes ações:

    * **Criar tabela se não existir** - cria uma tabela, se ele ainda não exista. Como a palavra-chave **externa** não for usada, isso é uma tabela interna, que é armazenada na seção data warehouse e é gerenciada completamente pela seção.
    * **ARMAZENADO como ORC** - armazena os dados no formato de linha de otimizado colunas (ORC). Este é um formato altamente otimizado e eficiente para armazenar dados de seção.
    * SUBSTITUIR **Inserir … Selecione** - seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]**e, em seguida, insere os dados na tabela **logs de erros** .
    
    > [AZURE.NOTE] Ao contrário de tabelas externas, soltar uma tabela interna excluirá os dados subjacentes também.
    
3. Para salvar o arquivo, use __Ctrl__+__X___, depois digite __Y__e finalmente __Enter__.

4. Use o seguinte para executar o arquivo usando Beeline. Substitua o __nome do host__ com o nome obtido anteriormente para o nó principal e a __senha__ com a senha da conta de administrador:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i query.hql

    > [AZURE.NOTE] O `-i` parâmetro inicia Beeline, executa as instruções no arquivo query.hql e permanece na Beeline na `jdbc:hive2://localhost:10001/>` prompt. Você também pode executar um arquivo usando o `-f` parâmetro, que retorna à Bash depois que o arquivo foi processado.

5. Para verificar que a tabela de **logs de erros** foi criada, use a seguinte instrução para retornar todas as linhas de **logs de erros**:

        SELECT * from errorLogs;

    Três linhas de dados deve ser retornado, todos contendo **[ERROR]** na coluna t4:
    
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a name="more-about-beeline-connectivity"></a>Mais sobre a conectividade de Beeline

As etapas neste documento use `localhost` para se conectar ao HiveServer2 em execução no headnode cluster. Enquanto você também pode usar o nome do host ou o nome de domínio totalmente qualificado do headnode aqueles requerem etapas adicionais para o processo (etapas para localizar o nome do host ou FQDN). Usando `localhost` é suficiente quando usando Beeline da headnode.

Se você tiver um nó de borda no seu cluster, com Beeline instalado, você precisará usar o nome do host ou o FQDN do headnode para se conectar.

Se você tiver Beeline instalado em um cliente fora do seu cluster, você pode se conectar usando o seguinte comando. Substitua o nome do seu cluster HDInsight __CLUSTERNAME__ . Substitua a __senha__ com a senha da conta de administrador (login HTTP).

    beeline -u 'jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=hive2' -n admin -p PASSWORD

Observe que os parâmetros/URI é diferente quando executando diretamente em uma headnode ou de um nó de borda dentro do cluster. Isso ocorre porque se conectando ao cluster da internet usa um gateway público que rotear o tráfego pela porta 443. Além disso, vários outros serviços estão expostos através do gateway público na porta 443, portanto, o URI é diferente ao conectar diretamente. Ao conectar-se da internet você também deve autenticar a sessão, fornecendo a senha.

##<a id="summary"></a><a id="nextsteps"></a>Próximas etapas

Como você pode ver, o comando de Beeline fornece uma maneira fácil de interativamente executar consultas de seção em um cluster de HDInsight.

Para obter informações gerais sobre a seção no HDInsight:

* [Use a seção com Hadoop em HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop em HDInsight:

* [Usar porco com Hadoop em HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce com Hadoop em HDInsight](hdinsight-use-mapreduce.md)

Se você estiver usando Tez com seção, consulte os seguintes documentos para informações de depuração:

* [Usar a interface do usuário Tez em HDInsight baseado no Windows](hdinsight-debug-tez-ui.md)

* [Usar o modo de exibição Ambari Tez HDInsight baseados em Linux](hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

