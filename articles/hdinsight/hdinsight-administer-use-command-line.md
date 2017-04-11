<properties
    pageTitle="Gerenciar clusters Hadoop usando Azure CLI | Microsoft Azure"
    description="Como usar a CLI do Azure para gerenciar clusters Hadoop em HDIsight"
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    tags="azure-portal"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Gerenciar clusters Hadoop em HDInsight usando a CLI do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Saiba como usar a [Interface de linha de comando do Azure](../xplat-cli-install.md) para gerenciar clusters Hadoop no Azure HDInsight. A CLI do Azure está implementada Node. Ele pode ser usado em qualquer plataforma que ofereça suporte Node, incluindo Windows, Mac e Linux.

Este artigo aborda usando somente o CLI Azure com HDInsight. Para um guia geral sobre como usar o Azure CLI, consulte [instalar e configurar o Azure CLI][azure-command-line-tools].

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Azure CLI** - consulte [instalar e configurar o CLI Azure](../xplat-cli-install.md) para obter informações de instalação e configuração.
- **Conectar-se ao Azure**, usando o seguinte comando:

        azure login

    Para obter mais informações sobre a autenticação usando uma conta corporativa ou escolar, consulte [conectar a uma assinatura do Azure da CLI Azure](xplat-cli-connect.md).
    
- **Alternar para o modo do Gerenciador de recursos do Azure**, usando o seguinte comando:

        azure config mode arm

Para obter ajuda, use a opção **-h** .  Por exemplo:

    azure hdinsight cluster create -h
    
##<a name="create-clusters"></a>Criar clusters

Consulte [clusters baseados em Linux criar no HDInsight utilizando a CLI do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

##<a name="list-and-show-cluster-details"></a>Listar e mostrar detalhes de cluster
Use os seguintes comandos para listar e mostrar detalhes de cluster:

    azure hdinsight cluster list
    azure hdinsight cluster show <Cluster Name>

![HDI. CLIListCluster][image-cli-clusterlisting]


##<a name="delete-clusters"></a>Excluir clusters

Use o seguinte comando para excluir um cluster:

    azure hdinsight cluster delete <Cluster Name>

Você também pode excluir um cluster excluindo grupo de recursos que contém o cluster. Observe que isso excluirá todos os recursos do grupo, incluindo a conta de armazenamento padrão.

    azure group delete <Resource Group Name>

##<a name="scale-clusters"></a>Clusters de escala

Para alterar o tamanho do cluster Hadoop:

    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## <a name="enabledisable-http-access-for-a-cluster"></a>Ativar/desativar o acesso HTTP para um cluster

    azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
    azure hdinsight cluster disable-http-access [options] <Cluster Name>

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Ativar/desativar acesso RDP para um cluster

    azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
    azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


##<a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a executar tarefas administrativas do diferentes HDInsight cluster. Para saber mais, consulte os seguintes artigos:

* [Administrar HDInsight usando o Portal do Azure] [hdinsight-admin-portal]
* [Administrar HDInsight usando o PowerShell do Azure] [hdinsight-admin-powershell]
* [Introdução ao Azure HDInsight] [hdinsight-get-started]
* [Como usar a CLI do Azure] [azure-command-line-tools]


[azure-command-line-tools]: ../xplat-cli-install.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Listar e mostrar clusters"
