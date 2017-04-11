<properties
   pageTitle="Exportar um banco de dados do SQL Server para um arquivo BACPAC usando SqlPackage | Microsoft Azure"
   description="Microsoft Azure SQL Database, migração de banco de dados, exportar o banco de dados, Exportar arquivo BACPAC, sqlpackage"
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

# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sqlpackage"></a>Exportar um banco de dados do SQL Server para um arquivo BACPAC usando SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

Este artigo mostra como exportar um banco de dados do SQL Server para um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) usando o utilitário de linha de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Este utilitário é fornecido com as versões mais recentes do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [Ferramentas de dados do SQL Server para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou você pode baixar a versão mais recente do [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) diretamente do Centro de download da Microsoft.

1. Abra um prompt de comando e altere um diretório contendo o utilitário de linha de comando sqlpackage.exe - esse utilitário acompanha o Visual Studio e SQL Server. Use a pesquisa em seu computador para encontrar o caminho em seu ambiente.
2. Execute o seguinte comando sqlpackage.exe com os seguintes argumentos para seu ambiente:

    ' sqlpackage.exe /Action:Export /ssn: < nome_do_servidor > /sdn: < nome_do_banco_de_dados > /tf: < target_file >

  	| Argumento  | Descrição  |
  	|---|---|
  	| < nome_do_servidor >  | nome do servidor de origem  |
  	| < nome_do_banco_de_dados >  | nome do banco de dados de origem  |
  	| < target_file >  | nome do arquivo e local para o arquivo BACPAC  |

    ![Exportar um aplicativo de camada de dados no menu de tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## <a name="next-steps"></a>Próximas etapas

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Importar um BACPAC banco de dados do SQL Azure usando o SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Importar um BACPAC para SqlPackage de banco de dados do SQL Azure](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Importar um BACPAC para o portal de banco de dados do SQL Azure Azure](sql-database-import.md)
- [Importar um BACPAC PowerShell de banco de dados do SQL Azure](sql-database-import-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de banco de dados SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcial ou não suporte funções](sql-database-transact-sql-information.md)
- [Migrar bancos de dados do SQL Server usando o Assistente de migração do SQL Server](http://blogs.msdn.com/b/ssma/)
