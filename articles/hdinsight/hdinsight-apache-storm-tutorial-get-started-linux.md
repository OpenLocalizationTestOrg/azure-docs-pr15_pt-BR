<properties
    pageTitle="Tutorial de tempestade Apache: Introdução ao baseados em Linux tempestade em HDInsight | Microsoft Azure"
    description="Começar a usar a análise de dados grande usando Apache tempestade e as amostras de tempestade Starter em HDInsight baseado em Linux. Saiba como usar tempestade para processar dados em tempo real."
    keywords="Tempestade Apache, tutorial de tempestade apache, a análise de dados grande, starter tempestade"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/12/2016"
   ms.author="larryfr"/>


# <a name="apache-storm-tutorial-get-started-with-the-storm-starter-samples-for-big-data-analytics-on-hdinsight"></a>Tutorial de tempestade Apache: começar a usar as amostras de tempestade Starter para análise de dados grande em HDInsight

Tempestade Apache é um sistema de computação scalable, tolerância, distribuído, em tempo real para o processamento de fluxos de dados. Com tempestade em Azurehdinsight, você pode criar um cluster de tempestade baseado em nuvem que realiza a análise de dados grande em tempo real.

> [AZURE.NOTE] As etapas neste artigo criam um cluster de HDInsight baseados em Linux. Para obter etapas criar uma tempestade baseado no Windows em cluster HDInsight, consulte [tutorial Apache tempestade: começar a usar a amostra de tempestade Starter usando a análise de dados em HDInsight](hdinsight-apache-storm-tutorial-get-started.md)

## <a name="prerequisites"></a>Pré-requisitos

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Você deve ter o seguinte para concluir este tutorial Apache tempestade com êxito:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Familiaridade com SSH e SCP**. Para obter mais informações sobre como usar SSH e SCP com HDInsight, consulte o seguinte:

    - **Clientes Linux, Unix ou OS X**: consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    - **Clientes do Windows**: consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

### <a name="access-control-requirements"></a>Requisitos de controle de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Criar um cluster de tempestade

Nesta seção, você cria um cluster de versão 3,2 HDInsight (tempestade versão 0.9.3) usando um modelo do Gerenciador de recursos do Azure. Para obter informações sobre versões de HDInsight e seus SLAs, consulte [o controle de versão do HDInsight componente](hdinsight-component-versioning.md). Para outros métodos de criação de cluster, consulte [criar HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem a seguir para abrir o modelo no portal do Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-storm-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    O modelo está localizado em um contêiner de blob pública, *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-storm-cluster-in-hdinsight.json*. 
   
2. Da lâmina parâmetros, insira o seguinte:

    - **ClusterName**: insira um nome para o cluster Hadoop que será criado.
    - **Nome de login de cluster e de senha**: O nome de login padrão é administrador.
    - **SSH nome de usuário e senha**.
    
    Escreva esses valores.  Você precisará deles mais tarde no tutorial.

    > [AZURE.NOTE] SSH é usada para acessar remotamente o cluster de HDInsight usando uma linha de comando. O nome de usuário e senha usados aqui é usado quando se conectando ao cluster por meio de SSH. Além disso, o nome de usuário SSH deve ser exclusivo, como ela cria uma conta de usuário em todos os nós de cluster HDInsight. A seguir está alguns dos nomes de conta reservados para uso pelos serviços em cluster e não pode ser usada como o nome de usuário SSH:
    >
    > raiz, hdiuser, tempestade, hbase, ubuntu, zookeeper, hdfs, fio colorido, mapred, hbase, seção, oozie, falcon, sqoop, administrador, tez, hcat, zookeeper hdinsight.

    > Para obter mais informações sobre como usar SSH com HDInsight, consulte um dos seguintes artigos:

    > * [Use SSH com baseado em Linux Hadoop em HDInsight Linux, Unix ou dos X](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3. Clique **Okey** para salvar os parâmetros.

4. da lâmina **implantação personalizada** , clique em caixa de menu suspenso de **grupo de recursos** e clique em **novo** para criar um novo grupo de recursos. O grupo de recursos é um contêiner que agrupa cluster, a conta de armazenamento dependentes e outros recursos vinculados.

5. Clique **termos legais**e, em seguida, clique em **criar**.

6. clique em **criar**. Você verá um novo bloco intitulado Submitting implantação para implantação do modelo. Ele leva cerca de cerca de 20 minutos para criar o cluster e o banco de dados SQL.


##<a name="run-a-storm-starter-sample-on-hdinsight"></a>Executar uma amostra de tempestade Starter em HDInsight

Os exemplos de [tempestade starter](https://github.com/apache/storm/tree/master/examples/storm-starter) estão incluídos no cluster HDInsight. Nas etapas a seguir, você executará o exemplo WordCount.

1. Conectar-se ao cluster HDInsight usando SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    Se você usou uma senha para proteger sua conta de usuário SSH, você será solicitado para inseri-la. Se você usou uma chave pública, você talvez precise usar o `-i` parâmetro para especificar a chave privada correspondente. Por exemplo, `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
        
    Para obter mais informações sobre como usar SSH com baseado em Linux HDInsight, consulte os seguintes artigos:
    
    * [Use SSH com baseado em Linux Hadoop em HDInsight Linux, Unix ou dos X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows)

2. Use o seguinte comando para iniciar um exemplo de topologia:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar storm.starter.WordCountTopology wordcount
        
    > [AZURE.NOTE] O `*` parte do nome do arquivo é usada para corresponder ao número de versão, que é alterado conforme HDInsight for atualizado.

    Isso iniciará a topologia de WordCount de exemplo em cluster, com um nome amigável de 'wordcount'. Ele aleatoriamente gerar sentenças e contar a ocorrência de cada palavra nas frases.

    > [AZURE.NOTE] Ao enviar topologia ao cluster, primeiro você deve copiar o arquivo jar que contém o cluster antes de usar o `storm` comando. Isso pode ser feito usando o `scp` comando do cliente onde existe o arquivo. Por exemplo,`scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > O exemplo WordCount e outros exemplos de starter tempestade, já estão incluídos em seu cluster em `/usr/hdp/current/storm-client/contrib/storm-starter/`.

##<a name="monitor-the-topology"></a>Monitorar a topologia

A interface de usuário de tempestade fornece uma interface da web para trabalhar com executando topologias e está incluído em seu cluster HDInsight.

Use as etapas a seguir para monitorar a topologia usando a interface do usuário tempestade:

1. Abra um navegador da web para https://CLUSTERNAME.azurehdinsight.net/stormui, onde __CLUSTERNAME__ é o nome do seu cluster. Isso abrirá a interface do usuário tempestade.

    > [AZURE.NOTE] Se solicitado a fornecer um nome de usuário e senha, insira o administrador de cluster (admin) e senha que você usou quando criar o cluster.

2. Em **Resumo de topologia**, selecione a entrada de **wordcount** na coluna **nome** . Isto irá exibir mais informações sobre a topologia.

    ![Painel de tempestade com informações de topologia de tempestade Starter WordCount.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)

    Esta página fornece as seguintes informações:

    * **Estatísticas de topologia** - informações básicas sobre o desempenho de topologia, organizados em janelas de tempo.

        > [AZURE.NOTE] A seleção de uma janela de tempo específico altera a janela de tempo para informações exibidas em outras seções da página.

    * **Spouts** - informações básicas sobre spouts, incluindo o último erro retornado por cada spout.

    * **Bolts** - informações básicas sobre parafusos.

    * **Configuração de topologia** - informações detalhadas sobre a configuração de topologia.

    Essa página também fornece ações que podem ser executadas na topologia:

    * **Ativar** - processamento de currículos de uma topologia desativada.

    * **Desativar** - pausa uma topologia em execução.

    * **Rebalancear** - ajustará o paralelismo da topologia. Você deve rebalancear topologias em execução depois de alterar o número de nós no cluster. Isso permite a topologia ajustar paralelismo para compensar o número de aumento/redução de nós no cluster. Para obter mais informações, consulte [Noções básicas sobre o paralelismo de uma topologia de tempestade](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

    * **Eliminar** - termina uma topologia de tempestade após o tempo limite especificado.

3. Nesta página, selecione uma entrada da seção **Spouts** ou **Bolts** . Isto irá exibir informações sobre o componente selecionado.

    ![Painel de tempestade com informações sobre componentes selecionados.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)

    Esta página exibe as seguintes informações:

    * **Estatísticas de spout/raio** - informações básicas sobre o desempenho do componente, organizados em janelas de tempo.

        > [AZURE.NOTE] A seleção de uma janela de tempo específico altera a janela de tempo para informações exibidas em outras seções da página.

    * **Estatísticas de entrada** (incluir apenas) - informações sobre componentes que produzem dados consumidos pelo raio.

    * **Estatísticas de saída** - obter informações sobre dados emitidas por este raio.

    * **Executores** - informações sobre ocorrências desse componente.

    * **Erros** - erros produzidos por esse componente.

4. Ao exibir os detalhes de uma spout ou raio, selecione uma entrada da coluna **porta** na seção **executores** para exibir detalhes de uma instância específica do componente.

        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

    A partir desses dados, você pode ver que o word **sete** ocorreu 1493957 vezes. Isso é quantas vezes ela foi encontrada desde que essa topologia foi iniciada.

##<a name="stop-the-topology"></a>Parar a topologia

Retorne para a página de **Resumo de topologia** para a topologia de contagem de palavras e selecione o botão **Finalizar** na seção **ações de topologia** . Quando solicitado, digite 10 para os segundos aguardar antes de interromper a topologia. Após o período de tempo limite, a topologia não aparecerá mais quando visitar a seção de **Tempestade UI** do painel de controle.

##<a name="delete-the-cluster"></a>Excluir o cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a id="next"></a>Próximas etapas

Neste tutorial Apache tempestade, você usava o Starter tempestade para aprender a criar uma tempestade em cluster HDInsight e usar o painel de tempestade para implantar, monitorar e gerenciar topologias tempestade. A seguir, saiba como [topologias baseado em Java desenvolver usando Maven](hdinsight-storm-develop-java-topology.md).

Se você já estiver familiarizado com o desenvolvimento baseado em Java topologias e deseja implantar uma topologia existente em HDInsight, consulte [implantar e gerenciar topologias Apache tempestade em HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md).

Se você for um desenvolvedor .NET, você pode criar c# ou híbrido c# / topologias Java usando o Visual Studio. Para obter mais informações, consulte [desenvolver c# topologias para Apache tempestade em HDInsight usando ferramentas de Hadoop para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Por exemplo, topologias que podem ser usadas com tempestade em HDInsight, consulte os exemplos a seguir:

    * [Topologias de exemplo para tempestade em HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/
