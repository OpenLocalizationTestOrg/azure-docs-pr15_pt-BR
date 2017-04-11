<properties
    pageTitle="Mover uma máquina virtual Linux | Microsoft Azure"
    description="Mova uma VM Linux para outro grupo Azure assinatura ou recurso no modelo de implantação do Gerenciador de recursos."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cynthn"/>

    


# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Mover uma VM Linux para outra assinatura ou grupo de recursos

Este artigo explica como mover uma VM Linux entre grupos de recursos ou assinaturas. Movendo uma máquina virtual entre assinaturas pode ser útil se você criou uma máquina virtual em uma assinatura pessoal e agora deseja movê-lo a assinatura da sua empresa.

> [AZURE.NOTE] Novas IDs de recurso são criados como parte da mudança. Depois que a máquina virtual foi movida, você precisa atualizar seu ferramentas e scripts para usar o novo recurso IDs. 


## <a name="use-the-azure-cli-to-move-a-vm"></a>Usar a CLI do Azure para mover uma máquina virtual 

Para mover com êxito uma máquina virtual, você precisará mover a máquina virtual e todos os seus recursos de suporte. Use o comando **Mostrar grupo azure** para listar todos os recursos em um grupo de recursos e suas identificações. É útil para comunicar a saída desse comando para um arquivo para que possa copiar e colar as identificações em comandos posteriores.

    azure group show <resourceGroupName>

Para mover uma máquina virtual e seus recursos para outro grupo de recursos, use o comando CLI **recurso azure mover** . O exemplo a seguir mostra como mover uma máquina virtual e os recursos mais comuns requer. Usamos o parâmetro **-i** e passar em uma lista separada por vírgulas (sem espaços) de IDs para os recursos para mover.

    
    vm=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Compute/virtualMachines/<vmName>
    nic=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkInterfaces/<nicName>
    nsg=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkSecurityGroups/<nsgName>
    pip=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/publicIPAddresses/<publicIPName>
    vnet=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/virtualNetworks/<vnetName>
    diag=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<diagnosticStorageAccountName>
    storage=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<storageAcountName>      
    
    azure resource move --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag -d "<destinationResourceGroup>"
    
Se você deseja mover a máquina virtual e seus recursos para uma assinatura diferente, adicione a **-destino subscriptionId #60; destinationSubscriptionID & #62;** parâmetro para especificar a assinatura de destino.

Se você estiver trabalhando no Prompt de comando em um computador Windows, você precisará adicionar um **$** na frente os nomes de variável quando você declará-los. Isso não é necessário no Linux.

Você será solicitado a confirmar que você deseja mover o recurso especificado. Digite **Y** para confirmar que você deseja mover os recursos.
    

[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Próximas etapas

Você pode mover vários tipos de recursos entre assinaturas e grupos de recursos. Para obter mais informações, consulte [Mover recursos para novo grupo de recursos ou assinatura](../resource-group-move-resources.md).    