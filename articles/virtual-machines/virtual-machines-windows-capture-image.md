<properties
    pageTitle="Capturar uma imagem de máquina virtual do generalizado VM Azure | Microsoft Azure"
    description="Saiba como capturar uma imagem de máquina virtual de uma VM Azure generalizado criado no modelo de implantação do Gerenciador de recursos"
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
    ms.date="10/20/2016"
    ms.author="cynthn"/>

# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>Como capturar uma imagem de máquina virtual de uma VM generalizado do Azure


Este artigo mostra como usar o PowerShell do Azure para criar uma imagem de uma VM generalizado do Azure. Em seguida, você pode usar a imagem para criar outra VM. A imagem inclui o disco de sistema operacional e os discos de dados que estão anexados à máquina virtual. A imagem não inclui os recursos de rede virtual, então você precisa configurar esses recursos quando você cria a máquina virtual novo. 


## <a name="prerequisites"></a>Pré-requisitos

- Você precisa ter já [generalizado a máquina virtual](virtual-machines-windows-generalize-vhd.md). Generalizar uma máquina virtual remove todas as suas informações de conta pessoal, entre outras coisas e prepara o computador para ser usado como uma imagem.

- Você precisa ter o Azure PowerShell versão 1.0. x ou mais recente esteja instalado. Se você ainda não tiver instalado o PowerShell, leia [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para as etapas de instalação.


## <a name="log-in-to-azure-powershell"></a>Faça logon no Azure PowerShell

1. Abra o PowerShell do Azure e entrar em sua conta do Azure.

    ```powershell
    Login-AzureRmAccount
    ```

    Abre uma janela pop-up para que você insira suas credenciais de conta do Microsoft Azure.

2. Obtenha a IDs de assinatura para suas assinaturas disponíveis.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Definir a assinatura correta usando a identificação de assinatura.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Desalocar a máquina virtual e definir o estado da generalizado       

1. Desalocar os recursos de máquina virtual.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```

    O *Status* para a máquina virtual no portal do Azure muda de **Parar** para **parado (desalocado)**.

2. Defina o status da máquina virtual para **Generalized**. 

    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```

3. Verificar o status da máquina virtual. A seção **OSState/generalizado** para a máquina virtual deve ter **DisplayStatus** definido como **generalizado de máquina virtual**.  

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Criar a imagem 

1. Copie a imagem de máquina virtual para o contêiner de armazenamento de destino usando este comando. A imagem é criada na mesma conta de armazenamento como da máquina virtual original. O `-Path` parâmetro salva uma cópia do modelo JSON localmente. O `-DestinationContainerName` parâmetro é o nome do contêiner que você deseja manter suas imagens. Se o contêiner não existir, ele é criado.

    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```

    Você pode obter a URL da imagem a partir do modelo de arquivo JSON. Vá para os **recursos** > **storageProfile** > **osDisk** > **imagem** > seção de**uri** para o caminho completo da imagem. A URL da imagem se parece com: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
    
    Você também pode verificar o URI no portal. A imagem é copiada para um contêiner denominado **sistema** em sua conta de armazenamento. 


## <a name="next-steps"></a>Próximas etapas

- Agora você pode [criar uma máquina virtual da imagem](virtual-machines-windows-create-vm-generalized.md).

