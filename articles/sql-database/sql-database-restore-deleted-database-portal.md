<properties
    pageTitle="Restaurar um banco de dados do SQL Azure (portal Azure) excluído | Microsoft Azure"
    description="Restaure um banco de dados do SQL Azure (portal Azure) excluído."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="restore-a-deleted-azure-sql-database-using-the-azure-portal"></a>Restaurar um banco de dados excluído do SQL Azure usando o Portal do Azure

> [AZURE.SELECTOR]
- [Visão geral](sql-database-recovery-using-backups.md)
- [**Restaurar excluídos DB: Portal**](sql-database-restore-deleted-database-portal.md)
- [Restaurar excluídos DB: PowerShell](sql-database-restore-deleted-database-powershell.md)

## <a name="select-the-database-to-restore"></a>Selecione o banco de dados para restaurar 

Para restaurar um banco de dados excluído no portal do Azure:

1.  No [portal do Azure](https://portal.azure.com), clique em **mais serviços** > **servidores SQL**.
3.  Selecione o servidor que continha o banco de dados que você deseja restaurar.
4.  Role para baixo até a seção de **operações** de sua lâmina de servidor e selecione **bancos de dados excluídos**: ![restaurar um banco de dados do SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  Selecione o banco de dados que você deseja restaurar.
6.  Especifique um nome de banco de dados e clique em **Okey**:

    ![Restaurar um banco de dados do SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)


## <a name="next-steps"></a>Próximas etapas

- Para uma visão de continuidade de negócios e cenários, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md)
- Para saber sobre backups do banco de dados do SQL Azure automatizado, consulte [backups automatizados do banco de dados SQL](sql-database-automated-backups.md)
- Para saber sobre como usar backups automatizados de recuperação, consulte [restaurar um banco de dados os backups iniciado pelo serviço](sql-database-recovery-using-backups.md)
- Para saber sobre as opções de recuperação mais rápidas, consulte [Ativo geográfica-replicação](sql-database-geo-replication-overview.md)  
- Para saber sobre como usar backups automatizados para arquivamento, consulte [cópia do banco de dados](sql-database-copy.md)
