<properties 
   pageTitle="Comandos comuns do PowerShell para VMs | Microsoft Azure"
   description="Comandos comuns do PowerShell para começar a criar e gerenciar suas VMs no Azure no Windows"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="davidmu1" 
   manager="timlt" 
   editor="tysonn" 
   tags="azure-resource-manager"/>
   
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="09/27/2016"
   ms.author="davidmu" />

# <a name="common-powershell-commands-for-creating-and-managing-vms"></a>Comandos do PowerShell comuns para criar e gerenciar VMs

Este artigo aborda alguns dos comandos do PowerShell do Azure que você pode usar para criar e gerenciar máquinas virtuais em sua assinatura do Azure.  Para obter ajuda mais detalhada com opções de linha de comando específicas e opções, você pode usar **Get-Help** *comando*.

Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter informações sobre como instalar a versão mais recente do PowerShell do Azure, selecionando sua assinatura e entrar sua conta.

## <a name="create-a-vm"></a>Criar uma máquina virtual

Tarefa | Comando
-------------- | -------------------------
Criar uma configuração de máquina virtual | $vm = [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) - VMName "vm_name" - VMSize "vm_size"<BR></BR><BR></BR>A configuração de máquina virtual é usada para definir ou atualizar as configurações para a máquina virtual. A configuração é iniciada com o nome do seu [tamanho](virtual-machines-windows-sizes.md)e a máquina virtual.
Adicionar configurações de configuração | $vm = [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) - máquina virtual $vm-Windows - ComputerName "nome do computador"-$cred - ProvisionVMAgent de credencial - EnableAutoUpdate<BR></BR><BR></BR>Configurações de sistema operacional incluindo [credenciais](https://technet.microsoft.com/library/hh849815.aspx) são adicionadas ao objeto de configuração que você criou anteriormente usando New-AzureRmVMConfig.
Adicionar uma interface de rede | $vm = [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) - máquina virtual $vm-NIC. $ de Id ID<BR></BR><BR></BR>Uma máquina virtual deve ter uma [interface de rede](virtual-machines-windows-ps-create.md) para se comunicar em uma rede virtual. Você também pode usar o [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) para recuperar um objeto de interface de rede existente.
Especificar uma imagem de plataforma | $vm = [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) - máquina virtual $vm - PublisherName "nome do Editor"-oferecer "publisher_offer" - Skus "product_sku"-"mais recente" versão<BR></BR><BR></BR>[Informações sobre a imagem](virtual-machines-windows-cli-ps-findimage.md) é adicionada ao objeto de configuração que você criou anteriormente usando New-AzureRmVMConfig. O objeto retornado por este comando é usado somente quando você define o disco de sistema operacional para usar uma imagem de plataforma.
Configurar o disco de sistema operacional para usar uma imagem de plataforma | $vm = [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) - máquina virtual $vm-- VhdUri de "nome_do_disco" nome "http://mystore1.blob.core.windows.net/vhds/disk_name.vhd" - CreateOption FromImage<BR></BR><BR></BR>O nome do disco do sistema operacional e sua localização em [armazenamento](../storage/storage-powershell-guide-full.md) é adicionado ao objeto de configuração que você criou anteriormente.
Configurar o disco de sistema operacional para usar uma imagem generalizada | $vm = set-AzureRmVMOSDisk - máquina virtual $vm-- SourceImageUri de "nome_do_disco" nome "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk. {guid}. vhd"- VhdUri"https://mystore1.blob.core.windows.net/vhds/disk_name.vhd"- CreateOption FromImage-Windows<BR></BR><BR></BR>O nome do disco do sistema operacional, o local da imagem de origem e local do disco em [armazenamento](../storage/storage-powershell-guide-full.md) é adicionado ao objeto de configuração.
Configurar o disco de sistema operacional para usar uma imagem especializada | $vm = set-AzureRmVMOSDisk - máquina virtual $vm-- VhdUri de "name_of_disk" nome "http://mystore1.blob.core.windows.net/vhds/" - CreateOption anexar - Windows
Criar uma máquina virtual | [Novo-AzureRmVM]() - ResourceGroupName "resource_group_name"-local "nome_do_local" - máquina virtual $vm<BR></BR><BR></BR>Todos os recursos são criados em um [grupo de recursos](../powershell-azure-resource-manager.md). A máquina virtual deve ser criada no mesmo [local](https://msdn.microsoft.com/library/azure/dn495177.aspx) como o grupo de recursos. Antes de executar este comando, execute New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, adicionar AzureRmVMNetworkInterface e Set-AzureRmVMOSDisk.

## <a name="get-information-about-vms"></a>Obter informações sobre VMs

Tarefa | Comando
-------------- | -------------------------
VMs de lista em uma assinatura| [Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx)
VMs de lista em um grupo de recursos | Get-AzureRmVM - ResourceGroupName "resource_group_name"<BR></BR><BR></BR>Para obter uma lista de grupos de recursos em sua assinatura, use [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx).
Obter informações sobre uma máquina virtual | Get-AzureRmVM - ResourceGroupName "resource_group_name"-nome "vm_name"

## <a name="manage-vms"></a>Gerenciar VMs

Tarefa | Comando
-------------- | -------------------------
Iniciar uma máquina virtual | [Início-AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) - ResourceGroupName "resource_group_name"-nome "vm_name"
Interromper uma máquina virtual | [Parar-AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) - ResourceGroupName "resource_group_name"-nome "vm_name"
Reiniciar uma máquina virtual em execução | [Reiniciar-AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) - ResourceGroupName "resource_group_name"-nome "vm_name"
Excluir uma máquina virtual | [Remover-AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) - ResourceGroupName "resource_group_name"-nome "vm_name"
Generalize uma máquina virtual | [Set-AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) - ResourceGroupName YourResourceGroup-nome "vm_name"-generalizado<BR></BR><BR></BR>Execute esse comando antes de executar AzureRmVMImage de salvar.
Capturar uma máquina virtual | [Salvar-AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) - ResourceGroupName "resource_group_name" - VMName "vm_name" - DestinationContainerName "image_container" - VHDNamePrefix "image_name_prefix"-caminho "C:\filepath\filename.json"<BR></BR><BR></BR>Uma máquina virtual deve ser [Desligar e generalizado](virtual-machines-windows-generalize-vhd.md) a ser usado para criar uma imagem. Antes de executar este comando, execute Set-AzureRmVm.
Atualizar uma máquina virtual | [Atualização-AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) - ResourceGroupName "resource_group_name" - máquina virtual $vm<BR></BR><BR></BR>Obter a configuração de máquina virtual atual usando Get-AzureRmVM, alterar definições de configuração do objeto de máquina virtual e, em seguida, execute este comando.
Adicionar um disco de dados a uma máquina virtual | [Adicionar AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) - máquina virtual $vm-- VhdUri de "nome_do_disco" nome "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" - LUN #-cache ReadWrite - DiskSizeinGB # - CreateOption vazio<BR></BR><BR></BR>Use Get-AzureRmVM para obter o objeto de máquina virtual. Especifica o número LUN e o tamanho do disco. Execute a atualização-AzureRmVM para aplicar as alterações de configuração para a máquina virtual. O disco que você adicionar não inicializado. Para obter informações sobre como inicializar discos conforme elas são adicionadas, consulte [máquinas virtuais do Azure gerenciar usando o Gerenciador de recursos e PowerShell](virtual-machines-windows-ps-manage.md).
Remover um disco de dados de uma máquina virtual | [Remover AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) - máquina virtual $vm-nome_do_disco"nome"<BR></BR><BR></BR>Use Get-AzureRmVM para obter o objeto de máquina virtual. Execute a atualização-AzureRmVM para aplicar as alterações de configuração para a máquina virtual.
Adicione uma extensão a uma máquina virtual | [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) - ResourceGroupName "resource_group_name"-local "azure_location" - VMName "vm_name"-nome "extension_name"-"nome do Editor" do Publisher-tipo "extension_type" - TypeHandlerVersion "#. #"-configurações $Settings - ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Execute este comando com as [informações de configuração](virtual-machines-windows-extensions-configuration-samples.md) apropriadas para a extensão que você deseja instalar.
Remover uma extensão de máquina virtual | [Remover-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) - ResourceGroupName "resource_group_name"-- VMName de "extension_name" nome "vm_name"

## <a name="next-steps"></a>Próximas etapas

- Consulte as etapas básicas para a criação de uma máquina virtual em [criar uma máquina de virtual do Windows usando o Gerenciador de recursos e PowerShell](virtual-machines-windows-ps-create.md).

