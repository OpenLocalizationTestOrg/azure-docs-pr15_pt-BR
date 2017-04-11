<properties
pageTitle="Usar Azure dados Lucerne armazenamento com tempestade Apache no Azure HDInsight"
description="Aprenda a gravar dados Azure dados Lucerne repositório de uma topologia de tempestade Apache em HDInsight. Este documento e exemplo associado, demonstram como o componente HdfsBolt pode ser usado para gravar dados Lucerne Store."
services="hdinsight"
documentationCenter="na"
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="09/06/2016"
ms.author="larryfr"/>

#<a name="use-azure-data-lake-store-with-apache-storm-with-hdinsight"></a>Usar Azure dados Lucerne armazenamento com tempestade Apache com HDInsight

Azure Data Lucerne Store é um serviço de armazenamento de nuvem compatível HDFS que fornece alta taxa de transferência, disponibilidade, durabilidade e confiabilidade para seus dados. Neste documento, você aprenderá a usar uma topologia de tempestade baseado em Java para gravar dados para armazenamento de Lucerne de dados do Azure usando o componente [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) , que é fornecido como parte do Apache tempestade.

> [AZURE.IMPORTANT] A topologia de exemplo usada neste documento depende de componentes que são fornecidos com tempestade em clusters de HDInsight e podem exigir modificações para funcionar com o Azure dados Lucerne repositório quando usada com outros clusters tempestade Apache.

##<a name="prerequisites"></a>Pré-requisitos

* [Java JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou superior
* [Maven 3. x](https://maven.apache.org/download.cgi)
* Uma assinatura do Azure
* Uma tempestade na versão de cluster HDInsight 3,2. Para criar uma nova tempestade em cluster HDInsight, use as etapas no documento [usar HDInsight com armazenamento de Lucerne de dados usando o Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) . As etapas neste documento apresentará criando um novo HDInsight cluster e armazenamento de Lucerne de dados do Azure.  

    > [AZURE.IMPORTANT] Quando você cria o cluster HDInsight, você deve selecionar __tempestade__ como o tipo de cluster e __3,2__ como a versão. O sistema operacional pode ser Windows ou Linux.  

###<a name="configure-environment-variables"></a>Configurar variáveis de ambiente

As seguintes variáveis de ambiente podem ser definidas quando você instala o Java e o JDK na sua estação de trabalho de desenvolvimento. No entanto, você deve verificar que eles existem e que contêm os valores corretos para seu sistema.

* __JAVA_HOME__ - devem apontar para a pasta onde o ambiente de tempo de execução Java (JRE) está instalado. Por exemplo, em uma distribuição Unix ou Linux, ele deve ter um valor semelhante a `/usr/lib/jvm/java-7-oracle`. No Windows, ele teria um valor semelhante a `c:\Program Files (x86)\Java\jre1.7`.

* __Caminho__ - deve conter os seguintes caminhos:

    * __JAVA\_HOME__ (ou o caminho equivalente)
    
    * __JAVA\_HOME\bin__ (ou o caminho equivalente)
    
    * O diretório onde Maven está instalado

##<a name="topology-implementation"></a>Implementação de topologia

O exemplo usado neste documento escrito em Java e usa os seguintes componentes:

* __TickSpout__: gera os dados usados por outros componentes da topologia.

* __PartialCount__: contagens de eventos gerados pelo TickSpout.

* __FinalCount__: agregações contagem dados de PartialCount.

* __ADLStoreBolt__: grava dados para armazenamento de Lucerne de dados do Azure usando o componente [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) .

O projeto que contém essa topologia está disponível como um download de [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

###<a name="understanding-adlstorebolt"></a>Noções básicas sobre ADLStoreBolt

O ADLStoreBolt é o nome usado para a instância de HdfsBolt na topologia que grava Lucerne de dados do Azure. Isso não é uma versão especial do HdfsBolt criado pela Microsoft; No entanto-lo dependem de valores de configuração de sites principais, além de componentes de Hadoop que são fornecidos com Azurehdinsight para a comunicação com dados Lucerne.

Especificamente, quando você cria um cluster de HDInsight, você pode associá-lo com um armazenamento de Lucerne de dados do Azure. Isso grava entradas em sites principais para o repositório de Lucerne de dados que você selecionou, que são usadas pelos componentes como cliente de hadoop e hadoop hdfs para permitir a comunicação com armazenamento de Lucerne de dados.

> [AZURE.NOTE] Microsoft contribuiu código ao Apache Hadoop e projetos de tempestade que permite a comunicação com armazenamento de Lucerne de dados do Azure e Azure Blob storage, mas essa funcionalidade não pode ser incluída por padrão em outras distribuições Hadoop e tempestade.

A configuração para HdfsBolt na topologia é da seguinte maneira:

    // 1. Create sync and rotation policies to control when data is synched
    //    (written) to the file system and when to roll over into a new file.
    SyncPolicy syncPolicy = new CountSyncPolicy(1000);
    FileRotationPolicy rotationPolicy = new FileSizeRotationPolicy(0.5f, Units.KB);
    // 2. Set the format. In this case, comma delimited
    RecordFormat recordFormat = new DelimitedRecordFormat().withFieldDelimiter(",");
    // 3. Set the directory name. In this case, '/stormdata/'
    FileNameFormat fileNameFormat = new DefaultFileNameFormat().withPath("/stormdata/");
    // 4. Create the bolt using the previously created settings,
    //    and also tell it the base URL to your Data Lake Store.
    // NOTE! Replace 'MYDATALAKE' below with the name of your data lake store.
    HdfsBolt adlsBolt = new HdfsBolt()
        .withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")
        .withRecordFormat(recordFormat)
        .withFileNameFormat(fileNameFormat)
        .withRotationPolicy(rotationPolicy)
        .withSyncPolicy(syncPolicy);
    // 4. Give it a name and wire it up to the bolt it accepts data
    //    from. NOTE: The name used here is also used as part of the
    //    file name for the files written to Data Lake Store.
    builder.setBolt("ADLStoreBolt", adlsBolt, 1)
      .globalGrouping("finalcount");
      
Se você estiver familiarizado com o uso de HdfsBolt, você observará que se trata de todas as configurações padrão, exceto para a URL. A URL fornece o caminho na raiz do seu armazenamento de Lucerne de dados do Azure.

Como escrever dados Lucerne repositório usa HdfsBolt e é apenas uma alteração de URL, você deve ser capaz de fazer qualquer topologia existente que grava HDFS ou WASB usando HdfsBolt e facilmente alterá-lo para usar o armazenamento de Lucerne de dados do Azure.

##<a name="build-and-package-the-topology"></a>Criar e empacotar a topologia

1. Baixe o projeto de exemplo do [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store
) para seu ambiente de desenvolvimento.

2. Abrir o `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` em um editor de arquivo e localize a linha que contém `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")`. Alterar __MYDATALAKE__ para o nome do Azure Lucerne de repositório de dados usado na criação de seu servidor de HDInsight.

3. Um comando de prompt, terminal, ou sessão do shell, altere diretórios na raiz do projeto baixado e execute os seguintes comandos para criar e a topologia de pacote.

        mvn compile
        mvn package
    
    Uma vez concluída a criação e a embalagem, haverá um novo diretório chamado `target`, que contém um arquivo denominado `StormToDataLakeStore-1.0-SNAPSHOT.jar`. Esta página contém a topologia compilada.

##<a name="deploy-and-run-on-linux-based-hdinsight"></a>Implantar e executar em HDInsight baseado em Linux

Se você criou uma tempestade baseados em Linux em cluster HDInsight, use as etapas abaixo para implantar e executar a topologia.

1. Use o seguinte comando para copiar a topologia ao cluster HDInsight. Substitua o __usuário__ com o nome de usuário SSH que você usou quando criar o cluster. Substitua o nome do cluster __CLUSTERNAME__ .

        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
    
    Quando solicitado, digite a senha usada para criar o usuário SSH para o cluster. Se você usou uma chave pública em vez de uma senha, você pode precisar usar o `-i` parâmetro para especificar o caminho para a chave privada correspondente.
    
    > [AZURE.NOTE] Se você estiver usando um cliente Windows para desenvolvimento, talvez você não tenha um `scp` comando. Se assim, você pode usar `pscp`, que está disponível em [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

2. Quando o carregamento for concluída, use o seguinte para conectar-se ao cluster HDInsight usando SSH. Substitua o __usuário__ com o nome de usuário SSH que você usou quando criar o cluster. Substitua o nome do cluster __CLUSTERNAME__ .

        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net

    Quando solicitado, digite a senha usada para criar o usuário SSH para o cluster. Se você usou uma chave pública em vez de uma senha, você pode precisar usar o `-i` parâmetro para especificar o caminho para a chave privada correspondente.
    
    > [AZURE.NOTE] Se você estiver usando um cliente Windows para desenvolvimento, siga as informações em [conectar ao HDInsight baseados em Linux com SSH do Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obter informações em usando o cliente de acabamento para se conectar ao cluster.
    
3. Uma vez conectado, use o seguinte para iniciar a topologia:

        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar com.microsoft.example.StormToDataLakeStore datalakewriter
    
    Isso iniciará a topologia com um nome amigável de `datalakewriter`.

##<a name="deploy-and-run-on-windows-based-hdinsight"></a>Implantar e executar em HDInsight baseado no Windows

1. Abra um navegador da web e vá para HTTPS://CLUSTERNAME.azurehdinsight.net, onde __CLUSTERNAME__ é o nome do seu cluster HDInsight. Quando solicitado, forneça o nome de usuário de administrador (`admin`) e a senha que você usou para essa conta quando o cluster foi criado.

2. No painel de tempestade, selecione __Procurar__ na lista suspensa do __Arquivo Jar__ , em seguida, selecione o arquivo StormToDataLakeStore-1.0-SNAPSHOT.jar o `target` diretório. Use os seguintes valores para as outras entradas no formulário:

    * Nome da classe: com.microsoft.example.StormToDataLakeStore
    * Parâmetros adicionais: datalakewriter
    
    ![imagem do painel de tempestade](./media/hdinsight-storm-write-data-lake-store/submit.png)

3. Selecione o botão __Enviar__ para carregar e iniciar a topologia. O campo de resultado abaixo do botão __Enviar__ deve exibir informações semelhantes às seguintes depois que a topologia de começar:

        Process exit code: 0
        Currently running topologies:
        Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
        -------------------------------------------------------------------
        datalakewriter       ACTIVE     68         8            10        

##<a name="view-output-data"></a>Exibir dados de saída

Há várias maneiras de exibir os dados. Nesta seção, podemos usar o Portal do Azure e o `hdfs` comando para exibir os dados.

> [AZURE.NOTE] Você deve permitir que as topologias executar por vários minutos antes de verificar se os dados de saída, para que os dados tenham sido sincronizados em vários arquivos no repositório de Lucerne de dados do Azure.

* __A partir do [Portal Azure](https://portal.azure.com)__: no portal do, selecione a loja de Lucerne de dados do Azure que você usou com HDInsight.

    > [AZURE.NOTE] Se você não fixar o armazenamento de Lucerne dados até o painel de portal Azure, você pode encontrá-lo selecionando __Procurar__ na parte inferior da lista à esquerda, então __Dados Lucerne Store__, e finalmente selecionando o repositório.
    
    Dos ícones na parte superior do armazenamento de Lucerne de dados, selecione __Gerenciador de dados__.
    
    ![ícone de exploração de dados](./media/hdinsight-storm-write-data-lake-store/dataexplorer.png)
    
    Em seguida, selecione a pasta de __stormdata__ . Uma lista de arquivos de texto deve ser exibida.
    
    ![arquivos de texto](./media/hdinsight-storm-write-data-lake-store/stormoutput.png)
    
    Selecione um dos arquivos para exibir seu conteúdo.

* __Do cluster__: se você tiver conectado ao cluster HDInsight usando SSH (Linux cluster) ou área de trabalho remota (cluster do Windows), você pode usar este procedimento para exibir os dados. Substituir __DATALAKE__ com o nome do seu armazenamento de Lucerne de dados

        hdfs dfs -cat adl://DATALAKE.azuredatalakestore.net/stormdata/*.txt

    Isto irá concatenar os arquivos de texto armazenados no diretório e exibir informações similar ao seguinte:
    
        406000000
        407000000
        408000000
        409000000
        410000000
        411000000
        412000000
        413000000
        414000000
        415000000
        
##<a name="stop-the-topology"></a>Parar a topologia

Topologias de tempestade serão executadas até parar ou cluster é excluído. Para interromper as topologias, use as seguintes informações.

__Para HDInsight baseados em Linux__:

Em uma sessão SSH ao cluster, use o seguinte comando:

    storm kill datalakewriter

__Para HDInsight baseados no Windows__:

1. No painel de tempestade (https://CLUSTERNAME.azurehdinsight.net), selecione o link de __Interface de usuário de tempestade__ na parte superior da página.

2. Depois que a interface do usuário tempestade carrega, selecione o link de __datalakewriter__ .

    ![link para datalakewriter](./media/hdinsight-storm-write-data-lake-store/selecttopology.png)

3. Na seção __Topologia ações__ , selecione __eliminar__ e selecione Okey na caixa de diálogo que aparece.

    ![ações de topologia](./media/hdinsight-storm-write-data-lake-store/topologyactions.png)

## <a name="delete-your-cluster"></a>Excluir seu cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como usar tempestade para gravar Azure dados Lucerne armazenamento, descubra outros [exemplos de tempestade para HDInsight](hdinsight-storm-example-topology.md).
