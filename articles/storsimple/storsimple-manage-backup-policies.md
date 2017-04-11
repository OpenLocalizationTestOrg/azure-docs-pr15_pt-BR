<properties 
   pageTitle="Gerenciar suas políticas de backup StorSimple | Microsoft Azure"
   description="Explica como você pode usar o serviço do Gerenciador de StorSimple para criar e gerenciar backups manuais, agendas de backup e retenção de backup."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/10/2016"
   ms.author="v-sharos"/>

# <a name="use-the-storsimple-manager-service-to-manage-backup-policies"></a>Usar o serviço do Gerenciador de StorSimple para gerenciar políticas de backup

[AZURE.INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>Visão geral

Este tutorial explica como usar a página de **Políticas de Backup** de serviço do Gerenciador de StorSimple para controlar os processos de backup e retenção de backup para os volumes de StorSimple. Ele também descreve como completar um backup manual.

A página de **Políticas de Backup** permite que você gerenciar políticas de backup e agendar local e nuvem instantâneos. (Políticas de backup são usadas para configurar agendas de backup e retenção de backup para um conjunto de volumes). Políticas de backup permitem que você tire um instantâneo de vários volumes simultaneamente. Isso significa que os backups criados por uma política de backup será cópias consistentes de falha. Esta página lista as políticas de backup, seus tipos, os volumes associados, o número de backups mantidos e a opção para habilitar essas políticas.

A página de **Políticas de Backup** também permite filtrar as políticas de backup existentes por uma ou mais dos seguintes campos:

- **Nome de política** – o nome associado à política. Os diferentes tipos de políticas incluem:

   - Diretivas agendadas, que sejam criadas explicitamente pelo usuário.
   - Diretivas automáticas, que são criadas quando o backup padrão para essa opção de volume foi habilitado no momento da criação de volume. Essas políticas são nomeadas como VolumeName_Default onde nome do Volume refere-se para o nome do volume StorSimple configurado pelo usuário no portal de clássico do Azure. As políticas automáticas resultarem em instantâneos diários de nuvem, começando na hora do dispositivo de 22:30.
   - Diretivas importadas, que foram criadas no Gerenciador de instantâneo StorSimple. Eles têm uma marca que descreva o host do Gerenciador de instantâneo StorSimple que as políticas foram importadas de.

- **Volumes** – os volumes associados a política. Todos os volumes associados a uma política de backup são agrupados quando os backups são criados.

- **Último backup bem-sucedido** – a data e hora do último backup bem-sucedido realizada com esta política.

- **Próximo backup** – a data e hora do próximo backup agendado que será iniciada por essa política.

- **Cronogramas** – o número de agendamentos associadas com a política de backup.

As operações usadas com frequência que você pode executar desta página são:

- Adicionar uma política de backup 
- Adicionar ou modificar uma agenda 
- Excluir uma política de backup 
- Fazer um backup manual 
- Criar uma política de backup personalizada com vários volumes e agendas 

## <a name="add-a-backup-policy"></a>Adicionar uma política de backup

Adicione uma política de backup para agendar automaticamente seus backups. Execute as seguintes etapas no portal de clássico do Azure para adicionar uma política de backup para seu dispositivo de StorSimple. Depois de adicionar a política, você pode definir uma agenda (consulte [Adicionar ou modificar uma agenda](#add-or-modify-a-schedule)).

[AZURE.INCLUDE [storsimple-add-backup-policy](../../includes/storsimple-add-backup-policy.md)]

![Vídeo disponível](./media/storsimple-manage-backup-policies/Video_icon.png) **vídeo disponível**

Para assistir a um vídeo que demonstra como criar um local ou na nuvem a política de backup, clique [aqui](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/).


## <a name="add-or-modify-a-schedule"></a>Adicionar ou modificar uma agenda

Você pode adicionar ou modificar uma agenda que está anexada a uma política de backup existente em seu dispositivo de StorSimple. Execute as seguintes etapas no portal de clássico do Azure para adicionar ou modificar uma agenda.

[AZURE.INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule.md)]

## <a name="delete-a-backup-policy"></a>Excluir uma política de backup

Execute as seguintes etapas no portal de clássico do Azure para excluir uma política de backup no seu dispositivo de StorSimple.

[AZURE.INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]


## <a name="take-a-manual-backup"></a>Fazer um backup manual

Execute as seguintes etapas no portal de clássico do Azure para criar um backup (manual) sob demanda para um único volume.

[AZURE.INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>Criar uma política de backup personalizada com vários volumes e agendas

Execute as seguintes etapas no portal de clássico do Azure para criar uma política de backup personalizada que tem vários volumes e agendas.

[AZURE.INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy.md)]


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
