<properties
   pageTitle="Benefícios de usar Azure híbrido para servidor de janela | Microsoft Azure"
   description="Saiba como maximizar seus benefícios de Windows Server Software Assurance para trazer licenças de locais no Azure"
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
   ms.workload="infrastructure-services"
   ms.date="07/13/2016"
   ms.author="georgem"/>

# <a name="azure-hybrid-use-benefit-for-windows-server"></a>Benefícios de usar Azure híbrido para o Windows Server

Para clientes usando o Windows Server com o Software Assurance, você pode trazer seu local licenças do Windows Server para Azure e executar o Windows Server VMs no Azure a um custo reduzido. A vantagem de usar do Azure híbrido permite que você executar o Windows Server VMs no Azure e apenas obter cobrado para a taxa de computação base. Para obter mais informações, consulte o [benefício de uso do Azure híbrido licenciamento por página](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Este artigo explica como implantar o Windows Server VMs no Azure usar esse benefício de licenciamento.

> [AZURE.NOTE] Você não pode usar imagens do Azure Marketplace para implantar o Windows Server VMs utilizando o benefício de usar do Azure híbrido. Você deve implantar suas VMs usando modelos PowerShell ou Gerenciador de recursos para registrar corretamente suas VMs como qualificado para o desconto de taxa de computação base.

## <a name="pre-requisites"></a>Pré-requisitos
Há algumas pré-requisitos para utilizar Azure híbrido uso benefício para o Windows Server VMs no Azure:

- Ter o módulo do PowerShell do Azure instalado
- Seu VHD de servidor Windows carregou ao armazenamento do Azure

### <a name="install-azure-powershell"></a>Instale o PowerShell Azure
Verifique se que você tiver [instalado e configurado o PowerShell mais recente do Azure](../powershell-install-configure.md). Mesmo se você pretende implantar suas VMs usando modelos do Gerenciador de recursos, você ainda precisar Azure PowerShell instalado para carregar seu VHD de servidor do Windows (consulte a etapa a seguir).

### <a name="upload-a-windows-server-vhd"></a>Carregar um VHD do Windows Server

Para implantar um servidor de Windows máquina virtual no Azure, primeiro é necessário criar um VHD que contém sua compilação do Windows Server base. Esse VHD deve ser preparado adequadamente via Sysprep antes de carregá-lo no Azure. Você pode [ler mais sobre os requisitos de VHD e o processo de Sysprep](./virtual-machines-windows-upload-image.md) e [Suporte a Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Faça backup a máquina virtual antes de executar Sysprep. Depois de preparar seu VHD, carregue o VHD para sua conta de armazenamento do Azure usando o `Add-AzureRmVhd` cmdlet da seguinte forma:

```
Add-AzureRmVhd -ResourceGroupName MyResourceGroup -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd" -LocalFilePath 'C:\Path\To\myvhd.vhd'
```

> [AZURE.NOTE] Microsoft SQL Server, o SharePoint Server e Dynamics também pode utilizar o licenciamento do Software Assurance. Você ainda precisa preparar a imagem do Windows Server por instalar os componentes do aplicativo e fornecendo chaves de licença adequadamente, depois carregando a imagem do disco do Azure. Examine a documentação apropriada para executar Sysprep com seu aplicativo, como [Considerações para instalar o SQL Server usando o Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) ou [criar uma imagem de referência do SharePoint Server 2016 (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx).

Você também pode ler mais sobre como [carregar o VHD ao processo Azure](./virtual-machines-windows-upload-image.md#upload-the-vm-image-to-your-storage-account).

> [AZURE.TIP] Este artigo se concentra nos Implantando VMs do Windows Server. Você também pode implantar VMs de cliente do Windows da mesma maneira. Os exemplos a seguir, você substitui `Server` com `Client` adequadamente.

## <a name="deploy-a-vm-via-powershell-quick-start"></a>Implantar uma máquina virtual por meio do PowerShell-início rápido
Ao implantar o seu servidor de Windows máquina virtual por meio do PowerShell, você tem um parâmetro adicional para `-LicenseType`. Quando você tiver seu VHD carregado Azure, você cria uma nova máquina virtual usando `New-AzureRmVM` e especifique o tipo de licenciamento da seguinte maneira:

```
New-AzureRmVM -ResourceGroupName MyResourceGroup -Location "West US" -VM $vm -LicenseType Windows_Server
```

Você pode [ler uma explicação mais detalhada sobre como implantar uma máquina virtual no Azure por meio do PowerShell](./virtual-machines-windows-hybrid-use-benefit-licensing.md#deploy-windows-server-vm-via-powershell-detailed-walkthrough) abaixo ou ler um guia mais descritivo sobre as diferentes etapas para [criar uma máquina de virtual do Windows usando o Gerenciador de recursos e PowerShell](./virtual-machines-windows-ps-create.md).

## <a name="deploy-a-vm-via-resource-manager"></a>Implantar uma máquina virtual por meio do Gerenciador de recursos
Dentro de seus modelos do Gerenciador de recursos, um parâmetro adicional para `licenseType` pode ser especificado. Você pode ler mais sobre [a criação de modelos do Gerenciador de recursos do Azure](../resource-group-authoring-templates.md). Quando você tiver seu VHD carregado Azure, edite modelo do Gerenciador de recursos para incluir o tipo de licença como parte do provedor de computação e implantar o modelo como normal:

```
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```
 
## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Verifique se que sua máquina virtual está utilizando o benefício de licenciamento
Depois de ter implantado sua máquina virtual através do método de implantação do PowerShell ou Gerenciador de recursos, verifique se o tipo de licença com `Get-AzureRmVM` da seguinte maneira:
 
```
Get-AzureRmVM -ResourceGroup MyResourceGroup -Name MyVM
```

A saída é similar ao seguinte:

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Isso contrasta com a seguinte máquina virtual implantada sem o benefício de uso do Azure híbrido licenciamento, como uma máquina virtual implantada diretamente da Galeria de Azure:

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : 
```
 
## <a name="detailed-powershell-walkthrough"></a>Explicação passo a passo detalhadas do PowerShell

As seguintes etapas detalhadas do PowerShell mostram uma implantação completa de uma máquina virtual. Você pode ler mais contexto como para os cmdlets real e diferentes componentes sendo criados em [criar uma máquina de virtual do Windows usando o Gerenciador de recursos e PowerShell](./virtual-machines-windows-ps-create.md). Você percorrer a criação de seu grupo de recursos, conta de armazenamento e rede virtual, e em seguida, defina sua máquina virtual e finalmente cria sua máquina virtual.
 
Primeiro, com segurança obter credenciais, defina um local e o nome do grupo de recursos:

```
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "TestLicensing"
```

Crie um IP público:

```
$publicIPName = "testlicensingpublicip"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
```

Defina sua sub-rede, NIC e VNET:

```
$subnetName = "testlicensingsubnet"
$nicName = "testlicensingnic"
$vnetName = "testlicensingvnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

Nomeie sua máquina virtual e crie uma configuração de máquina virtual:

```
$vmName = "testlicensing"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

Defina seu sistema operacional:

```
$computerName = "testlicensing"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
```

Adicione sua NIC para a máquina virtual:

```
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Defina a conta de armazenamento para usar:

```
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName testlicensing
```

Carregar seu VHD, devidamente preparado e anexar à sua máquina virtual para uso:

```
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://testlicensing.blob.core.windows.net/vhd/licensing.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
```

Finalmente, crie sua máquina virtual e definir o tipo de licença para utilizar os benefícios de uso do Azure híbrido:

```
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType Windows_Server
```

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre o [Licenciamento do benefício de uso do Azure híbrido](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Saiba mais sobre como [usar modelos do Gerenciador de recursos](../azure-resource-manager/resource-group-overview.md).
