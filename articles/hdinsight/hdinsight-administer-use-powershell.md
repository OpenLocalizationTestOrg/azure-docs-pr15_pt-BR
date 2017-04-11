<properties
    pageTitle="Gerenciar clusters Hadoop em HDInsight com o PowerShell | Microsoft Azure"
    description="Saiba como executar tarefas administrativas para clusters Hadoop no HDInsight usando o PowerShell do Azure."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    tags="azure-portal"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Gerenciar clusters de Hadoop em HDInsight usando o PowerShell do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

PowerShell Azure é um poderoso ambiente de script que você pode usar para controlar e automatizar a implantação e gerenciamento de suas cargas de trabalho no Azure. Neste artigo, você aprenderá a gerenciar clusters Hadoop no Azurehdinsight usando um console local do PowerShell do Azure por meio do uso do Windows PowerShell. Para a lista de cmdlets do PowerShell do HDInsight, consulte [referência de cmdlets do HDInsight][hdinsight-powershell-reference].



**Pré-requisitos**

Antes de começar este artigo, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a name="install-azure-powershell"></a>Instale o PowerShell Azure

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Se você instalou o Azure PowerShell versão 0.9 x, você deve desinstalá-lo antes de instalar uma versão mais recente.

Para verificar a versão do PowerShell instalado:

    Get-Module *azure*
    
Para desinstalar a versão mais antiga, execute programas e recursos no painel de controle. 


##<a name="create-clusters"></a>Criar clusters

Consulte [baseados em Linux criar clusters em HDInsight usando o PowerShell do Azure](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

##<a name="list-clusters"></a>Clusters de lista
Use o seguinte comando para listar todos os clusters na assinatura atual:

    Get-AzureRmHDInsightCluster

##<a name="show-cluster"></a>Mostrar cluster

Use o seguinte comando para mostrar detalhes de um cluster específico na assinatura atual:

    Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>

##<a name="delete-clusters"></a>Excluir clusters

Use o seguinte comando para excluir um cluster:

    Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>

Você também pode excluir um cluster removendo o grupo de recursos que contém o cluster. Observe que isso excluirá todos os recursos do grupo, incluindo a conta de armazenamento padrão.

    Remove-AzureRmResourceGroup -Name <Resource Group Name>
            
##<a name="scale-clusters"></a>Clusters de escala
O cluster dimensionamento recurso permite que você altere o número de nós de trabalho usado por um cluster que é executado em Azurehdinsight sem precisar recriar o cluster.

>[AZURE.NOTE] Somente clusters com HDInsight versão 3.1.3 ou superior são suportadas. Se você não tiver certeza da versão do seu cluster, você pode verificar a página de propriedades.  Consulte [clusters de lista e mostrar](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

O impacto das alterando o número de nós de dados para cada tipo de cluster suportado pelo HDInsight:

- Hadoop

    Perfeita, você pode aumentar o número de nós de trabalho em um cluster de Hadoop que está em execução sem afetar os trabalhos em execução ou pendentes. Novos trabalhos também podem ser enviados enquanto a operação está em andamento. Falhas em uma operação de dimensionamento normalmente são tratadas de forma que o cluster sempre seja deixado em um estado funcional.

    Quando um cluster de Hadoop será dimensionado para baixo, reduzindo o número de nós de dados, alguns dos serviços no cluster são reiniciados. Isso faz com que todos funcionando e trabalhos pendentes falha após a conclusão da operação de escala. No entanto, você pode reenviar os trabalhos quando a operação for concluída.

- HBase

    Perfeita, você pode adicionar ou remover nós ao cluster HBase enquanto ele é executado. Servidores regionais são automaticamente equilibrados em poucos minutos de concluir a operação de escala. No entanto, você também manualmente pode comparar os servidores regionais efetuar login na headnode de cluster e executando os seguintes comandos em uma janela do prompt de comando:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

- Tempestade

    Perfeitamente pode adicionar ou remover nós de dados para o seu cluster tempestade enquanto ele é executado. Mas após a conclusão bem-sucedida da operação de escala, você precisará rebalancear a topologia.

    Redistribuição pode ser feito de duas maneiras:

    * Web de tempestade interface do usuário
    * Ferramenta de interface de linha (comando)

    Consulte a [documentação de tempestade Apache](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.

    Web tempestade UI está disponível no cluster HDInsight:

    ![HDInsight reequilíbrio dos escala de tempestade](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

    Eis um exemplo de como usar o comando CLI para rebalancear a topologia de tempestade:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

Para alterar o tamanho do cluster Hadoop usando o Azure PowerShell, execute o seguinte comando de um computador cliente:

    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
    

##<a name="grantrevoke-access"></a>Acesso de Grant/revoke

HDInsight clusters têm os seguintes serviços de web HTTP (todos esses serviços têm pontos de extremidade RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Por padrão, esses serviços são concedidos acesso. Você pode revogar/conceder acesso. Para revogar:

    Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>

Para conceder:

    $clusterName = "<HDInsight Cluster Name>"

    # Credential option 1
    $hadoopUserName = "admin"
    $hadoopUserPassword = "<Enter the Password>"
    $hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

    # Credential option 2
    #$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"
    
    Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential

>[AZURE.NOTE] Por concedendo/revogar o acesso, você irá redefinir cluster nome de usuário e senha.

Isso também pode ser feito através do Portal. Consulte [Administrar HDInsight usando o portal Azure][hdinsight-admin-portal].

##<a name="update-http-user-credentials"></a>Atualizar credenciais de usuário HTTP

É o mesmo procedimento como [Grant/revoke HTTP acesso](#grant/revoke-access). Se o cluster concedeu o acesso HTTP, você deve primeiro revogá-lo.  E, em seguida, conceda o acesso com novas credenciais de usuário HTTP.


##<a name="find-the-default-storage-account"></a>Localize a conta de armazenamento padrão

O seguinte script do Powershell demonstra como obter o nome de conta de armazenamento do padrão e a chave de conta de armazenamento padrão para um cluster.

    $clusterName = "<HDInsight Cluster Name>"
    
    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup
    $defaultStorageAccountName = ($cluster.DefaultStorageAccount).Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 

##<a name="find-the-resource-group"></a>Localize o grupo de recursos

No modo do Gerenciador de recursos, cada cluster HDInsight pertence a um grupo de recursos Azure.  Para localizar o grupo de recursos:

    $clusterName = "<HDInsight Cluster Name>"
    
    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup


##<a name="submit-jobs"></a>Enviar trabalhos

**Para enviar trabalhos MapReduce**

Consulte [exemplos de executar Hadoop MapReduce em HDInsight baseado no Windows](hdinsight-run-samples.md).

**Para enviar trabalhos de seção** 

Consulte a [seção executar consultas usando o PowerShell](hdinsight-hadoop-use-hive-powershell.md).

**Para enviar trabalhos de porco**

Consulte [trabalhos de executar porco usando o PowerShell](hdinsight-hadoop-use-pig-powershell.md).

**Para enviar trabalhos de Sqoop**

Consulte [usar Sqoop com HDInsight](hdinsight-use-sqoop.md).

**Para enviar trabalhos de Oozie**

Consulte [Usar Oozie com Hadoop a definir e executar um fluxo de trabalho em HDInsight](hdinsight-use-oozie.md).

##<a name="upload-data-to-azure-blob-storage"></a>Carregar dados ao armazenamento de Blob do Azure
Consulte [carregar dados ao HDInsight][hdinsight-upload-data].


## <a name="see-also"></a>Consulte também
* [Documentação de referência de cmdlet do HDInsight][hdinsight-powershell-reference]
* [Administrar HDInsight usando o portal do Azure][hdinsight-admin-portal]
* [Administrar HDInsight usando uma interface de linha][hdinsight-admin-cli]
* [Criar clusters de HDInsight][hdinsight-provision]
* [Carregar dados ao HDInsight][hdinsight-upload-data]
* [Enviar trabalhos de Hadoop programaticamente][hdinsight-submit-jobs]
* [Introdução ao Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: powershell-install-configure.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png
