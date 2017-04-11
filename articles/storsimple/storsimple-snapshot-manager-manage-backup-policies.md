<properties 
   pageTitle="Políticas de backup do Gerenciador de instantâneo StorSimple | Microsoft Azure"
   description="Descreve como usar o snap-in MMC Gerenciador de instantâneo StorSimple para criar e gerenciar as políticas de backup que controlam backups agendados."
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
   ms.date="05/12/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Use o Gerenciador de instantâneo StorSimple para criar e gerenciar políticas de backup

## <a name="overview"></a>Visão geral

Uma política de backup cria um agendamento para fazer backup de dados de volume localmente ou na nuvem. Quando você cria uma política de backup, você também pode especificar uma política de retenção. (Você pode manter um máximo de 64 instantâneos.) Para obter mais informações sobre políticas de backup, consulte [tipos de Backup](storsimple-what-is-snapshot-manager.md#backup-type) em [série 8000 StorSimple: uma solução de nuvem híbrida](storsimple-overview.md).

Este tutorial explica como:

- Criar uma política de backup 
- Editar uma política de backup 
- Excluir uma política de backup 

## <a name="create-a-backup-policy"></a>Criar uma política de backup

Use o procedimento a seguir para criar uma nova política de backup.

#### <a name="to-create-a-backup-policy"></a>Para criar uma política de backup

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , clique com botão direito **Políticas de Backup**e clique em **Criar diretiva de Backup**.

    ![Criar uma política de backup](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Caixa de diálogo **criar uma política** é exibida. 

    ![Criar uma política - guia Geral](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)

3. Na guia **Geral** , preencha as seguintes informações:

   1. Na caixa de texto **nome** , digite um nome para a política.

   2. Na caixa de texto **grupo de Volume** , digite o nome do grupo de volumes associado à política.

   3. Selecione **instantâneo Local** ou **instantâneo de nuvem**.

   4. Selecione o número de instantâneos para reter. Se você selecionar **tudo**, 64 instantâneos serão mantidos (o máximo). 

4. Clique na guia **cronograma** .

    ![Criar uma política - guia Cronograma](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)

5. Na guia **cronograma** , preencha as seguintes informações: 

   1. Clique na caixa de seleção **Habilitar** para agendar o próximo backup.

   2. Em **configurações**, selecione **uma vez**, **diária**, **semanal**ou **mensal**. 

   3. Na caixa de texto **Iniciar** , clique no ícone de calendário e selecione uma data de início.

   4. Em **Configurações avançadas**, você pode definir agendas de repetição opcionais e uma data de término.

   5. Clique em **Okey**.

Depois de criar uma política de backup, as seguintes informações aparecem no painel de **resultados** :

- **Nome** – o nome da política de backup.

- **Tipo** – instantâneo local ou um instantâneo de nuvem.

- **Grupo de volume** – o grupo de volumes associado à política.

- **Retenção** – o número de instantâneos mantidas; o máximo é 64.

- **Criado** – a data em que esta política foi criada.

- **Ativado** – se a política está em vigor: **True** indica que ele está em vigor; **False** indica que ele não está em vigor. 

## <a name="edit-a-backup-policy"></a>Editar uma política de backup

Use o procedimento a seguir para editar uma política de backup existente.

#### <a name="to-edit-a-backup-policy"></a>Para editar uma política de backup

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple. 

2. No painel de **escopo** , clique no nó de **Políticas de Backup** . Todas as políticas de backup aparecem no painel de **resultados** . 

3. Clique com botão direito a política que você deseja editar e clique em **Editar**. 

    ![Editar uma política de backup](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png) 

4. Quando a janela de **criar uma política** é exibida, insira suas alterações e clique em **Okey**. 

## <a name="delete-a-backup-policy"></a>Excluir uma política de backup

Use o procedimento a seguir para excluir uma política de backup.

#### <a name="to-delete-a-backup-policy"></a>Para excluir uma política de backup

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple. 

2. No painel de **escopo** , clique no nó de **Políticas de Backup** . Todas as políticas de backup aparecem no painel de **resultados** . 

3. Clique com botão direito a política de backup que você deseja excluir e clique em **Excluir**.

4. Quando aparece a mensagem de confirmação, clique em **Sim**.

    ![Política de backup confirmação de exclusão](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Gerenciador de instantâneo StorSimple administrar sua solução de StorSimple](storsimple-snapshot-manager-admin.md).
- Saiba como [usar o Gerenciador de instantâneo StorSimple para exibir e gerenciar trabalhos de backup](storsimple-snapshot-manager-manage-backup-jobs.md).
