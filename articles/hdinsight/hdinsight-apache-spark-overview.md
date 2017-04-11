<properties 
    pageTitle="Uma visão geral do Apache Spark em HDInsight | Microsoft Azure" 
    description="Uma introdução ao Spark Apache no HDInsight e cenários em que usar Spark em HDInsight em seus aplicativos." 
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
    ms.topic="get-started-article" 
    ms.date="08/25/2016" 
    ms.author="nitinme"/>

# <a name="overview-apache-spark-on-hdinsight-linux"></a>Visão geral: Apache Spark no HDInsight Linux
 
<a href="http://spark.apache.org/" target="_blank">Apache Spark</a> é um paralelo de código-fonte aberto processamento framework que suporta o processamento de memória para melhorar o desempenho dos aplicativos analíticos grandes de dados. Mecanismo de processamento de Spark baseia-se para velocidade, facilidade de uso e a análise de sofisticados. Recursos de computação na memória do Spark faça uma boa opção para algoritmos iterativos em cálculos de aprendizagem e gráfico de máquina. Spark também é compatível com o armazenamento de Blob do Azure (WASB) para que os dados existentes armazenados no Azure facilmente podem ser processados via Spark.

Quando você cria um cluster Spark no HDInsight, você cria recursos de computação Azure com Spark instalado e configurado. Somente leva cerca de dez minutos para criar um cluster de Spark em HDInsight. Os dados sejam processados são armazenados no armazenamento de Blob do Azure. Consulte [usar armazenamento de blob do Microsoft Azure com HDInsight][hdinsight-storage].

![Apache Spark no Azure HDInsight] (./media/hdinsight-apache-spark-overview/hdispark.architecture.png  "Apache Spark no Azure HDInsight")


**Deseja começar a usar o Apache Spark em Azurehdinsight?** Consulte [início rápido: criar um cluster de Spark no HDInsight Linux e executar aplicativos de amostra usando Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md).

>[AZURE.NOTE] Para obter uma lista de problemas conhecidos e limitações com a versão atual, consulte [problemas de Spark Apache no Azure HDInsight (Linux) conhecidos](hdinsight-apache-spark-jupyter-spark-sql.md).


## <a name="why-use-spark-on-azure-hdinsight"></a>Por que usar Spark em Azurehdinsight? 

Azure HDInsight oferece um serviço de Spark totalmente gerenciado. Benefícios do uso Spark no HDInsight são:

| Recurso                             | Descrição       |
|-------------------------------------|-------------------|
| Facilidade de criação de clusters            | Você pode criar um novo cluster Spark em HDInsight em minutos usando o Portal de gerenciamento do Azure, Azure PowerShell ou o SDK do .NET HDInsight. Consulte [Introdução ao cluster Spark em HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) |
| Facilidade de uso                     | Spark em clusters de HDInsight inclui blocos de anotações de Jupyter pré-configurado. Você pode usá-los para o processamento de dados interativo e visualização. As URLs para o é https://CLUSTERNAME.azurehdinsight.net/jupyter. Substitua o nome do seu cluster Spark HDInsight __CLUSTERNAME__ .|
| APIs REST                       | Spark no HDInsight inclui [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), uma API REST baseadas em servidor de trabalho Spark para remotamente enviar e monitorar trabalhos em execução. |
| Suporte para armazenamento de Lucerne de dados do Azure | Spark em HDInsight pode ser configurado para usar o armazenamento de Lucerne de dados do Azure como um armazenamento adicional. Para obter mais informações sobre dados Lucerne Store, consulte [Visão geral do Azure Lucerne armazenamento de dados](../data-lake-store/data-lake-store-overview.md).
| Integração com os serviços do Azure | Spark em HDInsight vem com um conector para Hubs de evento do Azure. Os clientes podem criar aplicativos streaming usando os Hubs de evento, além de [Kafka](http://kafka.apache.org/), que já está disponível como parte do Spark. |
| Suporte para o servidor de R  | Você pode configurar um servidor de R em cluster HDInsight Spark para executar cálculos de R distribuídos com as velocidades prometidas com um cluster Spark. Para obter mais informações, consulte [começar a usar o servidor de R em HDInsight](hdinsight-hadoop-r-server-get-started.md).   |
| Integração com IntelliJ IDEIA  | Você pode usar o HDInsight Plugin para IntelliJ para criar e enviar aplicativos para um cluster de HDInsight Spark. Para obter mais informações, consulte [Usar HDInsight ferramentas plug-in para IntelliJ IDEIA criar Spark aplicativos para cluster HDInsight Spark Linux](hdinsight-apache-spark-intellij-tool-plugin.md). |
| Consultas simultâneas              | Spark no HDInsight suporta consultas simultâneas. Isso permite várias consultas de um usuário ou várias consultas de vários usuários e aplicativos compartilhem os mesmos recursos de cluster. |
| Em cache no SSDs                 | Você pode optar por dados em cache na memória ou no SSDs anexado a nós do cluster. Armazenamento em cache na memória oferece o melhor desempenho de consulta, mas pode ser caro; cache no SSDs fornece uma ótima opção para melhorar o desempenho de consulta sem a necessidade de criar um cluster de um tamanho que é necessário para caber o conjunto de dados inteiro na memória.|
| Integração com ferramentas de BI       | Spark para HDInsight fornece conectores para as ferramentas de BI como [Power BI](http://www.powerbi.com/) e [Tableau](http://www.tableau.com/products/desktop) para análise de dados.|
| Bibliotecas de Anaconda pré-carregado        | Spark clusters em HDInsight vêm com bibliotecas Anaconda pré-instaladas. [Anaconda](http://docs.continuum.io/anaconda/) fornece próximo 200 bibliotecas para aprendizado de máquina, análise de dados, visualização, etc.|
| Escalabilidade                     | Embora você possa especificar o número de nós no seu cluster durante a criação, talvez você queira aumente ou diminua o cluster para corresponder a carga de trabalho. Todos os clusters HDInsight permitem que você altere o número de nós no cluster. Além disso, clusters Spark podem ser descartados sem perda de dados, desde que todos os dados são armazenados no armazenamento de Blob do Azure. |
| Suporte 24/7                    | Spark em HDInsight vem com o suporte de 24/7 de nível empresarial e um SLA de 99,9% tempo.|



## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>Quais são os casos de uso para Spark em HDInsight?

Apache Spark no HDInsight habilita os principais cenários a seguir.

### <a name="interactive-data-analysis-and-bi"></a>Análise de dados interativos e BI

[Examinar um tutorial](hdinsight-apache-spark-use-bi-tools.md)

Apache Spark no HDInsight armazena dados em bolhas do Azure. Especialistas em negócios principais tomadores de decisão podem analisar e criar relatórios sobre esses dados e usar o Microsoft Power BI para construir relatórios interativos dos dados analisados. Analistas podem iniciar a partir de dados não estruturados/semi-estruturadas no armazenamento do Azure, definir um esquema para os dados usando os blocos de anotações e, em seguida, criar modelos de dados usando o Microsoft Power BI. Spark em HDInsight também oferece suporte a uma série de ferramentas de BI de terceiros como Tableau, Qlikview e Lumira SAP tornando uma plataforma ideal para analistas de dados, especialistas em negócios e principais tomadores de decisão.

### <a name="iterative-machine-learning"></a>Aprendizado de máquina iterativo

[Examinar um tutorial: prever construção temperaturas usar dados HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[Examinar um tutorial: prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

Apache Spark vem com [MLlib](http://spark.apache.org/mllib/), uma biblioteca desenvolvida sobre Spark de aprendizado de máquina. Além disso, Spark em HDInsight também inclui Anaconda, uma distribuição de Python com uma variedade de pacotes de aprendizado de máquina. Junte isso com um suporte interno para blocos de anotações de Jupyter e você tiver um ambiente de início da linha para a criação de aplicativos de aprendizado de máquina.  

### <a name="streaming-and-real-time-data-analysis"></a>Análise de dados de streaming e em tempo real

[Examinar um tutorial](hdinsight-apache-spark-eventhub-streaming.md)

Análise de dados em tempo real é usada para cenários desde reduz o tempo para uma visão de dados em processamento como ele chega, à criação de uma verdadeira solução de fluxo de dados. Spark no HDInsight oferece um suporte avançado para criar soluções de análise em tempo real. Enquanto Spark já tiver conectores para incluir dados de várias fontes, como Kafka, Flume, Twitter, ZeroMQ ou TCP sockets, Spark no HDInsight adiciona suporte de primeira classe para a inclusão de dados de Hubs de evento do Azure. Hubs de evento são o serviço de fila mais amplamente usado no Azure. Faz com um suporte de-de-prontos para Hubs de evento despertar no HDInsight uma plataforma ideal para a criação de pipeline de análise de tempo real.

##<a name="next-steps"></a>Quais são os componentes incluídos como parte de um cluster de Spark?

Spark no HDInsight inclui os seguintes componentes que estão disponíveis em clusters por padrão.

- [Spark Core](https://spark.apache.org/docs/1.5.1/). Inclui Spark Core, Spark SQL, Spark streaming APIs, GraphX e MLlib.
- [Anaconda](http://docs.continuum.io/anaconda/)
- [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Bloco de anotações de Jupyter](https://jupyter.org)

Spark em HDInsight também fornece um [driver ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) para conectividade para clusters Spark em HDInsight de ferramentas de BI como o Microsoft Power BI e Tableau.

## <a name="where-do-i-start"></a>Onde começar?

Comece com a criação de um cluster de Spark no HDInsight Linux. Consulte [início rápido: criar um cluster de Spark no HDInsight Linux e executar aplicativos de amostra usando Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md). 

## <a name="next-steps"></a>Próximas etapas

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

* [Usar os pacotes externos com blocos de anotações de Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter no seu computador e se conectar a um cluster de HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos

* [Gerenciar recursos para cluster Spark Apache no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Rastrear e depurar trabalhos em execução em um cluster de Apache Spark em HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
