<properties
    pageTitle="Use a ação de Script instalar Solr em HDInsight baseado em Linux | Microsoft Azure"
    description="Saiba como instalar Solr em clusters baseados em Linux HDInsight Hadoop usando ações de Script."
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
    ms.date="10/03/2016"
    ms.author="larryfr"/>

# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Instalar e usar Solr em clusters de HDInsight Hadoop

Neste tópico, você aprenderá como instalar Solr em Azurehdinsight usando a ação de Script. Solr é uma plataforma de pesquisa avançada e fornece recursos de pesquisa de nível empresarial em dados gerenciados pelo Hadoop. Depois que você instalou Solr em cluster de HDInsight, você também aprenderá a pesquisar dados usando Solr.

> [AZURE.NOTE] As etapas neste documento exigem um cluster de HDInsight baseados em Linux. Para obter informações sobre como usar Solr com um cluster baseado no Windows, consulte [instalação e uso Solr em clusters de HDinsight Hadoop (Windows)](hdinsight-hadoop-solr-install.md)

O script de exemplo usado neste tópico cria um cluster de Solr com uma configuração específica. Se você quiser configurar o cluster Solr com coleções diferentes fragmentos, esquemas, réplicas, etc., é necessário modificar o script e binários Solr adequadamente.

## <a name="whatis"></a>O que é Solr?

[Apache Solr](http://lucene.apache.org/solr/features.html) é uma plataforma de pesquisa corporativo que permite eficientes de pesquisa de texto completo em dados. Enquanto Hadoop permite armazenar e gerenciar grandes quantidades de dados, Apache Solr fornece os recursos de pesquisa para recuperar os dados rapidamente. Este tópico fornece instruções sobre como personalizar um cluster de HDInsight para instalar o Solr.

> [AZURE.WARNING] Componentes fornecidos com o cluster HDInsight são totalmente suportadas e Microsoft Support ajudará para isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados, como Solr, recebem suporte comercialmente razoável para ajudar a solucionar o problema. Isso pode resultar em resolver o problema ou solicitando envolva canais disponíveis para as tecnologias de fonte aberta onde profunda especialização para que a tecnologia é encontrada. Por exemplo, existem muitos sites de comunidade que podem ser usados, como: [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Também projetos Apache tem sites de projeto no [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/).

## <a name="what-the-script-does"></a>O que significa o script

Esse script faz as seguintes alterações ao cluster HDInsight:

* Instala Solr em`/usr/hdp/current/solr`
* Cria um novo usuário, __solrusr__, que é usado para executar o serviço de Solr
* Define __solruser__ como o proprietário do`/usr/hdp/current/solr`
* Adiciona uma configuração de [Upstart](http://upstart.ubuntu.com/) que iniciará Solr se reiniciar de um nó de cluster. Solr é iniciado em nós de cluster também automaticamente após a instalação

## <a name="install"></a>Instalar o Solr usando ações de Script

Um exemplo de script instalar Solr em um cluster de HDInsight está disponível no seguinte local.

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Esta seção fornece instruções sobre como usar o script de exemplo, ao criar um novo cluster usando o portal do Azure. 

> [AZURE.NOTE] PowerShell Azure, a CLI do Azure, o SDK do .NET HDInsight ou Gerenciador de recursos do Azure modelos também podem ser usados para aplicar ações de script. Você também pode aplicar ações de script já executando clusters. Para obter mais informações, consulte [Personalizar HDInsight clusters com ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Iniciar um cluster de provisionamento usando as etapas em [clusters baseados em Linux provisionar HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md), mas não concluída provisionamento.

2. Na lâmina **Configuração opcional** , selecione **Ações de Script**e forneça as informações abaixo:

    * __Nome__: digite um nome amigável para a ação de script.
    * __URI de SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
    * __Cabeça__: marque esta opção
    * __TRABALHADOR__: marque esta opção
    * __ZOOKEEPER__: marque esta opção para instalar no nó Zookeeper
    * __Parâmetros__: deixe este campo em branco

3. Na parte inferior das **Ações de Script**, use o botão **Selecionar** para salvar a configuração. Finalmente, use o botão **seleção** na parte inferior da lâmina **Configuração opcional** para salvar as informações de configuração opcional.

4. Continue cluster de provisionamento, conforme descrito em [clusters baseados em Linux provisionar HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usesolr"></a>Como usar o Solr no HDInsight?

### <a name="indexing-data"></a>Dados de indexação

Você deve começar com indexação Solr com alguns arquivos de dados. Em seguida, você pode usar Solr para executar consultas de pesquisa nos dados indexados. Use as etapas a seguir para adicionar alguns dados de exemplo para Solr e, em seguida, consultá-lo:

1. Conectar-se ao cluster HDInsight usando SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Para obter mais informações sobre como usar SSH com HDInsight, consulte o seguinte:

    * [Use SSH com baseado em Linux Hadoop em HDInsight Linux, Unix ou dos X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    > [AZURE.IMPORTANT] Etapas posteriormente em tornar este documento usam de um túnel SSL para se conectar à web Solr da interface do usuário. Para poder usar essas etapas, você deve estabelecer um túnel SSL e, em seguida, configure o seu navegador para usá-lo.
    >
    > Para obter mais informações, consulte [Usar SSH túnel para acessar Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie e outra web da interface do usuário](hdinsight-linux-ambari-ssh-tunnel.md)

2. Use os seguintes comandos ter dados de exemplo do índice de Solr:

        cd /usr/hdp/current/solr/example/exampledocs
        java -jar post.jar solr.xml monitor.xml

    Você verá a seguinte saída no console:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    O utilitário de post.jar índices Solr com documentos de duas amostras, **solr.xml** e **monitor.xml**. Eles serão armazenados nos __collection1__ em Solr.

3. Use o seguinte para consultar a API REST expostos pela Solr:

        curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"

    Isso emite uma consulta contra __collection1__ para quaisquer documentos correspondência __ \*:\* __ (codificada como \*% 3A\* na sequência de consulta,) e que a resposta deve ser retornada como JSON. A resposta deve aparecer semelhante ao seguinte:

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

### <a name="using-the-solr-dashboard"></a>Usando o painel de Solr

O painel de Solr é um interface do usuário que permite que você trabalhe com Solr por meio de seu navegador da web. No painel de Solr não é exposto diretamente na Internet do seu cluster HDInsight, mas deve ser acessado através de um túnel SSH. Para obter mais informações sobre como usar um túnel SSH, consulte [Usar SSH túnel para acessar Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie e outra web da interface do usuário](hdinsight-linux-ambari-ssh-tunnel.md)

Depois de ter estabelecido um túnel SSH, use as seguintes etapas para usar o painel de Solr:

1. Determine o nome de host para o headnode principal:

    1. Use SSH para se conectar ao cluster na porta 22. Por exemplo, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` onde __NOMEUSUÁRIO__ é seu nome de usuário SSH e __CLUSTERNAME__ é o nome do seu cluster.

        Para obter mais informações sobre como usar o SSH, consulte os seguintes documentos:

        * [Use SSH com baseado em Linux HDInsight de um cliente Linux, Unix ou Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

        * [Use SSH com baseado em Linux HDInsight de um cliente do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
    3. Use o comando a seguir para obter o nome de host totalmente qualificado:

            hostname -f

        Isso retornará um nome semelhante ao seguinte:

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    
        Este é o nome de host que deve ser usado nas etapas a seguir.
    
1. No seu navegador, conectar ao __solr/http://HOSTNAME:8983 / #/__, onde __HOSTNAME__ é o nome que você determinado nas etapas anteriores. 

    A solicitação deve ser roteada pelo túnel SSH para o nó principal para o seu cluster HDInsight. Você deve ver uma página semelhante ao seguinte:

    ![Imagem do painel de Solr](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

2. No painel esquerdo, use o **Seletor de núcleo** suspensa para selecionar **collection1**. Várias entradas elas devem aparecer abaixo __collection1__.

3. Das entradas abaixo __collection1__, selecione a __consulta__. Use os seguintes valores para preencher a página de pesquisa:

    * Na caixa de texto **p** , insira ** \*:**\*. Isso retornará todos os documentos que estão indexados em Solr. Se você quiser procurar uma cadeia de caracteres específica dentro dos documentos, você pode inserir essa cadeia de caracteres aqui.

    * Na caixa de texto **wt** , selecione o formato de saída. Padrão é **json**.

    Finalmente, selecione o botão **Executar consulta** na parte inferior da e de pesquisa.

    ![Use a ação de Script para personalizar um cluster](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

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

### <a name="starting-and-stopping-solr"></a>Iniciar e interromper Solr

Se você precisar interromper manualmente ou iniciar Solar, use os seguintes comandos:

    sudo stop solr

    sudo start solr

## <a name="backup-indexed-data"></a>Backup dados indexados

Como uma boa prática, você deve fazer backup dos dados indexados de nós do cluster Solr no armazenamento de Blob do Azure. Execute as seguintes etapas para fazer isso:

1. Conectar-se ao cluster usando SSH, em seguida, use o seguinte comando para obter o nome de host para o nó principal:

        hostname -f
        
2. Use o seguinte para criar um instantâneo dos dados indexados. Substitua __HOSTNAME__ com o nome retornado do comando anterior:

        curl http://HOSTNAME:8983/solr/replication?command=backup

    Você deve ver uma resposta assim:

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

2. Em seguida, altere diretórios para __/usr/hdp/current/solr/example/solr__. Haverá uma subpasta para cada conjunto. Cada pasta de coleção contém um diretório de __dados__ , que é onde o instantâneo para esse conjunto é localizado.

    Por exemplo, se você usou as etapas anteriores para indexar documentos de exemplo, o diretório __/usr/hdp/current/solr/example/solr/collection1/data__ agora deve conter um diretório chamado __instantâneo. # # #__ onde o # s são a data e hora do instantâneo.

3. Crie um arquivo compactado da pasta de instantâneo usando um comando semelhante ao seguinte:

        tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855

    Isso criará um novo arquivo chamado __snapshot.20150806185338855.tgz__, que contém o conteúdo da pasta __snapshot.20150806185338855__ .

3. Em seguida, você pode armazenar o arquivo morto armazenamento principal do cluster, usando o seguinte comando:

    Hadoop fs - copyFromLocal snapshot.20150806185338855.tgz/exemplo/dados

    > [AZURE.NOTE] Talvez você queira criar um diretório dedicado para armazenar Solr instantâneos. Por exemplo, `hadoop fs -mkdir /solrbackup`.

Para obter mais informações sobre como trabalhar com Solr backup e restaura, consulte [fazer e restaurando backups das SolrCores](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups+of+SolrCores).


## <a name="see-also"></a>Consulte também

- [Instalar e usar o matiz em HDInsight clusters](hdinsight-hadoop-hue-linux.md). Matiz é uma web UI que torna mais fácil criar, executar e salvar trabalhos porco e ramificação, bem como procurar o armazenamento padrão para sua HDInsight cluster.

- [Instalar o R em clusters de HDInsight][hdinsight-install-r]. Use a personalização de cluster para instalar R em clusters de HDInsight Hadoop. R é um ambiente de computação estatísticas e o idioma de código-fonte aberto. Ele fornece centenas de funções estatísticas internas e sua própria linguagem de programação que combina aspectos da programação orientada a objeto e funcional. Ele também fornece recursos de gráficos considerável.

- [Instalar Giraph em clusters de HDInsight](hdinsight-hadoop-giraph-install-linux.md). Use a personalização de cluster para instalar Giraph em clusters de HDInsight Hadoop. Giraph permite que você execute graph processamento usando Hadoop e pode ser usada com Azurehdinsight.

- [Instalar o matiz em clusters de HDInsight](hdinsight-hadoop-hue-linux.md). Use a personalização de cluster para instalar matiz em clusters de HDInsight Hadoop. Matiz é um conjunto de aplicativos da Web usado para interagir com um cluster de Hadoop.



[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
