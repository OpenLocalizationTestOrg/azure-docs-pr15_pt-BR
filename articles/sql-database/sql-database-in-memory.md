<properties
    pageTitle="SQL na memória, comece a | Microsoft Azure"
    description="Tecnologias do SQL na memória melhorar o desempenho da transação e cargas de trabalho de análise. Saiba como aproveitar essas tecnologias."
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jodebrui"/>


# <a name="get-started-with-in-memory-preview-in-sql-database"></a>Introdução na memória (visualização) no banco de dados SQL

Recursos de memória bastante melhorar o desempenho da transação e cargas de trabalho de análise nas situações à direita.

Este tópico enfatiza duas demonstrações, um para OLTP na memória e uma para análise na memória. Cada demonstração vem completa com as etapas e o código que você precisa executar a demonstração. Você pode:

- Use o código para testar variações para ver as diferenças nos resultados de desempenho; ou
- Ler o código para entender o cenário e para ver como criar e utilizar os objetos de memória.

> [AZURE.VIDEO azure-sql-database-in-memory-technologies]

- [1 de início rápido: tecnologias de OLTP na memória para mais rápido desempenho de T-SQL](http://msdn.microsoft.com/library/mt694156.aspx) -é outro artigo para ajudá-lo a começar.

#### <a name="in-memory-oltp"></a>OLTP na memória

Os recursos na memória [OLTP](#install_oltp_manuallink) (processamento de transações online) são:

- Tabelas de memória otimizada.
- Compilados nativamente procedimentos armazenados.


Uma tabela de memória otimizada tem uma representação de si mesmo na memória ativa, além da representação padrão em um disco rígido. Transações de negócios com base na tabela executam mais rapidamente porque eles interagem diretamente com apenas a representação que está na memória ativa.

Com OLTP na memória, você pode obter o 30 vezes obtenha de produtividade de transação, dependendo das especificações da carga de trabalho.


Procedimentos armazenados nativamente compilados exigem menos instruções de máquina durante o tempo de execução de interpretado tradicional procedimentos armazenados. Temos visto o resultado da compilação nativo em durações 1/100th da duração interpretada.


#### <a name="in-memory-analytics"></a>Análise na memória 

O recurso do na memória [Analytics](#install_analytics_manuallink) é:

Índices de ColumnStore melhoram o desempenho da análise e relatório de consultas. 


#### <a name="real-time-analytics"></a>Análise em tempo real

Para [Análise em tempo real](http://msdn.microsoft.com/library/dn817827.aspx) você combinar na memória OLTP e análise para obter:

- Visão de negócios em tempo real com base em dados operacionais.


#### <a name="availability"></a>Disponibilidade


GA, disponibilidade geral:

- [Índices de Columnstore](http://msdn.microsoft.com/library/dn817827.aspx) que estão *no disco*.


Visualizar:

- OLTP na memória
- Análise de operacional em tempo real


Considerações enquanto os recursos de memória estão na visualização são descritos [posteriormente neste tópico](#preview_considerations_for_in_memory).


> [AZURE.NOTE] Esses recursos de visualização estão disponíveis apenas para [*Premium*](sql-database-service-tiers.md) bancos de dados não Elástico pools e não está disponível para bancos de dados básico ou padrão.  Suporte para bancos de dados Premium em pools Elástico é em breve. 


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="a-install-the-in-memory-oltp-sample"></a>R. Instalar o exemplo OLTP de memória

Você pode criar o banco de dados de exemplo AdventureWorksLT [V12] alguns cliques no [portal do Azure](https://portal.azure.com/). Em seguida, as etapas desta seção explicam como você pode enriquecer seu banco de dados AdventureWorksLT com:

- Tabelas de memória.
- Um procedimento armazenado nativamente compilado.


#### <a name="installation-steps"></a>Etapas de instalação

1. No [portal do Azure](https://portal.azure.com/), crie um banco de dados Premium em um servidor de V12. Defina a **fonte** para o banco de dados de exemplo AdventureWorksLT [V12].
 - Para obter instruções detalhadas, você pode ver [criar seu primeiro banco de dados do SQL Azure](sql-database-get-started.md).

2. Conecte ao banco de dados com o SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Copie o [script na memória OLTP Transact-SQL](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) para sua área de transferência.
 - O script T-SQL cria os objetos necessários na memória no banco de dados de exemplo AdventureWorksLT criado na etapa 1.

4. Cole o script T-SQL em SSMS e, em seguida, execute o script.
 - É fundamental o `MEMORY_OPTIMIZED = ON` instruções CREATE TABLE cláusula, como em:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Erro 40536


Se você receber o erro 40536 quando você executar o script T-SQL, execute o seguinte script T-SQL para verificar se o banco de dados suporta na memória:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Um resultado de **0** significa na memória não é suportada e 1 significa que é suportado. Para diagnosticar o problema:

- Certifique-se de que o banco de dados foi criado após os recursos de memória OLTP ficaram ativos para visualização.
- Certifique-se de que o banco de dados é o nível de serviço Premium.


#### <a name="about-the-created-memory-optimized-items"></a>Sobre os itens criados memória otimizada

**Tabelas**: A amostra contém tabelas de memória otimizada a seguir:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Você pode inspecionar memória otimizada tabelas através do **Explorador de objetos** no SSMS por:

- **Tabelas**de atalho > **filtro** > **As configurações de filtro** > **É otimizado de memória** é igual a 1.


Ou você pode consultar os modos de exibição de catálogo, como:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Procedimento armazenado nativamente compilado**: SalesLT.usp_InsertSalesOrder_inmem pode inspecionar por meio de uma consulta de modo de exibição de catálogo:


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

## <a name="run-the-sample-oltp-workload"></a>Executar a carga de trabalho OLTP de amostra

A única diferença entre os seguintes *procedimentos armazenados* do duas é que o primeiro procedimento usa memória otimizada versões das tabelas, enquanto o segundo procedimento usa as tabelas em disco regulares:

- SalesLT**.** usp_InsertSalesOrder**_inmem**
- SalesLT**.** usp_InsertSalesOrder**_ondisk**


Nesta seção, você verá como usar o utilitário prático **ostress.exe** para executar os dois procedimentos armazenados nos níveis estressantes. Você pode comparar quanto tempo leva o executa duas Enfatize para ser concluída.


Quando você executa ostress.exe, recomendamos que você passe valores de parâmetro projetados para ambos:

- Executar um grande número de conexões simultâneas, usando - n100.
- Ter cada loop de conexão centenas de vezes, por usando - r500.


Entretanto, talvez você queira iniciar com muito menores valores como - n10 e - r50 para garantir que tudo está funcionando.


### <a name="script-for-ostressexe"></a>Script para ostress.exe


Esta seção exibe o script T-SQL que é incorporado em nossa linha de comando ostress.exe. O script usa itens que foram criados pelo script T-SQL instalada anteriormente.


O script a seguir insere uma ordem de venda de amostra com cinco itens de linha a seguir memória otimizada *tabelas*:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;
    
INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);
    
WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


Para tornar a versão _ondisk do T-SQL anterior para ostress.exe, você poderia simplesmente substitua ambas as ocorrências da subcadeia de caracteres *_inmem* *_ondisk*. Estas substitui afeta os nomes das tabelas e procedimentos armazenados.


### <a name="install-rml-utilities-and-ostress"></a>Instalar ostress e utilitários RML


O ideal seria você planeja executar ostress.exe em uma máquina virtual do Azure. Você criaria uma [Máquina Virtual de Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) na mesma Azure geográfico região onde seu banco de dados AdventureWorksLT reside. Mas você pode executar ostress.exe em seu laptop, em vez disso.


Na máquina virtual ou em qualquer host que você escolher, instale os utilitários de linguagem de marcação de repetição (RML), que incluem ostress.exe.

- Consulte a discussão de ostress.exe no [Banco de dados de exemplo para OLTP na memória](http://msdn.microsoft.com/library/mt465764.aspx).
 - Ou consulte o [Banco de dados de exemplo para OLTP na memória](http://msdn.microsoft.com/library/mt465764.aspx).
 - Ou consulte [Blog para instalar o ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Execute a carga de trabalho de carga de _inmem primeiro


Você pode usar uma janela do *Prompt de Cmd RML* para executar nossa linha de comando ostress.exe. Os parâmetros de linha de comando direcionar ostress para:

- Executar 100 conexões simultaneamente (-n100).
- Ter cada conexão executar o script T-SQL 50 vezes (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Para executar a linha de comando ostress.exe anterior:


1. Redefina o conteúdo de dados do banco de dados executando o seguinte comando no SSMS, excluir todos os dados que foi inseridos por qualquer execuções anteriores:
```
EXECUTE Demo.usp_DemoReset;
```

2. Copie o texto da linha de comando ostress.exe anterior para sua área de transferência.

3. Substituir o `<placeholders>` para os parâmetros -S - U -P -d com os valores reais corretos.

4. Execute sua linha de comando editada em uma janela de RML Cmd.


#### <a name="result-is-a-duration"></a>Resultado é uma duração


Quando ostress.exe for concluído, ele grava a duração da execução como sua linha final da saída na janela RML Cmd. Por exemplo, uma execução de teste mais curta duração cerca de 1,5 minutos:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Redefinir, editar para _ondisk e depois execute novamente


Depois que o resultado de executar _inmem, execute as seguintes etapas para o _ondisk executar:


1. Redefina o banco de dados executando o seguinte comando no SSMS, excluir todos os dados que foi inseridos pela execução anterior:
```
EXECUTE Demo.usp_DemoReset;
```

2. Edite a linha de comando ostress.exe para substituir todas as *_inmem* *_ondisk*.

3. Execute novamente ostress.exe para a segunda vez e capturar o resultado de duração.

4. Redefina novamente o banco de dados, para exclusão responsável do que pode ser uma grande quantidade de dados de teste.


#### <a name="expected-comparison-results"></a>Resultados da comparação esperado

Nossos testes de memória demonstrou um aprimoramento do desempenho de **9 vezes** para essa carga de trabalho simples, com ostress em execução em uma VM Azure na mesma região Azure do banco de dados.



<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;


## <a name="b-install-the-in-memory-analytics-sample"></a>B. Instalar o exemplo de análise na memória


Nesta seção, você compara os resultados de IO e estatísticas ao usar um índice de columnstore versus um índice de árvore b tradicional.


Para obter análise em tempo real em uma carga OLTP, muitas vezes é melhor usar um índice de cluster columnstore. Para obter detalhes, consulte [Columnstore índices descrito](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Preparar o teste de análise columnstore


1. Use o portal do Azure para criar um novo banco de dados AdventureWorksLT de amostra.
 - Use esse nome exato.
 - Escolha qualquer nível de serviço Premium.

2. Copie a [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) sua área de transferência.
 - O script T-SQL cria os objetos necessários na memória no banco de dados de exemplo AdventureWorksLT criado na etapa 1.
 - O script cria a tabela de dimensão e duas tabelas de fatos. As tabelas de fatos são preenchidas com 3,5 milhões de linhas cada.
 - O script pode levar 15 minutos para concluir.

3. Cole o script T-SQL em SSMS e, em seguida, execute o script.
 - Fundamental é a palavra-chave **COLUMNSTORE** em uma instrução **CREATE INDEX** , como em:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Definir AdventureWorksLT para o nível de compatibilidade 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`
 - 130 de nível não está diretamente relacionado aos recursos de memória. Mas nível 130 geralmente fornece desempenho de consulta mais rápido que 120.


#### <a name="crucial-tables-and-columnstore-indexes"></a>Tabelas fundamentais e índices de columnstore


- dbo. FactResellerSalesXL_CCI é uma tabela que tem um índice clusterizado **columnstore** , que tem avançados compressão no nível de *dados* .

- dbo. FactResellerSalesXL_PageCompressed é uma tabela que tenha um equivalente índice de cluster regular, que é compactado somente no nível da *página* .


#### <a name="crucial-queries-to-compare-the-columnstore-index"></a>Consultas fundamentais para comparar o índice columnstore


[Aqui](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) estão vários tipos de consulta de T-SQL, você pode executar para ver as melhorias de desempenho. Na etapa 2 no script T-SQL, há um par de consultas que interessam direto. As duas consultas diferem apenas em uma linha:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Um índice clusterizado columnstore está na tabela**_CCI** de FactResellerSalesXL.

O seguinte trecho de script T-SQL imprime estatísticas de IO e a hora para a consulta de cada tabela.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
    AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a Clustered Columnstore index CCI 
-- The comparison numbers are even more dramatic the larger the table is, this is a 11 million row table only.
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
    AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```



<a id="preview_considerations_for_in_memory" name="preview_considerations_for_in_memory"></a>


## <a name="preview-considerations-for-in-memory-oltp"></a>Considerações de visualização para OLTP na memória


Os recursos de memória OLTP do Azure SQL Database ficaram [ativa para visualização em 28 de outubro de 2015](https://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/).


Na visualização atual, OLTP na memória só há suporte para:

- Bancos de dados que estão em um nível de serviço *Premium* .

- Bancos de dados criados após os recursos de memória OLTP ficaram ativos.
 - Um novo banco de dados não oferece suporte a OLTP de memória se ele for restaurado de um banco de dados criado antes que os recursos de memória OLTP ficaram ativos.


Quando estiver em dúvida, sempre é possível executar o seguinte T-SQL Selecione para verificar se o seu banco de dados oferece suporte a OLTP de memória. Um resultado de **1** significa que o banco de dados dá suporte a OLTP na memória:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


Se a consulta retorna **1**, na memória OLTP é compatível com este banco de dados e qualquer cópia do banco de dados e a restauração de banco de dados criado com base neste banco de dados.


#### <a name="objects-allowed-only-at-premium"></a>Objetos permitidos somente em Premium


Se um banco de dados contiver qualquer um dos seguintes tipos de objetos na memória OLTP ou tipos, rebaixar o nível de serviço do banco de dados do Premium básico ou padrão não é suportada. Para fazer um downgrade o banco de dados, primeiro descartar esses objetos:

- Tabelas de memória otimizada
- Tipos de memória otimizada tabela
- Módulos nativamente compilados


#### <a name="other-relationships"></a>Outras relações


- Não há suporte para recursos de uso de memória OLTP com bancos de dados em pools Elástico durante a visualização.
 - Para mover um banco de dados ou teve objetos na memória OLTP a um pool de Elástico, siga estas etapas:
  - 1. Solte qualquer memória otimizada tabelas, tipos de tabela e compilados nativamente T-SQL módulos no banco de dados
  - 2. Alterar o nível de serviço do banco de dados padrão
  - 3. Mover o banco de dados para o pool de Elástico

- Não há suporte para o uso de memória OLTP com depósito de dados do SQL.
 - O recurso de índice columnstore de análise na memória é suportado no SQL Data Warehouse.

- O armazenamento de consulta não captura consultas interior compilados nativamente módulos.

- Não há suporte para alguns recursos de Transact-SQL na memória OLTP. Isso se aplica ao Microsoft SQL Server e o Azure SQL Database. Para obter detalhes, consulte:
 - [Suporte de Transact-SQL para OLTP na memória](http://msdn.microsoft.com/library/dn133180.aspx)
 - [Construções de Transact-SQL não suportadas pelo OLTP na memória](http://msdn.microsoft.com/library/dn246937.aspx)


## <a name="next-steps"></a>Próximas etapas


- Tente [OLTP do uso de memória em um aplicativo existente do SQL Azure.](sql-database-in-memory-oltp-migration.md)


## <a name="additional-resources"></a>Recursos adicionais

#### <a name="deeper-information"></a>Informações mais detalhadas

- [Saiba mais sobre OLTP na memória, que se aplica ao Microsoft SQL Server e o banco de dados do SQL Azure](http://msdn.microsoft.com/library/dn133186.aspx)

- [Saiba mais sobre a análise de operacional em tempo real no MSDN](http://msdn.microsoft.com/library/dn817827.aspx)

- White paper sobre [padrões de carga de trabalho comuns e considerações de migração](http://msdn.microsoft.com/library/dn673538.aspx), que descreve os padrões de carga de trabalho onde na memória OLTP comumente fornece ganhos significativos de desempenho.

#### <a name="application-design"></a>Design de aplicativo

- [OLTP (otimização de memória) na memória](http://msdn.microsoft.com/library/dn133186.aspx)

- [Uso de memória OLTP, em um aplicativo existente do SQL Azure.](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Ferramentas

- [SQL Server dados ferramentas Preview (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx), para a versão mais recente de mensal.

- [Descrição dos utilitários repetição Markup Language (RML) para o SQL Server](http://support.microsoft.com/en-us/kb/944837)

- [Monitor de memória armazenamento](sql-database-in-memory-oltp-monitoring.md) para OLTP na memória.

