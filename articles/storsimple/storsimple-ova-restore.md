<properties
   pageTitle="Restaurar a partir de um backup do seu Array Virtual StorSimple"
   description="Saiba mais sobre como restaurar um backup do seu Array Virtual StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli"/>

# <a name="restore-from-a-backup-of-your-storsimple-virtual-array"></a>Restaurar a partir de um backup do seu Array Virtual StorSimple

## <a name="overview"></a>Visão geral 

Este artigo se aplica à versão do Microsoft Azure StorSimple Array Virtual (também conhecido como o dispositivo virtual do StorSimple local ou um dispositivo virtual StorSimple) em execução março de 2016 disponibilidade geral (GA) ou posterior. Este artigo descreve passo a passo como restaurar um conjunto de backup de seus compartilhamentos ou volumes em sua matriz Virtual StorSimple. O artigo também detalha como a recuperação de nível de item funciona em seu Array Virtual StorSimple que está configurado como um servidor de arquivos.


## <a name="restore-shares-from-a-backup-set"></a>Restaurar compartilhamentos de um conjunto de backup


**Antes de tentar restaurar compartilhamentos, certifique-se de que você tem espaço suficiente no dispositivo para concluir a operação.** Para restaurar a partir de um backup, no [portal de clássico Azure](https://manage.windowsazure.com/), execute as seguintes etapas.

#### <a name="to-restore-a-share"></a>Para restaurar um compartilhamento

1.  Navegue até o **Catálogo de Backup**. Filtrar por dispositivo apropriado e intervalo de tempo para procurar os backups. Clique no ícone de seleção ![](./media/storsimple-ova-restore/image1.png) para executar a consulta.


1.  Na lista de conjuntos de backup exibido, clique em e selecione um backup específico. Expanda o backup para ver vários compartilhamentos sob ele. Clique em e selecione um compartilhamento que você deseja restaurar.

2.  Na parte inferior da página, clique em **restaurar como novo**.

3.  Isso iniciará o Assistente de **restauração como novo compartilhamento** . Na página **local e especificar o nome** :


    1.  Verifique se o nome do dispositivo de origem. Isso deve ser o dispositivo que contém o compartilhamento que você deseja restaurar. A seleção do dispositivo fica acinzentada. Para selecionar um dispositivo de origem diferentes, você precisará sair do assistente e selecione novamente o conjunto de backup novamente.

    2.  Forneça um nome de compartilhamento. O nome do compartilhamento deve conter caracteres de 3 a 127.

    3.  Examine o tamanho, tipo e permissões associadas com o compartilhamento que você está tentando restaurar. Você poderá modificar as propriedades de compartilhamento através do Windows Explorer após a restauração for concluída.

    4.  Clique no ícone de seleção ![](./media/storsimple-ova-restore/image1.png).

        ![](./media/storsimple-ova-restore/image9.png)

1.  Após concluir o trabalho de restauração, a restauração iniciará e você verá outra notificação. Para monitorar o progresso de restauração, clique em **Exibir trabalho**. Você será levado para a página de **trabalhos** .

2.  Você pode controlar o andamento do trabalho de restauração. Quando a restauração estiver 100% concluída, navegue para a página de **compartilhamentos** em seu dispositivo.

3.  Agora você pode exibir o novo compartilhamento restaurado na lista de compartilhamentos em seu dispositivo. Observe que a restauração é feita para o mesmo tipo do compartilhamento. Um compartilhamento hierárquico é restaurado como hierárquico e um compartilhamento localmente fixado como um compartilhamento localmente fixado.

Agora você tiver concluído a configuração de dispositivo e aprendeu a fazer backup ou restaurar um compartilhamento. 


## <a name="restore-volumes-from-a-backup-set"></a>Restaurar volumes de um conjunto de backup


Para restaurar a partir de um backup, no portal de clássico do Azure, execute as seguintes etapas. A operação de restauração restaura o backup para um novo volume no mesmo dispositivo virtual; Você não pode restaurar para um dispositivo diferente.

#### <a name="to-restore-a-volume"></a>Restaurar um volume

1.  Navegue até o **Catálogo de Backup**. Filtrar por dispositivo apropriado e intervalo de tempo para procurar os backups. Clique no ícone de seleção ![](./media/storsimple-ova-restore/image1.png) para executar a consulta.

2.  Na lista de conjuntos de backup exibido, clique em e selecione um backup específico. Expanda o backup para ver os vários volumes sob ele. Selecione o volume que você deseja restaurar. 

5.  Na parte inferior da página, clique em **restaurar como novo**. O assistente **restaurar como novo volume** será iniciado.

1.  Na página **local e especificar o nome** :


    1.  Verifique se o nome do dispositivo de origem. Isso deve ser o dispositivo que contém o volume que você deseja restaurar. A seleção do dispositivo não está disponível. Para selecionar um dispositivo de origem diferentes, você precisará sair do assistente e selecione novamente o conjunto de backup novamente.

    2.  Forneça um nome de volume para o volume está sendo restaurado como novo. O nome do volume deve conter caracteres de 3 a 127.

    3.  Clique no ícone de seta.

        ![](./media/storsimple-ova-restore/image12.png)

1.  Na página **especificar hosts que podem usar este volume** , selecione os ACRs apropriados na lista suspensa.

    ![](./media/storsimple-ova-restore/image13.png)

1.  Clique no ícone de seleção ![](./media/storsimple-ova-restore/image1.png). Isso iniciará um trabalho de restauração e você verá a seguinte notificação que o trabalho está em andamento.

2.  Após concluir o trabalho de restauração, a restauração iniciará e você verá outra notificação. Para monitorar o progresso de restauração, clique em **Exibir trabalho**. Você será levado para a página de **trabalhos** .

3.  Você pode controlar o andamento do trabalho de restauração. Navegar de volta para a página de **Volumes** em seu dispositivo.

4.  Agora você pode exibir o novo volume restaurado na lista de volumes em seu dispositivo. Observe que a restauração é feita para o mesmo tipo de volume. Um volume hierárquico é restaurado como hierárquico e um volume localmente fixado será restaurado como um volume localmente fixado.

5.  Depois que o volume aparece online na lista de volumes, o volume está disponível para uso.  No host do iniciador iSCSI, atualize a lista de destinos na janela de propriedades do iniciador iSCSI.  Um novo destino que contém o nome de volume restaurado deverá aparecer como 'Inativo' na coluna status.

6.  Selecione o destino e clique em **Conectar**.   Quando o iniciador estiver conectado ao destino, o status deve mudar para **conectado**. 

7.  Na janela **Gerenciamento de disco** , os volumes montados aparecerá como mostrado na ilustração a seguir. O volume de descobertas de atalho (clique o nome do disco) e, em seguida, clique em **Online**.

> [AZURE.IMPORTANT] Ao tentar restaurar um volume ou em um compartilhamento de um backup definido, se o trabalho de restauração falha, um volume de destino ou compartilhar ainda pode ser criada no portal. É importante que você excluir este volume de destino ou compartilha do portal para minimizar problemas futuros decorrente desse elemento.

## <a name="item-level-recovery-ilr"></a>Recuperação de nível de item (ILR)

Esta versão apresenta a recuperação de nível de item (ILR) em um dispositivo virtual StorSimple configurado como um servidor de arquivos. O recurso permite que você faça recuperação granular de arquivos e pastas de um backup de nuvem de todos os compartilhamentos no dispositivo StorSimple. Os usuários podem recuperar arquivos excluídos de backups recentes usando um modelo pessoal.

Cada compartilhamento tem uma pasta de *.backups* que contém os backups mais recentes. O usuário pode navegar até o backup desejado, copie relevantes arquivos e pastas do backup e restaurá-las. Isso elimina chamadas para administradores para restaurar arquivos a partir de backups.

1.  Ao executar o ILR, você pode exibir os backups através do Windows Explorer. Clique em compartilhar específico que você deseja examinar o backup. Você verá uma pasta de *.backups* criada sob o compartilhamento que armazena todos os backups. Expanda a pasta *.backups* para exibir os backups. A pasta mostrará o modo de exibição detalhada de toda a hierarquia de backup. Este modo de exibição é criado sob demanda e normalmente leva apenas alguns segundos para criar.

    Os últimos 5 backups são exibidos dessa forma e podem ser usados para executar uma recuperação do nível do item. Os 5 backups recentes incluem o padrão agendada e os backups manuais.

    
    -   **Os backups agendados** nomeado como &lt;nome do dispositivo&gt;DailySchedule-AAAAMMDD-HHMMSS-UTC.

    -   **Backups manuais** nomeado como Ad-hoc-AAAAMMDD-HHMMSS-UTC.
    
        ![](./media/storsimple-ova-restore/image14.png)

1.  Identifica o backup que contém a versão mais recente do arquivo excluído. Embora o nome da pasta contém um carimbo de hora UTC em cada um dos casos acima, a hora em que a pasta foi criada é o dispositivo real quando o backup é iniciado. Use o carimbo de hora de pasta para localizar e identificar os backups.

2.  Localize a pasta ou o arquivo que você deseja restaurar no backup identificado na etapa anterior. Observação que você só pode exibir os arquivos ou pastas que você tem permissões para. Se você não conseguir acessar determinados arquivos ou pastas, será necessário contatar um administrador de compartilhamento quem pode usar o Windows Explorer para editar as permissões de compartilhamento e conceder acesso para o arquivo ou pasta específica. É uma prática recomendada que o administrador de compartilhamento ser um grupo de usuários em vez de um único usuário.

3.  Copie o arquivo ou pasta para o compartilhamento apropriado no seu servidor de arquivos StorSimple.

![video_icon](./media/storsimple-ova-restore/video_icon.png) **vídeo disponível**

Assista ao vídeo para ver como você pode criar compartilhamentos, fazer backup de compartilhamentos e restaurar dados em uma matriz de Virtual StorSimple.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [administrar sua matriz Virtual StorSimple usando o interface do usuário de web local](storsimple-ova-web-ui-admin.md).
