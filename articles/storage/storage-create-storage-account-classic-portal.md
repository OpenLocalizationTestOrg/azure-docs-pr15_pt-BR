<properties
    pageTitle="Como criar, gerenciar ou excluir uma conta de armazenamento no Portal de clássico do Azure | Microsoft Azure"
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

Uma conta de armazenamento do Azure lhe dá acesso aos serviços de Azure Blob, fila, tabela e arquivo do armazenamento do Azure. Sua conta de armazenamento fornece namespace exclusivo para os objetos de dados de armazenamento do Azure. Por padrão, os dados em sua conta está disponíveis somente para você, o proprietário da conta.

Há dois tipos de contas de armazenamento:

- Uma conta de armazenamento padrão inclui armazenamento de Blob, tabela, fila e arquivo.
- Uma conta de armazenamento premium atualmente suporta somente a discos Azure máquina virtual. Consulte [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](storage-premium-storage.md) para uma visão geral detalhada de armazenamento Premium.

## <a name="storage-account-billing"></a>Cobrança de conta de armazenamento

Você será cobrado para uso de armazenamento do Azure com base em sua conta de armazenamento. Os custos de armazenamento se baseiam em quatro fatores: capacidade de armazenamento, esquema de replicação, transações de armazenamento e saída de dados.

- Capacidade de armazenamento refere-se de quanto de sua alocação de conta de armazenamento você está usando para armazenar dados. O custo de simplesmente armazenar seus dados é determinado por quanto aos dados que você está armazenando e como ele é replicado.
- Replicação determina quantas cópias dos seus dados são mantidas ao mesmo tempo e em quais locais.
- Transações referem-se para todas as operações leitura e gravação ao armazenamento do Azure.
- Saída de dados se refere a dados transferidos fora uma região Azure. Quando os dados em sua conta de armazenamento for acessados por um aplicativo que não está sendo executado na mesma região, se esse aplicativo é um serviço de nuvem ou algum outro tipo de aplicativo, em seguida, cobrado para saída de dados. (Para serviços do Azure, você pode tomar medidas para agrupar dados e serviços nos data centers mesmo reduzir ou eliminar encargos de saída de dados.)  

A página de [Preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage) fornece informações detalhadas de preços de capacidade de armazenamento, replicação e transações. A página de [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/) fornece informações detalhadas de preços de saída de dados.

Para obter detalhes sobre destinos de capacidade e desempenho de conta de armazenamento, consulte [metas de desempenho e escalabilidade de armazenamento do Azure](storage-scalability-targets.md).

> [AZURE.NOTE] Quando você cria uma máquina virtual Azure, uma conta de armazenamento é criada automaticamente no local de implantação se você ainda não tiver uma conta de armazenamento nesse local. Portanto, não é necessário seguir as etapas abaixo para criar uma conta de armazenamento para seus discos de máquina virtual. O nome da conta de armazenamento se baseará no nome da máquina virtual. Consulte a [documentação de máquinas virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) para obter mais detalhes.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. Entre [Portal de clássico Azure](https://manage.windowsazure.com).

2. Clique em **novo** na barra de tarefas na parte inferior da página. Escolha **Serviços de dados** | **armazenamento**e clique em **Criar rápida**.

    ![NewStorageAccount](./media/storage-create-storage-account-classic-portal/storage_NewStorageAccount.png)

3. Na **URL**, insira um nome para sua conta de armazenamento.

    > [AZURE.NOTE] Nomes de conta de armazenamento devem estar entre 3 e 24 caracteres e podem conter números e apenas letras minúsculas.
    >  
    > O nome da sua conta de armazenamento deve ser exclusivo dentro do Azure. Portal de clássico do Azure indicará se o nome da conta de armazenamento que você selecionar já existe.

    Consulte de [pontos de extremidade de conta de armazenamento](#storage-account-endpoints) abaixo para obter detalhes sobre como o nome da conta de armazenamento será usado para os objetos no armazenamento do Azure de endereço.

4. No **Grupo local/afinidade**, selecione um local para sua conta de armazenamento Fechar para você ou aos seus clientes. Se os dados em sua conta de armazenamento serão acessados de outro serviço de Azure, como um Azure máquina virtual ou um serviço na nuvem, talvez você queira selecionar um grupo de afinidade da lista para sua conta de armazenamento no Centro de dados do mesmo com outros serviços do Azure que você está usando para melhorar o desempenho e os custos de grupo.

    Observe que você deve selecionar um grupo de afinidade quando sua conta de armazenamento é criada. Você não pode mover uma conta existente a um grupo de afinidade. Para obter mais informações sobre grupos de afinidade, consulte [localização conjunta de serviço com um grupo de afinidade](#service-co-location-with-an-affinity-group) abaixo.

    >[AZURE.IMPORTANT] Para determinar quais locais estão disponíveis para sua assinatura, você pode chamar a operação de [todos os provedores de recurso de lista](https://msdn.microsoft.com/library/azure/dn790524.aspx) . Para listar os provedores do PowerShell, chamada [Get-AzureLocation](https://msdn.microsoft.com/library/azure/dn757693.aspx). A partir do .NET, use o método de [lista](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.provideroperationsextensions.list.aspx) da classe ProviderOperationsExtensions.
    >
    >Além disso, consulte [Regiões do Azure](https://azure.microsoft.com/regions/#services) para obter mais informações sobre quais serviços estão disponíveis em que região.


5. Se você tiver mais de uma assinatura do Azure, o campo de **assinatura** é exibido. Na **assinatura**, digite a assinatura Azure que você deseja usar a conta de armazenamento com.

6. Na **replicação**, selecione o nível desejado de replicação para sua conta de armazenamento. A opção de replicação recomendado é replicação geográfica redundante, que fornece durabilidade máxima para seus dados. Para obter mais detalhes sobre as opções de replicação de armazenamento do Azure, consulte [replicação de armazenamento do Azure](storage-redundancy.md).

6. Clique em **Criar conta de armazenamento**.

    Pode levar alguns minutos para criar sua conta de armazenamento. Para verificar o status, você pode monitorar as notificações na parte inferior do Portal de clássico do Azure. Depois que a conta de armazenamento tiver sido criada, sua nova conta de armazenamento tem status **Online** e está pronta para uso.

![StoragePage](./media/storage-create-storage-account-classic-portal/Storage_StoragePage.png)


### <a name="storage-account-endpoints"></a>Pontos de extremidade de conta de armazenamento

Cada objeto que você armazena no armazenamento do Azure tem um endereço de URL exclusivo. O nome da conta de armazenamento de formulários o subdomínio desse endereço. A combinação de nome de domínio e subdomínio, que é específico para cada serviço, constitui um *ponto de extremidade* para a sua conta de armazenamento.

Por exemplo, se sua conta de armazenamento é denominada *mystorageaccount*, os pontos de extremidade padrão para a sua conta de armazenamento são:

- Serviço de blob: http://*mystorageaccount*. blob.core.windows.net

- Serviço de tabela: http://*mystorageaccount*. table.core.windows.net

- Fila de serviço: http://*mystorageaccount*. queue.core.windows.net

- Serviço de arquivo: http://*mystorageaccount*. file.core.windows.net

Você pode ver os pontos de extremidade para sua conta de armazenamento no painel de armazenamento no [Portal de clássico do Azure](https://manage.windowsazure.com) depois que a conta foi criada.

O URL para acessar um objeto em uma conta de armazenamento baseia-se anexando o local do objeto na conta de armazenamento para o ponto de extremidade. Por exemplo, um endereço de blob pode ter este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Você também pode configurar um nome de domínio personalizado para usar com sua conta de armazenamento. Consulte [Configurar um nome de domínio personalizado para seu ponto de extremidade de armazenamento de blob](storage-custom-domain-name.md) para obter detalhes.

### <a name="service-co-location-with-an-affinity-group"></a>Localização conjunta de serviço com um grupo de afinidade

Um *grupo de afinidade* é um agrupamento geográfico de seus serviços Azure e VMs com sua conta de armazenamento do Azure. Um grupo de afinidade pode melhorar o desempenho do serviço Localizando cargas de trabalho do computador no mesmo data center ou perto o público-alvo usuário. Além disso, não há cobranças são incorridas pelo egresso quando dados de uma conta de armazenamento são acessados de outro serviço que faz parte do mesmo grupo afinidade.

> [AZURE.NOTE]  Para criar um grupo de afinidade, abrir a área de <b>configurações</b> do [Portal clássico do Azure](https://manage.windowsazure.com), clique em <b>grupos de afinidade</b>e clique em <b>Adicionar um grupo de afinidade</b> ou no botão <b>Adicionar</b> . Você também pode criar e gerenciar grupos de afinidade usando a API de gerenciamento de serviço do Azure. Consulte <a href="http://msdn.microsoft.com/library/azure/ee460798.aspx">operações em grupos de afinidade</a> para obter mais informações.

## <a name="view-copy-and-regenerate-storage-access-keys"></a>Exibir, copiar e gerar as teclas de acesso de armazenamento

Quando você cria uma conta de armazenamento, o Azure gera duas teclas de acesso para armazenamento de 512 bits, que são usadas para autenticação quando a conta de armazenamento é acessada. Fornecendo duas teclas de acesso de armazenamento, Azure permite gerar as chaves com acesso a esse serviço ou sem interrupção do seu serviço de armazenamento.

> [AZURE.NOTE] Recomendamos que você evite suas chaves de acesso de armazenamento de compartilhamento com outras pessoas. Para permitir o acesso aos recursos de armazenamento sem divulgar suas chaves de acesso, você pode usar uma *assinatura de acesso compartilhado*. Uma assinatura de acesso compartilhado fornece acesso a um recurso em sua conta para um intervalo que você definir e com as permissões que você especificar. Consulte [Usando compartilhados acesso assinaturas (SAS)](storage-dotnet-shared-access-signature-part-1.md) para obter mais informações.

No [Portal de clássico do Azure](https://manage.windowsazure.com), use **Gerenciamento de chaves** no painel ou a página de **armazenamento** para exibir, copiar e gerar as teclas de acesso de armazenamento que são usadas para acessar os serviços de Blob, tabela e fila.

### <a name="copy-a-storage-access-key"></a>Copiar uma tecla de acesso de armazenamento  

Você pode usar o **Gerenciamento de chaves** para copiar uma tecla de acesso de armazenamento para usar em uma cadeia de conexão. A cadeia de conexão requer o nome da conta de armazenamento e uma chave para usar em autenticação. Para obter informações sobre como configurar cadeias de caracteres de conexão para acessar os serviços de armazenamento do Azure, consulte [Configurar cadeias de Conexão de armazenamento do Azure](storage-configure-connection-string.md).

1. No [Portal de clássico do Azure](https://manage.windowsazure.com), clique em **armazenamento**e clique no nome da conta de armazenamento para abrir o painel de controle.

2. Clique em **Gerenciar chaves**.

    **Gerenciar as teclas de acesso** é aberta.

    ![Managekeys](./media/storage-create-storage-account-classic-portal/Storage_ManageKeys.png)


3. Para copiar uma tecla de acesso de armazenamento, selecione o texto da tecla. Em seguida, clique com botão direito e clique em **Copiar**.

### <a name="regenerate-storage-access-keys"></a>Gerar as teclas de acesso de armazenamento
Recomendamos que você altere as teclas de acesso à sua conta de armazenamento periodicamente para ajudar a manter suas conexões de armazenamento seguro. Duas teclas de acesso são atribuídas para que você pode manter conexões com a conta de armazenamento usando uma tecla de acesso enquanto você gerar tecla de acesso.

> [AZURE.WARNING] Gerar suas chaves de acesso pode afetar os serviços no Azure, bem como seus próprios aplicativos que dependem da conta de armazenamento. Todos os clientes que usam a chave de acesso para acessar a conta de armazenamento devem ser atualizados para usar a nova chave.

**Serviços de mídia** - se você tiver serviços de mídia que dependem de sua conta de armazenamento, você deve sincronize novamente as teclas de acesso com o seu serviço de mídia após você gerar as teclas.

**Aplicativos** - se você tiver aplicativos da web ou os serviços de nuvem que usam a conta de armazenamento, você perderá as conexões se você gerar chaves, a menos que você implementar suas chaves. 

**Gerenciadores de armazenamento** - se você estiver usando os [aplicativos do Explorador de armazenamento](storage-explorers.md), você provavelmente precisará atualizar a chave de armazenamento usada pelos aplicativos.

Aqui está o processo para girar suas chaves de acesso de armazenamento:

1. Atualize as cadeias de caracteres de conexão no seu código do aplicativo para fazer referência a tecla de acesso secundário da conta de armazenamento.

2. Gerar novamente a chave primária de acesso para sua conta de armazenamento. No [Portal de clássico do Azure](https://manage.windowsazure.com), do painel ou a página de **Configurar** , clique em **Gerenciar chaves**. Clique em **Gerar** sob a chave de acesso primário e, em seguida, clique em **Sim** para confirmar que você deseja gerar uma nova chave.

3. Atualize as cadeias de caracteres de conexão no seu código para fazer referência a nova chave de acesso primário.

4. Gerar novamente a chave de acesso secundário.

## <a name="delete-a-storage-account"></a>Excluir uma conta de armazenamento

Para remover uma conta de armazenamento que você já não está usando, use **Excluir** no painel ou a página de **Configurar** . **Delete** exclui a conta de armazenamento inteiro, incluindo todos os blobs, tabelas e filas na conta.

> [AZURE.WARNING] Não é possível restaurar uma conta de armazenamento excluída ou recuperar qualquer conteúdo que continha antes da exclusão. Certifique-se de fazer backup de qualquer coisa que desejar salvar antes de excluir a conta. Isso também acontece com todos os recursos na conta — quando você exclui um blob, tabela, fila ou arquivo, ele é permanentemente excluído.
>
> Se sua conta de armazenamento contiver arquivos VHD para uma máquina virtual Azure, você deve excluir as imagens e discos que estão usando esses arquivos VHD antes de excluir a conta de armazenamento. Primeiro, interromper a máquina virtual se ele estiver sendo executado e exclua-o. Para excluir discos, navegue até a guia **Disks** e exclua qualquer disco lá. Para excluir imagens, navegue até a guia de **imagens** e excluir quaisquer imagens que são armazenadas na conta.

1. No [Portal do Azure clássico](https://manage.windowsazure.com), clique em **armazenamento**.

2. Clique em qualquer lugar a entrada de conta de armazenamento, exceto o nome e clique em **Excluir**.

     - Ou -

    Clique no nome da conta de armazenamento para abrir o painel de controle e clique em **Excluir**.

3. Clique em **Sim** para confirmar que você deseja excluir a conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o armazenamento do Azure, consulte a [documentação de armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/).
- Visite o [Blog da equipe de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/).
- [Transferir dados com o utilitário de AzCopy de linha de comando](storage-use-azcopy.md)
