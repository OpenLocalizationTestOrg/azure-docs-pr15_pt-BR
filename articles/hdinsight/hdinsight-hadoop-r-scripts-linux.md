<properties
    pageTitle="Instale R em baseados em Linux HDInsight | Microsoft Azure"
    description="Saiba como instalar e usar R para personalizar clusters Hadoop baseados em Linux."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="larryfr"/>

# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Instalar e usar R em clusters de HDInsight Hadoop

Você pode instalar R em qualquer tipo de cluster no Hadoop em HDInsight usando a personalização de cluster de **Ação de Script** . Isso permite cientistas de dados e analistas usar R para implantar a poderosa estrutura de programação MapReduce/fio COLORIDO processar grandes quantidades de dados em clusters de Hadoop que são implantadas no HDInsight.

> [AZURE.IMPORTANT] A [camada de premium](https://azure.microsoft.com/pricing/details/hdinsight/) oferta para HDInsight inclui servidor R como parte do seu cluster HDInsight. Isso permite que os scripts de R usar MapReduce e Spark para executar cálculos distribuídos. Para obter mais informações, consulte [começar a usar o servidor de R em HDInsight](hdinsight-hadoop-r-server-get-started.md). 


## <a name="what-is-r"></a>O que é R?

O <a href="http://www.r-project.org/" target="_blank">Projeto de R para computação de estatísticas</a> é um ambiente para computação estatísticas e linguagem de fonte aberta. R fornece centenas de compilação de funções estatísticas e sua própria linguagem de programação que combina aspectos da programação orientada a objeto e funcional. Ele também fornece recursos de gráficos considerável. R é o ambiente de programação preferencial para mais profissional estatísticos e cientistas em uma ampla variedade de campos.

R scripts podem ser executados em clusters de Hadoop em HDInsight que foram personalizadas usando a ação de Script quando criado para instalar o ambiente de R. R é compatível com o Azure Blob Storage (WASB) para que os dados armazenados nesse local podem ser processados usando R em HDInsight.

## <a name="what-the-script-does"></a>O que significa o script

A ação de script usada para instalar R em seu cluster HDInsight instala os seguintes pacotes Ubuntu, que fornecem uma instalação de R básica:

* [base de r](http://packages.ubuntu.com/precise/r-base): pacote de Base GNU R
* [r-base-desenvolvimento](http://packages.ubuntu.com/precise/r-base-dev): pacotes Auxilliary GNU R

Os pacotes de RHadoop a seguir também são instalados, que fornecem integração com MapReduce e HDFS:

* [rmr2](https://github.com/RevolutionAnalytics/rmr2): permite que os desenvolvedores de R usar Hadoop MapReduce
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs): permite que os desenvolvedores de R usar Hadoop HDFS (WASB para HDInsight)

Além disso, os seguintes pacotes de R são instalados:

| Pacote R | O que ele oferece |
| --------- | ---------------- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html) | Um nível baixo R para interface Java. |
| [Rcpp](https://cran.r-project.org/web/packages/Rcpp/index.html) | Integração de R e C++. |
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html) | R serializar/desserializar objetos JSON |
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html) | Funções para operações bit a bit em vetores inteiro. |
| [Resumo](https://cran.r-project.org/web/packages/digest/index.html) | Crie resumos de Hash criptografia de objetos de R. |
| [funcional](https://cran.r-project.org/web/packages/functional/index.html) | Santos, redigir e outras funções de ordem superior |
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html) | Flexibilidade reestruturar e agregar dados. |
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html) | Conteúdos adicionais simples e consistentes para operações de cadeia de caracteres comuns. |
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html) | Ferramentas para divisão, aplicando e combinar dados. |
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html) | Ferramentas para mover estatísticas de janela, GIF, na Base 64, ROC AUC, etc. |
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html) | Aproximado a correspondência de cadeia de caracteres e distância funções de cadeia de caracteres. |

## <a name="install-r-using-script-actions"></a>Instalar R usando ações de Script

A ação de script a seguir é usada para instalar o R em um cluster de HDInsight. https://hdiconfigactions.blob.Core.Windows.NET/linuxrconfigactionv01/r-Installer-v01.sh
    
Esta seção fornece instruções sobre como usar o script ao criar um novo cluster usando o portal do Azure. 

> [AZURE.NOTE] PowerShell Azure, a CLI do Azure, o SDK do .NET HDInsight ou Gerenciador de recursos do Azure modelos também podem ser usados para aplicar ações de script. Você também pode aplicar ações de script já executando clusters. Para obter mais informações, consulte [Personalizar HDInsight clusters com ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Iniciar um cluster de provisionamento usando as etapas em [clusters baseados em Linux provisionar HDInsight](hdinsight-hadoop-provision-linux-clusters.md#portal), mas não concluída provisionamento.

2. Na lâmina **Configuração opcional** , selecione **Ações de Script**e forneça as informações abaixo:

    * __Nome__: digite um nome amigável para a ação de script.
    * __URI de SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
    * __Cabeça__: marque esta opção
    * __TRABALHADOR__: marque esta opção
    * __ZOOKEEPER__: marque esta opção para instalar no nó Zookeeper.
    * __Parâmetros__: deixe este campo em branco

3. Na parte inferior das **Ações de Script**, use o botão **Selecionar** para salvar a configuração. Finalmente, use o botão **seleção** na parte inferior da lâmina **Configuração opcional** para salvar as informações de configuração opcional.

4. Continue cluster de provisionamento, conforme descrito em [clusters baseados em Linux provisionar HDInsight](hdinsight-hadoop-provision-linux-clusters.md#portal).

## <a name="run-r-scripts"></a>Executar scripts de R

Após o cluster de provisionamento, use as seguintes etapas para usar R para executar uma operação de MapReduce em cluster.

1. Conectar-se ao cluster HDInsight usando SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Para obter mais informações sobre como usar SSH com HDInsight, consulte o seguinte:

    * [Use SSH com baseado em Linux Hadoop em HDInsight Linux, Unix ou dos X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Do `username@hn0-CLUSTERNAME:~$` solicitar, digite o seguinte comando para iniciar uma sessão de R interativa:

        R

3. Insira o seguinte programa R. Isso gera os números de 1 a 100 e, em seguida, multiplica-las por 2.

        library(rmr2)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))

    A primeira linha chama o rmr2 de biblioteca RHadoop, que é usada para operações de MapReduce.

    A segunda linha gera valores 1-100, e em seguida, armazena-los para o sistema de arquivo Hadoop usando `to.dfs`.

    A terceira linha cria um processo de MapReduce usando a funcionalidade fornecida pelo rmr2 e começará processamento. Você deve ver várias linhas passar rolar conforme o processamento começa.

4. Em seguida, use o seguinte para ver o caminho temporário que a saída MapReduce foi armazenada para:

        print(calc())

    Isso deve ser algo semelhante a `/tmp/file5f615d870ad2`. Para exibir a saída real, use o seguinte:

        print(from.dfs(calc))

    A saída deve ter esta aparência:

        [1,]  1 2
        [2,]  2 4
        .
        .
        .
        [98,]  98 196
        [99,]  99 198
        [100,] 100 200

5. Para sair R, insira o seguinte:

        q()


## <a name="next-steps"></a>Próximas etapas

- [Instalar e usar o matiz em HDInsight clusters](hdinsight-hadoop-hue-linux.md). Matiz é uma web UI que torna mais fácil criar, executar e salvar trabalhos porco e ramificação, bem como procurar o armazenamento padrão para sua HDInsight cluster.

- [Instalar Giraph em clusters de HDInsight](hdinsight-hadoop-giraph-install.md). Use a personalização de cluster para instalar Giraph em clusters de HDInsight Hadoop. Giraph permite executar processamento de gráfico usando Hadoop e pode ser usado com o Azure HDInsight.

- [Instalar Solr em clusters de HDInsight](hdinsight-hadoop-solr-install.md). Use a personalização de cluster para instalar Solr em clusters de HDInsight Hadoop. Solr permite executar operações sofisticadas de pesquisa em dados armazenados.

- [Instalar o matiz em clusters de HDInsight](hdinsight-hadoop-hue-linux.md). Use a personalização de cluster para instalar matiz em clusters de HDInsight Hadoop. Matiz é um conjunto de aplicativos da Web usado para interagir com um cluster de Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
