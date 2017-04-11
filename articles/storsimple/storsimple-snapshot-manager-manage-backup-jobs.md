<properties 
   pageTitle="Gerenciador de instantâneo StorSimple trabalhos de backup | Microsoft Azure"
   description="Descreve como usar o snap-in MMC Gerenciador de instantâneo StorSimple para exibir e gerenciar trabalhos de backup programados, atualmente em execução e concluídos."
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


# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Use o Gerenciador de instantâneo StorSimple para exibir e gerenciar trabalhos de backup

## <a name="overview"></a>Visão geral

O nó de **trabalhos** no painel de **escopo** mostra o **agendada**, **últimas 24 horas**e **executando** as tarefas de backup que você iniciou interativamente ou por uma política de configurado. 

Este tutorial explica como você pode usar o nó de **trabalhos** para exibir informações sobre trabalhos de backup programadas, recentes e em execução no momento. (A lista de trabalhos e informações correspondente aparece no painel de **resultados** .) Além disso, você pode um trabalho listados de atalho e ver um menu de contexto que lista Ações disponíveis.

## <a name="view-scheduled-jobs"></a>Exibir os trabalhos agendados

Use o procedimento a seguir para exibir os trabalhos de backup agendados.

#### <a name="to-view-scheduled-jobs"></a>Para exibir os trabalhos agendados

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple. 

2. No painel de **escopo** , expanda o nó de **trabalhos** e clique em **agendada**. As seguintes informações aparecem no painel de **resultados** :

    - **Nome** – o nome do instantâneo agendado

    - **Executar próximo** – a data e hora do próximo instantâneo programado

    - **Última execução** – a data e hora do instantâneo agendado mais recente

    >[AZURE.NOTE] Para uso únicos instantâneos somente, a **Próxima execução** e a **Última execução** serão iguais. 
 
    ![Trabalhos de backup agendados](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
 
3. Para executar ações adicionais em um trabalho específico, clique com botão direito no nome do trabalho no painel de **resultados** e selecione as opções de menu.

## <a name="view-recent-jobs"></a>Exibir trabalhos recentes

Use o procedimento a seguir para exibir o backup e restaurar trabalhos que foram concluídos nas últimas 24 horas.

#### <a name="to-view-recent-jobs"></a>Para exibir trabalhos recentes

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , expanda o nó de **trabalhos** e clique em **últimas 24 horas**. O painel de **resultados** mostra trabalhos de backup para as últimas 24 horas (para um máximo de 64 trabalhos). As seguintes informações aparecem no painel de **resultados** , dependendo das opções de **exibição** que você especificar:

    - **Nome** – o nome do instantâneo agendado.
 
    - **Introdução** – a data e hora quando o instantâneo começou.

    - **Parado** – a data e hora quando o instantâneo concluída ou foi encerrado.

    - Tempo **decorrido** – a quantidade de tempo entre o **iniciado** e **interrompido** .

    - **Status** – o estado do trabalho concluído recentemente. **Sucesso** indica que o backup foi criado com êxito. **Falha** indica que o trabalho não foi executado com êxito.

    - **Informações** – o motivo da falha.

    - **Bytes processados (MB)** – a quantidade de dados a partir do grupo de volume que foi processado (em MB). 

    ![Trabalhos que executou nas últimas 24 horas](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 

3. Para executar ações adicionais em um trabalho específico, clique com botão direito no nome do trabalho no painel de **resultados** e selecione as opções de menu.

    ![Excluir um trabalho](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png) 
     
## <a name="view-currently-running-jobs"></a>Exibir trabalhos em execução no momento

Use o procedimento a seguir para exibir os trabalhos que estão sendo executados.

#### <a name="to-view-currently-running-jobs"></a>Para exibir trabalhos em execução no momento

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , expanda o nó de **trabalhos** e clique em **Executar**. Dependendo das opções de **exibição** que você especificar, as seguintes informações aparecem no painel de **resultados** : 

    - **Nome** – o nome do instantâneo agendado.

    - **Introdução** – a data e hora quando o instantâneo começou.

    - **Ponto de verificação** – a ação atual do backup.

    - **Status** – a porcentagem de conclusão.
    
    - **Decorrido** – a quantidade de tempo decorrido desde o backup foi iniciada. 

    - **Taxa de transferência média (MB)** – proporção do total de bytes dos dados processados do tempo total gasto para processamento (MB).

    - **Bytes processados (MB)** – total de bytes dos dados processados (em MB).

    - **Bytes gravados (MB)** – total de bytes de dados gravados (em MB). Ele inclui os dados, bem como os metadados e, portanto, é geralmente maior do que o Bytes processados.

    ![Trabalhos em execução](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)

3. Para executar ações adicionais em um trabalho específico, clique com botão direito no nome do trabalho no painel de **resultados** e selecione as opções de menu.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Gerenciador de instantâneo StorSimple administrar sua solução de StorSimple](storsimple-snapshot-manager-admin.md).
- Saiba como [usar o Gerenciador de instantâneo StorSimple para gerenciar o catálogo de backup](storsimple-snapshot-manager-manage-backup-catalog.md).















            


 

