<properties
   pageTitle="MapReduce e área de trabalho remota com Hadoop em HDInsight | Microsoft Azure"
   description="Saiba como usar área de trabalho remota para conectar-se a Hadoop em HDInsight e executar MapReduce trabalhos."
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
   ms.date="09/27/2016"
   ms.author="larryfr"/>

# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>Usar MapReduce em Hadoop em HDInsight com a área de trabalho remota

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Neste artigo, você aprenderá como se conectar a um Hadoop em HDInsight cluster usando a área de trabalho remota e, em seguida, executar trabalhos MapReduce usando o comando Hadoop.

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um cluster baseado no Windows HDInsight (Hadoop em HDInsight)

* Um computador cliente executando o Windows 10, Windows 8 ou Windows 7

##<a id="connect"></a>Conectar-se com a área de trabalho remota

Habilitar a área de trabalho remota para o cluster HDInsight, em seguida, conectá-la seguindo as instruções em [conectar ao HDInsight clusters usando o RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="hadoop"></a>Use o comando Hadoop

Quando você está conectado na área de trabalho para o cluster HDInsight, use as etapas a seguir para executar um trabalho MapReduce usando o comando Hadoop:

1. Da área de trabalho HDInsight, comece a **linha de comando do Hadoop**. Isso abre um novo prompt de comando na **c:\apps\dist\hadoop-&lt;número da versão >** diretório.

    > [AZURE.NOTE] O número da versão é alterado conforme Hadoop for atualizado. A variável de ambiente **HADOOP_HOME** pode ser usada para localizar o caminho. Por exemplo, `cd %HADOOP_HOME%` altera os diretórios para o diretório Hadoop, sem a necessidade de saber o número da versão.

2. Para usar o comando **Hadoop** para executar um trabalho de MapReduce de exemplo, use o seguinte comando:

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/WordCountOutput

    Isso inicia a classe **wordcount** , que está contida no arquivo **hadoop-mapreduce-examples.jar** na pasta atual. Como entrada, ele usa o documento **wasbs://example/data/gutenberg/davinci.txt** e saída é armazenada em: **wasbs: / / / exemplo/dados/WordCountOutput**.

    > [AZURE.NOTE] Para obter mais informações sobre esse trabalho MapReduce e os dados de exemplo, consulte <a href="hdinsight-use-mapreduce.md">Usar MapReduce em HDInsight Hadoop</a>.

2. O trabalho emite detalhes conforme ela é processada, e retorna informações semelhante à seguinte quando o trabalho for concluído:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Quando o trabalho estiver concluído, use o seguinte comando para listar os arquivos de saída armazenados em **wasbs://example/data/WordCountOutput**:

        hadoop fs -ls wasbs:///example/data/WordCountOutput

    Isso deve exibir dois arquivos, **_SUCCESS** e **parte-r-00000**. O arquivo de **parte-r-00000** contém a saída para este trabalho.

    > [AZURE.NOTE] Alguns trabalhos MapReduce podem dividir os resultados em vários arquivos de **parte-r-# # #** . Em caso afirmativo, use o # sufixo para indicar a ordem dos arquivos.

4. Para exibir a saída, use o seguinte comando:

        hadoop fs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    Isso exibe uma lista das palavras contidas no arquivo **wasbs://example/data/gutenberg/davinci.txt** , juntamente com o número de vezes que ocorreu de cada palavra. A seguir é um exemplo de dados que serão contidos no arquivo:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

##<a id="summary"></a>Resumo

Como você pode ver, o comando de Hadoop fornece uma maneira fácil de executar MapReduce trabalhos em um cluster de HDInsight e, em seguida, exibir a saída de trabalho.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre MapReduce trabalhos em HDInsight:

* [Use o MapReduce em HDInsight Hadoop](hdinsight-use-mapreduce.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop em HDInsight:

* [Use a seção com Hadoop em HDInsight](hdinsight-use-hive.md)

* [Usar porco com Hadoop em HDInsight](hdinsight-use-pig.md)
