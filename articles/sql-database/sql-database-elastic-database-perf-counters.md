<properties
    pageTitle="Contadores de desempenho do Gerenciador de mapa fragmentar"
    description="ShardMapManager classe e dados dependentes roteamento contadores de desempenho"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="SilviaDoomra"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="SilviaDoomra"/>

# <a name="performance-counters-for-shard-map-manager"></a>Contadores de desempenho do Gerenciador de mapa fragmentar

Você pode capturar o desempenho de um [gerente de mapa fragmentar](sql-database-elastic-scale-shard-map-management.md), especialmente quando usar o [Roteamento dependentes de dados](sql-database-elastic-scale-data-dependent-routing.md). Contadores são criados com métodos da classe Microsoft.Azure.SqlDatabase.ElasticScale.Client.  

Contadores são usados para controlar o desempenho das operações de [Roteamento dependentes de dados](sql-database-elastic-scale-data-dependent-routing.md) . Esses contadores são acessíveis no Monitor de desempenho, na categoria "Elástica banco de dados: fragmentar gerenciamento".

**Para a versão mais recente:** Vá para [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Consulte também [atualizar um aplicativo para usar a biblioteca de cliente mais recente do banco de dados elástica](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Pré-requisitos

* Para criar a categoria desempenho e contadores, o usuário deve ser uma parte do grupo de **administradores** local na máquina hospeda o aplicativo.  

* Para criar uma instância de contador de desempenho e atualizar os contadores, o usuário deve ser um membro do grupo de **administradores** ou **Usuários de Monitor de desempenho** . 

## <a name="create-performance-category-and-counters"></a>Criar categoria de desempenho e contadores 

Para criar os contadores, chame o método CreatePeformanceCategoryAndCounters da [classe ShardMapManagmentFactory](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). Somente um administrador pode executar o método: 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Você também pode usar [Este](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) script do PowerShell para executar o método. O método cria os contadores de desempenho a seguir:  

* **Mapeamentos de cache**: número de mapeamentos de cache para o mapa de fragmentar.
*  **Operações/seg DDR**: taxa dependentes roteamento das operações de dados para o mapa de fragmentar. Esse contador é atualizado quando uma chamada para [OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) resulta em uma conexão com êxito para o fragmentar de destino. 
*  **Mapeamento de ocorrências do cache de pesquisa/s**: taxa de operações de pesquisa de cache bem-sucedida para mapeamentos no mapa fragmentar. 
*  **Mapeamento de erros do cache de pesquisa/s**: taxa de operações de pesquisa com falha de cache para mapeamentos no mapa fragmentar.
*  **Mapeamentos adicionados ou atualizados no cache/seg**: taxa na quais os mapeamentos estão sendo adicionados ou atualizados no cache para o mapa de fragmentar. 
*  **Mapeamentos removidos do cache/s**: taxa em que os mapeamentos estão sendo removidos do cache para o mapa de fragmentar. 

Contadores de desempenho são criados para cada mapa fragmentar em cache por processo.  


## <a name="notes"></a>Anotações
Os seguintes eventos disparam a criação dos contadores de desempenho:  

* Inicialização da [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) o [carregamento adiantado](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), se o ShardMapManager contiver quaisquer mapas fragmentar. Eles incluem o [GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) e os métodos de [TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx) .
* Pesquisa bem-sucedida de um mapa de fragmentar (usando [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) ou [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)). 

* Criação bem-sucedida de mapa de fragmentar usando CreateShardMap().

Os contadores de desempenho serão atualizados por todas as operações de cache executadas no mapa fragmentar e mapeamentos. Remoção bem-sucedida do mapa fragmentar usando DeleteShardMap () reults na exclusão da instância de contadores de desempenho.  

## <a name="best-practices"></a>Práticas recomendadas

* Criação de categoria de desempenho e contadores deve ser executada apenas uma vez antes da criação do objeto ShardMapManager. Cada execução do comando CreatePerformanceCategoryAndCounters() limpa os contadores anterior (perder dados relatados por todas as instâncias) e cria novos.  

* Instâncias de contador de desempenho são criadas por processo. Qualquer falha de aplicativo ou a remoção de um mapa de fragmentar do cache resultará em exclusão das instâncias de contadores de desempenho.  

### <a name="see-also"></a>Consulte também

[Visão geral dos recursos de banco de dados elástica](sql-database-elastic-scale-introduction.md)  

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

