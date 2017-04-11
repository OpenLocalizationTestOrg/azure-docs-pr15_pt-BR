<properties
   pageTitle="Indexação tabelas no SQL Data Warehouse | Microsoft Azure"
   description="Guia de Introdução com tabela indexação no depósito de dados do SQL Azure."
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
   ms.date="07/12/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="indexing-tables-in-sql-data-warehouse"></a>Tabelas de indexação no depósito de dados do SQL

> [AZURE.SELECTOR]
- [Visão geral][]
- [Tipos de dados][]
- [Distribuir][]
- [Índice][]
- [Partição][]
- [Estatísticas][]
- [Temporário][]

SQL Data Warehouse oferece várias opções de indexação incluindo [índices columnstore agrupada][], [índices agrupados e índices não agrupados][].  Além disso, ele também oferece uma opção sem índice também conhecido como [pilha][].  Este artigo aborda os benefícios de cada tipo de índice, bem como dicas para obter o desempenho a maioria dos seus índices. Consulte [criar sintaxe de tabela][] para obter mais detalhes sobre como criar uma tabela no SQL Data Warehouse.

## <a name="clustered-columnstore-indexes"></a>Índices de columnstore agrupada

Por padrão, o SQL Data Warehouse cria um índice de columnstore agrupadas quando são especificadas sem opções de índice em uma tabela. Tabelas de columnstore agrupada oferecem o nível mais alto de compactação de dados, bem como o melhor desempenho geral de consulta.  Tabelas de columnstore agrupada geralmente superará tabelas de índice ou pilha agrupadas e geralmente são a melhor opção para tabelas grandes.  Por estes motivos, columnstore agrupada é o melhor lugar para começar quando você não tiver certeza de como sua tabela de índice.  

Para criar uma tabela de columnstore agrupada, basta especificar índice COLUMNSTORE AGRUPADO na cláusula WITH ou deixar a cláusula WITH desativado:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Existem alguns cenários onde columnstore agrupada pode não ser uma boa opção:

- Tabelas de ColumnStore não suportam índices não-clusterizados secundários.  Considere a pilha ou tabelas de índice agrupado em vez disso.
- Tabelas de ColumnStore não suportam varchar (max), nvarchar (max) e varbinary (max).  Considere a pilha ou índice agrupado em vez disso.
- Tabelas de ColumnStore podem ser menos eficientes para dados temporárias.  Considere a possibilidade de pilha e talvez até mesmo temporárias tabelas.
- Tabelas pequenas com menos de 100 milhões de linhas.  Considere a possibilidade de tabelas de pilha.

## <a name="heap-tables"></a>Tabelas de pilha

Quando você está temporariamente iniciais dados no SQL Data Warehouse, você descobrirá que usando uma tabela de pilha tornará o processo geral mais rápido.  Isso ocorre porque cargas para pilhas são mais rápidas que a tabelas de índice e em alguns casos que a leitura subsequente pode ser feita em cache.  Se você estiver carregando dados apenas para estágio-lo antes de executar transformações mais, carregar a tabela à tabela de pilha será muito mais rápido que carregar os dados a uma tabela de columnstore agrupada. Além disso, o carregamento de dados a uma [tabela temporária][temporário] também carregará muito mais rápido do que carregar uma tabela para armazenamento permanente.  

Para tabelas de pesquisa pequenas, menos de 100 milhões de linhas, tabelas de pilha geralmente fazem sentido.  Tabelas de columnstore cluster começam a obter compactação ideal quando há mais de 100 milhões de linhas.

Para criar uma tabela de pilha, basta Especifica pilha na cláusula com:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Índices de cluster e

Índices clusterizados podem superam columnstore agrupada tabelas quando uma única linha precisa ser recuperadas rapidamente.  Para consultas onde um único ou poucos pesquisa de linha é necessária para desempenho com extrema velocidade, considere a possibilidade de um índice de cluster ou cluster índice secundário.  A desvantagem de usar um índice agrupado é que apenas as consultas que usam um filtro altamente seletivo na coluna índice clusterizado irá se beneficiar.  Para melhorar o filtro em outras colunas um índice sem cluster pode ser adicionado a outras colunas.  No entanto, cada índice que é adicionado a uma tabela adicionará espaço e o tempo de processamento aos carregamentos.

Para criar uma tabela de índice agrupado, basta Especifica índice AGRUPADO na cláusula com:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Para adicionar um índice não agrupado em uma tabela, basta use a seguinte sintaxe:

```SQL
CREATE INDEX zipCodeIndex ON t1 (zipCode);
```

> [AZURE.NOTE] Um índice não agrupado é criado por padrão quando criar índice é usado. Além disso, um índice não agrupado só é permitido em uma tabela de armazenamento de linha (pilha ou índice AGRUPADO). Índices não agrupados na parte superior de um índice de COLUMNSTORE cluster não é permitido neste momento.


## <a name="optimizing-clustered-columnstore-indexes"></a>Otimização de índices columnstore agrupada

Tabelas de columnstore agrupadas são organizadas em dados em segmentos.  Ter qualidade de segmento alta é essencial para a obtenção de desempenho ideal de consulta em uma tabela de columnstore.  Qualidade de segmento pode ser medida pelo número de linhas em um grupo de linha compactado.  Qualidade de segmento é ideal em que há pelo menos 100 mil linhas por linha compactada agrupar e obtém no desempenho como o número de linhas por abordagem de grupo de linha 1.048.576 linhas, que é as maioria das linhas que um grupo de linha pode conter.

O abaixo de modo de exibição pode ser criado e usado no seu sistema para calcular as linhas média por linha agrupar e identificam qualquer índices de columnstore cluster ideal.  A última coluna neste modo de exibição gerará como instrução SQL que pode ser usada para recriar os índices.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,   COUNT(DISTINCT rg.[partition_number])                   AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,   CEILING ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Agora que você criou o modo de exibição, executar essa consulta para identificar tabelas com grupos de linhas com menos de 100 mil linhas.  Claro, você talvez queira aumentar o limite de 100K se você estiver procurando mais qualidade de segmento ideais. 

```sql
SELECT  *
FROM    [dbo].[vColumnstoreDensity]
WHERE   COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Depois de executar a consulta que você pode começar a examinar os dados e analisar os resultados. Esta tabela explica o que procurar em sua análise de grupo de linha.


| Coluna                             | Como usar esses dados                                                                                                                                                                      |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table_partition_count]            | Se a tabela estiver particionada, você pode esperar ver a contagem de grupo de abrir linha superior. Cada partição na distribuição poderia teoricamente ter um grupo de linha aberta associado a ele. Incluir isso em sua análise. Uma pequena tabela que foi particionada poderia ser otimizada, removendo as partições totalmente como isso seria melhora a compactação.                                                                        |
| [row_count_total]                  | Contagem total de linhas da tabela. Por exemplo, você pode usar esse valor para calcular a porcentagem de linhas no estado compactado.                                                                      |
| [row_count_per_distribution_MAX]   | Se todas as linhas são distribuídas uniformemente esse valor seria o número de destino de linhas por distribuição. Compare esse valor com o compressed_rowgroup_count.                                 |
| [COMPRESSED_rowgroup_rows]         | Número total de linhas no formato de columnstore para a tabela.                                                                                                                                 |
| [COMPRESSED_rowgroup_rows_AVG]     | Se o número médio de linhas é significativamente menor que o número máximo de linhas para um grupo de linha, em seguida, considere usar CTAS ou alterar RECRIAR de índice para compactar novamente os dados                     |
| [COMPRESSED_rowgroup_count]        | Número de grupos de linha no formato de columnstore. Se esse número for muito alto em relação a tabela é um indicador que a densidade columnstore é baixa.                                  |
| [COMPRESSED_rowgroup_rows_DELETED] | Linhas são excluídas logicamente no formato de columnstore. Se o número for alto relativo tamanho da tabela, considere recriar a partição ou recriar o índice como isso remove física. |
| [COMPRESSED_rowgroup_rows_MIN]     | Use este junto com as colunas de média e máx entender o intervalo de valores para os grupos de linha em sua columnstore. Um número baixo excede o limite de carregamento (102,400 por partição alinhado à distribuição) sugere que otimizações estão disponíveis no carregamento de dados                                                                                                                                                 |
| [COMPRESSED_rowgroup_rows_MAX]     | Como acima                                                                                                                                                                                  |
| [OPEN_rowgroup_count]              | Os grupos de linha aberto são normais. Um valor esperado razoavelmente um grupo de linha aberto por distribuição de tabela (60). Números excessivos sugerem carregamento em partições de dados. Verifique a estratégia de partição para garantir que ele é som                                                                                                                                                                                                |
| [OPEN_rowgroup_rows]               | Cada grupo de linha pode ter 1.048.576 linhas máximo. Use esse valor para ver como completo os grupos de linha aberto no momento                                                                 |
| [OPEN_rowgroup_rows_MIN]           | Abrir grupos indicam que dados estão sendo está carregados na tabela ou a carga anterior derramado restantes linhas neste grupo de linhas. Use MIN, MAX, colunas AVG para ver a quantidade de dados é sai aberto em grupos de linha. Para tabelas pequenas poderia ser 100% de todos os dados! Nesse caso ALTER índice RECRIAR para forçar os dados para columnstore.                                                                       |
| [OPEN_rowgroup_rows_MAX]           | Como acima                                                                                                                                                                                  |
| [OPEN_rowgroup_rows_AVG]           | Como acima                                                                                                                                                                                  |
| [CLOSED_rowgroup_rows]             | Examine as linhas de grupo de linha fechada como uma verificação de integridade.                                                                                                                                       |
| [CLOSED_rowgroup_count]            | O número de grupos de linha fechada deve ser baixo se qualquer são vistos de forma alguma. Grupos de linha fechada podem ser convertidos em rupo rowg compactado usando o índice ALTER... REORGANISE comando. No entanto, isso normalmente não é necessário. Grupos fechados automaticamente são convertidos em grupos de linhas columnstore pelo processo de "mover tupla" do plano de fundo.                                                                                               |
| [CLOSED_rowgroup_rows_MIN]         | Grupos de linha fechada devem ter uma taxa de preenchimento muito alto. Se a taxa de preenchimento de um grupo de linha fechada for baixa, posterior análise do columnstore é necessário.                                   |
| [CLOSED_rowgroup_rows_MAX]         | Como acima                                                                                                                                                                                  |
| [CLOSED_rowgroup_rows_AVG]         | Como acima                                                                                                                                                                                  |
| [Rebuild_Index_SQL]         | SQL recriar columnstore índice para uma tabela                                                                                                                                                     |

## <a name="causes-of-poor-columnstore-index-quality"></a>Causas de columnstore má qualidade de índice

Se você tiver identificado tabelas com qualidade de segmento má, você desejará identificar a causa raiz.  Estas são algumas causas comuns de má segmento quaility:

1. Pressão de memória quando índice foi criado
2. Alto volume de operações DML
3. Pequeno ou fluir as operações de carregamento
4. Número excessivo de partições

Esses fatores podem causar um índice columnstore ter significativamente menor do que as linhas de 1 milhão ideais por grupo de linhas.  Eles também podem causar linhas ir para o grupo de linhas de delta em vez de um grupo de linhas compactado. 

### <a name="memory-pressure-when-index-was-built"></a>Pressão de memória quando índice foi criado

O número de linhas por grupo de linha compactado estejam diretamente relacionado à largura da linha e a quantidade de memória disponível para processar o grupo de linhas.  Quando linhas são gravadas em tabelas de columnstore sob pressão de memória, pode sofrer columnstore qualidade de segmento.  Portanto, a prática recomendada é dar a sessão que está escrevendo seu acesso de tabelas de índice columnstore a mesma quantidade de memória possível.  Como não há uma troca entre memória e concorrência, a orientação sobre a alocação de memória certo depende dos dados em cada linha da tabela, a quantidade de DWU você tiver alocado ao seu sistema e a quantidade de slots de concorrência que você pode dar à sessão que está gravando dados em sua tabela.  Como prática recomendada, recomendamos começando com xlargerc se você estiver usando DW300 ou menos, largerc se você estiver usando DW400 DW600 e mediumrc se você estiver usando DW1000 e acima.

### <a name="high-volume-of-dml-operations"></a>Alto volume de operações DML

Um alto volume de operações DML que atualizar e excluir linhas pode introduzir ineficiência no columnstore. Isso é especialmente verdadeiro quando a maioria das linhas de um grupo de linhas são modificadas.

- Excluir uma linha de um grupo de linhas compactados apenas logicamente marca a linha como excluído. A linha permanece no grupo compactado linha até que a partição ou tabela é reconstruída.
- Inserir uma linha adiciona a linha a uma tabela de rowstore interno chamada de grupo de linha delta. Linha inserida não é convertida em columnstore até o grupo de linhas de delta está cheio e está marcado como fechado. Grupos de linhas são fechados depois que eles alcançam a capacidade máxima de 1.048.576 linhas. 
- Atualizar uma linha em formato columnstore é processada como uma exclusão lógica e, em seguida, uma inserção. Linha inserida pode ser armazenada no repositório do delta.

Em lote atualização e inserir operações que excedem o limite de massa de 102.400 linhas por partição distribuição alinhada será gravada diretamente para o formato de columnstore. No entanto, supondo que uma distribuição uniforme, você precisa ser modificando mais de 6.144 milhões de linhas em uma única operação para isso acontecer. Se o número de linhas para uma determinada partição alinhado à distribuição é menor que 102,400 as linhas irão para o repositório de delta e permanecerão lá até linhas suficientes foram inseridas ou modificadas para fechar o grupo de linhas ou o índice foi recriado.

### <a name="small-or-trickle-load-operations"></a>Pequeno ou fluir as operações de carregamento

Pequeno carrega que fluxo para SQL Data Warehouse às vezes também são conhecidos como fluir cargas. Eles normalmente representam um fluxo de constante perto de dados sejam incluídos pelo sistema. No entanto, como esse fluxo é quase contínua o volume de linhas não é muito grande. Mais frequentemente que não os dados serão significativamente sob o limite necessário para uma carga direta columnstore formato.

Nessas situações, muitas vezes é melhor será levado os dados primeiro no armazenamento de blob do Microsoft Azure e permita a acumular antes de carregamento. Essa técnica muitas vezes é conhecida como *processamento em lotes micro*.

### <a name="too-many-partitions"></a>Número excessivo de partições

Outra coisa a considerar é o impacto da partição em suas tabelas columnstore agrupada.  Antes de partição, SQL Data Warehouse já divide os dados em 60 bancos de dados.  Ainda mais partição divide seus dados.  Se você partição seus dados, você desejará considerar que **cada** partição precisam ter pelo menos 1 milhão de linhas para usufruir dos benefícios de um índice de columnstore agrupada.  Se você partição sua tabela em 100 partições, em seguida, sua tabela precisam ter pelo menos 6 bilhões de linhas para usufruir dos benefícios de um índice clusterizado columnstore (linhas de *100 partições* 1 milhão de 60 distribuições). Se sua tabela de 100 partição não tem linhas de 6 bilhões, reduza o número de partições ou considere o uso de uma tabela de pilha em vez disso.

Depois de suas tabelas tiverem sido carregadas com alguns dados, siga as etapas a seguir para identificar e reconstruir tabelas com cluster ideal columnstore índices.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Recriar índices para melhorar a qualidade do segmento

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Etapa 1: Identificar ou criar usuário que usa a classe de recurso à direita

Uma maneira rápida de imediatamente melhorar a qualidade de segmento é recriar o índice.  O SQL retornado pelo modo de exibição acima retornará uma instrução ALTER RECRIAR índice que pode ser usada para recriar os índices.  Ao recriar os índices, certifique-se de que você alocar memória suficiente para a sessão que irá recriar o índice.  Para fazer isso, aumente a classe de recurso de um usuário que tem permissões para recriar o índice nessa tabela para o mínimo recomendado.  A classe de recurso do usuário proprietário do banco de dados não pode ser alterada, portanto, se você não tiver criado um usuário no sistema, será necessário fazer isso primeiro.  O mínimo que é recomendável é xlargerc se você estiver usando DW300 ou menos, largerc se você estiver usando DW400 DW600 e mediumrc se você estiver usando DW1000 e acima.

Abaixo está um exemplo de como alocar mais memória a um usuário, aumentando a sua classe de recurso.  Para obter mais informações sobre o recurso de classes e como criar um novo usuário podem ser encontrados no [gerenciamento de concorrência e carga de trabalho] [ Concurrency] artigo.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Etapa 2: Reconstruir índices de columnstore agrupadas com maior usuário de classe do recurso
Faça logon como o usuário da etapa 1 (por exemplo, LoadUser), que é agora usando uma classe superior do recurso e execute as instruções ALTER INDEX.  Certifique-se de que esse usuário tem permissão ALTER a tabelas onde o índice está sendo recriado.  Esses exemplos mostram como recriar o índice inteiro columnstore ou reconstruir uma única partição. Em tabelas grandes, é mais prático para reconstruir índices uma única partição por vez.

Como alternativa, em vez de recriar o índice, você pode copiar a tabela para uma nova tabela usando [CTAS][].  Como é a melhor? Para grandes volumes de dados, [CTAS][] é geralmente mais rápido que [ALTER INDEX][]. Para menores volumes de dados, [ALTER INDEX][] é mais fácil de usar e não exigem que você troque a tabela.  Consulte **índices de reconstrução com CTAS e mudança de partição** abaixo para obter mais detalhes sobre como reconstruir índices com CTAS.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

Recompilar um índice em SQL Data Warehouse é uma operação offline.  Para obter mais informações sobre como recriar índices, consulte a seção alterar RECRIAR índice no [Columnstore índices desfragmentação][] e no tópico de sintaxe [ALTER INDEX][].
 
### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Etapa 3: Confirmar a qualidade de segmento columnstore agrupada foi aprimorada
Executar a consulta de qual tabela identificada com baixa qualidade de segmento e verificar qualidade de segmento tem aprimorado.  Se não melhorar a qualidade de segmento, é possível que as linhas na tabela são muito ampla.  Considere usar uma classe de recurso mais alto ou DWU durante a recriação índices.

 
## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Recriar índices com CTAS e mudança de partição

Este exemplo usa [CTAS][] e partição alternando para recriar uma partição de tabela. 

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Create a SWITCH out table
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 3: Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 4: Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

Para obter mais detalhes sobre como criar novamente partições usando `CTAS`, consulte o artigo de [partição][] .

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os artigos sobre [Tabela visão geral][Visão geral], [Tipos de dados de tabela][Tipos de dados], [Distribuindo uma tabela][Distribuir], [partição uma tabela][partição], [Mantendo estatísticas de tabela][estatísticas] e [Tabelas temporárias][temporário].  Para saber mais sobre as práticas recomendadas, consulte [Práticas recomendadas de depósito de dados do SQL][].

<!--Image references-->

<!--Article references-->
[Visão geral]: ./sql-data-warehouse-tables-overview.md
[Tipos de dados]: ./sql-data-warehouse-tables-data-types.md
[Distribuir]: ./sql-data-warehouse-tables-distribute.md
[Índice]: ./sql-data-warehouse-tables-index.md
[Partição]: ./sql-data-warehouse-tables-partition.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporário]: ./sql-data-warehouse-tables-temporary.md
[Concurrency]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[Práticas recomendadas de depósito de dados do SQL]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[ALTERAR O ÍNDICE]: https://msdn.microsoft.com/library/ms188388.aspx
[pilha]: https://msdn.microsoft.com/library/hh213609.aspx
[índices agrupados e índices não agrupados]: https://msdn.microsoft.com/library/ms190457.aspx
[Criar sintaxe de tabela]: https://msdn.microsoft.com/library/mt203953.aspx
[ColumnStore índices desfragmentação]: https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1
[índices de columnstore agrupada]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
