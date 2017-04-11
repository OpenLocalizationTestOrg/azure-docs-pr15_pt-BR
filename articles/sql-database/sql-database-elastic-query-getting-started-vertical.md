<properties
    pageTitle="Começar a usar consultas de bancos de dados (partição vertical) | Microsoft Azure"   
    description="como usar a consulta de banco de dados elástica com verticalmente particionado bancos de dados"
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
    ms.date="05/23/2016"
    ms.author="torsteng" />

# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Começar a usar consultas de bancos de dados (partição vertical) (prévia)

Consulta de banco de dados elástica (visualização) para o banco de dados do Azure SQL permite executar consultas de T-SQL que abrangem vários bancos de dados usando um ponto de conexão única. Este tópico se aplica a [verticalmente particionados bancos de dados](sql-database-elastic-query-vertical-partitioning.md).  

Quando concluir, você irá: Saiba como configurar e usar um banco de dados do SQL Azure para executar consultas que abrangem vários bancos de dados relacionados. 

Para obter mais informações sobre o recurso de consulta de banco de dados elástica, consulte [Visão geral de consultas de banco de dados elástica Azure SQL Database](sql-database-elastic-query-overview.md). 

## <a name="create-the-sample-databases"></a>Criar os bancos de dados de exemplo

Começar, precisamos criar dois bancos de dados, **clientes** e **pedidos**, em servidores lógicos iguais ou diferentes.   

Execute as seguintes consultas no banco de dados de **pedidos** para criar a tabela de **OrderInformation** e entrada de dados de exemplo. 

    CREATE TABLE [dbo].[OrderInformation]( 
        [OrderID] [int] NOT NULL, 
        [CustomerID] [int] NOT NULL 
        ) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

Agora, execute seguindo consulta o banco de dados de **clientes** para criar a tabela de **CustomerInformation** e entrada de dados de exemplo. 

    CREATE TABLE [dbo].[CustomerInformation]( 
        [CustomerID] [int] NOT NULL, 
        [CustomerName] [varchar](50) NULL, 
        [Company] [varchar](50) NULL 
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
    ) 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## <a name="create-database-objects"></a>Criar objetos de banco de dados
### <a name="database-scoped-master-key-and-credentials"></a>Banco de dados com escopo de chave mestre e credenciais

1. Abra o SQL Server Management Studio ou ferramentas de dados do SQL Server no Visual Studio.
2. Conectar o banco de dados de pedidos e execute os seguintes comandos T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
        WITH IDENTITY = '<username>', 
        SECRET = '<password>';  

    O "nome" e "senha" devem ser o nome de usuário e senha usados para efetuar login no banco de dados clientes.
    Autenticação usando o Azure Active Directory com consultas elástica não é suportada atualmente.

### <a name="external-data-sources"></a>Fontes de dados externos
Para criar uma fonte de dados externos, execute o seguinte comando no banco de dados de pedidos: 

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
        (TYPE = RDBMS, 
        LOCATION = '<server_name>.database.windows.net', 
        DATABASE_NAME = 'Customers', 
        CREDENTIAL = ElasticDBQueryCred, 
    ) ;

### <a name="external-tables"></a>Tabelas externas
Crie uma tabela externa do banco de dados de pedidos, que corresponde a definição da tabela CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
    ( [CustomerID] [int] NOT NULL, 
      [CustomerName] [varchar](50) NOT NULL, 
      [Company] [varchar](50) NOT NULL) 
    WITH 
    ( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Executar uma consulta de T-SQL de elástica banco de dados de exemplo

Depois que você definiu sua fonte de dados externos e suas tabelas externas agora você pode usar T-SQL para consultar suas tabelas externas. Execute esta consulta do banco de dados de pedidos: 

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
    FROM OrderInformation 
    INNER JOIN CustomerInformation 
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## <a name="cost"></a>Custo

Atualmente, o recurso de consulta de banco de dados elástica está incluído no custo de seu banco de dados do SQL Azure.  

Para informações sobre preços consulte [Preços de banco de dados do SQL](/pricing/details/sql-database). 


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->

<!--anchors-->
