<properties 
   pageTitle="Gerenciar o catálogo de backup StorSimple | Microsoft Azure"
   description="Explica como usar a página de catálogo de Backup de serviço do Gerenciador de StorSimple para a lista, selecione e exclua os conjuntos de backup de um volume."
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
   ms.date="04/28/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-your-backup-catalog"></a>Usar o serviço do Gerenciador de StorSimple para gerenciar seu catálogo de backup

## <a name="overview"></a>Visão geral

A página de **Catálogo de Backup** de serviço do Gerenciador de StorSimple exibe todos os conjuntos de backup que são criados quando são realizados backups manuais ou programados. Você pode usar esta página para listar todos os backups para uma política de backup ou um volume, selecione ou excluir backups ou usar um backup para restaurar ou clonar um volume.

Este tutorial explica como lista, selecione e exclua um conjunto de backup. Para saber como restaurar seu dispositivo de backup, vá para [restaurar o seu dispositivo de um conjunto de backup](storsimple-restore-from-backup-set.md). Para saber como clonar um volume, vá para [clonar um volume StorSimple](storsimple-clone-volume.md).

![Catálogo de backup](./media/storsimple-manage-backup-catalog/backupcatalog.png) 

A página de **Catálogo de Backup** fornece uma consulta para restringir o backup conjunto de seleção. Você pode filtrar os conjuntos de backup que são recuperados, com base nos seguintes parâmetros:

- **Dispositivo** – o dispositivo em que o conjunto de backup foi criado.

- **Política de backup ou Volume** – a política de backup ou volume associado a esse conjunto de backup.

- **De e para** – o intervalo de data e hora quando o conjunto de backup foi criado.

Os conjuntos de backup filtrados são tabulação, em seguida, com base em atributos a seguir:

- **Nome** – o nome da política de backup ou volume associado com o conjunto de backup.

- **Tamanho** – o tamanho real do conjunto de backup.

- **Criado no** – a data e hora quando os backups foram criados. 

- **Tipo** – conjuntos de Backup podem ser instantâneos locais ou nuvem instantâneos. Um instantâneo local é um backup de todos os dados de volume armazenados localmente no dispositivo, enquanto um instantâneo de nuvem refere-se para o backup dos dados de volume residente na nuvem. Instantâneos locais fornecem acesso mais rápido, enquanto instantâneos de nuvem são escolhidos para resiliência de dados.

- **Iniciada por** – os backups podem ser iniciados automaticamente por um cronograma ou manualmente por um usuário. Você pode usar uma política de backup para agendar backups. Como alternativa, você pode usar a opção de **fazer backup** para fazer um backup manual.

## <a name="list-backup-sets-for-a-volume"></a>Conjuntos de backup de lista para um volume
 
Conclua as etapas a seguir para listar todos os backups para um volume.

#### <a name="to-list-backup-sets"></a>Para conjuntos de backup de lista

1. Na página Gerenciador de StorSimple serviço, clique na guia **Catálogo de Backup** .

2. Filtre as seleções da seguinte maneira:

    1. Selecione o dispositivo apropriado.

    2. Na lista suspensa, escolha um volume para exibir os backups correspondente.

    3. Especifique o intervalo de tempo.

    4. Clique no ícone de seleção ![Ícone de seleção](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) para executar esta consulta.
 
    Os backups associados com o volume selecionado devem aparecer na lista de conjuntos de backup.

## <a name="select-a-backup-set"></a>Selecione um conjunto de backup

Conclua as seguintes etapas para selecionar um conjunto de backup de um volume ou uma política de backup.

#### <a name="to-select-a-backup-set"></a>Para selecionar um conjunto de backup

1. Na página Gerenciador de StorSimple serviço, clique na guia **Catálogo de Backup** .

2. Filtre as seleções da seguinte maneira:

    1. Selecione o dispositivo apropriado.

    2. Na lista suspensa, escolha a política de volume ou backup para o backup que você deseja selecionar.

    3. Especifique o intervalo de tempo.

    4. Clique no ícone de seleção ![Ícone de seleção](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) para executar esta consulta.

    Política de backup deve aparecer na lista de conjuntos de backup ou os backups associadas com o volume selecionado.

3. Selecione e expanda um conjunto de backup. As opções de **Restaurar** e **Excluir** são exibidas na parte inferior da página. Você pode executar qualquer uma das seguintes ações no conjunto de backup que você selecionou.

## <a name="delete-a-backup-set"></a>Excluir um conjunto de backup

Exclua um backup quando você não deseja manter os dados associados a ele. Execute as seguintes etapas para excluir um conjunto de backup.

#### <a name="to-delete-a-backup-set"></a>Para excluir um conjunto de backup

1. Na página Gerenciador de StorSimple serviço, clique na **guia Catálogo de Backup**.

2. Filtre as seleções da seguinte maneira:

    1. Selecione o dispositivo apropriado.

    2. Na lista suspensa, escolha a política de volume ou backup para o backup que você deseja selecionar.

    3. Especifique o intervalo de tempo.

    4. Clique no ícone de seleção ![Ícone de seleção](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) para executar esta consulta.

    Política de backup deve aparecer na lista de conjuntos de backup ou os backups associadas com o volume selecionado.

3. Selecione e expanda um conjunto de backup. As opções de **Restaurar** e **Excluir** são exibidas na parte inferior da página. Clique em **Excluir**.

4. Você será notificado quando a exclusão está em andamento e quando tiver concluído com êxito. Após a exclusão, atualize a consulta nesta página. O conjunto de backup excluído não aparecerá mais na lista de conjuntos de backup.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o catálogo de backup para restaurar o seu dispositivo de um conjunto de backup](storsimple-restore-from-backup-set.md).

- Saiba como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
