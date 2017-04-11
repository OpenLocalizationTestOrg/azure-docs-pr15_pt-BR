<properties
   pageTitle="Projetar decisões e técnicas de codificação para desenvolvimento de SQL Data Warehouse | Microsoft Azure"
   description="Conceitos de desenvolvimento, decisões de design, recomendações e técnicas de codificação para depósito de dados do SQL."
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
   ms.date="08/16/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Decisões de design e técnicas de codificação para depósito de dados do SQL

Dê uma olhada estes artigos de desenvolvimento para entender melhor as principais decisões de design, recomendações e técnicas de codificação para depósito de dados do SQL.

## <a name="key-design-decisions"></a>Principais decisões de design
Os artigos a seguir realçam alguns dos conceitos-chave e decisões de design que você precisa compreender para o desenvolvimento de seu depósito de dados distribuídos usando um depósito de dados do SQL:

- [conexões][]
- [concorrência][]
- [transações][]
- [esquemas definidos pelo usuário][]
- [distribuição da tabela][]
- [índices de tabela][]
- [partições de tabela][]
- [CTAS][]
- [estatísticas][]

## <a name="development-recommendations-and-coding-techniques"></a>Técnicas de codificação e recomendações de desenvolvimento
Estes artigos realçar técnicas de codificação específicas, dicas e recomendações para desenvolvimento de seu Data Warehouse SQL:

- [procedimentos armazenados][]
- [rótulos][]
- [Modos de exibição][]
- [tabelas temporárias][]
- [SQL dinâmico][]
- [loop][]
- [Agrupar por opções][]
- [atribuição de variável][]

## <a name="next-steps"></a>Próximas etapas
Depois de você ter sido através os artigos de desenvolvimento dar uma olhada através da página de [referência Transact-SQL][] para obter mais detalhes sobre a sintaxe com suporte para depósito de dados do SQL.

<!--Image references-->

<!--Article references-->
[concorrência]: ./sql-data-warehouse-develop-concurrency.md
[conexões]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL dinâmico]: ./sql-data-warehouse-develop-dynamic-sql.md
[Agrupar por opções]: ./sql-data-warehouse-develop-group-by-options.md
[rótulos]: ./sql-data-warehouse-develop-label.md
[loop]: ./sql-data-warehouse-develop-loops.md
[estatísticas]: ./sql-data-warehouse-tables-statistics.md
[procedimentos armazenados]: ./sql-data-warehouse-develop-stored-procedures.md
[distribuição da tabela]: ./sql-data-warehouse-tables-distribute.md
[índices de tabela]: ./sql-data-warehouse-tables-index.md
[partições de tabela]: ./sql-data-warehouse-tables-partition.md
[tabelas temporárias]: ./sql-data-warehouse-tables-temporary.md
[transações]: ./sql-data-warehouse-develop-transactions.md
[esquemas definidos pelo usuário]: ./sql-data-warehouse-develop-user-defined-schemas.md
[atribuição de variável]: ./sql-data-warehouse-develop-variable-assignment.md
[Modos de exibição]: ./sql-data-warehouse-develop-views.md
[Referência Transact-SQL]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
