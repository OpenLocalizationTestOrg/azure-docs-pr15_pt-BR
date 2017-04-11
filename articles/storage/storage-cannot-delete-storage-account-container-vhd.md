<properties
    pageTitle="Solucionar problemas de exclusão de contas de armazenamento do Azure, contêineres ou VHDs em uma implantação clássica | Microsoft Azure"
    description="Solucionar problemas de exclusão de contas de armazenamento do Azure, contêineres ou VHDs em uma implantação clássica"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="tysonn"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="genli"/>

# <a name="troubleshoot-deleting-azure-storage-accounts-containers-or-vhds-in-a-classic-deployment"></a>Solucionar problemas de exclusão de contas de armazenamento do Azure, contêineres ou VHDs em uma implantação clássica

[AZURE.INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

Você pode receber erros quando você tenta excluir a conta de armazenamento do Azure, contêiner ou VHD no [portal do Azure](https://portal.azure.com/) ou o [Azure portal clássico](https://manage.windowsazure.com/). Os problemas podem ser causados por seguintes circunstâncias:

-   Quando você exclui uma máquina virtual, o disco e VHD não são automaticamente excluídos. Que pode ser o motivo Falha na exclusão da conta de armazenamento. Podemos não excluiu o disco para que você possa usar o disco para montar máquina virtual outra.

-   Ainda há uma concessão em um disco ou o blob associado com o disco.

Se seu problema Azure não endereçado neste artigo, visite os fóruns do Azure no [MSDN e estouro de pilha](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou para @AzureSupport no Twitter. Além disso, você pode enviar uma solicitação de suporte Azure selecionando **obter suporte** no site de [suporte do Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Sintomas

A seção a seguir lista os erros comuns que podem ser exibidas quando você tenta excluir as contas de armazenamento do Azure, contêineres ou VHDs.

### <a name="scenario-1-unable-to-delete-a-storage-account"></a>Cenário 1: Não é possível excluir uma conta de armazenamento

Quando você navegar para a conta de armazenamento no [portal do Azure](https://portal.azure.com/) ou [portal clássico Azure](https://manage.windowsazure.com/) e selecione **Excluir**, talvez você veja a seguinte mensagem de erro:

*Conta de armazenamento StorageAccountName contém imagens de máquina virtual. Certifique-se de que essas imagens de máquina virtual são removidas antes de excluir esta conta de armazenamento.*

Você também pode ver esse erro:

**O Azure portal**:

*Falha ao excluir a conta de armazenamento < máquina virtual armazenamento-nome da conta >. Não é possível excluir a conta de armazenamento < máquina virtual armazenamento-nome da conta >: ' conta de armazenamento < máquina virtual armazenamento-nome da conta > tem algumas imagens ativas e/ou s. Certifique-se de que essas imagens e/ou s são removidos antes de excluir esta conta de armazenamento.'.*

**O Azure clássico portal**:

*Conta de armazenamento < máquina virtual armazenamento-nome da conta > tem algumas imagens ativas e/ou s, por exemplo, xxxxxxxxx-xxxxxxxxx-O-209490240936090599. Certifique-se de essas imagens e/ou s são removidos antes de excluir esta conta de armazenamento.*

Ou

**O Azure portal**:

Conta de armazenamento *< máquina virtual armazenamento-nome da conta > tem 1 contêiner (s) que têm uma imagem ativa e/ou artefatos de disco. Certifique-se de que esses artefatos são removidos do repositório de imagens antes de excluir esta conta de armazenamento*.

**O Azure clássico portal**:

Conta de armazenamento de falha enviar *< máquina virtual armazenamento-nome da conta > tem 1 contêiner (s) que têm uma imagem ativa e/ou artefatos de disco. Certifique-se de que esses artefatos são removidos do repositório de imagens antes de excluir esta conta de armazenamento. Quando você tentar excluir uma conta de armazenamento e existem discos ainda ativos associados a ele, você verá uma mensagem informando existem discos ativos que precisam ser excluídas*.

### <a name="scenario-2-unable-to-delete-a-container"></a>Cenário 2: Não é possível excluir um contêiner

Quando você tenta excluir o contêiner de armazenamento, talvez você veja o seguinte erro:

*Falha ao excluir o contêiner de armazenamento <container name>. Erro: ' não há atualmente uma concessão no contêiner e nenhuma ID de concessão foi especificada na solicitação de*.

### <a name="scenario-3-unable-to-delete-a-vhd"></a>Cenário 3: Não é possível excluir um VHD

Depois de excluir uma máquina virtual e, em seguida, tentar excluir os blobs para os VHDs associados, você pode receber a seguinte mensagem:

*Falha ao excluir blob ' caminho/XXXXXX-XXXXXX-os-1447379084699.vhd'. Erro: ' atualmente há uma concessão no blob e nenhuma ID de concessão foi especificada na solicitação.*

## <a name="solution"></a>Solução
Para resolver os problemas mais comuns, tente o seguinte método:

### <a name="step-1-delete-any-os-disks-that-are-preventing-deletion-of-the-storage-account-container-or-vhd"></a>Etapa 1: Excluir qualquer disco de sistema operacional que não permitirem a exclusão da conta de armazenamento, contêiner ou VHD

1. Alternar para o [portal de clássico Azure](https://manage.windowsazure.com/).
2. Selecione **máquina VIRTUAL** > **discos**.

    ![Imagem de discos em máquinas virtuais nos Azure portal clássico.](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. Localize os discos que estão associados com a conta de armazenamento, contêiner ou VHD que você deseja excluir. Quando você verificar o local do disco, você encontrará a conta de armazenamento associado, contêiner ou VHD.

    ![Imagem que mostra as informações de localização para discos no Azure portal clássico](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. Confirme se não há máquina virtual está listado no campo **anexados** dos discos e exclua os discos.

    > [AZURE.NOTE] Se um disco é anexado a uma máquina virtual, você não poderá excluí-lo. Discos são destacados de uma máquina virtual excluída assíncrona. Poderá demorar alguns minutos após a máquina virtual é excluída para este campo Limpar.

### <a name="step-2-delete-any-vm-images-that-are-preventing-deletion-of-the-storage-account-or-container"></a>Etapa 2: Excluir quaisquer imagens de máquina virtual que não permitirem a exclusão da conta de armazenamento ou contêiner

1. Alternar para o [portal de clássico Azure](https://manage.windowsazure.com/).
2. Selecione **máquina VIRTUAL** > **imagens**e exclua as imagens que são associadas com a conta de armazenamento, contêiner ou VHD.

    Depois disso, tente excluir a conta de armazenamento, contêiner ou VHD novamente.

> [AZURE.WARNING] Certifique-se de fazer backup de qualquer coisa que desejar salvar antes de excluir a conta. Não é possível restaurar uma conta de armazenamento excluída ou recuperar qualquer conteúdo que continha antes da exclusão. Isso também acontece com todos os recursos na conta: quando você exclui um VHD, blob, tabela, fila ou arquivo, ele é permanentemente excluído. Certifique-se de que o recurso não está em uso.

## <a name="about-the-stopped-deallocated-status"></a>Sobre o status de parado (desalocado)

VMs que foram criadas no modelo clássico de implantação e que foram mantidas terá o status de **parado (desalocado)** no [portal do Azure](https://portal.azure.com/) ou [Azure portal clássico](https://manage.windowsazure.com/).

**Azure portal clássico**:

![Parado (Deallocated) status para VMs no portal do Azure.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)


**Azure portal**:

![Parado (liberada) status para VMs no Azure portal clássico.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

Um status de "Parado (desalocado)" lançamentos de recursos do computador, como a CPU, memória e rede. No entanto, os discos, ainda são mantidos para que você pode rapidamente recriar a máquina virtual se necessário. Esses discos são criados sobre VHDs, que contam com armazenamento do Azure. A conta de armazenamento tem estas VHDs e o disco tiver concessões nesses VHDs.

## <a name="next-steps"></a>Próximas etapas

- [Excluir uma conta de armazenamento](storage-create-storage-account.md#delete-a-storage-account)
- [Como interromper a concessão bloqueada do armazenamento de blob do Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)
