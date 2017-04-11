<properties 
   pageTitle="Exibir e gerenciar trabalhos de StorSimple | Microsoft Azure"
   description="Descreve a página de trabalhos de serviço do Gerenciador de StorSimple e como usá-lo para controlar trabalhos de backup recentes, atuais e agendados."
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
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs"></a>Usar o serviço do Gerenciador de StorSimple para exibir e gerenciar trabalhos de StorSimple

[AZURE.INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>Visão geral

A página **trabalhos** fornece um único portal central para visualização e gerenciamento de trabalhos que foram iniciados em dispositivos conectado ao seu serviço de Gerenciador de StorSimple. Você pode exibir trabalhos agendados, executando, concluídos e falhas para vários dispositivos. Os resultados são apresentados em um formato tabular. 

![Página trabalhos](./media/storsimple-manage-jobs/HCS_JobsPage.png)

Você pode localizar rapidamente os trabalhos que você está interessado em filtrando campos como:

- **Status** – trabalhos podem estar em execução, agendada, falha, concluído, cancelamento ou cancelada.

- **Tipo** – trabalhos podem ser criados como resultado de um agendada ou um backup de sob demanda (**Fazer Backup**), clonagem, uma restauração de dispositivo ou uma operação de atualização.

- **Dispositivos** – trabalhos são iniciados em um determinado dispositivo conectado ao seu serviço.

- **De e para** – trabalhos podem ser filtrados com base no intervalo de data e hora.

Os trabalhos filtrados são tabulação, em seguida, com base nas seguintes atributos:

- **Tipo** – Backup, clonar, restauração, failover ou atualização.

- **Status** – executando, agendada, falha, concluído, cancelamento ou cancelada.

- **Entidade** – os trabalhos podem ser associados um volume, uma política de backup ou um dispositivo. Um trabalho de clonar está associado um volume, enquanto um trabalho de backup agendado está associado a uma política de backup. Um trabalho de dispositivo é criado como resultado de uma operação de restauração ou uma recuperação de dados (DR).

- **Dispositivo** – o nome do dispositivo em que o trabalho foi iniciado.

- **Started On** – a hora em que o trabalho foi iniciado.

- **Andamento** – a porcentagem de conclusão de um trabalho em execução. Para um trabalho concluído, isso deve ser sempre 100%.

A lista de trabalhos é atualizada a cada 30 segundos.

Você pode executar as seguintes ações de trabalho nesta página:

- Exibir detalhes do trabalho

- Cancelar um trabalho

## <a name="view-job-details"></a>Exibir detalhes do trabalho

Execute as seguintes etapas para exibir os detalhes de qualquer trabalho.

#### <a name="to-view-job-details"></a>Exibir detalhes de trabalho

1. Na página **trabalhos** , exiba o trabalho (s) que você está interessado em executando uma consulta com filtros apropriados. Você pode procurar concluída, executando ou cancelado trabalhos.

2. Selecione um trabalho.

3. Na parte inferior da página, clique em **detalhes**.

4. Na caixa de diálogo **Detalhes do trabalho de Backup** , você pode exibir o status, detalhes, estatísticas de tempo e estatísticas de dados.

## <a name="cancel-a-job"></a>Cancelar um trabalho

Execute as seguintes etapas para cancelar um trabalho em execução.

### <a name="to-cancel-a-job"></a>Para cancelar um trabalho

1. Na página **trabalhos** , exiba a execução de trabalho (s) que você deseja cancelar executando uma consulta com filtros apropriados.

1. Selecione o trabalho.

1. Na parte inferior da página, clique em **Cancelar**.

1. Quando solicitado para confirmação, clique em **Sim**.

Este trabalho será cancelado agora.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar suas políticas de backup StorSimple](storsimple-manage-backup-policies.md).

- Saiba como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
