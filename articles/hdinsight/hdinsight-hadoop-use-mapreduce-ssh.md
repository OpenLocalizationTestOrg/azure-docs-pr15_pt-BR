<properties
   pageTitle="Conexão MapReduce e SSH com Hadoop em HDInsight | Microsoft Azure"
   description="Saiba como usar o SSH para executar trabalhos de MapReduce usando Hadoop em HDInsight."
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
   ms.date="08/23/2016"
   ms.author="larryfr"/>

# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>Usar MapReduce com Hadoop em HDInsight com SSH

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Neste artigo, você aprenderá como usar Secure Shell (SSH) para conectar-se a uma Hadoop em cluster HDInsight e, em seguida, enviar trabalhos de MapReduce usando os comandos do Hadoop.

> [AZURE.NOTE] Se você já estiver familiarizado com o uso de servidores baseados em Linux Hadoop, mas você ainda não conhece ao HDInsight, consulte [dicas de HDInsight baseados em Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um cluster baseado no Linux HDInsight (Hadoop em HDInsight)

* Um cliente SSH. Sistemas operacionais Linux, Unix e Mac deve ter um cliente SSH. Usuários do Windows devem baixar um cliente, como [Acabamento](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Conectar-se com SSH

Conectar-se para o nome de domínio totalmente qualificado (FQDN) do cluster HDInsight usando o comando SSH. O FQDN será o nome que você deu cluster, seguido por **. azurehdinsight.net**. Por exemplo, o seguinte deve se conectar a um cluster chamado **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se você forneceu uma chave de certificado para autenticação de SSH** quando você criou o cluster HDInsight, será necessário especificar o local da chave particular em seu sistema de cliente, por exemplo:

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Se você forneceu uma senha para autenticação de SSH** quando você criou o cluster HDInsight, você precisará fornecer a senha quando solicitado.

Para obter mais informações sobre como usar SSH com HDInsight, consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-clients"></a>Acabamento (clientes do Windows)

Windows não oferece um cliente SSH interno. Recomendamos o uso de **Acabamento**, que pode ser baixado em [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obter mais informações sobre como usar o acabamento, consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hadoop"></a>Use os comandos do Hadoop

1. Depois que você estiver conectado ao cluster HDInsight, use o seguinte comando **Hadoop** para iniciar um trabalho MapReduce:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    Isso inicia a classe **wordcount** , que está contida no arquivo **hadoop-mapreduce-examples.jar** . Como entrada, ele usa o documento **wasbs://example/data/gutenberg/davinci.txt** e saída é armazenada em **wasbs: / / / exemplo/dados/WordCountOutput**.

    > [AZURE.NOTE] Para obter mais informações sobre esse trabalho MapReduce e os dados de exemplo, consulte [Usar MapReduce em Hadoop em HDInsight](hdinsight-use-mapreduce.md).

2. O trabalho emite detalhes como ele processa e ela retorna informações semelhante à seguinte quando o trabalho for concluído:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Quando o trabalho for concluído, use o seguinte comando para listar os arquivos de saída que são armazenados em **wasbs://example/data/WordCountOutput**:

        hdfs dfs -ls wasbs:///example/data/WordCountOutput

    Isso deve exibir dois arquivos, **_SUCCESS** e **parte-r-00000**. O arquivo de **parte-r-00000** contém a saída para este trabalho.

    > [AZURE.NOTE] Alguns trabalhos MapReduce podem dividir os resultados em vários arquivos de **parte-r-# # #** . Em caso afirmativo, use o # sufixo para indicar a ordem dos arquivos.

4. Para exibir a saída, use o seguinte comando:

        hdfs dfs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    Isso exibe uma lista das palavras contidas no arquivo **wasbs://example/data/gutenberg/davinci.txt** e o número de vezes que ocorreu de cada palavra. A seguir é um exemplo de dados que serão contidos no arquivo:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

##<a id="summary"></a>Resumo

Como você pode ver, o Hadoop comandos fornecem uma maneira fácil de executar MapReduce trabalhos em um cluster de HDInsight e, em seguida, exibir a saída de trabalho.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre MapReduce trabalhos em HDInsight:

* [Use o MapReduce em HDInsight Hadoop](hdinsight-use-mapreduce.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop em HDInsight:

* [Use a seção com Hadoop em HDInsight](hdinsight-use-hive.md)

* [Usar porco com Hadoop em HDInsight](hdinsight-use-pig.md)
