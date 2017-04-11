<properties
    pageTitle="Criar clusters Hadoop, HBase, tempestade ou Spark no Linux em HDInsight usando o portal | Microsoft Azure"
    description="Aprenda a criar clusters Hadoop, HBase, tempestade ou Spark no Linux para HDInsight usando um navegador da web e o portal de visualização Azure."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/05/2016"
    ms.author="nitinme"/>


#<a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Criar clusters baseados em Linux em HDInsight usando o portal do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

O portal do Azure é uma ferramenta de gerenciamento baseado na web para serviços e recursos hospedados na nuvem Microsoft Azure. Neste artigo, você aprenderá como criar clusters baseados em Linux HDInsight usando o portal.

## <a name="prerequisites"></a>Pré-requisitos

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __Um navegador da web moderna__. O portal do Azure usa HTML5 e o Javascript e pode não funcionar corretamente em navegadores da web antigos.

### <a name="access-control-requirements"></a>Requisitos de controle de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="create-clusters"></a>Criar clusters

O portal do Azure expõe a maioria das propriedades cluster. Usando o modelo do Gerenciador de recursos do Azure, você pode ocultar muitos detalhes. Para obter mais informações, consulte [Hadoop baseados em Linux criar clusters de HDInsight usando modelos do Gerenciador de recursos do Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

1. Entrar no [portal do Azure](https://portal.azure.com).

2. Clique em **novo**, clique em **Análise de dados**e clique em **HDInsight**.

    ![Criar um novo cluster no portal do Azure] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.1.png "Criar um novo cluster no portal do Azure")
3. Insira o **Nome do Cluster**: esse nome deve ser exclusivo.
4. Clique em **Selecionar cluster tipo**e selecione:

    - **Tipo de cluster**: se você não souber qual escolher, selecione **Hadoop**. É o tipo de cluster mais popular.

        > [AZURE.IMPORTANT] HDInsight clusters ocorrer em uma variedade de tipos, que correspondem a carga de trabalho ou a tecnologia que o cluster é ajustado para. Não haverá nenhum método com suporte para criar um cluster que combina vários tipos, como tempestade e HBase em um cluster. 

    - **Sistema operacional**: selecione **Linux**.
    - **Versão**: usar a versão padrão se você não sabe o que escolher. Para obter mais informações, consulte [versões de cluster HDInsight](hdinsight-component-versioning.md).
    - **Nível de cluster**: Azurehdinsight fornece as ofertas de nuvem de grande volume em duas categorias: Standard e Premium camadas. Para obter mais informações, consulte [níveis de Cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers).
    
    ![Configuração de camada HDInsight premium](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)

4. Clique em **assinatura** para selecionar a assinatura Azure que será usada para o cluster.

5. Clique em **Grupo de recursos** para selecionar um grupo de recursos existente ou clique em **novo** para criar um novo grupo de recursos

    > [AZURE.NOTE] Essa entrada padrão será um de seus grupos de recursos existentes, se estiverem disponíveis.

6. Clique em **credenciais** e, em seguida, digite uma senha para o usuário admin. Você também deve inserir um **Nome de usuário SSH** e uma **senha** ou **Chave pública**, que será usado para autenticar o usuário SSH. Usar uma chave pública é a abordagem recomendada. Na parte inferior para salvar a configuração de credenciais, clique em **Selecionar** .

    ![Fornecer credenciais de cluster] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.3.png "Fornecer credenciais de cluster")

    Para obter mais informações sobre como usar SSH com HDInsight, consulte um dos seguintes artigos:

    * [Use SSH com baseado em Linux Hadoop em HDInsight Linux, Unix ou dos X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Clique em **Fonte de dados** para escolher uma fonte de dados existente para o cluster ou crie um novo.

    ![Blade de fonte de dados] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.4.png "Configuração de fonte de dados de fornecer")

    Atualmente, você pode selecionar uma conta de armazenamento do Azure como fonte de dados para um cluster de HDInsight. Use este procedimento para entender as entradas na lâmina **Fonte de dados** .

    - **Método de seleção**: defina **de todas as assinaturas** para habilitar a navegação de contas de armazenamento de todas as suas assinaturas. Defina esta **Acessar** a chave se desejar inserir o **Nome de armazenamento** e uma **Tecla de acesso** de uma conta de armazenamento existente.

    - **Selecione conta de armazenamento / nova**: clique em **Selecione a conta de armazenamento** para procurar e selecione uma conta de armazenamento existente que você deseja associar o cluster. Ou, clique em **novo** para criar uma nova conta de armazenamento. Use o campo que aparece para digitar o nome da conta de armazenamento. Uma marca de seleção verde aparecerá se o nome está disponível.

    - **Escolha o contêiner de padrão**: Use este comando para inserir o nome do contêiner padrão a ser usado para o cluster. Enquanto você pode inserir qualquer nome aqui, é recomendável usar o mesmo nome como cluster para que você possa reconhecer facilmente que o contêiner é usado para este cluster específico.

    - **Local**: A região geográfica que a conta de armazenamento no ou será criada em.

        > [AZURE.IMPORTANT] Selecionando o local para a fonte de dados padrão também definirá o local do cluster HDInsight. A fonte de dados de cluster e padrão deve estar localizada na mesma região.
        
    - **Cluster AAD identidade**: Configurando-lo, você tornar cluster acessível para os armazenamentos Lucerne de dados do Azure com base na configuração AAD.

    Clique em **Selecionar** para salvar a configuração de fonte de dados.

8. Clique em **Níveis de preços de nó** para exibir informações sobre os nós que serão criados para este cluster. Defina o número de nós de trabalho que você precisa para o cluster. O custo estimado do cluster será mostrado dentro a lâmina.

    ![Lâmina de níveis de preços de nó] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.5.png "Especificar o número de nós de cluster")
    
    > [AZURE.IMPORTANT] Se você planeja mais de 32 nós de trabalho, na criação de cluster ou dimensionando cluster após a criação, você deve selecionar um tamanho de nó principal com pelo menos 8 cores e 14GB de ram.
    >
    > Para obter mais informações sobre tamanhos de nó e os custos associados, consulte [HDInsight preços](https://azure.microsoft.com/pricing/details/hdinsight/).

    Clique em **Selecionar** para salvar a configuração de preços de nó.

9. Clique em **Configuração opcional** para selecionar a versão de cluster, bem como definir outras configurações opcionais, como ingressar em uma **Rede Virtual**, configurando um **Metastore externo** para armazenar dados de seção e Oozie, usar ações de Script para personalizar um cluster para instalar componentes personalizados ou usar contas de armazenamento adicional com o cluster.

    * **Rede virtual**: selecione uma rede virtual Azure e a sub-rede se quiser colocar o cluster em uma rede virtual.  

        ![Blade de rede virtual] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.6.png "Especificar detalhes de rede virtual")

        Para obter informações sobre como usar HDInsight com uma rede Virtual, incluindo os requisitos de configuração específica da rede Virtual, consulte [recursos de estender HDInsight usando uma rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md).

    * Clique em **Metastores externo** para especificar o banco de dados SQL que você deseja usar para salvar a seção e Oozie metadados associados ao cluster.
    
        > [AZURE.NOTE] Configuração de Metastore não está disponível para os tipos de cluster HBase.

        ![Blade de metastores personalizado] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.7.png "Especificar externos metastores")

        Para **usar um banco de dados existente do SQL para seção** metadados, clique em **Sim**, selecione um banco de dados do SQL e fornecer o nome de usuário/senha do banco de dados. Repita essas etapas se você quiser **usar um banco de dados existente do SQL para Oozie metadados**. Clique em **Selecionar** até voltar para a lâmina de **Configuração opcional** .

        >[AZURE.NOTE] O banco de dados do SQL Azure usado para o metastore deve permitir conectividade com outros serviços do Azure, incluindo Azurehdinsight. No painel de banco de dados SQL Azure, no lado direito, clique no nome do servidor. Este é o servidor no qual a instância de banco de dados do SQL está em execução. Uma vez que você está no modo de exibição do servidor, clique em **Configurar**e para os **Serviços do Azure**, clique em **Sim**e clique em **Salvar**.

        &nbsp;

        > [AZURE.IMPORTANT] Ao criar um metastore, não use um nome de banco de dados que contenha traços ou hifens, pois isso pode causar o processo de criação de cluster falha.

    * **Ações de script** se você quiser usar um script personalizado para personalizar um cluster, como o cluster está sendo criada. Para obter mais informações sobre as ações de script, consulte [Personalizar HDInsight clusters usando a ação de Script](hdinsight-hadoop-customize-cluster-linux.md). Na lâmina ações de Script fornecem os detalhes conforme mostrado na captura de tela.

        ![Blade de ação de script] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.8.png "Ação de script de especificar")

    * Clique em **Contas de armazenamento vinculadas** para especificar contas de armazenamento adicional para associar o cluster. Na lâmina **Chaves de armazenamento do Azure** , clique em **Adicionar uma chave de armazenamento**e selecione uma conta de armazenamento existente ou criar uma nova conta.

        ![Blade de armazenamento adicional] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.9.png "Especificar contas de armazenamento adicional")

        Você também pode adicionar contas de armazenamento adicional após a criação de um cluster.  Consulte [clusters baseados em Personalizar Linux HDInsight usando a ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

        Clique em **Selecionar** até voltar para a lâmina **HDInsight novo cluster** .
        
        Além da conta de armazenamento de Blob, você também pode vincular Lucerne de dados do Azure lojas. A configuração pode ser por configurar AAD de fonte de dados onde você configurou a conta padrão de armazenamento e o contêiner padrão.

10. No **Novo HDInsight Cluster** blade, certifique-se de que **Pin para Startboard** está selecionada e clique em **criar**. Isso criará o cluster e adicionar um bloco para disponibilizá-lo para o Startboard de seu portal Azure. O ícone indicará que o cluster é provisionamento e mudará para exibir o ícone de HDInsight após provisionamento ter sido concluído.

  	| Ao provisionar | Configuração concluída |
  	| ------------------ | --------------------- |
  	| ![Indicador de provisionamento em startboard](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioning.png) | ![Bloco de cluster provisionado](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioned.png) |

    > [AZURE.NOTE] Levará algum tempo para o cluster seja criado, geralmente cerca de 15 minutos. Use o bloco na Startboard ou a entrada de **notificações** no lado esquerdo da página para verificar o processo de provisionamento.

11. Quando o processo de criação for concluída, clique no bloco para o cluster do Startboard para iniciar a lâmina de cluster. A lâmina de cluster fornece informações essenciais sobre o cluster como o nome, o grupo de recursos que ele pertence, o local, o sistema operacional, a URL para o painel de controle de cluster, etc.

    ![Blade de cluster] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.Cluster.Blade.png "Propriedades do cluster")

    Use o seguinte para entender os ícones na parte superior deste blade e na seção **Essentials** :

    * **Configurações** e **Todas as configurações**: exibe a lâmina de **configurações** para o cluster, que permite que você acesse informações detalhadas de configuração para o cluster.

    * **Painel de controle**, **Cluster Dashboard**e **URL**: estas são todas as maneiras de acessar o painel de cluster, que é um portal da Web para executar trabalhos no cluster.

    * **Secure Shell**: informações necessárias para acessar o cluster usando SSH.

    * **Excluir**: exclui o cluster HDInsight.

    * **Início rápido** (![ícone nuvem e thunderbolt = início rápido](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)): exibe as informações que ajudarão você a começar a usar o HDInsight.

    * **Usuários** (![ícone usuários](./media/hdinsight-hadoop-create-linux-cluster-portal/users.png)): permite que você defina permissões para _gerenciamento de portal_ desse cluster para outros usuários na sua assinatura do Azure.

        > [AZURE.IMPORTANT] Este _só_ afeta as permissões para esse cluster no portal do Azure e acesso e não tem efeito sobre quem pode se conectar ao ou enviar trabalhos ao cluster HDInsight.

    * **Marcas** (![ícone marca](./media/hdinsight-hadoop-create-linux-cluster-portal/tags.png)): marcas permite que você defina pares de chave/valor para definir uma taxonomia personalizada dos seus serviços de nuvem. Por exemplo, você pode criar uma chave chamada __project__e, em seguida, use um valor em comum para todos os serviços associados a um projeto específico.

##<a name="customize-clusters"></a>Personalizar clusters

- Consulte [Personalizar HDInsight clusters usando inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Consulte [clusters baseados em Personalizar Linux HDInsight usando a ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

##<a name="delete-the-cluster"></a>Excluir o cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="next-steps"></a>Próximas etapas

Agora que você criou com êxito um cluster de HDInsight, use o seguinte para aprender a trabalhar com seu cluster:

###<a name="hadoop-clusters"></a>Hadoop clusters

* [Use a seção com HDInsight](hdinsight-use-hive.md)
* [Usar porco com HDInsight](hdinsight-use-pig.md)
* [Usar MapReduce com HDInsight](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>HBase clusters

* [Introdução ao HBase em HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicativos Java para HBase em HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>Clusters de tempestade

* [Desenvolva topologias Java para tempestade em HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usar componentes de Python em tempestade em HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com tempestade em HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

###<a name="spark-clusters"></a>Spark clusters

* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster de Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark com BI: executar análise de dados interativos usando Spark em HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com aprendizado de máquina: Spark de uso em HDInsight prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: Uso Spark no HDInsight para criar aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
