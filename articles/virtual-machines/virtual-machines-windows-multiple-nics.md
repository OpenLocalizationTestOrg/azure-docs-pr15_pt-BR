<properties
   pageTitle="Criar uma máquina virtual Windows com várias placas de rede | Microsoft Azure"
   description="Saiba como criar uma máquina virtual Windows com várias placas de rede anexadas a ele usando modelos Azure PowerShell ou Gerenciador de recursos."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="creating-a-windows-vm-with-multiple-nics"></a>Criando uma máquina virtual Windows com várias placas de rede
Você pode criar uma máquina virtual (VM) no Azure que tem várias interfaces de rede virtual (NICs) anexadas a ele. Um cenário comum seria ter sub-redes diferentes para conectividade de front-end e back-end ou uma rede dedicada a uma solução de monitoramento ou de backup. Este artigo fornece comandos rápidos para criar uma máquina virtual com várias placas de rede anexadas. Para obter informações detalhadas, incluindo como criar várias NICs dentro de seus próprios scripts do PowerShell, leia mais sobre como [implantar VMs multi-NIC](../virtual-network/virtual-network-deploy-multinic-arm-ps.md). Diferentes [tamanhos de máquina virtual](virtual-machines-windows-sizes.md) suporte um número variável de NICs, portanto dimensionar sua máquina virtual adequadamente.

>[AZURE.WARNING] Você deve anexar várias placas de rede quando você cria uma máquina virtual - você não pode adicionar NICs para uma máquina virtual existente. Você pode [criar uma máquina virtual com base na s virtual original](virtual-machines-windows-vhd-copy.md) e criar várias placas de rede quando você implantar a máquina virtual.

## <a name="create-core-resources"></a>Criar recursos principais
Certifique-se de que você tem as [últimas Azure PowerShell instalado e configurado](../powershell-install-configure.md). Faça logon em sua conta do Azure:

```powershell
Login-AzureRmAccount
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Nomes de parâmetro de exemplo incluídos `myResourceGroup`, `mystorageaccount`, e `myVM`.

Primeiro, crie um grupo de recursos. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no `WestUs` local:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

Crie uma conta de armazenamento para armazenar suas VMs. O exemplo a seguir cria uma conta de armazenamento denominada `mystorageaccount`:

```powershell
$storageAcc = New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "mystorageaccount" `
    -Kind "Storage" -SkuName "Premium_LRS" 
```

## <a name="create-virtual-network-and-subnets"></a>Criar sub-redes e rede virtual
Definir duas sub-redes de rede virtual - um para o tráfego de front-end e outro para o tráfego de back-end. O exemplo a seguir define duas sub-redes, chamados `mySubnetFrontEnd` e `mySubnetBackEnd`:

```powershell
$mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
    -AddressPrefix "192.168.1.0/24"
$mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
    -AddressPrefix "192.168.2.0/24"
```

Crie sua rede virtual e sub-redes. O exemplo a seguir cria uma rede virtual chamada `myVnet`:

```powershell
$myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myVnet" -AddressPrefix "192.168.0.0/16" `
    -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
```


## <a name="create-multiple-nics"></a>Criar várias placas de rede
Crie duas NICs, anexando uma NIC à sub-rede front-end e uma NIC à sub-rede back-end. O exemplo a seguir cria duas NICs, chamadas `myNic1` e `myNic2`:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic1" -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic2" -SubnetId $backEnd.Id
```

Normalmente você também cria um [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) ou [balanceador de carga](../load-balancer/load-balancer-overview.md) para ajudar a gerenciar e distribuir o tráfego em suas VMs. O artigo de [máquina virtual de multi-NIC mais detalhada](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) o orientará na criação de um grupo de segurança de rede e atribuindo NICs.


## <a name="create-the-virtual-machine"></a>Criar a máquina virtual
Agora começar a criar sua configuração de máquina virtual. O tamanho de cada máquina virtual tem um limite para o número total de NICs que você pode adicionar a uma máquina virtual. Leia mais sobre [os tamanhos de máquina virtual do Windows](virtual-machines-windows-sizes.md). 

Primeiro, defina suas credenciais de máquina virtual o `$cred` variável da seguinte forma:

```powershell
$cred = Get-Credential
```

O exemplo a seguir define uma máquina virtual chamada `myVM` e usa um tamanho de máquina virtual que suporta até duas NICs (`Standard_DS2_v2`):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2_v2"
```

Crie o resto da sua configuração de máquina virtual. O exemplo a seguir cria uma máquina de virtual do Windows Server 2012 R2:

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName Te"MyVM" `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
```

Anexe as duas NICs que você criou anteriormente:

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
```

Configure o armazenamento e o disco virtual para sua nova VM:

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption "fromImage"
```

Finalmente, crie uma máquina virtual:

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "WestUS"
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Criando várias placas de rede usando o recurso Gerenciador de modelos
Modelos de Gerenciador de recursos Azure usam arquivos JSON declarativos para definir seu ambiente. Você pode ler uma [Visão geral do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md). Modelos do Gerenciador de recursos fornecem uma maneira de criar várias instâncias de um recurso durante a implantação, como criar várias placas de rede. Usar a *cópia* para especificar o número de instâncias para criar:

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Leia mais sobre como [criar várias instâncias usando *Copiar*](../resource-group-create-multiple.md). 

Você também pode usar um `copyIndex()` , em seguida, acrescentar um número para um nome de recurso, que permite que você crie `myNic1`, `MyNic2`, etc. A seguir mostra um exemplo de acrescentar o valor de índice:

```bash
"name": "[concat('myNic', copyIndex())]", 
```

Você pode ler um exemplo completo de [criação de várias placas de rede usando o recurso Gerenciador de modelos](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Próximas etapas
Verifique se revisar [tamanhos de máquina virtual do Windows](virtual-machines-windows-sizes.md) ao tentar criar uma máquina virtual com várias placas de rede. Observe o número máximo de NICs suporta o tamanho de cada máquina virtual. 

Lembre-se de que você não pode adicionar NICs adicionais para uma máquina virtual existente, você deve criar todas as NICs quando você implanta a máquina virtual. Tome cuidado ao planejar as implantações para certificar-se de que você tem todos os a conectividade de rede necessária desde o início.