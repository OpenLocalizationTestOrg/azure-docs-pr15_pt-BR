<properties
   pageTitle="Gerenciar power de computação no Azure SQL dados depósito (REST) | Microsoft Azure"
   description="Tarefas de Transact-SQL (T-SQL) para desempenho de escala-out ajustando DWUs. Economizar custos dimensionando volta durante horários de pico."
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
   ms.date="08/08/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-t-sql"></a>Gerenciar power de computação no depósito de dados do SQL Azure (T-SQL)

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTANTE](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Dimensionar o desempenho dimensionando calcular memória para atender a mudança de sua carga de trabalho e recursos. Economizar custos por recursos back dimensionamento durante horários de pico ou computação pausa completamente. 

Este conjunto de tarefas usa T-SQL para:

- Exibir configurações de DWU atuais
- Recursos de computação de alteração ajustando DWUs

Para pausar ou continuar um banco de dados, escolha uma das outras opções de plataforma na parte superior deste artigo.

Para saber mais sobre isso, consulte [Visão geral de energia de computação de gerenciar][].

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>Exibir configurações de DWU atuais

Para exibir as configurações atuais de DWU para bancos de dados:

1. Abra o Explorador de objeto do SQL Server no Visual Studio 2015.
2. Se conecte ao banco de dados mestre associado ao servidor de banco de dados SQL lógico.
2. Selecione o modo de exibição de gerenciamento dinâmico sys.database_service_objectives. Aqui está um exemplo: 

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Computação de escala

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para alterar os DWUs:


1. Se conecte ao banco de dados mestre associado ao seu servidor de banco de dados SQL lógico.
2. Use a instrução de TSQL [ALTER DATABASE][] . O exemplo a seguir define o objetivo de nível de serviço para DW1000 para o banco de dados MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Próximas etapas

Para outras tarefas de gerenciamento, consulte [Visão geral de gerenciamento][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Visão geral do gerenciamento]: ./sql-data-warehouse-overview-manage.md
[Gerenciar a visão geral de energia de computação]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTERAR BANCO DE DADOS]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
