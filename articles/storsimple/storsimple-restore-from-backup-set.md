<properties 
   pageTitle="Restaurar um volume StorSimple de backup | Microsoft Azure"
   description="Explica como usar a página de catálogo de Backup de serviço do Gerenciador de StorSimple para restaurar um volume StorSimple um conjunto de backup."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Restaurar um volume StorSimple um conjunto de backup

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Visão geral

A página de **Catálogo de Backup** exibe todos os conjuntos de backup que são criados quando são realizados backups manuais ou automatizados. Você pode usar esta página para listar todos os backups para uma política de backup ou um volume, selecione ou excluir backups ou usar um backup para restaurar ou clonar um volume.

 ![Página de catálogo de backup](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

Este tutorial explica como usar a página de **Catálogo de Backup** para restaurar um volume em seu dispositivo de um conjunto de backup.

## <a name="how-to-use-the-backup-catalog"></a>Como usar o catálogo de backup 

A página de **Catálogo de Backup** fornece uma consulta que ajuda você a restringir o backup conjunto de seleção. Você pode filtrar os conjuntos de backup que são recuperados com base nos seguintes parâmetros:

- **Dispositivo** – o dispositivo em que o conjunto de backup foi criado.
- **Política de backup** ou **volume** – a política de backup ou volume associado a esse conjunto de backup.
- **De** e **para** – o intervalo de data e hora quando o conjunto de backup foi criado.

Os conjuntos de backup filtrados são tabulação, em seguida, com base em atributos a seguir:

- **Nome** – o nome da política de backup ou volume associado com o conjunto de backup.
- **Tamanho** – o tamanho real do conjunto de backup.
- **Criado em** – a data e hora quando os backups foram criados. 
- **Tipo** – conjuntos de Backup podem ser instantâneos locais ou nuvem instantâneos. Um instantâneo local é um backup de todos os dados de volume armazenados localmente no dispositivo, enquanto um instantâneo de nuvem refere-se para o backup dos dados de volume residente na nuvem. Instantâneos locais fornecem acesso mais rápido, enquanto instantâneos de nuvem são escolhidos para resiliência de dados.
- **Iniciado por** – os backups podem ser iniciados automaticamente de acordo com a um cronograma ou manualmente por um usuário. (Você pode usar uma política de backup para agendar backups. Como alternativa, você pode usar a opção de **fazer backup** para fazer um backup interativo.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Como restaurar o volume de StorSimple de um backup

Você pode usar a página de **Catálogo de Backup** para restaurar o volume de StorSimple de um backup específico. 

> [AZURE.WARNING] Restaurar de um backup substituirão os volumes existentes do backup. Isso pode causar a perda de quaisquer dados escrita depois que o backup foi feito.

Antes de iniciar uma restauração em um volume, certifique-se de que o volume está offline. Você precisará levar o volume offline do host primeiro e, em seguida, o dispositivo. Siga as etapas em [levar um volume offline](storsimple-manage-volumes.md#take-a-volume-offline). Execute as seguintes etapas para restaurar um volume de um conjunto de backup.

### <a name="to-restore-from-a-backup-set"></a>Para restaurar um conjunto de backup

1. Na página Gerenciador de StorSimple serviço, clique na guia **Catálogo de Backup** .

    ![Catálogo de backup](./media/storsimple-restore-from-backup-set/HCS_Restore.png)

2. Selecione um backup definido da seguinte maneira:
  1. Selecione o dispositivo apropriado.
  2. Na lista suspensa, escolha a política de volume ou backup para o backup que você deseja selecionar.
  3. Especifique o intervalo de tempo.
  4. Clique no ícone de seleção ![Verifique o ícone](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) para executar esta consulta.
 
    Política de backup deve aparecer na lista de conjuntos de backup ou os backups associadas com o volume selecionado.

3. Expanda o backup definido para exibir os volumes associados. Esses volumes devem ser seguidos offline no host e dispositivo antes que você possa restaurá-las. Siga as etapas em [levar um volume offline](storsimple-manage-volumes.md#take-a-volume-offline).

    >  [AZURE.IMPORTANT] Certifique-se de que fez os volumes offline do host primeiro, antes de colocar os volumes offline no dispositivo. Se você não tirar os volumes offline do host, ele pode levar à corrupção de dados.

4. Selecione um conjunto de backup. Clique em **Restaurar** na parte inferior da página.

6. Você será solicitado para confirmação. 

    ![Página de confirmação](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)

7. Examine as informações de restauração e clique no ícone de seleção ![Verifique o ícone](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png). Isso iniciará um trabalho de restauração que você pode exibir acessando a página de **trabalhos** . 

8. Após a restauração for concluída, você pode verificar que o conteúdo de seus volumes é substituído por volumes do backup.

![Vídeo disponível](./media/storsimple-restore-from-backup-set/Video_icon.png) **vídeo disponível**

Para assistir a um vídeo que demonstra como você pode usar o clonar e restaurar recursos no StorSimple para recuperar arquivos excluídos, clique [aqui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar StorSimple volumes](storsimple-manage-volumes.md).

- Saiba como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
