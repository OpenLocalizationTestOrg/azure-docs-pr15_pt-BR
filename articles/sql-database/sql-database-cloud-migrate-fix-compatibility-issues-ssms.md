<properties
   pageTitle="Corrigir problemas de compatibilidade do banco de dados do SQL Server usando o SQL Server Management Studio antes de migração para o banco de dados SQL | Microsoft Azure"
   description="Microsoft Azure SQL Database, migração de banco de dados, compatibilidade, o Assistente de migração do SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="fix-sql-server-database-compatibility-issues-using-sql-server-management-studio-before-migration-to-sql-database"></a>Corrigir problemas de compatibilidade do banco de dados do SQL Server usando o SQL Server Management Studio antes de migração para o banco de dados SQL

> [AZURE.SELECTOR]
- Usar o [Assistente de migração Azure SQL](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Use [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Use [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

Usuários avançados podem corrigir problemas de compatibilidade do banco de dados do SQL Server usando o SQL Server Management Studio antes de migração para o Azure SQL Database.


> [AZURE.IMPORTANT] É recomendável que você sempre usar a versão mais recente do Management Studio para permanecer sincronizados com atualizações do Microsoft Azure e banco de dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="using-sql-server-management-studio"></a>Usando o SQL Server Management Studio

Use o SQL Server Management Studio para corrigir problemas de compatibilidade usando vários comandos de Transact-SQL, como **Alterar o banco de dados**. Este método é principalmente para usuários avançados que estão confortáveis trabalhando Transact-SQL em um banco de dados ao vivo. Caso contrário, é recomendável que você use SSDT. 



## <a name="next-steps"></a>Próximas etapas

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Migrar um banco de dados do SQL Server compatível para o banco de dados SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de banco de dados SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcial ou não suporte funções](sql-database-transact-sql-information.md)
- [Migrar bancos de dados do SQL Server usando o Assistente de migração do SQL Server](http://blogs.msdn.com/b/ssma/)
