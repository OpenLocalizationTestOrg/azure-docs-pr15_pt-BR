<properties
   pageTitle="Corrigir problemas de compatibilidade de banco de dados do SQL Server antes de migração para o banco de dados SQL | Microsoft Azure"
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

# <a name="use-sql-azure-migration-wizard-to-fix-sql-server-database-compatibility-issues-before-migration-to-azure-sql-database"></a>Use o Assistente de migração do SQL Azure para problemas de compatibilidade de banco de dados do SQL Server corrigir antes da migração para o banco de dados do Azure SQL

> [AZURE.SELECTOR]
- Usar o [Assistente de migração Azure SQL](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Use [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Use [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

Neste artigo, você aprende a detectar e corrigir problemas de compatibilidade do banco de dados do SQL Server usando o Assistente de migração do SQL Azure antes da migração para Azure SQL Database.

## <a name="using-sql-azure-migration-wizard"></a>Usando o Assistente de migração do SQL Azure

Use a ferramenta de CodePlex do [Assistente de migração do SQL Azure](http://sqlazuremw.codeplex.com/) gerar um script T-SQL de um banco de dados de origem incompatíveis. Esse script, em seguida, é transformado pelo Assistente para torná-lo compatível com o banco de dados do SQL. Depois, você se conectar ao banco de dados do Azure SQL para executar o script. Esta ferramenta também analisa arquivos de rastreamento para determinar problemas de compatibilidade. O script pode ser gerado com esquema somente ou pode incluir dados no formato BCP. Documentação adicional, incluindo orientação passo a passo está disponível no CodePlex no [Assistente de migração do SQL Azure](http://sqlazuremw.codeplex.com/).  

 ![Diagrama de migração de SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  > [AZURE.NOTE] Nem todos os esquema incompatível que são detectados pelo assistente pode ser corrigidos por suas transformações internas. Script incompatível que não pode ser resolvida são relatados como erros, com comentários inseridos no script gerado. Se muitos erros forem detectados, use o Visual Studio ou SQL Server Management Studio para percorrer e corrigir cada erro que não pôde ser corrigido usando o Assistente de migração do SQL Server.

## <a name="next-steps"></a>Próximas etapas

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Migrar um banco de dados do SQL Server compatível para o banco de dados SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de banco de dados SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcial ou não suporte funções](sql-database-transact-sql-information.md)
- [Migrar bancos de dados do SQL Server usando o Assistente de migração do SQL Server](http://blogs.msdn.com/b/ssma/)
