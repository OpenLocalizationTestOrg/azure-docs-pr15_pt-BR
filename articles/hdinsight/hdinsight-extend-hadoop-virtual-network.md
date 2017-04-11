<properties
    pageTitle="Estender HDInsight com uma rede Virtual | Microsoft Azure"  
    description="Saiba como usar uma rede Virtual Azure conectem HDInsight a outros recursos de nuvem ou recursos no seu data center"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="larryfr"/>


#<a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Estender os recursos de HDInsight usando rede Virtual do Azure

Rede Virtual Azure permite que você estenda suas soluções Hadoop para incorporar recursos locais como o SQL Server, combinar vários tipos de cluster HDInsight ou criar redes privadas seguras entre recursos na nuvem.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-and-cli.md)]


##<a id="whatis"></a>O que é uma rede Virtual Azure?

[Rede Virtual do Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permite que você crie uma rede persistente segura que contém os recursos que necessários para a sua solução. Uma rede virtual permite que você:

* Conecte recursos de nuvem juntos em uma rede privada (somente na nuvem).

    ![diagrama de configuração somente na nuvem](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

    Usando rede Virtual para vincular os serviços do Azure com Azurehdinsight habilita as seguintes situações:

    * **Serviços de HDInsight invocar ou trabalhos** de sites Azure ou serviços em execução no Azure máquinas virtuais.

    * **Diretamente transferir dados** entre HDInsight e banco de dados do SQL Azure, SQL Server ou outra solução de armazenamento de dados em execução em uma máquina virtual.

    * **Combinando vários servidores de HDInsight** em uma única solução. HDInsight clusters ocorrer em uma variedade de tipos, que correspondem a carga de trabalho ou a tecnologia que o cluster é ajustado para. Não haverá nenhum método com suporte para criar um cluster que combina vários tipos, como tempestade e HBase em um cluster. Usando uma rede virtual permite que vários clusters para se comunicar diretamente uns com os outros.

* Conecte os recursos de nuvem para sua rede do data center local (-to-site ou ponto-a-site) usando uma rede virtual privada (VPN).

    Configuração de-to-site permite que você se conectar a vários recursos de data center à rede virtual Azure usando um hardware VPN ou o serviço de roteamento e acesso remoto.

    ![diagrama de configuração de-to-site](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)

    Configuração de ponto-a-site permite que você se conectar a um recurso específico à rede virtual Azure usando o software VPN.

    ![diagrama de configuração de ponto-a-site](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)

    Usando uma rede Virtual para vincular a nuvem e data center permite cenários semelhantes à configuração somente na nuvem. Mas, em vez de estar limitado a trabalhar com recursos na nuvem, você também pode trabalhar com recursos no seu data center.

    * **Diretamente transferir dados** entre HDInsight e data center. Um exemplo é usar Sqoop para transferir dados de / em SQL Server ou ler dados gerados por um aplicativo de linha de negócios (LOB).

    * **Serviços de HDInsight invocar ou trabalhos** de um aplicativo de LOB. Um exemplo é usar HBase Java APIs para armazenar e recuperar dados de um cluster de HDInsight HBase.

Para obter mais informações sobre recursos, benefícios e recursos de rede Virtual, consulte [Visão geral de rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE] Você deve criar a rede Virtual do Azure antes de provisionar um cluster de HDInsight. Para obter mais informações, consulte [tarefas de configuração de rede Virtual](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="virtual-network-requirements"></a>Requisitos de rede virtuais

> [AZURE.IMPORTANT] A criação de um cluster de HDInsight em uma rede Virtual requer configurações específicas de rede Virtual, que são descritas nesta seção.

###<a name="location-based-virtual-networks"></a>Redes de Virtual baseadas no local

Azure HDInsight suporta somente redes virtuais baseada no local e não funciona com redes virtuais com base em grupo de afinidade.

###<a name="classic-or-v2-virtual-network"></a>Rede Virtual clássico ou v2

Clusters baseados no Windows requerem uma rede Virtual clássico, enquanto clusters baseados em Linux requerem uma rede Virtual do Azure Gerenciador de recursos. Se você não tiver o tipo correto de rede, não será útil quando você cria o cluster.

Se você tiver recursos em uma rede Virtual que não pode ser usada pelo cluster que você planeja criar, você pode criar uma nova rede Virtual que pode ser usada pelo cluster e conecte-o à rede Virtual incompatível. Você pode criar cluster na versão de rede que requer e ele poderá acessar recursos em outra rede desde que os dois estão associados. Para obter mais informações sobre como conectar redes virtuais clássico e novo, consulte [Conectando-se VNets clássico para VNets novo](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

###<a name="custom-dns"></a>Custom DNS

Ao criar uma rede virtual, o Azure fornece resolução de nomes padrão para serviços Azure como HDInsight que são instalados na rede. No entanto, você talvez precise usar seu próprio sistema de nome de domínio (DNS) para situações como entre resolução de nomes de domínio de rede. Por exemplo, quando a comunicação entre serviços localizado em dois ingressou redes virtuais. HDInsight suporta tanto a resolução de nome Azure padrão como DNS personalizada quando usada com a rede Virtual do Azure.

Para obter mais informações sobre como usar seu próprio servidor DNS com rede Virtual do Azure, consulte a seção de __resolução de nomes usando seu próprio servidor DNS__ do documento [Resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) .

###<a name="secured-virtual-networks"></a>Redes virtuais protegidas

O serviço de HDInsight é um serviço gerenciado e requer acesso à Internet durante a configuração e durante a execução. Isso é que Azure pode monitorar a saúde do cluster, iniciar o failover dos recursos de cluster, altere o número de nós no cluster por meio de operações de dimensionamento e outras tarefas de gerenciamento.

Se você precisar instalar HDInsight em uma rede Virtual protegido, você deve permitir o acesso de entrada pela porta 443 para os seguintes endereços IP, que permitem Azure gerenciar o cluster HDInsight.

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

Permitir o acesso de entrada da porta 443 para esses endereços permitirá que você instale com êxito HDInsight em uma rede virtual protegida.

> [AZURE.IMPORTANT] HDInsight não dá suporte a restringindo o tráfego de saída, somente o tráfego de entrada. Ao definir regras de grupo de segurança de rede para a sub-rede que contém HDInsight, só use regras de entrada.

Os exemplos a seguir demonstram como criar um novo grupo de segurança de rede que permite que os endereços necessários e aplica o grupo de segurança para uma sub-rede dentro de sua rede Virtual. Estas etapas pressupõem que você já tiver criado uma rede Virtual e sub-rede que você deseja instalar o HDInsight em.

__Usando o PowerShell Azure__

    $vnetName = "Replace with your virtual network name"
    $resourceGroupName = "Replace with the resource group the virtual network is in"
    $subnetName = "Replace with the name of the subnet that HDInsight will be installed into"
    # Get the Virtual Network object
    $vnet = Get-AzureRmVirtualNetwork `
        -Name $vnetName `
        -ResourceGroupName $resourceGroupName
    # Get the region the Virtual network is in.
    $location = $vnet.Location
    # Get the subnet object
    $subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
    # Create a new Network Security Group.
    # And add exemptions for the HDInsight health and management services.
    $nsg = New-AzureRmNetworkSecurityGroup `
        -Name "hdisecure" `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -name "hdirule1" `
            -Description "HDI health and management address 168.61.49.99" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.49.99" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 300 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule2" `
            -Description "HDI health and management 23.99.5.239" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "23.99.5.239" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 301 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule3" `
            -Description "HDI health and management 168.61.48.131" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.48.131" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 302 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule4" `
            -Description "HDI health and management 138.91.141.162" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "138.91.141.162" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 303 `
            -Direction Inbound
    # Set the changes to the security group
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    # Apply the NSG to the subnet
    Set-AzureRmVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name $subnetName `
        -AddressPrefix $subnet.AddressPrefix `
        -NetworkSecurityGroupId $nsg

__Usando a CLI do Azure__

1. Use o seguinte comando para criar um novo grupo de segurança de rede denominado `hdisecure`. Substitua o grupo de recursos que contém a rede Virtual do Azure e o local (região), o grupo criado em __RESOURCEGROUPNAME__ e __local__ .

        azure network nsg create RESOURCEGROUPNAME hdisecure LOCATION
    
    Após o grupo tiver sido criado, você receberá informações sobre o novo grupo. Procure por uma linha semelhante à seguinte e salve o `/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure` informações. Ele será usado em uma etapa posterior.
    
        data:    Id                              : /subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure

2. Use o seguinte para adicionar regras para o novo grupo de segurança de rede que permitem a comunicação de entrada na porta 443 do serviço de saúde e gerenciamento do Azure HDInsight. Substitua __RESOURCEGROUPNAME__ com o nome do grupo de recursos que contém a rede Virtual do Azure.

        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule1 -p "*" -o "*" -u "443" -f "168.61.49.99" -e "VirtualNetwork" -c "Allow" -y 300 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule2 -p "*" -o "*" -u "443" -f "23.99.5.239" -e "VirtualNetwork" -c "Allow" -y 301 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule3 -p "*" -o "*" -u "443" -f "168.61.48.131" -e "VirtualNetwork" -c "Allow" -y 302 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule4 -p "*" -o "*" -u "443" -f "138.91.141.162" -e "VirtualNetwork" -c "Allow" -y 303 -r "Inbound"

3. Depois que as regras foram criadas, use o seguinte para aplicar o novo grupo de segurança de rede para uma sub-rede. Substitua __RESOURCEGROUPNAME__ com o nome do grupo de recursos que contém a rede Virtual do Azure. Substitua o nome da rede Virtual do Azure e a sub-rede que você usará durante a instalação do HDInsight __VNETNAME__ e __SUBNETNAME__ .

        azure network vnet subnet set RESOURCEGROUPNAME VNETNAME SUBNETNAME -w "/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    
    Quando esse comando for concluída, você pode instalar com êxito HDInsight na rede do Virtual protegido na sub-rede usada nestas etapas.

> [AZURE.IMPORTANT] Usando o acesso apenas abrir as etapas acima para o serviço de saúde e gerenciamento de HDInsight na nuvem Azure. Isso permite que você instale com êxito um cluster de HDInsight em sub-rede, no entanto, o acesso ao cluster HDInsight de fora da rede Virtual está bloqueado por padrão. Você precisará adicionar regras de grupo de segurança de rede adicionais se você deseja habilitar o acesso de fora da rede Virtual.
>
> Por exemplo, para permitir o acesso SSH da internet, você precisará adicionar uma regra semelhante à seguinte: 
>
> * PowerShell Azure-```Add-AzureRmNetworkSecurityRuleConfig -Name "SSSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound```
> * CLI Azure-```azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule4 -p "*" -o "*" -u "22" -f "*" -e "VirtualNetwork" -c "Allow" -y 304 -r "Inbound"```

Para obter mais informações sobre grupos de segurança de rede, consulte [Visão geral de grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md). Para obter informações sobre como controlar o roteamento em uma rede Virtual do Azure, consulte [encaminhamento de IP e rotas de definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md).

##<a id="tasks"></a>Tarefas e informações

Esta seção contém informações sobre tarefas comuns e as informações que você pode precisar quando usar HDInsight com uma rede virtual.

###<a name="determine-the-fqdn"></a>Determinar o FQDN

Cluster HDInsight será atribuído um nome de domínio totalmente qualificado (FQDN) específico para a interface de rede Virtual. Este é o endereço que você deve usar ao se conectar ao cluster de outros recursos em uma rede virtual. Para determinar o FQDN, use o seguinte URL para o serviço de gerenciamento de Ambari de consulta:

    https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [AZURE.NOTE] Para obter mais informações sobre como usar Ambari com HDInsight, consulte [clusters de Monitor Hadoop em HDInsight usando a API Ambari](hdinsight-monitor-use-ambari-api.md).

Especifique o nome do cluster e um serviço e o componente executado no cluster, como o Gerenciador de recursos de fio COLORIDO.

> [AZURE.NOTE] Os dados retornados são um documento de JavaScript Object Notation (JSON) que contém uma grande quantidade de informações sobre o componente. Para extrair apenas o FQDN, você deve usar um analisador JSON para recuperar o `host_components[0].HostRoles.host_name` valor.

Por exemplo, para retornar o FQDN de um cluster de HDInsight Hadoop, você pode usar um dos seguintes métodos para recuperar os dados para o Gerenciador de recursos de fio COLORIDO:

* [PowerShell Azure](../powershell-install-configure.md)

        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix

        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/     components/resourcemanager"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        $FQDN = $JsonObject.host_components[0].HostRoles.host_name
        Write-host $FQDN

* [cURL](http://curl.haxx.se/) e [jq](http://stedolan.github.io/jq/)

        curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

###<a name="connecting-to-hbase"></a>Conectando ao HBase

Para se conectar à HBase remotamente usando a API Java, você deve determinar os endereços de quorum ZooKeeper para o cluster HBase e especificar isso no seu aplicativo.

Para obter o ZooKeeper endereço de quorum, use um dos seguintes métodos para consultar o serviço de gerenciamento de Ambari:

* [PowerShell Azure](../powershell-install-configure.md)

        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix

        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [cURL](http://curl.haxx.se/) e [jq](http://stedolan.github.io/jq/)

        curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [AZURE.NOTE] Para obter mais informações sobre como usar Ambari com HDInsight, consulte [clusters de Monitor Hadoop em HDInsight usando a API Ambari](hdinsight-monitor-use-ambari-api.md).

Quando você tiver as informações de quorum, usá-lo em seu aplicativo cliente.

Por exemplo, para um aplicativo Java que usa a API HBase, você poderia adicionar um arquivo de **hbase-site.xml** ao projeto e especificar as informações de quorum no arquivo da seguinte maneira:

```
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

###<a name="verify-network-connectivity"></a>Verificar a conectividade de rede

Alguns serviços, como o SQL Server, podem limitar conexões de rede de entrada. Isso impedirá que HDInsight trabalhar com êxito com esses serviços.

Se você encontrar problemas ao acessar um serviço de HDInsight, consulte a documentação do serviço garantir que você ativou o acesso à rede. Você também pode verificar o acesso de rede, criando uma máquina virtual Azure na mesma rede virtual e usar utilitários de cliente para verificar se a máquina virtual pode se conectar ao serviço através da rede virtual.

##<a id="nextsteps"></a>Próximas etapas

Os exemplos a seguir demonstram como usar HDInsight com uma rede Virtual Azure:

* [Analisar dados de sensor com tempestade e HBase no HDInsight](hdinsight-storm-sensor-data-analysis.md) - demonstra como configurar um cluster tempestade e HBase em uma rede virtual, bem como gravar dados remotamente HBase de tempestade.

* [Provisionar Hadoop clusters no HDInsight](hdinsight-hadoop-provision-linux-clusters.md) - fornece informações sobre provisionamento Hadoop clusters, incluindo informações sobre como usar a rede Virtual do Azure.

* [Usar Sqoop com Hadoop em HDInsight](hdinsight-use-sqoop-mac-linux.md) - fornece informações sobre como usar o Sqoop para transferir dados com o SQL Server em uma rede virtual.

Para saber mais sobre redes virtuais Azure, consulte [Visão geral de rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).
