<properties
    pageTitle="Uso R em HDInsight personalizar clusters | Microsoft Azure"
    description="Saiba como instalar R usando a ação de Script e usar R em clusters de HDInsight."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Instalar e usar R em clusters de HDInsight Hadoop

Saiba como personalizar o Windows com base em cluster HDInsight com R usando a ação de Script e como usar R em HDInsight clusters. A [camada de premium](https://azure.microsoft.com/pricing/details/hdinsight/) oferta para HDInsight inclui servidor R como parte do seu cluster HDInsight. Isso permite que os scripts de R usar MapReduce e Spark para executar cálculos distribuídos. Para obter mais informações, consulte [começar a usar o servidor de R em HDInsight](hdinsight-hadoop-r-server-get-started.md). Para obter informações sobre como usar R com um cluster baseado em Linux, consulte [instalação e uso R em clusters de HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md).
 
Você pode instalar R em qualquer tipo de cluster (Hadoop, tempestade, HBase, Spark) em Azurehdinsight usando a *Ação de Script*. Um exemplo de script instalar R em um cluster de HDInsight está disponível em um blob de armazenamento do Azure somente leitura em [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1). 

**Artigos relacionados**

- [Instalar e usar R em clusters de HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [Criar Hadoop clusters no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre como criar clusters de HDInsight
- [Personalizar cluster HDInsight usando a ação de Script][hdinsight-cluster-customize]: informações gerais sobre como personalizar clusters HDInsight usando a ação de Script
- [Desenvolver scripts de ação de Script para HDInsight](hdinsight-hadoop-script-actions.md)

## <a name="what-is-r"></a>O que é R?

O <a href="http://www.r-project.org/" target="_blank">Projeto de R para computação de estatísticas</a> é um ambiente para computação estatísticas e linguagem de fonte aberta. R fornece centenas de compilação de funções estatísticas e sua própria linguagem de programação que combina aspectos da programação orientada a objeto e funcional. Ele também fornece recursos de gráficos considerável. R é o ambiente de programação preferencial para mais profissional estatísticos e cientistas em uma ampla variedade de campos.

R é compatível com o Azure Blob Storage (WASB) para que os dados armazenados nesse local podem ser processados usando R em HDInsight.  

## <a name="install-r"></a>Instalar R

Um [exemplo de script](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) instalar R em um cluster de HDInsight está disponível em um blob somente leitura no armazenamento do Azure. Esta seção fornece instruções sobre como usar o script de amostra ao criar o cluster usando o Portal do Azure.

> [AZURE.NOTE] O exemplo de script foi introduzido com a versão de cluster HDInsight 3.1. Para obter mais informações sobre versões de cluster HDInsight, consulte [versões de cluster HDInsight](hdinsight-component-versioning.md).

1. Quando você cria um cluster de HDInsight a partir do Portal, clique em **Configuração opcional**e, em seguida, clique em **Ações de Script**.
2. Na página **Ações de Script** , insira os seguintes valores:

    ![Ação de Script de uso para personalizar um cluster] (./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Ação de Script de uso para personalizar um cluster")

    <table border='1'>
        <tr><th>Propriedade</th><th>Valor</th></tr>
        <tr><td>Nome</td>
            <td>Especifique um nome para a ação de script, por exemplo, <b>R instalar</b>.</td></tr>
        <tr><td>Script URI</td>
            <td>Especifique o URI para o script que é invocado para personalizar o cluster, por exemplo, <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
        <tr><td>Tipo de nó</td>
            <td>Especifica os nós no qual o script de personalização é executado. Você pode escolher <b>Todos os nós</b>, <b>apenas nós de cabeça</b>ou <b>trabalhador nós</b> apenas.
        <tr><td>Parâmetros</td>
            <td>Especifique os parâmetros, se necessário pelo script. No entanto, o script para instalar R não exige qualquer parâmetros, assim você pode deixar em branco.</td></tr>
    </table>

    Você pode adicionar mais de uma ação de script para instalar vários componentes em cluster. Depois de ter adicionado os scripts, clique na marca de seleção para começar a se criar o cluster.

Você também pode usar o script instalar R em HDInsight usando o PowerShell do Azure ou o SDK do .NET HDInsight. Instruções para esses procedimentos são fornecidas neste artigo.

## <a name="run-r-scripts"></a>Executar scripts de R
Esta seção descreve como executar um script de R no cluster Hadoop com HDInsight.

1. **Estabelecer uma conexão de área de trabalho remota ao cluster**: do Portal do, habilitar a área de trabalho remota para o cluster que você criou com R instalado e conecte-se ao cluster. Para obter instruções, consulte [conectar ao HDInsight clusters usando o RDP](hdinsight-administer-use-management-portal.md#rdp).

2. **Abra o console de R**: instalação de R o coloca um link no console de R da área de trabalho do nó principal. Clique para abrir o console de R.

3. **Execute o script R**: R o script pode ser executado diretamente do console R colando-, selecionando-a e pressionando ENTER. Aqui está um script de exemplo simples que gera os números de 1 a 100 e, em seguida, multiplica-las por 2.

        library(rmr2)
        library(rhdfs)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
        from.dfs(calc)

As duas primeiras linhas chamadas as bibliotecas de RHadoop que são instaladas com R. A linha final imprime os resultados no console. A saída deve ter esta aparência:

    [1,]  1 2
    [2,]  2 4
    .
    .
    .
    [98,]  98 196
    [99,]  99 198
    [100,] 100 200


## <a name="install-r-using-aure-powershell"></a>Instalar R usando Aure PowerShell

Consulte [Personalizar HDInsight clusters usando a ação de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  O exemplo demonstra como instalar Spark usando o PowerShell do Azure. Você precisa personalizar o script para usar [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

## <a name="install-r-using-net-sdk"></a>Instalar R usando .NET SDK

Consulte [Personalizar HDInsight clusters usando a ação de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). O exemplo demonstra como instalar Spark usando o SDK do .NET. Você precisa personalizar o script para usar [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11).


## <a name="see-also"></a>Consulte também

- [Instalar e usar R em clusters de HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [Criar Hadoop clusters no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre como criar clusters de HDInsight
- [Personalizar cluster HDInsight usando a ação de Script][hdinsight-cluster-customize]: informações gerais sobre como personalizar clusters HDInsight usando a ação de Script
- [Desenvolver scripts de ação de Script para HDInsight](hdinsight-hadoop-script-actions.md)
- [Instalar e usar Spark em clusters de HDInsight][hdinsight-install-spark]: amostras de ação de Script sobre como instalar Spark
- [Instalar Giraph em clusters de HDInsight](hdinsight-hadoop-giraph-install.md): amostras de ação de Script sobre como instalar Giraph
- [Instalar Solr em clusters de HDInsight](hdinsight-hadoop-solr-install-linux.md): amostras de ação de Script sobre como instalar o Solr.

[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-apache-spark-jupyter-spark-sql.md
