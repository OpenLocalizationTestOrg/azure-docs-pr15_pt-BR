<properties 
   pageTitle="Restaurar um volume StorSimple de backup | Microsoft Azure"
   description="Explica como usar a página de catálogo de Backup de serviço do Gerenciador de StorSimple para restaurar um volume StorSimple um conjunto de backup."
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

# <a name="restore-a-storsimple-volume-from-a-backup-set-update-2"></a>Restaurar um volume StorSimple um conjunto de backup (atualização 2)

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Visão geral

A página de **Catálogo de Backup** exibe todos os conjuntos de backup que são criados quando são realizados backups manuais ou automatizados. Você pode usar esta página para listar todos os backups para uma política de backup ou um volume, selecione ou excluir backups ou usar um backup para restaurar ou clonar um volume.

 ![Página de catálogo de backup](./media/storsimple-restore-from-backup-set-u2/restore.png)

Este tutorial explica como usar a página de **Catálogo de Backup** para restaurar o seu dispositivo de um conjunto de backup.

Você pode restaurar um volume de um local ou fazer backup de nuvem. Nos dois casos, a operação de restauração traz o volume on-line imediatamente enquanto dados são baixados no plano de fundo. 

Antes de iniciar uma operação de restauração, você deve estar ciente do seguinte:

- **Que você deve executar o volume offline** – leve o volume offline no host e o dispositivo antes de iniciar a operação de restauração. Embora a operação de restauração traz automaticamente o volume on-line no dispositivo, você deve trazer manualmente o dispositivo on-line do host. Você pode trazer o volume on-line no host assim que o volume está online no dispositivo. (Não necessário aguardar até que a operação de restauração foi concluída.) Para obter procedimentos, vá para [tirar um volume offline](storsimple-manage-volumes-u2.md#take-a-volume-offline).

- **Tipo de volume após a restauração** – volumes excluídos são restaurados baseados no tipo no instantâneo; Isto é, volumes que foram fixados localmente são restaurados como volumes localmente fixados e volumes que foram hierárquico são restaurados como volumes hierárquicos.

    Para volumes existentes, o tipo de uso atual do volume substitui o tipo que está armazenado no instantâneo. Por exemplo, se você restaurar um volume de um instantâneo que foi executado quando o tipo de volume foi hierárquico e que tipo de volume agora é fixado localmente (devido a uma operação de conversão que foi executada), em seguida, o volume será restaurado como um volume localmente fixado. Da mesma forma, se um volume localmente fixado existente foi expandido e subsequentemente restaurado a partir de um instantâneo mais antigo seja tomado quando o volume era menor, o volume restaurado manterá o tamanho expandido atual.

    Você não pode converter um volume de um volume hierárquico para um volume localmente fixado ou de um volume localmente fixado a um volume hierárquico enquanto o volume está sendo restaurado. Aguarde até que a operação de restauração for concluída, e, em seguida, você pode converter o volume em outro tipo. Para obter informações sobre como converter um volume, vá para [alterar o tipo de volume](storsimple-manage-volumes-u2.md#change-the-volume-type). 

- **O tamanho do volume será refletido no volume restaurado** – essa é uma consideração importante se você estiver restaurando um volume localmente fixado que foi excluído (porque volumes localmente fixados totalmente são provisionados). Certifique-se de que você tem espaço suficiente antes de tentar restaurar um volume localmente fixado que foi excluído previamente. 

- **Que você não pode expandir um volume enquanto ela estiver sendo restaurada** – aguardar até que a operação de restauração seja concluída antes de tentar expandir o volume. Para obter informações sobre expandindo um volume, vá para [modificar um volume](storsimple-manage-volumes-u2.md#modify-a-volume).

- **Você pode executar um backup enquanto você está restaurando um volume local** – para obter os procedimentos vá para [usar o serviço de Gerenciador de StorSimple para gerenciar políticas de backup](storsimple-manage-backup-policies.md).

- **Que você pode cancelar uma operação de restauração** – se você cancelar o trabalho de restauração e, em seguida, o volume será revertida para o estado em que se encontrava antes de você iniciou a operação de restauração. Para obter procedimentos, vá para [Cancelar um trabalho](storsimple-manage-jobs-u2.md#cancel-a-job).

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

Você pode usar a página de **Catálogo de Backup** para restaurar o volume de StorSimple de um backup específico. Lembre-se, no entanto, que restaura um volume reverterá o volume para o estado em que estava quando o backup foi feito. Quaisquer dados que foi adicionados após a operação de backup serão perdidos.

> [AZURE.WARNING] Restaurar de um backup substituirão os volumes existentes do backup. Isso pode causar a perda de quaisquer dados escrita depois que o backup foi feito.

### <a name="to-restore-your-volume"></a>Para restaurar o volume

1. Na página Gerenciador de StorSimple serviço, clique na guia **Catálogo de Backup** .

    ![Catálogo de backup](./media/storsimple-restore-from-backup-set-u2/restore.png)

2. Selecione um backup definido da seguinte maneira:
  1. Selecione o dispositivo apropriado.
  2. Na lista suspensa, escolha a política de volume ou backup para o backup que você deseja selecionar.
  3. Especifique o intervalo de tempo.
  4. Clique no ícone de seleção ![Verifique o ícone](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) para executar esta consulta.
 
    Política de backup deve aparecer na lista de conjuntos de backup ou os backups associadas com o volume selecionado.

3. Expanda o backup definido para exibir os volumes associados. Esses volumes devem ser seguidos offline no host e dispositivo antes que você possa restaurá-las. Acessar os volumes na página **Contêineres de Volume** e, em seguida, siga as etapas em [levar um volume offline](storsimple-manage-volumes-u2.md#take-a-volume-offline) para fazê-lo offline.

    > [AZURE.IMPORTANT] Certifique-se de que fez os volumes offline do host primeiro, antes de colocar os volumes offline no dispositivo. Se você não tirar os volumes offline do host, ele pode levar à corrupção de dados.

4. Navegue de volta para a guia **Catálogo de Backup** e selecione um conjunto de backup.

5. Clique em **Restaurar** na parte inferior da página.

6. Você será solicitado para confirmação. Examine as informações de restauração e marque a caixa de seleção de confirmação.

    ![Página de confirmação](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)

7. Clique no ícone de seleção ![Verifique o ícone](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png). Isso iniciará um trabalho de restauração que você pode exibir acessando a página de **trabalhos** . 

8. Após a restauração for concluída, você pode verificar que o conteúdo de seus volumes é substituído por volumes do backup.

![Vídeo disponível](./media/storsimple-restore-from-backup-set-u2/Video_icon.png) **vídeo disponível**

Para assistir a um vídeo que demonstra como você pode usar o clonar e restaurar recursos no StorSimple para recuperar arquivos excluídos, clique [aqui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="if-the-restore-fails"></a>Se a restauração falhar

Se a operação de restauração falhar por algum motivo, você receberá um alerta. Nesse caso, atualize a lista de backup para verificar se o backup ainda é válido. Se o backup é válido e você está restaurando da nuvem, em seguida, problemas de conectividade podem estar causando o problema. 

Para concluir a operação de restauração, faça o volume offline no host e repita a operação de restauração. Observe que todas as modificações aos dados volume que foram executadas durante o processo de restauração serão perdidas.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar StorSimple volumes](storsimple-manage-volumes-u2.md).

- Saiba como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
