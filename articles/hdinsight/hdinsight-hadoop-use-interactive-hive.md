<properties
    pageTitle="Use a seção interativa no HDInsight | Microsoft Azure"
    description="Saiba como usar a seção interativo (seção em LLAP) em HDInsight."
    keywords=""
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="jgao"/>


# <a name="use-interactive-hive-in-hdinsight-preview"></a>Use a seção interativa no HDInsight (prévia)

Interativo seção (também conhecido como [Tempo ao vivo e processo]( https://cwiki.apache.org/confluence/display/Hive/LLAP)) é um novo [tipo de cluster]( hdinsight-hadoop-provision-linux-clusters.md#cluster-types)de HDInsight.  Seção interativa permite em cache de memória que torna seção consultas muito mais interativo e rápido. Esse novo recurso torna HDInsight do mundo de melhor desempenho, flexível e solução de grande volume aberta na nuvem na memória armazena (usando a seção e Spark) e advanced analytics por meio da profunda integração com serviços de R. 

O cluster seção interativo é diferente do cluster Hadoop. Ele contém apenas o serviço de seção. 

> [AZURE.NOTE] MapReduce, porco, Sqoop, Oozie e outros serviços serão removidos deste tipo de cluster em breve.
O serviço de seção no cluster seção interativos só é acessível via na seção Ambari exibição Beeline de seção ODBC. Ele não pode ser acessado por meio de console seção, Templeton, CLI do Azure e Azure PowerShell. 


 


## <a name="create-an-interactive-hive-cluster"></a>Criar um cluster de seção interativos

Interativo cluster seção só é suportado em clusters baseados em Linux. Para obter informações sobre a criação de clusters de HDInsight, consulte [clusters baseados em Linux criar Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).


## <a name="execute-hive-from-interactive-hive"></a>Executar a seção da seção interativa

Há diversas opções de como você pode executar consultas de seção:

- Executar a seção usando o modo de exibição de seção de Ambari

    Para obter informações sobre como usar o modo de exibição de seção, consulte [usar o modo de exibição de seção com Hadoop em HDInsight]( hdinsight-hadoop-use-hive-ambari-view.md).

- Executar a seção usando Beeline

    Para obter informações sobre como usar Beeline em HDInsight, consulte a [Seção uso com Hadoop em HDInsight com Beeline](hdinsight-hadoop-use-hive-beeline.md).

    Você pode usar Beeline da headnode ou de um nó de borda vazia.  É recomendável usar Beeline de um nó de borda vazia.  Para obter informações sobre como criar um cluster de HDInsight com um edgenode vazio, consulte [nós de borda vazia de uso em HDInsight](hdinsight-apps-use-edge-node.md).

- Executar a seção usando seção ODBC

    Para obter informações sobre como usar a seção ODBC, consulte [Conectar o Excel ao Hadoop com o driver ODBC do Microsoft seção](hdinsight-connect-excel-hive-odbc-driver.md).

**Para localizar a cadeia de conexão JDBC:**

1.  Entrar Ambari usando a seguinte URL: https://<ClusterName>. AzureHDInsight.net.
2.  No menu à esquerda, clique em **seção** .
3.  Clique no ícone realçado para copiar a URL:

    ![HDInsight Hadoop seção interativa LLAP JDBC](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="see-also"></a>Consulte também
-   [Hadoop baseados em Linux criar clusters no HDInsight](hdinsight-hadoop-provision-linux-clusters.md): Aprenda a criar clusters seção interativos em HDInsight.
-   [Usar seção com Hadoop em HDInsight com Beeline](hdinsight-hadoop-use-hive-beeline.md): Saiba como usar Beeline para enviar consultas de seção.
-   [Conectar o Excel ao Hadoop com o driver ODBC do Microsoft seção](hdinsight-connect-excel-hive-odbc-driver.md): Saiba como conectar o Excel à seção.
