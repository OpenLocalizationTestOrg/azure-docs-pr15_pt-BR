
<properties
   pageTitle="Exportar um banco de dados do SQL Server para um arquivo BACPAC usando o SQL Server Management Studio | Microsoft Azure"
   description="Microsoft Azure SQL Database, migração de banco de dados, exportar o banco de dados, Exportar arquivo BACPAC, o Assistente para exportar aplicação de nível de dados"
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
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="carlrab"/>

# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>Exportar um banco de dados do SQL Server para um arquivo BACPAC usando o SQL Server Management Studio

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

 
Este artigo mostra como exportar um banco de dados do SQL Server para um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) usando o Assistente para exportação de aplicativo de camada de dados no SQL Server Management Studio. 

1. Verifique se você tem a versão mais recente do SQL Server Management Studio. Novas versões do Management Studio são atualizadas mensal permaneçam em sincronia com as atualizações para o portal do Azure.

     > [AZURE.IMPORTANT] É recomendável que você sempre usar a versão mais recente do Management Studio para permanecer sincronizados com atualizações do Microsoft Azure e banco de dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Abra o Management Studio e conecte ao seu banco de dados de origem no Pesquisador de objetos.

    ![Exportar um aplicativo de camada de dados no menu de tarefas](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. O banco de dados de origem no Pesquisador de objetos de atalho, aponte para **tarefas**e clique em **Exportar aplicativo de camada de dados...**

    ![Exportar um aplicativo de camada de dados no menu de tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. No Assistente de exportação, configure a exportação para salvar o arquivo BACPAC como um local do disco local ou para um Azure blob. O BACPAC exportado sempre inclui o esquema de banco de dados completo e, por padrão, os dados de todas as tabelas. Se você quiser excluir dados das algumas ou todas as tabelas, use a guia Avançado. Por exemplo, você pode optar por exportar somente os dados de tabelas de referência, em vez de todas as tabelas.

***Importante*** Ao exportar um BACPAC para armazenamento de blob do Microsoft Azure, use o armazenamento padrão. Não há suporte para a importação de um BACPAC do armazenamento de premium.

    ![Export settings](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)


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
