<properties
   pageTitle="Tipos de dados para tabelas no SQL Data Warehouse | Microsoft Azure"
   description="Guia de Introdução com tipos de dados para tabelas de depósito de dados do SQL Azure."
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
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="data-types-for-tables-in-sql-data-warehouse"></a>Tipos de dados para tabelas no depósito de dados do SQL

> [AZURE.SELECTOR]
- [Visão geral][]
- [Tipos de dados][]
- [Distribuir][]
- [Índice][]
- [Partição][]
- [Estatísticas][]
- [Temporário][]

SQL Data Warehouse suporta os mais frequentemente usados tipos de dados.  Abaixo está uma lista dos tipos de dados compatíveis com o SQL Data Warehouse.  Para obter detalhes adicionais sobre o tipo de dados de suporte, consulte [criar tabela][].

|**Tipos de dados compatíveis**|||
|---|---|---|
[bigint][]|[decimal][]|[smallint][]|
[binário][]|[flutuação][]|[smallmoney][]|
[bit][]|[int][]|[sysname][]|
[CARACT][]|[dinheiro][]|[tempo][]|
[Data][]|[nchar][]|[tinyint][]|
[DateTime][]|[nvarchar][]|[Identificador exclusivo][]|
[datetime2][]|[real][]|[varbinary][]|
[DateTimeOffset][]|[smalldatetime][]|[varchar][]|


## <a name="data-type-best-practices"></a>Práticas recomendadas de tipo de dados

 Ao definir seus tipos de coluna, usando o tipo de dados menor que irá suportar seus dados irá melhorar o desempenho de consulta. Isso é especialmente importante para colunas CARACT e VARCHAR. Se o maior valor em uma coluna é 25 caracteres, então defina sua coluna como varchar (25). Evite definindo todas as colunas de caracteres para um comprimento grande padrão. Além disso, defina colunas como VARCHAR quando é tudo o que é necessário em vez de usar [NVARCHAR][].  Use NVARCHAR(4000) ou VARCHAR(8000) quando possível em vez de nvarchar (max) ou varchar (max).

## <a name="polybase-limitation"></a>Limitação de Polybase

Se você estiver usando Polybase para carregar suas tabelas, defina suas tabelas para que o tamanho máximo de linhas possíveis, incluindo o tamanho total de colunas de comprimento variável, não exceda 32.767 bytes.  Enquanto você pode definir uma linha com dados de comprimento variável que pode exceder esta largura e carregar linhas com BCP, você não poderá usar Polybase para carregar esses dados.  Suporte a Polybase linhas ampla será adicionado em breve.

## <a name="unsupported-data-types"></a>Tipos de dados sem suporte

Se você estiver migrando o banco de dados de outra plataforma SQL como o banco de dados do SQL Azure, como migrar, você poderá encontrar alguns tipos de dados que não são suportados no SQL Data Warehouse.  Abaixo estão os tipos de dados sem suporte, bem como algumas alternativas que você pode usar no lugar de tipos de dados sem suporte.

|Tipo de dados|Solução alternativa|
|---|---|
|[geometria][]|[varbinary][]|
|[Geografia][]|[varbinary][]|
|[HierarchyID][]|[nvarchar][] (4000)|
|[imagem][ntext,text,image]|[varbinary][]|
|[texto][ntext,text,image]|[varchar][]|
|[ntext][ntext,text,image]|[nvarchar][]|
|[sql_variant][]|Dividir coluna em várias colunas acentuadas.|
|[tabela][]|Converta em tabelas temporárias.|
|[carimbo de hora][]|Refazer o código para usar [datetime2][] e `CURRENT_TIMESTAMP` função.  Somente constantes são suportados como padrões, portanto current_timestamp não pode ser definido como uma restrição padrão. Se você precisar migrar valores de versão de linha de uma coluna de carimbo de hora digitado use [binário][](8) ou [VARBINARY][binário](8) para valores de versão de linha não nulo ou nulo.|
|[XML][]|[varchar][]|
|[tipos definidos pelo usuário][]|converter de volta para seus tipos nativos onde possível|
|valores padrão|valores padrão suportam literais e constantes somente.  Expressões não determinante ou funções, tais como `GETDATE()` ou `CURRENT_TIMESTAMP`, não têm suporte.|

O abaixo SQL podem ser executados em atual banco de dados SQL para identificar as colunas não forem aceitas pelo depósito de dados do SQL Azure:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os artigos em [Tabela visão geral][Visão geral], [Distribuindo uma tabela][Distribuir], [indexação de uma tabela de][índice], [partição uma tabela][partição], [Mantendo estatísticas de tabela][estatísticas] e [Tabelas temporárias][temporário].  Para obter mais informações sobre as práticas recomendadas, consulte [Práticas recomendadas de depósito de dados do SQL][].

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

<!--MSDN references-->

<!--Other Web references-->
[Criar tabela]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binário]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[CARACT]: https://msdn.microsoft.com/library/ms176089.aspx
[Data]: https://msdn.microsoft.com/library/bb630352.aspx
[DateTime]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[DateTimeOffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[flutuação]: https://msdn.microsoft.com/library/ms173773.aspx
[geometria]: https://msdn.microsoft.com/library/cc280487.aspx
[Geografia]: https://msdn.microsoft.com/library/cc280766.aspx
[HierarchyID]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[dinheiro]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[real]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[tabela]: https://msdn.microsoft.com/library/ms175010.aspx
[tempo]: https://msdn.microsoft.com/library/bb677243.aspx
[carimbo de hora]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[Identificador exclusivo]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[XML]: https://msdn.microsoft.com/library/ms187339.aspx
[tipos definidos pelo usuário]: https://msdn.microsoft.com/library/ms131694.aspx
