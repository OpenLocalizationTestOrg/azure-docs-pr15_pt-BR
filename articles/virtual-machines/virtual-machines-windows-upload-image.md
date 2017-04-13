<properties
    pageTitle="Carregar um VHD do Windows para o gerente de recursos | Microsoft Azure"
    description="Aprenda a carregar uma máquina virtual Windows VHD do local no Azure, usando o modelo de implantação do Gerenciador de recursos. Você pode carregar um VHD de tanto um generalizado ou uma máquina virtual especializada."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="cynthn"/>

# <a name="upload-a-windows-vhd-from-an-on-premises-vm-to-azure"></a>Carregar um VHD de uma máquina virtual local no Azure do Windows 


Este artigo mostra como criar e carregar um disco de rígido virtual do Windows (VHD) a ser usado na criação de uma máquina virtual do Azure. Você pode carregar um VHD de uma máquina virtual generalizada ou uma máquina virtual especializada. 

Para obter mais detalhes sobre discos e VHDs no Azure, consulte [sobre discos e VHDs para máquinas virtuais](virtual-machines-linux-about-disks-vhds.md).


## <a name="prepare-the-vm"></a>Preparar a máquina virtual 

Você pode carregar VHDs generalizadas e especializados no Azure. Cada tipo requer que você prepare a máquina virtual antes de iniciar.

- **VHD generalizado** - um VHD generalizado teve todas as suas informações de conta pessoal removidas usando Sysprep. Se você pretende usar o VHD como uma imagem para criar novas VMs de, faça o seguinte:
    - [Preparar um VHD do Windows para carregar no Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). 
    - [Generalize a máquina virtual usando o Sysprep](virtual-machines-windows-generalize-vhd.md). 

- **VHD especializados** - um VHD especializado mantém as contas de usuário, aplicativos e outros dados de estado de sua máquina virtual original. Se você pretende usar o VHD como-é criar uma nova VM, certifique-se de que as seguintes etapas são concluídas. 
    - [Preparar um VHD do Windows para carregar no Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). **Não** generalize a máquina virtual usando Sysprep.
    - Remova qualquer agentes que são instalados na máquina virtual (ou seja, ferramentas de VMware) e ferramentas de virtualização do convidado.
    - Certifique-se de que a máquina virtual está configurada para retirar suas configurações de DNS por meio de DHCP e o endereço IP. Isso garante que o servidor obtém um endereço IP dentro do VNet quando ele for iniciado. 

## <a name="log-in-to-azure"></a>Faça logon no Azure

Se você ainda não tiver o PowerShell versão 1,4 ou acima instalado, leia [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

1. Abra o PowerShell do Azure e entrar em sua conta do Azure. Abre uma janela pop-up para que você insira suas credenciais de conta do Microsoft Azure.

    ```powershell
    Login-AzureRmAccount
    ```


2. Obtenha a IDs de assinatura para suas assinaturas disponíveis.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Definir a assinatura correta usando a identificação de assinatura. Substituir `<subscriptionID>` com a ID da assinatura correta.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Obter a conta de armazenamento

Você precisa de uma conta de armazenamento do Azure para armazenar a imagem de máquina virtual carregada. Você pode usar uma conta de armazenamento existente ou crie um novo. 

Para mostrar as contas de armazenamento disponível, digite:

```powershell
Get-AzureRmStorageAccount
```

Se você quiser usar uma conta de armazenamento existente, vá para a seção de [carregar a imagem de máquina virtual](#upload-the-vm-vhd-to-your-storage-account) .

Se você precisa criar uma conta de armazenamento, siga estas etapas:

1. Você precisa do nome do grupo de recursos onde a conta de armazenamento deve ser criada. Para descobrir todos os grupos de recursos que estão em sua assinatura, digite:

    ```powershell
    Get-AzureRmResourceGroup
    ```

Para criar um grupo de recursos denominado **myResourceGroup** na região **Oeste EUA** , digite:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Crie uma conta de armazenamento denominada **mystorageaccount** este grupo de recursos usando o cmdlet [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) :

    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" -SkuName "Standard_LRS" -Kind "Storage"
    ```
            
    Valores válidos para SkuName - são:

    - **Standard_LRS** - armazenamento redundante localmente. 
    - **Standard_ZRS** - armazenamento redundante de zona.
    - **Standard_GRS** - armazenamento redundante localização geográfica. 
    - **Standard_RAGRS** - armazenamento redundante de localização geográfica de acesso de leitura. 
    - **Premium_LRS** - Premium localmente redundantes. 



## <a name="upload-the-vhd-to-your-storage-account"></a>Carregue o VHD para sua conta de armazenamento

Use o cmdlet [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) para carregar a imagem em um contêiner em sua conta de armazenamento. Este exemplo carrega o arquivo **myVHD.vhd** do `"C:\Users\Public\Documents\Virtual hard disks\"` em um armazenamento de conta denominada **mystorageaccount** no grupo de recursos **myResourceGroup** . O arquivo será colocado no contêiner chamado **mycontainer** e o novo nome do arquivo será **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se for bem-sucedido, você obterá uma resposta semelhante a esta:

```
  C:\> Add-AzureRmVhd -ResourceGroupName myResourceGroup -Destination https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
  MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
  MD5 hash calculation is completed.
  Elapsed time for the operation: 00:03:35
  Creating new page blob of size 53687091712...
  Elapsed time for upload: 01:12:49

  LocalFilePath           DestinationUri
  -------------           --------------
  C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Dependendo de sua conexão de rede e o tamanho do arquivo VHD, este comando pode demorar um pouco para ser concluída


## <a name="next-steps"></a>Próximas etapas

- [Criar uma máquina virtual no Azure a partir de um VHD generalizado](virtual-machines-windows-create-vm-generalized.md)
- [Criar uma máquina virtual no Azure a partir de um VHD especializado](virtual-machines-windows-create-vm-specialized.md) anexando-o como um disco de sistema operacional quando você cria uma nova VM.


