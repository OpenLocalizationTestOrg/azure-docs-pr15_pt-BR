<properties
    pageTitle="Use a ação de Script para instalar o Solr em cluster Hadoop | Microsoft Azure"
    description="Saiba como personalizar cluster HDInsight com Solr usando a ação de Script."
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

# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Instalar e usar Solr em clusters de HDInsight Hadoop

Saiba como personalizar cluster do Windows com base HDInsight com Solr usando a ação de Script e como usar Solr para pesquisar dados. Para obter informações sobre como usar Solr com um cluster baseado em Linux, consulte [instalação e uso Solr em clusters de HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md).
 
Você pode instalar Solr em qualquer tipo de cluster (Hadoop, tempestade, HBase, Spark) em Azurehdinsight usando a *Ação de Script*. Um exemplo de script instalar Solr em um cluster de HDInsight está disponível em um blob de armazenamento do Azure somente leitura em [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1). 

O exemplo de script só funciona com versão de cluster HDInsight 3.1. Para obter mais informações sobre versões de cluster HDInsight, consulte [versões de cluster HDInsight](hdinsight-component-versioning.md).

O script de exemplo usado neste tópico cria um cluster baseado no Windows Solr com uma configuração específica. Se você quiser configurar o cluster Solr com coleções diferentes fragmentos, esquemas, réplicas, etc., é necessário modificar o script e binários Solr adequadamente.

**Artigos relacionados**

- [Instalar e usar Solr em clusters de HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
- [Criar Hadoop clusters no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre como criar clusters de HDInsight.
- [Personalizar cluster HDInsight usando a ação de Script][hdinsight-cluster-customize]: informações gerais sobre como personalizar clusters HDInsight usando a ação de Script.
- [Ação de Script desenvolver scripts para HDInsight](hdinsight-hadoop-script-actions.md).


## <a name="what-is-solr"></a>O que é Solr?

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> é uma plataforma de pesquisa corporativo que permite eficientes de pesquisa de texto completo em dados. Enquanto Hadoop permite armazenar e gerenciar grandes quantidades de dados, Apache Solr fornece os recursos de pesquisa para recuperar os dados rapidamente. 

## <a name="install-solr-using-portal"></a>Instalar o Solr usando o portal

1. Começar a criar um cluster usando a opção **Criar personalizada** , conforme descrito em [Hadoop criar clusters no HDInsight](hdinsight-provision-clusters.md#portal).
2. Na página **Ações de Script** do assistente, clique em **Adicionar ação de script** para fornecer detalhes sobre a ação de script, conforme mostrado abaixo:

    ![Ação de Script de uso para personalizar um cluster] (./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Ação de Script de uso para personalizar um cluster")

    <table border='1'>
        <tr><th>Propriedade</th><th>Valor</th></tr>
        <tr><td>Nome</td>
            <td>Especifique um nome para a ação de script. Por exemplo, <b>Instale o Solr</b>.</td></tr>
        <tr><td>Script URI</td>
            <td>Especifique o URI Uniform Resource Identifier () como o script que é invocado para personalizar o cluster. Por exemplo, <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>Tipo de nó</td>
            <td>Especifica os nós no qual o script de personalização é executado. Você pode escolher <b>todos os nós</b>, <b>apenas nós de cabeça</b>ou <b>apenas nós de trabalho</b>.
        <tr><td>Parâmetros</td>
            <td>Especifique os parâmetros, se necessário pelo script. O script para instalar o Solr não exige qualquer parâmetros, assim você pode deixar em branco.</td></tr>
    </table>

    Você pode adicionar mais de uma ação de script para instalar vários componentes em cluster. Depois de ter adicionado os scripts, clique na marca de seleção para começar a criar o cluster.


## <a name="use-solr"></a>Use Solr

Você deve começar com indexação Solr com alguns arquivos de dados. Em seguida, você pode usar Solr para executar consultas de pesquisa nos dados indexados. Execute as seguintes etapas para usar Solr em um cluster de HDInsight:

1. **Usar protocolo RDP (Remote Desktop) para remoto em cluster HDInsight com Solr instalado**. Azure no portal do, habilite a área de trabalho remota para o cluster que você criou com Solr instalado e, em seguida, remote no cluster. Para obter instruções, consulte [conectar ao HDInsight clusters usando o RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

2. **Índice Solr carregando arquivos de dados**. Quando você indexar Solr, você colocar documentos em que talvez você precise pesquisar. Para indexar Solr, use RDP para remoto ao cluster, navegue até a área de trabalho, abra a linha de comando do Hadoop e navegue até **C:\apps\dist\solr-4.7.2\example\exampledocs**. Execute o seguinte comando:

        java -jar post.jar solr.xml monitor.xml

    Você verá a seguinte saída no console:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    O utilitário de post.jar índices Solr com documentos de duas amostras, **solr.xml** e **monitor.xml**. O utilitário de post.jar e os documentos de amostra estão disponíveis com a instalação do Solr.

3. **Use o painel de Solr pesquisar dentro dos documentos indexados**. Na sessão RDP ao cluster HDInsight, abra o Internet Explorer e iniciar o painel de controle de Solr em **solr/http://headnodehost:8983 / #/**. No painel esquerdo, da lista suspensa **Seletor de núcleo** , selecione **collection1**e dentro dele, clique em **consulta**. Como exemplo, para selecionar e retornar todos os documentos em Solr, forneça os valores a seguir:

    * Na caixa de texto **p** , insira ** \*:**\*. Isso retornará todos os documentos que estão indexados em Solr. Se você quiser procurar uma cadeia de caracteres específica dentro dos documentos, você pode inserir essa cadeia de caracteres aqui.
    
    * Na caixa de texto **wt** , selecione o formato de saída. Padrão é **json**. Clique em **Executar consulta**.

    ![Ação de Script de uso para personalizar um cluster] (./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Executar uma consulta no painel de ferramentas de Solr")
    
    A saída retornará os dois documentos que usamos para indexação Solr. A saída semelhante à seguinte:

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }


4. **Recomendado: fazer backup dos dados indexados de Solr ao armazenamento de Blob do Azure associado ao cluster HDInsight**. Como uma boa prática, você deve fazer backup dos dados indexados de nós do cluster Solr no armazenamento de Blob do Azure. Execute as seguintes etapas para fazer isso:

    1. Da sessão RDP, abra o Internet Explorer e, em seguida, aponte para a URL a seguir:

            http://localhost:8983/solr/replication?command=backup

        Você deve ver uma resposta assim:

            <?xml version="1.0" encoding="UTF-8"?>
            <response>
              <lst name="responseHeader">
                <int name="status">0</int>
                <int name="QTime">9</int>
              </lst>
              <str name="status">OK</str>
            </response>

    2. A sessão remota, navegue até {SOLR_HOME}\{conjunto} \data. Para o cluster criado via o script de exemplo, isso deve ser **C:\apps\dist\solr-4.7.2\example\solr\collection1\data**. Neste local, você deve ver uma pasta de instantâneo criada com um nome semelhante a * *instantâneo.* carimbo de hora** *.

    3. ZIP na pasta de instantâneo e carregue-o ao armazenamento de Blob do Azure. Na linha de comando Hadoop, navegue até o local da pasta de instantâneo usando o seguinte comando:

              hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

        Este comando copia o instantâneo para /example/data/sob o recipiente dentro a conta de armazenamento associada ao cluster padrão.

## <a name="install-solr-using-aure-powershell"></a>Instalar o Solr usando Aure PowerShell

Consulte [Personalizar HDInsight clusters usando a ação de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  O exemplo demonstra como instalar Spark usando o PowerShell do Azure. Você precisa personalizar o script para usar [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="install-solr-using-net-sdk"></a>Instalar o Solr usando o SDK do .NET

Consulte [Personalizar HDInsight clusters usando a ação de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). O exemplo demonstra como instalar Spark usando o SDK do .NET. Você precisa personalizar o script para usar [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).



## <a name="see-also"></a>Consulte também

- [Instalar e usar Solr em clusters de HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
- [Criar Hadoop clusters no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre como criar clusters de HDInsight.
- [Personalizar cluster HDInsight usando a ação de Script][hdinsight-cluster-customize]: informações gerais sobre como personalizar clusters HDInsight usando a ação de Script.
- [Ação de Script desenvolver scripts para HDInsight](hdinsight-hadoop-script-actions.md).
- [Instalar e usar Spark em clusters de HDInsight][hdinsight-install-spark]: amostras de ação de Script sobre como instalar Spark.
- [Instalar o R em clusters de HDInsight][hdinsight-install-r]: amostras de ação de Script sobre como instalar R.
- [Instalar Giraph em clusters de HDInsight](hdinsight-hadoop-giraph-install.md): amostras de ação de Script sobre como instalar o Giraph.


[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
