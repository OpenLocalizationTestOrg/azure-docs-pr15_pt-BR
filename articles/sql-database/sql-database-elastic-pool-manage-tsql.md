<properties 
    pageTitle="Criar ou mover um banco de dados do SQL Azure para um pool de Elástico usando T-SQL | Microsoft Azure" 
    description="Use T-SQL para criar um banco de dados do SQL Azure em um pool de Elástico. Ou use T-SQL para mover o banco e sair pools." 
    services="sql-database" 
    documentationCenter="" 
    authors="srinia" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="05/27/2016"
    ms.author="srinia"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-transact-sql"></a>Monitorar e gerenciar um pool de elástica banco de dados com Transact-SQL  

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Use os comandos [Criar banco de dados (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) e [Alterar Database(Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx) para criar e mover bancos de dados dentro e fora Elástico pools. O pool Elástico deve existir antes de poder usar esses comandos. Esses comandos afetam somente bancos de dados. Criação de novos pools e a configuração de propriedades do pool (como eDTUs mínimo e máximo) não podem ser alterados com comandos T-SQL.

## <a name="create-a-new-database-in-an-elastic-pool"></a>Criar um novo banco de dados em um pool de Elástico
Use o comando Criar banco de dados com a opção SERVICE_OBJECTIVE.   

    CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
    -- Create a database named db1 in a pool named S3M100.

Todos os bancos de dados em um pool de Elástico herdam a camada de serviço do pool elástica (Basic, padrão, Premium). 


## <a name="move-a-database-between-elastic-pools"></a>Mover um banco de dados entre pools Elástico
Use o comando ALTER DATABASE com modificar e configurar o serviço\_opção objetiva como ELÁSTICA\_do POOL; Defina o nome para o nome do pool de destino.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
    -- Move the database named db1 to a pool named P1M125  

## <a name="move-a-database-into-an-elastic-pool"></a>Mover um banco de dados em um pool Elástico 
Use o comando ALTER DATABASE com modificar e configurar o serviço\_opção objetiva como ELASTIC_POOL; Defina o nome para o nome do pool de destino.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
    -- Move the database named db1 to a pool named S3100.

## <a name="move-a-database-out-of-an-elastic-pool"></a>Tira um banco de dados um pool Elástico
Use o comando ALTER DATABASE e defina o SERVICE_OBJECTIVE para um dos níveis de desempenho (S0, S1 etc.).

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
    -- Changes the database into a stand-alone database with the service objective S1.

## <a name="list-databases-in-an-elastic-pool"></a>Bancos de dados de lista em um pool de Elástico
Use o [sys.database\_service \_objetivos exibição](https://msdn.microsoft.com/library/mt712619) para listar todos os bancos de dados em um pool de Elástico. Faça logon no mestre de banco de dados para o modo de exibição de consulta.

    SELECT d.name, slo.*  
    FROM sys.databases d 
    JOIN sys.database_service_objectives slo  
    ON d.database_id = slo.database_id
    WHERE elastic_pool_name = 'MyElasticPool'; 

## <a name="get-resource-usage-data-for-a-pool"></a>Obter dados de uso de recursos para um pool

Use o [sys.elastic\_pool \_recurso \_modo de exibição de estatísticas](https://msdn.microsoft.com/library/mt280062.aspx) para examinar as estatísticas de uso do recurso de um pool de elástica em um servidor lógico. Faça logon no mestre de banco de dados para o modo de exibição de consulta.

    SELECT * FROM sys.elastic_pool_resource_stats 
    WHERE elastic_pool_name = 'MyElasticPool'
    ORDER BY end_time DESC;

## <a name="get-resource-usage-for-an-elastic-database"></a>Obter o uso de recursos para um banco de dados Elástico

Use o [sys.dm\_ db\_ recurso\_modo de exibição de estatísticas](https://msdn.microsoft.com/library/dn800981.aspx) ou [sys.resource \_modo de exibição de estatísticas](https://msdn.microsoft.com/library/dn269979.aspx) para examinar as estatísticas de uso do recurso de um banco de dados em um pool de Elástico. Esse processo é semelhante à consultando o uso de recursos para qualquer banco de dados único.

## <a name="next-steps"></a>Próximas etapas

Depois de criar um pool de banco de dados elástica, você pode gerenciar Elástico bancos de dados no pool criando Elástico trabalhos. Trabalhos Elástico facilitam executando scripts T-SQL em relação a qualquer número de bancos de dados no pool. Para obter mais informações, consulte [Visão geral de trabalhos de elástica banco de dados](sql-database-elastic-jobs-overview.md). 

Consulte [escala check-out com o Azure SQL Database](sql-database-elastic-scale-introduction.md): usar ferramentas de banco de dados elástica para fora de escala, mover dados, consultar ou criar transações.
