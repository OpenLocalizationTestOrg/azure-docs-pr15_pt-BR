<properties
   pageTitle="Criar clusters baseados no Windows Hadoop em HDInsight usando CLI do Azure"
    description="Aprenda a criar clusters para Azurehdinsight usando CLI do Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-cli"></a>Criar clusters baseados no Windows Hadoop em HDInsight usando CLI do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Aprenda a criar clusters HDInsight usando CLI do Azure. Para a criação de cluster outros recursos e ferramentas clique em selecionar a guia na parte superior desta página ou consulte [métodos de criação de Cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

##<a name="prerequisites"></a>Pré-requisitos:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Antes de começar as instruções neste artigo, você deve ter o seguinte:

- **Assinatura do azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **CLI azure**.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

### <a name="access-control-requirements"></a>Requisitos de controle de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="connect-to-azure"></a>Conectar ao Azure

Use o seguinte comando para se conectar ao Azure:

    azure login

Para obter mais informações sobre a autenticação usando uma conta corporativa ou escolar, consulte [conectar a uma assinatura do Azure da CLI Azure](../xplat-cli-connect.md).

Use o seguinte comando para alternar para o modo ARM:

    azure config mode arm

Para obter ajuda, use a opção **-h** .  Por exemplo:

    azure hdinsight cluster create -h

##<a name="create-clusters"></a>Criar clusters

Você deve ter um Azure Resource Management (ARM) e uma conta de armazenamento de Blob do Azure antes de criar um cluster de HDInsight. Para criar um cluster de HDInsight, especifique o seguinte:

- **Grupo de recursos do Azure**: A dados Lucerne Analytics conta deve ser criada em um grupo de recursos do Azure. Gerenciador de recursos de Azure permite que você trabalhe com os recursos em seu aplicativo como um grupo. Você pode implantar, atualizar ou excluir todos os recursos para seu aplicativo em uma operação única e coordenado.

    Para listar os grupos de recursos em sua assinatura:

        azure group list

    Para criar um novo grupo de recursos:

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Nome de cluster HDInsight**

- **Local**: um dos Azure data centers que oferece suporte a clusters de HDInsight. Para obter uma lista dos locais com suporte, consulte [HDInsight preços](https://azure.microsoft.com/pricing/details/hdinsight/).

- **Conta de armazenamento padrão**: HDInsight usa um contêiner de armazenamento de Blob do Azure como o sistema de arquivo padrão. Antes de criar um cluster de HDInsight, é necessário uma conta de armazenamento do Azure.

    Para criar uma nova conta de armazenamento do Azure:

        azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS

    > [AZURE.NOTE]A conta de armazenamento deve ser sediada HDInsight no Centro de dados.
    > O tipo de conta de armazenamento não pode ser ZRS, como ZRS não dá suporte a tabela.

    Para obter informações sobre como criar uma conta de armazenamento do Azure usando o Portal do Azure, consulte [criar], gerenciar ou excluir uma conta de armazenamento [azure-criar-storageaccount].

    Se você já tiver uma conta de armazenamento mas não souber o nome da conta e chave da conta, você pode usar os seguintes comandos para recuperar as informações:

        -- Lists Storage accounts
        azure storage account list
        -- Shows a Storage account
        azure storage account show "<Storage Account Name>"
        -- Lists the keys for a Storage account
        azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

    Para obter detalhes sobre como obter as informações usando o Portal do Azure, consulte a seção "Gerenciar sua conta de armazenamento" do [Azure sobre contas de armazenamento](../storage/storage-create-storage-account#manage-your-storage-account).

- **Contêiner de Blob de padrão (opcional)**: O comando **criar azure hdinsight cluster** cria o recipiente se ele não existir. Se você optar por criar o contêiner antecipadamente, você pode usar o seguinte comando:

    Criar contêiner de armazenamento do Azure – o nome da conta "<Storage Account Name>"-chave da conta <Storage Account Key> [ContainerName]

Uma vez que a conta de armazenamento preparada, você está pronto para criar um cluster:


    azure hdinsight cluster create -g <Resource Group Name> -c <HDInsight Cluster Name> -l <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --workerNodeCount 2 --headNodeSize Large --workerNodeSize Large --defaultStorageAccountName <Azure Storage Account Name>.blob.core.windows.net --defaultStorageAccountKey "<Default Storage Account Key>" --defaultStorageContainer <Default Blob Storage Container> --userName admin --password "<HTTP User Password>" --rdpUserName <RDP Username> --rdpPassword "<RDP User Password" --rdpAccessExpiry "03/01/2016"


##<a name="create-clusters-using-configuration-files"></a>Criar clusters usando arquivos de configuração
Normalmente, você cria um cluster de HDInsight, executar trabalhos nele e, em seguida, excluir o cluster para reduzir o custo. A interface de linha de comando oferece a opção para salvar as configurações em um arquivo, para que você pode reutilizá-lo sempre que você criar um cluster.  

    azure hdinsight config create [options ] <Config File Path> <overwirte>
    azure hdinsight config add-config-values [options] <Config File Path>
    azure hdinsight config add-script-action [options] <Config File Path>

Exemplo: Crie um arquivo de configuração que contém uma ação de script para executar ao criar um cluster.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"
    azure hdinsight cluster create --configurationPath "C:\myFiles\configFile.config"

##<a name="create-clusters-with-script-action"></a>Criar clusters com ação de script

Crie um arquivo de configuração que contém uma ação de script para executar ao criar um cluster.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"

Criar um cluster com uma ação de script

    azure hdinsight cluster create -g myarmgroup01 -l westus -y Windows --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01


Para obter informações de ação de script gerais, consulte [Personalizar HDInsight clusters usando a ação de Script (Linux)](hdinsight-hadoop-customize-cluster.md).


## <a name="create-clusters-using-arm-templates"></a>Criar clusters usando modelos ARM

Você pode usar CLI para criar clusters chamando modelos ARM. Consulte [implantar com CLI Azure](hdinsight-hadoop-create-windows-clusters-arm-templates.md#deploy-with-azure-cli).

## <a name="see-also"></a>Consulte também

- [Introdução ao Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - Saiba como começar a trabalhar com seu cluster HDInsight
- [Enviar Hadoop trabalhos programaticamente](hdinsight-submit-hadoop-jobs-programmatically.md) - Aprenda a enviar programaticamente trabalhos ao HDInsight
- [Gerenciar clusters Hadoop em HDInsight usando a CLI do Azure](hdinsight-administer-use-command-line.md)
- [Usando a CLI do Azure para Mac, Linux e Windows com o gerenciamento de serviço Azure](../virtual-machines-command-line-tools.md)
