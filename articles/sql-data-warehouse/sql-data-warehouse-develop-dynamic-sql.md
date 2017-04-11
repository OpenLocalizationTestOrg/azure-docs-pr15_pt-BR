<properties
   pageTitle="SQL dinâmico em SQL Data Warehouse | Microsoft Azure"
   description="Dicas para usar SQL dinâmico do depósito de dados do SQL Azure para desenvolvimento de soluções."
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
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL dinâmico no depósito de dados do SQL
Ao desenvolver o código do aplicativo para o SQL Data Warehouse você talvez precise usar sql dinâmico para ajudar a fornecer soluções flexíveis, genéricas e modulares. SQL Data Warehouse não oferece suporte a tipos de dados de blob neste momento. Isso pode limitar o tamanho de seu cadeias de caracteres como tipos de blob incluem tipos varchar e nvarchar (max). Se você usou esses tipos no seu código de aplicativo ao criar cadeias de caracteres muito grandes, você precisará quebrar o código em blocos e em vez disso, use a instrução executivo.

Um exemplo simples:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se a cadeia de caracteres for curta que você pode usar [sp_executesql][] normalmente.

> [AZURE.NOTE] Declarações executadas como SQL dinâmico ainda estarão sujeitos a todas as regras de validação de TSQL.

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Visão geral de desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->
