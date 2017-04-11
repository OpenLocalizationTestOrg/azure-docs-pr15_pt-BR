<properties
   pageTitle="Gerenciar power de computação no depósito de dados do SQL Azure (portal Azure) | Microsoft Azure"
   description="Poder de computação do Azure tarefas portal gerenciar. Recursos de computação de escala ajustando DWUs. Ou, pausar e continuar recursos de computação para economizar custos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/22/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>Gerenciar power de computação no depósito de dados do SQL Azure (portal Azure)

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTANTE](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Dimensionar o desempenho dimensionando calcular memória para atender a mudança de sua carga de trabalho e recursos. Economizar custos por recursos back dimensionamento durante horários de pico ou computação pausa completamente. 

Este conjunto de tarefas usa o portal do Azure para:

- Computação de escala
- Computação de pausa
- Computação de currículo

Para obter mais informações, consulte [Visão geral de computação de gerenciar][].

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Alimentação de computação de escala

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para alterar os recursos de computação:

1. Abra o [portal do Azure][], abra o banco de dados e clique em **escala**.

    ![Clique em escala][1]

1. Na lâmina escala, mova o controle deslizante à esquerda ou direita para alterar a configuração de DWU.

    ![Mover o controle deslizante][2]

1. Clique em **Salvar**. Uma mensagem de confirmação será exibida. Clique em **Sim** para confirmar ou **não** para cancelar.

    ![Clique em Salvar][3]

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Computação de pausa

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar um banco de dados:

1. Abra o [portal do Azure][] e abra o banco de dados. Observe que o Status **Online**. 

    ![Status online][6]

1. Para suspender recursos de computação e memória, clique em **Pausar**e, em seguida, uma mensagem de confirmação será exibida. Clique em **Sim** para confirmar ou **não** para cancelar.

    ![Confirmar pausar][7]

1. Enquanto o SQL Data Warehouse estiver iniciando o banco de dados, o status é **Pausando**.
2. Quando o status for **pausado**, a operação pause é feita e você já não está sendo cobradas DWUs.

    ![Status de pausa][4]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Computação de currículo

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]Para retomar a um banco de dados:

1. Abra o [portal do Azure][] e abra o banco de dados. Observe que o Status é **pausado**. 

    ![Banco de dados de pausa][4]

1. Para retomar o banco de dados clique em **Iniciar**e, em seguida, aparecerá uma mensagem de confirmação. Clique em **Sim** para confirmar ou **não** para cancelar.

    ![Confirmar currículo][5]

1. Enquanto o SQL Data Warehouse estiver iniciando o banco de dados, o status é "Resuming".
2. Quando o status for **online**, o banco de dados está pronto.

    ![Status online][6]

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, consulte [Visão geral do gerenciamento][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Visão geral do gerenciamento]: ./sql-data-warehouse-overview-manage.md
[Gerenciar a visão geral de computação]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[Portal do Azure]: http://portal.azure.com/
