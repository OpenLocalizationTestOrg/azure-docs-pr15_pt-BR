<properties 
    pageTitle="Usar bibliotecas personalizadas com um cluster de HDInsight Spark para analisar os logs de site | Microsoft Azure" 
    description="Usar bibliotecas de personalizado com um cluster de HDInsight Spark para analisar os logs de site" 
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

# <a name="analyze-website-logs-using-a-custom-library-with-apache-spark-cluster-on-hdinsight-linux"></a>Analisar os logs de site usando uma biblioteca personalizada com cluster Spark Apache no HDInsight Linux

Este bloco de anotações demonstra como analisar dados de log usando uma biblioteca personalizada com Spark em HDInsight. A biblioteca personalizada que usamos é uma biblioteca de Python chamada **iislogparser.py**.

> [AZURE.TIP] Este tutorial também está disponível como um bloco de anotações de Jupyter em um cluster de Spark (Linux) que você criar no HDInsight. A experiência de bloco de anotações lhe permite executar os trechos de Python do próprio bloco de anotações. Para executar o tutorial de dentro de um bloco de anotações, crie um cluster Spark, inicie um bloco de anotações de Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), e execute o bloco de anotações **analisar logs com Spark usando um library.ipynb personalizado** na pasta **PySpark** .

**Pré-requisitos:**

Você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster de Apache Spark no HDInsight Linux. Para obter instruções, consulte [criar Apache Spark clusters no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Salvar dados processados como um RDD

Nesta seção, podemos usar o bloco de anotações de [Jupyter](https://jupyter.org) associado a um cluster de Apache Spark no HDInsight para executar trabalhos que seus dados de amostra bruto de processo e salvá-lo como uma tabela de seção. Os dados de exemplo serão um arquivo. csv (hvac.csv) disponível em todos os clusters por padrão.

Depois que os dados forem salvos como uma tabela de seção, na próxima seção nós conectará à tabela de seção usando as ferramentas de BI como Power BI e Tableau.

1. No [Portal do Azure](https://portal.azure.com/), do startboard, clique no bloco para o seu cluster Spark (se você fixados-lo a startboard). Você também pode navegar para o seu cluster em **Procurar tudo** > **HDInsight Clusters**.   

2. Da lâmina cluster Spark, clique em **Painel de Cluster**e clique em **Bloco de anotações de Jupyter**. Se solicitado, digite as credenciais de administrador para o cluster.

    > [AZURE.NOTE] Você também pode atingir o bloco de anotações de Jupyter para o seu cluster abrindo a seguinte URL no seu navegador. Substitua o nome do seu cluster __CLUSTERNAME__ :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crie um novo bloco de anotações. Clique em **novo**e, em seguida, clique em **PySpark**.

    ![Criar um novo bloco de anotações de Jupyter] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.createnotebook.png "Criar um novo bloco de anotações de Jupyter")

3. Um novo bloco de anotações será criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de anotações na parte superior e insira um nome amigável.

    ![Fornecer um nome para o bloco de anotações] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.notebook.name.png "Fornecer um nome para o bloco de anotações")

4. Como você criou um bloco de anotações usando o núcleo PySpark, você não precisa criar qualquer contextos explicitamente. Os contextos Spark e seção serão automaticamente criados para você quando você executa a primeira célula de código. Você pode começar importando os tipos que são necessários para esse cenário. Cole o seguinte trecho em uma célula vazia e pressione **SHIFT + ENTER**.


        from pyspark.sql import Row
        from pyspark.sql.types import *


5. Crie um RDD usando os dados de log de amostra já disponíveis no cluster. Você pode acessar os dados na conta de armazenamento padrão associado ao cluster em **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**.


        logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


6. Recupere um log de amostra definido para verificar se a etapa anterior foi concluída com êxito.

        logs.take(5)

    Você deve ver uma saída semelhante à seguinte:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [u'#Software: Microsoft Internet Information Services 8.0',
         u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analisar dados de log usando uma biblioteca de Python personalizada

7. Na saída acima, as primeiras linhas de algumas incluem as informações de cabeçalho e cada linha restante corresponde o esquema descrito nesse cabeçalho. Analisar esses logs pode ser complicado. Assim, podemos usar uma biblioteca de Python personalizada (**iislogparser.py**) que faz a análise tais logs muito mais fácil. Por padrão, essa biblioteca está incluída no seu cluster Spark em HDInsight em **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

    No entanto, essa biblioteca não estiver no `PYTHONPATH` para nós não pode usá-lo usando uma instrução de importação como `import iislogparser`. Para usar essa biblioteca, podemos deverá distribuí-lo para todos os nós de trabalho. Execute o trecho a seguir.


        sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


9. `iislogparser`Fornece uma função `parse_log_line` que retorna `None` se uma linha de log é uma linha de cabeçalho e retorna uma instância do `LogLine` classe se encontrar uma linha de log. Use o `LogLine` classe extrair somente as linhas de log do RDD:

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()


10. Recupere algumas linhas de log extraídas para verificar se a etapa concluída com êxito.

        logLines.take(2)

    A saída deve ser semelhante ao seguinte:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]


11. O `LogLine` classe, por sua vez, tem alguns métodos úteis, como `is_error()`, que retorna se uma entrada de log tem um código de erro. Use essa opção para calcular o número de erros nas linhas de log extraídos e efetue todos os erros em um arquivo diferente.

        errors = logLines.filter(lambda p: p.is_error())
        numLines = logLines.count()
        numErrors = errors.count()
        print 'There are', numErrors, 'errors and', numLines, 'log entries'
        errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

    Você deve ver uma saída semelhante ao seguinte:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        There are 30 errors and 646 log entries

12. Você também pode usar **Matplotlib** para construir uma visualização dos dados. Por exemplo, se você quiser isolar a causa de solicitações que são executadas por um longo tempo, você talvez queira encontrar os arquivos que levar mais tempo para servir em média.
O trecho a seguir recupera os recursos de 25 superiores que levaram mais tempo para atender a uma solicitação.

        def avgTimeTakenByKey(rdd):
            return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                    lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                    lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                      .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))
            
        avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

    Você deve ver uma saída semelhante ao seguinte:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [(u'/blogposts/mvc4/step13.png', 197.5),
         (u'/blogposts/mvc2/step10.jpg', 179.5),
         (u'/blogposts/extractusercontrol/step5.png', 170.0),
         (u'/blogposts/mvc4/step8.png', 159.0),
         (u'/blogposts/mvcrouting/step22.jpg', 155.0),
         (u'/blogposts/mvcrouting/step3.jpg', 152.0),
         (u'/blogposts/linqsproc1/step16.jpg', 138.75),
         (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
         (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
         (u'/blogposts/nested/step2.jpg', 126.0),
         (u'/blogposts/adminpack/step1.png', 124.0),
         (u'/BlogPosts/datalistpaging/step2.png', 118.0),
         (u'/blogposts/mvc4/step35.png', 117.0),
         (u'/blogposts/mvcrouting/step2.jpg', 116.5),
         (u'/blogposts/aboutme/basketball.jpg', 109.0),
         (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
         (u'/blogposts/mvc4/step12.png', 106.0),
         (u'/blogposts/linq8/step0.jpg', 105.5),
         (u'/blogposts/mvc2/step18.jpg', 104.0),
         (u'/blogposts/mvc2/step11.jpg', 104.0),
         (u'/blogposts/mvcrouting/step1.jpg', 104.0),
         (u'/blogposts/extractusercontrol/step1.png', 103.0),
         (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
         (u'/blogposts/mvcrouting/step21.jpg', 101.0),
         (u'/blogposts/mvc4/step1.png', 98.0)]


13. Você também pode apresentar essas informações no formulário de plotagem. A primeira etapa para criar um gráfico, vamos primeiro criar uma tabela temporária **AverageTime**. A tabela agrupa os logs por tempo para ver se houve qualquer picos incomuns latência a qualquer momento específico.

        avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
        schema = StructType([StructField('Minutes', IntegerType(), True),
                             StructField('Time', FloatType(), True)])
                             
        avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
        avgTimeTakenByMinuteDF.registerTempTable('AverageTime')

14. Em seguida, você pode executar a seguinte consulta SQL para obter todos os registros na tabela **AverageTime** .

        %%sql -o averagetime
        SELECT * FROM AverageTime

    O `%%sql` mágico seguido por `-o averagetime` garante que a saída da consulta é mantida localmente no servidor Jupyter (normalmente a headnode do cluster). A saída é mantida como um dataframe [Pandas](http://pandas.pydata.org/) com o nome especificado **averagetime**.

    Você deve ver uma saída semelhante ao seguinte:

    ![Saída da consulta SQL] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/sql.output.png "Saída da consulta SQL")

    Para obter mais informações sobre o `%%sql` mágico, bem como outras magics disponíveis com núcleo PySpark, consulte [Kernels disponíveis nos notebooks Jupyter com clusters Spark HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

15. Agora você pode usar Matplotlib, uma biblioteca usada para construir a visualização de dados para criar um gráfico. Porque a plotagem deve ser criada a partir do dataframe localmente persistentes **averagetime** , o trecho de código deve começar com o `%%local` mágico. Isso garante que o código é executado localmente no servidor Jupyter.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt
        
        plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
        plt.xlabel('Time (min)')
        plt.ylabel('Average time taken for request (ms)')

    Você deve ver uma saída semelhante ao seguinte:

    ![Saída Matplotlib] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdi-apache-spark-web-log-analysis-plot.png "Saída Matplotlib")

16. Após terminar de executar o aplicativo, você deve desligar o bloco de anotações para liberar os recursos. Para fazer isso, no menu **arquivo** no bloco de anotações, clique em **Fechar e parar**. Este será desligar e feche o bloco de anotações.
    

## <a name="seealso"></a>Consulte também


* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Spark com BI: executar análise de dados interativos usando Spark em HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Spark com aprendizado de máquina: Spark de uso em HDInsight para analisar a temperatura de construção usando dados HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark com aprendizado de máquina: Spark de uso em HDInsight prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark: Uso Spark no HDInsight para criar aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)

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
