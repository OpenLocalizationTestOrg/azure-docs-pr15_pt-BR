<properties
   pageTitle="Migrar um banco de dados do SQL Server para o banco de dados do Azure SQL | Microsoft Azure"
   description="Microsoft Azure SQL Database, banco de dados implantar, migração de banco de dados, banco de dados de importação, exportação de banco de dados, o Assistente de migração"
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

# <a name="import-from-bacpac-to-sql-database-using-ssms"></a>Importar do BACPAC para o banco de dados do SQL usando o SSMS

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portal do Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

Este artigo mostra como importar de um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) banco de dados SQL usando o Assistente para exportação de aplicativo de camada de dados no SQL Server Management Studio.

> [AZURE.NOTE] As etapas a seguir presumem que você já tiver provisionado sua instância do SQL Azure lógica e ter as informações de conexão disponível.

1. Verifique se você tem a versão mais recente do SQL Server Management Studio. Novas versões do Management Studio são atualizadas mensal permaneçam em sincronia com as atualizações para o portal do Azure.

     > [AZURE.IMPORTANT] É recomendável que você sempre usar a versão mais recente do Management Studio para permanecer sincronizados com atualizações do Microsoft Azure e banco de dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Conectar ao seu servidor de banco de dados do Azure SQL, clique com botão direito na pasta de **bancos de dados** e clique em **Importar aplicativo de camada de dados...**

    ![Item de menu do aplicativo de camada de dados de importação](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

3.  Para criar o banco de dados no banco de dados do SQL Azure, importar um arquivo BACPAC de disco local ou selecione a conta de armazenamento do Azure e o contêiner no qual você carregou o arquivo BACPAC.

    ![Importar configurações](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

     > [AZURE.IMPORTANT] Ao importar uma BACPAC do armazenamento de blob do Microsoft Azure, use o armazenamento padrão. Não há suporte para a importação de um BACPAC do armazenamento de premium.

4.  Forneça o **novo nome de banco de dados** para o banco de dados em DB do SQL Azure, defina a **Edição do Microsoft Azure SQL Database** (nível de serviço), **tamanho máximo do banco de dados**e o **Objetivo de serviço** (nível de desempenho).

    ![Configurações de banco de dados](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

5.  Clique em **Avançar** e clique em **Concluir** para importar o arquivo BACPAC para um novo banco de dados no servidor do Azure SQL Database.

6. Usando o Explorador de objetos, conecte seu banco de dados migrados no seu servidor do Azure SQL Database.

6.  Usando o portal do Azure, exiba seu banco de dados e suas propriedades.

## <a name="next-steps"></a>Próximas etapas

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de banco de dados SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcial ou não suporte funções](sql-database-transact-sql-information.md)
- [Migrar bancos de dados do SQL Server usando o Assistente de migração do SQL Server](http://blogs.msdn.com/b/ssma/)
