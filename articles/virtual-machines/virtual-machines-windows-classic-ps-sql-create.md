<properties
    pageTitle="Criar uma máquina Virtual do servidor SQL no Azure PowerShell (clássico) | Microsoft Azure"
    description="Fornece etapas e scripts do PowerShell para criar uma máquina virtual do Azure com imagens de galeria de máquina virtual do SQL Server. Este tópico usa o modo clássico de implantação."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/07/2016"
    ms.author="jroth" />

# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Provisionar uma máquina virtual do SQL Server usando o PowerShell Azure (clássico)

## <a name="overview"></a>Visão geral

Este artigo fornece etapas sobre como criar uma máquina virtual de SQL Server no Azure usando os cmdlets do PowerShell.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para a versão do Gerenciador de recursos deste tópico, consulte [provisionar uma máquina virtual do SQL Server usando o Gerenciador de recursos do PowerShell do Azure](virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Instalar e configurar o PowerShell:

1. Se você não tiver uma conta do Azure, visite a [versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

2. [Baixe e instale os comandos do PowerShell do Azure mais recentes](../powershell-install-configure.md).

3. Iniciar o Windows PowerShell e conectá-lo à sua assinatura do Azure com o comando **Add-AzureAccount** .

        Add-AzureAccount

## <a name="determine-your-target-azure-region"></a>Determinar seu destino região Azure

Sua máquina do SQL Server Virtual será hospedado em um serviço de nuvem que reside uma região específica do Azure. As seguintes etapas ajudarão-lo a determinar sua região, a conta de armazenamento e serviço que será usado para o restante do tutorial em nuvem.

1. Determine o Centro de dados que você deseja usar para hospedar sua máquina de virtual do SQL Server. Os seguintes comandos do PowerShell exibirão as regiões disponíveis em detalhes com uma lista de resumo no final.

        Get-AzureLocation
        (Get-AzureLocation).Name

2.  Depois de ter identificado seu local preferido, defina uma variável chamada **$dcLocation** nessa região.

        $dcLocation = "<region name>"

## <a name="set-your-subscription-and-storage-account"></a>Configurar sua conta de assinatura e de armazenamento

1. Determine a assinatura do Azure que você usará para a nova máquina virtual.

        (Get-AzureSubscription).SubscriptionName

1. Atribua sua assinatura do Azure de destino para a variável **$subscr** . Então defina isso como sua assinatura do Azure atual.

        $subscr="<subscription name>"
        Select-AzureSubscription -SubscriptionName $subscr –Current

1. Marque para contas de armazenamento existentes. O script a seguir exibe todas as contas de armazenamento que existem na sua região escolhida:

        (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName

    >[AZURE.NOTE] Se você solicitar uma nova conta de armazenamento, primeiro crie um nome de conta de armazenamento de todas as minúsculas com o comando New-AzureStorageAccount como no exemplo a seguir: **New-AzureStorageAccount - StorageAccountName "<storage account name>"-local $dcLocation**

1. Atribua o nome de conta de armazenamento de destino para o **$staccount**. Use **Set-AzureSubscription** para definir a assinatura e a conta de armazenamento atual.

        $staccount="<storage account name>"
        Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

## <a name="select-a-sql-server-virtual-machine-image"></a>Selecione uma imagem de máquina virtual do SQL Server

1. Descubra a lista de imagens de máquinas virtuais do SQL Server disponíveis na Galeria. Essas imagens que todos têm uma propriedade **ImageFamily** que começa com "SQL". A seguinte consulta exibe a família de imagem disponível para você e que possuem pré-instalado do SQL Server.

        Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily

1. Quando você encontrar a família de imagem de máquina virtual, pode haver várias imagens publicadas nesta família. Use o seguinte script para localizar o nome de imagem publicados máquina virtual mais recente para a sua família imagem selecionada (como o **SQL Server 2016 RTM Enterprise no Windows Server 2012 R2**):

        $family="<ImageFamily value>"
        $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

        echo "Selected SQL Server image name:"
        echo "   $image"

## <a name="create-the-virtual-machine"></a>Criar a máquina virtual

Por fim, crie a máquina virtual com PowerShell:

1. Crie um serviço de nuvem para hospedar a máquina virtual novo. Observe que também é possível usar um serviço de nuvem existente, em vez disso. Crie uma nova variável **$svcname** com o nome curto do serviço de nuvem.

        $svcname = "<cloud service name>"
        New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

2. Especifique o nome de máquina virtual e um tamanho. Para obter mais informações sobre tamanhos de máquina virtual, consulte [Tamanhos de máquina Virtual do Azure](virtual-machines-windows-sizes.md).

        $vmname="<machine name>"
        $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
        $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

3. Especifica a conta de administrador local e senha.

        $cred=Get-Credential -Message "Type the name and password of the local administrator account."
        $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

4. Execute o seguinte script para criar a máquina virtual.

        New-AzureVM –ServiceName $svcname -VMs $vm1

>[AZURE.NOTE] Para obter explicação adicional e opções de configuração, consulte a seção **Construa seu conjunto de comando** em [Usar o PowerShell do Azure para criar e configurar previamente máquinas virtuais baseadas em Windows](virtual-machines-windows-classic-create-powershell.md).

## <a name="example-powershell-script"></a>Script do PowerShell de exemplo

O script a seguir fornece e exemplo de um script completo que cria uma máquina virtual de **SQL Server 2016 RTM Enterprise no Windows Server 2012 R2** . Se você usar esse script, você deve personalizar as variáveis iniciais com base nas etapas anteriores neste tópico.

    # Customize these variables based on your settings and requirements:
    $dcLocation = "East US"
    $subscr="mysubscription"
    $staccount="mystorageaccount"
    $family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
    $svcname = "mycloudservice"
    $vmname="myvirtualmachine"
    $vmsize="A5"

    # Set the current subscription and storage account
    # Comment out the New-AzureStorageAccount line if the account already exists
    Select-AzureSubscription -SubscriptionName $subscr –Current
    New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

    # Select the most recent VM image in this image family:
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    # Create the new cloud service; comment out this line if cloud service exists already:
    New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

    # Create the VM config:
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    # Set administrator credentials:
    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

    # Create the SQL Server VM:
    New-AzureVM –ServiceName $svcname -VMs $vm1


## <a name="connect-with-remote-desktop"></a>Conectar-se com a área de trabalho remota

1. Criar o. Arquivos RDP na pasta de documentos do usuário atual para iniciar essas máquinas virtuais para concluir a configuração:

        $documentspath = [environment]::getfolderpath("mydocuments")
        Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"

1. No diretório de documentos, abra o arquivo RDP. Conectar-se com o nome de usuário de administrador e a senha fornecidos anteriormente (por exemplo, se seu nome de usuário foi VMAdmin, especifique "\VMAdmin" como o usuário e fornecer a senha).

        cd $documentspath
        .\vm1.rdp

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Concluir a configuração da máquina do servidor SQL para acesso remoto

Depois de fazer logon na máquina com área de trabalho remota, configure o SQL Server com base nas instruções em [etapas para configurar a conectividade do SQL Server em uma máquina virtual do Azure](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar instruções adicionais para máquinas virtuais com o PowerShell na [documentação de máquinas virtuais](virtual-machines-windows-classic-create-powershell.md)de provisionamento. Para scripts adicionais relacionados ao armazenamento de Premium e do SQL Server, consulte [Usar o armazenamento de Premium de Azure com o SQL Server em máquinas virtuais](virtual-machines-windows-classic-sql-server-premium-storage.md).

Em muitos casos, a próxima etapa é migrar seus bancos de dados para essa nova VM de servidor SQL. Para obter diretrizes de migração do banco de dados, consulte [migrar um banco de dados SQL Server em uma máquina virtual do Azure](virtual-machines-windows-migrate-sql.md).

Se você também estiver interessado em usando o portal do Azure para criar máquinas virtuais de SQL, consulte [provisionamento uma máquina de Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md). Observe que o tutorial que o orientará durante o portal cria VMs usando o modelo recomendado do Gerenciador de recursos, em vez do modelo clássico usada neste tópico PowerShell.

Além desses recursos, recomendamos que você examine [outros tópicos relacionados a executando o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
