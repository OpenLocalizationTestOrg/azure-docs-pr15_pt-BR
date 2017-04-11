<properties 
    pageTitle="Usar Apache Spark para criar aplicativos de aprendizado de máquina em HDInsight | Microsoft Azure" 
    description="Instruções passo a passo sobre como usar blocos de anotações com Apache Spark para criar aplicativos de aprendizado de máquina" 
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
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="build-machine-learning-applications-to-run-on-apache-spark-clusters-on-hdinsight-linux"></a>Criar aplicativos de aprendizado de máquina para executar em clusters Spark Apache no HDInsight Linux

Saiba como criar uma aplicativo usando um cluster de Apache Spark no HDInsight de aprendizado de máquina. Este artigo mostra como usar o bloco de anotações de Jupyter disponível com o cluster para criar e testar o nosso aplicativo. O aplicativo usa os dados de HVAC.csv de exemplo que está disponíveis em todos os clusters por padrão.

**Pré-requisitos:**

Você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster de Apache Spark no HDInsight Linux. Para obter instruções, consulte [criar Apache Spark clusters no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md). 

##<a name="data"></a>Mostrar os dados

Antes de começar criação do aplicativo, vamos compreenda a estrutura dos dados e o tipo de análise que faremos nos dados. 

Neste artigo, usamos o arquivo de dados do exemplo **HVAC.csv** que estão disponível na conta de armazenamento do Azure que você associado ao cluster de HDInsight. Dentro da conta de armazenamento, o arquivo está em **\HdiSamples\HdiSamples\SensorSampleData\hvac**. Baixe e abra o arquivo CSV para obter um instantâneo dos dados.  

![Instantâneo de dados HVAC] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.png "Instantâneo dos dados HVAC")

Os dados mostram a temperatura de destino e a temperatura real de um edifício com sistemas HVAC instalados. Vamos supor que a coluna de **sistema** representa o ID do sistema e a coluna **SystemAge** representa o número de anos que o sistema HVAC foi no lugar no edifício.

Usamos esses dados para prever se um edifício será mais quentes ou colder com base na temperatura de destino, sendo fornecida uma ID de sistema e a idade de sistema.

##<a name="app"></a>Escreva um aplicativo de aprendizado de máquina usando Spark MLlib

Neste aplicativo podemos usar um pipeline de Spark ML para executar uma classificação de documento. No pipeline, podemos dividir o documento em palavras, converter as palavras em um vetor recurso numéricos e finalmente criar um modelo de previsão usando os rótulos e vetores de recurso. Execute as seguintes etapas para criar o aplicativo.

1. No [Portal do Azure](https://portal.azure.com/), do startboard, clique no bloco para o seu cluster Spark (se você fixados-lo a startboard). Você também pode navegar para o seu cluster em **Procurar tudo** > **HDInsight Clusters**.   

2. Da lâmina cluster Spark, clique em **Painel de Cluster**e clique em **Bloco de anotações de Jupyter**. Se solicitado, digite as credenciais de administrador para o cluster.

    > [AZURE.NOTE] Você também pode atingir o bloco de anotações de Jupyter para o seu cluster abrindo a seguinte URL no seu navegador. Substitua o nome do seu cluster __CLUSTERNAME__ :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crie um novo bloco de anotações. Clique em **novo**e, em seguida, clique em **PySpark**.

    ![Criar um novo bloco de anotações de Jupyter] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.createnotebook.png "Criar um novo bloco de anotações de Jupyter")

3. Um novo bloco de anotações será criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de anotações na parte superior e insira um nome amigável.

    ![Fornecer um nome para o bloco de anotações] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.notebook.name.png "Fornecer um nome para o bloco de anotações")

3. Como você criou um bloco de anotações usando o núcleo PySpark, você não precisa criar qualquer contextos explicitamente. Os contextos Spark e seção serão automaticamente criados para você quando você executa a primeira célula de código. Você pode começar importando os tipos que são necessários para esse cenário. Cole o seguinte trecho em uma célula vazia e pressione **SHIFT + ENTER**. 

        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
        
        import os
        import sys
        from pyspark.sql.types import *
        
        from pyspark.mllib.classification import LogisticRegressionWithSGD
        from pyspark.mllib.regression import LabeledPoint
        from numpy import array
        
        
     
4. Agora você deve carregar os dados (hvac.csv), analisá-lo e usá-lo para treinar o modelo. Para isso, você define uma função que verifica se a temperatura real do edifício é maior do que a temperatura de destino. Se a temperatura real for maior, a construção está quente, indicado pelo valor **1.0**. Se a temperatura real é menor, a construção é fria, indicado pelo valor **0,0**. 

    Cole o seguinte trecho em uma célula vazia e pressione **SHIFT + ENTER**.

        
        # List the structure of data for better understanding. Becuase the data will be
        # loaded as an array, this structure makes it easy to understand what each element
        # in the array corresponds to

        # 0 Date
        # 1 Time
        # 2 TargetTemp
        # 3 ActualTemp
        # 4 System
        # 5 SystemAge
        # 6 BuildingID

        LabeledDocument = Row("BuildingID", "SystemInfo", "label")

        # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
        
        def parseDocument(line):
            values = [str(x) for x in line.split(',')]
            if (values[3] > values[2]):
                hot = 1.0
            else:
                hot = 0.0        
    
            textValue = str(values[4]) + " " + str(values[5])
    
            return LabeledDocument((values[6]), textValue, hot)

        # Load the raw HVAC.csv file, parse it using the function
        data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
        training = documents.toDF()


5. Configurar o pipeline de aprendizado de máquina Spark que consiste em três estágios: tokenizer, hashingTF e lr. Para obter mais informações sobre o que é um pipeline e como ele funciona, consulte <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">pipeline de aprendizado de máquina de Spark</a>.

    Cole o seguinte trecho em uma célula vazia e pressione **SHIFT + ENTER**.

        tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
        hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
        lr = LogisticRegression(maxIter=10, regParam=0.01)
        pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

6. Ajuste o pipeline para o documento de treinamento. Cole o seguinte trecho em uma célula vazia e pressione **SHIFT + ENTER**.

        model = pipeline.fit(training)

7. Verifique se o documento de treinamento para o ponto de verificação seu progresso com o aplicativo. Cole o seguinte trecho em uma célula vazia e pressione **SHIFT + ENTER**.

        training.show()

    Isso deve fornecer a saída similar ao seguinte:

        +----------+----------+-----+
        |BuildingID|SystemInfo|label|
        +----------+----------+-----+
        |         4|     13 20|  0.0|
        |        17|      3 20|  0.0|
        |        18|     17 20|  1.0|
        |        15|      2 23|  0.0|
        |         3|      16 9|  1.0|
        |         4|     13 28|  0.0|
        |         2|     12 24|  0.0|
        |        16|     20 26|  1.0|
        |         9|      16 9|  1.0|
        |        12|       6 5|  0.0|
        |        15|     10 17|  1.0|
        |         7|      2 11|  0.0|
        |        15|      14 2|  1.0|
        |         6|       3 2|  0.0|
        |        20|     19 22|  0.0|
        |         8|     19 11|  0.0|
        |         6|      15 7|  0.0|
        |        13|      12 5|  0.0|
        |         4|      8 22|  0.0|
        |         7|      17 5|  0.0|
        +----------+----------+-----+


    Voltar e verifique se a saída contra bruto arquivo CSV. Por exemplo, a primeira linha do arquivo CSV tem estes dados:

    ![Instantâneo de dados HVAC] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.first.row.png "Instantâneo dos dados HVAC")

    Observe como a temperatura real é menor que a temperatura de destino sugerindo que a construção é fria. Portanto, na saída do treinamento, o valor para o **rótulo** na primeira linha é **0,0**, o que significa que a construção não está quente.

8.  Prepare um conjunto de dados para executar o modelo de treinamento contra. Para fazer isso, passar em uma ID de sistema e a idade de sistema (indicada como **SystemInfo** na saída treinamento) e o modelo seria prever se a construção com essa ID de sistema e a idade de sistema seria mais quente (indicado pelo 1.0) ou Resfriador (indicado pelo 0,0).

    Cole o seguinte trecho em uma célula vazia e pressione **SHIFT + ENTER**.
        
        # SystemInfo here is a combination of system ID followed by system age
        Document = Row("id", "SystemInfo")
        test = sc.parallelize([(1L, "20 25"),
                      (2L, "4 15"),
                      (3L, "16 9"),
                      (4L, "9 22"),
                      (5L, "17 10"),
                      (6L, "7 22")]) \
            .map(lambda x: Document(*x)).toDF() 

9. Por fim, fazer previsões sobre os dados de teste. Cole o seguinte trecho em uma célula vazia e pressione **SHIFT + ENTER**.

        # Make predictions on test documents and print columns of interest
        prediction = model.transform(test)
        selected = prediction.select("SystemInfo", "prediction", "probability")
        for row in selected.collect():
            print row

10. Você deve ver uma saída semelhante à seguinte:

        Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
        Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
        Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
        Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
        Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
        Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))

    Da primeira linha na previsão, você pode ver que para um sistema HVAC com ID 20 e idade de sistema de 25 anos, a construção será quente (**previsão = 1.0**). O primeiro valor para DenseVector (0.49999) corresponde à previsão 0,0 e o segundo valor (0.5001) corresponde a previsão 1.0. Na saída, mesmo que o segundo valor só é ligeiramente maior, o modelo mostra **previsão = 1.0**.

11. Após terminar de executar o aplicativo, você deve desligar o bloco de anotações para liberar os recursos. Para fazer isso, no menu **arquivo** no bloco de anotações, clique em **Fechar e parar**. Este será desligar e feche o bloco de anotações.
           

##<a name="anaconda"></a>Use Anaconda scikit-Saiba biblioteca de aprendizado de máquina

Apache Spark clusters em HDInsight incluem bibliotecas de Anaconda. Isso também inclui a **scikit-Saiba** biblioteca para aprendizado de máquina. A biblioteca também inclui vários conjuntos de dados que você pode usar para criar aplicativos de amostra diretamente a partir de um bloco de anotações de Jupyter. Para obter exemplos sobre como usar o scikit-saber biblioteca, consulte [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

##<a name="seealso"></a>Consulte também

* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Spark com BI: executar análise de dados interativos usando Spark em HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

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

* [Usar os pacotes externos com blocos de anotações de Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter no seu computador e se conectar a um cluster de HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos

* [Gerenciar recursos para cluster Spark Apache no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Rastrear e depurar trabalhos em execução em um cluster de Apache Spark em HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md
