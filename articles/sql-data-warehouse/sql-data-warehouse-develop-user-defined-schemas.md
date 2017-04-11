<properties
   pageTitle="Esquemas definidos pelo usuário no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para usar esquemas de Transact-SQL no armazém de dados do SQL Azure para desenvolvimento de soluções."
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

# <a name="user-defined-schemas-in-sql-data-warehouse"></a>Esquemas definidos pelo usuário no depósito de dados do SQL

Depósitos de dados tradicionais geralmente usam bancos de dados separados para criar limites de aplicativo com base na carga de trabalho, domínio ou segurança. Por exemplo, um depósito de dados do SQL Server tradicional pode conter um banco de dados temporário, um banco de dados de depósito de dados e alguns bancos de dados de armazém de dados. Nessa topologia cada banco de dados funciona como uma carga de trabalho e o limite de segurança na arquitetura.

Por outro lado, o SQL Data Warehouse executa a carga de trabalho de depósito de dados inteiro dentro de um banco de dados. Junções não são permitidas entre banco de dados. Portanto SQL Data Warehouse espera todas as tabelas usadas pelo depósito para estar armazenados em um banco de dados.

> [AZURE.NOTE] SQL Data Warehouse não oferece suporte para consultas de banco de dados cruzada de qualquer tipo. Consequentemente, implementações de data warehouse que utilizam este padrão precisa ser revisada.

## <a name="recommendations"></a>Recomendações

Estas são recomendações para consolidar cargas de trabalho, segurança, domínio e limites funcionais usando esquemas definidos pelo usuário

1. Usar um banco de dados do SQL Data Warehouse para executar sua carga de trabalho de depósito de dados inteiro
2. Consolidar seu ambiente de data warehouse existente para usar um banco de dados do SQL Data Warehouse
3. Aproveite **esquemas definidos pelo usuário** para fornecer o limite anteriormente implementado usando bancos de dados.

Se esquemas definidos pelo usuário não foram usados anteriormente, em seguida, você tem zero. Simplesmente use o nome do banco de dados antigo como base para seu esquemas definidos pelo usuário do banco de dados do SQL Data Warehouse.

Se os esquemas já foram usados você tem algumas opções:

1. Remover os nomes de esquema herdados e começar do zero
2. Mantêm os nomes de esquema herdados por previamente pendente no nome de esquema legados para o nome da tabela
3. Mantêm os nomes de esquema herdados implementando exibições sobre a tabela em um esquema extra para recriar a estrutura do esquema antigo.

> [AZURE.NOTE] Na primeira inspeção opção 3 pode parecer a opção mais atraente. No entanto, o diabo é o detalhadamente. Modos de exibição são somente leitura na SQL Data Warehouse. Modificações dados ou tabela precisa ser executada em relação a tabela base. Opção 3 também apresenta uma camada de modos de exibição no seu sistema. Talvez você queira dar alguns pensamento adicional se você já está usando modos de exibição em sua arquitetura.


### <a name="examples"></a>Exemplos:

Implementar esquemas definidos pelo usuário com base nos nomes de banco de dados

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Reter nomes de esquema herdados por previamente pendente-los ao nome da tabela. Use esquemas para o limite de carga de trabalho.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Manter nomes de esquema herdados usando modos de exibição

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [AZURE.NOTE] Qualquer alteração na estratégia de esquema precisa uma revisão do modelo de segurança para o banco de dados. Em muitos casos, você pode ser capaz de simplificar o modelo de segurança atribuindo permissões no nível do esquema. Se são necessárias permissões mais granulares, você pode usar funções de banco de dados.

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Visão geral de desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
