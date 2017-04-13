<properties
    pageTitle="Criar uma cópia da sua máquina virtual Windows | Microsoft Azure"
    description="Saiba como criar uma cópia da sua VM Azure especializados executando o Windows, no modelo de implantação do Gerenciador de recursos."
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
    ms.date="09/21/2016"
    ms.author="cynthn"/>

# <a name="create-a-vm-from-a-specialized-vhd"></a>Criar uma máquina virtual a partir de um VHD especializado

Crie uma nova VM anexando um VHD especializado como o disco de sistema operacional usando o Powershell. Um VHD especializado mantém as contas de usuário, aplicativos e outros dados de estado de sua máquina virtual original. 

Se você quiser criar uma máquina virtual a partir de um VHD generalizado, consulte [criar uma máquina virtual de uma imagem VHD generalizada](virtual-machines-windows-create-vm-generalized.md).

## <a name="create-the-subnet-and-vnet"></a>Criar a sub-rede e vNet

Crie a vNet e sub-rede da [rede virtual](../virtual-network/virtual-networks-overview.md).

1. Crie a sub-rede. Este exemplo cria uma sub-rede chamada **mySubNet**, no o grupo de recursos **myResourceGroup**e define o prefixo de endereço de sub-rede como **10.0.0.0/24**.

    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```

2. Crie o vNet. Este exemplo define o nome de rede virtual para ser **myVnetName**, o local para **Oeste EUA**e o prefixo de endereço para a rede virtual **10.0.0.0/16**. 

    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
            
## <a name="create-a-public-ip-address-and-nic"></a>Criar um endereço IP público e NIC

Para permitir a comunicação com máquina virtual na rede virtual, você precisará de um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie o IP público. Neste exemplo, o nome de endereço IP público está definido para **myIP**.

    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
    ```       

2. Criar a NIC. Neste exemplo, o nome NIC está definido como **myNicName**.

    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP

Para poder fazer logon sua máquina virtual usando RDP, você precisa ter uma regra de segurança que permita acesso RDP na porta 3389. Como o VHD para a máquina virtual novo foi criado a partir de uma existente especializado máquina virtual, após a máquina virtual é criada que você pode usar uma conta existente na máquina virtual origem que tinham permissão para fazer logon usando o RDP.

Este exemplo define o nome NSG **myNsg** e o nome da regra RDP para **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```

Para obter mais informações sobre pontos de extremidade e regras NSG, consulte [abrindo portas para uma máquina virtual no Azure usando o PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

## <a name="create-the-vm-configuration"></a>Criar a configuração de máquina virtual

Defina a configuração de máquina virtual para anexar o VHD copiado como o VHD do sistema operacional.


```powershell
    # Set the URI for the VHD that you want to use. In this example, the VHD file named "myOsDisk.vhd" is kept in a storage account named "myStorageAccount" in a container named "myContainer".
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    
    #Set the VM name and size. This example sets the VM name to "myVM" and the VM size to "Standard_A2".
    $vmName = "myVM"
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

    #Add the NIC
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

    #Add the OS disk by using the URL of the copied OS VHD. In this example, when the OS disk is created, the term "osDisk" is appened to the VM name to create the OS disk name. This example also specifies that this Windows-based VHD should be attached to the VM as the OS disk.
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
```


Se você tiver discos de dados que precisam ser anexado a máquina virtual, você também deve adicionar o seguinte: 

```powershell
    # Optional: Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun).
    $dataDiskName = $vmName + "dataDisk"
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach
```

Os dados e as URLs de disco do sistema operacional algo parecido com isto: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Você pode encontrar isso no portal navegando até o contêiner de armazenamento de destino, clicando no sistema operacional ou dados VHD que foi copiado e, em seguida, copiando o conteúdo da URL.


## <a name="create-the-vm"></a>Criar a máquina virtual

Crie a máquina virtual usando as configurações que acabou de criar.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Se esse comando foi bem-sucedido, você verá a saída como esta:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
 
```
 
## <a name="verify-that-the-vm-was-created"></a>Verificar se a máquina virtual foi criada 
 
Você deve ver a máquina virtual recém-criado no [portal do Azure](https://portal.azure.com), em **Procurar** > **máquinas virtuais**, ou usando os seguintes comandos do PowerShell:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Próximas etapas

Para entrar sua nova máquina virtual, navegue até a máquina virtual no [portal](https://portal.azure.com), clique em **Conectar**e abra o arquivo RDP de área de trabalho remota. Use as credenciais de conta da máquina virtual original para entrar em sua nova máquina virtual. Para obter mais informações, consulte [como conectar e faça logon uma máquina virtual Azure executando o Windows](virtual-machines-windows-connect-logon.md).







