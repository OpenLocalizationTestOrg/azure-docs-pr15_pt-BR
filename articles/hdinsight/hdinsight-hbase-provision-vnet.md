<properties
    pageTitle="Criar clusters HBase em uma rede Virtual | Microsoft Azure"
    description="Comece a usar HBase no Azure HDInsight. Aprenda a criar HDInsight HBase clusters em rede Virtual do Azure."
    keywords=""
    services="hdinsight,virtual-network"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/18/2016"
   ms.author="jgao"/>

# <a name="create-hbase-clusters-in-azure-virtual-network"></a>Criar HBase clusters na rede Virtual do Azure 

Aprenda a criar clusters Azure HDInsight HBase em uma [Rede Virtual do Azure][1].

Com a integração de rede virtual, HBase clusters podem ser implantados à mesma rede virtual como seus aplicativos para que os aplicativos possam se comunicar com HBase diretamente. Os benefícios incluem:

- Conectividade direta do aplicativo da web para os nós do cluster HBase, que permite a comunicação por meio de procedimento remoto HBase Java chamar APIs (RPC).
- Melhor desempenho por não ter seu tráfego vá sobre vários gateways e balanceadores de carga.
- A capacidade de processar informações confidenciais de maneira mais segura sem expor um ponto de extremidade público.

###<a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Uma estação de trabalho com o PowerShell do Azure**. Consulte [instalação e uso do PowerShell do Azure](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). 

## <a name="create-hbase-cluster-into-virtual-network"></a>Criar um cluster de HBase em uma rede virtual

Nesta seção, você criará um cluster baseado no Linux HBase com a conta de armazenamento do Azure dependente em uma rede virtual Azure usando um [modelo do Gerenciador de recursos do Azure](../resource-group-template-deploy.md). Para outros métodos de criação de cluster e Noções básicas sobre as configurações, consulte [criar HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre como usar um modelo para criar clusters de Hadoop no HDInsight, consulte [criar Hadoop clusters no HDInsight usando modelos do Gerenciador de recursos do Azure](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

> [AZURE.NOTE] Algumas propriedades foram embutidas no modelo. Por exemplo:
>
> * __Local__: Leste dos EUA 2
> * Versão do __Cluster: 3.4
> * __Contagem de nós do cluster trabalhador__: 4
> * __Conta de armazenamento padrão__: &lt;nome do Cluster > armazenar
> * __Nome de rede virtual__: &lt;nome do Cluster >-vnet
> * __Espaço de endereço de rede virtual__: 10.0.0.0/16
> * __Nome de sub-rede__: padrão
> * __Intervalo de endereços de sub-rede__: 10.0.0.0/24
>
> &lt;Nome do cluster > será substituído pelo nome de cluster que você fornecer ao usar o modelo.

1. Clique na imagem a seguir para abrir o modelo no portal do Azure. O modelo está localizado em um contêiner de blob pública. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Da lâmina **implantação personalizada** , insira o seguinte:

    - **Assinatura**: selecione uma assinatura do Azure usada para criar o cluster HDInsight, a conta de armazenamento dependente e a rede virtual Azure.
    - **Grupo de recursos**: selecione **Criar novo**e especifique um novo nome de grupo de recursos.
    - **Local**: selecione um local para o grupo de recursos.
    - **ClusterName**: insira um nome para o cluster Hadoop que será criado.
    - **Nome de login de cluster e de senha**: O nome de login padrão é **admin**.
    - **E SSH senha**: O nome de usuário padrão é **sshuser**.  Você pode renomeá-la. 
    - **Eu concordar com os termos e as condições descritas acima**: (selecione)
    

3. Clique em **comprar**. Ele leva cerca de cerca de 20 minutos para criar um cluster. Depois que o cluster for criado, você pode clicar a lâmina cluster no portal para abri-lo.

Depois de concluir o tutorial, talvez você queira excluir o cluster. Com HDInsight, seus dados são armazenados no armazenamento do Azure, assim você pode excluir com segurança um cluster quando ele não está em uso. Você também cobrado para um cluster de HDInsight, mesmo quando não estiver em uso. Como os encargos para o cluster são muitas vezes mais do que os encargos de armazenamento, faz sentido econômico excluir clusters quando eles não estão em uso. Para obter as instruções de exclusão de um cluster, consulte [clusters de gerenciar Hadoop em HDInsight usando o portal do Azure](hdinsight-administer-use-management-portal.md#delete-clusters).

Para começar a trabalhar com seu novo cluster HBase, você pode usar os procedimentos encontrados em [começar a usar o HBase com Hadoop em HDInsight](hdinsight-hbase-tutorial-get-started.md).

## <a name="connect-to-the-hbase-cluster-using-hbase-java-rpc-apis"></a>Conectar-se ao cluster HBase usando HBase Java RPC APIs

1.  Crie uma infraestrutura como máquina virtual serviço (IaaS) na mesma rede virtual Azure e mesma sub-rede. Para obter instruções sobre como criar uma nova máquina virtual IaaS, consulte [criar uma máquina Virtual executando o Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md). Ao seguir as etapas neste documento, você deve usar o seguinte para a configuração de rede:

    - __Rede virtual__: &lt;nome do Cluster >-vnet
    - __Sub-rede__: padrão

    > [AZURE.IMPORTANT] Substituir &lt;nome do Cluster > com o nome que você usou quando criar o cluster HDInsight nas etapas anteriores.

    Usando esses valores irá configurar a máquina virtual para usar a mesma rede virtual e sub-rede como o cluster HDInsight. Isso permitirá que eles se comunicar diretamente uns com os outros.

2.  Ao usar um aplicativo Java para se conectar ao HBase remotamente, você deve usar o nome de domínio totalmente qualificado (FQDN). Para determinar isso, você deve obter o sufixo DNS específico da conexão do cluster HBase. Para fazer isso, você pode usar um dos seguintes métodos:

    * Use um navegador da Web para fazer uma chamada de Ambari:
    
        Navegue até https://&lt;ClusterName >.azurehdinsight.net/api/v1/clusters/&lt;ClusterName > / hosts?minimal_response = true. Na verdade, um arquivo JSON com os sufixos DNS.

    * Use o site de Ambari:

        1. Navegue até https://&lt;ClusterName >. azurehdinsight.net.
        2. Clique em **Hosts** de menu superior.

    * Ondulação de uso para fazer chamadas REST:

            curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

        Os dados de JavaScript Object Notation (JSON) retornado, localize a entrada de "nome_do_host". Isto irá conter o FQDN para os nós no cluster. Por exemplo:

            ...
            "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
            ...

        A parte do início do nome de domínio com o nome do cluster é o sufixo DNS. Por exemplo, mycluster.b1.cloudapp.net.

    * Usar o PowerShell Azure
    
        Use o seguinte script do PowerShell do Azure para registrar a função **Get-ClusterDetail** , que pode ser usada para retornar o sufixo DNS:

            function Get-ClusterDetail(
                [String]
                [Parameter( Position=0, Mandatory=$true )]
                $ClusterDnsName,
                [String]
                [Parameter( Position=1, Mandatory=$true )]
                $Username,
                [String]
                [Parameter( Position=2, Mandatory=$true )]
                $Password,
                [String]
                [Parameter( Position=3, Mandatory=$true )]
                $PropertyName
                )
            {
            <#
                .SYNOPSIS
                 Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
                .Description
                 This command shows the following 4 properties of an HDInsight cluster:
                 1. ZookeeperQuorum (supports only HBase type cluster)
                    Shows the value of HBase property "hbase.zookeeper.quorum".
                 2. ZookeeperClientPort (supports only HBase type cluster)
                    Shows the value of HBase property "hbase.zookeeper.property.clientPort".
                 3. HBaseRestServers (supports only HBase type cluster)
                    Shows a list of host FQDNs that run the HBase REST server.
                 4. FQDNSuffix (supports all cluster types)
                    Shows the FQDN suffix of hosts in the cluster.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
                 This command shows the value of HBase property "hbase.zookeeper.quorum".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
                 This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
                 This command shows a list of host FQDNs that run the HBase REST server.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
                 This command shows the FQDN suffix of hosts in the cluster.
            #>

                $DnsSuffix = ".azurehdinsight.net"

                $ClusterFQDN = $ClusterDnsName + $DnsSuffix
                $webclient = new-object System.Net.WebClient
                $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

                if($PropertyName -eq "ZookeeperQuorum")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
                }
                if($PropertyName -eq "ZookeeperClientPort")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
                }
                if($PropertyName -eq "HBaseRestServers")
                {
                    $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                    $Response1 = $webclient.DownloadString($Url1)
                    $JsonObject1 = $Response1 | ConvertFrom-Json
                    $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                    $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                    $Response2 = $webclient.DownloadString($Url2)
                    $JsonObject2 = $Response2 | ConvertFrom-Json
                    foreach ($host_component in $JsonObject2.host_components)
                    {
                        $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                        Write-host $ConnectionString
                    }
                }
                if($PropertyName -eq "FQDNSuffix")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                    $pos = $FQDN.IndexOf(".")
                    $Suffix = $FQDN.Substring($pos + 1)
                    Write-host $Suffix
                }
            }

        Depois de executar o script do PowerShell do Azure, use o seguinte comando para retornar o sufixo DNS usando a função de **Get-ClusterDetail** . Especifique seu nome de cluster de HDInsight HBase, o nome de administrador e a senha de administrador quando usar este comando.

            Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

        Isso retornará o sufixo DNS. Por exemplo, **yourclustername.b4.internal.cloudapp.net**.

    * Use RDP
    
        Você também pode usar a área de trabalho remota para se conectar ao cluster HBase (você será conectado para o nó principal) e execute **ipconfig** no prompt de comando para obter o sufixo DNS. Para obter instruções sobre como habilitar o protocolo RDP (Remote Desktop) e conectar-se ao cluster usando RDP, consulte [Gerenciar Hadoop clusters em HDInsight usando o portal de Azure][hdinsight-admin-portal].
        
        ![hdinsight.hbase.DNS.surffix][img-dns-surffix]


<!--
3.  Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Para verificar que a máquina virtual possa se comunicar com o cluster HBase, use o comando `ping headnode0.<dns suffix>` na máquina virtual. Por exemplo, ping headnode0.mycluster.b1.cloudapp.net.

Para usar essas informações em um aplicativo Java, você pode seguir as etapas em [Usar Maven para construir aplicativos de Java que usam HBase com HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md) para criar um aplicativo. Para fazer com que o aplicativo se conectar a um servidor de HBase remoto, modifique o arquivo **hbase-site.xml** neste exemplo para usar o FQDN para Zookeeper. Por exemplo:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [AZURE.NOTE] Para obter mais informações sobre resolução de nomes no Azure redes virtuais, incluindo como usar seu próprio servidor DNS, consulte [Resolução de nomes (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

##<a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como criar um cluster de HBase. Para saber mais, consulte:

- [Introdução ao HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Configurar a duplicação de HBase em HDInsight](hdinsight-hbase-geo-replication.md)
- [Criar clusters Hadoop em HDInsight](hdinsight-provision-clusters.md)
- [Começar a usar HBase com Hadoop em HDInsight](hdinsight-hbase-tutorial-get-started.md)
- [Analisar sentimento Twitter com HBase em HDInsight](hdinsight-hbase-analyze-twitter-sentiment.md)
- [Visão geral de rede virtual][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines/virtual-machines-windows-hero-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: powershell-install-configure.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Detalhes de provisionamento do novo cluster HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Use a ação de Script para personalizar um cluster de HBase"

[azure-preview-portal]: https://portal.azure.com
