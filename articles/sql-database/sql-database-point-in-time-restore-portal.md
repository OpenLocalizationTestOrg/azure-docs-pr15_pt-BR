<properties
    pageTitle="Restaurar um banco de dados do SQL Azure para um ponto anterior no tempo (portal Azure) | Microsoft Azure"
    description="Restaure um banco de dados do SQL Azure para um ponto anterior no tempo."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/18/2016"
    ms.author="sstein"
    ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Restaurar um banco de dados do SQL Azure para um ponto anterior no tempo com o portal do Azure


> [AZURE.SELECTOR]
- [Visão geral](sql-database-recovery-using-backups.md)
- [Restaurar no momento: PowerShell](sql-database-point-in-time-restore-powershell.md)

Este artigo mostra como restaurar o banco de dados para um ponto anterior no tempo de [que backups automatizados do banco de dados SQL](sql-database-automated-backups.md) usando o portal do Azure.

## <a name="restore-a-sql-database-to-a-previous-point-in-time"></a>Restaurar um banco de dados SQL para um ponto anterior no tempo

Selecione um banco de dados para restaurar no portal do Azure:

1.  Abra o [portal do Azure](https://portal.azure.com).
2.  No lado esquerdo da tela, selecione **mais serviços** > **bancos de dados SQL**.
3.  Clique em do banco de dados que você deseja restaurar.
4.  Na parte superior da página do seu banco de dados, selecione **Restaurar**:

    ![Restaurar um banco de dados do SQL Azure](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  Na página **Restaurar** , selecione a data e hora (em vez de UTC) para restaurar o banco de dados e clique em **Okey**:

    ![Restaurar um banco de dados do SQL Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## <a name="monitor-the-restore-operation"></a>Monitorar a operação de restauração

1. Após clicar em **Okey** na etapa anterior, clique no ícone de notificação no canto superior direito da página e clique na notificação de **banco de dados SQL restaurando** para obter detalhes.

    ![Restaurar um banco de dados do SQL Azure](./media/sql-database-point-in-time-restore-portal/notification-icon.png)

2. Página de banco de dados do SQL restaurando abre com informações sobre o status da restauração. Você pode clicar no item de linha para obter mais detalhes:

    ![Restaurar um banco de dados do SQL Azure](./media/sql-database-point-in-time-restore-portal/inprogress.png)

 

## <a name="next-steps"></a>Próximas etapas

- Para uma visão de continuidade de negócios e cenários, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md)
- Para saber sobre backups do banco de dados do SQL Azure automatizado, consulte [backups automatizados do banco de dados SQL](sql-database-automated-backups.md)
- Para saber sobre como usar backups automatizados de recuperação, consulte [restaurar um banco de dados os backups iniciado pelo serviço](sql-database-recovery-using-backups.md)
- Para saber sobre as opções de recuperação mais rápidas, consulte [Ativo geográfica-replicação](sql-database-geo-replication-overview.md)  
- Para saber sobre como usar backups automatizados para arquivamento, consulte [cópia do banco de dados](sql-database-copy.md)
