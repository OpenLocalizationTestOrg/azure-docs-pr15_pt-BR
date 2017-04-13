<properties
    pageTitle="Redimensionar uma VM clássico do Windows | Microsoft Azure"
    description="Redimensione uma máquina virtual do Windows criada no modelo clássico de implantação, usando o Powershell do Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="drewm"/>


# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>Redimensionar uma máquina de virtual do Windows criado no modelo clássico de implantação

Este artigo mostra como redimensionar uma máquina virtual Windows, criado no modelo clássico de implantação usando o Powershell do Azure.

Ao considerar a capacidade de redimensionar uma máquina virtual, há dois conceitos que controlam o intervalo de tamanhos disponíveis para redimensionar a máquina virtual. O primeiro conceito é a região no qual sua máquina virtual é implantado. A lista de tamanhos de máquina virtual disponíveis na região está na guia Serviços da página da web do Azure regiões. O segundo conceito é o hardware físico hospedando sua máquina virtual no momento. Os servidores físicos hospedagem VMs são agrupados em clusters de hardware físico comuns. O método de alterar um tamanho de máquina virtual é diferente dependendo se o novo tamanho de máquina virtual desejado é suportado pelo cluster hardware atualmente hospeda a máquina virtual.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Você também pode [redimensionar uma máquina virtual criada no modelo de implantação do Gerenciador de recursos](virtual-machines-windows-resize-vm.md).


## <a name="add-your-account"></a>Adicionar sua conta

Você deve configurar Azure PowerShell para trabalhar com recursos Azure clássicos. Siga as etapas abaixo para configurar o Azure PowerShell para gerenciar recursos clássicos.

1. No prompt do PowerShell, digite `Add-AzureAccount` e clique em **Enter**. 
2. Digite o endereço de email associado a sua assinatura do Azure e clique em **continuar**. 
3. Digite a senha da sua conta. 
4. Clique em **entrar**. 


## <a name="resize-in-the-same-hardware-cluster"></a>Redimensionar no mesmo cluster hardware

Para redimensionar uma máquina virtual para um tamanho disponível no cluster hardware hospeda a máquina virtual, execute as seguintes etapas.

1. Execute o seguinte comando do PowerShell para listar os tamanhos de máquina virtual disponíveis no cluster hardware hospeda o serviço de nuvem que contém a máquina virtual.

    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```

2. Execute os seguintes comandos para redimensionar a máquina virtual.

    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>Redimensionar um novo cluster de hardware

Para redimensionar uma máquina virtual para um tamanho não está disponível no cluster hardware a máquina virtual, a nuvem de hospedagem serviço e todas as VMs no serviço de nuvem devem ser recriadas. Cada serviço de nuvem é hospedado em um cluster de hardware único para que todas as VMs no serviço de nuvem devem ser um tamanho que é suportado em um cluster de hardware. As etapas a seguir descreverá como redimensionar uma máquina virtual, excluindo e, em seguida, recriar o serviço de nuvem.

1. Execute o seguinte comando do PowerShell para listar os tamanhos de máquina virtual disponíveis na região. 

    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```

2. Anote todas as configurações para cada máquina virtual no serviço de nuvem que contém a máquina virtual para ser redimensionada. 
3. Exclua todas as VMs no serviço de nuvem selecionando a opção para manter os discos para cada máquina virtual.
4. Recrie a máquina virtual para ser redimensionada usando o tamanho de máquina virtual desejado.
5. Recrie todas as outras VMs que estavam no serviço de nuvem usando um tamanho de máquina virtual disponível no cluster hardware agora hospeda o serviço de nuvem.

Um exemplo de script para excluir e recriar um serviço de nuvem usando um novo tamanho de máquina virtual pode ser encontrado [aqui](https://github.com/Azure/azure-vm-scripts). 


## <a name="next-steps"></a>Próximas etapas

- [Redimensionar uma máquina virtual criada no modelo de implantação do Gerenciador de recursos](virtual-machines-windows-resize-vm.md).