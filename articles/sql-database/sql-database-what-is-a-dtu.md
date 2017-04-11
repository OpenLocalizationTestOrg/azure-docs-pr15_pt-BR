<properties
    pageTitle="Banco de dados do SQL: O que é um DTU? | Microsoft Azure"
    description="Noções básicas sobre quais um Azure SQL Database é unidade de transação."
    keywords="Opções de banco de dados, desempenho de banco de dados"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="CarlRabeler"/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="NA"
    ms.date="09/06/2016"
    ms.author="carlrab"/>

# <a name="explaining-database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Explicando unidades de transação de banco de dados (DTUs) e elástica unidades de transação de banco de dados (eDTUs)

Este artigo explica unidades de transação de banco de dados (DTUs) e elástica unidades de transação de banco de dados (eDTUs) e o que acontece quando você pressionar o máximo DTUs ou eDTUs.  

## <a name="what-are-database-transaction-units-dtus"></a>O que são unidades de transação de banco de dados (DTUs)

Um DTU é uma unidade de medida dos recursos que são certamente estarão disponíveis para um banco de dados do SQL Azure autônoma em um nível de desempenho específico dentro de um [nível de serviço de banco de dados autônomo](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels). Um DTU é uma medida combinada de CPU, memória e dados s e i/o log de transação em um determinado por uma carga de referência OLTP desenvolvida para ser típicas de cargas de trabalho OLTP reais de proporção. Dobrar os DTUs aumentando o nível de desempenho de um banco de dados equivale a dobrar o conjunto de recursos disponíveis para esse banco de dados. Por exemplo, um banco de dados Premium P11 com 1750 DTUs fornece 350 x DTU mais calcular energia que um banco de dados básico com 5 DTUs. Para compreender a metodologia atrás a carga de trabalho de referência OLTP usada para determinar a mistura DTU, consulte [Visão geral de referência de banco de dados SQL](sql-database-benchmark-overview.md).

![Introdução ao banco de dados SQL: único DTUs de banco de dados por nível e nível](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Você pode [alterar os níveis de serviço](sql-database-scale-up.md) a qualquer momento com o tempo de inatividade mínimo ao seu aplicativo (geralmente Calculando a média em quatro segundos). Muitas empresas e aplicativos, sendo possível criar bancos de dados e desempenho de banco de dados único de discagem para cima ou para baixo sob demanda é suficiente, especialmente se os padrões de uso são relativamente previsíveis. Mas se você tiver padrões de uso imprevisíveis, ele pode dificultar a gerenciar custos e seu modelo de negócios. Para esse cenário, você pode usar um pool de Elástico com um determinado número de eDTUs.

## <a name="what-are-elastic-database-transaction-units-edtus"></a>O que são elástica unidades de transação de banco de dados (eDTUs)

Um eDTU é uma unidade de medida do conjunto de recursos (DTUs) que podem ser compartilhados entre um conjunto de bancos de dados em um servidor do SQL Azure - chamado um [pool elástica](sql-database-elastic-pool.png). Grupos de Elástico fornecem uma solução econômica simples para gerenciar as metas de desempenho para vários bancos de dados que têm mais variados e padrões de uso imprevisíveis. Consulte [pools Elástico e níveis de serviço](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) para obter mais informações.

![Introdução ao banco de dados SQL: eDTUs por nível e nível](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Um pool é fornecido um número definido de eDTUs, por um preço definido. Dentro do pool, bancos de dados individuais recebem a flexibilidade para dimensionar automaticamente em definir parâmetros. Sob carga pesada, um banco de dados pode consumir mais eDTUs para atender à demanda. Bancos de dados sob cargas light consumam menor e bancos de dados sem carga consumam sem eDTUs. Provisionamento de recursos para todo o pool, em vez de para bancos de dados único simplifica as tarefas de gerenciamento. Além disso, você tem um orçamento previsível para o pool.

EDTUs adicionais podem ser adicionados a um pool existente com sem tempo de inatividade do banco de dados ou nenhum impacto nos bancos de dados no pool Elástico. Da mesma forma, se eDTUs extras não forem mais necessárias só podem ser removidas de um pool existente em qualquer ponto no tempo. Você pode adicionar ou subtrair bancos de dados ao pool. Se um banco de dados está previsíveis em-utilizando recursos, mova-o check-out.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Como determinar o número de DTUs necessários para minha carga de trabalho?

Se você estiver procurando para migrar um existente local ou carga de máquina virtual do SQL Server banco de dados do Azure SQL, você pode usar a [Calculadora de DTU](http://dtucalculator.azurewebsites.net/) aproximar o número de DTUs necessários. Para uma carga de trabalho do Azure SQL Database existente, você pode usar [Uma visão de desempenho de consulta de banco de dados SQL](sql-database-query-performance.md) para entender o consumo de recursos de banco de dados (DTUs) para obter uma visão mais profunda como otimizar sua carga de trabalho. Você também pode usar o [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) departamento de trânsito para obter as informações de consumo de recurso de última hora. Como alternativa, o modo de exibição de catálogo [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) também podem ser consultados para obter os mesmos dados nos últimos 14 dias, embora em uma fidelidade inferior de médias de cinco minutos.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Como saber se eu poderia se beneficiar um Elástico pool de recursos?

Pools são adequados para um grande número de bancos de dados com padrões de utilização específicos. Para um determinado banco de dados, esse padrão é caracterizado por baixa utilização média com picos de utilização relativamente raros. Banco de dados SQL automaticamente avalia o uso de recursos históricos de bancos de dados em um servidor de banco de dados SQL existente e recomenda a configuração do pool apropriado no portal do Azure. Para obter mais informações, consulte [quando um pool de banco de dados elástica deve ser usado?](sql-database-elastic-pool-guidance.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>O que acontece quando eu acertar meu DTUs máximos

Níveis de desempenho são calibrados e controlados para fornecer os recursos necessários para executar sua carga de trabalho de banco de dados até os limites máximo permitido para o seu nível / desempenho de nível de serviço selecionado. Se sua carga de trabalho é atingir os limites em um dos limites de CPU/dados IO/Log IO, você continuar a receber os recursos no máximo permitido nível, mas provavelmente consulte latências aumento de suas consultas. Esses limites não resultam em todos os erros, mas preferir uma diminuição na carga de trabalho, a menos que a lentidão fica tão grave que consultas começaram intervalo. Se você está atingir limites de máximo de usuários simultâneos permitidos sessões/solicitações (segmentos de trabalho), você verá erros explícitos. Consulte [limites de recursos do Azure SQL Database](sql-database-resource-limits.md) para obter informações sobre o limite de recursos diferente de CPU, memória, dados e/s e i/o log de transação.

## <a name="next-steps"></a>Próximas etapas

- Consulte o [nível de serviço](sql-database-service-tiers.md) para obter informações sobre a DTUs e eDTUs disponíveis para bancos de dados autônomo e Elástico pools.
- Consulte [limites de recursos do Azure SQL Database](sql-database-resource-limits.md) para obter informações sobre o limite de recursos diferente de CPU, memória, dados e/s e i/o log de transação.
- Consulte [Uma visão de desempenho de consulta de banco de dados SQL](sql-database-query-performance.md) para entender o consumo (DTUs).
- Consulte [Visão geral de referência de banco de dados SQL](sql-database-benchmark-overview.md) para entender a metodologia atrás a carga de trabalho de referência OLTP usada para determinar a mistura DTU.