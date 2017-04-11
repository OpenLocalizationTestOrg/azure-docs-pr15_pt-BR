<properties
   pageTitle="Distribuir tabelas no SQL Data Warehouse | Microsoft Azure"
   description="Primeiros passos com distribuir tabelas no depósito de dados do SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="distributing-tables-in-sql-data-warehouse"></a>Distribuir tabelas no depósito de dados do SQL

> [AZURE.SELECTOR]
- [Visão geral][]
- [Tipos de dados][]
- [Distribuir][]
- [Índice][]
- [Partição][]
- [Estatísticas][]
- [Temporário][]

SQL Data Warehouse é que um processamento altamente paralelo (MPP) distribuído com o sistema de banco de dados.  Dividindo dados e capacidade de processamento em vários nós, SQL Data Warehouse podem oferecer enorme escalabilidade - muito além de qualquer sistema único.  Decidir como distribuir seus dados dentro de sua Data Warehouse SQL é um dos fatores mais importantes para atingir o desempenho ideal.   A chave para um desempenho ideal é minimizando a movimentação de dados e alternadamente a chave para minimizar a movimentação de dados é selecionar a estratégia de distribuição à direita.

## <a name="understanding-data-movement"></a>Noções básicas sobre a movimentação de dados

Em um sistema MPP, os dados de cada tabela são divididos entre vários bancos de dados subjacentes.  As consultas mais otimizadas em um sistema MPP simplesmente podem ser passadas para executar nos bancos de dados distribuídos individuais sem a interação entre outros bancos de dados.  Por exemplo, digamos que você tenha um banco de dados com dados de vendas que contém duas tabelas, vendas e clientes.  Se você tiver uma consulta que precisa ingressar em sua tabela de vendas à tabela clientes e divida tabelas de cliente para cima e suas vendas por número de cliente, a inserção de cada cliente em um banco de dados separado, todas as consultas que ingressar vendas e atendimento ao cliente podem ser resolvidas dentro de cada banco de dados sem conhecimento de outros bancos de dados.  Em contraste, se você dividido seus dados de vendas por número de pedido e dados do cliente por número de cliente, em seguida, qualquer banco de dados determinado não terão os dados correspondentes para cada cliente e, portanto, se você quiser participar de seus dados de vendas com os dados do cliente, você precisa obter os dados para cada cliente de outros bancos de dados.  Neste exemplo segundo, movimentação de dados precisa ocorrer para mover os dados do cliente para os dados de vendas, para que as duas tabelas podem ser unidas.  

Movimentação de dados não é sempre ruim, às vezes, é necessário resolver uma consulta.  Mas quando esta etapa extra pode evitar, naturalmente sua consulta será executada com mais rapidez.  Movimentação de dados mais comumente surge quando tabelas associadas ou agregações são executadas.  Muitas vezes você precisa fazer ambos, portanto enquanto você poderá otimizar para um cenário, como uma associação, você ainda precisar de movimentação de dados para ajudá-lo a resolver para o outro cenário, como uma agregação.  O truque é descobrir qual é menos trabalho.  Na maioria dos casos, distribuir grandes tabelas de fatos em uma coluna comumente associada é o método mais eficaz para reduzir a maioria dos movimentação de dados.  Distribuir dados em colunas de junção é um método muito mais comuns para reduzir a movimentação de dados de distribuição de dados em colunas envolvidas em uma agregação.

## <a name="select-distribution-method"></a>Selecione o método de distribuição

Nos bastidores, o SQL Data Warehouse divide os dados em 60 bancos de dados.  Cada banco de dados individual é conhecido como uma **distribuição**.  Quando os dados são carregados em cada tabela, o SQL Data Warehouse tem saber como dividir seus dados entre essas 60 distribuições.  

O método de distribuição é definido no nível de tabela e atualmente, há duas opções:

1. **Round robin** qual distribuir dados uniformemente mas aleatoriamente.
2. **Hash distribuído** que distribui dados com base em valores de uma única coluna de hash

Por padrão, quando você não definir um método de distribuição de dados, sua tabela será distribuída usando o método de distribuição de **repetição alternada** .  No entanto, conforme se tornam mais sofisticadas na sua implementação, você precisará considerar usando tabelas **hash distribuído** para minimizar a movimentação de dados, que por sua vez otimizar desempenho da consulta.

### <a name="round-robin-tables"></a>Tabelas de repetição alternada

Usando o método Round Robin da distribuição de dados é muito como parece.  Como seus dados são carregados, cada linha simplesmente é enviada para a próxima distribuição.  Este método de distribuição os dados serão sempre aleatoriamente distribuir os dados muito uniformemente entre todas as distribuições.  Ou seja, não há nenhuma classificação feito durante o processo de repetição alternada que coloca seus dados.  Uma distribuição de repetição alternada é chamada um hash aleatório por esse motivo.  Com uma tabela distribuída alternada não é necessário compreender os dados.  Por esse motivo, tabelas de alternada geralmente, fazem destinos de carregamento boa.

Por padrão, se nenhum método de distribuição for escolhido, o método de distribuição de repetição alternada será usado.  Entretanto, enquanto as tabelas de repetição alternada são fáceis de usar, porque os dados são distribuídos aleatoriamente o sistema significa que o sistema não garante que distribuição cada linha é.  Como resultado, o sistema às vezes precisa chamar uma operação de movimentação de dados para organizar melhor seus dados antes que ele poderá resolver uma consulta.  Esta etapa extra pode abrandar suas consultas.

Considere usando a distribuição de Round Robin para sua tabela nas seguintes situações:

- Quando o ponto de partida como ponto de partida simple
- Se não houver nenhuma chave de ingresso óbvio
- Se não houver coluna boa candidata para distribuir a tabela de hash
- Se a tabela não compartilha uma chave de junção comum com outras tabelas
- Se a junção é menos significativa de outras ligações na consulta
- Quando a tabela é uma tabela de preparação temporária

Dois exemplos criará uma tabela de repetição alternada:

```SQL
-- Round Robin created by default
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
;

-- Explicitly Created Round Robin Table
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

> [AZURE.NOTE] Enquanto round robin é o tipo de tabela padrão sendo explícitos em sua DDL é considerado uma prática recomendada para que as intenções do seu layout de tabela são claras para outras pessoas.

### <a name="hash-distributed-tables"></a>Hash tabelas distribuídas

Usando um algoritmo de **Hash distribuído** para distribuir suas tabelas pode melhorar o desempenho para muitos cenários, reduzindo a movimentação de dados em tempo de consulta.  Tabelas de hash distribuído estiverem que são divididas entre os bancos de dados distribuídos usando um algoritmo de hash em uma única coluna que você selecionar.  A coluna de distribuição é o que determina como os dados são divididos em seus bancos de dados distribuídos.  A função de hash usa a coluna de distribuição para atribuir linhas para distribuições.  O algoritmo de hash e de distribuição resultante é determinante.  Esse é o mesmo valor com o mesmo tipo de dados será sempre tenha a mesma distribuição.    

Este exemplo criará uma tabela distribuída em id:

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

## <a name="select-distribution-column"></a>Selecione a coluna de distribuição

Quando você escolhe a **distribuição de hash** uma tabela, você precisará selecionar uma coluna de distribuição única.  Ao selecionar uma coluna de distribuição, há três principais fatores a considerar.  

Selecione uma única coluna que será:

1. Não ser atualizados
2. Distribuir dados uniformemente, evitando dados distorção
3. Minimizar a movimentação de dados

### <a name="select-distribution-column-which-will-not-be-updated"></a>Selecione a coluna de distribuição que não será atualizada

Colunas de distribuição não são atualizáveis, portanto, selecione uma coluna com valores estáticos.  Se uma coluna precisarão ser atualizados, geralmente não é um candidato boa distribuição.  Se houver um caso onde você deve atualizar uma coluna de distribuição, isso pode ser feito primeiro a exclusão da linha e, em seguida, inserindo uma nova linha.

### <a name="select-distribution-column-which-will-distribute-data-evenly"></a>Selecione a coluna de distribuição que distribui dados uniformemente

Como um sistema distribuído executa apenas tão rápida quanto sua distribuição mais lenta, é importante dividir o trabalho uniformemente entre as distribuições para atingir execução equilibrada através do sistema.  A maneira como o trabalho é dividido em um sistema distribuído baseia-se nos onde residem os dados para cada distribuição.  Isso torna muito importante para selecionar a coluna à direita de distribuição para distribuir os dados para que cada distribuição tem trabalho igual e levará ao mesmo tempo para concluir sua parte do trabalho.  Quando o trabalho é dividido bem através do sistema, os dados são equilibrados as distribuições.  Quando os dados não estão equilibrados igualmente, chamamos essa **distorção de dados**.  

Para dividir dados uniformemente e evitar distorção de dados, considere o seguinte ao selecionar a coluna de distribuição:

1. Selecione uma coluna que contém um número significativo de valores distintos.
2. Evite a distribuição de dados em colunas com alguns valores distinta. 
3. Evite a distribuição de dados em colunas com uma alta frequência de valores nulos.
4. Evite a distribuição de dados em colunas de data.

Desde que cada valor é hash a 1 de 60 distribuições, para conseguir distribuição uniforme você desejará selecione uma coluna que é altamente exclusivo e contém mais de 60 valores exclusivos.  Para ilustrar, considere a possibilidade de um caso onde uma coluna tem apenas 40 valores exclusivos.  Se esta coluna foi marcada como a chave de distribuição, os dados para essa tabela seriam pouse em 40 distribuições no máximo, deixando 20 distribuições com nenhum dado e nenhum processamento fazer.  Por outro lado, as outras 40 distribuições teria mais trabalho fazer isso se os dados uniformemente foi distribuídos mais de 60 distribuições.  Esse cenário é um exemplo de dados distorção.

No sistema MPP, cada etapa de consulta aguarda todas as distribuições concluir o compartilhamento do trabalho.  Se uma distribuição está fazendo mais trabalho que os outros, em seguida, o recurso das outras distribuições são essencialmente desperdício espera na distribuição de ocupado.  Quando o trabalho não é distribuído uniformemente todas as distribuições, chamamos esse **processamento skew**.  Distorção de processamento fará com que as consultas para executar mais lentamente que se a carga de trabalho pode ser distribuída uniformemente entre as distribuições.  Dados distorção conduzirá a distorção de processamento.

Evite distribuir na coluna altamente anulável como os valores nulos serão todos será levado na mesma distribuição. Distribuindo em uma coluna de data também pode causar distorção de processamento porque todos os dados de uma determinada data ficará na mesma distribuição. Se vários usuários estão executando consultas toda a filtragem na mesma data, e em seguida, apenas 1 das 60 distribuições fará todo o trabalho desde uma determinada data só ficará em uma distribuição. Neste cenário, as consultas provavelmente serão executado 60 vezes mais lentos que se os dados foram distribuídos igualmente sobre todas as distribuições. 

Quando nenhuma coluna de boa candidata existir, em seguida, considere usando o round robin como o método de distribuição.

### <a name="select-distribution-column-which-will-minimize-data-movement"></a>Selecione a coluna de distribuição que minimizará a movimentação de dados

Minimizar a movimentação de dados selecionando a coluna à direita de distribuição é uma das estratégias mais importantes para otimizar o desempenho do SQL Data Warehouse.  Movimentação de dados mais comumente surge quando tabelas associadas ou agregações são executadas.  Colunas usadas em `JOIN`, `GROUP BY`, `DISTINCT`, `OVER` e `HAVING` cláusulas todos fazer para **bons** candidatos a distribuição de hash. 

Por outro lado, colunas na `WHERE` cláusula faça **não** fazer para candidatos de coluna de hash boa porque eles limitam quais distribuições participarem da consulta, causando processamento inclinar.  Um bom exemplo de uma coluna que pode estar tentando distribuir em, mas geralmente pode causar esse processamento distorção é uma coluna de data.

Em geral, se você tiver duas grandes tabelas de fatos frequentemente envolvidas em uma associação, você obterá a maioria do desempenho distribuindo ambas as tabelas em uma das colunas de junção.  Se você tiver uma tabela que nunca está associada a outra tabela de fatos maiores, examine a colunas que estão frequentemente na `GROUP BY` cláusula.

Há alguns critérios importantes que devem ser atendidos para evitar a movimentação de dados durante uma junção:

1. As tabelas envolvidas na join devem ser distribuído em **uma** das colunas participando de junção de hash.
2. Os tipos de dados das colunas de junção devem corresponder entre as duas tabelas.
3. As colunas devem ser associadas com um operador de igual.
4. O tipo de junção não pode ser um `CROSS JOIN`.


## <a name="troubleshooting-data-skew"></a>Distorção de dados de solução de problemas

Quando os dados da tabela são distribuídos usando o método de distribuição de hash há uma chance que algumas distribuições serão inclinadas para ter desproporcional mais dados do que as outras pessoas. Distorção de excesso de dados pode afetar o desempenho de consulta porque o resultado final de uma consulta distribuída deve esperar que a distribuição mais longa em execução concluir. Dependendo do grau da inclinação de dados, convém solucioná-lo.

### <a name="identifying-skew"></a>Identificando inclinação

Uma maneira simples para identificar uma tabela como Inclinado é usar `DBCC PDW_SHOWSPACEUSED`.  Isso é uma maneira de maneira rápida e simple de ver o número de linhas da tabela que estão armazenados em cada uma das 60 distribuições do banco de dados.  Lembre-se de que para o desempenho mais equilibrado, as linhas na tabela distribuída devem ser distribuídas uniformemente em todas as distribuições.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

No entanto, se você consultar os modos de exibição de gerenciamento dinâmico de depósito de dados do SQL Azure (departamento de trânsito) você pode executar uma análise mais detalhada.  Para começar, crie o modo de exibição do modo de exibição [dbo.vTableSizes][] usando a instrução SQL do artigo de[Visão geral] de [Visão geral de tabela].  Depois que o modo de exibição é criado, execute essa consulta para identificar quais tabelas tem mais de inclinação de dados de 10%.

```sql
select *
from dbo.vTableSizes 
where two_part_name in 
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
    )
order by two_part_name, row_count
;
```

### <a name="resolving-data-skew"></a>Resolvendo dados distorção

Não tudo distorção é suficiente para garantir uma correção.  Em alguns casos, o desempenho de uma tabela em algumas consultas pode exceder o prejuízo dos dados distorção.  Para decidir se você deve resolver dados distorção em uma tabela, você deve compreender tanto quanto possível sobre os volumes de dados e consultas na carga de trabalho.   Uma maneira de examinar o impacto de distorção é usar as etapas no artigo [Monitoramento de consulta][] para monitorar o impacto de inclinação no desempenho da consulta e especificamente o impacto às consultas quanto tempo levar ao concluir as distribuições individuais.

Distribuição de dados é uma questão de encontrar o equilíbrio certo entre minimizando dados inclinação e minimizando movimentação de dados. Elas podem ser opostas metas e às vezes você desejará manter dados distorção para reduzir a movimentação de dados. Por exemplo, quando a coluna de distribuição com frequência é a coluna compartilhada em junções e agregações, você irá minimizar movimentação de dados. O benefício de ter o movimento de dados mínima talvez superam o impacto de ter dados inclinar. 

A forma típica de resolver skew de dados é recriar a tabela com uma coluna de distribuição diferentes. Como não há nenhuma maneira de alterar a coluna de distribuição em uma tabela existente, a maneira de alterar a distribuição de uma tabela para criá-lo novamente com um [] [CTAS].  Aqui estão dois exemplos de como resolver dados distorção:

### <a name="example-1-re-create-the-table-with-a-new-distribution-column"></a>Exemplo 1: Criar novamente a tabela com uma nova coluna de distribuição

Este exemplo usa [[CTAS]] para recriar uma tabela com uma coluna de distribuição de hash diferente. 

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### <a name="example-2-re-create-the-table-using-round-robin-distribution"></a>Exemplo 2: Criar novamente a tabela usando a distribuição de repetição alternada

Este exemplo usa [[CTAS]] para recriar uma tabela com round robin em vez de uma distribuição de hash. Essa alteração produzirá mesmo distribuição de dados ao custo de movimentação de aumento de dados. 

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_ROUND_ROBIN] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_ROUND_ROBIN] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_ROUND_ROBIN] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_ROUND_ROBIN] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_ROUND_ROBIN] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_ROUND_ROBIN] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_ROUND_ROBIN] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_ROUND_ROBIN] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o design da tabela, consulte o [Distribuir][], [índice][], [partição][], [Tipos de dados][], [estatísticas][] e artigos[temporário] [Tabelas temporárias].

Para obter uma visão geral das práticas recomendadas, consulte [Práticas recomendadas de depósito de dados do SQL][].


<!--Image references-->

<!--Article references-->
[Visão geral]: ./sql-data-warehouse-tables-overview.md
[Tipos de dados]: ./sql-data-warehouse-tables-data-types.md
[Distribuir]: ./sql-data-warehouse-tables-distribute.md
[Índice]: ./sql-data-warehouse-tables-index.md
[Partição]: ./sql-data-warehouse-tables-partition.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporário]: ./sql-data-warehouse-tables-temporary.md
[Práticas recomendadas de depósito de dados do SQL]: ./sql-data-warehouse-best-practices.md
[Monitoramento de consulta]: ./sql-data-warehouse-manage-monitor.md
[dbo.vTableSizes]: ./sql-data-warehouse-tables-overview.md#querying-table-sizes

<!--MSDN references-->
[DBCC PDW_SHOWSPACEUSED()]: https://msdn.microsoft.com/library/mt204028.aspx

<!--Other Web references-->
