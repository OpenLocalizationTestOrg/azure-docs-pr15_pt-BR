<properties
    pageTitle="Mover um máquina virtual do Windows | Microsoft Azure"
    description="Mova uma máquina virtual Windows para outro Azure assinatura ou grupo de recursos no modelo de implantação do Gerenciador de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cynthn"/>

    


# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Mover uma máquina virtual Windows para outro Azure assinatura ou grupo de recursos 

Este artigo explica como mover uma máquina virtual Windows entre grupos de recursos ou assinaturas. Mover entre assinaturas pode ser útil se você criou uma máquina virtual em uma assinatura pessoal e agora deseja movê-la para a assinatura da sua empresa para continuar seu trabalho.

> [AZURE.NOTE] Novas IDs de recurso serão criados como parte da mudança. Depois que a máquina virtual foi movida, você precisará atualizar seu ferramentas e scripts para usar o novo recurso IDs. 


[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]


## <a name="use-powershell-to-move-a-vm"></a>Usar o Powershell para mover uma máquina virtual

Para mover uma máquina virtual para outro grupo de recursos, você precisa certificar-se de que você também mover todos os recursos dependentes. Para usar o cmdlet Move-AzureRMResource, é necessário o nome do recurso e o tipo de recurso. Você pode obter ambos do cmdlet localizar AzureRMResource.

    Find-AzureRMResource -ResourceGroupNameContains "<sourceResourceGroupName>"
    

Para mover uma máquina virtual precisamos mover vários recursos. Podemos apenas criar variáveis separadas para cada recurso e, em seguida, listá-lo. Este exemplo inclui a maioria dos recursos básicos de uma máquina virtual, mas você pode adicionar mais conforme necessário.

    $sourceRG = "<sourceResourceGroupName>"
    $destinationRG = "<destinationResourceGroupName>"
    
    $vm = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Compute/virtualMachines" -ResourceName "<vmName>"
    $storageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<storageAccountName>"
    $diagStorageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<diagnosticStorageAccountName>"
    $vNet = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/virtualNetworks" -ResourceName "<vNetName>"
    $nic = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkInterfaces" -ResourceName "<nicName>"
    $ip = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/publicIPAddresses" -ResourceName "<ipName>"
    $nsg = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkSecurityGroups" -ResourceName "<nsgName>"
    
    Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId

Para mover os recursos para assinatura diferente, inclua o parâmetro **- DestinationSubscriptionId** . 

    Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId



Você será solicitado a confirmar que você deseja mover os recursos especificados. Digite **Y** para confirmar que você deseja mover os recursos.

  
## <a name="next-steps"></a>Próximas etapas

Você pode mover vários tipos de recursos entre assinaturas e grupos de recursos. Para obter mais informações, consulte [Mover recursos para novo grupo de recursos ou assinatura](../resource-group-move-resources.md).    