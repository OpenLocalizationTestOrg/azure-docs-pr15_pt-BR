<properties
   pageTitle="Use a seção de Hadoop e área de trabalho remota da HDInsight | Microsoft Azure"
   description="Saiba como se conectar ao cluster Hadoop em HDInsight usando a área de trabalho remota e então executar consultas de seção usando a Interface de linha de seção."
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
   ms.date="09/06/2016"
   ms.author="larryfr"/>

# <a name="use-hive-with-hadoop-on-hdinsight-with-remote-desktop"></a>Use a seção com Hadoop em HDInsight com a área de trabalho remota

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Neste artigo, você saiba como se conectar a um cluster de HDInsight usando a área de trabalho remota e então executar consultas de seção usando a seção Interface de linha de comando (CLI).

> [AZURE.NOTE] Este documento não fornece uma descrição detalhada do que fazem as instruções de HiveQL que são usadas nos exemplos. Para obter informações sobre a HiveQL que é usada neste exemplo, consulte a [Seção uso com Hadoop em HDInsight](hdinsight-use-hive.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um cluster baseado no Windows HDInsight (Hadoop em HDInsight)

* Um computador cliente executando o Windows 10, janela 8 ou Windows 7

##<a id="connect"></a>Conectar-se com a área de trabalho remota

Habilitar a área de trabalho remota para o cluster HDInsight, em seguida, conectá-la seguindo as instruções em [conectar ao HDInsight clusters usando o RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="hive"></a>Use o comando de seção

Quando você tiver conectado na área de trabalho para o cluster HDInsight, use as etapas a seguir para trabalhar com seção:

1. Da área de trabalho HDInsight, comece a **linha de comando do Hadoop**.

2. Digite o seguinte comando para iniciar a CLI seção:

        %hive_home%\bin\hive

    Quando a CLI for iniciado, você verá o prompt da seção CLI: `hive>`.

3. Usando a CLI, digite as instruções para criar uma nova tabela denominada **log4jLogs** usando dados de exemplo a seguir:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Essas instruções executam as seguintes ações:

    * **DROP TABLE**: exclui a tabela e o arquivo de dados se a tabela já existe.

    * **Criar tabela externa**: cria uma nova tabela 'external' na seção. Tabelas externas armazenam apenas a definição de tabela na seção (os dados à esquerda no local original).

        > [AZURE.NOTE] Tabelas externas devem ser usadas quando você espera os dados subjacentes sejam atualizadas por uma fonte externa (como um processo de carregamento de dados automatizada) ou por outra operação MapReduce, mas quiser sempre as consultas de seção para usar os dados mais recentes.
        >
        > Soltar uma tabela externa faz **não** excluir os dados, somente a definição da tabela.

    * **Formato de linha**: informa seção como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

    * **Local de arquivo de texto como ARMAZENADO**: informa seção onde os dados estão armazenados (o diretório de dados do exemplo) e que ele está armazenado como texto.

    * **Selecione**: seleciona uma contagem de todas as linhas onde coluna **t4** contém o valor **[ERROR]**. Isso deve retornar um valor de **3** porque há três linhas que contêm esse valor.

    * **INPUT__FILE__NAME como '%.log'** - informa seção que estamos só deve retornar dados de arquivos que terminam em. log. Isso restringe a pesquisa para o arquivo de sample.log que contém os dados e impede que ela retornar dados do exemplo de outro arquivos de dados que não correspondem o esquema definimos.


4. Use as instruções a seguir para criar uma nova tabela 'interna' denominada **logs de erros**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Essas instruções executam as seguintes ações:

    * **Criar tabela se não existir**: cria uma tabela se ele ainda não exista. Como a palavra-chave **externa** não for usada, isso é uma tabela interna, que é armazenada na seção data warehouse e é gerenciada completamente pela seção.

        > [AZURE.NOTE] Ao contrário de tabelas **externas** , soltar uma tabela interna também exclui os dados subjacentes.

    * **ARMAZENADO como ORC**: armazena os dados no formato do linha otimizado coluna (ORC). Este é um formato altamente otimizado e eficiente para armazenar dados de seção.

    * SUBSTITUIR **Inserir … Selecione**: seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]**, em seguida, insere os dados na tabela **logs de erros** .

    Para verificar que apenas as linhas que contêm **[ERROR]** na coluna t4 foram armazenadas na tabela de **logs de erros** , use a seguinte instrução para retornar todas as linhas de **logs de erros**:

        SELECT * from errorLogs;

    Três linhas de dados deve ser retornado, todos contendo **[ERROR]** na coluna t4.

##<a id="summary"></a>Resumo

Como você pode ver, o o comando seção fornece uma maneira fácil de interativamente executar consultas de seção em um cluster de HDInsight, monitorar o status do trabalho e recuperar a saída.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre a seção no HDInsight:

* [Use a seção com Hadoop em HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop em HDInsight:

* [Usar porco com Hadoop em HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce com Hadoop em HDInsight](hdinsight-use-mapreduce.md)

Se você estiver usando Tez com seção, consulte os seguintes documentos para informações de depuração:

* [Usar a interface do usuário Tez em HDInsight baseado no Windows](hdinsight-debug-tez-ui.md)

* [Usar o modo de exibição Ambari Tez HDInsight baseados em Linux](hdinsight-debug-ambari-tez-view.md)

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

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





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

