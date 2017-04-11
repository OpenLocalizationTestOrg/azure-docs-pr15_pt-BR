<properties
    pageTitle="Enviar trabalhos Spark remotamente usando Livy | Microsoft Azure"
    description="Saiba como usar Livy com clusters de HDInsight para enviar trabalhos de Spark remotamente."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="nitinme"/>


# <a name="submit-spark-jobs-remotely-to-an-apache-spark-cluster-on-hdinsight-linux-using-livy"></a>Enviar trabalhos Spark remotamente para um cluster de Apache Spark no HDInsight Linux usando Livy

Cluster Spark Apache no Azure HDInsight inclui Livy, uma interface REST para enviar os trabalhos remotamente um cluster de Spark. Para documentação detalhada, consulte [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server).

Você pode usar Livy para executar Spark interativo conchas ou enviar trabalhos em lotes para ser executado em Spark. Este artigo fala sobre como usar Livy enviar trabalhos em lotes. A sintaxe abaixo usa ondulação para fazer chamadas de restante para o ponto de extremidade Livy.

**Pré-requisitos:**

Você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster de Apache Spark no HDInsight Linux. Para obter instruções, consulte [criar Apache Spark clusters no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="submit-a-batch-job-the-cluster"></a>Enviar um trabalho de lote cluster

Antes de enviar um trabalho em lotes, você deve carregar o jar de aplicativo no armazenamento de cluster associado ao cluster. Você pode usar [**AzCopy**](../storage/storage-use-azcopy.md), um utilitário de linha de comando, para fazê-lo. Existem muitos outros clientes que você pode usar para carregar dados. Você pode encontrar mais sobre eles em [carregar dados para trabalhos de Hadoop em HDInsight](hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Exemplos**:

* Se o arquivo jar no armazenamento de cluster (WASB)

        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"

* Se você quiser passar o nome de arquivo jar e o nome da classe como parte de um arquivo de entrada (neste exemplo, txt)

        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-batches-running-on-the-cluster"></a>Obtenha informações sobre lotes executado no cluster

    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Exemplos**:

* Se você quiser recuperar todos os lotes executado no cluster:

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

* Se você quiser recuperar um lote específico com uma determinada batchId

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"


## <a name="delete-a-batch-job"></a>Excluir um trabalho em lotes

    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Exemplo**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-and-high-availability"></a>Livy e alta disponibilidade

Livy fornece alta disponibilidade para Spark trabalhos em execução no cluster. Aqui estão alguns exemplos.

* Se o serviço de Livy falhar depois que você enviou um trabalho remotamente para um cluster de Spark, o trabalho continuará a ser executado em segundo plano. Quando Livy é fazer backup, ele restaura o status do trabalho e relatórios-o novamente.

* Blocos de anotações de Jupyter para HDInsight são ativados pelo Livy no back-end. Se um bloco de anotações está executando um trabalho Spark e o serviço de Livy obtém reiniciado, o bloco de anotações continuará a ser executado nas células de código. 

## <a name="show-me-an-example"></a>Mostrar um exemplo

Nesta seção, podemos ver exemplos de como usar Livy para enviar um aplicativo Spark, monitorar o progresso do aplicativo e, em seguida, excluir o trabalho. O aplicativo que usamos neste exemplo é aquele desenvolvidos no artigo [criar uma aplicação de Scala independente e para executar em cluster HDInsight Spark](hdinsight-apache-spark-create-standalone-application.md). As etapas a seguir presumem o seguinte:

* Você já tiver copiado sobre jar da aplicação para a conta de armazenamento associada ao cluster.
* Você tem ondulação instalada no computador onde você está tentando estas etapas.

Execute as etapas a seguir.

1. Vamos primeiro verificar se Livy está em execução no cluster. Podemos fazer isso obtendo uma lista de execução lotes. Se esta for a primeira vez que você está executando um trabalho usando Livy, isso deve retornar zero.

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

    Você deve receber uma saída semelhante à seguinte:

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    Observe como a última linha na saída diz **total: 0**, que sugere sem lotes em execução.

2. Agora vamos envie um trabalho em lotes. O trecho a seguir usa um arquivo de entrada (txt) para passar o nome de jar e o nome da classe como parâmetros. Essa é a abordagem recomendada se você estiver executando estas etapas de um computador com Windows.

        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

    Os parâmetros no arquivo **txt** são definidos da seguinte maneira:

        { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }

    Você deve ver uma saída semelhante à seguinte:

        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    Observe como a última linha da saída diz **estado: iniciando**. Também diz, **id: 0**. Este é o ID de lote.

3. Agora você pode recuperar o o status deste lote específico usando a identificação de lote.

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

    Você deve ver uma saída semelhante à seguinte:

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    A saída agora mostra **estado: sucesso**, que sugere que o trabalho foi concluído com êxito.

4. Se você quiser, agora você pode excluir o lote.

        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

    Você deve ver uma saída semelhante à seguinte:

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    A última linha da saída mostra que o lote foi excluído com êxito. Se você excluir um trabalho enquanto ele é executado, ele será essencialmente eliminar o trabalho. Se você excluir um trabalho que foi concluída com êxito ou caso contrário, ele exclui as informações de trabalho completamente.

## <a name="seealso"></a>Consulte também


* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Spark com BI: executar análise de dados interativos usando Spark em HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Spark com aprendizado de máquina: Spark de uso em HDInsight para analisar a temperatura de construção usando dados HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark com aprendizado de máquina: Spark de uso em HDInsight prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark: Uso Spark no HDInsight para criar aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análise de log de site usando Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos

* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Usar o plug-in de ferramentas de HDInsight para IntelliJ IDEIA para criar e enviar Spark Scala aplicativos](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Usar o plug-in de ferramentas de HDInsight para IntelliJ IDEIA para depurar aplicativos de Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Usar blocos de anotações de Zeppelin com um cluster Spark em HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponíveis para o bloco de anotações de Jupyter em cluster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Usar os pacotes externos com blocos de anotações de Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter no seu computador e se conectar a um cluster de HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos

* [Gerenciar recursos para cluster Spark Apache no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Rastrear e depurar trabalhos em execução em um cluster de Apache Spark em HDInsight](hdinsight-apache-spark-job-debugging.md)
