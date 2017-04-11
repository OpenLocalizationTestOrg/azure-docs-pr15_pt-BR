<properties
    pageTitle="Use a ação de Script para instalar Spark em cluster Hadoop | Microsoft Azure"
    description="Saiba como personalizar um cluster de HDInsight com Spark usando a ação de Script."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/05/2016"
    ms.author="nitinme"/>

# <a name="install-and-use-spark-on-hdinsight-hadoop-clusters-using-script-action"></a>Instalar e usar Spark em clusters de HDInsight Hadoop usando a ação de Script

> [AZURE.IMPORTANT] Este artigo agora está obsoleta. HDInsight agora fornece Spark como um tipo de primeira classe cluster para clusters baseados no Windows, o que significa que você pode agora diretamente criar um cluster de Spark sem modificar um cluster de Hadoop usando a ação de Script. Usando o tipo de cluster Spark, você obtém um cluster de versão 3,2 HDInsight com Spark versão 1.3.1.  Para instalar versões diferentes do Spark, você pode usar a ação de Script. HDInsight fornece um script de ação de Script de amostra.

Saiba como instalar Spark em Windows com base em HDInsight usando a ação de Script e como executar consultas de Spark em HDInsight clusters.


**Artigos relacionados**

- [Criar Hadoop clusters no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre como criar clusters de HDInsight.

- [Introdução ao Apache Spark em HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): criar um cluster de HDInsight Spark.

- [Personalizar cluster HDInsight usando a ação de Script][hdinsight-cluster-customize]: informações gerais sobre como personalizar clusters HDInsight usando a ação de Script.

- [Ação de Script desenvolver scripts para HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-spark"></a>O que é Spark?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> é um paralelo de código-fonte aberto processamento framework que suporta o processamento de memória para melhorar o desempenho dos aplicativos analíticos grandes de dados. Recursos de computação na memória do Spark faça uma boa opção para algoritmos iterativos em cálculos de aprendizagem e gráfico de máquina.

Spark também pode ser usado para executar o processamento de dados baseado em disco convencional. Spark melhora a estrutura MapReduce tradicional evitando gravações em disco em estágios intermediários. Além disso, Spark é compatível com o armazenamento de Blob do Azure e de sistema de arquivo distribuído da Hadoop (HDFS) para que os dados existentes podem ser processados facilmente via Spark.

Este tópico fornece instruções sobre como personalizar um cluster de HDInsight para instalar Spark.

## <a name="install-spark-using-the-azure-portal"></a>Instalar Spark usando o Portal do Azure

Um exemplo de script instalar Spark em um cluster de HDInsight está disponível em um blob de armazenamento do Azure somente leitura em [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1). Esse script pode instalar Spark 1.2.0 ou Spark 1.0.2 dependendo da versão do cluster HDInsight que você cria.

- Se você usa o script ao criar um cluster de **3,2 HDInsight** , ele instala **Spark 1.2.0**.
- Se você usa o script ao criar um cluster de **HDInsight 3.1** , ele instala **Spark 1.0.2**.

Você pode modificar esse script ou criar seu próprio script para instalar outras versões do Spark.

> [AZURE.NOTE] O exemplo de script só funciona com HDInsight 3.1 e 3,2 clusters. Para obter mais informações sobre versões de cluster HDInsight, consulte [versões de cluster HDInsight](hdinsight-component-versioning.md).

1. Começar a criar um cluster usando a opção **Criar personalizada** , conforme descrito em [Hadoop criar clusters no HDInsight](hdinsight-provision-clusters.md#portal). Escolha a versão de cluster dependendo o seguinte:

    - Se você deseja instalar **Spark 1.2.0**, crie um cluster de 3,2 HDInsight.
    - Se você deseja instalar **Spark 1.0.2**, crie um cluster de HDInsight 3.1.


2. Na página **Ações de Script** do assistente, clique em **Adicionar ação de script** para fornecer detalhes sobre a ação de script, conforme mostrado abaixo:

    ![Ação de Script de uso para personalizar um cluster] (./media/hdinsight-hadoop-spark-install/HDI.CustomProvision.Page6.png "Ação de Script de uso para personalizar um cluster")

    <table border='1'>
        <tr><th>Propriedade</th><th>Valor</th></tr>
        <tr><td>Nome</td>
            <td>Especifique um nome para a ação de script. Por exemplo, <b>Instalar Spark</b>.</td></tr>
        <tr><td>Script URI</td>
            <td>Especifique o URI Uniform Resource Identifier () como o script que é invocado para personalizar o cluster. Por exemplo, <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1</i></td></tr>
        <tr><td>Tipo de nó</td>
            <td>Especifica os nós no qual o script de personalização é executado. Você pode escolher <b>todos os nós</b>, <b>apenas nós de cabeça</b>ou <b>apenas nós de trabalho</b>.
        <tr><td>Parâmetros</td>
            <td>Especifique os parâmetros, se necessário pelo script. O script para instalar Spark não exige quaisquer parâmetros, assim você pode deixar em branco.</td></tr>
    </table>

    Você pode adicionar mais de uma ação de script para instalar vários componentes em cluster. Depois de ter adicionado os scripts, clique na marca de seleção para começar a criar o cluster.

Você também pode usar o script instalar Spark em HDInsight usando o PowerShell do Azure ou o SDK do .NET HDInsight. Instruções para esses procedimentos são fornecidas neste tópico.

## <a name="use-spark-in-hdinsight"></a>Usar Spark em HDInsight
Spark fornece APIs no Scala, Python e Java. Você também pode usar o shell Spark interativo para executar Spark consultas. Esta seção fornece instruções sobre como usar as diferentes abordagens para trabalhar com Spark:

- [Usar o shell de Spark para executar consultas de interativas](#sparkshell)
- [Usar o shell de Spark para executar consultas SQL Spark](#sparksql)
- [Usar um programa de Scala autônomo](#standalone)

###<a name="sparkshell"></a>Usar o shell de Spark para executar consultas de interativas
Execute as seguintes etapas para executar consultas de Spark de um shell Spark interativo. Nesta seção, podemos executar uma consulta de Spark em um arquivo de dados de amostra (/ example/data/gutenberg/davinci.txt) que está disponível em clusters de HDInsight por padrão.

1. Azure no portal do, habilite a área de trabalho remota para o cluster que você criou com Spark instalado e, em seguida, remote no cluster. Para obter instruções, consulte [conectar ao HDInsight clusters usando o RDP](hdinsight-administer-use-management-portal.md#rdp).

2. Na seção protocolo RDP (Remote Desktop), da área de trabalho, abra a linha de comando do Hadoop (de um atalho da área de trabalho) e navegue até o local onde Spark está instalado; Por exemplo, **C:\apps\dist\spark-1.2.0**.


3. Execute o seguinte comando para iniciar o shell Spark:

         .\bin\spark-shell --master yarn

    Após o comando concluir a execução, você deve obter um Scala prompt:

         scala>

5. No prompt Scala, insira a consulta Spark mostrada abaixo. Essa consulta conta a ocorrência de cada palavra no arquivo davinci.txt que está disponível no/exemplo/dados/gutenberg/local do armazenamento de Blob do Azure associado ao cluster.

        val file = sc.textFile("/example/data/gutenberg/davinci.txt")
        val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
        counts.toArray().foreach(println)

6. A saída deve ser semelhante ao seguinte:

    ![Resultado da execução Scala shell interativo em um cluster de HDInsight](./media/hdinsight-hadoop-spark-install/hdi-scala-interactive.png)


7. Insira: q para sair do prompt de Scala.

        :q

###<a name="sparksql"></a>Usar o shell de Spark para executar consultas SQL Spark

Spark SQL permite que você use Spark para executar consultas relacionais expressas em Structured Query Language (SQL), HiveQL ou Scala. Nesta seção, examinaremos usando Spark para executar uma consulta de seção em uma tabela de seção de amostra. A tabela de seção usada nesta seção (chamado **hivesampletable**) está disponível por padrão quando você cria um cluster.

>[AZURE.NOTE] O exemplo abaixo foi criado contra **gerar 1.2.0**, que é instalado se você executar a ação de script ao criar HDInsight 3,2 cluster.

1. Azure no portal do, habilite a área de trabalho remota para o cluster que você criou com Spark instalado e, em seguida, remote no cluster. Para obter instruções, consulte [conectar ao HDInsight clusters usando o RDP](hdinsight-administer-use-management-portal.md#rdp).

2. Na sessão RDP, da área de trabalho, abra a linha de comando do Hadoop (de um atalho da área de trabalho) e navegue até o local onde Spark está instalado; Por exemplo, **C:\apps\dist\spark-1.2.0**.


3. Execute o seguinte comando para iniciar o shell Spark:

         .\bin\spark-shell --master yarn

    Após o comando concluir a execução, você deve obter um Scala prompt:

         scala>

4. No prompt Scala, defina o contexto de seção. Isso é necessário para trabalhar com consultas de seção usando Spark.

        val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

    Observe que **sc** é contexto de Spark padrão que é definido quando você inicia o shell Spark.

5. Executar uma consulta de seção usando o contexto de seção e imprimir a saída para o console. A consulta recupera dados em dispositivos de uma marca específica e limita o número de registros recuperados a 20.

        hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. Você deve ver uma saída semelhante ao seguinte:

    ![Saída executem Spark SQL em um cluster de HDInsight](./media/hdinsight-hadoop-spark-install/hdi-spark-sql.png)

7. Insira: q para sair do prompt de Scala.

        :q

### <a name="standalone"></a>Usar um programa de Scala autônomo

Nesta seção, podemos escrever um aplicativo de Scala que calcula o número de linhas que contêm as letras 'a' e 'b' em um arquivo de dados de amostra (/ example/data/gutenberg/davinci.txt) que está disponível em clusters de HDInsight por padrão. Para escrever e usar um programa de Scala autônomo com um cluster personalizado com instalação Spark, você deve executar as seguintes etapas:

- Escreva um programa Scala
- Construir o programa Scala para obter o arquivo. jar
- Executar o trabalho no cluster

#### <a name="write-a-scala-program"></a>Escreva um programa Scala
Nesta seção, você escreve um programa de Scala que calcula o número de linhas que contêm 'a' e 'b' no arquivo de dados de exemplo.

1. Abra um editor de texto e cole o seguinte código:


        /* SimpleApp.scala */
        import org.apache.spark.SparkContext
        import org.apache.spark.SparkContext._
        import org.apache.spark.SparkConf

        object SimpleApp {
          def main(args: Array[String]) {
            val logFile = "/example/data/gutenberg/davinci.txt"         //Location of the sample data file on Azure Blob storage
            val conf = new SparkConf().setAppName("SimpleApplication")
            val sc = new SparkContext(conf)
            val logData = sc.textFile(logFile, 2).cache()
            val numAs = logData.filter(line => line.contains("a")).count()
            val numBs = logData.filter(line => line.contains("b")).count()
            println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
          }
        }

2. Salve o arquivo com o nome **SimpleApp.scala**.

#### <a name="build-the-scala-program"></a>Construir o programa Scala
Nesta seção, você usa a <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">Ferramenta de criação simples</a> (ou sbt) para criar o programa Scala. sbt requer Java 1,6 ou posterior, portanto, verifique se que você tem a versão correta do Java instalado antes de continuar com esta seção.

1. Instale sbt de http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html.
2. Crie uma pasta chamada **SimpleScalaApp**e dentro dessa pasta, crie um arquivo denominado **simple.sbt**. Este é um arquivo de configuração que contém informações sobre a versão de Scala, dependências de biblioteca, etc. Cole o seguinte no arquivo simple.sbt e salvá-lo:


        name := "SimpleApp"

        version := "1.0"

        scalaVersion := "2.10.4"

        libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"



    >[AZURE.NOTE] Certificar-se de que você mantém as linhas em branco no arquivo.


3. Na pasta **SimpleScalaApp** , crie uma estrutura de diretório **\src\main\scala** e cole o programa Scala (**SimpleApp.scala**) que você criou anteriormente na pasta \src\main\scala.
4. Abra um prompt de comando, navegue até o diretório SimpleScalaApp e digite o seguinte comando:


        sbt package


    Depois que o aplicativo é compilado, você verá um arquivo de **simpleapp_2.10 1.0.jar** criado sob o diretório **\target\scala-2.10** na pasta raiz SimpleScalaApp.


#### <a name="run-the-job-on-the-cluster"></a>Executar o trabalho no cluster
Nesta seção, você remoto em cluster que tem Spark instalado e, em seguida, copie a pasta de destino do projeto SimpleScalaApp. Você usar o comando **Enviar spark** para submeter o trabalho no cluster.

1. Remoto em cluster que tem Spark instalado. No computador em que você escreveu e criado o programa SimpleApp.scala, copie a pasta **SimpleScalaApp\target** e cole-o em um local no cluster.
2. Na sessão RDP, da área de trabalho, abra a linha de comando do Hadoop e navegue até o local onde você colou a pasta de **destino** .
3. Digite o seguinte comando para executar o programa de SimpleApp.scala:


        C:\apps\dist\spark-1.2.0\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. Quando o programa terminar a execução, a saída é exibida no console.


        Lines with a: 21374, Lines with b: 11430

## <a name="install-spark-using-azure-powershell"></a>Instalar Spark usando o PowerShell do Azure

Nesta seção, usamos o cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para chamar scripts usando a ação de Script para personalizar um cluster. Antes de prosseguir, certifique-se de ter instalado e configurado o Azure PowerShell. Para obter informações sobre como configurar uma estação de trabalho para executar cmdlets do PowerShell do Azure para HDInsight, consulte [instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

Execute as seguintes etapas:

1. Abra uma janela do PowerShell do Azure e declarar as variáveis a seguir:

        # Provide values for these variables
        $subscriptionName = "<SubscriptionName>"        # Name of the Azure subscription
        $clusterName = "<HDInsightClusterName>"         # HDInsight cluster name
        $storageAccountName = "<StorageAccountName>"    # Azure Storage account that hosts the default container
        $storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
        $containerName = $clusterName
        $location = "<MicrosoftDataCenter>"             # Location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $clusterNodes = <ClusterSizeInNumbers>          # Number of nodes in the HDInsight cluster
        $version = "<HDInsightClusterVersion>"          # For example, "3.2"

2. Especifique os valores de configuração como nós no cluster e o armazenamento de padrão a ser usado.

        # Specify the configuration options
        Select-AzureSubscription $subscriptionName
        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
        $config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
        $config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
        $config.DefaultStorageAccount.StorageContainerName=$containerName

3. Use o cmdlet **Add-AzureHDInsightScriptAction** para adicionar uma ação de script à configuração de cluster. Mais tarde, quando o cluster estiver sendo criado, a ação de script é executada.

        # Add a script action to the cluster configuration
        $config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1

    O cmdlet **Add-AzureHDInsightScriptAction** usa os seguintes parâmetros:

    <table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
    <tr>
    <th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parâmetro</th>
    <th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definição</th></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Configuração</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">O objeto de configuração à qual script informações de ação são adicionadas.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome da ação script.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica os nós no qual o script de personalização é executado. Os valores válidos são HeadNode (para instalar no nó principal) ou DataNode (para instalar em todos os nós de dados). Você pode usar um ou ambos os valores.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">URI</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica o URI para o script que é executado.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parâmetros</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parâmetros necessários para o script. O script de exemplo usado neste tópico não exige todos os parâmetros e, portanto, você não vir esse parâmetro no trecho acima.
    </td></tr>
    </table>

4. Por fim, começar a criar um cluster personalizados Spark instalado.  

        # Start creating a cluster with Spark installed
        New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

Quando solicitado, digite as credenciais para o cluster. Pode levar alguns minutos antes que o cluster for criado.

## <a name="install-spark-using-powershell"></a>Instalar Spark usando o PowerShell

Consulte [Personalizar HDInsight clusters usando a ação de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).

## <a name="install-spark-using-net-sdk"></a>Instalar Spark usando o SDK do .NET

Consulte [Personalizar HDInsight clusters usando a ação de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell).


## <a name="see-also"></a>Consulte também

- [Criar Hadoop clusters no HDInsight](hdinsight-provision-clusters.md): criar clusters de HDInsight.
- [Introdução ao Apache Spark em HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): Introdução ao Spark em HDInsight.
- [Personalizar cluster HDInsight usando a ação de Script][hdinsight-cluster-customize]: Personalizar clusters HDInsight usando a ação de Script.
- [Ação de Script desenvolver scripts para HDInsight](hdinsight-hadoop-script-actions.md): desenvolver scripts de ação de Script.
- [Instalar o R em clusters de HDInsight] [ hdinsight-install-r] fornece instruções sobre como usar a personalização de cluster para instalar e usar R em clusters de HDInsight Hadoop. R é um ambiente de computação estatísticas e o idioma de código-fonte aberto. Ele fornece centenas de funções estatísticas internas e sua própria linguagem de programação que combina aspectos da programação orientada a objeto e funcional. Ele também fornece recursos de gráficos considerável.
- [Instalar Giraph em clusters de HDInsight](hdinsight-hadoop-giraph-install.md). Use a personalização de cluster para instalar Giraph em clusters de HDInsight Hadoop. Giraph permite que você execute graph processamento usando Hadoop e pode ser usada com Azurehdinsight.
- [Instalar Solr em clusters de HDInsight](hdinsight-hadoop-solr-install.md). Use a personalização de cluster para instalar Solr em clusters de HDInsight Hadoop. Solr permite que você execute operações de pesquisa avançada de dados armazenados.

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[powershell-install-configure]: powershell-install-configure.md
