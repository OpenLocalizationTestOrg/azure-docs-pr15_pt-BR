<properties
    pageTitle="Alterar um conjunto de disponibilidade de VMs | Microsoft Azure"
    description="Saiba como alterar a disponibilidade definida para suas máquinas virtuais usando o PowerShell do Azure e o modelo de implantação do Gerenciador de recursos."
    keywords=""
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="drewm"/>



# <a name="change-the-availability-set-for-a-windows-vm"></a>Alterar a disponibilidade definida para uma máquina virtual Windows

As etapas a seguir descrevem como alterar o conjunto de disponibilidade de uma máquina virtual usando o PowerShell do Azure. Uma máquina virtual só pode ser adicionada a uma disponibilidade definida quando ele é criado. Para alterar a disponibilidade configurar, você precisa excluir e recriar a máquina virtual. 

## <a name="change-the-availability-set-using-powershell"></a>Alterar a disponibilidade definiu usando o PowerShell

1. Capture os seguintes detalhes importantes da máquina virtual a ser modificada.

    Nome da máquina virtual
    
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <Name-of-resource-group> -Name <name-of-VM>
    $vm.Name
    ```
 
    Tamanho de máquina virtual
    
    ```powershell
    $vm.HardwareProfile.VmSize
    ```

    Interface de rede principal de rede e interfaces de rede opcional se existirem na máquina virtual
    
    ```powershell
    $vm.NetworkProfile.NetworkInterfaces[0].Id
    ```

    Perfil de disco do sistema operacional

    ```powershell
    $vm.StorageProfile.OsDisk.OsType
    $vm.StorageProfile.OsDisk.Name
    $vm.StorageProfile.OsDisk.Vhd.Uri
    ```

    Perfis de disco para cada disco de dados 
    
    ```powershell
    $vm.StorageProfile.DataDisks[<index>].Lun
    $vm.StorageProfile.DataDisks[<index>].Vhd.Uri
    ```

    Extensões de máquina virtual instaladas 
    
    ```powershell
    $vm.Extensions
    ```

2. Exclua a máquina virtual sem excluir qualquer um dos discos ou as interfaces de rede.

    ```powershell
    Remove-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName> 
    ```

3. Criar a disponibilidade defina se ele ainda não existir

    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName <resourceGroupName> -Name <availabilitySetName> -Location "<location>" 
    ```

4. Recriar a máquina virtual usando o novo conjunto de disponibilidade

    ```powershell
    $vm2 = New-AzureRmVMConfig -VMName <VM-name> -VMSize <vm-size> -AvailabilitySetId <availability-set-id>

    Set-AzureRmVMOSDisk -CreateOption "Attach" -VM <vmConfig> -VhdUri <osDiskURI> -Name <osDiskName> [-Windows | -Linux]

    Add-AzureRmVMNetworkInterface -VM <vmConfig> -Id  <nicId> 

    New-AzureRmVM -ResourceGroupName <resourceGroupName> -Location <location> -VM <vmConfig>
    ``` 

5. Adicione extensões e discos de dados. Para obter mais informações, consulte [Anexar dados disco para máquina virtual](virtual-machines-windows-attach-disk-portal.md) e [Exemplos de configuração de extensão](virtual-machines-windows-extensions-configuration-samples.md). Extensões e discos de dados podem ser adicionadas para a máquina virtual usando o PowerShell ou CLI do Azure.

## <a name="example-script"></a>Script de exemplo

O script a seguir fornece um exemplo de coletar as informações necessárias, excluindo a máquina virtual original e, em seguida, recriá-lo em um novo conjunto de disponibilidade.

```powershell
    #set variables
    $rg = "demo-resource-group"
    $vmName = "demo-vm"
    $newAvailSetName = "demo-as"
    $outFile = "C:\temp\outfile.txt"

    #Get VM Details
    $OriginalVM = get-azurermvm -ResourceGroupName $rg -Name $vmName

    #Output VM details to file
    "VM Name: " | Out-File -FilePath $outFile 
    $OriginalVM.Name | Out-File -FilePath $outFile -Append

    "Extensions: " | Out-File -FilePath $outFile -Append
    $OriginalVM.Extensions | Out-File -FilePath $outFile -Append

    "VMSize: " | Out-File -FilePath $outFile -Append
    $OriginalVM.HardwareProfile.VmSize | Out-File -FilePath $outFile -Append

    "NIC: " | Out-File -FilePath $outFile -Append
    $OriginalVM.NetworkProfile.NetworkInterfaces[0].Id | Out-File -FilePath $outFile -Append

    "OSType: " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.OsDisk.OsType | Out-File -FilePath $outFile -Append

    "OS Disk: " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.OsDisk.Vhd.Uri | Out-File -FilePath $outFile -Append

    if ($OriginalVM.StorageProfile.DataDisks) {
    "Data Disk(s): " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.DataDisks | Out-File -FilePath $outFile -Append
    }

    #Remove the original VM
    Remove-AzureRmVM -ResourceGroupName $rg -Name $vmName

    #Create new availability set if it does not exist
    $availSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rg -Name $newAvailSetName -ErrorAction Ignore
    if (-Not $availSet) {
    $availset = New-AzureRmAvailabilitySet -ResourceGroupName $rg -Name $newAvailSetName -Location $OriginalVM.Location
    }

    #Create the basic configuration for the replacement VM
    $newVM = New-AzureRmVMConfig -VMName $OriginalVM.Name -VMSize $OriginalVM.HardwareProfile.VmSize -AvailabilitySetId $availSet.Id
    Set-AzureRmVMOSDisk -VM $NewVM -VhdUri $OriginalVM.StorageProfile.OsDisk.Vhd.Uri  -Name $OriginalVM.Name -CreateOption Attach -Windows

    #Add Data Disks
    foreach ($disk in $OriginalVM.StorageProfile.DataDisks ) { 
    Add-AzureRmVMDataDisk -VM $newVM -Name $disk.Name -VhdUri $disk.Vhd.Uri -Caching $disk.Caching -Lun $disk.Lun -CreateOption Attach -DiskSizeInGB $disk.DiskSizeGB
    }

    #Add NIC(s)
    foreach ($nic in $OriginalVM.NetworkInterfaceIDs) {
        Add-AzureRmVMNetworkInterface -VM $NewVM -Id $nic
    }

    #Create the VM
    New-AzureRmVM -ResourceGroupName $rg -Location $OriginalVM.Location -VM $NewVM -DisableBginfoExtension
```

## <a name="next-steps"></a>Próximas etapas

Adicione armazenamento adicional para sua máquina virtual adicionando um adicional [disco de dados](virtual-machines-windows-attach-disk-portal.md).

