<properties
    pageTitle="Anexar um disco a uma máquina virtual | Microsoft Azure"
    description="Anexar um disco de dados a uma máquina virtual do Windows criada com o modelo clássico de implantação e inicializá-lo."
    services="virtual-machines-windows, storage"
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
    ms.date="06/27/2016"
    ms.author="cynthn"/>

# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Anexar um disco de dados a uma máquina virtual do Windows criada com o modelo clássico de implantação

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Se você quiser usar o novo portal, consulte [como anexar um disco de dados para uma máquina de virtual do Windows no portal do Azure](virtual-machines-windows-attach-disk-portal.md).

Se precisar de um disco de dados adicionais, você pode anexar um disco vazio ou um disco existente com dados a uma máquina virtual. Em ambos os casos, os discos são arquivos. vhd que residem em uma conta de armazenamento do Azure. No caso de um novo disco, depois de anexar o disco, você também precisará inicializá-lo para que ele está pronto para ser usado por uma máquina virtual Windows.

Para obter mais detalhes sobre discos, consulte [sobre discos e VHDs para máquinas virtuais](virtual-machines-windows-about-disks-vhds.md).


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a name="initialize-the-disk"></a>Inicializar o disco

1. Conectar-se à máquina virtual. Para obter instruções, consulte [como fazer logon em uma máquina virtual executando o Windows Server][logon].

2. Depois de fazer logon na máquina virtual, abra o **Gerenciador de servidor**. No painel esquerdo, selecione o **arquivo e serviços de armazenamento**.

    ![Abrir o Gerenciador de servidor](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. Expandir o menu e selecione **discos**.

4. A seção de **discos** listas os discos. Na maioria dos casos, ele terá disco 0, disco 1 e 2 do disco. Disco 0 é o sistema operacional, disco 1 é o disco temporário e disco 2 é o disco de dados anexado apenas para a máquina virtual. O novo disco de dados listará a partição como **desconhecido**. Clique com botão direito no disco e selecione **inicializar**.

5.  Você será notificado de que todos os dados serão apagados quando o disco for inicializado. Clique em **Sim** para confirmar o aviso e inicializar o disco. Após a conclusão, a partição será listada como **GPT**. Clique no disco novamente e selecione **Novo Volume**.

6.  Conclua o assistente usando os valores padrão. Quando o assistente for concluído, a seção de **Volumes** lista o novo volume. Agora, o disco está online e pronto para armazenar dados.

    ![Volume inicializado com êxito](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] O tamanho da máquina virtual determina quantos discos você pode anexar a ele. Para obter detalhes, consulte [tamanhos para máquinas virtuais](virtual-machines-linux-sizes.md).

## <a name="additional-resources"></a>Recursos adicionais

[Como desanexar um disco de máquina virtual do Windows](virtual-machines-windows-classic-detach-disk.md)

[Sobre discos e VHDs para máquinas virtuais](virtual-machines-linux-about-disks-vhds.md)

[logon]: virtual-machines-windows-classic-connect-logon.md
