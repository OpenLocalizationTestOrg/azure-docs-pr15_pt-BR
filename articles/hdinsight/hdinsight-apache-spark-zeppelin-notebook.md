<properties 
    pageTitle="Usar blocos de anotações de Zeppelin com cluster Spark no HDInsight Linux | Microsoft Azure" 
    description="Instruções passo a passo sobre como usar blocos de anotações de Zeppelin com clusters Spark no HDInsight Linux." 
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
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-hdinsight-linux"></a>Usar blocos de anotações de Zeppelin com cluster Spark Apache no HDInsight Linux

HDInsight Spark clusters incluem blocos de anotações de Zeppelin que você pode usar para executar Spark trabalhos. Neste artigo, você aprende a usar o bloco de anotações de Zeppelin em um cluster de HDInsight.


**Pré-requisitos:**

* Uma assinatura do Azure. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Um cluster de Apache Spark. Para obter instruções, consulte [criar Apache Spark clusters no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="launch-a-zeppelin-notebook"></a>Iniciar um bloco de anotações de Zeppelin

1. Da lâmina cluster Spark, clique em **Painel de Cluster**e clique em **Bloco de anotações de Zeppelin**. Se solicitado, digite as credenciais de administrador para o cluster.

    > [AZURE.NOTE] Você também pode atingir o bloco de anotações de Zeppelin para o seu cluster abrindo a seguinte URL no seu navegador. Substitua o nome do seu cluster __CLUSTERNAME__ :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Crie um novo bloco de anotações. No painel de cabeçalho, clique em **Bloco de anotações**e, em seguida, clique em **Criar nova anotação**.

    ![Criar um novo bloco de anotações de Zeppelin] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.createnewnote.png "Criar um novo bloco de anotações de Zeppelin")

    Insira um nome para o bloco de anotações e clique em **Criar anotação**.

3. Além disso, verifique se que o cabeçalho de bloco de anotações mostra um status conectado. Ele é indicado por um ponto verde no canto superior direito.

    ![Status de bloco de anotações de Zeppelin] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.newnote.connected.png "Status de bloco de anotações de Zeppelin")

4. Carregar dados de amostra para uma tabela temporária. Quando você cria um cluster Spark no HDInsight, o arquivo de dados de exemplo, **hvac.csv**, é copiado para a conta de armazenamento associado em **\HdiSamples\SensorSampleData\hvac**.

    No parágrafo vazio que é criado por padrão no novo bloco de anotações, cole o seguinte trecho.

        %livy.spark
        //The above magic instructs Zeppelin to use the Livy Scala interpreter

        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
        
        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
        
        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0), 
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()
        
        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")
        
    Pressione **SHIFT + ENTER** ou clique no botão **Executar** para o parágrafo executar o trecho. O status no canto direito do parágrafo devem progresso de pronto, pendente, execução como concluído. A saída aparece na parte inferior do mesmo parágrafo. A captura de tela tem a seguinte aparência:

    ![Criar uma tabela temporária de dados processados] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.loaddDataintotable.png "Criar uma tabela temporária de dados processados")

    Você também pode fornecer um título a cada parágrafo. Do canto direito, clique no ícone **configurações** e clique em **Mostrar título**.

5. Agora você pode executar instruções SQL Spark na tabela **hvac** . Cole a seguinte consulta em um novo parágrafo. A consulta recupera a ID de construção e a diferença entre o destino e reais temperaturas para cada prédio em uma determinada data. Pressione **SHIFT + ENTER**.

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 

    A instrução **sql %** no começo informa o bloco de anotações para usar o intérprete Livy Scala.

    A captura de tela a seguir mostra a saída.

    ![Executar uma instrução SQL de Spark usando o bloco de anotações] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Executar uma instrução SQL de Spark usando o bloco de anotações")

     Clique nas opções de exibição (realçadas no retângulo) para alternar entre diferentes representações para a mesma saída. Clique em **configurações** para escolher quais consitutes a chave e os valores na saída. A captura de tela acima usa **buildingID** como a chave e a média de **temp_diff** como o valor.

    
6. Você também pode executar instruções SQL Spark usando variáveis na consulta. O próximo trecho mostra como definir uma variável, **Temp**, na consulta com os valores possíveis que você deseja consultar com. Quando você primeiro executa a consulta, uma lista suspensa é preenchida automaticamente com os valores especificados para a variável.

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 

    Cole este trecho de código em um novo parágrafo e pressione **SHIFT + ENTER**. A captura de tela a seguir mostra a saída.

    ![Executar uma instrução SQL de Spark usando o bloco de anotações] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Executar uma instrução SQL de Spark usando o bloco de anotações")

    Para consultas subsequentes, você pode selecionar um novo valor na lista suspensa e execute a consulta novamente. Clique em **configurações** para escolher quais consitutes a chave e os valores na saída. A captura de tela acima usa **buildingID** como a chave, a média de **temp_diff** como o valor e **targettemp** como o grupo.

7. Reinicie o intérprete Livy para sair do aplicativo. Para fazer isso, abra configurações de intérprete clicando no conectado em nome de usuário do canto superior direito e clique em **intérprete**.

    ![Iniciar intérprete] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Seção de saída")

2. Role até configurações de intérprete Livy e clique em **Reiniciar**.

    ![Reinicie o intepreter Livy] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Reinicie o intepreter Zeppelin")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Como uso pacotes externos com o bloco de anotações?

Você pode configurar o bloco de anotações de Zeppelin cluster Apache Spark em HDInsight (Linux) para usar os pacotes externos, contribuiu com comunidade que não estão incluídos-de-prontos no cluster. Você pode pesquisar o [Repositório Maven](http://search.maven.org/) para a lista completa dos pacotes disponíveis. Você também pode obter uma lista dos pacotes disponíveis de outras fontes. Por exemplo, uma lista completa dos pacotes contribuiu comunidade está disponível em [Pacotes Spark](http://spark-packages.org/).

Neste artigo, você verá como usar o pacote de [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) com o bloco de anotações de Jupyter.

1. Abra configurações de intérprete. Do canto superior direito, clique o conectado em nome de usuário e clique em **intérprete**.

    ![Iniciar intérprete] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Seção de saída")

2. Role até configurações de intérprete Livy e clique em **Editar**.

    ![Alterar configurações de intérprete] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Alterar configurações de intérprete")

3. Adicionar uma nova chave, chamada **livy.spark.jars.packages** e defina seu valor no formato `group:id:version`. Portanto, se você quiser usar o pacote de [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) , você deve definir o valor da chave para `com.databricks:spark-csv_2.10:1.4.0`.

    ![Alterar configurações de intérprete] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Alterar configurações de intérprete")

    Clique em **Salvar** e depois reinicie o intérprete Livy.

4. **Dica**: se você quiser entender como chegar ao valor da chave digitada acima, veja como.

    a. Localize o pacote no repositório Maven. Para este tutorial, usamos [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
    
    b. Do repositório, reunir os valores de **ID do grupo**, **ArtifactId**e **versão**.

    ![Pacotes externos de uso com o bloco de anotações de Jupyter] (./media/hdinsight-apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Pacotes externos de uso com o bloco de anotações de Jupyter")

    c. Concatene os três valores, separados por dois-pontos (**:**).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Onde os blocos de anotações de Zeppelin são salvos?

Os blocos de anotações de Zeppelin são salvas na headnodes cluster. Portanto, se você excluir o cluster, os blocos de anotações serão excluídos também. Se você quiser preservar seus blocos de anotações para uso posterior em outros clusters, você deve exportá-los depois de concluir os trabalhos em execução. Para exportar um bloco de anotações, clique no ícone de **Exportar** , conforme mostrado na imagem abaixo.

![Baixar o bloco de anotações] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Baixar o bloco de anotações")

Isso salva o bloco de anotações como um arquivo JSON em seu local de download.

## <a name="livy-session-management"></a>Gerenciamento de sessão Livy

Quando você executa o primeiro parágrafo de código no seu bloco de anotações de Zeppelin, uma nova sessão Livy é criada no seu cluster HDInsight Spark. Esta sessão é compartilhada entre todos os blocos de anotações de Zeppelin que você criar posteriormente. Se por algum motivo o Livy sessão é encerrado (reinicialização cluster, etc.), não será capaz de executar trabalhos do bloco de anotações Zeppelin.

Nesse caso, você deve executar as seguintes etapas antes de iniciar trabalhos em execução de um bloco de anotações de Zeppelin. 

1. Reinicie o intérprete Livy do bloco de anotações Zeppelin. Para fazer isso, abra configurações de intérprete clicando no conectado em nome de usuário do canto superior direito e clique em **intérprete**.

    ![Iniciar intérprete] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Seção de saída")

2. Role até configurações de intérprete Livy e clique em **Reiniciar**.

    ![Reinicie o intepreter Livy] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Reinicie o intepreter Zeppelin")

3. Execute uma célula de código de um bloco de anotações existente do Zeppelin. Isso cria uma nova sessão Livy no cluster HDInsight.

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

* [Kernels disponíveis para o bloco de anotações de Jupyter em cluster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Usar os pacotes externos com blocos de anotações de Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter no seu computador e se conectar a um cluster de HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos

* [Gerenciar recursos para cluster Spark Apache no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Rastrear e depurar trabalhos em execução em um cluster de Apache Spark em HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md 







