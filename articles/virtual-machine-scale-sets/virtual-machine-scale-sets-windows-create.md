<properties
    pageTitle="Criar um conjunto de escala de máquina Virtual usando o PowerShell | Microsoft Azure"
    description="Criar um conjunto de escala de máquina Virtual usando o PowerShell"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-virtual-machine-scale-set-using-azure-powershell"></a>Criar uma escala de máquina virtual do Windows definiu usando o PowerShell do Azure

Estas etapas siga uma abordagem de preencher apaga para a criação de um conjunto de escala Azure máquina virtual. Consulte [Visão geral sobre conjuntos de escala de máquina Virtual](virtual-machine-scale-sets-overview.md) para saber mais sobre conjuntos de escala.

Ele deve levar cerca de 30 minutos para executar as etapas neste artigo.

## <a name="step-1-install-azure-powershell"></a>Etapa 1: Instalar o PowerShell Azure

Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter informações sobre como instalar a versão mais recente do PowerShell do Azure, selecionando sua assinatura e entrar sua conta.

## <a name="step-2-create-resources"></a>Etapa 2: Criar recursos

Crie os recursos que são necessários para o novo conjunto de escala.

### <a name="resource-group"></a>Grupo de recursos

Um conjunto de escala de máquina virtual deve estar contido em um grupo de recursos.

1. Obter uma lista de locais disponíveis onde você pode colocar recursos:

        Get-AzureLocation | Sort Name | Select Name

2. Escolha um local que funciona melhor para você, substitua o valor de **$locName** com esse nome local e, em seguida, criar a variável:

        $locName = "location name from the list, such as Central US"

3. Substitua o valor de **$rgName** com o nome que você deseja usar para o novo grupo de recursos e, em seguida, criar a variável: 

        $rgName = "resource group name"
        
4. Crie o grupo de recursos:
    
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    Você verá algo parecido com este exemplo:

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### <a name="storage-account"></a>Conta de armazenamento

Uma conta de armazenamento é usada por uma máquina virtual para armazenar o disco do sistema operacional e dados de diagnóstico usados para dimensionar. Quando possível, é prática recomendada ter uma conta de armazenamento para cada máquina virtual criada em um conjunto de escala. Se não for possível, planejar não mais de 20 VMs por conta de armazenamento. O exemplo neste artigo mostra três contas de armazenamento sendo criadas para três máquinas virtuais.

1. Substitua o valor de **$saName** um nome para a conta de armazenamento. Teste o nome para exclusividade. 

        $saName = "storage account name"
        Get-AzureRmStorageAccountNameAvailability $saName

    Se a resposta for **verdadeiro**, seu nome proposto é exclusivo.

3. Substitua o valor de **$saType** com o tipo da conta de armazenamento e, em seguida, crie a variável:  

        $saType = "storage account type"
        
    Valores possíveis são: Standard_LRS, Standard_GRS, Standard_RAGRS ou Premium_LRS.
        
4. Crie a conta:
    
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

    Você verá algo parecido com este exemplo:

        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
                              .Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext

5. Repita as etapas 1 a 4 para criar três contas de armazenamento, por exemplo myst1, myst2 e myst3.

### <a name="virtual-network"></a>Rede virtual

Uma rede virtual é necessária para as máquinas virtuais do conjunto de escala.

1. Substitua o valor de **$subnetName** com o nome que você deseja usar para a sub-rede da rede virtual e, em seguida, criar a variável: 

        $subnetName = "subnet name"
        
2. Crie a configuração de sub-rede:
    
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
    O prefixo de endereço pode ser diferente da sua rede virtual.

3. Substitua o valor de **$netName** com o nome que você deseja usar para a rede virtual e, em seguida, criar a variável: 

        $netName = "virtual network name"
        
4. Crie rede virtual:
    
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="configuration-of-the-scale-set"></a>Configuração do conjunto de escala

Você tem todos os recursos que você precisa para a escala de conjunto de configuração, então vamos criá-lo.  

1. Substitua o valor de **$ipName** com o nome que você deseja usar para a configuração de IP e, em seguida, criar a variável: 

        $ipName = "IP configuration name"
        
2. Crie a configuração de IP:

        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. Substitua o valor de **$vmssConfig** com o nome que você deseja usar para a configuração do conjunto de escala e, em seguida, criar a variável:   

        $vmssConfig = "Scale set configuration name"
        
3. Crie a configuração para o conjunto de escala:

        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
        
    Este exemplo mostra que uma escala definir sendo criado com três máquinas virtuais. Consulte [Visão geral sobre conjuntos de escala de máquina Virtual](virtual-machine-scale-sets-overview.md) para saber mais sobre a capacidade de conjuntos de escala. Essa etapa também inclui a definição do tamanho (referido como SkuName) das máquinas virtuais do conjunto. Para localizar um tamanho que atenda às suas necessidades, examine [tamanhos para máquinas virtuais](../virtual-machines/virtual-machines-windows-sizes.md).
    
4. Adicione a configuração da interface de rede à configuração de conjunto de escala:
        
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
        
    Você verá algo parecido com este exemplo:

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### <a name="operating-system-profile"></a>Perfil de sistema operacional

1. Substitua o valor de **$computerName** com o prefixo de nome do computador que você deseja usar e, em seguida, criar a variável: 

        $computerName = "computer name prefix"
        
2. Substitua o valor de **$adminName** o nome da conta de administrador nas máquinas virtuais e, em seguida, crie a variável:

        $adminName = "administrator account name"
        
3. Substitua o valor de **$adminPassword** com a senha da conta e, em seguida, crie a variável:

        $adminPassword = "password for administrator accounts"
        
4. Crie o perfil do sistema operacional:

        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### <a name="storage-profile"></a>Perfil de armazenamento

1. Substitua o valor de **$storageProfile** com o nome que você deseja usar para o perfil de armazenamento e, em seguida, criar a variável:  

        $storageProfile = "storage profile name"
        
2. Crie as variáveis que definem a imagem a ser usada:  
      
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
        
    Para localizar as informações sobre outras imagens para usar, examine [navegar e selecionar máquina virtual Azure imagens com o Windows PowerShell e a CLI do Azure](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md).
        
3. Substitua o valor de **$vhdContainers** com uma lista que contém os caminhos onde os discos virtuais são armazenados, como "https://mystorage.blob.core.windows.net/vhds" e, em seguida, crie a variável:
       
        $vhdContainers = @("https://myst1.blob.core.windows.net/vhds","https://myst2.blob.core.windows.net/vhds","https://myst3.blob.core.windows.net/vhds")
        
4. Crie o perfil de armazenamento:

        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storageProfile -VhdContainer $vhdContainers -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### <a name="virtual-machine-scale-set"></a>Conjunto de escala de máquina virtual

Por fim, você pode criar o conjunto de escala.

1. Substitua o valor de **$vmssName** com o nome do conjunto de escala de máquina virtual e, em seguida, crie a variável:

        $vmssName = "scale set name"
        
2. Crie o conjunto de escala:

        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    Você verá algo parecido com este exemplo que mostra uma implantação bem-sucedida:

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                                ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## <a name="step-3-explore-resources"></a>Etapa 3: Explorar recursos

Use estes recursos para explorar o conjunto de escala de máquina virtual que você criou:

- Portal Azure - uma quantidade limitada de informações está disponível no portal.
- [Azure Resource Explorer](https://resources.azure.com/) - esta ferramenta é o melhor para explorar o estado atual do seu conjunto de escala.
- PowerShell Azure - Use este comando para obter informações:

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        
        Or 
        
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        

## <a name="next-steps"></a>Próximas etapas

- Gerenciar o conjunto de escala que você acabou de criar usando as informações em [gerenciar máquinas virtuais em um conjunto de escala de máquina Virtual](virtual-machine-scale-sets-windows-manage.md)
- Considere configurar o dimensionamento automático de sua escala definir usando informações na [escala automática de dimensionamento e máquina virtual define](virtual-machine-scale-sets-autoscale-overview.md)
- Saiba mais sobre o dimensionamento vertical revisando [escala Vertical automática com conjuntos de escala de máquina Virtual](virtual-machine-scale-sets-vertical-scale-reprovision.md)
