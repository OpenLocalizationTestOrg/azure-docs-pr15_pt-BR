<properties
    pageTitle="Capture uma imagem de uma VM do Windows Azure | Microsoft Azure"
    description="Capture uma imagem de uma máquina virtual do Windows Azure criada com o modelo de implantação clássico."
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
    ms.date="09/27/2016"
    ms.author="cynthn"/>

#<a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Capture uma imagem de uma máquina virtual do Windows Azure criada com o modelo de implantação clássico.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para obter informações de modelo do Gerenciador de recursos, consulte [criar uma cópia máquina virtual do Windows em execução no Azure](virtual-machines-windows-vhd-copy.md).


Este artigo mostra como capturar uma máquina virtual Azure executando o Windows para poder usá-lo como uma imagem para criar outras máquinas virtuais. Esta imagem inclui o disco de sistema operacional e qualquer disco de dados que está anexado à máquina virtual. Ele não inclui configurações de rede, portanto você precisará configurar aqueles quando você cria as outras máquinas virtuais que usa a imagem.

Azure armazena a imagem em **Minhas imagens**. Este é o mesmo local onde as imagens que você carregou estão armazenadas. Para obter detalhes sobre imagens, consulte [sobre imagens para máquinas virtuais](virtual-machines-linux-classic-about-images.md).

##<a name="before-you-begin"></a>Antes de começar##

Estas etapas pressupõem que você já tiver criado uma máquina virtual Azure e configurado o sistema operacional, incluindo como anexar qualquer disco de dados. Se você ainda não tiver feito isso ainda, consulte estas instruções:

- [Criar uma máquina virtual a partir de uma imagem](virtual-machines-windows-classic-createportal.md)
- [Como anexar um disco de dados a uma máquina virtual](virtual-machines-windows-classic-attach-disk.md)
- Certificar-se de que as funções de servidor são compatíveis com Sysprep. Para obter mais informações, consulte [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [AZURE.WARNING] Esse processo exclui da máquina virtual original depois que ela é capturada. 

Antes de caputuring uma imagem de uma máquina virtual Azure, é recomendável backup na máquina virtual de destino. Azure máquinas virtuais pode ser feitas usando o Backup do Azure. Para obter detalhes, consulte [fazer backup Azure máquinas virtuais](../backup/backup-azure-vms.md). Outras soluções estão disponíveis em parceiros certificados. Para descobrir o que está disponível no momento, pesquise do Azure Marketplace.


##<a name="capture-the-virtual-machine"></a>Capturar a máquina virtual

1. No [portal de clássico Azure](http://manage.windowsazure.com), **Conectar** na máquina virtual. Para obter instruções, consulte [como entrar uma máquina virtual executando o Windows Server] [].

2.  Abra uma janela do Prompt de comando como administrador.

3.  Altere o diretório para `%windir%\system32\sysprep`, e execute sysprep.exe.

4.  A caixa de diálogo da **Ferramenta de preparação do sistema** é exibida. Faça o seguinte:

    - Em **Ação de limpeza do sistema**, selecione **Enter System prontos da experiência (OOBE)** e certifique-se de que **Generalize** está marcada. Para obter mais informações sobre como usar o Sysprep, consulte [como usar Sysprep: uma introdução][].

    - Nas **Opções de desligamento**, selecione **Desligar**.

    - Clique em **Okey**.

    ![Executar o Sysprep](./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png)

7.  Sysprep desliga da máquina virtual, que altera o status da máquina virtual no portal de clássico do Azure **interrompido**.

8.  No portal do Azure clássico, clique em **máquinas virtuais** e selecione a máquina virtual que você deseja capturar.

9.  Na barra de comandos, clique em **capturar**.

    ![Capturar máquina virtual](./media/virtual-machines-windows-classic-capture-image/CaptureVM.png)

    A caixa de diálogo **capturar a máquina Virtual** aparece.

10. Em **Nome da imagem**, digite um nome para a nova imagem.

11. Antes de adicionar uma imagem do Windows Server para seu conjunto de imagens personalizadas, ele deve ser generalizado executando o Sysprep conforme as instruções nas etapas anteriores. Clique em **para executar o Sysprep na máquina virtual** para indicar que fazia isso.

12. Clique na marca de seleção para capturar uma imagem. A nova imagem agora está disponível em **imagens**.

    ![Captura de imagem bem-sucedida](./media/virtual-machines-windows-classic-capture-image/VMCapturedImageAvailable.png)

##<a name="next-steps"></a>Próximas etapas

A imagem está pronta para ser usada para criar máquinas virtuais. Para fazer isso, você vai criar uma máquina virtual usando o item de menu **Da Galeria** e selecionando a imagem que você acabou de criar. Para obter instruções, consulte [criar uma máquina virtual de uma imagem](virtual-machines-windows-classic-createportal.md).



[Como entrar uma máquina virtual executando o Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Como usar o Sysprep: uma introdução]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-windows-classic-capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
