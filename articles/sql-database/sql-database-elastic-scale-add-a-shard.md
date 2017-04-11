<properties 
    pageTitle="Adicionando um fragmentar usando ferramentas de banco de dados elástica | Microsoft Azure" 
    description="Como usar elástica APIs de escala para adicionar novos fragmentos a um fragmentar conjunto." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="adding-a-shard-using-elastic-database-tools"></a>Adicionando um fragmentar usando ferramentas de banco de dados elástica

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Para adicionar um fragmentar para um novo intervalo ou chave  

Aplicativos geralmente precisam simplesmente adicionar novos fragmentos para lidar com dados esperado de novas chaves ou intervalos de chave, para um mapa de fragmentar que já existe. Por exemplo, um aplicativo sharded pelo ID de locatário, talvez seja necessário provisionar um novo fragmentar para um novo locatário ou mensal sharded dados talvez seja necessário um novo fragmentar provisionado antes do início de cada novo mês. 

Se o novo intervalo de valores-chave já não faz parte de um mapeamento existente, é muito simple de adicionar a nova fragmentar e associar a nova chave ou o intervalo para esse fragmentar. 

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Exemplo: adicionar uma fragmentar e seu intervalo para um mapa de fragmentar existente
Este exemplo usa a [TryGetShard](https://msdn.microsoft.com/library/azure/dn823929.aspx) a [CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx), métodos de [CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn807221.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeShardMap`1.CreateRangeMapping(Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeMappingCreationInfo{`0})) e cria uma instância da classe [ShardLocation](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation.shardlocation.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardLocation.) . No exemplo abaixo, um banco de dados denominada **sample_shard_2** e todos os objetos de esquema necessárias dentro-foram criados para manter o intervalo [300, 400).  

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range being added. 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Create the mapping and associate it with the new shard 
    sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 


Como alternativa, você pode usar o Powershell para criar um novo Gerenciador de mapa fragmentar. Um exemplo está disponível [aqui](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).
## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Para adicionar um fragmentar para uma parte vazia de um intervalo existente  

Em algumas circunstâncias, você pode ter já mapeado um intervalo para um fragmentar e parcialmente preenchido com dados, mas agora deseja futuros dados será direcionado para uma fragmentar diferentes. Por exemplo, você fragmentar pelo intervalo de dia e ter já alocados 50 dias para um fragmentar, mas no dia 24, você deseja futuros dados inseridos em um fragmentar diferentes. O de banco de dados elástica [ferramenta de mesclagem de divisão](sql-database-elastic-scale-overview-split-and-merge.md) podem executar essa operação, mas se a movimentação de dados não é necessária (por exemplo, dados para o intervalo de dias [25, 50), ou seja, dia 25 inclusivo para 50 exclusivo, não existir ainda) é possível executar este inteiramente usando as APIs de gerenciamento de mapa fragmentar diretamente.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Exemplo: a divisão de um intervalo e atribuindo a parte vazia a um fragmentar recém-adicionado

Um banco de dados denominada "sample_shard_2" e todos os objetos de esquema necessárias dentro de ele ter sido criado.  

 
    // sm is a RangeShardMap object.
    // Add a new shard to hold the range we will move 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
    
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Split the Range holding Key 25 

    sm.SplitMapping(sm.GetMappingForKey(25), 25); 

    // Map new range holding [25-50) to different shard: 
    // first take existing mapping offline 
    sm.MarkMappingOffline(sm.GetMappingForKey(25)); 
    // now map while offline to a different shard and take online 
    RangeMappingUpdate upd = new RangeMappingUpdate(); 
    upd.Shard = shard2; 
    sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 

**Importante**: usar essa técnica somente se você tiver certeza de que o intervalo para o mapeamento atualizado está vazio.  Os métodos acima não marque dados para o intervalo sendo movido, portanto, é melhor incluir verificações no seu código.  Se existirem linhas no intervalo sendo movido, a distribuição de dados reais não coincidirão mapa fragmentar atualizado. Use a [ferramenta de mesclagem de divisão](sql-database-elastic-scale-overview-split-and-merge.md) para executar a operação em vez nesses casos.  


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 
