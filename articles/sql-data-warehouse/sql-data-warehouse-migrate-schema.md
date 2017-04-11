<properties
   pageTitle="Migrar seu esquema depósito de dados do SQL | Microsoft Azure"
   description="Dicas para migrar seu esquema depósito de dados do SQL Azure para desenvolvimento de soluções."
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
   ms.date="08/25/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="migrate-your-schema-to-sql-data-warehouse"></a>Migrar seu esquema depósito de dados do SQL#

Os seguintes resumos ajudarão-lo a compreender as diferenças entre o SQL Server e SQL Data Warehouse para ajudá-lo a migrar seu banco de dados.

## <a name="table-migration"></a>Migração de tabela

Ao migrar suas tabelas, você desejará familiarizar-se com os recursos de tabela de tabelas do SQL Data Warehouse.  A [tabela visão geral][] é um ótimo lugar para começar.  Este artigo apresenta as mais importantes considerações ao criar uma tabela como estatísticas de tabela, distribuição, partição e indexação.  Ela também aborda alguns [recursos de tabela sem suporte][] e soluções alternativas.

SQL Data Warehouse suporta os tipos de dados de negócios comuns.  Consulte o artigo de [tipos de dados][] para uma lista de suporte e [tipos de dados sem suporte][].  O artigo de [tipos de dados][] também contém uma consulta para identificar [os tipos de dados sem suporte][].  Ao converter os tipos de dados, certifique-se de examinar as [práticas recomendadas de tipo de dados][].

## <a name="next-steps"></a>Próximas etapas
Após você ter migradas com êxito seu esquema de banco de dados depósito de dados do SQL, vá para um dos seguintes artigos:

- [Migrar seus dados][]
- [Migrar o código][]

Para obter mais informações sobre as práticas recomendadas de depósito de dados do SQL, consulte o artigo de [práticas recomendadas][] .

<!--Image references-->

<!--Article references-->
[Migrar o código]: ./sql-data-warehouse-migrate-code.md
[Migrar seus dados]: ./sql-data-warehouse-migrate-data.md
[práticas recomendadas]: ./sql-data-warehouse-best-practices.md
[Visão geral de tabela]: ./sql-data-warehouse-tables-overview.md
[recursos de tabela sem suporte]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[tipos de dados]: ./sql-data-warehouse-tables-data-types.md
[tipos de dados sem suporte]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[práticas recomendadas de tipo de dados]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->
