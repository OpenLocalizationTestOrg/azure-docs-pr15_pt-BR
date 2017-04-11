<properties
    pageTitle="Monitoramento do desempenho de banco de dados no banco de dados do SQL Azure | Microsoft Azure"
    description="Saiba mais sobre as opções para monitorar seu banco de dados com ferramentas Azure e exibições de gerenciamento dinâmico."
    keywords="monitoramento, desempenho de banco de dados de nuvem do banco de dados"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="09/27/2016"
    ms.author="carlrab"/>

# <a name="monitoring-database-performance-in-azure-sql-database"></a>Monitoramento do desempenho de banco de dados no banco de dados do SQL Azure
Monitorar o desempenho de um banco de dados SQL Azure começa com monitoramento a utilização de recursos em relação o nível de desempenho de banco de dados que você escolher. Monitorando ajuda a que determinar se seu banco de dados possui capacidade excedente ou está tendo problemas porque recursos estão esgotando e decida se é hora para ajustar o nível de desempenho e o [nível de serviço](sql-database-service-tiers.md) do seu banco de dados. Você pode monitorar o seu banco de dados usando ferramentas gráficas no [portal do Azure](https://portal.azure.com) ou [exibições de gerenciamento dinâmico](https://msdn.microsoft.com/library/ms188754.aspx)do SQL.

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorar bancos de dados usando o portal do Azure

No [portal do Azure](https://portal.azure.com/), você pode monitorar a utilização de um único banco de dados selecionando seu banco de dados e clicando no gráfico de **monitoramento** . Isso abre uma janela de **métrica** que você pode alterar clicando no botão **Editar gráfico** . Adicione as seguintes métricas:

- Porcentagem de CPU
- Porcentagem DTU
- Porcentagem de IO de dados
- Porcentagem de tamanho de banco de dados

Depois que você adicionou essas métricas, você pode continuar para exibi-los no gráfico de **monitoramento** com mais detalhes na janela de **métrica** . Todas as quatro métricas mostram a porcentagem de utilização média em relação a **DTU** do seu banco de dados. Consulte o artigo de [níveis de serviço](sql-database-service-tiers.md) para obter detalhes sobre DTUs.

![Serviço camada monitoramento do desempenho de banco de dados.](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

Você também pode configurar alertas em métricas de desempenho. Clique no botão **Adicionar alerta** na janela **métrica** . Siga o Assistente para configurar o alerta. Você tem a opção de alerta, se as métricas excedam um determinado limite ou se a métrica cai abaixo de um determinado limite.

Por exemplo, se você espera a carga de trabalho no seu banco de dados para crescer, opte por configurar um alerta de email sempre que seu banco de dados atinge 80% em qualquer uma das métricas de desempenho. Você pode usar isso como um aviso antecipado para descobrir quando você talvez precise alternar para o próximo nível mais alto de desempenho.

As métricas de desempenho também podem ajudá-lo a determinar se você é capaz de fazer downgrade para um nível de desempenho inferior. Suponha que você estiver usando um banco de dados padrão S2 e todas as avaliações de desempenho mostram que o banco de dados em média não use mais de 10% a qualquer momento. É provável que o banco de dados funcionará bem em S1 padrão. No entanto, lembre-se das cargas de trabalho que aumente ou flutuar antes de tomar a decisão de mover para um nível de desempenho inferior.

## <a name="monitor-databases-using-dmvs"></a>Bancos de dados do monitor usando DMVs

As mesmas medições estão expostas no portal também estão disponíveis por meio de exibições do sistema: [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) no banco de dados lógico **mestre** do seu servidor e [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) do banco de dados do usuário. Use **sys.resource_stats** se você precisar monitorar dados menos granulares em um período de tempo maior. Use **sys.dm_db_resource_stats** se você precisar monitorar dados mais granulares em um período menor. Para obter mais informações, consulte [A orientação de desempenho de banco de dados do SQL Azure](sql-database-performance-guidance.md#monitoring-resource-use-with-sysresourcestats).

>[AZURE.NOTE] **Sys.dm_db_resource_stats** retorna um resultado vazio definido quando usada em Web e Business edition bancos de dados que são removidos.

Para pools de elástica banco de dados, você pode monitorar bancos de dados individuais no pool com as técnicas descritas nesta seção. Mas você também pode monitorar o pool como um todo. Para obter informações, consulte [Monitor e gerenciar um pool de banco de dados elástica](sql-database-elastic-pool-manage-portal.md).
