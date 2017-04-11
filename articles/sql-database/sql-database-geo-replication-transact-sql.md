<properties
    pageTitle="Configurar replicação geográfica para o banco de dados do SQL Azure com Transact-SQL | Microsoft Azure"
    description="Configurar a localização geográfica-replicação de banco de dados do SQL Azure com Transact-SQL"
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
    ms.workload="NA"
    ms.date="10/13/2016"
    ms.author="carlrab"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-transact-sql"></a>Configurar replicação geográfica para o banco de dados do SQL Azure com Transact-SQL

> [AZURE.SELECTOR]
- [Visão geral](sql-database-geo-replication-overview.md)
- [Portal do Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

Este artigo mostra como configurar replicação geográfica ativo para um banco de dados do SQL Azure com Transact-SQL.

Para iniciar o failover usando Transact-SQL, consulte [Iniciar um failover planejado ou para o banco de dados do SQL Azure com Transact-SQL](sql-database-geo-replication-failover-transact-sql.md).

>[AZURE.NOTE] Localização geográfica-replicação ativa (legíveis secundários) agora está disponível para todos os bancos de dados em todos os níveis de serviço. Em abril de 2017 o tipo de secundário não legível será desativado e bancos de dados não legível existentes serão automaticamente atualizados para secundários legíveis.

Para configurar a replicação de localização geográfica ativa usando Transact-SQL, você precisa fazer o seguinte:

- Uma assinatura do Azure.
- Um servidor de banco de dados do Azure SQL lógico <MyLocalServer> e um banco de dados do SQL <MyDB> -o banco de dados principal que você deseja duplicar.
- Um ou mais Azure SQL Database servidores lógicos < MySecondaryServer(n) > - os servidores lógicos que serão os servidores de parceiro em que você irá criar bancos de dados secundários.
- Um logon que é DBManager no sistema principal, tiver db_ownership de banco de dados local que você irá geográfica replicar, e ser DBManager nos servidores parceiro para o qual você configurará replicação geográfica.
- SQL Server Management Studio (SSMS)

> [AZURE.IMPORTANT] É recomendável que você sempre usar a versão mais recente do Management Studio para permanecer sincronizados com atualizações do Microsoft Azure e banco de dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="add-secondary-database"></a>Adicionar banco de dados secundário

Você pode usar a instrução **ALTER DATABASE** para criar um banco de dados de secundário replicado geográfica em um servidor de parceiro. Você executa esta instrução no banco de dados mestre do servidor que contém o banco de dados a serem replicados. O banco de dados replicado geográfica ("principal banco de dados") terá o mesmo nome do banco de dados que está sendo replicado e, por padrão, terá o mesmo nível de serviço do banco de dados principal. O banco de dados secundário pode ser lido ou não legível e pode ser um único banco de dados ou um databbase elástica. Para obter mais informações, consulte [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Níveis de serviço](sql-database-service-tiers.md).
Banco de dados secundário é criado e propagado, dados começará replicação assíncrona do banco de dados principal. As etapas a seguir descrevem como configurar replicação geográfica usando o Management Studio. Etapas para criar secundários não legível e legíveis, com um único banco de dados ou um banco de dados Elástico são fornecidas.

> [AZURE.NOTE] Se houver um banco de dados no servidor especificado parceiro com o mesmo nome do banco de dados principal, que o comando falhará.


### <a name="add-non-readable-secondary-single-database"></a>Adicionar secundário não legível (banco de dados único)

Use as seguintes etapas para criar um secundário não legível como um único banco de dados.

1. Usando a versão 13.0.600.65 ou posterior do SQL Server Management Studio.

     > [AZURE.IMPORTANT] Baixe a versão [mais recente](https://msdn.microsoft.com/library/mt238290.aspx) do SQL Server Management Studio. É recomendável que você sempre usar a versão mais recente do Management Studio para permanecer em sincronia com as atualizações para o portal do Azure.


2. Abra a pasta de bancos de dados, expanda a pasta de **Bancos de dados do sistema** , clique com botão direito no **mestre**e, em seguida, clique em **Nova consulta**.

3. Use a seguinte instrução **ALTER DATABASE** para transformar um banco de dados local em uma replicação de localização geográfica principal com um banco de dados secundário não legível em MySecondaryServer1 onde MySecondaryServer1 é o nome do servidor amigável.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);

4. Clique em **Executar** para executar a consulta.


### <a name="add-readable-secondary-single-database"></a>Adicionar legível secundário (banco de dados único)
Use as seguintes etapas para criar um secundário legível como um único banco de dados.

1. No Management Studio, conectar-se ao servidor lógico Azure SQL Database.

2. Abra a pasta de bancos de dados, expanda a pasta de **Bancos de dados do sistema** , clique com botão direito no **mestre**e, em seguida, clique em **Nova consulta**.

3. Use a seguinte instrução **ALTER DATABASE** para transformar um banco de dados local em uma replicação de localização geográfica principal com um banco de dados secundário legível em um servidor secundário.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);

4. Clique em **Executar** para executar a consulta.



### <a name="add-non-readable-secondary-elastic-database"></a>Adicionar secundário não legível (Elástico banco de dados)

Use as seguintes etapas para criar um secundário não legível como Elástico banco de dados.

1. No Management Studio, conectar-se ao servidor lógico Azure SQL Database.

2. Abra a pasta de bancos de dados, expanda a pasta de **Bancos de dados do sistema** , clique com botão direito no **mestre**e, em seguida, clique em **Nova consulta**.

3. Use a seguinte instrução **ALTER DATABASE** para transformar um banco de dados local em uma replicação de localização geográfica principal com um banco de dados secundário não legível em um servidor secundário em um pool de Elástico.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));

4. Clique em **Executar** para executar a consulta.



### <a name="add-readable-secondary-elastic-database"></a>Adicionar legível secundário (Elástico banco de dados)
Use as seguintes etapas para criar um secundário legível como Elástico banco de dados.

1. No Management Studio, conectar-se ao servidor lógico Azure SQL Database.

2. Abra a pasta de bancos de dados, expanda a pasta de **Bancos de dados do sistema** , clique com botão direito no **mestre**e, em seguida, clique em **Nova consulta**.

3. Use a seguinte instrução **ALTER DATABASE** para transformar um banco de dados local em uma replicação de localização geográfica principal com um banco de dados secundário legível em um servidor secundário em um pool de Elástico.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));

4. Clique em **Executar** para executar a consulta.



## <a name="remove-secondary-database"></a>Remover o banco de dados secundário

Você pode usar a instrução **ALTER DATABASE** para encerrar permanentemente a parceria de replicação entre um banco de dados secundário e seu principal. Esta política é executada no banco de dados mestre no qual o banco de dados principal reside. Após o encerramento de relação, o banco de dados secundário se torna um banco de dados de leitura e gravação regular. Se a conectividade com o banco de dados secundário foi desfeita o comando é bem sucedida, mas o secundário se tornarão leitura depois conectividade for restaurada. Para obter mais informações, consulte [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Níveis de serviço](sql-database-service-tiers.md).

Use as etapas a seguir para remover replicado geográfica secundária de um parceiro de replicação de localização geográfica.

1. No Management Studio, conectar-se ao servidor lógico Azure SQL Database.

2. Abra a pasta de bancos de dados, expanda a pasta de **Bancos de dados do sistema** , clique com botão direito no **mestre**e, em seguida, clique em **Nova consulta**.

3. Use a seguinte instrução **ALTER DATABASE** para remover um secundário replicado localização geográfica.

        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;

4. Clique em **Executar** para executar a consulta.

## <a name="monitor-geo-replication-configuration-and-health"></a>Integridade e a configuração de replicação geográfica do monitor

Tarefas de monitoramento incluem monitoramento da configuração de replicação geográfica e integridade de replicação de dados.  Você pode usar o modo de exibição de gerenciamento dinâmico **sys.dm_geo_replication_links** no banco de dados mestre para retornar informações sobre todos os links de replicação existente para cada banco de dados no servidor lógico Azure SQL Database. Este modo de exibição contém uma linha para cada um do link de replicação entre os bancos de dados principais e secundários. Você pode usar o modo de exibição de gerenciamento dinâmico **sys.dm_replication_link_status** para retornar uma linha para cada banco de dados de SQL Azure que no momento está participando de um link de replicação de replicação. Isso inclui bancos de dados principais e secundários. Se houver mais de um link de replicação contínua para um determinado banco de dados principal, esta tabela contém uma linha para cada uma das relações. O modo de exibição é criado em todos os bancos de dados, incluindo o master lógico. Entretanto, consultar este modo de exibição no mestre lógica retorna um conjunto vazio. Você pode usar o modo de exibição de gerenciamento dinâmico **sys.dm_operation_status** para mostrar o status de todas as operações de banco de dados, incluindo o status dos links de replicação. Para obter mais informações, consulte [sys.geo_replication_links (Azure SQL Database)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm_geo_replication_link_status (Azure SQL Database)](https://msdn.microsoft.com/library/mt575504.aspx)e [sys.dm_operation_status (Azure SQL Database)](https://msdn.microsoft.com/library/dn270022.aspx).

Use as etapas a seguir para monitorar uma parceria de replicação geográfica.

1. No Management Studio, conectar-se ao servidor lógico Azure SQL Database.

2. Abra a pasta de bancos de dados, expanda a pasta de **Bancos de dados do sistema** , clique com botão direito no **mestre**e, em seguida, clique em **Nova consulta**.

3. Use a instrução a seguir para mostrar todos os bancos de dados com links de replicação geográfica.

        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;

4. Clique em **Executar** para executar a consulta.
5. Abra a pasta de bancos de dados, expanda a pasta de **Bancos de dados do sistema** , clique com botão direito em **MyDB**e, em seguida, clique em **Nova consulta**.
6. Usar a seguinte instrução para mostrar a replicação lento e replicação de última hora do meu bancos de dados secundários de MyDB.

        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status

7. Clique em **Executar** para executar a consulta.
8. Use a instrução a seguir para mostrar as operações de replicação geográfica mais recentes associadas com o banco de dados MyDB.

        SELECT * FROM sys.dm_operation_status where major_resource_id = 'MyDB'
        ORDER BY start_time DESC

9. Clique em **Executar** para executar a consulta.

## <a name="upgrade-a-non-readable-secondary-to-readable"></a>Atualizar um secundário não legível para legível

Em abril de 2017 o tipo de secundário não legível será desativado e bancos de dados não legível existentes serão automaticamente atualizados para secundários legíveis. Se você está usando não legível secundários hoje e deseja atualizá-los para ser lido, você pode usar as seguintes etapas simples para cada secundário.

> [AZURE.IMPORTANT] Não há nenhum método de autoatendimento de atualização in-loco de um secundário não legível legível. Se você soltar sua somente secundário, então o banco de dados primário permanecerá desprotegido até que o novo secundário esteja totalmente sincronizado. Se SLA seu aplicativo exigir que o principal é sempre protegido, você deve considerar criando um secundário paralelo em um servidor diferente antes de aplicar as etapas de atualização acima. Observe que cada principal pode ter até 4 bancos de dados secundários.


1. Primeiro, se conectar ao servidor *secundário* e descartar o banco de dados secundário não legível:  
        
        DROP DATABASE <MyNonReadableSecondaryDB>;

2. Agora, conectar-se ao servidor *primário* e adicione um novo secundário legível

        ALTER DATABASE <MyDB>
            ADD SECONDARY ON SERVER <MySecondaryServer> WITH (ALLOW_CONNECTIONS = ALL);




## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre Active geográfica replicação, consulte - [Replicação de localização geográfica ativa](sql-database-geo-replication-overview.md)
- Para uma visão de continuidade de negócios e cenários, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md)
