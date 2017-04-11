<properties
   pageTitle="Seção consulta com ferramentas de Hadoop para Visual Studio | Microsoft Azure"
   description="Aprenda a usar seção com Hadoop em HDInsight usando as ferramentas do Visual Studio Hadoop."
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

#<a name="run-hive-queries-using-the-hdinsight-tools-for-visual-studio"></a>Executar consultas de seção usando as ferramentas de HDInsight para Visual Studio

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Neste artigo, você aprenderá como enviar consultas de seção para um cluster de HDInsight usando as ferramentas de HDInsight para Visual Studio.

> [AZURE.NOTE] Este documento não fornece uma descrição detalhada do que fazem as instruções de HiveQL usadas nos exemplos. Para obter informações sobre a HiveQL que é usada neste exemplo, consulte a [Seção uso com Hadoop em HDInsight](hdinsight-use-hive.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

* Um cluster de Azurehdinsight (Hadoop em HDInsight) (Linux ou baseado no Windows)

* Visual Studio (um dos seguintes versões):

    Visual Studio 2013 comunidade/Professional/Premium/Ultimate com [atualização 4](https://www.microsoft.com/download/details.aspx?id=44921)

    Visual Studio 2015 (comunidade/Enterprise)

- Ferramentas de HDInsight para Visual studio. Consulte [Introdução ao uso de ferramentas do Visual Studio Hadoop para HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md) para obter informações sobre como instalar e configurar as ferramentas.

##<a id="run"></a>Executar consultas de seção usando as ferramentas de HDInsight para Visual Studio

1. Abra o **Visual Studio** e selecione **novo** > **projeto** > **HDInsight** > **Seção aplicativo**. Forneça um nome para este projeto.

2. Abra o arquivo **Script.hql** que é criado com este projeto e, em seguida, cole nas instruções de HiveQL a seguir:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Essas instruções executam as seguintes ações:

    * **DROP TABLE**: exclui a tabela e o arquivo de dados se a tabela já existe.
    * **Criar tabela externa**: cria uma nova tabela 'external' na seção. Tabelas externas só armazenam a definição da tabela na seção (os dados à esquerda no local original).

        > [AZURE.NOTE] Tabelas externas devem ser usadas quando você espera os dados subjacentes sejam atualizadas por uma fonte externa (como um processo de carregamento de dados automatizada) ou por outra operação MapReduce, mas quiser sempre as consultas de seção para usar os dados mais recentes.
        >
        > Soltar uma tabela externa faz **não** excluir os dados, somente a definição da tabela.

    * **Formato de linha**: informa seção como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.
    * **Local de arquivo de texto como ARMAZENADO**: informa seção onde os dados estão armazenados (o diretório de dados do exemplo) e que ele está armazenado como texto.
    * **Selecione**: selecione uma contagem de todas as linhas onde coluna **t4** contêm o valor **[ERROR]**. Isso deve retornar um valor de **3** porque há três linhas que contêm esse valor.
    * **INPUT__FILE__NAME como '%.log'** - informa seção que estamos só deve retornar dados de arquivos que terminam em. log. Isso restringe a pesquisa para o arquivo de sample.log que contém os dados e impede que ela retornar dados do exemplo de outro arquivos de dados que não correspondem o esquema definimos.

3. Na barra de ferramentas, selecione o **HDInsight Cluster** que você deseja usar para esta consulta e selecione **Enviar para WebHCat** para executar as instruções como um trabalho de seção usando WebHCat. Você também pode enviar o trabalho usando o botão __Execute via HiveServer2__ se HiveServer2 está disponível na sua versão de cluster. A **Seção Resumo de trabalho** aparece e exibe informações sobre o trabalho em execução. Use o link **Atualizar** para atualizar as informações de trabalho, até que o **Status do trabalho** muda para **concluído**.

4. Use o link de **Saída de trabalho** para exibir a saída deste trabalho. Ele deve exibir `[ERROR] 3`, que é o valor retornado pela instrução SELECT.

5. Você também pode executar consultas de seção sem criar um projeto. Usando **Server Explorer**, expanda **Azure** > **HDInsight**, seu servidor de HDInsight de atalho e selecione **escrever uma consulta de seção**.

6. No documento **temp.hql** que aparece, adicione as seguintes instruções de HiveQL:

        set hive.execution.engine=tez;
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Essas instruções executam as seguintes ações:

    * **Criar tabela se não existir**: cria uma tabela se ele ainda não exista. Como a palavra-chave **externa** não for usada, isso é uma tabela interna, que é armazenada na seção data warehouse e é gerenciada completamente pela seção.

        > [AZURE.NOTE] Ao contrário de tabelas **externas** , soltar uma tabela interna também exclui os dados subjacentes.

    * **ARMAZENADO como ORC**: armazena os dados no formato do linha otimizado coluna (ORC). Este é um formato altamente otimizado e eficiente para armazenar dados de seção.
    * SUBSTITUIR **Inserir … Selecione**: seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]**, em seguida, insere os dados na tabela **logs de erros** .

7. Na barra de ferramentas, selecione **Enviar** para executar o trabalho. Use o **Status do trabalho** para determinar que o trabalho foi concluída com êxito.

8. Para verificar que o trabalho concluído e criado uma nova tabela, use o **Server Explorer** e expanda **Azure** > **HDInsight** > cluster HDInsight > **Seção bancos de dados** > e **padrão**. Você deve ver a tabela de **logs de erros** e a tabela de **log4jLogs** .

##<a id="summary"></a>Resumo

Como você pode ver, as ferramentas de HDInsight para Visual Studio fornecem uma maneira fácil de executar consultas de seção em um cluster de HDInsight, monitorar o status do trabalho e recuperar a saída.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre a seção no HDInsight:

* [Use a seção com Hadoop em HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop em HDInsight:

* [Usar porco com Hadoop em HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce com Hadoop em HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações sobre as ferramentas de HDInsight para Visual Studio:

* [Introdução ao HDInsight ferramentas para Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)


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



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
