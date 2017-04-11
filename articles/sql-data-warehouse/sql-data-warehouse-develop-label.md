<properties
   pageTitle="Usar rótulos às consultas de instrumentos em SQL Data Warehouse | Microsoft Azure"
   description="Dicas para usar rótulos às consultas de instrumentos do depósito de dados do SQL Azure para desenvolvimento de soluções."
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

# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>Usar rótulos às consultas de instrumentos em SQL Data Warehouse
SQL Data Warehouse suporta um conceito chamado rótulos de consulta. Antes de entrar qualquer profundidade vamos examine um exemplo de um:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Essa última linha marcas a cadeia de caracteres 'Minha consulta rótulo' à consulta. Isso é particularmente úteis como o rótulo é capaz de consulta por meio de DMVs. Isso fornece um mecanismo para rastrear consultas de problema e também para ajudar a identificar o andamento por meio de um tempo de execução ETL.

Uma boa convenção de nomenclatura realmente ajuda aqui. Por exemplo, algo como ' projeto: procedimento: instrução: comentário ' ajudam a identificar exclusivamente a consulta entre todos os códigos no controle de origem.

Para pesquisar por rótulo, você pode usar a seguinte consulta que usa os modos de exibição de gerenciamento dinâmico:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [AZURE.NOTE] É essencial que você quebra colchetes ou aspas duplas em torno do rótulo do word ao consultar. Rótulo é uma palavra reservada e será causou um erro se ele não delimitado.


## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Visão geral de desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
