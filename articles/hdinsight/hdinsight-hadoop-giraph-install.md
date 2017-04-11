<properties
    pageTitle="Instalar e usar Giraph em clusters de Hadoop em HDInsight | Microsoft Azure"
    description="Saiba como personalizar cluster HDInsight com Giraph e como usar o Giraph."
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
    ms.date="02/05/2016"
    ms.author="nitinme"/>

# <a name="install-and-use-giraph-in-hdinsight"></a>Instalar e usar Giraph no HDInsight


Saiba como personalizar cluster do Windows com base HDInsight com Giraph usando a ação de Script e como usar Giraph para processar gráficos em grande escala. Para obter informações sobre como usar Giraph com um cluster baseado em Linux, consulte [Instalar Giraph em clusters de HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md).
 
Você pode instalar Giraph em qualquer tipo de cluster (Hadoop, tempestade, HBase, Spark) em Azurehdinsight usando a *Ação de Script*. Um exemplo de script instalar Giraph em um cluster de HDInsight está disponível em um blob de armazenamento do Azure somente leitura em [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). O exemplo de script só funciona com versão de cluster HDInsight 3.1. Para obter mais informações sobre versões de cluster HDInsight, consulte [versões de cluster HDInsight](hdinsight-component-versioning.md).

**Artigos relacionados**

- [Instalar o Giraph em clusters de HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [Criar Hadoop clusters no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre como criar clusters de HDInsight.
- [Personalizar cluster HDInsight usando a ação de Script][hdinsight-cluster-customize]: informações gerais sobre como personalizar clusters HDInsight usando a ação de Script.
- [Ação de Script desenvolver scripts para HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-giraph"></a>O que é Giraph?

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> permite que você execute graph processamento usando Hadoop e pode ser usada com Azurehdinsight. Gráficos de modelo de relações entre objetos, como as conexões entre roteadores em uma rede grande como a Internet ou relações entre pessoas em redes sociais (às vezes chamadas de como um gráfico social). Processamento de gráfico permite motivo sobre relações entre objetos em um gráfico, tais como:

- Identificando possíveis amigos com base em suas relações atuais.
- Identificando a rota mais curta entre dois computadores em uma rede.
- Calculando a classificação de página de páginas da Web.


## <a name="install-giraph-using-portal"></a>Instalar o Giraph usando o portal

1. Começar a criar um cluster usando a opção **Criar personalizada** , conforme descrito em [Hadoop criar clusters no HDInsight](hdinsight-provision-clusters.md#portal).
2. Na página **Ações de Script** do assistente, clique em **Adicionar ação de script** para fornecer detalhes sobre a ação de script, conforme mostrado abaixo:

    ![Ação de Script de uso para personalizar um cluster] (./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Ação de Script de uso para personalizar um cluster")

    <table border='1'>
        <tr><th>Propriedade</th><th>Valor</th></tr>
        <tr><td>Nome</td>
            <td>Especifique um nome para a ação de script. Por exemplo, <b>Instale o Giraph</b>.</td></tr>
        <tr><td>Script URI</td>
            <td>Especifique o URI Uniform Resource Identifier () como o script que é invocado para personalizar o cluster. Por exemplo, <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
        <tr><td>Tipo de nó</td>
            <td>Especifica os nós no qual o script de personalização é executado. Você pode escolher <b>todos os nós</b>, <b>apenas nós de cabeça</b>ou <b>apenas nós de trabalho</b>.
        <tr><td>Parâmetros</td>
            <td>Especifique os parâmetros, se necessário pelo script. O script para instalar o Giraph não exige qualquer parâmetros, assim você pode deixar em branco.</td></tr>
    </table>

    Você pode adicionar mais de uma ação de script para instalar vários componentes em cluster. Depois de ter adicionado os scripts, clique na marca de seleção para começar a criar o cluster.

## <a name="use-giraph"></a>Use Giraph

Usamos o exemplo SimpleShortestPathsComputation para demonstrar a implementação <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> básica para encontrar o caminho mais curto entre objetos em um gráfico. Use as etapas a seguir para carregar os dados de exemplo e o jar de amostra, execute um trabalho usando o exemplo SimpleShortestPathsComputation e exibir os resultados.

1. Carregar um arquivo de dados de exemplo para o armazenamento de Blob do Azure. Na sua estação de trabalho local, crie um novo arquivo chamado **tiny_graph.txt**. Ele deve conter as seguintes linhas:

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Carregue o arquivo tiny_graph.txt para o armazenamento principal para o seu cluster HDInsight. Para obter instruções sobre como carregar dados, consulte [carregar dados para trabalhos de Hadoop em HDInsight](hdinsight-upload-data.md).

    Estes dados descreve uma relação entre objetos em um gráfico direcionado, usando o formato [origem\_identificação, origem\_valor, [[destino\_id], [borda\_valor],...]]. Cada linha representa uma relação entre uma **fonte\_id** objeto e um ou mais **destino\_id** objetos. O **borda\_valor** (ou espessura) pode ser considerado como a intensidade ou a distância da conexão entre **source_id** e **destino\_id**.

    Desenhou, e usando o valor (ou espessura) como a distância entre objetos, os dados acima teria esta aparência:

    ![tiny_graph.txt desenhado como círculos com linhas de variável distância entre](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)



4. Execute o exemplo SimpleShortestPathsComputation. Use os seguintes cmdlets do PowerShell do Azure para executar o exemplo usando o arquivo de tiny_graph.txt como entrada. 

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

        $clusterName = "clustername"
        # Giraph examples jar
        $jarFile = "wasbs:///example/jars/giraph-examples.jar"
        # Arguments for this job
        $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
                        "-ca", "mapred.job.tracker=headnodehost:9010",
                        "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
                        "-vip", "wasbs:///example/data/tiny_graph.txt",
                        "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
                        "-op",  "wasbs:///example/output/shortestpaths",
                        "-w", "2"
        # Create the definition
        $jobDefinition = New-AzureHDInsightMapReduceJobDefinition
          -JarFile $jarFile
          -ClassName "org.apache.giraph.GiraphRunner"
          -Arguments $jobArguments

        # Run the job, write output to the Azure PowerShell window
        $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $job
        Write-Host "STDERR"
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

    No exemplo acima, substitua **clustername** o nome do seu cluster HDInsight com Giraph instalado.

5. Exiba os resultados. Quando o trabalho tiver terminado, os resultados serão armazenados nos dois arquivos de saída no __wasbs: / / / exemplo/out/shotestpaths__ pasta. Os arquivos são chamados __parte-m-00001__ e __parte-m-00002__. Execute as seguintes etapas para baixar e exibir a saída:

        $subscriptionName = "<SubscriptionName>"       # Azure subscription name
        $storageAccountName = "<StorageAccountName>"   # Azure Storage account name
        $containerName = "<ContainerName>"             # Blob storage container name

        # Select the current subscription
        Select-AzureSubscription $subscriptionName

        # Create the Storage account context object
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

        # Download the job output to the workstation
        Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
        Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

    Isso irá criar a estrutura do diretório de __saída/exemplo/shortestpaths__ no diretório atual na estação de trabalho e baixar os arquivos de dois saída para esse local.

    Use o cmdlet __gato__ para exibir o conteúdo dos arquivos:

        Cat example/output/shortestpaths/part*

    A saída deve aparecer semelhante ao seguinte:


        0   1.0
        4   5.0
        2   2.0
        1   0.0
        3   1.0

    O SimpleShortestPathComputation exemplo é codificado a iniciar com objeto ID 1 e encontre o caminho mais curto a outros objetos. Para que a saída deve ser lido como `destination_id distance`, onde a distância é o valor (ou espessura) das bordas mover entre objeto ID 1 e a ID de destino.

    Visualizando isso, você pode verificar os resultados por viajando os caminhos mais curto entre 1 de ID e todos os outros objetos. Observe que o caminho mais curto entre 1 de ID e ID 4 é 5. Essa é a distância total entre <span style="color:orange">ID 1 e 3</span>e, em seguida, <span style="color:red">ID 3 e 4</span>.

    ![Desenho de objetos como círculos com caminhos mais curto desenhados entre](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-aure-powershell"></a>Instalar o Giraph usando Aure PowerShell

Consulte [Personalizar HDInsight clusters usando a ação de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  O exemplo demonstra como instalar Spark usando o PowerShell do Azure. Você precisa personalizar o script para usar [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="install-giraph-using-net-sdk"></a>Instalar o Giraph usando o SDK do .NET

Consulte [Personalizar HDInsight clusters usando a ação de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). O exemplo demonstra como instalar Spark usando o SDK do .NET. Você precisa personalizar o script para usar [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).


## <a name="see-also"></a>Consulte também

- [Instalar o Giraph em clusters de HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [Criar Hadoop clusters no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre como criar clusters de HDInsight.
- [Personalizar cluster HDInsight usando a ação de Script][hdinsight-cluster-customize]: informações gerais sobre como personalizar clusters HDInsight usando a ação de Script.
- [Ação de Script desenvolver scripts para HDInsight](hdinsight-hadoop-script-actions.md).
- [Instalar e usar Spark em clusters de HDInsight][hdinsight-install-spark]: amostras de ação de Script sobre como instalar Spark.
- [Instalar o R em clusters de HDInsight][hdinsight-install-r]: amostras de ação de Script sobre como instalar R.
- [Instalar Solr em clusters de HDInsight](hdinsight-hadoop-solr-install.md): amostras de ação de Script sobre como instalar o Solr.



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: ../powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
