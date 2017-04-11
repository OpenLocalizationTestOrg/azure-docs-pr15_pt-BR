<properties 
   pageTitle="Catálogo de backup do Gerenciador de instantâneo StorSimple | Microsoft Azure"
   description="Descreve como usar o snap-in MMC Gerenciador de instantâneo StorSimple para exibir e gerenciar o catálogo de backup."
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
   ms.date="04/26/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Usar o Gerenciador de instantâneo de StorSimple para gerenciar o catálogo de backup

## <a name="overview"></a>Visão geral

A função principal do Gerenciador de instantâneo StorSimple é permitem que você crie cópias de backup consistente com o aplicativo de volumes de StorSimple na forma de instantâneos. Instantâneos serão listados em um arquivo XML chamado de *Catálogo de backup*. O catálogo de backup organiza instantâneos por grupo de volume e depois por instantâneo local ou um instantâneo de nuvem. 

Este tutorial descreve como você pode usar o nó do **Catálogo de Backup** para concluir as seguintes tarefas:

- Restaurar um volume 
- Clonar um volume ou grupo de volume 
- Excluir um backup 
- Recuperar um arquivo
- Restaurar o banco de dados do Gerenciador de instantâneo Storsimple

Você pode exibir o catálogo de backup expandindo o nó do **Catálogo de Backup** no painel de **escopo** e, em seguida, expanda o grupo de volume.

- Se você clicar no nome do grupo de volume, o painel de **resultados** mostra o número de instantâneos locais e instantâneos de nuvem disponíveis para o grupo de volume. 

- Se você clicar **Instantâneo Local** ou um **Instantâneo de nuvem**, o painel de **resultados** mostra as seguintes informações sobre cada backup instantâneo (dependendo das suas configurações de **exibição** ): 

    - **Nome** – a hora em que o instantâneo foi criado. 

    - **Tipo** – se esse é um instantâneo local ou um instantâneo de nuvem. 

    - **Proprietário** – o proprietário do conteúdo. 

    - **Disponível** – se o instantâneo está disponível no momento. **True** indica que o instantâneo está disponível e pode ser restaurado; **False** indica que o instantâneo não está mais disponível. 

    - **Importados** – se o backup foi importado. **True** indica que o backup foi importado do serviço do Gerenciador de StorSimple no momento em que o dispositivo foi configurado no Gerenciador de instantâneo do StorSimple; **False** indica que ele não foi importado, mas foi criado pelo Gerenciador de instantâneo StorSimple. (Você pode facilmente identificar um grupo de volume importado porque é adicionado um sufixo que identifica o dispositivo da qual o grupo de volume foi importado.)

    ![Catálogo de backup](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)

- Se você expandir **Instantâneo Local** ou um **Instantâneo de nuvem**e, em seguida, clique em um nome de instantâneo individuais, o painel de **resultados** mostra as seguintes informações sobre o instantâneo que você selecionou:

    - **Nome** – o volume identificado pela letra da unidade. 

    - **Nome local** – o nome local da unidade (se disponível). 

    - **Dispositivo** – o nome do dispositivo em que reside o volume. 

    - **Disponível** – se o instantâneo está disponível no momento. **True** indica que o instantâneo está disponível e pode ser restaurado; **False** indica que o instantâneo não está mais disponível. 


## <a name="restore-a-volume"></a>Restaurar um volume

Use o procedimento a seguir para restaurar um volume de backup.

#### <a name="prerequisites"></a>Pré-requisitos

Se você ainda não o fez, crie um volume e o grupo de volume e exclua o volume. Por padrão, o Gerenciador de instantâneo StorSimple backup um volume antes de permitir que seja excluída. Essa precaução evitar a perda de dados se o volume é excluído acidentalmente ou se os dados precisam ser recuperadas por qualquer motivo. 

Gerenciador de instantâneo StorSimple exibirá a seguinte mensagem enquanto cria o backup precaução.

![Mensagem de instantâneo automático](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

>[AZURE.IMPORTANT]Você não pode excluir um volume que faz parte de um grupo de volume. A opção Excluir não está disponível. <br>

#### <a name="to-restore-a-volume"></a>Restaurar um volume

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple. 

2. No painel de **escopo** , expanda o nó do **Catálogo de Backup** , expandir um grupo de volume e, em seguida, clique em **Instantâneos locais** ou **Instantâneos de nuvem**. Uma lista de instantâneos backup aparece no painel de **resultados** . 

3. Localize o backup que você deseja restaurar, clique com botão direito e, em seguida, clique em **Restaurar**. 

    ![Restaurar o catálogo de backup](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 

4. Na página confirmação, examine os detalhes, digite **Confirmar**e clique em **Okey**. Gerenciador de instantâneo StorSimple usa o backup para restaurar o volume. 

    ![Restaurar a mensagem de confirmação](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 

5. Você pode monitorar a ação Restaurar que seja executado. No painel de **escopo** , expanda o nó de **trabalhos** e clique em **Executar**. Os detalhes de trabalho aparecem no painel de **resultados** . Quando o trabalho de restauração for concluído, os detalhes de trabalho são transferidos para a lista de **últimas 24 horas** .

## <a name="clone-a-volume-or-volume-group"></a>Clonar um volume ou grupo de volume

Use o procedimento a seguir para criar uma duplicata (clonagem) de um volume ou grupo de volume.

#### <a name="to-clone-a-volume-or-volume-group"></a>Para clonar um volume ou grupo de volume

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , expanda o nó do **Catálogo de Backup** , expandir um grupo de volume e clique em **Nuvem instantâneos**. Uma lista de backups aparece no painel de **resultados** .

3. Encontre o volume ou o grupo de volume que você deseja clonar, clique com botão direito o volume ou o nome do grupo de volume e clique em **clonar**. A caixa de diálogo **Instantâneo de nuvem clonar** aparece.

    ![Clonar um instantâneo de nuvem](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 

4. Preencha a caixa de diálogo **Clonar nuvem instantâneo** da seguinte maneira: 

    1. Na caixa de texto **nome** , digite um nome para o volume clonado. Esse nome aparecerá no nó **Volumes** . 

    2. (Opcional) selecione **unidade**e, em seguida, selecione uma letra de unidade na lista suspensa. 

    3. (Opcional) selecione **Pasta (NTFS)**, digite um caminho de pasta ou clique em Procurar e selecione um local para a pasta. 

    4. Clique em **criar**.

5. Quando o processo de clonagem for concluído, você deve inicializar o volume clonado. Iniciar o Gerenciador de servidor e inicie o gerenciamento de disco. Para obter instruções detalhadas, consulte [montar volumes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Depois que ele é inicializado, o volume será listado sob o nó de **Volumes** no painel de **escopo** . Se você não vir o volume listado, atualize a lista de volumes (o nó **Volumes** de atalho e, em seguida, clique em **Atualizar**).

## <a name="delete-a-backup"></a>Excluir um backup

Use o procedimento a seguir para excluir um instantâneo do catálogo de backup. 

>[AZURE.NOTE]Excluir um instantâneo exclui os dados de backups associados o instantâneo. No entanto, o processo de limpeza de dados da nuvem pode levar algum tempo.<br>
 
#### <a name="to-delete-a-backup"></a>Para excluir um backup

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , expanda o nó do **Catálogo de Backup** , expandir um grupo de volume e, em seguida, clique em **Instantâneos locais** ou **Instantâneos de nuvem**. Uma lista de instantâneos aparece no painel de **resultados** . 

3. Clique com botão direito o instantâneo que você deseja excluir e clique em **Excluir**.

4. Quando aparecer a mensagem de confirmação, clique em **Okey**. 

## <a name="recover-a-file"></a>Recuperar um arquivo

Se um arquivo for acidentalmente excluído de um volume, você pode recuperar o arquivo de recuperação de um instantâneo que previamente datas a exclusão, usando o instantâneo para criar um clonar do volume e, em seguida, copiando o arquivo de volume clonado para o volume original.

#### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que você tenha um backup atual do grupo de volume. Em seguida, exclua um arquivo armazenado em um dos volumes desse grupo de volume. Por fim, use as etapas a seguir para restaurar o arquivo excluído do backup. 

#### <a name="to-recover-a-deleted-file"></a>Recuperar um arquivo excluído

1. Clique no ícone do Gerenciador de instantâneo StorSimple na área de trabalho. A janela de console do Gerenciador de instantâneo StorSimple aparece. 

2. No painel de **escopo** , expanda o nó do **Catálogo de Backup** e navegue até um instantâneo que contém o arquivo excluído. Normalmente, você deve selecionar um instantâneo criado logo antes da exclusão. 

3. Localizar o volume que você deseja clonar, clique com botão direito e clique em **clonar**. A caixa de diálogo **Instantâneo de nuvem clonar** aparece.

    ![Clonar um instantâneo de nuvem](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 

4. Preencha a caixa de diálogo **Clonar nuvem instantâneo** da seguinte maneira: 

   1. Na caixa de texto **nome** , digite um nome para o volume clonado. Esse nome aparecerá no nó **Volumes** . 

   2. (Opcional) Selecione a **unidade**e, em seguida, selecione uma letra de unidade na lista suspensa. 

   3. (Opcional) Selecione **Pasta (NTFS)**, digite um caminho de pasta ou clique em **Procurar** e selecione um local para a pasta. 

   4. Clique em **criar**. 

5. Quando o processo de clonagem for concluído, você deve inicializar o volume clonado. Iniciar o Gerenciador de servidor e inicie o gerenciamento de disco. Para obter instruções detalhadas, consulte [montar volumes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Depois que ele é inicializado, o volume será listado sob o nó de **Volumes** no painel de **escopo** . 

    Se você não vir o volume listado, atualize a lista de volumes (o nó **Volumes** de atalho e, em seguida, clique em **Atualizar**).

6. Abra a pasta NTFS que contém o volume clonado, expanda o nó de **Volumes** e, em seguida, abra o volume clonado. Localize o arquivo que você deseja recuperar e copiá-lo para volume principal.

7. Depois de restaurar o arquivo, você pode excluir a pasta NTFS que contém o volume clonado.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Restaurar o banco de dados do Gerenciador de instantâneo StorSimple

Você deve fazer backup regularmente o banco de dados do Gerenciador de instantâneo StorSimple no computador host. Se ocorrer um desastre ou o computador host falha por algum motivo, você poderá restaurá-lo do backup. Criar o backup do banco de dados é um processo manual.

#### <a name="to-back-up-and-restore-the-database"></a>Fazer backup e restaurar o banco de dados

1. Pare o serviço de gerenciamento de StorSimple da Microsoft:

    1. Inicie o Gerenciador de servidor.

    2. No painel Gerenciador do servidor, no menu **Ferramentas** , selecione **Serviços**.

    3. Na janela **Serviços** , selecione o **Serviço de gerenciamento do Microsoft StorSimple**.

    4. No painel direito, em **Serviço de gerenciamento do Microsoft StorSimple**, clique em **Parar o serviço**.

2. No computador host, navegue até C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    >[AZURE.NOTE] ProgramData é uma pasta oculta.
 
3. Localize o arquivo XML de catálogo, copie o arquivo e armazenar a cópia em um local seguro ou na nuvem. Se o host falhar, você pode usar esse arquivo de backup para ajudar a recuperar as políticas de backup que você criou no Gerenciador de instantâneo StorSimple.

    ![Arquivo de catálogo de backup do Azure StorSimple](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)

4. Reinicie o serviço de gerenciamento de StorSimple da Microsoft: 

    1. No painel Gerenciador do servidor, no menu **Ferramentas** , selecione **Serviços**.
    
    2. Na janela **Serviços** , selecione o **Serviço de gerenciamento do Microsoft StorSimple**.

    3. No painel direito, em **Serviço de gerenciamento do Microsoft StorSimple**, clique em **reiniciar o serviço**.

5. No computador host, navegue até C:\ProgramData\Microsoft\StorSimple\BACatalog. 

6. Exclua o arquivo XML de catálogo e substituí-lo com a versão de backup que você criou. 

7. Clique no ícone da área de trabalho do Gerenciador de instantâneo StorSimple para iniciar o Gerenciador de instantâneo StorSimple. 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [usar o Gerenciador de instantâneo StorSimple administrar sua solução de StorSimple](storsimple-snapshot-manager-admin.md).
- Saiba mais sobre [fluxos de trabalho e tarefas do Gerenciador de instantâneo StorSimple](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).
