<properties
    pageTitle="Desanexar um disco de dados de uma máquina virtual Windows | Microsoft Azure"
    description="Aprenda a desanexar um disco de dados de uma máquina virtual no Azure usando o modelo de implantação do Gerenciador de recursos."
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
    ms.date="09/27/2016"
    ms.author="cynthn"/>



# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Como desanexar um disco de dados de uma máquina virtual Windows


Quando você precisa mais de um disco de dados que está anexado a uma máquina virtual, é possível desanexá-lo facilmente. Isso remove o disco de máquina virtual, mas não removê-lo do armazenamento. 

> [AZURE.WARNING] Se você desanexar um disco que não é excluída automaticamente. Se você se inscreveu ao armazenamento de Premium, você continuará a pagar encargos de armazenamento do disco. Para obter mais informações consulte [preços e cobrança ao usar o armazenamento de Premium](../storage/storage-premium-storage.md#pricing-and-billing). 

Se você quiser usar os dados existentes no disco novamente, você pode anexe-o a mesma máquina virtual ou outro.  


## <a name="detach-a-data-disk-using-the-portal"></a>Desanexar um disco de dados usando o portal

1. No hub do portal, selecione **máquinas virtuais**.

2. Selecione a máquina virtual que tem o disco de dados que você deseja desconectar e, em seguida, clique em **todas as configurações**.

3. Na lâmina **configurações** , selecione **discos**.

4. Na lâmina **discos** , selecione o disco de dados que você gostaria de desanexar.

5. Na lâmina para o disco de dados, clique em **Desanexar**.


    ![Captura de tela mostrando o botão desanexar.](./media/virtual-machines-windows-detach-disk/detach-disk.png)

O disco permanece em armazenamento, mas não esteja conectado a uma máquina virtual.


## <a name="detach-a-data-disk-using-powershell"></a>Desanexar um disco de dados usando o PowerShell

Neste exemplo, o primeiro comando obtém a máquina virtual chamada **MyVM07** no grupo de recursos **RG11** usando o cmdlet Get-AzureRmVM. O comando armazena na máquina virtual na variável **$VirtualMachine** . 

O segundo comando remove o disco de dados denominado DataDisk3 na máquina virtual. 

O comando final atualiza o estado da máquina virtual para concluir o processo de remoção do disco de dados.

    $VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" 
    Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
    Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine


Para obter mais informações, consulte [Remover AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx)

## <a name="next-steps"></a>Próximas etapas

Se você quiser reutilizar o disco de dados, você pode apenas [anexá-lo para máquina virtual outro](virtual-machines-windows-attach-disk-portal.md)
