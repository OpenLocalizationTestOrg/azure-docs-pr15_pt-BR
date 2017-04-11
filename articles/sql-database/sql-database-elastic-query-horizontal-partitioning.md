<properties
    pageTitle="Relatórios em bancos de dados externa dimensionada nuvem | Microsoft Azure"
    description="como configurar consultas elástica sobre partições horizontais"    
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/27/2016"
    ms.author="torsteng" />

# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Relatórios em bancos de dados de nuvem dimensionada-out (prévia)

![Consulta através de fragmentos][1]

Bancos de dados sharded distribuir linhas em uma escalados check-out de dados camada. O esquema é idêntico em todos os bancos de dados participantes, também conhecidos como partição horizontal. Usando uma consulta elástica, você pode criar relatórios que se estendem por todos os bancos de dados em um banco de dados sharded.

Para um início rápido, consulte [relatórios em bancos de dados de nuvem dimensionada-out](sql-database-elastic-query-getting-started.md).

Não sharded bancos de dados, consulte [consulta em bancos de dados de nuvem com esquemas diferentes](sql-database-elastic-query-vertical-partitioning.md). 

 
## <a name="prerequisites"></a>Pré-requisitos

* Crie um mapa de fragmentar usando a biblioteca de cliente elástica banco de dados. consulte [Fragmentar mapear gerenciamento](sql-database-elastic-scale-shard-map-management.md). Ou use o aplicativo de amostra em [Introdução às ferramentas de banco de dados elástica](sql-database-elastic-scale-get-started.md).
* Como alternativa, consulte [Migrar bancos de dados existentes para bancos de dados em escala-out](sql-database-elastic-convert-to-use-elastic-tools.md).
* O usuário deve ter permissão de alterar qualquer fonte de dados EXTERNOS. Essa permissão é incluída com a permissão ALTER DATABASE.
* ALTERAR qualquer fonte de dados EXTERNOS permissões é necessárias para se referir a fonte de dados subjacente.

## <a name="overview"></a>Visão geral

Essas instruções criam a representação de metadados de sua camada de dados sharded do banco de dados de consulta elástica. 


1. [CRIAR CHAVE MESTRE](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CRIAR BANCO DE DADOS COM ESCOPO CREDENCIAL](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CRIAR FONTE DE DADOS EXTERNOS](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CRIAR TABELA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="11-create-database-scoped-master-key-and-credentials"></a>Criar 1.1 do banco de dados com escopo de chave mestre e credenciais 

A credencial é usada pela consulta elástica para se conectar a seus bancos de dados remotos.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
>[AZURE.NOTE] Verifique se o *"\<username\>"* não inclui qualquer *"@servername"* sufixo. 

## <a name="12-create-external-data-sources"></a>1.2 criar fontes de dados externos

Sintaxe:

    <External_Data_Source> ::=    
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                                            
            (TYPE = SHARD_MAP_MANAGER,
                    LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>, 
            SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 

### <a name="example"></a>Exemplo 

    CREATE EXTERNAL DATA SOURCE MyExtSrc 
    WITH 
    ( 
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ShardMapDatabase', 
        CREDENTIAL= SMMUser, 
        SHARD_MAP_NAME='ShardMap' 
    );
 
Recupere a lista de fontes de dados externas atual: 

    select * from sys.external_data_sources; 

Fonte de dados externa referências seu mapa fragmentar. Uma consulta elástica usa a fonte de dados externa e o mapa de fragmentar subjacente para enumerar os bancos de dados que participam da camada de dados. As mesmas credenciais são usadas para ler o mapa de fragmentar e acessar os dados nos fragmentos durante o processamento de uma consulta elástica. 

## <a name="13-create-external-tables"></a>1.3 criar tabelas externas 
 
Sintaxe:  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  
    
    <sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**Exemplo**

    CREATE EXTERNAL TABLE [dbo].[order_line]( 
         [ol_o_id] int NOT NULL, 
         [ol_d_id] tinyint NOT NULL,
         [ol_w_id] int NOT NULL, 
         [ol_number] tinyint NOT NULL, 
         [ol_i_id] int NOT NULL, 
         [ol_delivery_d] datetime NOT NULL, 
         [ol_amount] smallmoney NOT NULL, 
         [ol_supply_w_id] int NOT NULL, 
         [ol_quantity] smallint NOT NULL, 
         [ol_dist_info] char(24) NOT NULL 
    ) 
    
    WITH 
    ( 
        DATA_SOURCE = MyExtSrc, 
        SCHEMA_NAME = 'orders', 
        OBJECT_NAME = 'order_details', 
        DISTRIBUTION=SHARDED(ol_w_id)
    ); 

Recupere a lista de tabelas externas do banco de dados atual: 

    SELECT * from sys.external_tables; 

Para descartar tabelas externas:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Comentários

Os dados\_cláusula de origem define a fonte de dados externos (um mapa fragmentar) que é usada para a tabela externa.  

O esquema\_nome e objeto\_cláusulas de nome mapeiam a definição de tabela externa para uma tabela em um esquema diferente. Se omitido, o esquema do objeto remoto será considerado "dbo" e seu nome será considerado idêntico ao nome da tabela externa está sendo definido. Isso é útil se o nome da sua tabela remota já está sendo usado no banco de dados onde você deseja criar a tabela externa. Por exemplo, você deseja definir uma tabela externa para obter uma exibição agregada das exibições do catálogo ou nível de DMVs em seus dados escalados check-out. Como DMVs e modos de exibição de catálogo já existirem localmente, você não pode usar seus nomes para a definição de tabela externa. Em vez disso, use um nome diferente e usar o modo de exibição de catálogo ou o departamento de trânsito nome no esquema\_nome e/ou objeto\_cláusulas de nome. (Consulte o exemplo abaixo.) 

A cláusula de distribuição Especifica a distribuição de dados usada para esta tabela. O processador de consulta utiliza as informações fornecidas na cláusula de distribuição para construir os planos de consulta mais eficientes.  

1. **SHARDED** significa dados horizontalmente são particionados entre os bancos de dados. A chave de partição para a distribuição de dados é o parâmetro **< sharding_column_name >** .
2. **REPLICADO** significa que cópias idênticas da tabela estão presentes em cada banco de dados. É sua responsabilidade garantir que as réplicas são idênticas entre os bancos de dados.
3. **ARRED\_ROBIN** significa que a tabela está horizontalmente particionada usando um método de distribuição dependem de aplicativo. 

**Referência de camada de dados**: A tabela externa DDL refere-se a uma fonte de dados externos. Fonte de dados externa Especifica um mapa de fragmentar que fornece a tabela externa com as informações necessárias para localizar todos os bancos de dados em sua camada de dados. 


### <a name="security-considerations"></a>Considerações de segurança 

Usuários com acesso à tabela de dados externo automaticamente acessem as tabelas subjacentes remotas sob as credenciais fornecidas na definição da fonte de dados externos. Evite indesejada elevação de privilégio por meio da credencial de fonte de dados externa. Use CONCEDER ou REVOGAR para uma tabela externa, como se estivesse uma tabela normal.  

Depois que você definiu sua fonte de dados externos e suas tabelas externas, agora você pode usar T-SQL completo sobre suas tabelas externas.

## <a name="example-querying-horizontal-partitioned-databases"></a>Exemplo: consultando bancos de dados particionados horizontais 

A seguinte consulta executa uma associação de três formas entre depósitos, pedidos e linhas de ordem e usa várias agregações e um filtro seletivo. Ele assume partição (1) horizontal (fragmentação) e (2) que depósitos, pedidos e linhas de ordem são sharded pela coluna de identificação de depósito, e que a consulta elástica pode localizar conjunta junções nos fragmentos e processar a cara parte da consulta nos fragmentos em paralelo. 

    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount, 
         min(ol_delivery_d) as min_deliv_date
    from warehouse 
    join orders 
    on w_id = o_w_id
    join order_line 
    on o_id = ol_o_id and o_w_id = ol_w_id 
    where w_id > 100 and w_id < 200 
    group by w_id, o_c_id 
 
## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Procedimento para execução de T-SQL remota armazenado: sp\_execute_remote

Consulta elástica também introduz um procedimento armazenado que fornece acesso direto para os fragmentos. O procedimento armazenado é chamado [sp\_executar \_remoto](https://msdn.microsoft.com/library/mt703714) e pode ser usado para executar remotos procedimentos armazenados ou código T-SQL nos bancos de dados remotos. Ele usa os seguintes parâmetros: 

* Nome de fonte de dados (nvarchar): O nome da fonte de dados externa do tipo RDBMS. 
* Consulta (nvarchar): consulta o T-SQL a ser executada em cada fragmentar. 
* Declaração de parâmetro (nvarchar) - opcional: String com definições de tipo de dados para os parâmetros usados no parâmetro de consulta (como sp_executesql). 
* Lista de valores de parâmetro - opcional: separados por vírgulas lista de valores de parâmetro (como sp_executesql).

O sp\_executar\_remoto usa a fonte de dados externos fornecida nos parâmetros de invocação para executar a instrução T-SQL nos bancos de dados remotos. Ele usa a credencial de fonte de dados externa para conectar o banco de dados do Gerenciador de shardmap e os bancos de dados remotos.  

Exemplo: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 

## <a name="connectivity-for-tools"></a>Conectividade de ferramentas  

Use regulares cadeias de caracteres de conexão do SQL Server para conectar seu aplicativo, suas ferramentas de integração de BI e dados no banco de dados com suas definições de tabela externa. Certifique-se de que o SQL Server é suportado como uma fonte de dados para sua ferramenta. Em seguida, referência o banco de dados de consulta elástica como qualquer outro banco de dados do SQL Server conectada à ferramenta e use tabelas externas de seu aplicativo ou ferramenta como se fossem tabelas locais. 

## <a name="best-practices"></a>Práticas recomendadas 

* Certifique-se de que o banco de dados de ponto de extremidade de consulta elástica recebeu acesso ao banco de dados shardmap e todos os fragmentos pelos firewalls DB do SQL.  

* Validar ou impor a distribuição de dados definida pela tabela externa. Se a distribuição de dados reais é diferente da distribuição especificada em sua definição de tabela, as consultas podem levar a resultados inesperados. 

* Consulta elástica atualmente não executará eliminação de fragmentar quando predicados da chave de fragmentação permita com segurança excluir determinados fragmentos do processamento.

* Consulta elástica funciona melhor para consultas onde a maioria da computação pode ser feita nos fragmentos. Você geralmente obtém o melhor desempenho de consulta com predicados de filtro seletiva que pode ser avaliada no fragmentos ou junções as chaves de partição que podem ser executadas de maneira alinhadas com a partição em todos os fragmentos. Outros padrões de consulta pode ser necessário carregar grandes quantidades de dados dos fragmentos para o nó principal e podem executar mal

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
