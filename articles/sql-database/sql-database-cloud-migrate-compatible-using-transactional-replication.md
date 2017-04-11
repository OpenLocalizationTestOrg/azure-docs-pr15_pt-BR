<properties
   pageTitle="Migrar para o banco de dados do SQL usando replicação de transação | Microsoft Azure"
   description="Microsoft Azure SQL Database, migração de banco de dados, importar banco de dados, replicação de transação"
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
   ms.date="08/23/2016"
   ms.author="carlrab"/>

# <a name="migrate-sql-server-database-to-azure-sql-database-using-transactional-replication"></a>Migrar o banco de dados do SQL Server para o banco de dados do SQL Azure usando replicação de transação

> [AZURE.SELECTOR]
- [Assistente de migração de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exportar para arquivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importar de arquivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Replicação de transação](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Neste artigo, você aprende a migrar um banco de dados do SQL Server compatível para o banco de dados do SQL Azure com o tempo de inatividade mínimo usando replicação de transação do SQL Server.

## <a name="understanding-the-transactional-replication-architecture"></a>Entender a arquitetura de replicação de transação

Quando você não puder remover o banco de dados do SQL Server de produção enquanto a migração está ocorrendo, você pode usar replicação de transação do SQL Server como sua solução de migração. Para usar essa solução, você configura seu banco de dados do SQL Azure como um assinante instância do SQL Server no local que você deseja migrar. O local distribuidor de replicação de transação sincroniza os dados do banco de dados local a ser sincronizado (o Editor) enquanto novas transações continuam ocorrer. 

Você também pode usar replicação de transação para migrar um subconjunto de seu banco de dados local. A publicação que você replicar o banco de dados do Azure SQL pode ser limitada a um subconjunto das tabelas do banco de dados que está sendo replicado. Para cada tabela sendo replicada, você pode limitar os dados a um subconjunto das linhas e/ou um subconjunto das colunas.

Com replicação de transação, todas as alterações nos seus dados ou o esquema mostram em seu banco de dados do SQL Azure. Depois que a sincronização é concluída e estiver pronto para migrar, altere a cadeia de conexão de seus aplicativos para apontá-las para seu banco de dados do SQL Azure. Depois de replicação de transação esvazia quaisquer alterações em seu banco de dados locais e todos os seus aplicativos aponte para banco de dados do Azure para a esquerda, você pode desinstalar replicação de transação. O banco de dados do SQL Azure agora é o sistema de produção.

 ![Diagrama de SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## <a name="transactional-replication-requirements"></a>Requisitos de replicação transações

Replicação de transação é uma tecnologia interna e integrada com o SQL Server desde o SQL Server 6.5. É uma tecnologia desenvolvida e aprovada que a maioria dos DBAs Saiba com as quais eles têm experiência. Com o [SQL Server 2016](https://www.microsoft.com/en-us/cloud-platform/sql-server), agora é possível configurar seu banco de dados do SQL Azure como um [assinante de replicação de transação](https://msdn.microsoft.com/library/mt589530.aspx) à sua publicação no local. A experiência que você obtenha Configurando-lo do Management Studio é o mesmo como se você configurar um assinante de replicação de transação em um servidor local. Suporte para esse cenário é suportado quando o publisher e o distribuidor estão pelo menos uma das seguintes versões do SQL Server:

 - SQL Server 2016 e acima 
 - SQL Server 2014 SP1 CU3 e acima
 - SQL Server 2014 RTM CU10 e acima
 - SQL Server 2012 SP2 CU8 e acima
 - SQL Server 2012 SP3 e acima


> [AZURE.IMPORTANT] Use a versão mais recente do SQL Server Management Studio para permanecer sincronizados com atualizações do Microsoft Azure e banco de dados SQL. Versões mais antigas do SQL Server Management Studio não consegue configurar banco de dados SQL como um assinante. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="next-steps"></a>Próximas etapas

- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server 2016](https://www.microsoft.com/en-us/cloud-platform/sql-server)

## <a name="additional-resources"></a>Recursos adicionais

- [Replicação de transação](https://msdn.microsoft.com/library/mt589530.aspx)
- [V12 de banco de dados SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcial ou não suporte funções](sql-database-transact-sql-information.md)
- [Migrar bancos de dados do SQL Server usando o Assistente de migração do SQL Server](http://blogs.msdn.com/b/ssma/)
