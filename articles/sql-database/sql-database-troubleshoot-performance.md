<properties
    pageTitle="Dicas de ajuste de desempenho de banco de dados SQL | Microsoft Azure"
    description="Dicas para ajuste no banco de dados do Azure SQL por meio de avaliação e melhorias de desempenho."
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    keywords="SQL ajuste de desempenho, banco de dados ajuste de desempenho, dicas, de ajuste de desempenho de sql ajuste de desempenho de banco de dados do sql"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="sql-database-performance-tuning-tips"></a>Dicas de ajuste de desempenho do banco de dados SQL
Você pode alterar o [nível de serviço](sql-database-service-tiers.md) de um banco de dados ou aumentar o eDTUs de um pool de elástica banco de dados a qualquer momento para melhorar o desempenho, mas talvez você queira identificar oportunidades de melhorar e otimizar o desempenho de consulta primeiro. Índices ausentes e consultas mal otimizadas estão alguns motivos comuns para um desempenho ruim do banco de dados. Este artigo fornece orientação para o banco de dados SQL de ajuste de desempenho.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="steps-to-evaluate-and-tune-database-performance"></a>Etapas para avaliar e ajuste de desempenho de banco de dados
1.  No [Portal do Azure](https://portal.azure.com), clique em **bancos de dados SQL**, selecione o banco de dados e, em seguida, usar o gráfico de monitoramento procure a aproximação o máximo de recursos. Consumo de DTU é exibido por padrão. Clique em **Editar** para alterar o intervalo de tempo e valores mostrados.
2.  Use [Ideias de desempenho de consulta](sql-database-query-performance.md) para avaliar as consultas usando DTUs e, em seguida, use o [Supervisor de banco de dados do SQL](sql-database-advisor.md) para exibir recomendações para a criação e soltando índices, parametrizando consultas e corrigir problemas de esquema.
3.  Você pode usar o gerenciamento dinâmico DMVs (exibições), eventos estendida (Xevents) e o repositório de consulta no SSMS para obter parâmetros de desempenho em tempo real. Consulte o [tópico de orientação de desempenho](sql-database-performance-guidance.md) para monitoramento e dicas de ajuste detalhadas.


    > [AZURE.IMPORTANT] É recomendável que você sempre usar a versão mais recente do Management Studio para permanecer sincronizados com atualizações do Microsoft Azure e banco de dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="steps-to-improve-database-performance-with-more-resources"></a>Etapas para melhorar o desempenho de banco de dados com mais recursos
1.  Bancos de dados único, você pode [alterar os níveis de serviço](sql-database-scale-up.md) sob demanda para melhorar o desempenho do banco de dados.
2.  Para vários bancos de dados, considere usar [pools de banco de dados elástica](sql-database-elastic-pool-guidance.md) dimensionar recursos automaticamente.
