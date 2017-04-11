<properties
    pageTitle="Limites de recursos de banco de dados do SQL Azure"
    description="Esta página descreve alguns limites de recursos comuns do Azure SQL Database."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="10/13/2016"
    ms.author="carlrab" />


# <a name="azure-sql-database-resource-limits"></a>Limites de recursos de banco de dados do SQL Azure

## <a name="overview"></a>Visão geral

Banco de dados do SQL Azure gerencia os recursos disponíveis para um banco de dados usando dois mecanismos diferentes: **Gestão de recursos** e **Imposição de limites**. Este tópico explica essas duas áreas principais do gerenciamento de recursos.

## <a name="resource-governance"></a>Gestão de recursos
Uma das metas de design dos níveis de serviço Basic, Standard e Premium é Azure SQL Database se comporte como se o banco de dados está em execução no seu próprio computador, completamente isolada de outros bancos de dados. Gestão de recurso emula esse comportamento. Se a utilização de recursos agregados atingir o máxima CPU disponível, memória, i/o Log e recursos de dados e/s atribuídos ao banco de dados, governança de recursos será consultas em execução na fila e atribuir recursos às consultas enfileiradas conforme eles liberam.

Como em uma máquina dedicada, utilizar todos os recursos disponíveis resultará em uma execução mais longa de consultas, que podem resultar em tempos limite de comando no cliente em execução no momento. Aplicativos com lógica de repetição rigorosas e aplicativos que executam consultas em banco de dados com uma alta frequência podem encontrar mensagens de erro ao tentar executar novas consultas quando você atingiu o limite de solicitações simultâneas.

### <a name="recommendations"></a>Recomendações:
Monitore a utilização de recursos, bem como os tempos de resposta médio de consultas quando se aproximando a utilização máxima de um banco de dados. Quando se deparar com maiores latências de consulta que você geralmente tem três opções:

1.  Reduza a quantidade de solicitações de entrada no banco de dados para impedir que a pilha para cima de solicitações e tempo limite.

2.  Atribua um nível mais alto de desempenho ao banco de dados.

3.  Otimize consultas para reduzir a utilização de recursos de cada consulta. Para obter mais informações, consulte a seção de ajuste de consulta/Hinting no artigo orientações sobre desempenho de banco de dados de SQL Azure.

## <a name="enforcement-of-limits"></a>Imposição de limites
Recursos diferente de CPU, memória, i/o Log e i/o de dados são aplicados pelo rejeição de novas solicitações quando limites. Os clientes receberão uma [mensagem de erro](sql-database-develop-error-messages.md) dependendo o limite que foi atingido.

Por exemplo, o número de conexões para um banco de dados do SQL, bem como o número de solicitações simultâneas que podem ser processados é restrito. Banco de dados SQL permite que o número de conexões ao banco de dados seja maior que o número de solicitações simultâneas para dar suporte a pool de conexão. Enquanto a quantidade de conexões que estão disponíveis com facilidade pode ser controlada pelo aplicativo, a quantidade de solicitações paralelas costuma ser mais difícil para estimar e controlar vezes. Especialmente durante picos de carga quando o aplicativo seja envia muitas solicitações ou o banco de dados atinge seu recurso limites e inicia acumulando segmentos de trabalho devido a mais consultas em execução, erros podem ser encontrados.

## <a name="service-tiers-and-performance-levels"></a>Níveis de serviço e níveis de desempenho

Há níveis de serviço e níveis de desempenho para ambos os pools de banco de dados e elástica autônomo.

### <a name="standalone-databases"></a>Bancos de dados autônoma

Um banco de dados autônomo, os limites de um banco de dados são definidos pelo nível de desempenho e nível de serviço do banco de dados. A tabela a seguir descreve as características de básica, Standard e Premium bancos de dados em vários níveis de desempenho.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

### <a name="elastic-pools"></a>Pools Elástico

[Pools Elástico](sql-database-elastic-pool.md) compartilhar recursos entre bancos de dados no pool. A tabela a seguir descreve as características de pools de banco de dados elástica Basic, Standard e Premium.

[AZURE.INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Para uma definição expandida de cada recurso listado nas tabelas a anterior, consulte as descrições em [limites e recursos de nível de serviço](sql-database-performance-guidance.md#service-tier-capabilities-and-limits). Para obter uma visão geral dos níveis de serviço, consulte [níveis de serviço de banco de dados do Azure SQL e níveis de desempenho](sql-database-service-tiers.md).

## <a name="other-sql-database-limits"></a>Outros limites de banco de dados SQL

| Área | Limite | Descrição |
|---|---|---|
| Bancos de dados usando automatizada exportar por assinatura | 10 | Exportar automatizada permite que você criar um agendamento personalizado para fazer backup de seus bancos de dados do SQL. Para obter mais informações, consulte [bancos de dados SQL: suporte para automatizada exportações de banco de dados do SQL](http://weblogs.asp.net/scottgu/windows-azure-july-updates-sql-database-traffic-manager-autoscale-virtual-machines).|
| Banco de dados por servidor | Até 5000 | Até 5000 bancos de dados são permitidos por servidor em servidores de V12. |  
| DTUs por servidor | 45000 | 45000 DTUs estão disponíveis por servidor em servidores de V12 para bancos de dados de provisionamento, Elástico pools e depósitos de dados. |



## <a name="resources"></a>Recursos

[Assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md)

[Níveis de desempenho e níveis de serviço de banco de dados do SQL Azure](sql-database-service-tiers.md)

[Mensagens de erro para programas de cliente do banco de dados SQL](sql-database-develop-error-messages.md)
