<properties
    pageTitle="Criar e carregar uma imagem de máquina virtual usando o Powershell | Microsoft Azure"
    description="Aprenda a criar e carregar uma imagem do Windows Server generalizada (VHD) usando o modelo clássico de implantação e o Azure Powershell."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/21/2016"
    ms.author="cynthn"/>

# <a name="create-and-upload-a-windows-server-vhd-to-azure"></a>Criar e carregar um VHD de servidor do Windows Azure

Este artigo mostra como carregar sua própria imagem de máquina virtual generalizada como um disco rígido virtual (VHD) para poder usá-lo para criar máquinas virtuais. Para obter mais detalhes sobre discos e VHDs no Microsoft Azure, consulte [sobre discos e VHDs para máquinas virtuais](virtual-machines-linux-about-disks-vhds.md).


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]. Você também pode [carregar](virtual-machines-windows-upload-image.md) uma máquina virtual usando o modelo do Gerenciador de recursos. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tenha:

- **Assinatura de um Azure** - se você não tiver um, você pode [Abrir uma conta do Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F).

- **[Microsoft Azure PowerShell](../powershell-install-configure.md)** - você possui o módulo Microsoft Azure PowerShell instalado e configurado para usar sua assinatura. 

- **A . Arquivo VHD** - operacional sistema armazenados em um arquivo. vhd e anexada a uma máquina virtual do Windows com suporte. Verifique se as funções de servidor em execução no VHD são suportadas pelo Sysprep. Para obter mais informações, consulte [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [AZURE.IMPORTANT] Não há suporte para o formato VHDX no Microsoft Azure. Você pode converter o disco em formato VHD usando o Gerenciador de Hyper-V ou o [cmdlet VHD Convert](http://technet.microsoft.com/library/hh848454.aspx). Para obter detalhes, consulte este [blogpost](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## <a name="step-1-prep-the-vhd"></a>Etapa 1: Preparar o VHD 

Antes de carregar o VHD Azure, ele precisa ser generalizado usando a ferramenta Sysprep. Prepara o VHD a ser usado como uma imagem. Para obter detalhes sobre Sysprep, consulte [como usar Sysprep: uma introdução](http://technet.microsoft.com/library/bb457073.aspx). Faça backup a máquina virtual antes de executar Sysprep.

Na máquina virtual que o sistema operacional foi instalado, conclua o procedimento a seguir:

1. Entrar no sistema operacional.

2. Abra uma janela do prompt de comando como administrador. Altere o diretório para **%windir%\system32\sysprep**e execute `sysprep.exe`.

    ![Abra uma janela do Prompt de comando](./media/virtual-machines-windows-classic-createupload-vhd/sysprep_commandprompt.png)

3.  A caixa de diálogo da **Ferramenta de preparação do sistema** é exibida.

    ![Inicie Sysprep](./media/virtual-machines-windows-classic-createupload-vhd/sysprepgeneral.png)

4.  Na **Ferramenta de preparação do sistema**, selecione **Inserir sistema ausência (OOBE)** e verifique se que **Generalize** está marcada.

5.  Nas **Opções de desligamento**, selecione **Desligar**.

6.  Clique em **Okey**.

## <a name="step-2-create-a-storage-account-and-a-container"></a>Etapa 2: Criar uma conta de armazenamento e um contêiner

É necessário uma conta de armazenamento do Azure para que você tenha um local para carregar o arquivo. vhd. Esta etapa mostra como criar uma conta ou obtenha as informações que você precisa de uma conta existente. Substituir as variáveis em &lsaquo; colchetes &rsaquo; por suas próprias informações.

1. Fazer login

        Add-AzureAccount

1. Defina sua assinatura do Azure.

        Select-AzureSubscription -SubscriptionName <SubscriptionName> 

2. Crie uma nova conta de armazenamento. O nome da conta de armazenamento deve ser exclusivo, 3-24 caracteres. O nome pode ser qualquer combinação de letras e números. Você também precisará especificar um local como "Leste EUA"
        
        New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>

3. Defina a nova conta de armazenamento como padrão.
        
        Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>

4. Crie um novo recipiente.

        New-AzureStorageContainer -Name <ContainerName> -Permission Off

 

## <a name="step-3-upload-the-vhd-file"></a>Etapa 3: Carregar o arquivo. vhd

Use o [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) para carregar o VHD.

Na janela do PowerShell do Azure você usou na etapa anterior, digite o seguinte comando e substitua as variáveis no &lsaquo; colchetes &rsaquo; por suas próprias informações.

        Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>


## <a name="step-4-add-the-image-to-your-list-of-custom-images"></a>Etapa 4: Adicionar a imagem à sua lista de imagens personalizadas

Use o cmdlet [Add-AzureVMImage](https://msdn.microsoft.com/library/mt589167.aspx) para adicionar a imagem à lista de suas imagens personalizadas.

        Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"


## <a name="next-steps"></a>Próximas etapas

Você pode agora [criar uma máquina virtual personalizada](virtual-machines-windows-classic-createportal.md) usando a imagem que você carregou.

