<properties
    pageTitle="Criar máquina virtual a partir de um VHD generalizado | Microsoft Azure"
    description="Saiba como criar uma máquina virtual Windows a partir de uma imagem VHD generalizada usando o PowerShell do Azure, no modelo de implantação do Gerenciador de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="cynthn"/>

# <a name="create-a-vm-from-a-generalized-vhd-image"></a>Criar uma máquina virtual a partir de uma imagem VHD generalizada

Uma imagem VHD generalizada teve todas as suas informações de conta pessoal removidas usando [Sysprep](virtual-machines-windows-generalize-vhd.md). Você pode criar um VHD generalizado executando Sysprep em uma máquina virtual local, depois [Carregando o VHD no Azure](virtual-machines-windows-upload-image.md), ou executando o Sysprep em um máquina virtual de Azure existente e, em seguida, [copiando o VHD](virtual-machines-windows-vhd-copy.md).

Se você quiser criar uma máquina virtual a partir de um VHD especializado, consulte [criar uma máquina virtual de um VHD especializado](virtual-machines-windows-create-vm-specialized.md).

A maneira mais rápida para criar uma máquina virtual de um VHD generalizado é usar um [modelo de início rápido] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image). 


## <a name="prerequisites"></a>Pré-requisitos

Se você vai usar um VHD carregado em um máquina virtual local, como um criado usando o Hyper-V, você deverá verificar se você seguiu as instruções em [Preparar-se um VHD do Windows para carregar no Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). 

VHDs carregados e existente VHDs de máquina virtual do Azure precisam ser generalizado antes de criar uma máquina virtual usando esse método. Para obter mais informações, consulte [Generalize uma máquina virtual Windows usando Sysprep](virtual-machines-windows-generalize-vhd.md). 


## <a name="set-the-uri-of-the-vhd"></a>Definir o URI do VHD

O URI do VHD usar está no formato: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**VHD. Neste exemplo o VHD denominado **myVHD** está em a conta de armazenamento **mystorageaccount** no contêiner **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie a vNet e sub-rede da [rede virtual](../virtual-network/virtual-networks-overview.md).


1. Crie a sub-rede. O exemplo a seguir cria uma sub-rede denominada **mySubnet** no o grupo de recursos **myResourceGroup** com o prefixo de **10.0.0.0/24**endereço.  

    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
      
2. Crie a rede virtual. O exemplo a seguir cria uma rede virtual chamada **myVnet** no local **Oeste EUA** com o prefixo de endereço do **10.0.0.0/16**.  

    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
            
## <a name="create-a-public-ip-address-and-network-interface"></a>Criar uma interface de rede e endereços IP pública

Para permitir a comunicação com máquina virtual na rede virtual, você precisará de um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie um endereço IP público. Este exemplo cria um endereço IP público chamado **myPip**. 

    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
    ```       

2. Criar a NIC. Este exemplo cria uma NIC denominada **myNic**. 

    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP

Para poder fazer logon sua máquina virtual usando RDP, você precisa ter uma regra de segurança que permita acesso RDP na porta 3389. 

Este exemplo cria um NSG chamado **myNsg** que contém uma regra chamada **myRdpRule** que permita o tráfego RDP pela porta 3389. Para saber mais sobre NSGs, consulte [abrindo portas para uma máquina virtual no Azure usando o PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Criar uma variável para a rede virtual

Crie uma variável para a rede virtual concluída. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

## <a name="create-the-vm"></a>Criar a máquina virtual

O seguinte script do PowerShell mostra como configurar as configurações de máquina virtual e usar a imagem de máquina virtual carregada como fonte para a nova instalação.

</br>


```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential
    
    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"
    
    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"
    
    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"
    
    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"
    
    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"
    
    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage, Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"
    
    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName
    
    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
    
    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    
    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
    
    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows
    
    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Verificar se a máquina virtual foi criada 

Ao concluir, você deverá ver a máquina virtual recém-criado no [portal do Azure](https://portal.azure.com) em **Procurar** > **máquinas virtuais**, ou usando os seguintes comandos do PowerShell:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Próximas etapas

Para gerenciar sua nova máquina virtual com o Azure PowerShell, consulte [máquinas virtuais de gerenciar usando o Gerenciador de recursos do Azure e PowerShell](virtual-machines-windows-ps-manage.md).


