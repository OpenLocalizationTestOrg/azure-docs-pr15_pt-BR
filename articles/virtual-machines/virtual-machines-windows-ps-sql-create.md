<properties
    pageTitle="Criar uma máquina Virtual do servidor SQL no Azure PowerShell (Gerenciador de recurso) | Microsoft Azure"
    description="Fornece etapas e scripts do PowerShell para criar uma máquina virtual do Azure com imagens de galeria de máquina virtual do SQL Server."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/25/2016"
    ms.author="jroth"/>

# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-resource-manager"></a>Provisionar uma máquina virtual do SQL Server usando o PowerShell Azure (Gerenciador de recurso)

> [AZURE.SELECTOR]
- [Portal](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

## <a name="overview"></a>Visão geral

Este tutorial mostra como criar uma única máquina virtual Azure usando o modelo de implantação do **Gerenciador de recursos do Azure** usando cmdlets do PowerShell do Azure. Neste tutorial, vamos criar uma única máquina virtual usando uma única unidade de disco a partir de uma imagem na Galeria de SQL. Vamos criar novos provedores para o armazenamento, rede e recursos de computação que serão usados pela máquina virtual. Se você tiver provedores existentes para qualquer um desses recursos, você pode usar esses provedores.

Se você precisar a versão clássica deste tópico, consulte [provisionar uma máquina virtual do SQL Server usando o Azure PowerShell clássico](virtual-machines-windows-classic-ps-sql-create.md).

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, você precisará:

- Uma conta do Azure e assinatura antes de começar. Se você não tiver um, inscreva-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- [Azure PowerShell)](../powershell-install-configure.md), versão mínima do 1.4.0 ou posterior (Este tutorial escrito usando a versão 1.5.0).
    - Para recuperar sua versão, digite **Get-módulo Azure-ListAvailable**.

## <a name="configure-your-subscription"></a>Configurar a sua assinatura

Abrir o Windows PowerShell e estabelecer o acesso à sua conta do Azure executando o cmdlet a seguir. Você será apresentada com uma tela de entrada para inserir suas credenciais. Use o mesmo email e senha que você usa para entrar no portal do Azure.

    Add-AzureRmAccount

Depois de entrar com êxito, você verá algumas informações na tela que inclui a SubscriptionId com o qual você entrou. Esta é a assinatura no qual os recursos para este tutorial serão criados, a menos que você altere para uma assinatura diferente. Se você tiver vários SubscriptionIds, execute o seguinte cmdlet para retornar uma lista de todos os seus SubscriptionIds:

    Get-AzureRmSubscription

Para mudar para outro SubscriptionID, execute o seguinte cmdlet com seu SubscriptionId desejado.

    Select-AzureRmSubscription -SubscriptionId xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## <a name="define-image-variables"></a>Definir variáveis de imagem

Para simplificar a usabilidade e compreensão do script concluído partir deste tutorial, vamos começar definindo um número de variáveis. Altere os valores de parâmetro como ajustá-la, mas lembre-se de nomenclatura restrições relacionadas à comprimentos de nome e caracteres especiais ao modificar os valores fornecidos.

### <a name="location-and-resource-group"></a>Local e o grupo de recursos
Use duas variáveis para definir a região de dados e o grupo de recursos no qual você criará os outros recursos da máquina virtual.

Modificar conforme desejado e, em seguida, execute os seguintes cmdlets para inicializar essas variáveis.

    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"

### <a name="storage-properties"></a>Propriedades de armazenamento

Use as seguintes variáveis para definir a conta de armazenamento e o tipo de armazenamento a ser usado pela máquina virtual.

Modificar conforme desejado e, em seguida, execute o seguinte cmdlet para inicializar essas variáveis. Observe que neste exemplo, estamos usando [Armazenamento Premium](../storage/storage-premium-storage.md), que é recomendado para cargas de trabalho de produção. Para obter detalhes sobre este guia e outras recomendações, consulte [práticas recomendadas de desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md).

    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

### <a name="network-properties"></a>Propriedades de rede

Use as seguintes variáveis para definir a interface de rede, o método de alocação de TCP/IP, o nome de rede virtual, o nome de sub-rede virtual, o intervalo de endereços IP para a rede virtual, o intervalo de endereços IP para a sub-rede e o rótulo de nome de domínio público a ser usado pela rede na máquina virtual.

Modificar conforme desejado e, em seguida, execute o seguinte cmdlet para inicializar essas variáveis.

    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $TCPIPAllocationMethod = "Dynamic"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $DomainName = "sqlvm1"   

### <a name="virtual-machine-properties"></a>Propriedades de máquina virtual

Use as seguintes variáveis para definir o nome da máquina virtual, o nome do computador, o tamanho da máquina virtual e o nome do disco do sistema operacional da máquina virtual.

Modificar conforme desejado e, em seguida, execute o seguinte cmdlet para inicializar essas variáveis.

    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

### <a name="image-properties"></a>Propriedades da imagem

Use as seguintes variáveis para definir a imagem a ser usada para a máquina virtual. Neste exemplo, a imagem do SQL Server 2016 Enterprise é usada.

Modificar conforme desejado e, em seguida, execute o seguinte cmdlet para inicializar essas variáveis.

    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

Observe que você pode obter uma lista completa das ofertas de imagem do SQL Server com o comando Get-AzureRmVMImageOffer:

    Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'

E você pode ver as Skus disponíveis para uma oferta com o comando Get-AzureRmVMImageSku. O comando a seguir mostra que todas as Skus disponíveis para a **SQL2014SP1-WS2012R2** oferecer.

    Get-AzureRmVMImageSku -Location 'East US' -Publisher 'MicrosoftSQLServer' -Offer 'SQL2014SP1-WS2012R2' | Select Skus

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Com o modelo de implantação do Gerenciador de recursos, o primeiro objeto que você criar é o grupo de recursos. Usaremos o cmdlet [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt759837.aspx) para criar um grupo de recursos do Azure e seus recursos com o nome do grupo de recursos e o local definido pelas variáveis que você anteriormente inicializado.

Execute o cmdlet a seguir para criar seu novo grupo de recursos.

    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Na máquina virtual requer recursos de armazenamento para o disco de sistema operacional e para os arquivos de dados e log do SQL Server. Para simplificar, vamos criar um único disco para ambos. Você pode anexar discos adicionais mais tarde usando o cmdlet [Add-Azure disco](https://msdn.microsoft.com/library/azure/dn495252.aspx) para colocar os dados do SQL Server e arquivos em discos dedicados de log. Usaremos o cmdlet [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) para criar uma conta de armazenamento padrão em seu novo grupo de recursos e com o nome da conta de armazenamento, o nome de Sku de armazenamento e local definidos usando as variáveis que você anteriormente inicializado.

Execute o cmdlet a seguir para criar sua nova conta de armazenamento.  

    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

## <a name="create-network-resources"></a>Criar recursos de rede

Na máquina virtual requer um número de recursos de rede conectividade de rede.

- Cada máquina virtual requer uma rede virtual.
- Uma rede virtual deve ter pelo menos uma sub-rede definida.
- Uma interface de rede deve ser definida com um público ou um endereço IP particular.

### <a name="create-a-virtual-network-subnet-configuration"></a>Criar uma configuração de sub-rede de rede virtual

Vamos começar criando uma configuração de sub-rede para nossa rede virtual. Para nosso tutorial, vamos criar uma sub-rede padrão usando o cmdlet [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) . Vamos criar nossa configuração de sub-rede de rede virtual com o prefixo de nome e endereço de sub-rede definido usando as variáveis que você anteriormente inicializado.

>[AZURE.NOTE] Você pode definir propriedades adicionais da configuração de sub-rede da rede virtual usando esse cmdlet, mas que está além do escopo deste tutorial.

Execute o cmdlet a seguir para criar sua configuração de sub-rede virtual.

    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Em seguida, vamos criar nossa rede virtual usando o cmdlet [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) . Vamos criar nossa rede virtual em seu novo grupo de recursos, com o nome, o local e o endereço prefixo definido usando as variáveis que você inicializado anteriormente e usando a configuração de sub-rede que você definiu na etapa anterior.

Execute o cmdlet a seguir para criar sua rede virtual.

    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig

### <a name="create-the-public-ip-address"></a>Criar o endereço IP público

Agora que temos nossa rede virtual definido, precisamos configurar um endereço IP para conectividade na máquina virtual. Para este tutorial, vamos criar um endereço IP público usando endereçamento para oferecer suporte a conectividade com a Internet IP dinâmico. Usaremos o cmdlet [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) para criar o endereço IP público em grupo de recursos criado prevously e com o nome, local, método de alocação e rótulo de nome de domínio DNS definido usando as variáveis que você anteriormente inicializado.

>[AZURE.NOTE] Você pode definir propriedades adicionais do endereço IP público usando esse cmdlet, mas que está além do escopo deste tutorial inicial. Você também pode criar um endereço privado ou um endereço com um endereço estático, mas que também está além do escopo deste tutorial.

Execute o cmdlet a seguir para criar o seu endereço IP público.

    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName

### <a name="create-the-network-interface"></a>Criar a interface de rede

Agora está prontos para criar a interface de rede que usará nossa máquina virtual. Usaremos o cmdlet [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) criar nossa interface de rede no grupo de recursos criado anteriores e com o nome, o local, a sub-rede e o endereço IP público definido anteriormente.

Execute o cmdlet a seguir para criar sua interface de rede.

    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

## <a name="configure-a-vm-object"></a>Configurar um objeto de máquina virtual

Agora que temos recursos de rede e de armazenamento definidos, estamos prontos para definir recursos de computação para a máquina virtual. Para nosso tutorial, podemos será especificar o tamanho de máquina virtual e várias propriedades do sistema operacional, especifique a interface de rede que criamos anteriormente, defina o armazenamento de blob e especifique o disco de sistema operacional.

### <a name="create-the-vm-object"></a>Criar o objeto de máquina virtual

Vamos começar especificando o tamanho de máquina virtual. Para este tutorial, estamos especificando uma DS13. Usaremos o cmdlet [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) para criar um objeto configurável máquina virtual com o nome e o tamanho definido usando as variáveis que você anteriormente inicializado.

Execute o cmdlet a seguir para criar o objeto de máquina virtual.

    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Criar um objeto de credencial para manter o nome e senha para as credenciais de administrador local

Antes de nós pode definir as propriedades de sistema operacional da máquina virtual, precisamos fornecer as credenciais da conta administrador local como uma cadeia de segura. Para fazer isso, usaremos o cmdlet [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) .

Execute o cmdlet a seguir e, na janela de solicitação de credencial do Windows PowerShell, digite o nome e senha para usar para a conta de administrador local na máquina virtual Windows.

    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Definir as propriedades de sistema operacional da máquina virtual

Agora, estamos prontos para definir propriedades de sistema operacional da máquina virtual. Usaremos o cmdlet [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) para definir o tipo de sistema operacional como Windows, exigem o [agente de máquina virtual](virtual-machines-windows-classic-agents-and-extensions.md) para ser instalado, especificar que o cmdlet permite que a atualização automática e definir o nome de máquina virtual, o nome do computador e a credencial usando as variáveis que você anteriormente inicializado.

Execute o cmdlet a seguir para definir as propriedades de sistema operacional para sua máquina virtual.

    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Adicionar a interface de rede na máquina virtual

Em seguida, adicionaremos a interface de rede que criamos anteriormente na máquina virtual. Usaremos o cmdlet [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) para adicionar a interface de rede usando a variável de interface de rede que você definiu anteriormente.

Execute o cmdlet a seguir para definir a interface de rede para sua máquina virtual.

    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Definir o local de armazenamento de blob do disco a ser usado pela máquina virtual

Em seguida, definir o local de armazenamento de blob do disco a ser usado pela máquina virtual usando as variáveis que você definiu anteriormente.

Execute o cmdlet a seguir para definir o local de armazenamento de blob.

    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Definir as propriedades de disco para a máquina virtual do sistema operacional

Em seguida, podemos definirá o sistema operacional disco propriedades da máquina virtual. Usaremos o cmdlet [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) para especificar que o sistema operacional da máquina virtual será provenientes de uma imagem, definir cache para ler somente (porque o SQL Server está sendo instalado no mesmo disco) e definir o nome da máquina virtual e o disco de sistema operacional definidos usando as variáveis que definimos anteriormente.

Execute o cmdlet a seguir para definir o sistema operacional propriedades de disco para sua máquina virtual.

    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Especifique a imagem a plataforma da máquina virtual

Nossa última etapa de configuração é especificar a imagem de plataforma para nossa máquina virtual. Para nosso tutorial, estamos usando a imagem mais recente do SQL Server 2016 CTP. Usaremos o cmdlet [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) para usar essa imagem conforme definido pelas variáveis que você definiu anteriormente.

Execute o cmdlet a seguir para especificar a imagem de plataforma para sua máquina virtual.

    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## <a name="create-the-sql-vm"></a>Criar a máquina virtual SQL

Agora que você concluiu as etapas de configuração, você está pronto para criar a máquina virtual. Usaremos o cmdlet [New-AzureRmVM](https://msdn.microsoft.com/library/mt603754.aspx) para criar a máquina virtual usando as variáveis que definimos.

Execute o cmdlet a seguir para criar sua máquina virtual.

    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

Na máquina virtual é criada. Observe que uma conta de armazenamento padrão é criada para diagnóstico de inicialização porque a conta de armazenamento especificado para disco da máquina virtual é uma conta de armazenamento premium.

Agora você pode exibir esta máquina no Portal do Azure para ver [seu endereço IP público e o seu nome de domínio totalmente qualificado](virtual-machines-windows-portal-sql-server-provision.md#Connect).  

## <a name="example-script"></a>Script de exemplo

O script a seguir contém o script PowerShell completo para este tutorial. Ele pressupõe que você tenha já instalação a assinatura Azure para usar com os comandos **Add-AzureRmAccount** e **Selecione AzureRmSubscription** .


    # Variables
    ## Global
    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"
    ## Storage
    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

    ## Network
    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $TCPIPAllocationMethod = "Dynamic"
    $DomainName = "sqlvm1"

    ##Compute
    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

    ##Image
    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

    # Resource Group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    # Storage
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

    # Network
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

    # Compute
    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."
    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

    # Image
    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

    ## Create the VM in Azure
    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

## <a name="next-steps"></a>Próximas etapas
Depois que a máquina virtual é criada, você está pronto para se conectar à máquina virtual usando conectividade RDP e configuração. Para obter mais informações, consulte [conectar a um SQL Máquina Virtual do servidor no Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
