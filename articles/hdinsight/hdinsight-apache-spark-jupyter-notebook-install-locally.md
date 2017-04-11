<properties 
    pageTitle="Instalar o bloco de anotações de Jupyter no seu computador e conecte-a um cluster de HDInsight Spark | Microsoft Azure" 
    description="Saiba mais sobre como instalar o bloco de anotações de Jupyter localmente em seu computador e conectá-la a um cluster de Apache Spark no Azure HDInsight." 
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
    ms.date="09/26/2016" 
    ms.author="nitinme"/>


# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-cluster-on-hdinsight-linux"></a>Instalar o bloco de anotações de Jupyter no seu computador e se conectar ao cluster Spark Apache no HDInsight Linux

Neste artigo, você aprenderá como instalar o bloco de anotações de Jupyter, com o PySpark personalizado (por Python) e kernels Spark (para Scala) com mágico Spark e conectar o bloco de anotações para um cluster de HDInsight. Pode haver vários motivos para instalar o Jupyter no computador local, e pode haver alguns desafios também. Para obter uma lista de motivos e desafios, consulte a seção [por que devo instalar Jupyter em Meu computador](#why-should-i-install-jupyter-on-my-computer) no final deste artigo.

Há três etapas principais envolvidas na instalação Jupyter e a magia Spark em seu computador.

* Instalar o bloco de anotações de Jupyter
* Instalar os kernels PySpark e Spark com a magia Spark
* Configurar mágico Spark para acessar o cluster Spark em HDInsight

Para obter mais informações sobre os kernels personalizados e a magia Spark disponível para blocos de anotações de Jupyter com cluster HDInsight, consulte [Kernels disponíveis para blocos de anotações de Jupyter com clusters Apache Spark Linux em HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md).

##<a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos listados aqui não são para instalar o Jupyter. Estas são de conexão do bloco de anotações de Jupyter com um cluster de HDInsight assim que o bloco de anotações estiver instalado.

- Uma assinatura do Azure. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster de Apache Spark no HDInsight Linux. Para obter instruções, consulte [criar Apache Spark clusters no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Instalar o bloco de anotações de Jupyter no seu computador

Você deve instalar Python antes de instalar o Jupyter blocos de anotações. Python e Jupyter estão disponíveis como parte da [distribuição de Ananconda](https://www.continuum.io/downloads). Quando você instala Anaconda, você realmente instalar uma distribuição de Python. Quando Anaconda estiver instalado, você adicionar a instalação Jupyter executando um comando. Esta seção fornece as instruções que devem ser seguidas.

1. Baixar o [instalador do Anaconda](https://www.continuum.io/downloads) para sua plataforma e execute a instalação. Enquanto estiver executando o Assistente de configuração, verifique se que você selecionar a opção para adicionar Anaconda à sua variável de caminho.

2. Execute o seguinte comando para instalar o Jupyter.

        conda install jupyter

    Para obter mais informações sobre installting Jupyter, consulte [Instalando Jupyter usando Anaconda](http://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-the-kernels-and-spark-magic"></a>Instalar os kernels e mágico Spark

Para obter instruções sobre como instalar o mágico Spark, os kernels PySpark e Spark, consulte a [documentação de sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation) no GitHub.

## <a name="configure-spark-magic-to-access-the-hdinsight-spark-cluster"></a>Configurar mágico Spark para acessar o cluster HDInsight Spark

Nesta seção você configurar a magia Spark que você instalou anteriormente para se conectar a um cluster de Apache Spark que você já deve ter criado no Azure HDInsight.

1. As informações de configuração de Jupyter geralmente são armazenadas no diretório inicial de usuários. Para localizar seu diretório inicial em qualquer plataforma de sistema operacional, digite os seguintes comandos.

    Inicie o shell de Python. Em uma janela de comando, digite o seguinte:

        python

    No shell Python, insira o seguinte comando para descobrir o diretório inicial.

        import os
        print(os.path.expanduser('~'))

2. Navegue até o diretório inicial e crie uma pasta chamada **.sparkmagic** se ele ainda não exista.

3. Dentro da pasta, crie um arquivo chamado **config.json** e adicione o seguinte trecho JSON dentro dela.

        {
          "kernel_python_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          },
          "kernel_scala_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          }
        }

4. Substitua **{USERNAME}**, **{CLUSTERDNSNAME}**e **{BASE64ENCODEDPASSWORD}** com valores apropriados. Você pode usar um número de utilitários em sua linguagem de programação favorita ou online para gerar uma senha de codificação base64 para sua senha YReal. Seria um trecho de Python simple para executar do prompt de comando:

        python -c "import base64; print(base64.b64encode('{YOURPASSWORD}'))"

5. Inicie Jupyter. Use o seguinte comando no prompt de comando.

        jupyter notebook

6. Verifique se você pode se conectar ao cluster usando o bloco de anotações de Jupyter e que você pode usar a magia Spark disponível com os kernels. Execute as etapas a seguir.

    1. Crie um novo bloco de anotações. No canto direito, clique em **novo**. Você deve ver o núcleo de padrão **Python2** e os dois novos kernels instalados, **PySpark** e **Spark**.

        ![Criar um novo bloco de anotações de Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Criar um novo bloco de anotações de Jupyter")

    
        Clique em **PySpark**.


    2. Execute o trecho de código a seguir.

            %%sql
            SELECT * FROM hivesampletable LIMIT 5

        Se com êxito, você pode recuperar a saída, sua conexão ao cluster HDInsight é testada.

    >[AZURE.TIP] Se você deseja atualizar a configuração de bloco de anotações para se conectar a um cluster diferente, atualize o config.json com o novo conjunto de valores, conforme mostrado na etapa 3 acima. 

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Por que devo instalar Jupyter em Meu computador?

Pode haver vários motivos por que talvez você queira instalar Jupyter no seu computador e conecte-o a um cluster de Spark em HDInsight.

* Apesar de blocos de anotações de Jupyter já estão disponíveis no cluster Spark no Azure HDInsight, instalando Jupyter em seu computador fornece a opção para criar seus blocos de anotações localmente, teste seu aplicativo em relação a um cluster em execução e carregue os blocos de anotações ao cluster. Para carregar os blocos de anotações ao cluster, você pode carregá-los usando o bloco de anotações de Jupyter que esteja executando ou cluster, ou salvá-los para a pasta /HdiNotebooks na conta de armazenamento associada ao cluster. Para obter mais informações sobre como os blocos de anotações são armazenados no cluster, consulte [onde Jupyter blocos de anotações são armazenados](hdinsight-apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Os blocos de anotações disponíveis localmente, você pode se conectar a clusters Spark diferentes com base em suas necessidades de aplicativo.
* Você pode usar o GitHub para implementar um sistema de controle de origem e ter controle de versão para os blocos de anotações. Você também pode ter um ambiente colaborativo onde vários usuários podem trabalhar com o mesmo bloco de anotações.
* Você pode trabalhar com blocos de anotações localmente sem mesmo ter um cluster para cima. Você só precisará de um cluster para testar seus blocos de anotações contra, não para gerenciar manualmente seus blocos de anotações ou um ambiente de desenvolvimento.
* Talvez seja mais fácil de configurar seu próprio ambiente de desenvolvimento local que configurar a instalação Jupyter no cluster.  Você pode aproveitar todo o software que você instalou localmente sem configurar um ou mais clusters remotos.

>[AZURE.WARNING] Com Jupyter instalado no seu computador local, vários usuários podem executar o mesmo bloco de anotações no mesmo cluster Spark ao mesmo tempo. Em uma situação, várias sessões de Livy são criadas. Se você enfrentar algum e deseja depurar que, será uma tarefa complexa para controlar quais sessão Livy pertence à qual o usuário.




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

* [Usar os pacotes externos com blocos de anotações de Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Gerenciar recursos

* [Gerenciar recursos para cluster Spark Apache no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Rastrear e depurar trabalhos em execução em um cluster de Apache Spark em HDInsight](hdinsight-apache-spark-job-debugging.md)
