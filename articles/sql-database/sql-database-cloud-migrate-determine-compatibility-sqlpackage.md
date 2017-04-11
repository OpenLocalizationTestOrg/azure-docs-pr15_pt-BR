<properties
   pageTitle="Determine a compatibilidade de banco de dados SQL usando SqlPackage.exe | Microsoft Azure"
   description="Microsoft Azure SQL Database, migração de banco de dados, a compatibilidade de banco de dados SQL, SqlPackage"
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

# <a name="determine-sql-database-compatibility-using-sqlpackageexe"></a>Determine a compatibilidade de banco de dados SQL usando SqlPackage.exe

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Supervisor de atualização](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

Neste artigo, você aprende a determinar se um banco de dados do SQL Server é compatível para migrar para o banco de dados do SQL usando o utilitário de prompt de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) .

## <a name="using-sqlpackageexe"></a>Usando SqlPackage.exe

1. Abra um prompt de comando e altere um diretório contendo a versão mais recente do sqlpackage.exe. Este utilitário é fornecido com as versões mais recentes do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [Ferramentas de dados do SQL Server para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou você pode baixar a versão mais recente do [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) diretamente do Centro de download da Microsoft.
2. Execute o seguinte comando SqlPackage com os seguintes argumentos para seu ambiente:

    ' sqlpackage.exe /Action:Export /ssn: < nome_do_servidor > /sdn: < nome_do_banco_de_dados > /tf: /p:TableData < target_file > = < schema_name.table_name >>< output_file > 2 > & 1'

  	| Argumento  | Descrição  |
  	|---|---|
  	| < nome_do_servidor >  | nome do servidor de origem  |
  	| < nome_do_banco_de_dados >  | nome do banco de dados de origem  |
  	| < target_file >  | nome do arquivo e local para o arquivo BACPAC  |
  	| < schema_name.table_name >  | as tabelas para as quais dados são saída para o arquivo de destino  |
  	| < output_file >  | o nome do arquivo e o local para o arquivo de saída com erros, se houver  |

    O motivo para o argumento /p:TableName é que só queremos teste de compatibilidade de banco de dados para exportar para V12 de banco de dados do SQL Azure, em vez de exportar os dados de todas as tabelas. Infelizmente, o argumento de exportação para sqlpackage.exe não suporta extraindo zero tabelas. Você precisa especificar pelo menos uma tabela, como uma única tabela pequena. O < output_file > contém o relatório de erros. O "> 2 > & 1" cadeia pipes na saída padrão e o erro padrão resultante da execução de comando para o arquivo de saída especificado.

    ![Exportar um aplicativo de camada de dados no menu de tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Abra o arquivo de saída e examine os erros de compatibilidade, se houver. 

    ![Exportar um aplicativo de camada de dados no menu de tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## <a name="next-steps"></a>Próximas etapas

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
[versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Corrigir problemas de compatibilidade de migração de banco de dados](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Migrar um banco de dados do SQL Server compatível para o banco de dados SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de banco de dados SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcial ou não suporte funções](sql-database-transact-sql-information.md)
- [Migrar bancos de dados do SQL Server usando o Assistente de migração do SQL Server](http://blogs.msdn.com/b/ssma/)
