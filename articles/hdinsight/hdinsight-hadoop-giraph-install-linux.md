<properties
    pageTitle="Instalar e usar Giraph em HDInsight baseado em Linux (Hadoop) | Microsoft Azure"
    description="Saiba como instalar Giraph em clusters baseados em Linux HDInsight usando ações de Script. Ações de script permitem que você personalize o cluster durante a criação, alterando a configuração de cluster ou instalando serviços e utilitários."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="larryfr"/>

# <a name="install-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Instalar Giraph em clusters de HDInsight Hadoop e usar Giraph para processar gráficos em grande escala

Você pode instalar Giraph em qualquer tipo de cluster no Hadoop em Azurehdinsight usando a **Ação de Script** para personalizar um cluster.

Neste tópico, você aprenderá a instalar o Giraph usando a ação de Script. Depois que você instalou Giraph, você também aprenderá usar Giraph para aplicativos mais comuns, que é processar gráficos em grande escala.

> [AZURE.NOTE] As informações neste artigo são específicas para clusters HDInsight baseados em Linux. Para obter informações sobre como trabalhar com clusters baseados no Windows, consulte [Instalar Giraph em clusters de HDInsight Hadoop (Windows)](hdinsight-hadoop-giraph-install.md)

## <a name="whatis"></a>O que é Giraph?

[Apache Giraph](http://giraph.apache.org/) permite que você execute graph processamento usando Hadoop e pode ser usada com Azurehdinsight. Gráficos de modelo de relações entre objetos, como as conexões entre roteadores em uma rede grande como a Internet ou relações entre pessoas em redes sociais (às vezes chamadas de como um gráfico social). Processamento de gráfico permite motivo sobre relações entre objetos em um gráfico, tais como:

- Identificando possíveis amigos com base em suas relações atuais.
- Identificando a rota mais curta entre dois computadores em uma rede.
- Calculando a classificação de página de páginas da Web.

> [AZURE.WARNING] Componentes fornecidos com o cluster HDInsight são totalmente suportadas e Microsoft Support ajudará para isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados, como Giraph, recebem suporte comercialmente razoável para ajudar a solucionar o problema. Isso pode resultar em resolver o problema ou solicitando envolva canais disponíveis para as tecnologias de fonte aberta onde profunda especialização para que a tecnologia é encontrada. Por exemplo, existem muitos sites de comunidade que podem ser usados, como: [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Também projetos Apache tem sites de projeto no [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/).

##<a name="what-the-script-does"></a>O que significa o script

Esse script executa as seguintes ações:

* Instala Giraph para`/usr/hdp/current/giraph`
* Cópias a `giraph-examples.jar` arquivo para armazenamento de padrão (WASB) para o seu cluster:`/example/jars/giraph-examples.jar`

## <a name="install"></a>Instalar o Giraph usando ações de Script

Um exemplo de script instalar Giraph em um cluster de HDInsight está disponível no seguinte local.

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Esta seção fornece instruções sobre como usar o script de amostra ao criar o cluster usando o Portal do Azure. 

> [AZURE.NOTE] PowerShell Azure, a CLI do Azure, o SDK do .NET HDInsight ou Gerenciador de recursos do Azure modelos também podem ser usados para aplicar ações de script. Você também pode aplicar ações de script já executando clusters. Para obter mais informações, consulte [Personalizar HDInsight clusters com ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Começar a criar um cluster usando as etapas em [HDInsight baseado em Linux criar clusters](hdinsight-hadoop-create-linux-clusters-portal.md), mas não concluir a criação.

2. Na lâmina **Configuração opcional** , selecione **Ações de Script**e forneça as informações abaixo:

    * __Nome__: digite um nome amigável para a ação de script.
    * __URI de SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    * __Cabeça__: marque esta opção
    * __TRABALHADOR__: deixe este desmarcada
    * __ZOOKEEPER__: deixe este desmarcada
    * __Parâmetros__: deixe este campo em branco

3. Na parte inferior das **Ações de Script**, use o botão **Selecionar** para salvar a configuração. Finalmente, use o botão **seleção** na parte inferior da lâmina **Configuração opcional** para salvar as informações de configuração opcional.

4. Continue a criação do cluster conforme descrito em [clusters baseados em Linux criar HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Como usar o Giraph no HDInsight?

Quando o cluster tiver terminado de criação, use as etapas a seguir para executar o exemplo SimpleShortestPathsComputation incluído no Giraph. Isso implementa a implementação <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> básica para encontrar o caminho mais curto entre objetos em um gráfico.

1. Conectar-se ao cluster HDInsight usando SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Para obter mais informações sobre como usar SSH com HDInsight, consulte o seguinte:

    * [Use SSH com baseado em Linux Hadoop em HDInsight Linux, Unix ou dos X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

1. Use o seguinte para criar um novo arquivo chamado __tiny_graph.txt__:

        nano tiny_graph.txt

    Use o seguinte como o conteúdo deste arquivo:

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Estes dados descreve uma relação entre objetos em um gráfico direcionado, usando o formato [origem\_identificação, origem\_valor, [[destino\_id], [borda\_valor],...]]. Cada linha representa uma relação entre uma **fonte\_id** objeto e um ou mais **destino\_id** objetos. O **borda\_valor** (ou espessura) pode ser considerado como a intensidade ou a distância da conexão entre **source_id** e **destino\_id**.

    Desenhou, e usando o valor (ou espessura) como a distância entre objetos, os dados acima teria esta aparência:

    ![tiny_graph.txt desenhado como círculos com linhas de variável distância entre](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

2. Para salvar o arquivo, use __Ctrl + X__, __Y__e finalmente __Enter__ para aceitar o nome do arquivo.

3. Use o seguinte para armazenar os dados para o armazenamento principal para o seu cluster HDInsight:

        hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt

4. Execute o exemplo SimpleShortestPathsComputation usando o seguinte comando.

         yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2

    Os parâmetros usados com este comando são descritos na tabela a seguir.

  	| Parâmetro | O que ela faz |
  	| --------- | ------------ |
  	| `jar /usr/hdp/current/giraph/giraph-examples.jar` | O arquivo jar que contém os exemplos. |
  	| `org.apache.giraph.GiraphRunner` | A classe usada para iniciar os exemplos. |
  	| `org.apache.giraph.examples.SimpleShortestPathsCoputation` | O exemplo que será executado. Nesse caso, ele calculará o caminho mais curto entre 1 de ID e todas as outras identificações no gráfico. |
  	| `-ca mapred.job.tracker=headnodehost:9010` | O headnode para o cluster. |
  	| `-vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFromat` | O formato de entrada para usar para os dados de entrada. |
  	| `-vip /example/data/tiny_graph.txt` | O arquivo de dados de entrada. |
  	| `-vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat` | O formato de saída. Nesse caso, ID e valor como texto sem formatação. |
  	| `-op /example/output/shortestpaths` | O local de saída. |
  	| `-w 2` | O número de funcionários para usar. Neste caso, 2. |

    Para obter mais informações sobre esses e outros parâmetros usados com exemplos de Giraph, consulte o [início rápido do Giraph](http://giraph.apache.org/quick_start.html).

5. Quando o trabalho tiver terminado, os resultados serão armazenados na __wasbs: / / / exemplo/out/shotestpaths__ diretório. Os arquivos criados começará com __parte-m -__ e terminam com um número indicando o primeiro, segundo, arquivo de etc. Use este procedimento para exibir a saída:

        hdfs dfs -text /example/output/shortestpaths/*

    A saída deve aparecer semelhante ao seguinte:

        0   1.0
        4   5.0
        2   2.0
        1   0.0
        3   1.0

    O SimpleShortestPathComputation exemplo é codificado a iniciar com objeto ID 1 e encontre o caminho mais curto a outros objetos. Para que a saída deve ser lido como `destination_id distance`, onde a distância é o valor (ou espessura) das bordas mover entre objeto ID 1 e a ID de destino.

    Visualizando isso, você pode verificar os resultados por viajando os caminhos mais curto entre 1 de ID e todos os outros objetos. Observe que o caminho mais curto entre 1 de ID e ID 4 é 5. Essa é a distância total entre <span style="color:orange">ID 1 e 3</span>e, em seguida, <span style="color:red">ID 3 e 4</span>.

    ![Desenho de objetos como círculos com caminhos mais curto desenhados entre](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)


## <a name="next-steps"></a>Próximas etapas

- [Instalar e usar o matiz em HDInsight clusters](hdinsight-hadoop-hue-linux.md). Matiz é uma web UI que torna mais fácil criar, executar e salvar trabalhos porco e ramificação, bem como procurar o armazenamento padrão para sua HDInsight cluster.

- [Instalar R em clusters de HDInsight](hdinsight-hadoop-r-scripts-linux.md): instruções sobre como usar cluster personalização para instalar e usar R em clusters de HDInsight Hadoop. R é um ambiente de computação estatísticas e o idioma de código-fonte aberto. Ele fornece centenas de funções estatísticas internas e sua própria linguagem de programação que combina aspectos da programação orientada a objeto e funcional. Ele também fornece recursos de gráficos considerável.

- [Instalar Solr em clusters de HDInsight](hdinsight-hadoop-solr-install-linux.md). Use a personalização de cluster para instalar Solr em clusters de HDInsight Hadoop. Solr permite que você execute operações de pesquisa avançada de dados armazenados.
