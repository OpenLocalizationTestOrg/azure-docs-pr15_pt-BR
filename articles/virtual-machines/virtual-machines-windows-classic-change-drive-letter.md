<properties
    pageTitle="Verifique a unidade d: de uma máquina virtual um disco de dados | Microsoft Azure"
    description="Descreve como alterar as letras de unidade para uma máquina virtual Windows para que você possa usar a unidade d: como uma unidade de dados."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Usar a unidade d: como uma unidade de dados em uma máquina virtual Windows 

Se seu aplicativo precisar usar unidade D para armazenar dados, siga estas instruções para usar uma letra de unidade diferente para o disco temporário. Nunca use o disco temporário para armazenar os dados que você precisa manter.

Se você redimensionar ou **parada (desalocar)** uma máquina virtual, isso pode disparar posicionamento da máquina virtual para um novo hipervisor. Um evento de manutenção planejada ou não também pode disparar esse posicionamento. Neste cenário, o disco temporário será reatribuído à primeira letra de unidade disponível. Se você tiver um aplicativo que requer especificamente a unidade d:, você precisa siga estas etapas para mover o Pagefile temporariamente, anexar um novo disco de dados e atribuí-la a letra D e mova o Pagefile de volta para a unidade temporária. Após a conclusão, Azure não terão novamente a d: se a máquina virtual move para um hipervisor diferentes.

Para obter mais informações sobre como o Azure usa o disco temporário, consulte [Noções básicas sobre a unidade temporária em máquinas virtuais do Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="attach-the-data-disk"></a>Conecte o disco de dados

Primeiro, você precisará anexar o disco de dados na máquina virtual. 

- Para usar o portal do, consulte [como anexar um disco de dados no portal do Azure](virtual-machines-windows-attach-disk-portal.md)
- Para usar o portal clássico, consulte [como anexar um disco de dados para uma máquina virtual Windows](virtual-machines-windows-classic-attach-disk.md). 


## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Mover temporariamente Pagefile a unidade C

1. Conectar-se à máquina virtual. 

2. Clique com botão direito no menu **Iniciar** e selecione **sistema**.

3. No menu à esquerda, selecione **Configurações avançadas do sistema**.

4. Na seção de **desempenho** , selecione **configurações**.

5. Selecione a guia **Avançado** .

5. Na seção **Memória Virtual** , selecione **Alterar**.

6. Selecione a unidade **C** e, em seguida, clique em **Tamanho gerenciado pelo sistema** e clique em **Definir**.

7. Selecione a unidade **D** e, em seguida, clique em **Nenhum arquivo de paginação** e clique em **Definir**.

8. Clique em Aplicar. Você receberá um aviso de que o computador precisa ser reiniciado para que as alterações entrem em vigor.

9. Reinicie a máquina virtual.




## <a name="change-the-drive-letters"></a>Alterar as letras de unidade 

1. Depois de reinicia a máquina virtual, faça logon novamente a máquina virtual.

2. Clique no menu **Iniciar** e digite **diskmgmt. msc** e pressione Enter. Gerenciamento de disco será iniciado.

3. Clique com botão direito em **D**, a unidade de armazenamento temporário e selecione **Alterar letra de unidade e caminhos**.

4. Em letra da unidade, selecione a unidade **G** e clique em **Okey**. 

5. Clique com botão direito no disco de dados e selecione **Alterar letra de unidade e caminhos**.

6. Em letra da unidade, selecione a unidade de disco **D** e clique em **Okey**. 

7. Clique com botão direito em **G**, a unidade de armazenamento temporário e selecione **Alterar letra de unidade e caminhos**.

8. Em letra da unidade, selecione a unidade **E** e clique em **Okey**. 

> [AZURE.NOTE] Se sua máquina virtual tem outros discos ou unidades, use o mesmo método para reatribuir as letras de unidade dos outros discos e unidades. Você quer que a configuração de disco seja:  
>- C: disco do sistema operacional de  
>- D: dados disco  
>- E: disco temporário



## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Mover Pagefile de volta para a unidade de armazenamento temporário 

1. Clique com botão direito no menu **Iniciar** e selecione **sistema**

2. No menu à esquerda, selecione **Configurações avançadas do sistema**.

3. Na seção de **desempenho** , selecione **configurações**.

4. Selecione a guia **Avançado** .

5. Na seção **Memória Virtual** , selecione **Alterar**.

6. Selecione a unidade **C** do sistema operacional e clique em **Nenhum arquivo de paginação** e clique em **Definir**.

7. Selecione a unidade de armazenamento temporário **E** , em seguida, clique em **Tamanho gerenciado pelo sistema** e clique em **Definir**.

8. Clique em **Aplicar**. Você receberá um aviso de que o computador precisa ser reiniciado para que as alterações entrem em vigor.

9. Reinicie a máquina virtual.




## <a name="next-steps"></a>Próximas etapas
- Você pode aumentar o armazenamento disponível para sua máquina virtual anexando [um disco de dados adicionais](virtual-machines-windows-attach-disk-portal.md).



