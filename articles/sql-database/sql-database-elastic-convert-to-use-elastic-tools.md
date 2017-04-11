<properties
   pageTitle="Migrar bancos de dados existentes para escala | Microsoft Azure"
   description="Converter bancos de dados sharded para usar ferramentas de banco de dados elástica, criando um fragmentar Gerenciador de mapa"
   services="sql-database"
   documentationCenter=""
   authors="ddove"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="10/24/2016"
   ms.author="ddove"/>

# <a name="migrate-existing-databases-to-scale-out"></a>Migrar bancos de dados existentes para fora de escala

Gerencie facilmente dimensionada-out sharded bancos de dados existentes usando as ferramentas de banco de dados do Azure SQL Database (como a [biblioteca de cliente do banco de dados elástica](sql-database-elastic-database-client-library.md)). Você deve primeiro converter um conjunto de bancos de dados para usar o [Fragmentar mapear manager](sql-database-elastic-scale-shard-map-management.md)existentes. 

## <a name="overview"></a>Visão geral
Para migrar um banco de dados sharded existente: 

1. Prepare o [banco de dados do fragmentar mapa manager](sql-database-elastic-scale-shard-map-management.md).
2. Crie o mapa de fragmentar.
3. Prepare os fragmentos individuais.  
2. Adicione mapeamentos para o mapa de fragmentar.

Essas técnicas podem ser implementadas usando a [biblioteca de cliente do .NET Framework](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)ou os scripts do PowerShell encontrados em [DB do SQL Azure - scripts de ferramentas de banco de dados elástica](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Veja os exemplos usam dos scripts do PowerShell.

Para saber mais sobre o ShardMapManager, consulte [Fragmentar mapear gerenciamento](sql-database-elastic-scale-shard-map-management.md). Para obter uma visão geral das ferramentas de banco de dados elástica, consulte [Visão geral dos recursos de banco de dados elástica](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Preparar o banco de dados do Gerenciador de mapa fragmentar

O Gerenciador de mapa fragmentar é um banco de dados especial que contém os dados para gerenciar bancos de dados em escala-out. Você pode usar um banco de dados existente ou crie um novo banco de dados. Observe que um banco de dados que atua como gerente de mapa fragmentar não deve ser o mesmo banco de dados como uma fragmentar. Observe também que o script do PowerShell não cria o banco de dados para você. 

## <a name="step-1-create-a-shard-map-manager"></a>Etapa 1: criar um fragmentar Gerenciador de mapa

    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>Para recuperar o Gerenciador de mapa fragmentar

Após a criação, você pode recuperar o Gerenciador de mapa fragmentar com esse cmdlet. Essa etapa é necessária sempre que você precisa usar o objeto ShardMapManager.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 

  
## <a name="step-2-create-the-shard-map"></a>Etapa 2: criar o mapa de fragmentar

Você deve selecionar o tipo de mapa de fragmentar para criar. A opção depende da arquitetura de banco de dados: 

1. Único locatário por banco de dados (para termos, consulte o [Glossário](sql-database-elastic-scale-glossary.md).) 
2. Vários locatários por banco de dados (dois tipos):
    3. Mapeamento de lista
    4. Mapeamento de intervalo
 

Para um modelo de único locatário, crie um mapa de fragmentar **mapeamento da lista** . O modelo de locatário do único atribui um banco de dados por locatário. Esse é um modelo eficiente para desenvolvedores SaaS como simplifica o gerenciamento.

![Mapeamento de lista][1]

O modelo de vários locatário atribui vários locatários a um banco de dados único (e você pode distribuir grupos de locatários entre vários bancos de dados). Use esse modelo quando você espera cada locatário para têm necessidades de dados pequeno. Nesse modelo, podemos atribuir um intervalo de locatários a um banco de dados usando o **mapeamento de intervalo**. 
 

![Mapeamento de intervalo][2]

Ou você pode implementar um modelo de banco de dados de vários locatários usando um *mapeamento de lista* para atribuir vários locatários para um único banco de dados. Por exemplo, DB1 é usada para armazenar informações sobre identificação de locatário 1 e 5 e DB2 armazena dados de locatário 10 e locatário 7. 

![Muliple locatários no banco de dados único][3] 

**Com base na sua escolha, escolha uma destas opções:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Opção 1: criar um mapa de fragmentar para um mapeamento de lista
Crie um mapa de fragmentar usando o objeto ShardMapManager. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 
 
 
### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Opção 2: criar um mapa de fragmentar para um mapeamento de intervalo

Observe que para utilizar este padrão de mapeamento, a id de locatário valores precisa ser intervalos contínuos, e ele é aceitável ter lacunas nos intervalos simplesmente ignorando intervalo ao criar os bancos de dados.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-a-single-database"></a>Opção 3: Mapeamentos de lista em um banco de dados
Também configurar esse padrão requer a criação de um mapa de lista conforme mostrado na etapa 2, opção 1.

## <a name="step-3-prepare-individual-shards"></a>Etapa 3: Preparar fragmentos individuais

Adicione cada fragmentar (banco de dados) para o Gerenciador de mapa fragmentar. Prepara os bancos de dados individuais para armazenar informações de mapeamento. Execute este método em cada fragmentar.
     
    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.
 

## <a name="step-4-add-mappings"></a>Etapa 4: Adicionar mapeamentos

A adição de mapeamentos depende do tipo de mapa de fragmentar que você criou. Se você tiver criado um mapa de lista, adicione mapeamentos de lista. Se você criou um mapa de intervalo, você pode adicionar mapeamentos de intervalo.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Opção 1: mapear os dados para um mapeamento de lista

Mapeie os dados adicionando um mapeamento de lista para cada site principal.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Opção 2: mapear os dados para um mapeamento de intervalo

Adicione os mapeamentos de intervalo para todo o locatário id intervalo – associações de banco de dados:

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-a-single-database"></a>Etapa 4 opção 3: mapear os dados para vários locatários em um único banco de dados

Para cada site principal, execute o Add-ListMapping (opção 1, acima). 


## <a name="checking-the-mappings"></a>Os mapeamentos de verificação

Informações sobre os fragmentos existentes e os mapeamentos associados a eles podem ser consultadas usando a seguinte comandos:  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Resumo

Quando tiver concluído a instalação, você pode começar a usar a biblioteca de cliente elástica banco de dados. Você também pode usar [dados dependentes roteamento](sql-database-elastic-scale-data-dependent-routing.md) e [vários fragmentar consulta](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Próximas etapas


Obtenha os scripts do PowerShell do [sripts de ferramentas de banco de dados DB elástica do SQL Azure](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

As ferramentas também são no GitHub: [Azure/elástica-db-tools](https://github.com/Azure/elastic-db-tools).

Use a ferramenta de mesclagem de divisão para mover dados para ou de um modelo de vários locatário a um modelo de locatário único. Consulte a [ferramenta de mesclagem de divisão](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Recursos adicionais

Para obter informações sobre padrões comuns de arquitetura de dados de aplicativos de banco de dados do locatários vários software como serviço (SaaS), consulte [Os padrões de Design para vários locatários SaaS aplicativos com o Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Perguntas e solicitações de recursos

Para perguntas, por favor, comunique-se no [Fórum de banco de dados SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) e para solicitações de recurso, adicione-os para o [Fórum de comentários do banco de dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
 
