<properties 
   pageTitle="Exibir e gerenciar trabalhos de Array Virtual StorSimple | Microsoft Azure"
   description="Descreve a página de trabalhos de serviço do Gerenciador de StorSimple e como usá-lo para controlar trabalhos recentes e atuais para a matriz Virtual StorSimple."
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
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Use o serviço do Gerenciador de StorSimple para exibir trabalhos para a matriz Virtual StorSimple

## <a name="overview"></a>Visão geral

A página de **trabalhos** fornece um único portal central para exibir e gerenciar trabalhos que foram iniciados no Virtual matrizes (também conhecido como dispositivos virtuais no local) que estão conectadas ao seu serviço de Gerenciador de StorSimple. Você pode exibir trabalhos em execução, concluídos e falhas para vários dispositivos virtuais. Os resultados são apresentados em um formato tabular. 

![Página trabalhos](./media/storsimple-ova-manage-jobs/ovajobs1.png)

Você pode localizar rapidamente os trabalhos que você está interessado em filtrando campos como:

- **Status** – você pode procurar tudo, trabalhos em execução, concluídos ou falhas.
- **De e para** – trabalhos podem ser filtrados com base no intervalo de data e hora.
- **Tipo** – o tipo de trabalho pode ser restauração de backup, todos, failover, baixe atualizações ou instalar atualizações.
- **Dispositivos** – trabalhos são iniciados em um dispositivo específico conectado ao seu serviço. Os trabalhos filtrados são tabulação, em seguida, com base nas seguintes atributos:

    - **Tipo** – o tipo de trabalho pode ser restauração de backup, todos, failover, baixe atualizações ou instalar atualizações.

    - **Status** – can trabalhos ser tudo, executando, concluído ou falha.

    - **Entidade** – os trabalhos podem ser associados um volume, compartilhar ou dispositivo. 

    - **Dispositivo** – o nome do dispositivo em que o trabalho foi iniciado.

    - **Iniciada no** – a hora em que o trabalho foi iniciado.

    - **Andamento** – a porcentagem de conclusão de um trabalho em execução. Para um trabalho concluído, isso deve ser sempre 100%.

A lista de trabalhos é atualizada a cada 30 segundos.

## <a name="view-job-details"></a>Exibir detalhes do trabalho

Execute as seguintes etapas para exibir os detalhes de qualquer trabalho.

#### <a name="to-view-job-details"></a>Exibir detalhes de trabalho

1. Na página **trabalhos** , exiba o trabalho (s) que você está interessado em executando uma consulta com filtros apropriados. Você pode procurar tarefas concluídas ou em execução.

2. Selecione um trabalho na lista tabular de trabalhos.

3. Na parte inferior da página, clique em **detalhes**.

4. Na caixa de diálogo **detalhes** , você pode exibir o status, detalhes e estatísticas de tempo. A ilustração a seguir mostra um exemplo da caixa de diálogo **Detalhes do trabalho de Backup** .
 
    ![Página de detalhes do trabalho](./media/storsimple-ova-manage-jobs/ovajobs2.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Falhas de trabalho quando a máquina virtual está pausada no hipervisor

Quando um trabalho está em andamento na sua matriz Virtual StorSimple e o dispositivo (máquina virtual provisionado no hipervisor) está pausado para mais de 15 minutos, o trabalho falhará. Isso é devido a seu tempo Array Virtual StorSimple sendo fora de sincronia com a hora do Microsoft Azure. Um exemplo de uma falha de trabalho de restauração é mostrado na seguinte captura de tela.

![Restaurar falha de trabalho](./media/storsimple-ova-manage-jobs/restorejobfailure.png)

Essas falhas se aplicarão a trabalhos de backup, restaurar, atualização e failover. Se sua máquina virtual está configurada no Hyper-V, máquina eventualmente sincronizará tempo com sua hipervisor. Depois que isso acontece, você pode reiniciar seu trabalho. 

## <a name="next-steps"></a>Próximas etapas

[Saiba como usar da web local interface de usuário para administrar sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).
