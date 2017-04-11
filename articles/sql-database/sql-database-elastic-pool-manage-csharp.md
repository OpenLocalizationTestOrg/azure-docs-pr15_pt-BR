<properties
    pageTitle="Monitorar e gerenciar um pool de banco de dados elástica com c# | Microsoft Azure"
    description="Use técnicas de desenvolvimento de banco de dados c# para gerenciar um pool de banco de dados elástica Azure SQL Database."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="10/04/2016"
    ms.author="sstein"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-cx23"></a>Monitorar e gerenciar um pool de banco de dados elástica com C & #x 23; 

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Saiba como gerenciar um [pool de banco de dados elástica](sql-database-elastic-pool.md) usando C & #x 23;. 

>[AZURE.NOTE] Muitos recursos novos do banco de dados SQL são suportados apenas quando você estiver usando o [modelo de implantação do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md), então você sempre deve usar as últimas **biblioteca de gerenciamento de banco de dados do Azure SQL para .NET ([docs](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [Pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. As mais antigos [bibliotecas de baseado no modelo clássico de implantação](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) são suportados para compatibilidade com versões anteriores, portanto, recomendamos que você usa as bibliotecas mais recentes do Gerenciador de recursos com base.

Para concluir as etapas neste artigo, você precisa dos seguintes itens:

- Um pool Elástico (o pool que você deseja gerenciar). Para criar um pool, consulte [criar um pool de banco de dados elástica com c#](sql-database-elastic-pool-create-csharp.md).
- Visual Studio. Para obter uma cópia gratuita do Visual Studio, consulte a página de [Downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .


## <a name="move-a-database-into-an-elastic-pool"></a>Mover um banco de dados em um pool Elástico

Você pode mover um banco de dados autônomo ou reduzir um pool.  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>Bancos de dados de lista em um pool de Elástico

Para recuperar todos os bancos de dados em um pool, chame o método [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) .

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-a-pool"></a>Alterar as configurações de desempenho de um pool

Recupere as propriedades do pool existentes. Modifique os valores e executar o método CreateOrUpdate.

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## <a name="latency-of-elastic-pool-operations"></a>Latência de operações de pool elástica

- Alterar o eDTUs mínimo por banco de dados ou Máx eDTUs por banco de dados geralmente conclui em cinco minutos ou menos.
- Tempo para alterar o tamanho de pool (eDTUs) depende do tamanho combinado de todos os bancos de dados no pool. Média de alterações 90 minutos ou menos por 100 GB. Por exemplo, se o espaço total de todos os bancos de dados no pool é 200 GB, em seguida, a latência esperada para alterar o eDTU pool por pool é 3 horas ou menos.




## <a name="additional-resources"></a>Recursos adicionais

- [Códigos de erro SQL para aplicativos de cliente do banco de dados do SQL: erro de conexão e outros problemas de banco de dados](sql-database-develop-error-messages.md).
- [Banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [APIs de gerenciamento de recursos do Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)
- [Criar um novo pool de banco de dados elástica com c#](sql-database-elastic-pool-create-csharp.md)
- [Quando um pool de banco de dados elástica deve ser usado?](sql-database-elastic-pool-guidance.md)
- Consulte [escala check-out com o Azure SQL Database](sql-database-elastic-scale-introduction.md): usar ferramentas de banco de dados elástica para fora de escala, mover dados, consultar ou criar transações.

