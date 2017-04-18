<properties
    pageTitle="Usar o armazenamento do Azure Premium com o SQL Server | Microsoft Azure"
    description="Este artigo usa recursos criados com o modelo clássico de implantação e fornece orientação sobre como usar o armazenamento do Azure Premium com o SQL Server em máquinas virtuais do Azure em execução."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="danielsollondon"
    manager="jhubbard"
    editor="monicar"    
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth"/>

# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Usar o armazenamento do Azure Premium com o SQL Server em máquinas virtuais


## <a name="overview"></a>Visão geral

[O armazenamento do Azure Premium](../storage/storage-premium-storage.md) é a próxima geração de armazenamento que oferece baixa latência e alta produtividade IO. Ele funciona melhor para chaves IO intenso cargas de trabalho, como o SQL Server em IaaS [máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Este artigo fornece orientações de planejamento e para migrar uma máquina Virtual executando o SQL Server para usar o armazenamento de Premium. Isso inclui Azure infraestrutura (rede, armazenamento) e etapas de máquina virtual do Windows de convidado. O exemplo no [Apêndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) mostra uma migração de ponta a ponta completo abrangente de como mover VMs maiores aproveitar melhor armazenamento SSD local com o PowerShell.

É importante entender o processo de ponta a ponta do armazenamento de Premium utilizando do Azure com o SQL Server em VMs IAAS. Isso inclui:

- Identificação dos pré-requisitos para usar o armazenamento de Premium.
- Exemplos de implantação do SQL Server em IaaS ao armazenamento de Premium para novas implantações.
- Exemplos de migração implantações existentes, servidores autônomos e implantações usando SQL sempre em grupos de disponibilidade.
- Abordagens de migração possíveis.
- Exemplo de completo ponta a ponta mostrando etapas Azure, Windows e do SQL Server para a migração de uma implementação sempre existente.

Para mais informações de plano de fundo no SQL Server em máquinas virtuais do Azure, consulte [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

**Author:** Pedro Sol **revisores técnicos:** Luís Carlos Vargas Herring, Sanjay Mishra, Pravin Mital, Juergen Santos, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Pré-requisitos para armazenamento de Premium

Há vários pré-requisitos para usar o armazenamento de Premium.

### <a name="machine-size"></a>Tamanho da máquina

Para usar o armazenamento de Premium, você precisará usar série DS máquinas virtuais (VM). Se você não tiver usado máquinas série DS em seu serviço de nuvem antes, você deve excluir a máquina virtual existente, manter os discos anexados e, em seguida, criar um novo serviço de nuvem antes de recriar a máquina virtual como o tamanho de função DS *. Para obter mais informações sobre tamanhos de máquina Virtual, consulte [Máquina Virtual e tamanhos de serviço de nuvem do Azure](virtual-machines-linux-sizes.md).

### <a name="cloud-services"></a>Serviços de nuvem

Você pode usar apenas DS * VMs com armazenamento Premium quando eles são criados em um novo serviço de nuvem. Se você estiver usando o SQL Server sempre ativada no Azure, sempre no ouvinte irá se referir ao endereço Azure internos ou externos IP de Balanceador de carga que está associado um serviço de nuvem. Este artigo aborda como migrar enquanto mantém a disponibilidade neste cenário.

> [AZURE.NOTE] Uma série DS * deve ser a primeira máquina virtual que é implantada para o novo serviço de nuvem.

### <a name="regional-vnets"></a>VNETS regionais

Para DS * VMs, você deve configurar a rede Virtual (VNET) suas VMs para ser regionais de hospedagem. Isso "amplia" o VNET é permitir VMs maiores a ser provisionados em outros clusters e permitir a comunicação entre elas. Captura de tela a seguir, o local realçado mostra VNETs regionais, enquanto o primeiro resultado mostra um VNET "estreito".

![RegionalVNET][1]

Você pode aumentar um tíquete de suporte da Microsoft para migrar para um VNET regional, Microsoft ficará fizer uma alteração, em seguida, para concluir a migração para VNETs regionais, altere a propriedade AffinityGroup na configuração de rede. Primeiro exportar a configuração de rede no PowerShell e substitua a propriedade **AffinityGroup** no elemento **VirtualNetworkSite** com uma propriedade de **local** . Especificar `Location = XXXX` onde `XXXX` é uma região Azure. Importe a nova configuração.

Por exemplo, considerando a configuração de VNET a seguir:

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

Para mover esta para um VNET regional na Europa Ocidental, altere a configuração para o seguinte:

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### <a name="storage-accounts"></a>Contas de armazenamento

Você precisa criar uma nova conta de armazenamento que está configurada para armazenamento de Premium. Observe que o uso do armazenamento de Premium for definido com a conta de armazenamento, não em VHDs individuais, no entanto, ao usar uma série DS * máquina virtual você pode anexar do VHD de contas Premium e armazenamento padrão. Você pode considerar isso se não quiser colocar o VHD SO-lo a conta de armazenamento Premium.

O seguinte comando **New-AzureStorageAccountPowerShell** com o **tipo** de "Premium_LRS" cria uma conta de armazenamento Premium:

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### <a name="vhds-cache-settings"></a>Configurações de Cache de VHDs

A principal diferença entre criando discos que fazem parte de uma conta de armazenamento Premium é a configuração de cache de disco. Para discos de volume de dados do SQL Server é recomendável que você use '**Cache de leitura**'. Para volumes de log de transação, a configuração de cache de disco deve ser definida como '**Nenhum**'. Isso é diferente das recomendações para contas de armazenamento padrão.

Depois que os VHDs foram anexados, a configuração de cache não pode ser alterada. Você precisa desconectar e reconectar o VHD com uma configuração de cache atualizado.

### <a name="windows-storage-spaces"></a>Espaços de armazenamento do Windows

Você pode usar [Espaços de armazenamento do Windows](https://technet.microsoft.com/library/hh831739.aspx) que você fez com armazenamento padrão anterior, isso permitirá que você migrar uma máquina virtual que já está utilizando espaços de armazenamento. O exemplo [Apêndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) (etapa 9 e encaminhar) demonstra o código do Powershell para extrair e importar uma máquina virtual com diversos VHDs anexados.

Pools de armazenamento eram usados com conta de armazenamento do Azure padrão para melhorar a produtividade e reduzir a latência. Você pode encontrar o valor em testes Pools de armazenamento com armazenamento Premium de novas implantações, mas eles adicionar complexidade adicional com a instalação de armazenamento.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Como localizar qual mapa discos virtuais Azure pools de armazenamento

Como existem recomendações de configuração de cache diferentes para VHDs anexados, você pode decidir copiar os VHDs para uma conta de armazenamento Premium. No entanto, quando você reconectá-las para a nova série de DS máquina virtual, talvez seja necessário alterar as configurações de cache. É mais simples aplicar o armazenamento de Premium recomendados configurações de cache quando você tiver VHDs separadas para os arquivos de dados do SQL e arquivos de log (em vez de um único VHD que contém ambos).

> [AZURE.NOTE] Se você tiver arquivos de log e dados do SQL Server no mesmo volume, a opção de cache que você escolher depende dos padrões de acesso IO para suas cargas de trabalho de banco de dados. Somente testes podem demonstrar a qual opção de cache é mais adequada para esse cenário.

No entanto, se você estiver usando espaços de armazenamento do Windows que são compostos de diversos VHDs, você precisará examinar seus scripts originais para identificar quais anexados VHDs são em quais pool específico, assim você pode então definir as configurações de cache adequadamente para cada disco.

Se você não tiver o script original disponível para mostrar que mapeiam os VHDs ao pool de armazenamento, você pode usar as etapas a seguir para determinar o mapeamento de pool de armazenamento/disco.

Para cada disco, use as seguintes etapas:

1. Obter lista de discos anexados à máquina virtual com o comando **Get-AzureVM** :

    Get-AzureVM - ServiceName <servicename> -nome <vmname> | Get-AzureDataDisk

1. Observe o Diskname e LUN.

    ![DisknameAndLUN][2]

1. Área de trabalho remota para a máquina virtual. Vá para **Gerenciamento de computador** | **Gerenciador de dispositivos** | **unidades de disco**. Examinar as propriedades de cada um dos 'Discos virtuais Microsoft'

    ![VirtualDiskProperties][3]

1. O número LUN aqui é uma referência para o número LUN que você especificar ao anexar o VHD para a máquina virtual.
1. Para 'Disco Virtual Microsoft', vá para a guia **detalhes** , em seguida, na lista de **propriedade** , vá para **Driver chave**. No **valor**, observe o **deslocamento**, que é 0002 na seguinte captura de tela. O 0002 indica o PhysicalDisk2 que referencia o pool de armazenamento.

    ![VirtualDiskPropertyDetails][4]

2. Para cada pool de armazenamento, despeje os discos associados:

    Get-StoragePool - nome amigável AMS1pooldata | Get-físico

    ![GetStoragePool][5]

Agora você pode usar essas informações para associar anexados VHDs para discos físicos em Pools de armazenamento.

Depois que você tiver mapeado VHDs para discos físicos em Pools de armazenamento você pode desanexar e copiá-los sobre a uma conta de armazenamento Premium, então anexá-los com a configuração de cache correto. Consulte o exemplo no [Apêndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), as etapas 8 a 12. Estas etapas mostram como extrair uma configuração de disco VHD anexado de máquina virtual para um arquivo CSV, copie os VHDs, altere as configurações de cache de configuração do disco e finalmente reimplantar a máquina virtual como uma série de DS máquina virtual com todos os discos anexados.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Taxa de transferência do VHD armazenamento e largura de banda de armazenamento de máquina virtual

A quantidade de armazenamento desempenho depende o tamanho de DS * VM especificado e os tamanhos VHD. As VMs têm diferentes permitido para o número de VHDs que pode ser anexado e a largura de banda máxima eles oferecerá suporte (MB/s). Para os números de largura de banda específica, consulte [Máquina Virtual e tamanhos de serviço de nuvem do Azure](virtual-machines-linux-sizes.md).

Aumento IOPS são obtidas com tamanhos maiores de disco. Você deve considerar isso quando você pensar seu caminho de migração. Para obter detalhes, [consulte a tabela para IOPS e tipos de disco](../storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage).

Por fim, considere a possibilidade de que VMs tenham larguras de outro disco máximo oferecer suporte para todos os discos anexados. Sob carga alta, você poderia saturar a largura de banda de disco máximo disponível para esse tamanho de função de máquina virtual. Por exemplo, um Standard_DS14 oferecerá suporte até 512 MB/s; Portanto, com três discos P30 você poderia saturar a largura de banda do disco da máquina virtual. Mas, neste exemplo, o limite de produtividade poderia ser excedido dependendo a combinação de leitura e gravação IOs.

## <a name="new-deployments"></a>Novas implantações

As duas próximas seções demonstram como você pode implantar o SQL Server VMs ao armazenamento de Premium. Conforme mencionado anteriormente, você não precisa necessariamente colocar o disco de sistema operacional para armazenamento de Premium. Você pode optar por fazer isso se pretende colocar qualquer cargas de trabalho de IO intenso no sistema operacional VHD.

O primeiro exemplo demonstra utilizando imagens de galeria do Azure existentes. O segundo exemplo mostra como usar uma imagem de máquina virtual personalizada em uma conta existente do armazenamento padrão.

> [AZURE.NOTE] Estes exemplos pressupõem que você já tiver criado um VNET regionais.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Criar uma nova VM com armazenamento Premium com imagem da Galeria

O exemplo a seguir mostra como colocar o VHD do sistema operacional para armazenamento de premium e anexar VHDs de armazenamento Premium. No entanto, você pode também posicionar o disco de sistema operacional em uma conta de armazenamento padrão e anexar VHDs que residem em uma conta de armazenamento Premium. Os dois cenários são demonstrou.

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### <a name="step-1-create-a-premium-storage-account"></a>Etapa 1: Criar uma conta de armazenamento Premium


    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### <a name="step-2-create-a-new-cloud-service"></a>Etapa 2: Criar um novo serviço de nuvem

    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Etapa 3: Reservar um VIP de serviço de nuvem (opcional)
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### <a name="step-4-create-a-vm-container"></a>Etapa 4: Criar um contêiner de máquina virtual
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Etapa 5: Colocando o sistema operacional VHD em padrão ou Premium armazenamento
    #NOTE: Set up subscription and default storage account which will be used to place the OS VHD in

    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams"

    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### <a name="step-6-create-vm"></a>Etapa 6: Criar máquina virtual
    #Get list of available SQL Server Images from the Azure Image Gallery.
    $galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
    $image = $galleryImage.ImageName

    #Set up Machine Specific Information
    $vmName = "dsDan1"
    $vnet = "dansvnetwesteur"
    $subnet = "SQL"
    $ipaddr = "192.168.0.8"

    #Remember to change to DS series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "mycomplexpwd4*"

    #Create VM Config
    $vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Add Data and Log Disks to VM Config
    #Note the size specified ‘-DiskSizeInGB 1023’, this will attach 2 x P30 Premium Storage Disk Type
    #Utilising the Premium Storage enabled Storage account

    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

    #Create VM
    $vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

    #Add RDP Endpoint
    $EndpointNameRDPInt = "3389"
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

    #Check VHD storage account, these should be in $newxiostorageaccountname
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk


### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Criar uma nova VM para usar o armazenamento de Premium com uma imagem personalizada

Este cenário demonstra onde você tem imagens personalizadas existentes que residem em uma conta de armazenamento padrão. Conforme mencionado se você deseja colocar o VHD SO Premium armazenamento você precisará copiar a imagem que existe na conta de armazenamento padrão e transferi-las para um armazenamento Premium antes que ela possa ser usada. Se você tiver uma imagem local, você também pode usar esse método para copiar que diretamente para a conta de armazenamento Premium.

#### <a name="step-1-create-storage-account"></a>Etapa 1: Criar a conta de armazenamento
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### <a name="step-2-create-cloud-service"></a>Etapa 2 criar serviço de nuvem
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-use-existing-image"></a>Etapa 3: Usar a imagem existente
Você pode usar uma imagem existente. Ou, você pode [capturar uma imagem de uma máquina existente](virtual-machines-windows-classic-capture-image.md). Observe a imagem que você não precisa ser DS* de máquina máquina. Depois que você tenha a imagem, as etapas a seguir mostram como copiá-lo para a conta de armazenamento de Premium com o * *Início-AzureStorageBlobCopy** PowerShell commandlet.

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Etapa 4: Copiar Blob entre contas de armazenamento
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### <a name="step-5-regularly-check-copy-status"></a>Etapa 5: Verificar regularmente o status de cópia:
    $blob | Get-AzureStorageBlobCopyState

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Etapa 6: Adicionar disco de imagem no disco Azure repositório na assinatura
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [AZURE.NOTE] Você descobrirá que, apesar dos relatórios de status como sucesso, você pode obter um erro de concessão de disco. Nesse caso, aguarde cerca de 10 minutos.

#### <a name="step-7--build-the-vm"></a>Etapa 7: Construir a máquina virtual
Aqui você estiver criando a máquina virtual da sua imagem e anexar dois VHDs de armazenamento Premium:

    $newimageName = "prem"+"dansoldonorsql2k14"
    #Set up Machine Specific Information
    $vmName = "dansolchild"
    $vnet = "westeur"
    $subnet = "Clients"
    $ipaddr = "192.168.0.41"

    #This will need to be a new cloud service
    $destcloudsvc = "danregsvcamsxio2"

    #Use to DS Series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS3"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "theM)stC0mplexP@ssw0rd!”


    #Create VM Config
    $vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



    $vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Implantações existentes que não usam sempre em grupos de disponibilidade

> [AZURE.NOTE] Para implantações existentes, primeiro consulte a seção [pré-requisitos](#prerequisites-for-premium-storage) deste tópico.

Existem diferentes considerações para implantações do SQL Server que não usam sempre em grupos de disponibilidade e aqueles que fazer. Se você não estiver usando sempre no e tiver um autônomo existente do SQL Server, você pode atualizar para Premium armazenamento usando uma nova conta de armazenamento e serviços de nuvem. Considere as seguintes opções:

- **Criar uma nova VM servidor SQL**. Você pode criar uma nova VM do servidor SQL que usa uma conta de armazenamento Premium, conforme documentadas em implantações de novo. Em seguida, backup e restauração seus bancos de dados do SQL Server configuração e usuário. O aplicativo precisa ser atualizado para fazer referência a novo SQL Server se ela estiver sendo acessada interna ou externamente. Você precisa copiar todos os objetos 'fora db' como se você estava fazendo uma migração do lado a lado (SxS) SQL Server. Isso inclui objetos como logon, certificados e servidores vinculados.
- **Migrar uma VM de servidor SQL existente**. Isso exigirá levando a máquina de virtual do SQL Server offline e, em seguida, transferi-la para um novo serviço de nuvem, que inclui copiar todos seus VHDs anexados à conta de armazenamento Premium. Quando a máquina virtual for colocado online, o aplicativo fará referência o nome de host do servidor como antes. Lembre-se de que o tamanho do disco existente afetará as características de desempenho. Por exemplo, um disco de 400 GB é arredondado para cima uma P20. Se você souber que você não exigem que desempenho de disco, em seguida, você pode recriar a máquina virtual como uma série de DS máquina virtual e anexar VHDs de armazenamento Premium da especificação de tamanho/desempenho que você exige. Em seguida, você pode desconectar e reconectar os arquivos de banco de dados do SQL.

> [AZURE.NOTE] Quando cópia de discos VHD você deve estar ciente do tamanho, dependendo do tamanho significará que tipo de disco de armazenamento Premium que eles se encaixam, isso determina a especificação de desempenho de disco. Azure será arredondar para cima até o disco mais próximo tamanho, portanto se você tiver um disco de 400 GB, isso será arredondado para um P20. Dependendo das suas necessidades de IO existentes do VHD sistema operacional, talvez não precisar migrar isso para uma conta de armazenamento Premium.

Se o SQL Server for acessado externamente, o VIP de serviço de nuvem mudará. Você também terá a pontos de extremidade de atualização, ACLs e DNS configurações.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Implantações existentes que utilizam sempre em grupos de disponibilidade

> [AZURE.NOTE] Para implantações existentes, primeiro consulte a seção [pré-requisitos](#prerequisites-for-premium-storage) deste tópico.

Inicialmente nesta seção, veremos como sempre interage com a rede do Azure. Nós será subdividir migrações em dois cenários: migrações onde algum tempo de inatividade pode ser tolerado e migrações onde você deve obter o tempo de inatividade mínimo.

Local SQL Server sempre na disponibilidade grupos usar um ouvinte no local que registra um nome DNS virtual juntamente com um endereço IP que é compartilhado entre um ou mais servidores de SQL. Quando os clientes se conectam eles serão roteados através do IP de ouvinte para o SQL Server principal. Este é o servidor que possui o recurso sempre IP no momento.

![DeploymentsUseAlways em][6]

No Microsoft Azure que você pode ter apenas um endereço IP atribuído a uma NIC na máquina virtual, portanto para alcançar a mesma camada de abstração como local, Azure utiliza o endereço IP que é atribuído aos balanceadores de carga interna/externa (ILB/ELB). O recurso IP que é compartilhado entre os servidores está definido para o mesmo IP como o ILB/ELB. Isso é publicado no DNS e o tráfego de cliente é passado o ILB/ELB para a réplica do SQL Server principal. O ILB/ELB sabe qual servidor SQL está primária, já que ele usa testes investigar o recurso sempre IP. No exemplo anterior, ele testes cada nó que possui um ponto de extremidade referenciado pela ELB/ILB, aquele que responde é o SQL Server principal.

> [AZURE.NOTE] A ILB e ELB são atribuídas a um serviço de nuvem Azure específico, portanto qualquer migração de nuvem no Azure provavelmente significará que o IP de Balanceador de carga será alterado.

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrando sempre em implantações que podem permitir que algum tempo de inatividade

Há duas estratégias para migrar sempre em implantações que permitem algum tempo de inatividade:

1. **Adicionar mais réplicas secundárias a um sempre em Cluster existente**
1. **Migrar para um novo sempre em Cluster**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. adicionar mais réplicas secundário a um sempre em Cluster existente

Uma estratégia é adicionar mais secundários a sempre no grupo de disponibilidade. Você precisa adicioná-los em um novo serviço de nuvem e atualizar o ouvinte com o novo IP de Balanceador de carga.

##### <a name="points-of-downtime"></a>Pontos de inatividade:

- Validação de cluster.
- Testando failovers sempre na nova secundários.

Se você estiver usando Pools de armazenamento do Windows dentro a máquina virtual para maior produtividade de IO, e em seguida, eles serão colocados offline durante uma validação de Cluster completa. O teste de validação é necessário quando você adiciona nós ao cluster. O tempo necessário para executar o teste pode variar, portanto você deve testar isso em seu ambiente de teste de representante para obter um tempo aproximado de quanto tempo isso levará.

Você deve provisionar tempo onde você pode executar failover manual e caos testes em nós recém-adicionado para garantir que sempre em funções de alta disponibilidade como esperado.

![DeploymentUseAlways On2][7]

> [AZURE.NOTE] Você deve interromper todas as instâncias do SQL Server onde os Pools de armazenamento são usados antes do executado de validação.
##### <a name="high-level-steps"></a>Etapas de alto nível

1. Crie dois novos servidores de SQL no novo serviço de nuvem com armazenamento Premium conectado.
1. Copiar sobre backups completos e restaurar com **NORECOVERY**.
1. Copie sobre 'Sair do usuário DB' objetos dependentes, como logon etc.
1. Criar novo um novo interno carga balanceador (ILB) ou use um balanceador de carga externos (ELB) e configurar pontos de extremidade de balanceamento de carga em ambos os nós de novo.
> [AZURE.NOTE] Verificar que todos os nós têm a configuração de ponto de extremidade correta antes de continuar

1. Impedir o acesso de usuário/do aplicativo para o SQL Server (se usando Pools de armazenamento).
1. Pare serviços de mecanismo do SQL Server em todos os nós (se usando Pools de armazenamento).
1. Adicione novos nós para cluster e executar validação completa.
1. Quando a validação for bem-sucedida, começar todos os serviços do SQL Server.
1. Logs de transação de backup e restaurar bancos de dados do usuário.
1. Adicione novos nós em sempre no grupo de disponibilidade e coloque replicação em **síncrono**.
1. Adicione o recurso de endereço IP do novo nuvem serviço ILB/ELB através do PowerShell para sempre em com base no exemplo vários local no [Apêndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage). No Windows clusters, defina os **possíveis proprietários** do recurso **Endereço IP** para os novos nós antigos. Consulte a seção 'Adicionar recurso de endereço IP na mesma sub-rede' do [Apêndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).
1. Failover para um dos novos nós.
1. Verifique os novos nós failovers de teste e parceiros de Failover automático.
1. Remova nós originais do grupo de disponibilidade.

##### <a name="advantages"></a>Vantagens

- Novos servidores SQL podem ser testada (SQL Server e aplicativo) antes que eles são adicionados à sempre ligado.
- Você pode alterar o tamanho de máquina virtual e personalizar o armazenamento com suas necessidades exatas. No entanto, seria útil manter todos os caminhos de arquivo SQL a mesma.
- Você pode controlar quando a transferência dos backups DB às réplicas secundário são iniciados. Isso difere do utilizando commandlet Azure **Início-AzureStorageBlobCopy** para copiar VHDs, porque é uma cópia assíncrona.

##### <a name="disadvantages"></a>Desvantagens
- Ao usar Pools de armazenamento do Windows, há tempo de inatividade de Cluster durante a validação de Cluster completo para os novos nós adicionais.
- Dependendo da versão do SQL Server e o número existente de réplicas secundários, você não poderá adicionar mais réplicas secundárias sem remover secundários existentes.
- Pode haver muito tempo de transferência de dados SQL ao configurar secundários.
- Há custo adicional durante a migração, enquanto você tiver novas máquinas executando em paralelo.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. migrar para um novo sempre em Cluster

Outra estratégia é criar uma nova sempre em Cluster conosco novas no novo serviço de nuvem e redirecionar os clientes para usá-lo.

##### <a name="points-of-downtime"></a>Pontos de inatividade

Não há tempo de inatividade ao transferir aplicativos e usuários para o novo ouvinte sempre. O tempo de inatividade depende:

- O tempo necessário para restaurar backups de log de transação final para bancos de dados em servidores de novo.
- O tempo necessário para atualizar aplicativos cliente para usar o novo sempre no ouvinte.

##### <a name="advantages"></a>Vantagens

- Você pode testar o ambiente de produção real, SQL Server, e as alterações de compilação do sistema operacional.
- Você tem a opção para personalizar o armazenamento e reduzir o tamanho da máquina virtual. Isso pode resultar em redução de custos.
- Você pode atualizar seu compilação do SQL Server ou a versão durante este processo. Você também pode atualizar o sistema operacional.
- O anterior sempre em Cluster pode atuar como um destino de reversão sólida.

##### <a name="disadvantages"></a>Desvantagens

- Você precisa alterar o nome DNS do ouvinte se quiser que os dois clusters sempre em execução simultaneamente. Isso adiciona administração sobrecarga durante a migração, como cadeias de caracteres de aplicativo cliente devem refletir o novo nome de ouvinte.
- Você deve implementar um mecanismo de sincronização entre os dois ambientes para mantê-los como fechar possível minimizar os requisitos de sincronização final antes da migração.
- Lá é adicionada custo durante a migração enquanto você tem o novo ambiente em execução.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrando sempre em implantações do tempo de inatividade mínimo

Há duas estratégias para implantações de sempre migrando para o tempo de inatividade mínimo:

1. **Utilizar um secundário existente: Site único**
1. **Utilizar réplicas secundárias existentes: vários locais**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. utilizar um secundário existente: Site único

Uma estratégia de tempo de inatividade mínimo é para fazer uma nuvem existente secundário e removê-lo do serviço de nuvem atual. Em seguida, copie os VHDs para a nova conta de armazenamento Premium e criar a máquina virtual no novo serviço de nuvem. Em seguida, atualize o ouvinte em clusters e failover.

##### <a name="points-of-downtime"></a>Pontos de inatividade

- Não há tempo de inatividade quando você atualiza o nó final com o ponto de extremidade de balanceamento de carga.
- Sua reconexão do cliente pode ser atrasada dependendo da configuração cliente/DNS.
- Não há tempo de inatividade adicional se você optar por tomar grupo sempre em Cluster offline para trocar os endereços IP. Você pode evitar isso usando uma dependência ou e possíveis proprietários para o recurso de endereço IP adicionado. Consulte a seção 'Adicionar recurso de endereço IP na mesma sub-rede' do [Apêndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).

> [AZURE.NOTE] Quando você quiser o nó adicionado a partake em como sempre em Failover parceiro, você precisa adicionar um ponto de extremidade do Azure com uma referência a carga equilibrada definir. Quando você executa o comando **Add-AzureEndpoint** para fazer isso, conexões atuais permaneça abertos, mas novas conexões para o ouvinte não poderão ser estabelecido balanceador de carga foi atualizado. Nos testes isso foi visto últimos 90-120seconds, isso deve ser testado.

##### <a name="advantages"></a>Vantagens

- Sem extra custos incorridos durante a migração.
- Uma migração individual.
- Complexidade reduzida.
- Permite IOPS aumento da SKUs de armazenamento Premium. Quando os discos são desconectados da máquina virtual e copiados para o novo serviço de nuvem, um 3º terceiros ferramenta pode ser usada para aumentar o tamanho do VHD, que fornece maior produtividade. Para aumentar tamanhos VHD, consulte esta [discussão de fórum](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Desvantagens

- Há uma perda temporária de HA e DR durante a migração.
- Como isso é uma migração de 1:1, você terá que usar um tamanho mínimo de máquina virtual que será suporte para seu número de VHDs, portanto, talvez não seja capaz de downsize suas VMs.
- Este cenário usaria commandlet Azure **Início-AzureStorageBlobCopy** , que é assíncrona. Não há nenhum SLA na conclusão de cópia. O tempo das cópias varia, enquanto isso depende de espera na fila também dependerá da quantidade de dados para transferir. O tempo de cópia aumenta se a transferência for outro Azure data center que suporta o armazenamento de Premium em outra região. Se você tiver apenas 2 nós, considere uma atenuação possível caso a cópia demora mais do que em testes. Isso pode incluir as seguintes ideias.
    - Adicione um nó de SQL Server 3º temporário para HA antes da migração com tempo de inatividade combinado.
    - Execute a migração fora do Azure manutenção agendada.
    - Certifique-se de que você configurou sua quorum de cluster corretamente.  

##### <a name="high-level-steps"></a>Etapas de alto nível

Este documento não demonstram um exemplo de ponta a ponta completo, porém [Apêndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) fornece detalhes que podem ser utilizados para fazer isso.

![MinimalDowntime][8]

- Coletar configuração do disco e remover o nó (não exclua VHDs anexados).
- Crie uma conta de armazenamento Premium e copie VHDs da conta de armazenamento padrão
- Criar novo serviço de nuvem e reimplantar a máquina virtual de SQL2 em desse serviço de nuvem. Crie a máquina virtual usando copiada SO VDH original e anexar os VHDs copiados.
- Configurar ILB / ELB e adicionar pontos de extremidade.
- Atualize o ouvinte por qualquer um:
    - Fazendo sempre no grupo offline e atualizando o sempre no ouvinte com novos ILB / endereço IP ELB.
    - Ou, adicionando o recurso de endereço IP do novo nuvem serviço ILB/ELB através do PowerShell em clusters do Windows. Em seguida, defina os possíveis proprietários do recurso endereço IP para o nó migrado, SQL2 e isso como dependência ou no nome da rede. Consulte a seção 'Adicionar recurso de endereço IP na mesma sub-rede' do [Apêndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).
- Verificar a configuração de DNS/propagação para os clientes.
- Migrar máquina virtual SQL1 e percorrer as etapas 2 a 4.
- Se usando etapas 5ii, em seguida, adicione SQL1 como um possível proprietário para o recurso de endereço IP adicionado
- Teste failovers.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. utilizar réplicas secundárias existentes: vários locais

Se você tiver nós em mais de um data center Azure (DC) ou se você tiver um ambiente híbrido, em seguida, você pode usar uma configuração sempre nesse ambiente para minimizar o tempo de inatividade.

A abordagem é alterar a sincronização sempre para síncrono para o local ou DC secundário do Azure e failover ao longo do SQL Server. Em seguida, copie os VHDs para uma conta de armazenamento Premium e reimplantar máquina em um novo serviço de nuvem. Atualize o ouvinte e exibem-o novamente.

##### <a name="points-of-downtime"></a>Pontos de inatividade

O tempo de inatividade consiste o tempo de failover para o alternativo DC e vice-versa. Ele também depende sua configuração de clientes para o DNS e sua reconexão do cliente pode ser atrasada.
Considere o seguinte exemplo de uma configuração híbrida sempre em:

![MultiSite1][9]

##### <a name="advantages"></a>Vantagens

- Você pode utilizar infraestrutura existente.
- Você tem a opção de pré-atualização o armazenamento do Azure no DC DR Azure primeiro.
- Pode ser necessário reconfigurar o armazenamento do Azure DR DC.
- Há um mínimo de dois failovers durante a migração, excluindo failovers de teste.
- Você não precisa mover dados do SQL Server com backup e restauração.

##### <a name="disadvantages"></a>Desvantagens

- Dependendo de acesso para cliente para o SQL Server, pode haver maior latência quando SQL Server é executado em um DC alternativo para o aplicativo.
- O tempo de cópia de VHDs ao armazenamento de Premium pode ser longo. Isso pode afetar sua decisão de manter o nó do grupo de disponibilidade. Considere isso para quando o trabalho intenso log cargas estão sendo executados durante a migração é necessário, pois o nó primário terão que manter as transações não-duplicadas em seu log de transação. Portanto, isso pode aumentar significativamente.
- Este cenário usaria commandlet Azure **Início-AzureStorageBlobCopy** , que é assíncrona. Não há nenhum SLA após a conclusão. O tempo das cópias varia, enquanto isso depende de espera na fila, ele também dependerá da quantidade de dados para transferir. Portanto você ter apenas um nó em seu data center 2º, você deve tomar medidas de atenuação caso a cópia demora mais do que em testes. Isso pode incluir as seguintes ideias.
    - Adicione um nó SQL 2a temporário para HA antes da migração com tempo de inatividade combinado.
    - Execute a migração fora do Azure manutenção agendada.
    - Certifique-se de que você configurou sua quorum de cluster corretamente.

Neste cenário supõe que você tenha documentadas sua instalação e sabe como o armazenamento é mapeado para fazer alterações ideais para configurações de cache.

##### <a name="high-level-steps"></a>Etapas de alto nível
![Multisite2][10]

- Verifique o local / alternativo DC Azure SQL Server primária e torná-la a outro automático Failover parceiro (AFP).
- Reunir informações de configuração de disco do SQL2 e remover o nó (não exclua VHDs anexados).
- Crie uma conta de armazenamento Premium e copie VHDs da conta de armazenamento padrão.
- Criar um novo serviço de nuvem e crie a máquina virtual SQL2 com seus discos de armazenamento de prêmios anexados.
- Configurar ILB / ELB e adicionar pontos de extremidade.
- Atualize o sempre no ouvinte com novos ILB / failover de teste e endereço IP ELB.
- Verifique a configuração de DNS.
- Alterar o AFP para SQL2 e, em seguida, migrar SQL1 e percorrer as etapas 2 a 5.
- Teste failovers.
- Alterne o AFP para SQL1 e SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Apêndice: Migrando um multissite sempre em Cluster ao armazenamento de Premium

O resto deste tópico fornece um exemplo detalhado da conversão sempre em um cluster de vários local para o armazenamento de Premium. Ele também converte o ouvinte de usando um balanceador externo (ELB) para um balanceador de carga interno (ILB).

### <a name="environment"></a>Ambiente

- Windows 2k 12 / SQL 2k 12
- Arquivos de banco de dados 1 no SP
- 2x Pools de armazenamento por nó

![Appendix1][11]

### <a name="vm"></a>MÁQUINA VIRTUAL:

Neste exemplo, vamos demonstrar a movimentação de um ELB para ILB. ELB estava disponível antes de ILB, então isso mostra como alternar para isso durante a migração.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Etapas de pré: Conectar-se à assinatura

    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Etapa 1: Criar nova conta de armazenamento e serviço em nuvem
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Storage accounts
    #current storage account where the vm to migrate resides
    $origstorageaccountname = "danstdams"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Generate storage keys for later
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Generate storage acc contexts
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #CREATE NEW CLOUD SVC
    $vnet = "dansvnetwesteur"

    ##Existing cloud service
    $sourceSvc="dansolSrcAms"

    ##Create new cloud service
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Etapa 2: Aumente as falhas permitidas em recursos<Optional>
Em determinados recursos que fazem parte de sua sempre no grupo de disponibilidade há limites em quantas falhas que podem ocorrer em um período, onde o serviço de cluster tentará reiniciar o grupo de recursos. É recomendável que aumentar esse enquanto você está examinando neste procedimento, desde se não estiver manualmente failovers failover e disparador desligando máquinas que você pode obter perto esse limite.

Seria recomendável dobrar a reserva de falha, para fazer isso no Gerenciador de Cluster de Failover, vá para as propriedades do grupo de recursos sempre em:

![Appendix3][13]

Altere as falhas máximos para 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Etapa 3: Recurso de endereço de IP de adição para o grupo de Cluster<Optional>

Se você tiver apenas um endereço IP para o grupo de Cluster e isso é alinhado à sub-rede nuvem, lembre-se, se você acidentalmente coloca offline todos os nós de cluster na nuvem, na rede, em seguida, o recurso de IP de Cluster e nome de rede de Cluster não será capaz de ficar online. Em caso isso impedirá as atualizações para outros recursos de cluster.

#### <a name="step-4-dns-configuration"></a>Etapa 4: Configuração de DNS

Implementar um suave transição depende de como o DNS está sendo utilizado e atualizado.
Quando sempre é instalado, ele cria um grupo de recursos de Cluster do Windows, se você abrir o Gerenciador de Cluster de Failover, você verá que no mínimo, ele terá três recursos, os dois que o documento se refere a são:

- Nome de rede virtual (VNN) – Este é o nome DNS que o cliente se conectar ao quando desejam se conectar aos servidores do SQL por meio do sempre.
- Recurso de endereço IP – este é o endereço IP que associados a VNN, você pode ter mais de um e em uma configuração multissite terá um endereço IP por site/sub-rede.

Quando se conectando ao SQL Server, o SQL Server Client driver recuperará os registros DNS associados o ouvinte e tentar se conectar a cada sempre em associada endereço IP, abaixo discutimos alguns fatores que podem influenciar isso.

O número de registros DNS simultâneos que são associadas com o nome do ouvinte não depende apenas o número de endereços IP associados, mas a ' RegisterAllIpProviders'setting no cluster de Failover para o recurso sempre Diante VNN.

Quando você implanta sempre ativada no Azure há etapas diferentes para criar o ouvinte e endereços IP, você precisa configurar manualmente a 'RegisterAllIpProviders' 1, isso é diferente para um local em sempre na implantação onde ele já está definido como 1.

Se 'RegisterAllIpProviders' for 0, então você só verá um registro DNS no DNS associado com o ouvinte:

![Appendix4][14]

Se 'RegisterAllIpProviders' for 1:

![Appendix5][15]

O código abaixo despejar as configurações de VNN e defini-lo para você, observe que, para que a alteração seja efetivada, você precisará colocar o VNN offline e ativá-lo online novamente, este levando o ouvinte offline causando interrupção de conectividade de cliente.

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

Em uma etapa de migração posterior, você precisará atualizar o ouvinte sempre com um endereço IP atualizado que fazem referência a um balanceador de carga, isso envolve um remoção de recurso de endereço IP e adição. Após a atualização IP, você precisa garantir que o novo endereço IP foi atualizado na zona DNS e que os clientes estão atualizando seu cache DNS local.

Se seus clientes residem em um segmento de rede diferente e fazer referência a um servidor DNS diferente, você precisa considerar o que acontece sobre transferência de zona DNS durante a migração, como o aplicativo se reconectar tempo será restrita pelo menos o tempo de transferência de zona de qualquer novos endereços IP para o ouvinte. Se você estiver em restrição de tempo aqui, você deve discutir testar forçar uma transferência de zona incremental com suas equipes de Windows e também colocar o registro de host DNS para um menor tempo de vida (TTL), portanto os clientes atualizarem. Para obter mais informações, consulte [Transferências de zona incrementais](https://technet.microsoft.com/library/cc958973.aspx) e [Início-DnsServerZoneTransfer](https://technet.microsoft.com/library/jj649917.aspx).

Por padrão o TTL do registro DNS que está associado com o ouvinte no sempre no Azure é 1200 segundos. Talvez você queira reduzir isso se você estiver em horário de restrição durante a migração para garantir que os clientes atualize seus DNS com o endereço IP atualizado para o ouvinte. Você pode ver e modificar a configuração por despejar a configuração do VNN:

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

Observe, menor o 'HostRecordTTL,' ocorrerá uma quantidade maior de tráfego DNS.

##### <a name="client-application-settings"></a>Configurações de aplicativo do cliente

Se seu aplicativo de cliente SQL tem suporte para .net 4,5 SQLClient, em seguida, você pode usar ' MULTISUBNETFAILOVER = TRUE' palavra-chave, isso é recomendado seja aplicada porque permite a conexão mais rápido para SQL sempre no grupo de disponibilidade durante tolerância a falhas. Ele enumera através de todos os endereços IP associados com o ouvinte sempre em paralelo e executa uma velocidade de repetição de conexão TCP mais curtas durante um failover.

Para obter mais informações sobre as configurações acima, consulte [MultiSubnetFailover palavra-chave e recursos associados](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Consulte também [SqlClient suporte para alta disponibilidade, recuperação de dados](https://msdn.microsoft.com/library/hh205662(v=vs.110).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Etapa 5: Configurações de quorum de Cluster

Como você pretende ser fazendo check-out pelo menos um SQL Server para baixo uma vez, você deve modificar a configuração de quorum de cluster, se usando Testemunho de compartilhamento de arquivo (FSW) com 2 nós, você deve definir o quorum para permitir que a maioria de nós e utilizar votação dinâmico e isso é para permitir um único nó permaneça colocada.


    Set-ClusterQuorum -NodeMajority  

Para obter mais informações sobre como gerenciar e configurar o quorum do cluster, consulte [Configurar e gerenciar o Quorum em um Cluster de Failover do Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Etapa 6: Extrair ACLs e pontos de extremidade existentes
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the Always On Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

Salve esses arquivos para um arquivo de texto.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Etapa 7: Alterar parceiros de Failover e modos de replicação

Se você tiver mais de 2 servidores SQL, você deve alterar o failover do outro secundário no outro DC ou local para 'Síncrono' e torná-lo um parceiro de Failover automática (AFP), isso é para manter o HA enquanto você está fazendo alterações. Você pode fazer isso por meio de TSQL de modificar apesar SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Etapa 8: Remover máquina virtual secundário do serviço de nuvem

Você deve ser planejando migrar de um nó secundário nuvem primeiro, se isso for atualmente principal, você deve iniciar um failover manual.

    $vmNameToMigrate="dansqlams2"

    #Check machine status
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Shutdown secondary VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


    #Extract disk configuration

    ##Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk config, make sure below returns the disks associated with the VM
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machibe is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Etapa 9: Alterar as configurações no arquivo CSV de cache de disco e salvar

Para volumes de dados esses devem ser definidas como somente leitura.

Para volumes TLOG esses devem ser definidas como nenhum.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Etapa 10: Copiar VHDS
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContext

    ####DISK COPYING####
    #Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
       }



Você pode verificar o status de cópia dos VHDs para a conta de armazenamento Premium:

    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix8][18]

Aguarde até que todos esses são registrados como sucesso.

Para obter informações para blobs individuais:

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### <a name="step-11-register-os-disk"></a>Etapa 11: Disco de registro SO

    #Change storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Etapa 12: Importar secundário para o novo serviço de nuvem

O código a seguir também usa a opção extra aqui você pode importar máquina e usar o VIP devem ser retido.

    #Build VM Config
    $ipaddr = "192.168.0.5"
    #Remember to change to XIO
    $newInstanceSize = "Standard_DS13"
    $subnet = "SQL"

    #Create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Etapa 13: Criar ILB no novo Svc de nuvem, adicione carga equilibrada pontos de extremidade e ACLs
    #Check for existing ILB
    GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

    $ilb="sqlIntIlbDest"
    $subnet = "SQL"
    $IP="192.168.0.25"
    Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

    #SET Azure ACLs or Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

    ####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####

####<a name="step-14-update-always-on"></a>Etapa 14: Sempre atualizar em
    #Code to be executed on a Cluster Node
    $ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
    $newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"


    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

    #set dependancy and NETBIOS, then remove old IP address

    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

    #Make sure no static records in DNS

![Appendix9][19]

Agora, remova o serviço de nuvem antigo endereço IP.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Etapa 15: Verificação de atualizações de DNS

Agora, você deve verificar servidores DNS em suas redes de cliente do SQL Server e certificar-se de que o agrupamento adicionou o registro de host adicional para o endereço IP adicionado. Se os servidores DNS não atualizados, considere forçar uma transferência de zona DNS e certifique-se de que os clientes de lá sub-rede são capazes de resolver para os dois sempre em endereços IP, que é então você não precisa esperar automática replicação de DNS.

#### <a name="step-16-reconfigure-always-on"></a>Etapa 16: Reconfigurar o sempre

Neste ponto você aguardar secundário nó que foi migrado para totalmente sincronizar com o nó no local e alterne para o nó replicação síncrono e torná-la a AFP.  

#### <a name="step-17-migrate-second-node"></a>Etapa 17: Migrar segundo nó
    $vmNameToMigrate="dansqlams1"

    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Get endpoint information
    $endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

    #Shutdown VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

    #Get disk config

    #Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk configuration
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machine is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Etapa 18: Alterar as configurações no arquivo CSV de cache de disco e salvar

Para volumes de dados esses devem ser definidas como somente leitura.

Para volumes TLOG esses devem ser definidas como nenhum.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Etapa 19: Criar nova conta de armazenamento independentes para nó secundário
    $newxiostorageaccountnamenode2 = "danspremsams2"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

    #Reset the storage account src if node 1 in a different storage account
    $origstorageaccountname2nd = "danstdams2"

    #Generate storage keys for later
    $xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

    #Generate storage acc contexts
    $xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

#### <a name="step-20-copy-vhds"></a>Etapa 20: Copiar VHDS
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

    ####DISK COPYING####
    ##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
        -SrcContext $origContext `
        -DestContainer $containerName `
        -DestBlob $vhdname `
        -DestContext $xioContextnode2
       }

    #Check for copy progress

    #check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext


Você pode verificar o status de cópia VHD para todos os VHDs: ForEach ($disk em $diskobjects) {$lun = $disk. LUN $vhdname = $disk.vhdname $cacheoption = $disk. HostCaching $disklabel = $disk. DiskLabel $diskName = $disk. DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix12][22]

Aguarde até que todos esses são registrados como sucesso.

Para obter informações para blobs individuais: status de blob #Check induvidual Get-AzureStorageBlobCopyState-Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd"-contêiner $containerName-contexto $xioContextnode2

#### <a name="step-21-register-os-disk"></a>Etapa 21: Disco de registro SO
    #change storage account to the new XIO storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

    #Build VM Config
    $ipaddr = "192.168.0.4"
    $newInstanceSize = "Standard_DS13"

    #Join to existing Avaiability Set

    #Build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Etapa 22: Adicionar carga equilibrada pontos de extremidade e ACLs
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


    #STOP!!! CHECK in the Azure classic portal or Machine Endpoints through powershell that these Endpoints are created!

    #SET ACLs or Azure Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### <a name="step-23-test-failover"></a>Etapa 23: Teste failover

Agora você deve deixar o nó migrado sincronizar com o nó de sempre no local, colocá-lo no modo de replicação síncrono e aguarde até que ele é sincronizado. Em seguida, failover de local para o primeiro nó migradas, que é o AFP. Uma vez que trabalhou, altere o último nó migrado para o AFP.

Você deve testar failovers entre todos os nós e execute embora caos testes para garantir failovers funciona como esperado e em uma manor em tempo hábil.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Etapa 24: Colocar novamente as configurações de quorum de cluster / TTL do DNS / Failover Pntrs / as configurações de sincronização
##### <a name="adding-ip-address-resource-on-same-subnet"></a>Adicionar o recurso de endereço IP na mesma sub-rede

Se você tiver apenas 2 SQL Servers e deseja migrá-los para um novo serviço de nuvem, mas deseja mantê-las na mesma sub-rede, você pode evitar levando o ouvinte offline para excluir o sempre no endereço IP original e adicionar o novo endereço IP. Se você estiver migrando as VMs para outra sub-rede não precisará fazer isso como haverá uma rede de clusters adicionais que fazem referência a sub-rede.

Depois levados secundário migrado e adicionados ao novo recurso de endereço IP para o novo serviço de nuvem antes do failover primária existente, você deve levar essas etapas no Gerenciador de Failover de Cluster:

Para adicionar em endereço IP, consulte o [Apêndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), etapa 14.

1. Para o recurso de endereço IP atual, altere o proprietário possível 'Primária SQL Server existente', no exemplo abaixo, 'dansqlams4':

    ![Appendix13][23]

1. Para o novo recurso de endereço IP, altere o proprietário possível 'Migrated secundário SQL Server', no exemplo abaixo, 'dansqlams5':

    ![Appendix14][24]

1. Após esta configuração você pode failover e quando o último nó é migrado os possíveis proprietários devem ser editados para que esse nó é adicionado como um possível proprietário:

    ![Appendix15][25]

## <a name="additional-resources"></a>Recursos adicionais
- [Armazenamento do Azure Premium](../storage/storage-premium-storage.md)
- [Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/)
- [SQL Server no Azure máquinas virtuais](virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
