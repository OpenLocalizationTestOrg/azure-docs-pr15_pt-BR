<properties 
   pageTitle="Implantar uma máquina virtual com IP estático público usando o PowerShell no Gerenciador de recursos | Microsoft Azure"
   description="Saiba como implantar VMs com IP estático público usando o PowerShell no Gerenciador de recursos"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-powershell"></a>Implantar uma máquina virtual com IP estático público usando o PowerShell

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="step-1---start-your-script"></a>Etapa 1 - começar seu script

Você pode baixar o script PowerShell completo usado [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-ps.ps1). Siga as etapas abaixo para alterar o script para trabalhar no seu ambiente.

1. Altere os valores das variáveis a seguir com base nos valores que você deseja usar para sua implantação. Os valores abaixo de mapa para o cenário usado neste documento.

        # Set variables resource group
        $rgName                = "IaaSStory"
        $location              = "West US"
        
        # Set variables for VNet
        $vnetName              = "WTestVNet"
        $vnetPrefix            = "192.168.0.0/16"
        $subnetName            = "FrontEnd"
        $subnetPrefix          = "192.168.1.0/24"
        
        # Set variables for storage
        $stdStorageAccountName = "iaasstorystorage"
        
        # Set variables for VM
        $vmSize                = "Standard_A1"
        $diskSize              = 127
        $publisher             = "MicrosoftWindowsServer"
        $offer                 = "WindowsServer"
        $sku                   = "2012-R2-Datacenter"
        $version               = "latest"
        $vmName                = "WEB1"
        $osDiskName            = "osdisk"
        $nicName               = "NICWEB1"
        $privateIPAddress      = "192.168.1.101"
        $pipName               = "PIPWEB1"
        $dnsName               = "iaasstoryws1"

## <a name="step-2---create-the-necessary-resources-for-your-vm"></a>Etapa 2 - criar os recursos necessários para sua máquina virtual

Antes de criar uma máquina virtual, você precisa de um grupo de recursos, VNet, IP público e NIC a ser usado pela máquina virtual.

1. Crie um novo grupo de recursos.

        New-AzureRmResourceGroup -Name $rgName -Location $location
        
2. Crie a VNet e sub-rede.

        $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName `
            -AddressPrefix $vnetPrefix -Location $location   
        
        Add-AzureRmVirtualNetworkSubnetConfig -Name $subnetName `
            -VirtualNetwork $vnet -AddressPrefix $subnetPrefix
        
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet 

3. Crie o recurso IP público. 

        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName `
            -AllocationMethod Static -DomainNameLabel $dnsName -Location $location

4. Crie a interface de rede (NIC) para a máquina virtual na sub-rede criada acima, com o IP público. Observe que o primeiro cmdlet recuperar o VNet do Azure, isso é necessário como um **Conjunto-AzureRmVirtualNetwork** foi executado para alterar o VNet existente.

        $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
            -Subnet $subnet -Location $location -PrivateIpAddress $privateIPAddress `
            -PublicIpAddress $pip

5. Crie uma conta de armazenamento para hospedar a unidade do sistema operacional de máquina virtual.

        $stdStorageAccount = New-AzureRmStorageAccount -Name $stdStorageAccountName `
            -ResourceGroupName $rgName -Type Standard_LRS -Location $location

## <a name="step-3---create-the-vm"></a>Etapa 3 - criar a máquina virtual 

Agora que todos os recursos necessários estão no lugar, você pode criar uma nova VM.

1. Crie o objeto de configuração para a máquina virtual.

        $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize 

1. Obtenha as credenciais da conta de administrador local da máquina virtual.

        $cred = Get-Credential -Message "Type the name and password for the local administrator account."

2. Crie um objeto de configuração de máquina virtual.

        $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName `
            -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

3. Defina a imagem de sistema operacional para a máquina virtual.

        $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher `
            -Offer $offer -Skus $sku -Version $version

4. Configure o disco de sistema operacional.

        $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
        $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage

5. Adicione a NIC para a máquina virtual.

        $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id -Primary

6. Crie a máquina virtual.

        New-AzureRmVM -VM $vmConfig -ResourceGroupName $rgName -Location $location

7. Salve o arquivo de script.

## <a name="step-4---run-the-script"></a>Etapa 4 - execute o script

Após fazer as alterações necessárias e Noções básicas sobre o script Mostrar acima, execute o script. 

1. A partir de um console PowerShell ou PowerShell ISE, execute o script acima.
2. A saída abaixo deve ser exibida após alguns minutos.

        ResourceGroupName : IaaSStory
        Location          : westus
        ProvisioningState : Succeeded
        Tags              : 
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory
                
        AddressSpace      : Microsoft.Azure.Commands.Network.Models.PSAddressSpace
        DhcpOptions       : Microsoft.Azure.Commands.Network.Models.PSDhcpOptions
        Subnets           : {FrontEnd}
        ProvisioningState : Succeeded
        AddressSpaceText  : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptionsText   : {}
        SubnetsText       : [
                              {
                                "Name": "FrontEnd",
                                "AddressPrefix": "192.168.1.0/24"
                              }
                            ]
        ResourceGroupName : IaaSStory
        Location          : westus
        ResourceGuid      : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        Tag               : {}
        TagsTable         : 
        Name              : WTestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet
        
        AddressSpace      : Microsoft.Azure.Commands.Network.Models.PSAddressSpace
        DhcpOptions       : Microsoft.Azure.Commands.Network.Models.PSDhcpOptions
        Subnets           : {FrontEnd}
        ProvisioningState : Succeeded
        AddressSpaceText  : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptionsText   : {
                              "DnsServers": []
                            }
        SubnetsText       : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "ProvisioningState": "Succeeded"
                              }
                            ]
        ResourceGroupName : IaaSStory
        Location          : westus
        ResourceGuid      : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        Tag               : {}
        TagsTable         : 
        Name              : WTestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet
                
        TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        Status              : Succeeded
        StatusCode          : OK
        Output              : 
        StartTime           : 1/12/2016 12:57:56 PM -08:00
        EndTime             : 1/12/2016 12:59:13 PM -08:00
        Error               : 
        ErrorText           : 

   