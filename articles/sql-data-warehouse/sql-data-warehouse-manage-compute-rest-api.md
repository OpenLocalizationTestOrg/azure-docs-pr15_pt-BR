<properties
   pageTitle="Gerenciar power de computação no Azure SQL dados depósito (REST) | Microsoft Azure"
   description="Tarefas do PowerShell para gerenciar calcular power. Recursos de computação de escala ajustando DWUs. Ou, pausar e continuar recursos de computação para economizar custos."
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

# <a name="manage-compute-power-in-azure-sql-data-warehouse-rest"></a>Gerenciar power de computação no Azure SQL dados depósito (REST)

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTANTE](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Dimensionar o desempenho dimensionando calcular memória para atender a mudança de sua carga de trabalho e recursos. Economizar custos por recursos back dimensionamento durante horários de pico ou computação pausa completamente. 

Este conjunto de tarefas usa o portal do Azure para:

- Computação de escala
- Computação de pausa
- Computação de currículo

Para saber mais sobre isso, consulte [Visão geral de computação de gerenciar][].

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Alimentação de computação de escala

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para alterar os DWUs, use a API REST [criar ou banco de dados de atualização][] . O exemplo a seguir define o objetivo de nível de serviço para DW1000 para o banco de dados MySQLDW que está hospedado no servidor meuservidor. O servidor está em um grupo de recursos Azure denominado ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/MyServer/databases/MySQLDW?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Computação de pausa

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar um banco de dados, use a API REST [Pausar o banco de dados][] . O exemplo a seguir pausa um banco de dados denominado Database02 hospedado em um servidor chamado Server01. O servidor está em um grupo de recursos Azure denominado ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Computação de currículo

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para iniciar um banco de dados, use a API REST de [Banco de dados de currículo][] . O exemplo a seguir inicia um banco de dados denominado Database02 hospedado em um servidor chamado Server01. O servidor está em um grupo de recursos Azure denominado ResourceGroup1. 

```
POST https://management.azure.com/subscriptions{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/resume?api-version=2014-04-01-preview HTTP/1.1
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Próximas etapas

Para outras tarefas de gerenciamento, consulte [Visão geral de gerenciamento][].

<!--Image references-->

<!--Article references-->
[Visão geral do gerenciamento]: ./sql-data-warehouse-overview-manage.md
[Gerenciar a visão geral de computação]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Pausar de banco de dados]: https://msdn.microsoft.com/library/azure/mt718817.aspx
[Banco de dados do currículo]: https://msdn.microsoft.com/library/azure/mt718820.aspx
[Criar ou atualizar o banco de dados]: https://msdn.microsoft.com/library/azure/mt163685.aspx

<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
