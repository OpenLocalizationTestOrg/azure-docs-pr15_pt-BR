<properties
    pageTitle="Restaurar um banco de dados do SQL Azure a partir de um backup automático (portal Azure) | Microsoft Azure"
    description="Restaure um banco de dados do SQL Azure de um backup automático (portal Azure)."
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


# <a name="restore-an-azure-sql-database-from-an-automatic-backup-using-the-azure-portal"></a>Restaurar um banco de dados do SQL Azure a partir de um backup automático no portal Azure


> [AZURE.SELECTOR]
- [Visão geral](sql-database-recovery-using-backups.md#geo-restore)
- [Localização geográfica restauração: PowerShell](sql-database-geo-restore-powershell.md)

Este artigo mostra como restaurar seu banco de dados de um [backup automático](sql-database-automated-backups.md) em um novo servidor com a [Restauração de localização geográfica](sql-database-recovery-using-backups/.md#geo-restore) usando o portal do Azure.

## <a name="select-a-database-to-restore"></a>Selecionar um banco de dados para restaurar

Para restaurar um banco de dados no portal do Azure, siga estas etapas:

1.  Acesse o [portal do Azure](https://portal.azure.com).
2.  No lado esquerdo da tela, selecione **+ novo** > **bancos de dados** > **Banco de dados SQL**:

    ![Restaurar um banco de dados do SQL Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)

3.  Selecione **Backup** como a fonte e, em seguida, selecione o backup que você deseja restaurar. Especifique um nome de banco de dados, um servidor que você deseja restaurar o banco de dados, e clique em **criar**:
  
    ![Restaurar um banco de dados do SQL Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

Monitore o status da operação de restauração clicando no ícone de notificação no canto superior direito da página. 


## <a name="next-steps"></a>Próximas etapas

- Para uma visão de continuidade de negócios e cenários, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md)
- Para saber sobre backups do banco de dados do SQL Azure automatizado, consulte [backups automatizados do banco de dados SQL](sql-database-automated-backups.md)
- Para saber sobre como usar backups automatizados de recuperação, consulte [restaurar um banco de dados os backups iniciado pelo serviço](sql-database-recovery-using-backups.md)
- Para saber sobre as opções de recuperação mais rápidas, consulte [Ativo geográfica-replicação](sql-database-geo-replication-overview.md)  
- Para saber sobre como usar backups automatizados para arquivamento, consulte [cópia do banco de dados](sql-database-copy.md)
