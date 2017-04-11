<properties
   pageTitle="Migrar sua solução depósito de dados do SQL | Microsoft Azure"
   description="Guia de migração por trazer sua solução para plataforma de depósito de dados do SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="barbkess;jrj;sonyama"/>

# <a name="migrate-your-solution-to-sql-data-warehouse"></a>Migrar sua solução depósito de dados do SQL

SQL Data Warehouse é um sistema de banco de dados distribuído que se expande consumidor para atender às suas necessidades. Para manter o desempenho e o dimensionamento, nem todos os recursos dentro do SQL Server são implementados dentro de depósito de dados do SQL. Os seguintes tópicos de migração toquem em alguns dos principais fatores para migrar sua solução depósito de dados do SQL. Criar depósitos de dados de escala introduz design diferentes padrões e abordagens tradicionais não são sempre o melhor. Portanto, você descobrirá que adaptar sua solução existente garante que você tire proveito completo da plataforma distribuído fornecido por depósito de dados do SQL.

Também é importante lembrar que SQL Data Warehouse é uma plataforma com base no Microsoft Azure. Portanto parte de sua migração também pode incluir transferindo seus dados para a nuvem. Transferência de dados é um assunto no seu próprio direito e deve ser considerada cuidadosamente; especialmente como aumento de volumes. Transferência de dados e o carregamento de dados são tópicos distintos.

## <a name="migration-guidance"></a>Guia de migração

Verifique se que você tiver leia estes artigos para garantir que você entende algumas das diferenças dos produtos e conceitos fundamentais antes de embarcar em sua migração.

- [Migrar seu esquema][]
- [Migrar seus dados][]
- [Migrar o código][]

## <a name="next-steps"></a>Próximas etapas

O gato (cliente comunicado equipe) também tem algumas diretrizes de SQL Data Warehouse excelentes que eles publicam por meio de blogs.  Dê uma olhada seu artigo, [Migrando dados depósito de dados do SQL Azure na prática][] para obter orientação adicional sobre migração.

<!--Image references-->

<!--Article references-->
[Migrar seu esquema]: sql-data-warehouse-migrate-schema.md
[Migrar seus dados]: sql-data-warehouse-migrate-data.md
[Migrar o código]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->
[Migrando dados depósito de dados do SQL Azure na prática]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
