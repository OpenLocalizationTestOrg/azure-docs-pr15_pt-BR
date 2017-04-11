<properties
   pageTitle="Migrar o banco de dados do SQL Server para o banco de dados do SQL usando implantar o banco de dados para o Assistente de banco de dados do Microsoft Azure | Microsoft Azure"
   description="Microsoft Azure SQL Database, migração de banco de dados, o Assistente de banco de dados do Microsoft Azure"
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

# <a name="migrate-sql-server-database-to-sql-database-using-deploy-database-to-microsoft-azure-database-wizard"></a>Migrar o banco de dados do SQL Server para o banco de dados do SQL usando implantar o banco de dados para o Assistente de banco de dados do Microsoft Azure


> [AZURE.SELECTOR]
- [Assistente de migração de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exportar para arquivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importar de arquivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Replicação de transação](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Implantar o banco de dados Assistente de banco de dados do Microsoft Azure no SQL Server Management Studio migra um [banco de dados do SQL Server compatível](sql-database-cloud-migrate.md) diretamente em seu servidor do Azure SQL Database.

## <a name="use-the-deploy-database-to-microsoft-azure-database-wizard"></a>Usar o banco de dados implantar Assistente de banco de dados do Microsoft Azure

> [AZURE.NOTE] Estas etapas pressupõem que você tenha um [provisionado banco de dados do SQL server](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/).

1. Verifique se você tem a versão mais recente do SQL Server Management Studio. Novas versões do Management Studio são atualizadas mensal permaneçam em sincronia com as atualizações para o portal do Azure.

    > [AZURE.IMPORTANT] É recomendável que você sempre usar a versão mais recente do Management Studio para permanecer sincronizados com atualizações do Microsoft Azure e banco de dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Abra o Management Studio e conecte ao seu banco de dados do SQL Server sejam migrados no Pesquisador de objetos.
3. O banco de dados no Pesquisador de objetos de atalho, aponte para **tarefas**e clique em **Implantar o banco de dados Microsoft Azure SQL Database...**

    ![Implantar para Azure no menu tarefas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.  No Assistente de implantação, clique em **Avançar**e, em seguida, clique em **Conectar** para configurar a conexão ao seu servidor de banco de dados SQL.

    ![Implantar para Azure no menu tarefas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)

5. Em conectar à caixa de diálogo do servidor, insira suas informações de conexão para se conectar ao seu servidor de banco de dados SQL.

    ![Implantar para Azure no menu tarefas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)

5.  Forneça as seguintes informações para o arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) que esse assistente cria durante o processo de migração:

 - O **novo nome de banco de dados** 
 - A **Edição do Microsoft Azure SQL Database** ([nível de serviço](sql-database-service-tiers.md))
 - O **tamanho máximo do banco de dados**
 - O **Objetivo de serviço** (nível de desempenho)
 - O **nome de arquivo temporário**  

    ![Exportar configurações](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.  Conclua o assistente. Dependendo do tamanho e complexidade do banco de dados, implantação pode levar alguns minutos a algumas horas. Se este assistente detecta problemas de compatibilidade, erros são exibidos na tela e a migração não continuar. Para obter orientações sobre como corrigir problemas de compatibilidade do banco de dados, vá para [corrigir problemas de compatibilidade do banco de dados](sql-database-cloud-migrate-fix-compatibility-issues.md).

7.  Usando o Explorador de objetos, conecte seu banco de dados migrados no seu servidor do Azure SQL Database.
8.  Usando o portal do Azure, exiba seu banco de dados e suas propriedades.

## <a name="next-steps"></a>Próximas etapas

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de banco de dados SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcial ou não suporte funções](sql-database-transact-sql-information.md)
- [Migrar bancos de dados do SQL Server usando o Assistente de migração do SQL Server](http://blogs.msdn.com/b/ssma/)
