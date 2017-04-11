<properties
    pageTitle="Como criar, gerenciar ou excluir uma conta de armazenamento no Portal do Azure | Microsoft Azure"
    description="Criar uma nova conta de armazenamento, gerenciar suas chaves de acesso de conta ou excluir uma conta de armazenamento no Portal do Azure. Saiba mais sobre contas de armazenamento padrão e premium."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/26/2016"
    ms.author="robinsh"/>


# <a name="about-azure-storage-accounts"></a>Sobre contas de armazenamento do Azure

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools](../../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Visão geral

Uma conta de armazenamento do Azure fornece um namespace exclusivo para armazenar e acessar os objetos de dados de armazenamento do Azure. Todos os objetos em uma conta de armazenamento serão cobrados juntos como um grupo. Por padrão, os dados em sua conta está disponíveis somente para você, o proprietário da conta.

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

## <a name="storage-account-billing"></a>Cobrança de conta de armazenamento

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

> [AZURE.NOTE] Quando você cria uma máquina virtual Azure, uma conta de armazenamento é criada automaticamente no local de implantação se você ainda não tiver uma conta de armazenamento nesse local. Portanto, não é necessário seguir as etapas abaixo para criar uma conta de armazenamento para seus discos de máquina virtual. O nome da conta de armazenamento se baseará no nome da máquina virtual. Consulte a [documentação de máquinas virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) para obter mais detalhes.

## <a name="storage-account-endpoints"></a>Pontos de extremidade de conta de armazenamento

Cada objeto que você armazena no armazenamento do Azure tem um endereço de URL exclusivo. O nome da conta de armazenamento de formulários o subdomínio desse endereço. A combinação de nome de domínio e subdomínio, que é específico para cada serviço, constitui um *ponto de extremidade* para a sua conta de armazenamento.

Por exemplo, se sua conta de armazenamento é denominada *mystorageaccount*, os pontos de extremidade padrão para a sua conta de armazenamento são:

- Serviço de blob: http://*mystorageaccount*. blob.core.windows.net

- Serviço de tabela: http://*mystorageaccount*. table.core.windows.net

- Fila de serviço: http://*mystorageaccount*. queue.core.windows.net

- Serviço de arquivo: http://*mystorageaccount*. file.core.windows.net

> [AZURE.NOTE] Uma conta de armazenamento de Blob expõe apenas o ponto de extremidade do serviço de Blob.

O URL para acessar um objeto em uma conta de armazenamento baseia-se anexando o local do objeto na conta de armazenamento para o ponto de extremidade. Por exemplo, um endereço de blob pode ter este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Você também pode configurar um nome de domínio personalizado para usar com sua conta de armazenamento. Para contas de armazenamento clássico, consulte [Configurar um nome para seu ponto de extremidade de armazenamento de Blob de domínio personalizado](storage-custom-domain-name.md) para obter detalhes. Para contas de armazenamento do Gerenciador de recursos, esse recurso não foi adicionado ao [portal Azure](https://portal.azure.com) ainda, mas você pode configurá-lo com o PowerShell. Para obter mais informações, consulte o cmdlet [Set-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx) .  

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. Entrar no [portal do Azure](https://portal.azure.com).

2. No menu Hub, selecione **novo** -> **dados + armazenamento** -> **conta de armazenamento**.

3. Insira um nome para a sua conta de armazenamento. Consulte [pontos de extremidade de conta de armazenamento](#storage-account-endpoints) para obter detalhes sobre como o nome da conta de armazenamento será usado para os objetos no armazenamento do Azure de endereço.

    > [AZURE.NOTE] Nomes de conta de armazenamento devem estar entre 3 e 24 caracteres e podem conter números e apenas letras minúsculas.
    >  
    > O nome da sua conta de armazenamento deve ser exclusivo dentro do Azure. O portal do Azure indicará se o nome da conta de armazenamento que você selecionar já está em uso.

4. Especificar o modelo de implantação a ser usado: **Gerenciador de recursos** ou **clássico**. **Gerenciador de recursos** é o modelo de implantação recomendada. Para obter mais informações, consulte [Gerenciador de recursos de Noções básicas sobre implantação e implantação clássica](../resource-manager-deployment-model.md).

    > [AZURE.NOTE] Contas de armazenamento de blob só podem ser criadas usando o modelo de implantação do Gerenciador de recursos.

5. Selecione o tipo de conta de armazenamento: **armazenamento de Blob**ou **finalidade geral** . **Finalidade geral** é o padrão.

    Se **propósito geral** foi selecionado, em seguida, especifique o nível de desempenho: **padrão** ou **Premium**. O padrão é **padrão**. Para obter mais detalhes sobre contas de armazenamento padrão e premium, consulte [Introdução ao armazenamento do Microsoft Azure](storage-introduction.md) e [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](storage-premium-storage.md).

    Se o **Armazenamento de Blob** foi selecionado, em seguida, especifique o nível de acesso: **quente** ou **legais**. O padrão é **quente**. Consulte [armazenamento de Blob do Azure: interessantes e quente níveis](storage-blob-storage-tiers.md) para obter mais detalhes.

6. Selecione a opção de replicação para a conta de armazenamento: **LRS**, **GRS**, **Ar-GRS**ou **ZRS**. O padrão é **GRS ar**. Para obter mais detalhes sobre as opções de replicação de armazenamento do Azure, consulte [replicação de armazenamento do Azure](storage-redundancy.md).

7. Selecione a assinatura na qual você deseja criar a nova conta de armazenamento.

8. Especificar um novo grupo de recursos ou selecione um grupo de recursos existente. Para obter mais informações sobre grupos de recursos, consulte [Visão geral do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

9. Selecione a localização geográfica para sua conta de armazenamento. Consulte [Regiões do Azure](https://azure.microsoft.com/regions/#services) para obter mais informações sobre quais serviços estão disponíveis em que região.

10. Clique em **criar** para criar a conta de armazenamento.

## <a name="manage-your-storage-account"></a>Gerenciar sua conta de armazenamento

### <a name="change-your-account-configuration"></a>Alterar a configuração de conta

Depois de criar sua conta de armazenamento, você pode modificar sua configuração, como mudar a opção de replicação usada para a conta ou alterando o nível de acesso para uma conta de armazenamento de Blob. No [portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento, clique em **todas as configurações** e, em seguida, clique em **configuração** para exibir e/ou alterar a configuração de conta.

> [AZURE.NOTE] Dependendo o nível de desempenho que você escolheu ao criar a conta de armazenamento, algumas opções de replicação podem não estar disponíveis.

Alterando a opção de replicação mudará a seus preços. Para obter mais detalhes, consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) page.

Para contas de armazenamento de Blob, alterar o nível de acesso pode provoca encargos para que a alteração além de alterar os preços. Consulte as [contas de armazenamento de Blob - preços e cobrança](storage-blob-storage-tiers.md#pricing-and-billing) para obter mais detalhes.

### <a name="manage-your-storage-access-keys"></a>Gerenciar suas chaves de acesso de armazenamento

Quando você cria uma conta de armazenamento, o Azure gera duas teclas de acesso para armazenamento de 512 bits, que são usadas para autenticação quando a conta de armazenamento é acessada. Fornecendo duas teclas de acesso de armazenamento, Azure permite gerar as chaves com acesso a esse serviço ou sem interrupção do seu serviço de armazenamento.

> [AZURE.NOTE] Recomendamos que você evite suas chaves de acesso de armazenamento de compartilhamento com outras pessoas. Para permitir o acesso aos recursos de armazenamento sem divulgar suas chaves de acesso, você pode usar uma *assinatura de acesso compartilhado*. Uma assinatura de acesso compartilhado fornece acesso a um recurso em sua conta para um intervalo que você definir e com as permissões que você especificar. Consulte [Usando compartilhados acesso assinaturas (SAS)](storage-dotnet-shared-access-signature-part-1.md) para obter mais informações.

#### <a name="view-and-copy-storage-access-keys"></a>Exibir e copiar as teclas de acesso de armazenamento

No [portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento, clique em **todas as configurações** e clique em **teclas de acesso** para exibir, copiar e gerar suas chaves de acesso de conta. A lâmina de **Teclas de acesso** também inclui cadeias de caracteres de conexão pré-configurada usando suas chaves primárias e secundárias que você pode copiar para usar em seus aplicativos.

#### <a name="regenerate-storage-access-keys"></a>Gerar as teclas de acesso de armazenamento

Recomendamos que você altere as teclas de acesso à sua conta de armazenamento periodicamente para ajudar a manter suas conexões de armazenamento seguro. Duas teclas de acesso são atribuídas para que você pode manter conexões com a conta de armazenamento usando uma tecla de acesso enquanto você gerar tecla de acesso.

> [AZURE.WARNING] Gerar suas chaves de acesso pode afetar os serviços no Azure, bem como seus próprios aplicativos que dependem da conta de armazenamento. Todos os clientes que usam a chave de acesso para acessar a conta de armazenamento devem ser atualizados para usar a nova chave.

**Serviços de mídia** - se você tiver serviços de mídia que dependem de sua conta de armazenamento, você deve sincronize novamente as teclas de acesso com o seu serviço de mídia após você gerar as teclas.

**Aplicativos** - se você tiver aplicativos da web ou os serviços de nuvem que usam a conta de armazenamento, você perderá as conexões se você gerar chaves, a menos que você implementar suas chaves.

**Gerenciadores de armazenamento** - se você estiver usando os [aplicativos do Explorador de armazenamento](storage-explorers.md), você provavelmente precisará atualizar a chave de armazenamento usada pelos aplicativos.

Aqui está o processo para girar suas chaves de acesso de armazenamento:

1. Atualize as cadeias de caracteres de conexão no seu código do aplicativo para fazer referência a tecla de acesso secundário da conta de armazenamento.

2. Gerar novamente a chave primária de acesso para sua conta de armazenamento. Na lâmina **Teclas de acesso** , clique em **Gerar CHAVE1**e, em seguida, clique em **Sim** para confirmar que você deseja gerar uma nova chave.

3. Atualize as cadeias de caracteres de conexão no seu código para fazer referência a nova chave de acesso primário.

4. Gerar novamente a chave de acesso secundário da mesma maneira.

## <a name="delete-a-storage-account"></a>Excluir uma conta de armazenamento

Para remover uma conta de armazenamento que você já não está usando, navegue até a conta de armazenamento no [portal do Azure](https://portal.azure.com)e clique em **Excluir**. Excluindo uma conta de armazenamento exclui a conta inteira, incluindo todos os dados na conta.

> [AZURE.WARNING] Não é possível restaurar uma conta de armazenamento excluída ou recuperar qualquer conteúdo que continha antes da exclusão. Certifique-se de fazer backup de qualquer coisa que desejar salvar antes de excluir a conta. Isso também acontece com todos os recursos na conta — quando você exclui um blob, tabela, fila ou arquivo, ele é permanentemente excluído.

Para excluir uma conta de armazenamento que está associada uma máquina virtual Azure, primeiro você deve garantir que qualquer disco de máquina virtual foram excluído. Se você não excluir seus discos de máquina virtual primeiro, em seguida, quando você tenta excluir sua conta de armazenamento, você verá uma mensagem de erro semelhante a:

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

Se a conta de armazenamento usa o modelo clássico de implantação, você pode remover o disco de máquina virtual seguindo estas etapas no [portal do Azure](https://manage.windowsazure.com):

1. Navegue até o [portal do Azure clássico](https://manage.windowsazure.com).
2. Navegue até a guia de máquinas virtuais.
3. Clique na guia Disks.
4. Selecione seu disco de dados, clique em Excluir disco.
5. Para excluir imagens de disco, navegue até a guia de imagens e excluir quaisquer imagens que são armazenadas na conta.

Para obter mais informações, consulte a [documentação do Azure Virtual Machine](http://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="next-steps"></a>Próximas etapas

- [Armazenamento de Blob do Azure: Níveis de interessante e acesso](storage-blob-storage-tiers.md)
- [Azure replicação de armazenamento](storage-redundancy.md)
- [Configurar cadeias de caracteres de Conexão de armazenamento do Azure](storage-configure-connection-string.md)
- [Transferir dados com o utilitário de AzCopy de linha de comando](storage-use-azcopy.md)
- Visite o [Blog da equipe de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/).
