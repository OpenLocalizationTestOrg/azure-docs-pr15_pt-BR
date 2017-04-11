<properties
    pageTitle="Usar o matiz com Hadoop em clusters HDInsight Linux | Microsoft Azure"
    description="Saiba como instalar e usar o matiz com clusters Hadoop no HDInsight Linux."
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
    ms.date="09/13/2016" 
    ms.author="nitinme"/>

# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Instalar e usar o matiz em clusters de HDInsight Hadoop

Saiba como instalar o matiz em clusters HDInsight Linux e usar túnel para rotear as solicitações para matiz.

## <a name="what-is-hue"></a>O que é o matiz?

Matiz é um conjunto de aplicativos da Web usado para interagir com um cluster de Hadoop. Você pode usar o matiz para procurar o armazenamento associado a um cluster de Hadoop (WASB, no caso de HDInsight clusters), execute trabalhos de seção e scripts porco, etc. Os seguintes componentes estão disponíveis com instalações de matiz em um cluster de HDInsight Hadoop.

* Editor de seção de beeswax
* Porco
* Gerenciador de Metastore
* Oozie
* FileBrowser (que fala ao contêiner de padrão WASB)
* Navegador de trabalho

> [AZURE.WARNING] Componentes fornecidos com o cluster HDInsight são totalmente suportadas e Microsoft Support ajudará para isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudar a solucionar o problema. Isso pode resultar em resolver o problema ou solicitando envolva canais disponíveis para as tecnologias de fonte aberta onde profunda especialização para que a tecnologia é encontrada. Por exemplo, existem muitos sites de comunidade que podem ser usados, como: [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Também projetos Apache tem sites de projeto no [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Instalar o matiz usando ações de Script

A seguinte ação de script pode ser usada para instalar o matiz em um cluster de HDInsight baseados em Linux.
https://hdiconfigactions.blob.Core.Windows.NET/linuxhueconfigactionv02/Install-Hue-uber-v02.sh
    
Esta seção fornece instruções sobre como usar o script ao provisionar o cluster usando o Portal do Azure. 

> [AZURE.NOTE] PowerShell Azure, a CLI do Azure, o SDK do .NET HDInsight ou Gerenciador de recursos do Azure modelos também podem ser usados para aplicar ações de script. Você também pode aplicar ações de script já executando clusters. Para obter mais informações, consulte [Personalizar HDInsight clusters com ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Iniciar um cluster de provisionamento usando as etapas em [clusters provisionar HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), mas não concluída provisionamento.

    > [AZURE.NOTE] Para instalar o matiz em clusters de HDInsight, o tamanho de headnode recomendada é pelo menos A4 (8 cores, 14 GB de memória).

2. Na lâmina **Configuração opcional** , selecione **Ações de Script**e forneça as informações conforme mostrado abaixo:

    ![Fornecer parâmetros de ação de script para matiz] (./media/hdinsight-hadoop-hue-linux/hue_script_action.png "Fornecer parâmetros de ação de script para matiz")

    * __Nome__: digite um nome amigável para a ação de script.
    * __URI de SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
    * __Cabeça__: marque esta opção
    * __TRABALHADOR__: deixe em branco.
    * __ZOOKEEPER__: deixe em branco.
    * __Parâmetros__: deixe em branco.

3. Na parte inferior das **Ações de Script**, use o botão **Selecionar** para salvar a configuração. Finalmente, use o botão **seleção** na parte inferior da lâmina **Configuração opcional** para salvar as informações de configuração opcional.

4. Continue cluster de provisionamento, conforme descrito em [clusters provisionar HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

## <a name="use-hue-with-hdinsight-clusters"></a>Usar o matiz com clusters de HDInsight

SSH tunelamento é a única maneira de acessar matiz no cluster quando estiver em execução. Túnel SSH permita o tráfego ir diretamente para o headnode do cluster onde o matiz está sendo executado. Após o cluster de provisionamento, use as seguintes etapas para usar o matiz em um cluster de HDInsight Linux.

1. Usar as informações em [Usar SSH túnel para acessar Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie e outra web da interface do usuário](hdinsight-linux-ambari-ssh-tunnel.md) para criar um túnel SSH de seu sistema de cliente ao cluster HDInsight e configure seu navegador da Web para usar o túnel SSH como um proxy.

2. Depois que você tiver criado um túnel SSH e configurou seu navegador para tráfego de proxy através dele, você deve encontrar o nome do host do nó principal primário. Você pode fazer isso conectando-se ao cluster usando SSH em porta 22. Por exemplo, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` onde __NOMEUSUÁRIO__ é seu nome de usuário SSH e __CLUSTERNAME__ é o nome do seu cluster.

    Para obter mais informações sobre como usar o SSH, consulte os seguintes documentos:

    * [Use SSH com baseado em Linux HDInsight de um cliente Linux, Unix ou Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Use SSH com baseado em Linux HDInsight de um cliente do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

3. Uma vez conectado, use o comando a seguir para obter o nome de domínio totalmente qualificado do headnode principal:

        hostname -f

    Isso retornará um nome semelhante ao seguinte:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    
    Este é o nome de host do headnode principal onde se encontra o site de matiz.

2. Use o navegador para abrir o portal de matiz em http://HOSTNAME:8888. Substitua o nome do host com o nome que você obteve na etapa anterior.

    > [AZURE.NOTE] Quando fizer logon pela primeira vez, você será solicitado a criar uma conta para efetuar login no portal de matiz. As credenciais especificadas aqui serão limitadas ao portal e não estão relacionadas à administração ou SSH credenciais de usuário que você especificou ao provisionar o cluster.

    ![Faça logon no portal de matiz] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Login.png "Especificar credenciais para o portal de matiz")

### <a name="run-a-hive-query"></a>Executar uma consulta de seção

1. No portal de matiz, clique em **Editores de consulta**e clique em **seção** para abrir o editor de seção.

    ![Use a seção] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.png "Use a seção")

2. Na guia **auxiliar** , em **banco de dados**, você deve ver **hivesampletable**. Este é um exemplo de tabela que é fornecido com todos os clusters Hadoop HDInsight. Insira um exemplo de consulta no painel à direita e ver a saída na guia **resultados** no painel abaixo, conforme mostrado na captura de tela.

    ![Executar seção consulta] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.Query.png "Executar seção consulta")

    Você também pode usar a guia de **gráfico** para ver uma representação visual do resultado.

### <a name="browse-the-cluster-storage"></a>Procure o armazenamento de cluster

1. No portal de matiz, clique em **Navegador de arquivo** no canto superior direito da barra de menus.

2. Por padrão o navegador de arquivos abre no diretório **/user/myuser** . Clique na barra direita antes o diretório de usuário no caminho para ir para a raiz do contêiner de armazenamento do Azure associado ao cluster.

    ![Navegador de arquivos de uso] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.File.Browser.png "Navegador de arquivos de uso")

3. Clique com botão direito em um arquivo ou pasta para ver as operações disponíveis. Use o botão **carregar** no canto direito para carregar arquivos para a pasta atual. Use o botão **novo** para criar novos arquivos ou pastas.

> [AZURE.NOTE] O navegador de arquivos matiz só pode mostrar o conteúdo do contêiner padrão associado ao cluster HDInsight. Qualquer contas/contêineres de armazenamento adicional que você pode ter associado ao cluster não será acessíveis usando o navegador de arquivos. No entanto, os contêineres adicionais associados ao cluster serão sempre acessíveis para os trabalhos de seção. Por exemplo, se você inserir o comando `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` no editor de seção, você pode ver o conteúdo de contêineres adicionais também. Neste comando, **newcontainer** não é o recipiente padrão associado a um cluster.

## <a name="important-considerations"></a>Considerações importantes

1. O script usado para instalar o matiz instala somente na headnode primário do cluster.

2. Durante a instalação, vários serviços de Hadoop (HDFS, fio COLORIDO, MR2, Oozie) são reiniciados para atualizar a configuração. Após o script concluir instalando matiz, ele pode demorar de outros serviços Hadoop de inicialização. Isso pode afetar o desempenho do matiz inicialmente. Depois que todos os serviços de inicialização, matiz será totalmente funcional.

3.  Matiz não entende trabalhos de Tez, que é o padrão atual para a seção. Se você quiser usar MapReduce como o mecanismo de execução de seção, atualize o script para usar o seguinte comando no script:

        set hive.execution.engine=mr;

4.  Com clusters Linux, você pode ter um cenário onde seus serviços estão sendo executadas no headnode principal enquanto o Gerenciador de recursos poderiam estar em execução no secundário. Esse cenário pode resultar em erros (mostrados abaixo) ao usar o matiz para exibir detalhes de trabalhos em execução no cluster. No entanto, você pode exibir os detalhes de trabalho quando o trabalho concluído.

    ![Erro de portal de matiz] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Error.png "Erro de portal de matiz")

    Isso é devido a um problema conhecido. Como alternativa, modifique Ambari para que o Gerenciador de recursos ativos também é executado na headnode principal.

5.  Matiz entende WebHDFS enquanto HDInsight clusters usam o armazenamento do Azure usando `wasbs://`. Portanto, o script personalizado usado com a ação de script instala WebWasb, que é um serviço compatível com o WebHDFS para conversar com WASB. Portanto, embora o portal de matiz diz HDFS em locais (como quando você move o mouse sobre o **Navegador de arquivo**), ele deve ser interpretado como WASB.


## <a name="next-steps"></a>Próximas etapas

- [Instalar Giraph em clusters de HDInsight](hdinsight-hadoop-giraph-install-linux.md). Use a personalização de cluster para instalar Giraph em clusters de HDInsight Hadoop. Giraph permite executar processamento de gráfico usando Hadoop e pode ser usado com o Azure HDInsight.

- [Instalar Solr em clusters de HDInsight](hdinsight-hadoop-solr-install-linux.md). Use a personalização de cluster para instalar Solr em clusters de HDInsight Hadoop. Solr permite executar operações sofisticadas de pesquisa em dados armazenados.

- [Instalar R em clusters de HDInsight](hdinsight-hadoop-r-scripts-linux.md). Use a personalização de cluster para instalar R em clusters de HDInsight Hadoop. R é um ambiente de computação estatísticas e o idioma de código-fonte aberto. Ele fornece centenas de funções estatísticas internas e sua própria linguagem de programação que combina aspectos da programação orientada a objeto e funcional. Ele também fornece recursos de gráficos considerável.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
