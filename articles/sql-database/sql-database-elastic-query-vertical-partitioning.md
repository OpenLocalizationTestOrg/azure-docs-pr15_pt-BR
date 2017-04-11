<properties
    pageTitle="Consulta em bancos de dados de nuvem com esquema diferente | Microsoft Azure"
    description="como configurar consultas de bancos de dados em relação às partições verticais"    
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

# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Consulta através de bancos de dados de nuvem com diferentes esquemas (prévia)

![Consulta através de tabelas em bancos de dados diferentes][1]

Bancos de dados verticalmente particionada usam diferentes conjuntos de tabelas em bancos de dados diferentes. Isso significa que o esquema é diferente em bancos de dados diferentes. Por exemplo, todas as tabelas para estoque estão em um banco de dados enquanto todas as tabelas relacionadas contábil estiverem em um segundo banco de dados. 

## <a name="prerequisites"></a>Pré-requisitos

* O usuário deve ter permissão de alterar qualquer fonte de dados EXTERNOS. Essa permissão é incluída com a permissão ALTER DATABASE.
* ALTERAR qualquer fonte de dados EXTERNOS permissões é necessárias para se referir a fonte de dados subjacente.

## <a name="overview"></a>Visão geral

**Observação**: ao contrário com partição horizontal, essas instruções DDL não dependem definindo uma camada de dados com um mapa de fragmentar por meio da biblioteca de cliente elástica banco de dados.

1. [CRIAR CHAVE MESTRE](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CRIAR BANCO DE DADOS COM ESCOPO CREDENCIAL](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CRIAR FONTE DE DADOS EXTERNOS](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CRIAR TABELA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx) 


## <a name="create-database-scoped-master-key-and-credentials"></a>Criar chave mestre de banco de dados com escopo e credenciais 

A credencial é usada pela consulta elástica para se conectar a seus bancos de dados remotos.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
**Observação**    Certifique-se de que o *<username>* não inclui qualquer *"@servername"* sufixo. 

## <a name="create-external-data-sources"></a>Criar fontes de dados externos

Sintaxe:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

**Importante**   O parâmetro de tipo deve ser definido como **RDBMS**. 

### <a name="example"></a>Exemplo 

O exemplo a seguir ilustra o uso da instrução criar fontes de dados externas. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 
 
Para recuperar a lista de fontes de dados externas atual: 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Tabelas externas 

Sintaxe:

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
    { WITH ( <rdbms_external_table_options> ) } 
    )[;] 
    
    <rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### <a name="example"></a>Exemplo  

    CREATE EXTERNAL TABLE [dbo].[customer]( 
        [c_id] int NOT NULL, 
        [c_firstname] nvarchar(256) NULL, 
        [c_lastname] nvarchar(256) NOT NULL, 
        [street] nvarchar(256) NOT NULL, 
        [city] nvarchar(256) NOT NULL, 
        [state] nvarchar(20) NULL, 
        [country] nvarchar(50) NOT NULL, 
    ) 
    WITH 
    ( 
           DATA_SOURCE = RemoteReferenceData 
    ); 

O exemplo a seguir mostra como recuperar a lista de tabelas externas do banco de dados atual: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Comentários

Consulta elástica estende a sintaxe da tabela externa existente para definir tabelas externas que usa fontes de dados externos do tipo RDBMS. Uma definição de tabela externa para partição vertical aborda os seguintes aspectos: 

* **Esquema**: A tabela externa DDL define um esquema que podem usar suas consultas. Esquema fornecido em sua definição de tabela externa precisa coincidir com o esquema das tabelas no banco de dados remoto onde os dados reais estão armazenados. 

* **Referência de banco de dados remoto**: A tabela externa DDL refere-se a uma fonte de dados externos. Fonte de dados externa Especifica o nome de servidor lógico e o nome do banco de dados do banco de dados remoto onde os dados da tabela real estão armazenados. 

A sintaxe para criar tabelas externas usando uma fonte de dados externa, conforme descrito na seção anterior, é da seguinte maneira: 

A cláusula DATA_SOURCE define a fonte de dados externos (ou seja, o banco de dados remoto em caso de partição vertical) que é usada para a tabela externa.  

As cláusulas SCHEMA_NAME e nome_objeto fornecem a capacidade de mapear a definição de tabela externa para uma tabela em um esquema diferente no banco de dados remoto, ou para uma tabela com um nome diferente, respectivamente. Isso é útil se você deseja definir uma tabela externa a um modo de exibição de catálogo ou departamento de trânsito em seu banco de dados remoto – ou qualquer outra situação onde o nome de tabela remota já está em uso localmente.  

A seguinte instrução DDL descarta uma definição de tabela externa existente do catálogo local. Ele não afeta o banco de dados remoto. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Permissões para tabela externa criar/SOLTAR**: permissões de alterar qualquer fonte de dados EXTERNOS são necessárias para a tabela externa DDL que também é necessário para se referir a fonte de dados subjacente.  

## <a name="security-considerations"></a>Considerações de segurança
Usuários com acesso à tabela de dados externo automaticamente acessem as tabelas subjacentes remotas sob as credenciais fornecidas na definição da fonte de dados externos. Você deve cuidadosamente gerenciar o acesso à tabela de dados externo para evitar indesejada elevação de privilégio por meio da credencial de fonte de dados externa. Permissões de SQL regulares podem ser usadas para CONCEDER ou REVOGAR o acesso a uma tabela externa como se estivesse uma tabela normal.  


## <a name="example-querying-vertically-partitioned-databases"></a>Exemplo: consultando verticalmente particionado bancos de dados 

A seguinte consulta executa uma junção de três formas entre as duas tabelas locais para linhas de pedido e pedidos e a tabela remota para clientes. Este é um exemplo do caso de uso de dados de referência, para consulta elástica: 

    SELECT      
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline, 
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer 
    JOIN orders 
    ON c_id = o_c_id
    JOIN  order_line 
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100


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

Você pode usar regulares cadeias de caracteres de conexão do SQL Server para conectar suas ferramentas de integração de BI e dados para bancos de dados no servidor de banco de dados do SQL que tenha elástica consulta habilitado e tabelas externas definidas. Certifique-se de que o SQL Server é suportado como uma fonte de dados para sua ferramenta. Em seguida, consulte o banco de dados de consulta elástica e suas tabelas externas assim como qualquer outro SQL Server banco de dados que você deseja se conectar a com a ferramenta. 

## <a name="best-practices"></a>Práticas recomendadas 
 
* Certifique-se de que o banco de dados de ponto de extremidade de consulta elástica recebeu acesso ao banco de dados remoto, permitindo o acesso para serviços do Azure em sua configuração de firewall do banco de dados do SQL. Também verifique se a credencial fornecida na definição da fonte de dados externos com êxito pode efetuar logon no banco de dados remoto e tem as permissões para acessar a tabela remota.  

* Consulta elástica funciona melhor para consultas onde a maioria da computação pode ser feita nos bancos de dados remotos. Normalmente, você obtém o melhor desempenho de consulta com predicados de filtro seletiva que pode ser avaliada no bancos de dados remotos ou junções que podem ser executadas completamente no banco de dados remoto. Outros padrões de consulta talvez precise carregar grandes quantidades de dados do banco de dados remoto e podem executar mal. 


## <a name="next-steps"></a>Próximas etapas

Para consultar horizontalmente particionados bancos de dados (também conhecido como bancos de dados sharded), consulte [consultas em bancos de dados de nuvem sharded (horizontalmente particionados)](sql-database-elastic-query-horizontal-partitioning.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
