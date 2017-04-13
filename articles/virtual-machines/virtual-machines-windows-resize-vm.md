<properties
    pageTitle="Redimensionar um máquina virtual do Windows | Microsoft Azure"
    description="Redimensione uma máquina virtual do Windows criada no modelo de implantação do Gerenciador de recursos, usando o Powershell do Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="drewm"/>

    
# <a name="resize-a-windows-vm"></a>Redimensionar um máquina virtual do Windows

Este artigo mostra como redimensionar uma máquina virtual Windows, criado no modelo de implantação do Gerenciador de recursos usando o Powershell do Azure.

Depois de criar uma máquina virtual (VM), você pode dimensionar a máquina virtual para cima ou para baixo, alterando o tamanho de máquina virtual. Em alguns casos, você deve desalocar a máquina virtual primeiro. Isso pode acontecer se o novo tamanho não está disponível no cluster hardware que atualmente hospeda a máquina virtual.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Redimensionar uma máquina de virtual do Windows não em um conjunto de disponibilidade

1. Liste os tamanhos de máquina virtual que estão disponíveis no cluster hardware onde a máquina virtual está hospedada. 

    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName> 
    ```

2. Se o tamanho desejado estiver listado, execute os seguintes comandos para redimensionar a máquina virtual. Se o tamanho desejado não estiver listado, vá para a etapa 3.

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVMsize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```

3. Se o tamanho desejado não estiver listado, execute os seguintes comandos desaloque a máquina virtual, redimensioná-la e reinicie a máquina virtual.

    ```powershell
    $rgname = "<resourceGroupName>"
    $vmname = "<vmName>"
    Stop-AzureRmVM -ResourceGroupName $rgname -VMName $vmname -Force
    $vm = Get-AzureRmVM -ResourceGroupName $rgname -VMName $vmname
    $vm.HardwareProfile.VmSize = "<newVMSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgname
    Start-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    ```

> [AZURE.WARNING] Desalocando a máquina virtual libera quaisquer endereços IP dinâmicos atribuídos para a máquina virtual. O sistema operacional e dados discos não são afetados. 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Redimensionar uma máquina de virtual do Windows em um conjunto de disponibilidade

Se o novo tamanho para uma máquina virtual em um conjunto de disponibilidade não estiver disponível no cluster hardware atualmente hospeda a máquina virtual, todas as VMs no conjunto de disponibilidade precisará ser desalocada para redimensionar a máquina virtual. Você também talvez seja necessário atualizar o tamanho das outras VMs na disponibilidade definida após uma VM foi redimensionada. Para redimensionar uma máquina virtual em um conjunto de disponibilidade, execute as seguintes etapas.

1. Liste os tamanhos de máquina virtual que estão disponíveis no cluster hardware onde a máquina virtual está hospedada.

    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName>
    ```

2. Se o tamanho desejado estiver listado, execute os seguintes comandos para redimensionar a máquina virtual. Se não estiver listado, vá para a etapa 3.

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVmSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```

3. Se o tamanho desejado não estiver listado, continue com as seguintes etapas para desalocar todas VMs no conjunto de disponibilidade, redimensionar VMs e reiniciá-los.

4.  Pare todas as VMs no conjunto de disponibilidade.

    ```powershell
    $rg = "<resourceGroupName>"
    $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
    $vmIds = $as.VirtualMachinesReferences
    foreach ($vmId in $vmIDs){
        $string = $vmID.Id.Split("/")
        $vmName = $string[8]
        Stop-AzureRmVM -ResourceGroupName $rg -Name $vmName -Force
    } 
    ```
              
5.  Redimensionar e reinicie VMs no conjunto de disponibilidade.

    ```powershell
    $rg = "<resourceGroupName>"
    $newSize = "<newVmSize>"
    $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
    $vmIds = $as.VirtualMachinesReferences
    foreach ($vmId in $vmIDs){
        $string = $vmID.Id.Split("/")
        $vmName = $string[8]
        $vm = Get-AzureRmVM -ResourceGroupName $rg -Name $vmName
        $vm.HardwareProfile.VmSize = $newSize
        Update-AzureRmVM -ResourceGroupName $rg -VM $vm
        Start-AzureRmVM -ResourceGroupName $rg -Name $vmName
    }
    ```

## <a name="next-steps"></a>Próximas etapas

- Para escalabilidade adicional, executar várias instâncias de máquina virtual e dimensionar. Para obter mais informações, consulte [Dimensionar automaticamente máquinas em um conjunto de escala de máquina Virtual do Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).



