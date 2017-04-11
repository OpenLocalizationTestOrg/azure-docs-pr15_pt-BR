<properties 
   pageTitle="Configurar a replicação de HBase entre dois centros de dados | Microsoft Azure" 
   description="Saiba como configurar a replicação de HBase em dois data centers e sobre os casos de uso para replicação de cluster." 
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
   ms.date="07/25/2016"
   ms.author="jgao"/>

# <a name="configure-hbase-geo-replication-in-hdinsight"></a>Configurar replicação HBase geográfica em HDInsight

> [AZURE.SELECTOR]
- [Configurar a conectividade VPN](../hdinsight-hbase-geo-replication-configure-vnets.md)
- [Configurar o DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Configurar a replicação de HBase](hdinsight-hbase-geo-replication.md) 
 
Saiba como configurar a replicação de HBase em dois data centers. Alguns casos de uso para replicação cluster incluem:

- Backup e recuperação de desastres
- Agregação de dados
- Distribuição de dados geográficos
- Inclusão de dados online combinado com a análise de dados offline

Replicação de cluster usa uma metodologia de envio de origem. Um cluster de HBase pode ser uma fonte, um destino, ou pode realizar ambas as funções de uma vez. Replicação é assíncrona, e o objetivo de replicação é consistência eventual. Quando a fonte recebe uma edição para uma família de coluna com replicação habilitada, essa edição é propagada para todos os clusters de destino. Quando os dados são replicados de um cluster para outro, o cluster de origem e todos os clusters que já têm consumidas os dados são controlados para evitar loops de replicação. Para obter mais informações, neste tutorial, você irá configurar uma replicação de origem / destino.  Para outras topologias de cluster, consulte o [Guia de referência do Apache HBase](http://hbase.apache.org/book.html#_cluster_replication).

Esta é a terceira parte da série:

- [Configurar uma conectividade VPN entre duas redes virtuais][hdinsight-hbase-replication-vnet]
- [Configurar o DNS para as redes virtuais][hdinsight-hbase-replication-dns]
- Configurar a replicação de localização geográfica HBase (Este tutorial)

O diagrama a seguir ilustra as duas redes virtuais e a conectividade de rede que você criou em [Configurar uma conectividade VPN entre duas redes virtuais] [ hdinsight-hbase-geo-replication-vnet] e [Configurar o DNS para as redes virtuais][hdinsight-hbase-replication-dns]: 

![Diagrama de rede virtual de replicação de HDInsight HBase][img-vnet-diagram]

## <a id="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Uma estação de trabalho com o PowerShell do Azure**.

    Para executar scripts do PowerShell, você deve executar o Azure PowerShell como administrador e defina a política de execução para *RemoteSigned*. Consulte usando o cmdlet Set-ExecutionPolicy.
    
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Dois Azure rede virtual com conectividade VPN e DNS configurados**.  Para obter instruções, consulte [Configurar uma conexão VPN entre duas redes virtuais Azure][hdinsight-hbase-replication-vnet]e [Configurar o DNS entre duas redes virtuais Azure][hdinsight-hbase-replication-dns].


    Antes de executar scripts do PowerShell, verifique se que você está conectado à sua assinatura do Azure usando o seguinte cmdlet:

        Add-AzureAccount

    Se você tiver várias assinaturas Azure, use o cmdlet a seguir para configurar a assinatura atual:

        Select-AzureSubscription <AzureSubscriptionName>



## <a name="provision-hbase-clusters-in-hdinsight"></a>Provisionar clusters HBase no HDInsight

Em [Configurar uma conexão VPN entre duas redes virtuais Azure][hdinsight-hbase-replication-vnet], você criou uma rede virtual em uma Europa Central de dados e uma rede virtual em um data center dos EUA. A rede virtual dois estão conectadas por meio de VPN. Nesta seção, você irá provisionar um cluster de HBase em cada uma das redes virtuais. Posteriormente neste tutorial, você criará um dos clusters HBase replicar outro cluster HBase.

Portal de clássico do Azure não dá suporte a provisionamento clusters HDInsight com opções de configuração personalizada. Por exemplo, defina *hbase.replication* como *true*. Se você definir o valor no arquivo de configuração após um cluster está provisionado, você perderá a configuração após o cluster está sendo criado. Para obter mais informações, consulte [clusters de provisionar Hadoop em HDInsight][hdinsight-provision]. Uma das opções para provisionar cluster HDInsight com opções personalizadas está usando o PowerShell do Azure.


**Provisionar um Cluster de HBase da Contoso-VNet-UE** 

1. Em sua estação de trabalho, abra o Windows PowerShell ISE.
2. Defina as variáveis no início do script e execute o script.

        # create hbase cluster with replication enabled
        
        $azureSubscriptionName = "[AzureSubscriptionName]"
        
        $hbaseClusterName = "Contoso-HBase-EU" # This is the HBase cluster name to be used.
        $hbaseClusterSize = 1   # You must provision a cluster that contains at least 3 nodes for high availability of the HBase services.
        $hadoopUserLogin = "[HadoopUserName]"
        $hadoopUserpw = "[HadoopUserPassword]"
        
        $vNetName = "Contoso-VNet-EU"  # This name must match your Europe virtual network name.
        $subNetName = 'Subnet-1' # This name must match your Europe virtual network subnet name.  The default name is "Subnet-1".
        
        $storageAccountName = 'ContosoStoreEU'  # The script will create this storage account for you.  The storage account name doesn't support hyphens. 
        $storageAccountName = $storageAccountName.ToLower() #Storage account name must be in lower case.
        $blobContainerName = $hbaseClusterName.ToLower()  #Use the cluster name as the default container name.
        
        #connect to your Azure subscription
        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName
        
        # Create a storage account used by the HBase cluster
        $location = Get-AzureVNetSite -VNetName $vNetName | %{$_.Location} # use the virtual network location
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
        
        # Create a blob container used by the HBase cluster
        $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{$_.Primary}
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $blobContainerName -Context $storageContext
        
        # Create provision configuration object
        $hbaseConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHBaseConfiguration'
            
        $hbaseConfigValues.Configuration = @{ "hbase.replication"="true" } #this modifies the hbase-site.xml file
        
        # retrieve vnet id based on vnetname
        $vNetID = Get-AzureVNetSite -VNetName $vNetName | %{$_.id}
        
        $config = New-AzureHDInsightClusterConfig `
                        -ClusterSizeInNodes $hbaseClusterSize `
                        -ClusterType HBase `
                        -VirtualNetworkId $vNetID `
                        -SubnetName $subNetName `
                    | Set-AzureHDInsightDefaultStorage `
                        -StorageAccountName $storageAccountName `
                        -StorageAccountKey $storageAccountKey `
                        -StorageContainerName $blobContainerName `
                    | Add-AzureHDInsightConfigValues `
                        -HBase $hbaseConfigValues
        
        # provision HDInsight cluster
        $hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
        
        $config | New-AzureHDInsightCluster -Name $hbaseClusterName -Location $location -Credential $credential



**Provisionar um Cluster de HBase em Contoso-VNet-US** 

- Use o mesmo script com os seguintes valores:

        $hbaseClusterName = "Contoso-HBase-US" # This is the HBase cluster name to be used.
        $vNetName = "Contoso-VNet-US"  # This name must match your Europe virtual network name.
        $storageAccountName = 'ContosoStoreUS'  

    Porque você já tiver conectado à sua conta do Azure, você não precisa executar as seguintes comlets mais:

        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName




## <a name="configure-dns-conditional-forwarder"></a>Configurar encaminhadores condicionais DNS

Em [Configurar o DNS para as redes virtuais][hdinsight-hbase-replication-dns], você configurou servidores DNS para as duas redes. Os clusters HBase têm sufixos de domínio diferente. Portanto, você precisa configurar os encaminhadores condicionais DNS adicionais.

Para configurar encaminhadores condicionais, você precisa saber o sufixo dos dois clusters HBase referente ao domínio. 

**Para localizar os sufixos de domínio dos dois clusters HBase**

1. RDP em **Contoso-HBase-UE**.  Para obter instruções, consulte [Gerenciar Hadoop clusters em HDInsight usando o Portal de clássico do Azure][hdinsight-manage-portal]. É realmente headnode0 do cluster.
2. Abra um console do Windows PowerShell ou um prompt de comando.
3. Executar **ipconfig**e anote o **sufixo DNS específico de Conexão**.
4. Não feche a sessão RDP.  Você precisará dele mais tarde para testar a resolução de nome de domínio.
5. Repita as mesmas etapas para descobrir o **sufixo DNS específico de Conexão** da **Contoso-HBase-US**.


**Para configurar os encaminhadores DNS**
 
1.  RDP em **Contoso DNS-da UE**. 
2.  Clique na tecla do Windows no canto inferior esquerdo.
2.  Clique em **Ferramentas administrativas**.
3.  Clique em **DNS**.
4.  No painel esquerdo, expanda **DSN**, **Contoso DNS-da UE**.
5.  Clique com botão direito **Encaminhadores condicionais**e clique em **Novo encaminhador condicional**. 
5.  Insira as seguintes informações:
    - **Domínio DNS**: insira o sufixo DNS de Contoso-HBase-US. Por exemplo: Contoso-HBase-US.f5.internal.cloudapp.net.
    - **Endereços IP dos servidores mestres**: insira 10.2.0.4, que é endereço IP do Contoso-DNS-US. Verifique se o IP. Seu servidor DNS pode ter um endereço IP diferente.
6.  Pressione **ENTER**e, em seguida, clique em **Okey**.  Agora você poderá resolver endereço IP do Contoso-DNS-US da Contoso DNS-da UE.
7.  Repita as etapas para adicionar um encaminhador condicional de DNS para o serviço DNS na máquina virtual Contoso-DNS-US com os seguintes valores:
    - **Domínio DNS**: insira o sufixo DNS da Contoso-HBase-UE. 
    - **Endereços IP dos servidores mestres**: insira 10.2.0.4, que é endereço IP do Contoso-DNS-UE.

**Para testar a resolução de nome de domínio**

1. Alternar para a janela de Contoso-HBase-UE RDP.
2. Abra um prompt de comando.
3. Execute o comando ping:

        ping headnode0.[DNS suffix of Contoso-HBase-US]

    O protocolo ICM está ativado os nós trabalhador dos clusters HBase

4. Não feche a sessão RDP. Você ainda precisará dele mais tarde no tutorial.
5. Repita as mesmas etapas para ping a headnode0 da Contoso-HBase-UE da Contoso-HBase-US.

>[AZURE.IMPORTANT] DNS deve trabalhar antes de passar para as próximas etapas.

## <a name="enable-replication-between-hbase-tables"></a>Habilitar a replicação entre tabelas de HBase

Agora, você pode criar uma tabela de HBase de exemplo, habilitar a replicação e, em seguida, testá-lo com alguns dados. A tabela de exemplo que você usará tem duas famílias de coluna: pessoal e o Office. 

Neste tutorial, você criará o cluster Europa HBase como o cluster de origem e o cluster de US HBase como o cluster de destino.

Crie tabelas de HBase com os mesmos nomes e famílias de coluna em clusters de origem e de destino, para que o cluster de destino Saiba onde armazenar dados que ele receberá. Para obter mais informações sobre como usar o shell de HBase, consulte [Introdução ao Apache HBase em HDInsight][hdinsight-hbase-get-started].

**Para criar uma tabela de HBase em Contoso-HBase-UE**

1. Alternar para a janela RDP **Contoso-HBase-UE** .
2. Da área de trabalho, clique em **linha de comando do Hadoop**.
2. Altere a pasta para a pasta HBase:

        cd %HBASE_HOME%\bin
3. Abra o shell de HBase:

        hbase shell
4. Crie uma tabela de HBase:

        create 'Contacts', 'Personal', 'Office'
5. Não feche a sessão RDP nem a janela de linha de comando do Hadoop. Você ainda será necessário-las mais tarde no tutorial.
    
**Para criar uma tabela de HBase em Contoso-HBase-US**

- Repita as mesmas etapas para criar a mesma tabela na Contoso-HBase-US.


**Para adicionar Contoso-HBase-US como um ponto de replicação**

1. Alternar para a janela RDP **Contso-HBase_EU** .
2. Na janela de shell HBase, adicione o cluster de destino (Contoso-HBase-US) como um ponto, por exemplo:

        add_peer '1', 'zookeeper0.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper1.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper2.contoso-hbase-us.d4.internal.cloudapp.net:2181:/hbase'

    No exemplo, o sufixo do domínio é *contoso-hbase-us.d4.internal.cloudapp.net*. Você precisa atualizá-lo para corresponder o sufixo do domínio do cluster nos HBase. Verifique se que há sem espaços entre o nome de host.

**Para configurar cada família de coluna a ser replicado no cluster fonte**

1. Na janela de shell HBase da sessão RDP **Contso HBase-da UE** , configure cada família de coluna a ser replicado:

        disable 'Contacts'
        alter 'Contacts', {NAME => 'Personal', REPLICATION_SCOPE => '1'}
        alter 'Contacts', {NAME => 'Office', REPLICATION_SCOPE => '1'}
        enable 'Contacts'

**Para carregar dados para a tabela de HBase em massa**

Um arquivo de dados de exemplo é carregado em um contêiner de Blob do Azure público com a seguinte URL:

        wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

O conteúdo do arquivo:

        8396    Calvin Raji 230-555-0191    5415 San Gabriel Dr.
        16600   Karen Wu    646-555-0113    9265 La Paz
        4324    Karl Xie    508-555-0163    4912 La Vuelta
        16891   Jonathan Jackson    674-555-0110    40 Ellis St.
        3273    Miguel Miller   397-555-0155    6696 Anchor Drive
        3588    Osarumwense Agbonile    592-555-0152    1873 Lion Circle
        10272   Julia Lee   870-555-0110    3148 Rose Street
        4868    Jose Hayes  599-555-0171    793 Crawford Street
        4761    Caleb Alexander 670-555-0141    4775 Kentucky Dr.
        16443   Terry Chander   998-555-0171    771 Northridge Drive

Você pode carregar o mesmo arquivo de dados para o seu cluster HBase e importar os dados a partir daí.

1. Alternar para a janela RDP **Contoso-HBase-UE** .
2. Da área de trabalho, clique em **linha de comando do Hadoop**.
3. Altere a pasta para a pasta HBase:

        cd %HBASE_HOME%\bin

4. carregar os dados:

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts


## <a name="verify-that-data-replication-is-taking-place"></a>Verifique se que essa replicação de dados está ocorrendo

Você pode confirmar que a replicação está ocorrendo examinando as tabelas de ambos os clusters com os seguintes comandos do shell de HBase:

        Scan 'Contacts'


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a configurar a replicação de HBase entre dois centros de dados. Para saber mais sobre HDInsight e HBase, consulte:

- [Página de serviço de HDInsight](https://azure.microsoft.com/services/hdinsight/)
- [Documentação do HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
- [Introdução ao Apache HBase em HDInsight][hdinsight-hbase-get-started]
- [Visão geral de HDInsight HBase][hdinsight-hbase-overview]
- [Provisionar HBase clusters em rede Virtual do Azure][hdinsight-hbase-provision-vnet]
- [Analisar sentimento Twitter em tempo real com HBase][hdinsight-hbase-twitter-sentiment]
- [Analisar dados de sensor com tempestade e HBase em HDInsight (Hadoop)][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: powershell-install-configure.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
