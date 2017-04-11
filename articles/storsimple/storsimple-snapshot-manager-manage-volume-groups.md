<properties 
   pageTitle="Grupos de volume do Gerenciador de instantâneo StorSimple | Microsoft Azure"
   description="Descreve como usar o snap-in MMC Gerenciador de instantâneo StorSimple para criar e gerenciar grupos de volume."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Use o Gerenciador de instantâneo StorSimple para criar e gerenciar grupos de volume

## <a name="overview"></a>Visão geral

Você pode usar o nó **Grupos de Volume** no painel de **escopo** para atribuir volumes aos grupos de volume, exibir informações sobre um grupo de volume, agendar backups e editar grupos de volume. 

Os grupos de volume são pools de volumes relacionados usados para garantir que os backups sejam consistentes com o aplicativo. Para obter mais informações, consulte [Volumes e grupos de volume](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) e a [integração com o serviço de cópia de sombra de Volume do Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

>[AZURE.IMPORTANT] 
>
> * Todos os volumes em um grupo de volume devem ser de um provedor de serviços de nuvem único.
> 
> * Quando você configura grupos de volume, não misture volumes compartilhados de cluster (CSVs) e CSVs no mesmo grupo de volume. Gerenciador de instantâneo StorSimple não dá suporte a uma mistura de CSVs e não CSVs no mesmo instantâneo.
 
![Nó de grupos de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Figura 1: Nó de grupos de Volume do Gerenciador de instantâneo StorSimple** 

Este tutorial explica como você pode usar o Gerenciador de instantâneo StorSimple para:

- Exibir informações sobre seus grupos de volume 
- Criar um grupo de volume
- Fazer backup de um grupo de volume
- Editar um grupo de volume
- Excluir um grupo de volume

Todas essas ações também estão disponíveis no painel de **ações** .
 
## <a name="view-volume-groups"></a>Exibir grupos de volume

Se você clicar no nó **Grupos de Volume** , o painel de **resultados** mostra as seguintes informações sobre cada grupo de volume, dependendo das seleções de coluna feitas. (As colunas no painel de **resultados** são configuráveis. Clique com botão direito o nó **Volumes** , selecione o **modo de exibição**e selecione **Adicionar/remover colunas**.)

Coluna de resultados | Descrição 
:--------------|:------------ 
Nome           | A coluna **nome** contém o nome do grupo de volume.
Aplicativo    | A coluna de **aplicativos** mostra o número de autores VSS atualmente instalada e em execução no host Windows.
Selecionada       | Coluna **selecionado** mostra o número de volumes que estão contidos no grupo de volume. Um zero (0) indica que nenhum aplicativo está associado os volumes no grupo de volume.
Importado       | A coluna **importados** mostra o número de volumes importados. Quando definida como **True**, esta coluna indica que um grupo de volume foi importado de portal clássico do Azure e não foi criado no Gerenciador de instantâneo StorSimple.
 
>[AZURE.NOTE] Grupos de volume do Gerenciador de instantâneo StorSimple também são exibidos na guia **Políticas de Backup** no portal de clássico do Azure.
 
## <a name="create-a-volume-group"></a>Criar um grupo de volume

Use o procedimento a seguir para criar um grupo de volume.

#### <a name="to-create-a-volume-group"></a>Para criar um grupo de volume

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple. 

2. No painel de **escopo** , **Grupos de Volume**de atalho e, em seguida, clique em **Criar grupo de Volume**. 

    ![Criar grupo de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
 
    Caixa de diálogo **criar um grupo de volume** é exibida. 

    ![Criar uma caixa de diálogo de grupo de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png) 

3.  Insira as seguintes informações: 

    1. Na caixa **nome** , digite um nome exclusivo para o novo grupo de volume. 

    2. Na caixa de **aplicativos** , selecione aplicativos associados com os volumes que você adicionará ao grupo de volume. 

        A caixa de **aplicativos** lista somente os aplicativos que usam volumes de StorSimple e tem criadores de VSS habilitados para eles. Um gravador VSS está habilitado somente se todos os volumes que o gravador está atento são volumes de StorSimple. Se a caixa de aplicativos estiver vazia, sem aplicativos que usam volumes de StorSimple do Azure e tem suporte para criadores de VSS são instalados. (Atualmente, StorSimple Azure suporta Microsoft Exchange e SQL Server.) Para obter mais informações sobre como escritores VSS, consulte [integração com o serviço de cópia de sombra de Volume do Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

        Se você selecionar um aplicativo, todos os volumes associados a ela são selecionados automaticamente. Por outro lado, se você selecionar volumes associados a um aplicativo específico, o aplicativo é selecionado automaticamente na caixa **aplicativos** . 

    3. Na caixa **Volumes** , selecione volumes de StorSimple para adicionar ao grupo de volume. 

      - Você pode incluir volumes com partições únicos ou múltiplos. (Vários volumes de partição podem ser discos dinâmicos ou discos básicos com várias partições.) Um volume que contém várias partições será tratado como uma unidade única. Consequentemente, se você adicionar apenas uma das partições a um grupo de volume, todas as partições são adicionadas automaticamente ao grupo volume ao mesmo tempo. Depois de adicionar um volume de partição vários para um grupo de volume, o volume de partição vários continua a ser tratado como uma unidade única.

      - Você pode criar grupos de volume vazio atribuindo não quaisquer volumes a eles. 

      - Não misture volumes compartilhados de cluster (CSVs) e CSVs no mesmo grupo de volume. Gerenciador de instantâneo StorSimple não dá suporte a uma mistura de volumes CSV e não-CSV no mesmo instantâneo. 

4. Clique em **Okey** para salvar o grupo de volume.

## <a name="back-up-a-volume-group"></a>Fazer backup de um grupo de volume

Use o procedimento a seguir para fazer backup de um grupo de volume.

#### <a name="to-back-up-a-volume-group"></a>Para fazer backup de um grupo de volume

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , expanda o nó de **Grupos de Volume** , um nome de grupo de volume de atalho e, em seguida, clique em **Fazer Backup**. 

    ![Fazer backup de grupo de volume imediatamente](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)

3. Na caixa de diálogo **Fazer Backup** , selecione **Instantâneo Local** ou um **Instantâneo de nuvem**e, em seguida, clique em **criar**. 

    ![Faça backup diálogo](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png) 

4. Para confirmar que o backup está sendo executado, expanda o nó de **trabalhos** e clique em **Executar**. O backup deve estar listado.

5. Para exibir o instantâneo concluído, expanda o nó do **Catálogo de Backup** , expanda o nome do grupo de volume e clique em **Instantâneo Local** ou um **Instantâneo de nuvem**. O backup será listado se ele foi concluída com êxito. 

## <a name="edit-a-volume-group"></a>Editar um grupo de volume

Use o procedimento a seguir para editar um grupo de volume.

#### <a name="to-edit-a-volume-group"></a>Para editar um grupo de volume

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , expanda o nó de **Grupos de Volume** , um nome de grupo de volume de atalho e, em seguida, clique em **Editar**. 

3. Caixa de diálogo **criar um grupo de volume **é exibida. Você pode alterar as entradas de **nome**, **aplicativos**e **Volumes** . 

4. Clique em **Okey** para salvar suas alterações.

## <a name="delete-a-volume-group"></a>Excluir um grupo de volume

Use o procedimento a seguir para excluir um grupo de volume. 

>[AZURE.WARNING] Isso também exclui todos os backups associados ao grupo de volume.

#### <a name="to-delete-a-volume-group"></a>Para excluir um grupo de volume

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple. 

2. No painel de **escopo** , expanda o nó de **Grupos de Volume** , um nome de grupo de volume de atalho e, em seguida, clique em **Excluir**. 

3. Caixa de diálogo **Excluir grupo de Volume** é exibida. Digite **Confirmar** na caixa de texto e clique em **Okey**. 

    O grupo de volume excluído desaparece da lista no painel de **resultados** e todos os backups que estão associados esse grupo de volume são excluídos do catálogo de backup.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Gerenciador de instantâneo StorSimple administrar sua solução de StorSimple](storsimple-snapshot-manager-admin.md).
- Saiba como [usar o Gerenciador de instantâneo StorSimple para criar e gerenciar políticas de backup](storsimple-snapshot-manager-manage-backup-policies.md).
