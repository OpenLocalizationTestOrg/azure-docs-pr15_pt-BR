<properties
    pageTitle="Solucionar problemas de erros quando você excluir contas de armazenamento do Azure, contêineres ou VHDs em uma implantação do Gerenciador de recursos | Microsoft Azure"
    description="Solucionar problemas de erros quando você excluir contas de armazenamento do Azure, contêineres ou VHDs em uma implantação do Gerenciador de recursos"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="na"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="genli"/>

# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds-in-a-resource-manager-deployment"></a>Solucionar problemas de erros quando você excluir contas de armazenamento do Azure, contêineres ou VHDs em uma implantação do Gerenciador de recursos

[AZURE.INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

Você pode receber erros quando você tenta excluir uma conta de armazenamento do Azure, contêiner ou disco rígido virtual (VHD) no [portal do Azure](https://portal.azure.com). Este artigo fornece orientação para solução de problemas para ajudar a resolver o problema em uma implantação do Gerenciador de recursos do Azure.

Se este artigo não resolver seu problema Azure, visite os fóruns do Azure no [MSDN e estouro de pilha](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou para @AzureSupport no Twitter. Além disso, você pode enviar uma solicitação de suporte Azure selecionando **obter suporte** no site de [suporte do Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Sintomas

### <a name="scenario-1"></a>Cenário 1

Quando você tenta excluir um VHD em uma conta de armazenamento em uma implantação do Gerenciador de recursos, você recebe a seguinte mensagem de erro:

**Falha ao excluir blob 'vhds/BlobName.vhd'. Erro: Há atualmente uma concessão no blob e nenhuma ID de concessão foi especificada na solicitação.**

Esse problema pode ocorrer porque uma máquina virtual (VM) tem uma concessão no VHD que você está tentando excluir.

### <a name="scenario-2"></a>Cenário 2

Quando você tenta excluir um contêiner de uma conta de armazenamento em uma implantação do Gerenciador de recursos, você recebe a seguinte mensagem de erro:

**Falha ao excluir o contêiner de armazenamento 'vhds'. Erro: Há atualmente uma concessão no contêiner e nenhuma ID de concessão foi especificada na solicitação.**

Esse problema pode ocorrer porque o contêiner tem um VHD que está bloqueado no estado concessão.

### <a name="scenario-3"></a>Cenário 3

Quando você tenta excluir uma conta de armazenamento em uma implantação do Gerenciador de recursos, você recebe a seguinte mensagem de erro:

**Falha ao excluir a conta de armazenamento 'StorageAccountName'. Erro: A conta de armazenamento não pode ser excluída devido a seus artefatos sendo utilizado.**

Esse problema pode ocorrer porque a conta de armazenamento contém um VHD que o estado de concessão.

## <a name="solution"></a>Solução

Para resolver esses problemas, você deve identificar o VHD que está causando o erro e a máquina virtual associada. Em seguida, desconectar o VHD na máquina virtual (discos de dados) ou excluir a máquina virtual que está usando o VHD (para discos de SO). Isso remove a concessão de VHD e permite que seja excluída.

### <a name="step-1-identify-the-problem-vhd-and-the-associated-vm"></a>Etapa 1: Identificar o problema VHD e a máquina virtual associada


1. Entrar no [portal do Azure](https://portal.azure.com).
2. No menu **Hub** , selecione **todos os recursos**. Vá para a conta de armazenamento que você deseja excluir e selecione **Blobs** > **vhds**.

    ![Captura de tela do portal do, com a conta de armazenamento e o contêiner de "vhds" realçado](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)

3. Verifique as propriedades de cada VHD no contêiner. Localize o VHD que o estado de **Leased** . Em seguida, determinar qual máquina virtual está usando o VHD. Geralmente, você pode determinar qual máquina virtual detém o VHD marcando o nome do VHD:

    - Discos de SO geralmente seguem essa convenção de nomenclatura: VMNameYYYYMMDDHHMMSS.vhd
    - Discos de dados geralmente seguem essa convenção de nomenclatura: VMName-AAAAMMDD-HHMMSS.vhd

    ![Captura de tela das informações de contêiner no portal do, com o nome da máquina virtual, o status de concessão de "Bloqueadas" e o estado de concessão de "Leased" realçado](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/locatevm.png)

### <a name="step-2-remove-the-lease-from-the-vhd"></a>Etapa 2: Remover a concessão de VHD

Para excluir a máquina virtual que está usando o VHD (para discos do sistema operacional):

1.  Entrar no [portal do Azure](https://portal.azure.com).
2.  No menu **Hub** , selecione **máquinas virtuais**.
3.  Selecione a máquina virtual que detém uma concessão no VHD.
4.  Certifique-se de que nada esteja usando ativamente a máquina virtual, e que você não precisar da máquina virtual.
5.  Na parte superior da lâmina **detalhes de máquina virtual** , selecione **Excluir**e, em seguida, clique em **Sim** para confirmar.
6.  A máquina virtual deve ser excluída, mas o VHD deve ser mantido. No entanto, o VHD já não deve ter uma concessão nele. Pode levar alguns minutos para a concessão ser lançado. Para verificar que a concessão foi lançada, vá para **todos os recursos** > **Nome da conta de armazenamento** > **Blobs** > **vhds**. No painel **Propriedades de Blob** , o valor de **Status de concessão** deve ser **desbloqueado**.

Para desconectar o VHD da máquina virtual que está usando (para discos de dados):

1.  Entrar no [portal do Azure](https://portal.azure.com).
2.  No menu **Hub** , selecione **máquinas virtuais**.
3.  Selecione a máquina virtual que detém uma concessão no VHD.
4.  Selecione **discos** na lâmina **detalhes de máquina virtual** .
5.  Selecione o disco de dados que contém uma concessão no VHD. Você pode determinar qual VHD está conectado no disco marcando a URL do VHD.
6.  Determine com certeza que nada estiver usando ativamente o disco de dados.
7.  Clique em **Desanexar** na lâmina **detalhes do disco** .
8.  O disco agora deve ser desanexado da máquina virtual, e o VHD já não deve ter uma concessão nele. Pode levar alguns minutos para a concessão ser lançado. Para verificar que a concessão foi lançada, vá para **todos os recursos** > **Nome da conta de armazenamento** > **Blobs** > **vhds**. No painel **Propriedades de Blob** , o valor de **Status de concessão** deve ser **desbloqueado**.

## <a name="what-is-a-lease"></a>O que é uma concessão?

Uma concessão é um bloqueio que pode ser usado para controlar o acesso a um blob (por exemplo, um VHD). Quando um blob é concedido, somente os proprietários da concessão podem acessar o blob. Uma concessão é importante pelos seguintes motivos:

-   Ele impede corrupção de dados se vários proprietários tentam escrever a mesma parte do blob ao mesmo tempo.
-   Ela impede que o blob sendo excluído se algo estiver usando ativamente (por exemplo, uma máquina virtual).
-   Ela impede que a conta de armazenamento está sendo excluído se algo estiver usando ativamente (por exemplo, uma máquina virtual).



## <a name="next-steps"></a>Próximas etapas

- [Excluir uma conta de armazenamento](storage-create-storage-account.md#delete-a-storage-account)
- [Como interromper a concessão bloqueada do armazenamento de blob do Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)
