<properties
    pageTitle="Criar uma máquina virtual no portal do clássico | Microsoft Azure"
    description="Crie uma máquina virtual Windows no portal de clássico do Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="cynthn"/>

# <a name="create-a-virtual-machine-running-windows-in-the-azure-classic-portal"></a>Criar uma máquina virtual executando o Windows no portal de clássico do Azure

> [AZURE.SELECTOR]
- [Azure portal clássico](virtual-machines-windows-classic-tutorial.md)
- [PowerShell: Implantação de clássico](virtual-machines-windows-classic-create-powershell.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Saiba como [executar essas etapas usando o modelo de implantação do Gerenciador de recursos](virtual-machines-windows-hero-tutorial.md) usando o **novo portal Azure**. 

Este tutorial mostra como criar uma máquina virtual Azure (máquina virtual) executando o Windows no portal de clássico do Azure. Vamos usar uma imagem do Windows Server como exemplo, mas isso é apenas uma das várias imagens que Azure oferece. Observe que as opções de imagem dependem de sua assinatura. Por exemplo, imagens da área de trabalho do Windows podem estar disponíveis para os assinantes do MSDN.

Esta seção mostra como usar a opção **De galeria** no portal de clássico do Azure para criar a máquina virtual. Esta opção fornece mais opções de configuração que a opção **Criar rápida** . Por exemplo, se você quiser participar de uma máquina virtual com uma rede virtual, você precisará usar a opção **De galeria** .

Você também pode criar VMs usando [suas próprias imagens](virtual-machines-windows-classic-createupload-vhd.md). Para saber mais sobre esse e outros métodos, consulte [maneiras diferentes de criar uma máquina virtual Windows](virtual-machines-windows-creation-choices.md).



## <a name="video-walkthrough"></a>Vídeo passo a passo

Aqui está um passo a passo deste tutorial.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>Criar a máquina virtual

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar uma máquina virtual usando o modelo de implantação do Gerenciador de recursos](virtual-machines-windows-hero-tutorial.md) no novo portal do Azure. 

- Faça logon na máquina virtual. Para obter instruções, consulte [logon em uma máquina virtual executando o Windows Server](virtual-machines-windows-classic-connect-logon.md).

- Anexe um disco para armazenar dados. Você pode anexar tanto vazios discos e que contêm dados. Para obter instruções, consulte [anexar um disco de dados para uma máquina virtual do Windows criado com o modelo de implantação clássico](virtual-machines-windows-classic-attach-disk.md).
