<properties 
    pageTitle="Usar os pacotes externos com blocos de anotações de Jupyter no Apache Spark clusters em HDInsight | Azure"
    description="Instruções passo a passo sobre como configurar blocos de anotações de Jupyter disponíveis com HDInsight Spark clusters para usar os pacotes de Spark externos." 
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


# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight-linux"></a>Usar os pacotes externos com blocos de anotações de Jupyter em clusters Spark Apache no HDInsight Linux

>[AZURE.NOTE] Este artigo é aplicável a Spark 1.5.2 em HDInsight 3.3 e Spark 1.6.2 em HDInsight 3.4. 

Saiba como configurar um bloco de anotações de Jupyter em cluster Apache Spark em HDInsight (Linux) usar externo, os pacotes contribuiu comunidade que não estão incluídos de-prontos no cluster. 

Você pode pesquisar o [Repositório Maven](http://search.maven.org/) para a lista completa dos pacotes disponíveis. Você também pode obter uma lista dos pacotes disponíveis de outras fontes. Por exemplo, uma lista completa dos pacotes contribuiu comunidade está disponível em [Pacotes Spark](http://spark-packages.org/).

Neste artigo, você aprenderá como usar o pacote de [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) com o bloco de anotações de Jupyter.

##<a name="prerequisites"></a>Pré-requisitos

Você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster de Apache Spark no HDInsight Linux. Para obter instruções, consulte [criar Apache Spark clusters no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Usar os pacotes externos com blocos de anotações de Jupyter 

1. No [Portal do Azure](https://portal.azure.com/), do startboard, clique no bloco para o seu cluster Spark (se você fixados-lo a startboard). Você também pode navegar para o seu cluster em **Procurar tudo** > **HDInsight Clusters**.   

2. Da lâmina cluster Spark, clique em **Links rápidos**e clique em **Bloco de anotações de Jupyter**da lâmina do **Painel de controle de Cluster** . Se solicitado, digite as credenciais de administrador para o cluster.

    > [AZURE.NOTE] Você também pode atingir o bloco de anotações de Jupyter para o seu cluster abrindo a seguinte URL no seu navegador. Substitua o nome do seu cluster __CLUSTERNAME__ :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crie um novo bloco de anotações. Clique em **novo**e, em seguida, clique em **Spark**.

    ![Criar um novo bloco de anotações de Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.createnotebook.png "Criar um novo bloco de anotações de Jupyter")

3. Um novo bloco de anotações será criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de anotações na parte superior e insira um nome amigável.

    ![Fornecer um nome para o bloco de anotações] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.notebook.name.png "Fornecer um nome para o bloco de anotações")

4. Você usará o `%%configure` mágico para configurar o bloco de anotações para usar um pacote externo. Em blocos de anotações que usam pacotes externos, verifique se você chamar o `%%configure` mágico na primeira célula código. Isso garante que o núcleo está configurado para usar o pacote antes de inicia a sessão.

        %%configure
        { "packages":["com.databricks:spark-csv_2.10:1.4.0"] }


    >[AZURE.IMPORTANT] Se você esquecer configurar o núcleo na primeira célula, você pode usar o `%%configure` com a `-f` parâmetro, mas que irá reiniciar a sessão e andamento todos serão perdido.

5. No trecho de acima, `packages` espera uma lista das coordenadas maven em repositório Central Maven. Neste trecho, `com.databricks:spark-csv_2.10:1.4.0` é as coordenadas maven para pacote **spark-csv** . Aqui está como você constrói as coordenadas de um pacote.

    a. Localize o pacote no repositório Maven. Para este tutorial, usamos [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
    
    b. Do repositório, reunir os valores de **ID do grupo**, **ArtifactId**e **versão**.

    ![Pacotes externos de uso com o bloco de anotações de Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Pacotes externos de uso com o bloco de anotações de Jupyter")

    c. Concatene os três valores, separados por dois-pontos (**:**).

        com.databricks:spark-csv_2.10:1.4.0

6. Executar a célula de código com o `%%configure` mágico. Isto configurará a sessão Livy subjacente para usar o pacote fornecido. Nas células subsequentes no bloco de anotações, agora você pode usar o pacote, conforme mostrado abaixo.

        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

7. Você pode executar os trechos de código, como mostrado abaixo, para exibir os dados a partir do dataframe que você criou na etapa anterior.

        df.show()

        df.select("Time").count()


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

* [Executar trabalhos remotamente em um cluster de Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Usar o plug-in de ferramentas de HDInsight para IntelliJ IDEIA para criar e enviar Spark Scala aplicativos](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Usar o plug-in de ferramentas de HDInsight para IntelliJ IDEIA para depurar aplicativos de Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Usar blocos de anotações de Zeppelin com um cluster Spark em HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponíveis para o bloco de anotações de Jupyter em cluster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Instalar Jupyter no seu computador e se conectar a um cluster de HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos

* [Gerenciar recursos para cluster Spark Apache no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Rastrear e depurar trabalhos em execução em um cluster de Apache Spark em HDInsight](hdinsight-apache-spark-job-debugging.md)
