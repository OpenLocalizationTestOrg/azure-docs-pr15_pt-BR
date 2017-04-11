<properties
    pageTitle="Consultar dados do armazenamento de Blob compatível com o HDFS | Microsoft Azure"
    description="Armazenamento de blob do Microsoft Azure HDInsight usa como o armazenamento de dados grande para HDFS. Saiba como consultar dados do armazenamento de Blob e armazenar os resultados de sua análise."
    keywords="blob storage, hdfs, dados estruturados, dados não estruturados"
    services="hdinsight,storage"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/06/2016"
    ms.author="jgao"/>


# <a name="use-hdfs-compatible-azure-blob-storage-with-hadoop-in-hdinsight"></a>Usar o armazenamento de Blob do Azure HDFS compatível com Hadoop em HDInsight

Aprenda a usar o armazenamento de Blob do Azure de baixo custo com HDInsight, criar conta de armazenamento do Azure e contêiner de armazenamento de Blob e, em seguida, os dados dentro de endereço.

Armazenamento de Blob do Azure é uma solução de armazenamento geral robustos que integra-se com HDInsight. Por meio de uma interface de sistema (HDFS) de arquivo distribuído Hadoop, o conjunto completo de componentes de HDInsight pode operar diretamente nos dados estruturados ou não no armazenamento de Blob.

Armazenamento de dados no armazenamento de Blob permite excluir com segurança os clusters HDInsight que são usados para computação sem perder dados do usuário.

> [AZURE.IMPORTANT] HDInsight suporta apenas blobs de bloco. Ele não oferece suporte a página ou acrescentar blobs.

Para obter informações sobre a criação de um cluster de HDInsight, consulte [Introdução ao HDInsight] [ hdinsight-get-started] ou [HDInsight criar clusters][hdinsight-creation].


## <a name="hdinsight-storage-architecture"></a>Arquitetura de armazenamento do HDInsight
O diagrama a seguir fornece um modo de exibição Resumo da HDInsight arquitetura de armazenamento:

![Hadoop clusters usam a API HDFS para acessar e armazenar dados estruturados e não estruturados no armazenamento de Blob.] (./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "Arquitetura de armazenamento de HDInsight")

HDInsight fornece acesso ao sistema de arquivos distribuídos que está anexado localmente para os nós de computação. Este sistema de arquivos pode ser acessado usando o URI totalmente qualificado, por exemplo:

    hdfs://<namenodehost>/<path>

Além disso, HDInsight fornece a capacidade de acessar os dados que estão armazenados no armazenamento de Blob do Azure. A sintaxe é:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

> [AZURE.NOTE] Em versões anteriores ao 3.0, de HDInsight `asv://` foi usado em vez de `wasb://`. `asv://`não deve ser usado com clusters HDInsight 3.0 ou superior, pois resultará em um erro.

Hadoop suporta uma noção do sistema de arquivos padrão. O sistema de arquivo padrão indica um esquema padrão e autoridade. Ele também pode ser usado para resolver caminhos relativos. Durante o processo de criação de HDInsight, uma conta de armazenamento do Azure e um armazenamento de Blob do Azure específico contêiner dessa conta é designado como o sistema de arquivo padrão.

Além dessa conta de armazenamento, você pode adicionar contas de armazenamento adicional a partir da mesma assinatura do Azure ou diferentes assinaturas do Azure durante o processo de criação ou após um cluster tiver sido criado. Para obter instruções sobre como adicionar contas de armazenamento adicional, consulte [criar HDInsight clusters][hdinsight-creation].

- **Contêineres nas contas de armazenamento que estão conectados a um cluster:** Porque o nome de conta e chave estão associados a cluster durante a criação, você tem acesso total aos blobs esses contêineres.

- **Contêineres públicos ou públicos blobs em contas de armazenamento que não estão conectadas a um cluster:** Você tem permissão de somente leitura para os blobs nos contêineres.

    > [AZURE.NOTE]
        > Contêineres públicos permitem que você obter uma lista de todas as bolhas que estão disponíveis no contêiner e obter metadados de contêiner. Blobs públicas permitem que você acesse os blobs somente se você souber a URL exata. Para obter mais informações, consulte <a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">restringir o acesso a contêineres e blobs</a>.

- **Contêineres particulares em contas de armazenamento que não estão conectados a um cluster:** Você não consegue acessar os blobs nos contêineres, a menos que você definir a conta de armazenamento quando você envia os trabalhos de WebHCat. Isso é explicado mais adiante neste artigo.


As contas de armazenamento definidos no processo de criação e suas chaves são armazenadas em %HADOOP_HOME%/conf/core-site.xml em nós do cluster. O comportamento padrão do HDInsight é usar as contas de armazenamento definidas no arquivo core-site.xml. Não é recomendável para editar o arquivo de núcleo site.xml porque o node(master) de cabeça cluster pode ser criada ou migrados a qualquer momento, e quaisquer alterações aos arquivos serão perdidas.

Vários trabalhos de WebHCat, incluindo ramificação, MapReduce, Hadoop streaming e porco, podem conter uma descrição das contas de armazenamento e metadados com eles. (Isso atualmente funciona para porco com contas de armazenamento, mas não para metadados.) Na seção [blobs de acesso usando o PowerShell do Azure](#powershell) deste artigo, há uma amostra desse recurso. Para obter mais informações, consulte [usando um Cluster de HDInsight com contas de armazenamento alternativos e Metastores](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Armazenamento de blob pode ser usado para dados estruturados e não estruturados. Contêineres de armazenamento de blob para armazenar dados como pares chave/valor, e há uma hierarquia de diretório. No entanto o caractere de barra (/) pode ser usado dentro do nome de chave para torná-la como se um arquivo está armazenado em uma estrutura de diretório. Por exemplo, a chave de um blob sejam *input/log1.txt*. Nenhum diretório real *entrada* existe, mas devido a presença de um caractere de barra no nome da chave, ela tem a aparência de um caminho de arquivo.

###<a id="benefits"></a>Benefícios do armazenamento de Blob
O custo de desempenho implícitas de localização de não conjunta clusters de computadores e recursos de armazenamento é reduzido aliás clusters de computadores são criados Fechar para os recursos da conta de armazenamento dentro da região Azure, onde a rede de alta velocidade facilita bastante eficiente para os nós de computação acessar os dados dentro do armazenamento de Blob do Azure.

Há vários benefícios associados ao armazenar os dados em armazenamento de Blob do Azure em vez de HDFS:

* **Compartilhamento e reutilização de dados:** Os dados em HDFS estar localizados dentro do cluster de cálculo. Apenas os aplicativos que têm acesso ao cluster de cálculo podem usar os dados usando APIs HDFS. Os dados no armazenamento de Blob do Azure podem ser acessados por meio de APIs do HDFS ou as [APIs de restante de armazenamento de Blob][blob-storage-restAPI]. Assim, um conjunto maior de ferramentas e aplicativos (incluindo outros clusters HDInsight) pode ser usado para produzir e consumir os dados.
* **Arquivamento de dados:** Armazenamento de dados no armazenamento de Blob do Azure habilita os clusters HDInsight usados para cálculo a ser excluído com segurança sem perder dados do usuário.
* **Custo de armazenamento de dados:** Armazenar dados em DFS a longo prazo é mais caro que armazenar os dados no armazenamento de Blob do Azure porque o custo de um cluster de cálculo é maior que o custo de um contêiner de armazenamento de Blob do Azure. Além disso, porque os dados não precisam ser Recarregar para cada geração de cluster de computação, você também está salvando custos de carregamento de dados.
* **Fora de escala elástica:** Embora HDFS fornece um sistema de arquivo dimensionada-out, a escala é determinada pelo número de nós que você cria para o seu cluster. Alterar a escala pode se tornar um processo mais complicado de depender elástica dimensionamento recursos que você obtém automaticamente no armazenamento de Blob do Azure.
* **Replicação geográfica:** Seus contêineres de armazenamento de Blob do Azure podem ser replicados de localização geográfica. Embora isso lhe permite recuperação geográfica e redundância de dados, um failover para a localização geográfica replicado seriamente afeta o desempenho, e ele pode provocam custos adicionais. Então nossa recomendação é escolher a localização geográfica-replicação de maneira sensata e somente se o valor dos dados vale o custo adicional.

Determinados trabalhos MapReduce e pacotes podem criar resultados intermediários que você realmente não deseja armazenar no armazenamento de Blob do Azure. Nesse caso, você pode optar por armazenar os dados na HDFS local. Na verdade, HDInsight usa DFS para vários desses resultados intermediária em trabalhos de seção e outros processos.

> [AZURE.NOTE] A maioria dos comandos HDFS (por exemplo, <b>ls</b>, <b>copyFromLocal</b> e <b>mkdir</b>) ainda funciona conforme esperado. Somente os comandos que são específicos para a HDFS implementação nativa (que é considerada como DFS), como <b>fschk</b> e <b>dfsadmin</b>, mostrará o comportamento diferente no armazenamento de Blob do Azure.

## <a name="create-blob-containers"></a>Criar contêineres de Blob

Para usar blobs, você cria uma [conta de armazenamento do Azure]primeiro[azure-storage-create]. Como parte desse, você especificar uma região Azure que armazenará os objetos que você cria usando esta conta. O cluster e a conta de armazenamento devem estar hospedadas na mesma região. A seção metastore SQL Server banco de dados e Oozie metastore SQL Server também devem estar localizados na mesma região.

Onde quer que ele reside, cada blob que criar pertence a um contêiner na sua conta de armazenamento do Azure. Este contêiner pode ser um blob existente que foi criado fora do HDInsight, ou pode ser um contêiner que é criado para um cluster de HDInsight.


O contêiner de Blob padrão armazena informações específicas do cluster como logs e o histórico de trabalho. Não compartilhe um contêiner de Blob padrão com vários clusters de HDInsight. Isso poderá ser corrompido histórico de trabalho e o cluster será misbehave. É recomendável usar um contêiner diferente para cada cluster e colocar dados compartilhados em uma conta de armazenamento vinculado especificada na implantação de todos os clusters relevantes em vez da conta de armazenamento padrão. Para obter mais informações sobre como configurar contas de armazenamento vinculado, consulte [criar HDInsight clusters][hdinsight-creation]. No entanto, você pode reutilizar um contêiner de armazenamento padrão depois do cluster HDInsight original foi excluído. Para HBase clusters, na verdade, você pode manter o esquema de tabela HBase e dados por criar um novo cluster de HBase usando o contêiner de armazenamento de blob padrão que é usado por um cluster de HBase que foi excluído.


### <a name="using-the-azure-portal"></a>Usando o Portal do Azure

Ao criar um cluster de HDInsight a partir do Portal, você tem as opções para usar uma conta de armazenamento existente ou criar uma nova conta de armazenamento:

![fonte de dados do hdinsight hadoop criação](./media/hdinsight-hadoop-use-blob-storage/hdinsight.provision.data.source.png)

###<a name="using-azure-cli"></a>Usando CLI Azure

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

Se você tiver [instalado e configurado a CLI do Azure](../xplat-cli-install.md), o comando a seguir pode ser usado para uma conta de armazenamento e o contêiner.

    azure storage account create <storageaccountname> --type LRS

> [AZURE.NOTE] O `--type` parâmetro indica como a conta de armazenamento será replicada. Para obter mais informações, consulte [Replicação de armazenamento do Azure](../storage/storage-redundancy.md). Não use ZRS como ZRS não dá suporte blob de página, arquivo, tabela ou fila.

Você será solicitado para especificar a região geográfica que a conta de armazenamento ficarão localizada em. Você deve criar a conta de armazenamento na mesma região que você planeja criar seu cluster HDInsight.

Após criar a conta de armazenamento, use o seguinte comando para recuperar as chaves de conta de armazenamento:

    azure storage account keys list <storageaccountname>

Para criar um contêiner, use o seguinte comando:

    azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>

### <a name="using-azure-powershell"></a>Usando o PowerShell Azure

Se você [instalado e configurado o Azure PowerShell][powershell-install], você pode usar o seguinte de prompt do PowerShell do Azure para criar uma conta de armazenamento e o contêiner:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"
    
    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID
    
    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location
    
    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS 
    
    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

## <a name="address-files-in-blob-storage"></a>Arquivos de endereço no armazenamento de Blob

O esquema URI para acessar os arquivos no armazenamento de Blob do HDInsight é:

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


O esquema de URI fornece acesso não criptografado (com o *wasb:* prefixo) e SSL criptografadas acesso (com *wasbs*). É recomendável usar *wasbs* sempre que possível, mesmo quando o acesso a dados que reside dentro da região da mesma no Azure.

O &lt;BlobStorageContainerName&gt; identifica o nome do contêiner no armazenamento de Blob do Azure.
O &lt;StorageAccountName&gt; identifica o nome da conta de armazenamento do Azure. É necessário um nome de domínio totalmente qualificado (FQDN).

Se nem &lt;BlobStorageContainerName&gt; nem &lt;StorageAccountName&gt; tiver sido especificado, o sistema de arquivo padrão é usado. Os arquivos no sistema de arquivos padrão, você pode usar um caminho relativo ou um caminho absoluto. Por exemplo, o arquivo *hadoop-mapreduce-examples.jar* que vem com HDInsight clusters pode ser referido usando um destes procedimentos:

    wasbs://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasbs:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE] O nome do arquivo é <i>hadoop-examples.jar</i> em clusters de versões 2.1 e 1,6 HDInsight.


O &lt;caminho&gt; é o nome de caminho HDFS do arquivo ou pasta. Como contêineres no armazenamento de Blob do Azure são simplesmente chave-valor armazena, não há nenhum sistema de arquivos hierárquico true. Um caractere de barra (/) dentro de uma chave de blob é interpretado como um separador de diretório. Por exemplo, o nome de blob do *hadoop-mapreduce-examples.jar* é:

    example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE] Ao trabalhar com blobs fora do HDInsight, a maioria dos utilitários não reconhece o formato WASB e em vez disso, esperar um formato de caminho básicas, tais como `example/jars/hadoop-mapreduce-examples.jar`.

## <a name="access-blobs-using-azure-cli"></a>Blobs de acesso usando CLI do Azure

Use o seguinte comando para listar os comandos relacionados blob:

    azure storage blob

**Exemplo de uso do Azure CLI para carregar um arquivo**

    azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Exemplo do uso do Azure CLI para baixar um arquivo**

    azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>

**Exemplo do uso do Azure CLI para excluir um arquivo**

    azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Exemplo do uso do Azure CLI para listar os arquivos**

    azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>

## <a name="access-blobs-using-azure-powershell"></a>Blobs de acesso usando o PowerShell do Azure

> [AZURE.NOTE] Os comandos desta seção oferecem um exemplo básico de uso do PowerShell para acessar dados armazenados em bolhas. Para um exemplo mais completo que é personalizado para trabalhar com HDInsight, consulte as [Ferramentas de HDInsight](https://github.com/Blackmist/hdinsight-tools).

Use o seguinte comando para listar os cmdlets relacionados blob:

    Get-Command *blob*

![Lista relacionados blob de cmdlets do PowerShell.][img-hdi-powershell-blobcommands]

###<a name="upload-files"></a>Carregar arquivos

Consulte [carregar dados ao HDInsight][hdinsight-upload-data].

###<a name="download-files"></a>Baixar arquivos

O seguinte script downloads um blob de bloco para a pasta atual. Antes de executar o script, altere o diretório para uma pasta onde você tem permissões de gravação.

    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at creation.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.
    
    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    Login-AzureRmAccount 
    Select-AzureRmSubscription -SubscriptionID "<Your Azure Subscription ID>"
    
    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    
    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
    
    Write-Host "List the downloaded file ..." -ForegroundColor Green
    cat "./$blob"

Fornecer o nome do grupo de recursos e o nome do cluster, você pode usar o seguinte código:

    $resourceGroupName = "<AzureResourceGroupName>"
    $clusterName = "<HDInsightClusterName>"
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.
    
    $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
    $defaultStorageContainer = $cluster.DefaultStorageContainer
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
    
    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob $blob -Context $storageContext -Force

###<a name="delete-files"></a>Excluir arquivos


    Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob

###<a name="list-files"></a>Lista de arquivos

    Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix "example/data/"

###<a name="run-hive-queries-using-an-undefined-storage-account"></a>Executar consultas de seção usando uma conta de armazenamento indefinido

Este exemplo mostra como uma pasta da conta de armazenamento que não está definida durante o processo de criação de lista.
$clusterName = "<HDInsightClusterName>"

    $undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
    $undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

    $undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

    Use-AzureRmHDInsightCluster $clusterName

    $defines = @{}
    $defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

    Invoke-AzureRmHDInsightHiveJob -Defines $defines -Query "dfs -ls wasbs://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar o armazenamento de Blob do Azure HDFS compatível com HDInsight e você aprendeu que o armazenamento de Blob do Azure é um componente fundamental do HDInsight. Isso permite criar soluções de aquisição de dados scalable, a longo prazo e arquivamento com o armazenamento de Blob do Azure e usar HDInsight para desbloquear as informações contidas em dados estruturados e não estruturados armazenados.

Para obter mais informações, consulte:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Carregar dados ao HDInsight][hdinsight-upload-data]
* [Use a seção com HDInsight][hdinsight-use-hive]
* [Usar porco com HDInsight][hdinsight-use-pig]
* [Usar assinaturas de acesso compartilhado do Azure armazenamento para restringir o acesso aos dados com HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: ../powershell-install-configure.md
[hdinsight-creation]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
