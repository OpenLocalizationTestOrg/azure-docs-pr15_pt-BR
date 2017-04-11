<properties
   pageTitle="Use o SQL Server Management Studio para compatibilidade de determinar banco de dados SQL antes da migração para o banco de dados do Azure SQL | Microsoft Azure"
   description="Microsoft Azure SQL Database, migração de banco de dados, a compatibilidade de banco de dados SQL, exportar assistente de aplicativo de camada de dados"
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
   ms.date="08/29/2016"
   ms.author="carlrab"/>

# <a name="use-sql-server-management-studio-to-determine-sql-database-compatibility-before-migration-to-azure-sql-database"></a>Use o SQL Server Management Studio para compatibilidade de determinar banco de dados SQL antes da migração para o banco de dados do Azure SQL

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Supervisor de atualização](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
 
Neste artigo que você aprenderá para determinar se um banco de dados do SQL Server é compatível para migrar para o banco de dados do SQL usando o Assistente para exportação de aplicativo de camada de dados no SQL Server Management Studio.

## <a name="using-sql-server-management-studio"></a>Usando o SQL Server Management Studio

1. Verifique se você tem a versão mais recente do SQL Server Management Studio. Novas versões do Management Studio são atualizadas mensal permaneçam em sincronia com as atualizações para o portal do Azure.

     > [AZURE.IMPORTANT] É recomendável que você sempre usar a versão mais recente do Management Studio para permanecer sincronizados com atualizações do Microsoft Azure e banco de dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Abra o Management Studio e conecte ao seu banco de dados de origem no Pesquisador de objetos.
3. O banco de dados de origem no Pesquisador de objetos de atalho, aponte para **tarefas**e clique em **Exportar aplicativo de camada de dados...**

    ![Exportar um aplicativo de camada de dados no menu de tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. No Assistente de exportação, clique em **Avançar**e, em seguida, na guia **configurações** , configurar a exportação para salvar o arquivo BACPAC para qualquer um local do disco local ou em um blob do Microsoft Azure. Um arquivo BACPAC é salvo se você não tiver nenhum problemas de compatibilidade do banco de dados. Se houver problemas de compatibilidade, eles serão exibidos no console.

    ![Exportar configurações](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. Para ignorar a exportação de dados, clique na **guia Avançado** e desmarque a caixa de seleção **Selecionar tudo** . Neste ponto, nosso objetivo é somente para testar a compatibilidade.

    ![Exportar configurações](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. Clique em **Avançar** e, em seguida, clique em **Concluir**. Problemas de compatibilidade do banco de dados, se houver, aparecem quando o assistente valida o esquema.

    ![Exportar configurações](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. Se nenhum erro aparecer, seu banco de dados é compatível e você está pronto para migrar. Se você tiver erros, é necessário corrigi-los. Para ver os erros, clique em **erro** **Validando**esquema. 
    ![Exportar configurações](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8.  Se o *. Arquivo BACPAC é gerado com êxito, e em seguida, seu banco de dados é compatível com o banco de dados SQL, e estiver pronto para migrar.

## <a name="next-steps"></a>Próximas etapas

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Corrigir problemas de compatibilidade de migração de banco de dados](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Migrar um banco de dados do SQL Server compatível para o banco de dados SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de banco de dados SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcial ou não suporte funções](sql-database-transact-sql-information.md)
- [Migrar bancos de dados do SQL Server usando o Assistente de migração do SQL Server](http://blogs.msdn.com/b/ssma/)
