<properties 
    pageTitle="Vários fragmentar consultando | Microsoft Azure" 
    description="Execute consultas em fragmentos usando a biblioteca de cliente elástica banco de dados." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/12/2016" 
    ms.author="torsteng"/>

# <a name="multi-shard-querying"></a>Vários fragmentar consultando

## <a name="overview"></a>Visão geral

Com as [Ferramentas de banco de dados elástica](sql-database-elastic-scale-introduction.md), você pode criar soluções de banco de dados sharded. **Vários fragmentar consultando** é usado para tarefas como conjunto de dados/relatórios que exigem a execução de uma consulta que estende entre vários fragmentos. (Compare com [Roteamento dependentes de dados](sql-database-elastic-scale-data-dependent-routing.md), que executa todo o trabalho em um único fragmentar.) 

## <a name="overview"></a>Visão geral

1. Obtenha um [**RangeShardMap**](https://msdn.microsoft.com/library/azure/dn807318.aspx) ou [**ListShardMap**](https://msdn.microsoft.com/library/azure/dn807370.aspx) usando o [**TryGetRangeShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx), o [**TryGetListShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)ou o método [**GetShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx) . Consulte [**Construindo um ShardMapManager**](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) e [**obter um RangeShardMap ou ListShardMap**](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Crie um objeto de **[MultiShardConnection](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)** .
2. Crie um **[MultiShardCommand](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)**. 
3. Defina a **[propriedade CommandText](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)** como um comando T-SQL.
3. Execute o comando chamando o **[método ExecuteReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)**.
4. Exiba os resultados usando a **[classe de MultiShardDataReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)**. 

## <a name="example"></a>Exemplo

O código a seguir ilustra o uso de vários fragmentar consultando usando um determinado **ShardMap** chamado *myShardMap*. 

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
    { 
    using (MultiShardCommand cmd = conn.CreateCommand())
           { 
            cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
            cmd.CommandType = CommandType.Text; 
            cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
            cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

            using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
                { 
                    while (sdr.Read())
                        { 
                            var c1Field = sdr.GetString(0); 
                            var c2Field = sdr.GetFieldValue<int>(1); 
                            var c3Field = sdr.GetFieldValue<Int64>(2);
                        } 
                } 
           } 
    } 

 
Uma diferença chave é a construção de fragmentar várias conexões. Onde **SqlConnection** opera em um único banco de dados, o **MultiShardConnection** leva uma ***coleção de fragmentos*** como sua entrada. Preencha a coleção de fragmentos de um mapa de fragmentar. A consulta é executada, em seguida, na coleção de fragmentos usando semântica **UNION ALL** para montar um único resultado geral. Opcionalmente, o nome da fragmentar onde a linha se origina pode ser adicionado a saída usando a propriedade **ExecutionOptions** no comando. 

Observe a chamada para **myShardMap.GetShards()**. Este método recupera todos os fragmentos de mapa fragmentar e fornece uma maneira fácil de executar uma consulta em todos os bancos de dados relevantes. A coleção de fragmentos de uma consulta de vários fragmentar pode ser refinada posterior executando uma consulta LINQ sobre a coleção retornado da chamada para **myShardMap.GetShards()**. Em combinação com a política de resultados parciais, a capacidade de corrente em vários fragmentar consultando foi projetada para funcionar bem para dezenas até centenas de fragmentos.

Uma limitação com vários fragmentar consultando atualmente é a falta de validação para fragmentos e shardlets que são consultados. Enquanto o roteamento de dependentes de dados verifica que um determinado fragmentar é parte do mapa fragmentar na ocasião da consulta, fragmentar várias consultas não realizam essa verificação. Isso pode levar a consultas de vários fragmentar executando em bancos de dados que foram removidos do mapa fragmentar.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Fragmentar várias consultas e operações de divisão direta

Fragmentar várias consultas não verifica se shardlets no banco de dados consultado está participando de operações de divisão direta contínuas. (Consulte [escala usando a ferramenta de divisão direta elástica banco de dados](sql-database-elastic-scale-overview-split-and-merge.md).) Isso pode levar inconsistências onde linhas de shardlet o mesmo mostram para vários bancos de dados na mesma consulta fragmentar vários. Lembre-se dessas limitações e considere descarga operações de divisão direta contínuas e alterações para o mapa de fragmentar ao executar consultas de vários fragmentar.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## <a name="see-also"></a>Consulte também
**[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient.aspx)** classes e métodos.


Gerencie fragmentos usando a [biblioteca de cliente do banco de dados elástica](sql-database-elastic-database-client-library.md). Inclui um namespace chamado [Microsoft.Azure.SqlDatabase.ElasticScale.Query](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.aspx) que fornece a capacidade de vários fragmentos usando uma única consulta e o resultado da consulta. Ele fornece uma abstração consultar sobre uma coleção de fragmentos. Ele também fornece políticas de execução alternativo, resultados parciais em particular, para lidar com falhas ao consultar sobre fragmentos muitos.  

 