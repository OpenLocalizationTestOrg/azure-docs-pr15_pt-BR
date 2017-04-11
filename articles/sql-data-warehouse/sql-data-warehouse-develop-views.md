<properties
   pageTitle="Modos de exibição em SQL Data Warehouse | Microsoft Azure"
   description="Dicas para usar modos de exibição de Transact-SQL no armazém de dados do SQL Azure para desenvolvimento de soluções."
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
   ms.date="07/01/2016"
   ms.author="jrj;barbkess;sonyama"/>


# <a name="views-in-sql-data-warehouse"></a>Modos de exibição no depósito de dados do SQL

Modos de exibição são particularmente úteis em SQL Data Warehouse. Eles podem ser usados em um número de diferentes maneiras de melhorar a qualidade da sua solução.  Este artigo destaca alguns exemplos de como enriquecer sua solução com modos de exibição, bem como as limitações que precisam ser consideradas.

> [AZURE.NOTE] Sintaxe para `CREATE VIEW` não é discutido neste artigo. Consulte o artigo [Criar modo de exibição][] no MSDN para obter essas informações de referência.

## <a name="architectural-abstraction"></a>Abstração arquitetônica
Um padrão de aplicativo muito comum é recriar tabelas usando criar tabela como selecionar (CTAS) seguido de um objeto renomeando padrão durante o carregamento de dados.

O exemplo a seguir adiciona registros de nova data a uma dimensão de data. Observe como uma nova tabble, DimDate_New, é criado pela primeira vez e, em seguida, renomeado para substituir a versão original da tabela.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

No entanto, essa abordagem pode resultar em tabelas que aparece e desaparece do modo de exibição de um usuário, bem como mensagens de erro "a tabela não existe". Modos de exibição podem ser usados para fornecer aos usuários uma camada de apresentação consistente enquanto os objetos subjacentes são renomeados. Fornecendo aos usuários acesso aos dados por meio de modos de exibição, significa que os usuários não precisam ter visibilidade das tabelas base. Isso fornece uma experiência de usuário consistente enquanto garantir que os dados depósitos designers pode evoluir o modelo de dados e maximizar o desempenho usando CTAS durante o processo de carregamento de dados.    

## <a name="performance-optimization"></a>Otimização de desempenho
Modos de exibição também podem ser utilizados para impor associações de desempenho otimizado entre tabelas. Por exemplo, um modo de exibição pode incorporar uma chave de distribuição redundantes como parte dos critérios de ingresso para minimizar o movimento de dados.  Outro benefício de um modo de exibição poderia ser forçar uma consulta específica ou a dica de ingresso. Usando modos de exibição dessa maneira garante que junções sempre são executadas de maneira ideal evitando a necessidade de usuários a lembrar a construção correta para seus junções.

## <a name="limitations"></a>Limitações
Modos de exibição em SQL Data Warehouse são somente metadados.  Consequentemente, as opções a seguir não estão disponíveis:

-   Há nenhuma opção de vinculação de esquema
-   Tabelas base não podem ser atualizadas por meio do modo de exibição
-   Modos de exibição não podem ser criados sobre tabelas temporárias
-   Não há suporte para expandir / dicas NOEXPAND
-   Não há nenhum modos de exibição indexados no depósito de dados do SQL


## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento de depósito de dados do SQL][].
Para `CREATE VIEW` sintaxe, consulte [Criar modo de exibição][].

<!--Image references-->

<!--Article references-->
[Visão geral de desenvolvimento de depósito de dados do SQL]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CRIAR MODO DE EXIBIÇÃO]: https://msdn.microsoft.com/en-us/library/ms187956.aspx

<!--Other Web references-->
