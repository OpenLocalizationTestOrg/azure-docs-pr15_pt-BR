<properties
    pageTitle="Criar clusters Hadoop, HBase ou tempestade no Linux em HDInsight usando a CLI do Azure entre plataformas | Microsoft Azure"
    description="Aprenda a criar clusters baseados em Linux HDInsight usando a CLI do Azure entre plataformas, modelos do Gerenciador de recursos do Azure e a API REST Azure. Você pode especificar o tipo de cluster (Hadoop, HBase ou tempestade) ou use scripts para instalar componentes personalizados.."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/20/2016"
    ms.author="larryfr"/>

#<a name="create-linux-based-clusters-in-hdinsight-using-the-azure-cli"></a>Criar clusters baseados em Linux no HDInsight utilizando a CLI do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

O Azure é um utilitário de linha de comando entre plataformas que permite que você gerencie os serviços do Azure. Ele pode ser usado, juntamente com modelos de gerenciamento de recursos do Azure, para criar um cluster de HDInsight, junto com contas de armazenamento associado e outros serviços.

Os modelos de gerenciamento de recursos Azure estão documentos JSON que descrevem um __grupo de recursos__ e todos os recursos nele (como HDInsight). Essa abordagem baseada no modelo permite que você defina todos os recursos que você precisa para HDInsight de um modelo. Ele também permite gerenciar as alterações no grupo como um todo por meio de __implantações__, qual aplicar alterações a todo o grupo.

As etapas neste documento percorrer o processo de criação de um novo cluster HDInsight usando a CLI do Azure e um modelo.

> [AZURE.IMPORTANT] As etapas neste documento usam o número padrão de nós de trabalhador (4) para um cluster de HDInsight. Se você planeja em mais de 32 nós de trabalhador (durante a criação de cluster ou dimensionando cluster), você deve selecionar um tamanho de nó principal com pelo menos 8 cores e 14 GB de ram.
>
> Para obter mais informações sobre tamanhos de nó e os custos associados, consulte [HDInsight preços](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="prerequisites"></a>Pré-requisitos

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __CLI azure__. As etapas neste documento última foram testadas com o Azure CLI versão 0.10.1.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 


### <a name="access-control-requirements"></a>Requisitos de controle de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="log-in-to-your-azure-subscription"></a>Faça logon em sua assinatura do Azure

Siga as etapas documentadas em [conectar a uma assinatura do Azure a Interface do Azure de linha (Azure comando)](../xplat-cli-connect.md) e se conectar à sua assinatura usando o método de __logon__ .

##<a name="create-a-cluster"></a>Criar um cluster

As seguintes etapas devem ser executadas de um prompt de comando, shell ou sessão de terminal após instalar e configurar o CLI do Azure.

1. Use o seguinte comando para autenticar à sua assinatura do Azure:

        azure login

    Você será solicitado a fornecer o seu nome e senha. Se você tiver várias assinaturas Azure, use `azure account set <subscriptionname>` para definir a assinatura que usam os comandos do Azure.

3. Alternar para modo de Gerenciador de recursos do Azure usando o seguinte comando:

        azure config mode arm

4. Crie um grupo de recursos. Este grupo de recursos conterá o cluster HDInsight e associadas a conta de armazenamento.

        azure group create groupname location
        
    * Substitua o __nome do grupo__ com um nome exclusivo para o grupo. 
    * Substitua __local__ com a região geográfica que você deseja criá-lo. 
    
        Para uma lista de locais válidos, use o `azure location list` de comando e use um dos locais da coluna __nome__ .

5. Crie uma conta de armazenamento. Esta conta de armazenamento será usada como o armazenamento padrão para o cluster HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
        
     * Substitua o __nome do grupo__ com o nome do grupo criado na etapa anterior.
     * Substitua __local__ com o mesmo local usado na etapa anterior. 
     * Substitua __nome_do_armazenamento__ com um nome exclusivo para a conta de armazenamento.
     
     > [AZURE.NOTE] Para obter mais informações sobre os parâmetros usados neste comando, use `azure storage account create -h` para exibir a Ajuda para este comando.

5. Recupere a chave usada para acessar a conta de armazenamento.

        azure storage account keys list -g groupname storagename
        
    * Substitua o __nome do grupo__ com o nome do grupo de recursos.
    * Substitua __nome_do_armazenamento__ com o nome da conta de armazenamento.
    
    Os dados retornados, salve o valor de __chave__ para __CHAVE1__.

6. Crie um cluster de HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Substitua o __nome do grupo__ com o nome do grupo de recursos.

    * Substitua __Hadoop__ com o tipo de cluster que você deseja criar. Por exemplo, `Hadoop`, `HBase`, `Storm` ou `Spark`.

        > [AZURE.IMPORTANT] HDInsight clusters ocorrer em uma variedade de tipos, que correspondem a carga de trabalho ou a tecnologia que o cluster é ajustado para. Não haverá nenhum método com suporte para criar um cluster que combina vários tipos, como tempestade e HBase em um cluster. 

    * Substitua __local__ com o mesmo local usado nas etapas anteriores.

    * Substitua o nome da conta de armazenamento __nome_do_armazenamento__ .

    * Substitua a __chave de armazenamento__ com a chave obtida na etapa anterior. 

    * Para o `--defaultStorageContainer` parâmetro, use o mesmo nome que você está usando para o cluster.

    * Substitua o __administrador__ e __httppassword__ com o nome e a senha que você deseja usar quando estiver acessando o cluster através de HTTPS.

    * Substituir __sshuser__ e __sshuserpassword__ com o nome de usuário e senha que você deseja usar quando estiver acessando o cluster usando SSH

    Ele pode levar alguns minutos para que o processo de criação de cluster concluir. Geralmente cerca de 15.

##<a name="next-steps"></a>Próximas etapas

Agora que você criou com êxito um cluster de HDInsight utilizando a CLI do Azure, use o seguinte para aprender a trabalhar com seu cluster:

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
