<properties
   pageTitle="Extensão de Script personalizado em uma máquina virtual Windows | Microsoft Azure"
   description="Automatizar tarefas de configuração de máquina virtual do Azure usando a extensão de Script personalizado para executar scripts do PowerShell em uma VM remoto do Windows"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="kundanap"/>

# <a name="custom-script-extension-for-windows-virtual-machines"></a>Extensão de Script personalizada para máquinas virtuais do Windows

Este artigo fornece uma visão geral de como usar a extensão de Script personalizado em Windows VMs usando cmdlets do PowerShell do Azure com APIs de gerenciamento de serviço do Azure.

Extensões de máquina virtual (VM) são criadas pela Microsoft e confiáveis fornecedores de terceiros para estender a funcionalidade da máquina virtual. Para obter uma visão geral das extensões de máquina virtual, consulte [recursos e extensões de máquina virtual do Azure](virtual-machines-windows-extensions-features.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Saiba como [executar essas etapas usando o modelo do Gerenciador de recursos](virtual-machines-windows-extensions-customscript.md).

## <a name="custom-script-extension-overview"></a>Visão geral sobre extensão de Script personalizado

Com a extensão de Script personalizado para Windows, você pode executar scripts do PowerShell em uma VM remota sem entrar para ele. Você pode executar os scripts após a máquina virtual de provisionamento ou a qualquer momento durante o ciclo de vida da máquina virtual sem abrir quaisquer portas adicionais. Os casos de uso mais comuns para execução de extensão de Script personalizado incluem executando, instalando e configurando o software adicional na máquina virtual depois que ela é provisionada.

### <a name="prerequisites-for-running-the-custom-script-extension"></a>Pré-requisitos para executar a extensão de Script personalizado

1. Instalar os <a href="http://azure.microsoft.com/downloads" target="_blank">cmdlets do PowerShell do Azure</a> versão 0.8.0 ou posterior.
2. Se desejar que os scripts sejam executados em uma máquina virtual existente, certifique-se de que agente de máquina virtual é habilitado na máquina virtual. Se não estiver instalado, siga estas [etapas](virtual-machines-windows-classic-agents-and-extensions.md). Se a máquina virtual for criada a partir do portal do Azure, agente de máquina virtual é instalado por padrão.
3. Carregue os scripts que você deseja executar na máquina virtual ao armazenamento do Azure. Os scripts podem vir de um único contêiner ou vários contêineres de armazenamento.
4. O script deve ser criado para que o script de entrada, que é iniciado pela extensão, inicia outros scripts.

## <a name="custom-script-extension-scenarios"></a>Cenários de extensão de Script personalizados

### <a name="upload-files-to-the-default-container"></a>Carregar arquivos para o recipiente padrão

O exemplo a seguir mostra como você pode executar scripts na máquina virtual se estiverem no contêiner de armazenamento da conta padrão da sua assinatura. Você carregar seus scripts em ContainerName. Você pode verificar a conta de armazenamento padrão usando o **Get-AzureSubscription – padrão** comando.

O exemplo a seguir cria uma máquina virtual, mas você também pode executar o mesmo cenário em uma máquina virtual existente.

    # Create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script extension to the VM. The container name refers to the storage container that contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### <a name="upload-files-to-a-non-default-storage-container"></a>Carregar arquivos para um contêiner de armazenamento não padrão

Este cenário mostra como usar um contêiner de armazenamento de não padrão na mesma assinatura ou em uma assinatura diferente para carregar arquivos e scripts. Este exemplo mostra uma máquina virtual existente, mas as mesmas operações podem ser feitas enquanto você estiver criando uma máquina virtual.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### <a name="upload-scripts-to-multiple-containers-across-different-storage-accounts"></a>Carregar scripts em vários contêineres entre contas de armazenamento diferentes

  Se os arquivos de script são armazenados em vários contêineres, você precisará fornecer a URL de assinaturas (SAS) de acesso compartilhado completo para os arquivos para executar os scripts.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### <a name="add-the-custom-script-extension-from-the-azure-portal"></a>Adicionar a extensão de Script personalizado do portal do Azure

Vá para a máquina virtual no <a href="https://portal.azure.com/ " target="_blank">portal do Azure</a> e adicione a extensão, especificando o arquivo de script para executar.

  ![Especifique o arquivo de script][5]


### <a name="uninstall-the-custom-script-extension"></a>Desinstalar a extensão de Script personalizado

Você pode desinstalar a extensão de Script personalizado da máquina virtual usando o comando a seguir.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### <a name="use-the-custom-script-extension-with-templates"></a>Usar a extensão de Script personalizado com modelos

Para saber mais sobre como usar a extensão de Script personalizado com modelos do Gerenciador de recursos do Azure, consulte [usando a extensão de Script personalizado para Windows VMs com modelos do Gerenciador de recursos do Azure](virtual-machines-windows-extensions-customscript.md).

<!--Image references-->
[5]: ./media/virtual-machines-windows-classic-extensions-customscript/addcse.png
