<properties
   pageTitle="Usar o shell de seção no HDInsight (Hadoop) | Microsoft Azure"
   description="Saiba como usar o shell de seção com um cluster de HDInsight baseados em Linux. Você irá aprender como se conectar ao cluster HDInsight usando SSh, use o Shell de seção interativamente executar consultas."
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
   ms.date="10/04/2016"
   ms.author="larryfr"/>

# <a name="use-hive-with-hadoop-in-hdinsight-with-ssh"></a>Use a seção com Hadoop em HDInsight com SSH

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Neste artigo, você aprenderá como usar Secure Shell (SSH) para conectar-se a uma Hadoop em cluster Azurehdinsight e, em seguida, enviar interativamente seção consultas usando a interface de linha de seção (CLI).

> [AZURE.IMPORTANT] O comando da seção está disponível em clusters baseados em Linux HDInsight, você deve considerar usando Beeline. Beeline é um cliente mais recente para trabalhar com a seção e está incluído no seu cluster HDInsight. Para obter mais informações sobre como usar esta, consulte [Usar seção com Hadoop em HDInsight com Beeline](hdinsight-hadoop-use-hive-beeline.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um Hadoop baseado em Linux em cluster HDInsight.

* Um cliente SSH. Mac OS, Unix e Linux devem ter um cliente SSH. Usuários do Windows devem baixar um cliente, como [Acabamento](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Conectar-se com SSH

Conectar-se para o nome de domínio totalmente qualificado (FQDN) do cluster HDInsight usando o comando SSH. O FQDN será o nome que você deu cluster, em seguida, **. azurehdinsight.net**. Por exemplo, o seguinte deve se conectar a um cluster chamado **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se você forneceu uma chave de certificado para autenticação de SSH** quando você criou o cluster HDInsight, será necessário especificar o local da chave particular em seu sistema de cliente:

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Se você forneceu uma senha para autenticação de SSH** quando você criou o cluster HDInsight, você precisará fornecer a senha quando solicitado.

Para obter mais informações sobre como usar SSH com HDInsight, consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>Acabamento (clientes baseados no Windows)

Windows não oferece um cliente SSH interno. Recomendamos o uso de **Acabamento**, que pode ser baixado em [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obter mais informações sobre como usar o acabamento, consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hive"></a>Use o comando de seção

2. Uma vez conectado, inicie a CLI seção usando o comando a seguir:

        hive

3. Usando a CLI, digite as instruções para criar uma nova tabela denominada **log4jLogs** usando os dados de exemplo a seguir:

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
    * **INPUT__FILE__NAME como '%.log'** - informa seção que estamos só deve retornar dados de arquivos que terminam em. log. Isso restringe a pesquisa para o arquivo de sample.log que contém os dados e impede que ela retornar dados do exemplo de outro arquivos de dados que não correspondem o esquema definimos.

    > [AZURE.NOTE] Tabelas externas devem ser usadas quando você espera os dados subjacentes para ser atualizados por uma fonte externa, como um processo de carregamento de dados automatizada ou por outra operação MapReduce, mas quer sempre que as consultas de seção para usar os dados mais recentes.
    >
    > Soltar uma tabela externa faz **não** excluir os dados, somente a definição da tabela.

4. Use as instruções a seguir para criar uma nova tabela 'interna' denominada **logs de erros**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Essas instruções executam as seguintes ações:

    * **Criar tabela se não existir** - cria uma tabela, se ele ainda não exista. Como a palavra-chave **externa** não for usada, isso é uma tabela interna, que é armazenada na seção data warehouse e é gerenciada completamente pela seção.
    * **ARMAZENADO como ORC** - armazena os dados no formato de linha de otimizado colunas (ORC). Este é um formato altamente otimizado e eficiente para armazenar dados de seção.
    * SUBSTITUIR **Inserir … Selecione** - seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]**e, em seguida, insere os dados na tabela **logs de erros** .

    Para verificar que apenas as linhas que contêm **[ERROR]** na coluna t4 foram armazenadas na tabela de **logs de erros** , use a seguinte instrução para retornar todas as linhas de **logs de erros**:

        SELECT * from errorLogs;

    Três linhas de dados deve ser retornado, todos contendo **[ERROR]** na coluna t4.

    > [AZURE.NOTE] Ao contrário de tabelas externas, soltar uma tabela interna excluirá os dados subjacentes também.

##<a id="summary"></a>Resumo

Como você pode ver, o comando de seção fornece uma maneira fácil de interativamente executar consultas de seção em um cluster de HDInsight, monitorar o status do trabalho e recuperar a saída.

##<a id="nextsteps"></a>Próximas etapas

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


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png

