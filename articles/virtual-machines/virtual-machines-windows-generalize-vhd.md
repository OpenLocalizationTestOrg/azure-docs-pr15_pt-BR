<properties
    pageTitle="Generalize Windows VHD | Microsoft Azure"
    description="Aprenda a usar Sysprep generalizar uma máquina de virtual do Windows para usar com o modelo de implantação do Gerenciador de recursos."
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
    ms.date="10/20/2016"
    ms.author="cynthn"/>
    
    
    
    
# <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalize uma máquina virtual do Windows usando o Sysprep

Esta seção mostra como generalizar sua máquina de virtual do Windows para ser usado como uma imagem. Sysprep remove todas as suas informações de conta pessoal, entre outras coisas e prepara o computador para ser usado como uma imagem. Para obter detalhes sobre Sysprep, consulte [como usar Sysprep: uma introdução](http://technet.microsoft.com/library/bb457073.aspx).

Certificar-se de que as funções de servidor em execução no computador são suportadas pelo Sysprep. Para obter mais informações, consulte [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

>[AZURE.IMPORTANT] Se você estiver executando o Sysprep antes de carregar seu VHD no Azure pela primeira vez, verifique se que você tiver [preparado sua máquina virtual](virtual-machines-windows-prepare-for-upload-vhd-image.md) antes de executar Sysprep. 

1. Acesse o computador virtual Windows.

2. Abra a janela do Prompt de comando como administrador. Altere o diretório para **%windir%\system32\sysprep**e execute `sysprep.exe`.

3. Na caixa de diálogo **Ferramenta de preparação do sistema** , selecione **Enter System prontos da experiência (OOBE)**e verifique se a caixa de seleção **Generalize** está marcada.

4. Nas **Opções de desligamento**, selecione **Desligar**.

5. Clique em **Okey**.

    ![Inicie Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

6. Quando Sysprep for concluído, ele desliga na máquina virtual. 

## <a name="next-steps"></a>Próximas etapas

- Se a máquina virtual for local, você pode agora [carregar o VHD no Azure](virtual-machines-windows-upload-image.md).
- Se a máquina virtual já estiver no Azure, você pode agora [criar uma imagem da máquina virtual generalizada](virtual-machines-windows-capture-image.md).