<properties 
   pageTitle="Tutorial de backup Array Virtual StorSimple | Microsoft Azure"
   description="Descreve como fazer backup de Array Virtual StorSimple compartilhamentos e volumes."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="back-up-your-storsimple-virtual-array"></a>Fazer backup de seu Array Virtual StorSimple

## <a name="overview"></a>Visão geral 

Este tutorial se aplica à versão do Microsoft Azure StorSimple Array Virtual (também conhecido como o dispositivo virtual do StorSimple local ou um dispositivo virtual StorSimple) em execução março de 2016 disponibilidade geral (GA) ou versões posteriores.

A matriz de Virtual StorSimple é híbrido nuvem armazenamento local dispositivo virtual que pode ser configurado como um servidor de arquivos ou um servidor de iSCSI. Ele pode criar backups, restaurar a partir de backups e realizar failover de dispositivo se recuperação for necessária. Quando configurado como um servidor de arquivos, ele também permite a recuperação de nível do item. Este tutorial descreve como usar o portal de clássico Azure ou web StorSimple da interface do usuário para criar backups manuais e agendados da sua matriz Virtual StorSimple.


## <a name="back-up-shares-and-volumes"></a>Fazer backup de compartilhamentos e volumes

Backups oferecem proteção no momento, melhorar a capacidade de recuperação e minimizar tempos de restauração de compartilhamentos e volumes. Você pode fazer backup de um volume ou compartilhamento em seu dispositivo de StorSimple de duas maneiras: **agendada** ou **Manual**. Cada um dos métodos é discutida nas seções a seguir.

> [AZURE.NOTE] Nesta versão, os backups agendados são criados por uma política de padrão que é executada diariamente em uma hora específica e faz backup de todos os volumes ou compartilhamentos no dispositivo. Não é possível criar políticas personalizadas para backups agendados neste momento.

## <a name="change-the-backup-schedule"></a>Alterar o agendamento de backup

Seu dispositivo de virtual StorSimple tem uma política de backup padrão que inicia em uma hora específica do dia (22:30) e faz backup de todos os volumes ou compartilhamentos no dispositivo uma vez por dia. Você pode alterar a hora em que o backup iniciado, mas a frequência e a retenção (que especifica o número de backups para reter) não podem ser alterados. Durante esses backups, todo o dispositivo virtual backup; Portanto, recomendamos que você agende esses backups pico.

Execute as seguintes etapas do [Azure portal clássico](https://manage.windowsazure.com/) para alterar a hora de início de backup padrão.

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Para alterar a hora de início para a política padrão de backup

1. Navegue até a guia de **configuração** do dispositivo.

2. Sob a seção de **Backup** , especifique a hora de início para o backup diariamente.

3. Clique em **Salvar**.

### <a name="take-a-manual-backup"></a>Fazer um backup manual

Além de backups agendados, você pode levar um manual (sob demanda) backup a qualquer momento.

#### <a name="to-create-a-manual-on-demand-backup"></a>Para criar um backup manual de (sob demanda)

1. Navegue até a guia de **compartilhamentos** ou **Volumes** .

2. Na parte inferior da página, clique em **Backup todos**. Você será solicitado a confirmar que você gostaria de fazer o backup agora. Clique no ícone de seleção ![Verifique o ícone](./media/storsimple-ova-backup/image3.png) para prosseguir com o backup.

    ![confirmação de backup](./media/storsimple-ova-backup/image4.png)

    Você será notificado de que um trabalho de backup está iniciando.

    ![Iniciando de backup](./media/storsimple-ova-backup/image5.png)

    Você será notificado de que o trabalho foi criado com êxito.

    ![trabalho de backup criado](./media/storsimple-ova-backup/image7.png)

3. Para controlar o andamento do trabalho, clique em **Exibir trabalho**.

4. Após concluir o trabalho de backup, vá para a guia **Catálogo de Backup** . Você deve ver o backup concluído.

    ![Backup concluído](./media/storsimple-ova-backup/image8.png)

5. Defina as seleções de filtro para o dispositivo apropriado, política de backup e intervalo de tempo e clique no ícone de seleção ![Verifique o ícone](./media/storsimple-ova-backup/image3.png).

    O backup deve aparecer na lista de conjuntos de backup que é exibida no catálogo.

## <a name="view-existing-backups"></a>Exibir backups existentes

Execute as seguintes etapas no portal de clássico do Azure para exibir os backups existentes.

#### <a name="to-view-existing-backups"></a>Para exibir os backups existentes

1. Na página Gerenciador de StorSimple serviço, clique na guia **Catálogo de Backup** .

2. Selecione um backup definido da seguinte maneira:

    1. Selecione o dispositivo.

    2. Na lista suspensa, escolha o compartilhamento ou volume para o backup que você deseja selecionar.

    3. Especifique o intervalo de tempo.

    4. Clique no ícone de seleção ![](./media/storsimple-ova-backup/image3.png) para executar esta consulta.

    Os backups associado com o compartilhamento selecionado ou volume deve aparecer na lista de conjuntos de backup.

![video_icon](./media/storsimple-ova-backup/video_icon.png) **vídeo disponível**

Assista ao vídeo para ver como você pode criar compartilhamentos, fazer backup de compartilhamentos e restaurar dados em uma matriz de Virtual StorSimple.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [administrar sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).
