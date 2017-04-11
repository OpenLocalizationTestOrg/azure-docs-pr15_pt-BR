<properties
    pageTitle="Criar uma imagem de RemoteApp Azure com base em uma máquina virtual do Azure | Microsoft Azure"
    description="Saiba como criar uma imagem para Azure RemoteApp começando por uma máquina virtual Azure."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="create-a-azure-remoteapp-image-based-on-an-azure-virtual-machine"></a>Criar uma imagem de RemoteApp Azure com base em uma máquina virtual Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Você pode criar imagens de Azure RemoteApp (que mantenha os aplicativos que você compartilha em seu conjunto) de uma máquina virtual Azure. Você também pode optar por usar uma imagem de máquina virtual que adicionamos à Galeria de imagens de máquina virtual do Azure que atende a todos os requisitos de imagem de RemoteApp Azure - você pode usar essa imagem de máquina virtual como ponto de partida para sua própria máquina virtual, se desejar. Basta procure a imagem de "Windows Server Desktop Host da sessão remota" na biblioteca.

Há duas etapas para criar sua própria imagem com base em uma máquina virtual do Azure - criar a imagem e carregue-o da biblioteca de máquina virtual do Azure em RemoteApp do Azure.

## <a name="create-a-custom-image-based-on-an-azure-vm"></a>Criar uma imagem personalizada com base em uma máquina virtual do Azure

Use estas etapas para criar uma imagem com base em uma máquina virtual do Azure.

1. Crie uma máquina virtual Azure. Você pode usar o "Windows Server Desktop Host da sessão remota" ou "Windows Server remoto Desktop sessão Host com o Microsoft Office 365 ProPlus" imagem da Galeria de imagem Azure máquina virtual. Esta imagem atende a todos os requisitos de imagem de modelo RemoteApp do Azure.

    Para obter detalhes, consulte [criar uma máquina virtual executando o Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

2. Conectar-se para a máquina virtual e instalar e configurar os aplicativos que você deseja compartilhar através de RemoteApp. Certifique-se realizar configurações adicionais Windows necessárias para seus aplicativos.

    Para obter detalhes, consulte [como faça logon no Máquina Virtual executando o Windows Server](../virtual-machines/virtual-machines-windows-classic-connect-logon.md).

3. Se você estiver usando uma das imagens de Host de sessão de área de trabalho remota do Windows Server, há um script de validação incluído que garante que sua máquina virtual atende a RemoteApp pré-reqs. Para executar o script, clique duas vezes em **ValidateRemoteAppImage** na área de trabalho. Certifique-se de que todos os erros relatados pelo script são corrigidos antes de prosseguir para a próxima etapa.

4. SYSPREP generalize e capturar a imagem. Veja [como capturar uma máquina Virtual Windows utilizar como um modelo](../virtual-machines/virtual-machines-windows-classic-capture-image.md) para obter instruções.



## <a name="import-the-image-into-the-azure-remoteapp-image-library"></a>Importar a imagem para a biblioteca de imagens do Azure RemoteApp

Use estas etapas para importar a nova imagem para o Azure RemoteApp:

1. Na guia **Imagens de modelo** :
    - Se você tiver sem imagens existentes, clique em **carregar ou importar uma imagem de modelo**.
    - Se você já tiver pelo menos uma imagem, clique em **+** para adicionar uma nova imagem.

2. Selecione **Importar uma imagem de suas máquinas virtuais** biblioteca e clique em **Avançar**.

3. Na próxima página, selecione sua imagem personalizada na lista e confirme se você seguiu as etapas listadas quando você criou sua imagem. Clique em **Avançar**.
4. Insira um nome para a nova imagem de RemoteApp e escolha o local, clique na marca de seleção para iniciar o processo de importação.

> [AZURE.NOTE] Você pode importar imagens de qualquer local Azure suportada pelo máquinas virtuais do Azure para qualquer local Azure suportado pelo RemoteApp do Azure. Dependendo dos locais a importação pode levar até 25 minutos.

Agora você está pronto para criar sua nova coleção, ou uma coleção de [nuvem](remoteapp-create-cloud-deployment.md) ou [híbrido](remoteapp-create-hybrid-deployment.md), dependendo das suas necessidades.
