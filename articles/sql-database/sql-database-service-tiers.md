<properties
    pageTitle="& Opções de desempenho de banco de dados SQL: níveis de serviço | Microsoft Azure"
    description="Compare os recursos do continuidade de negócios e desempenho do banco de dados SQL das camadas serviço equilibrando custo e capacidade ao dimensionar."
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
    ms.workload="data-management"
    ms.date="08/10/2016"
    ms.author="carlrab"/>

# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Opções de banco de dados SQL e desempenho: entender o que está disponível em cada nível de serviço

[Banco de dados do SQL Azure](sql-database-technical-overview.md) oferece três níveis de serviço com vários níveis de desempenho para lidar com cargas de trabalho diferentes. Cada nível de desempenho fornece um conjunto crescente de recursos projetados para fornecer a produtividade de cada vez mais alta. Você pode gerenciar cada banco de dados no seu próprio [nível de serviço](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels) com seu próprio nível de desempenho. Você também pode gerenciar vários bancos de dados em um [pool elástica](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) com um conjunto compartilhado de recursos. Os recursos disponíveis para bancos de dados independente são expressas em termos de unidades de transação de banco de dados (DTUs) e para pools Elástico em termos Elástico DTUs ou eDTUs. Para obter mais informações sobre DTUs e eDTUs, consulte [o que é uma DTU](sql-database-what-is-a-dtu.md). 

Em ambos os casos, os níveis de serviço incluem **básica**, **Standard**e **Premium**. Opções de banco de dados nesses níveis são semelhantes para bancos de dados de autônomo e pools Elástico, mas há outras considerações para pools elástica. Este artigo fornece detalhes de níveis de serviço para bancos de dados de autônomo e Elástico pools.

## <a name="service-tiers-and-database-options"></a>Opções de banco de dados e níveis de serviço
Básico, padrão, e níveis de serviço Premium todos têm um tempo de atividade SLA de 99,99% e oferecem desempenho previsível, opções de continuidade de negócios flexível, recursos de segurança e cobrança por hora. A tabela a seguir fornece exemplos das camadas melhores adequados para cargas de trabalho de aplicativo diferente.

| Nível de serviço | Cargas de trabalho de destino |
|---|---|
| **Básico** | São mais adequados para um banco de dados pequeno, normalmente uma única ativa operação em um determinado momento de suporte. Alguns exemplos incluem bancos de dados usados para desenvolvimento ou teste ou pequena aplicativos usados raramente. |
| **Padrão** | A opção Ir para a maioria dos aplicativos de nuvem, várias consultas simultâneas de suporte. Alguns exemplos incluem aplicativos da web ou grupo de trabalho. |
| **Premium** | Projetado para alto volume de transações, oferecer suporte a vários usuários simultâneos e exigindo o nível mais alto dos recursos de continuidade de negócios. Exemplos são bancos de dados com suporte a aplicativos essenciais. |

>[AZURE.NOTE] Edições da Web e de negócios são desativadas. Leia as [Perguntas Frequentes do pôr do sol](https://azure.microsoft.com/pricing/details/sql-database/web-business/) se você planeja continuar usando edições de negócios e da Web.

## <a name="standalone-database-service-tiers-and-performance-levels"></a>Níveis de serviço de banco de dados autônomo e níveis de desempenho
Bancos de dados independente, há vários níveis de desempenho em cada nível de serviço. Você tem a flexibilidade para escolher o nível que melhor atenda a exigências de sua carga de trabalho. Se você precisar expandir ou reduzir, você pode alterar facilmente as camadas do seu banco de dados. Para obter detalhes, consulte [alterando níveis de serviço de banco de dados e níveis de desempenho](sql-database-scale-up.md) .

Características de desempenho listadas aqui se aplicam aos bancos de dados criados usando [V12 de banco de dados do SQL](sql-database-v12-whats-new.md). Independentemente do número de bancos de dados hospedados, seu banco de dados obtém um conjunto garantido de recursos e as características de desempenho esperado do seu banco de dados não são afetadas.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

>[AZURE.NOTE] Para obter uma explicação detalhada de todas as outras linhas nesta tabela de níveis de serviço, consulte [limites e recursos de nível de serviço](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Níveis de serviço de pool elástica e desempenho em eDTUs
Além de criação e a escala de um banco de dados autônomo, você também tem a opção de gerenciamento de vários bancos de dados em um [pool elástica](sql-database-elastic-pool.md). Todos os bancos de dados em um pool de Elástico compartilham um conjunto comum de recursos. As características de desempenho são medidas por *Elástica unidades de transação de banco de dados* (eDTUs). Como com bancos de dados independente, pools vêm em três níveis de serviço: **básica**, **Standard**e **Premium**. Para pools, esses níveis de serviço de três ainda definem os limites de desempenho geral e diversos recursos.

Pools permitem bancos de dados compartilhar e consumir recursos DTU sem precisar atribuir um nível de desempenho específicas para cada banco de dados no pool. Por exemplo, um banco de dados autônomo em um pool de padrão pode ir de usando eDTUs 0 para o eDTU de banco de dados máximo que configurar quando você configura o pool. Pools permitem vários bancos de dados com variadas cargas de trabalho com eficiência usar eDTU recursos disponíveis para todo o pool. Consulte [Considerações de preço e desempenho para um pool de elástica](sql-database-elastic-pool-guidance.md) para obter detalhes.

A tabela a seguir descreve as características de níveis de serviço de pool.

[AZURE.INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Cada banco de dados dentro de um pool também cumpre as características de banco de dados autônomo para essa camada. Por exemplo, o pool básico tem um limite máximo de sessões por pool de 4800-28800, mas um banco de dados individual dentro de um pool básico tem um limite de banco de dados de 300 sessões.

## <a name="choosing-a-service-tier"></a>Escolhendo um nível de serviço

Para decidir em um nível de serviço, inicie determinando se o banco de dados deve ser um banco de dados autônomo ou ser parte de um pool Elástico. 

### <a name="choosing-a-service-tier-for-a-standalone-database"></a>Escolhendo um nível de serviço para um banco de dados autônomo

Para escolher um nível de serviço para um banco de dados autônomo, inicie determinando os recursos de banco de dados que você precisa escolher a edição de banco de dados SQL:

- Tamanho (2 GB máximo para básico, máximo padrão de 250 GB e 500 GB para máximo de 1 TB para Premium - dependendo do nível de desempenho) de banco de dados
- Período de retenção de backup do banco de dados (7 dias para Basic, 35 dias padrão e 35 dias para Premium)

Depois de ter determinado a edição de banco de dados SQL, você está pronto para determinar o nível de desempenho para o banco de dados (o número de DTUs). Você pode imaginar e então [Dimensionar para cima ou para baixo dinamicamente](sql-database-scale-up.md) com base na experiência real. Você também pode usar a [Calculadora de DTU](http://dtucalculator.azurewebsites.net/) aproximar o número de DTUs necessários. 

### <a name="choosing-a-service-tier-for-an-elastic-database-pool"></a>Escolhendo um nível de serviço para um pool de elástica banco de dados.

Para decidir no nível de serviço para um pool de banco de dados elástica, inicie determinando os recursos de banco de dados que você precisa para escolher o nível de serviço para o pool.

- Tamanho do banco de dados (2 GB para Basic, 250 GB para padrão e 500 GB para Premium)
- Período de retenção de backup do banco de dados (7 dias para Basic, 35 dias padrão e 35 dias para Premium)
- Número de bancos de dados por pool (400 para Basic, 400 padrão e 50 para Premium)
- Máximo de armazenamento por pool (117 GB para básico, 1200 padrão e 750 para Premium)

Depois de determinar o nível de serviço para o pool, você está pronto para determinar o nível de desempenho para o pool (eDTUs). Você pode imaginar e, em seguida, [Expandir ou dimensionar para baixo dinamicamente](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) com base na experiência real. Você pode usar a [Calculadora de DTU](http://dtucalculator.azurewebsites.net/) para aproximar o número de DTUs necessários para cada banco de dados em um pool para ajudar a determinar o limite superior para o pool.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre os preços para esses níveis sobre [Preços de banco de dados do SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Conheça os detalhes de [pools Elástico](sql-database-elastic-pool-guidance.md) e [Considerações de preço e desempenho para pools elástica](sql-database-elastic-pool-guidance.md).
- Saiba como [Monitor, gerenciar e redimensionar Elástico pools](sql-database-elastic-pool-manage-portal.md) e [monitorar o desempenho de bancos de dados independente](sql-database-single-database-monitor.md).
- Agora que você conhece as camadas de banco de dados SQL, experimente-as com uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) e Aprenda [a criar seu primeiro banco de dados do SQL](sql-database-get-started.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Padrões de design para aplicativos SaaS de vários locatários usando o banco de dados do SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
- [Curso de vídeo do Microsoft Virtual Academy nos recursos de banco de dados elástica no banco de dados do SQL Azure](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
