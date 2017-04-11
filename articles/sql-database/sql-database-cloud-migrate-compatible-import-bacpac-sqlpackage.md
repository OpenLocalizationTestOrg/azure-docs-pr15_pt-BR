<properties
   pageTitle="Importar para o banco de dados SQL de um arquivo BACPAC usando SqlPackage"
   description="Microsoft Azure SQL Database, migração de banco de dados, importar banco de dados, importar arquivo BACPAC, sqlpackage"
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

# <a name="import-to-sql-database-from-a-bacpac-file-using-sqlpackage"></a>Importar para o banco de dados SQL de um arquivo BACPAC usando SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portal do Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

Este artigo mostra como importar banco de dados SQL de um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) usando o utilitário de linha de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Este utilitário é fornecido com as versões mais recentes do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [Ferramentas de dados do SQL Server para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou você pode baixar a versão mais recente do [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) diretamente do Centro de download da Microsoft.


> [AZURE.NOTE] As etapas a seguir presumem que você já tiver configurado um servidor de banco de dados SQL, ter as informações de conexão disponível e verificar se o seu banco de dados de origem é compatível.

## <a name="import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage"></a>Importar de um arquivo BACPAC para o banco de dados do SQL Azure usando SqlPackage

Use as seguintes etapas para usar o utilitário de linha de comando [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) para importar um banco de dados do SQL Server compatível (ou banco de dados do SQL Azure) de um arquivo BACPAC.

> [AZURE.NOTE] As etapas a seguir presumem que você já tiver configurado um servidor de banco de dados do Azure SQL e ter as informações de conexão disponível.

1. Abra um prompt de comando e altere um diretório contendo o utilitário de linha de comando sqlpackage.exe - esse utilitário acompanha o Visual Studio e SQL Server.
2. Execute o seguinte comando sqlpackage.exe com os seguintes argumentos para seu ambiente:

    `sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >`

  	| Argumento  | Descrição  |
  	|---|---|
  	| < nome_do_servidor >  | nome do servidor de destino  |
  	| < nome_do_banco_de_dados >  | nome do banco de dados de destino  |
  	| < Nome_de_usuário >  | o nome de usuário no servidor de destino |
  	| < senha >  | a senha do usuário  |
  	| < source_file >  | o nome do arquivo e o local para o arquivo BACPAC está sendo importado  |

    ![Exportar um aplicativo de camada de dados no menu de tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## <a name="next-steps"></a>Próximas etapas

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de banco de dados SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcial ou não suporte funções](sql-database-transact-sql-information.md)
- [Migrar bancos de dados do SQL Server usando o Assistente de migração do SQL Server](http://blogs.msdn.com/b/ssma/)
