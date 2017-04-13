<properties
   pageTitle="Gerenciar suas máquinas virtuais usando o PowerShell do Azure | Microsoft Azure"
   description="Saiba comandos que você pode usar para automatizar tarefas no gerenciamento de suas máquinas virtuais."
   services="virtual-machines-windows"
   documentationCenter="windows"
   authors="singhkays"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

   <tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/12/2016"
   ms.author="kasing"/>

# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Gerenciar suas máquinas virtuais usando o PowerShell do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Muitas tarefas que você fazer a cada dia para gerenciar suas VMs podem ser automatizadas usando cmdlets do PowerShell do Azure. Este artigo fornece comandos de exemplo para tarefas mais simples e links para artigos que mostram os comandos para tarefas mais complexas.

>[AZURE.NOTE] Se você ainda não tiver instalado e configurado o Azure PowerShell ainda, você pode obter instruções no artigo [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

## <a name="how-to-use-the-example-commands"></a>Como usar os comandos de exemplo
Você precisará substituir parte do texto na comandos com texto apropriado para seu ambiente. O < e > símbolos indicam que você precisa substituir o texto. Quando você substitui o texto, remover os símbolos, mas deixe as aspas no lugar.

## <a name="get-a-vm"></a>Obtenha uma máquina virtual
Esta é uma tarefa básica que você usará com frequência. Usá-lo para obter informações sobre uma máquina virtual, realizar tarefas em uma máquina virtual ou obter a saída para armazenar em uma variável.

Para obter informações sobre a máquina virtual, execute este comando, substituindo tudo nas aspas, incluindo os caracteres < e >:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Para armazenar a saída em uma variável de $vm, execute:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Faça logon em uma máquina virtual baseado no Windows

Execute estes comandos:

>[AZURE.NOTE] Você pode obter o nome de serviço de nuvem e máquina virtual da exibição do comando **Get-AzureVM** .
>
    $svcName = "<cloud service name>"
    $vmName = "<virtual machine name>"
    $localPath = "<drive and folder location to store the downloaded RDP file, example: c:\temp >"
    $localFile = $localPath + "\" + $vmname + ".rdp"
    Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## <a name="stop-a-vm"></a>Interromper uma máquina virtual

Execute este comando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Use esse parâmetro para manter o IP virtual (VIP) do serviço de nuvem, caso seja o último VM em desse serviço de nuvem. <br><br> Se você usar o parâmetro StayProvisioned, você ainda serão cobrados para a máquina virtual.

## <a name="start-a-vm"></a>Iniciar uma máquina virtual

Execute este comando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Anexar um disco de dados
Esta tarefa exige algumas etapas. Primeiro, use o * * * Add-AzureDataDisk * * * cmdlet para adicionar o disco ao objeto $vm. Em seguida, você usa o cmdlet de **AzureVM de atualização** para atualizar a configuração da máquina virtual.

Você também precisará decidir se deseja anexar um novo disco ou que contenha dados. Para um novo disco, o comando cria o arquivo. vhd e anexa-lo.

Para anexar um novo disco, execute este comando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Para anexar um disco de dados existente, execute este comando:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Para anexar discos de dados de um arquivo. vhd existente no armazenamento de blob, execute este comando:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Criar uma máquina virtual baseado no Windows

Para criar uma nova máquina virtual baseado no Windows Azure, use as instruções em [Usar o PowerShell do Azure para criar e configurar previamente máquinas virtuais baseadas em Windows](virtual-machines-windows-classic-create-powershell.md). As etapas tópico você através da criação de um Azure PowerShell comando definir que cria uma máquina virtual baseado no Windows que pode ser pré-configuradas:

- Com associação de domínio do Active Directory.
- Com discos adicionais.
- Como um membro de um existente balanceamento de carga definido.
- Com um endereço IP estático.
