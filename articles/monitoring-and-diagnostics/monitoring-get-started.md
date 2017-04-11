<properties
    pageTitle="Introdução ao Azure Monitor | Microsoft Azure"
    description="Começar a usar o Monitor do Azure percepção a operação de seus recursos e agir com base de dados."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="johnkem"/>

# <a name="get-started-with-azure-monitor"></a>Introdução ao Azure Monitor

Monitor Azure é o serviço de plataforma que fornece uma única fonte para monitoramento dos recursos do Azure. Com o Azure Monitor, você pode visualizar, da consulta, rotear, arquivar e agir sobre os logs provenientes de recursos no Azure e métricas. Você pode trabalhar com esses dados usando a lâmina de portal Monitor, [Monitor Cmdlets do PowerShell](./insights-powershell-samples.md), [Entre plataformas CLI](insights-cli-samples.md)ou [APIs REST do Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx). Neste artigo, vamos percorrer alguns dos componentes principais do Monitor do Azure, usando o portal para demonstração.

1. No portal do, navegue até **mais serviços** e encontrar a opção de **Monitor** . Clique no ícone de estrela para adicionar esta opção à sua lista de Favoritos para que ele fique sempre facilmente acessível da barra de navegação à esquerda.

    ![Monitorar na lista de serviços](./media/monitoring-get-started/monitor-more-services.png)

2. Clique na opção de **Monitor** para abrir a lâmina **Monitor** . Este blade reúne todas as suas configurações de monitoramento e dados em um modo de exibição consolidado. Primeiro, ele abre a seção de **registro de atividades** .

    ![Navegação de blade do monitor](./media/monitoring-get-started/monitor-blade-nav.png)

    > [AZURE.WARNING] As opções de **notificações de serviço** e **grupos de notificação** exibidas acima só aparecerá para aqueles que ingressaram a visualização particular desses recursos.

    Monitor Azure tem três categorias básicas de monitoramento de dados: O **log de atividade**, **métricas**e **logs de diagnóstico**.

3. Clique em **log de atividade** para garantir que a seção de registro de atividade é exibida.

    ![Blade de Log de atividade](./media/monitoring-get-started/monitor-act-log-blade.png)

    O [**log de atividade**](./monitoring-overview-activity-logs.md) descreve todas as operações executadas em recursos em sua assinatura. Usando o Log de atividades, você pode determinar a ', quem e quando ' para qualquer criar, atualizar ou excluir operações em recursos em sua assinatura. Por exemplo, o Log de atividade informa quando um aplicativo web foi interrompido e quem parou. Eventos de Log de atividade são armazenados na plataforma e disponíveis para consultar 90 dias.
   
    Você pode criar e salvar consultas para os filtros comuns e fixar as consultas mais importantes a um painel portal para que você sempre saiba se ocorreram eventos que atendem aos critérios.

4. Filtrar a exibição a um grupo de recursos específico sobre semana passada, em seguida, clique no botão **Salvar** .

    ![Salvar a consulta de log de atividade](./media/monitoring-get-started/monitor-act-log-save.png)

5. Agora, clique no botão **Fixar** .

    ![Clique em fixar para log de atividade](./media/monitoring-get-started/monitor-act-log-pin.png)

    A maioria das exibições nesta explicação pode ser fixada a um painel. Isso ajuda você a criar uma única fonte de informações de dados operacionais em seus serviços. 

6. Retorne ao seu painel. Agora você pode ver que a consulta (e o número de resultados) são exibidos no seu painel. Isso é útil se você quiser ver rapidamente as ações de perfil alto que ocorreram recentemente em sua assinatura, por exemplo. uma nova função foi atribuída ou uma máquina virtual foi excluída.

    ![Registro de atividade fixado dashboard](./media/monitoring-get-started/monitor-act-log-db.png)

7. Retornar para o bloco de **Monitor** e clique na seção de **métricas** . Primeiro é necessário selecionar um recurso de filtragem e selecionando usando o menu suspenso opções na parte superior da lâmina.

    ![Recurso de filtro para métricas](./media/monitoring-get-started/monitor-met-filter.png)

    Todos os recursos Azure emitem [**métricas**](./monitoring-overview-metrics.md). Este modo de exibição une todas as métricas em um único painel facilmente compreender como os recursos são executados.

8. Uma vez que você selecionou um recurso, todas as métricas disponíveis aparecem no lado esquerdo da lâmina. Você pode várias métricas de gráfico ao mesmo tempo, selecionando métricas e modificar o intervalo de tempo e de tipo do gráfico. Você também pode exibir todos os alertas de métrica definir este recurso.

    ![Blade métrica](./media/monitoring-get-started/monitor-metric-blade.png)

    > [AZURE.NOTE] Algumas métricas estão disponíveis apenas habilitando [Ideias de aplicativo](../application-insights/app-insights-overview.md) e/ou Windows ou Linux diagnóstico do Azure em seu recurso.

9. Quando você estiver satisfeito com seu gráfico, você pode usar o botão de **fixação** para fixá-lo ao seu painel.

10. Retornar para a lâmina **Monitor** e clique em **logs de diagnóstico**.

    ![Blade de logs de diagnóstico](./media/monitoring-get-started/monitor-diaglogs-blade.png)

    [**Logs de diagnóstico**](monitoring-overview-of-diagnostic-logs.md) estão logs emitidos *por* um recurso que fornecem dados sobre a operação de determinado recurso. Por exemplo, contadores de regra de grupo de segurança de rede e Logs de fluxo de trabalho do aplicativo de lógica são os dois tipos de logs de diagnóstico. Esses logs podem ser armazenados em uma conta de armazenamento, transmitidos a um Hub de eventos e/ou enviados para [A análise de Log](../log-analytics/log-analytics-overview.md). Análise de log é produto de inteligência de dados operacionais da Microsoft para pesquisa avançada e alertas.
   
    No portal, você pode exibir e filtrar uma lista de todos os recursos em sua assinatura para identificar se ele tiver ativados os logs de diagnóstico.

11. Clique em um recurso na lâmina logs de diagnóstico. Se os logs de diagnóstico estão sendo armazenados em uma conta de armazenamento, você verá uma lista de registros por hora que você pode baixar diretamente.

    ![Logs de diagnóstico para um recurso](./media/monitoring-get-started/monitor-diaglogs-detail.png)

    Você também pode clicar em **Configurações de diagnóstico**, que permite que você configurar ou modificar as configurações de arquivamento para uma conta de armazenamento, streaming aos Hubs de evento ou enviando para um espaço de trabalho de análise de Log.

    ![Ativar logs de diagnóstico](./media/monitoring-get-started/monitor-diaglogs-enable.png)

    Se você configurou os logs de diagnóstico para análise de Log, você pode pesquisá-las na seção **pesquisa de Log** do portal.

12. Navegue até a seção de **alertas** da lâmina Monitor.

    ![lâmina de alertas para o público](./media/monitoring-get-started/monitor-alerts-nopp.png)

    Aqui você pode gerenciar todos os [**alertas**](./monitoring-overview-alerts.md) sobre os recursos do Azure. Isso inclui alertas em métricas, eventos de log de atividade (no modo de visualização particular), testes da web de obtenção de informações de aplicativo (locais) e diagnóstico proativo de obtenção de informações do aplicativo. Alertas podem acionar um email seja enviado ou um HTTP POST para uma URL de webhook.
   
13. Clique em **Adicionar alerta métrica** para criar um alerta.

    ![Adicionar alerta métrica](./media/monitoring-get-started/monitor-alerts-add.png)

    Em seguida, você pode fixar um alerta ao seu painel para ver facilmente seu estado a qualquer momento.

14. A seção de Monitor também inclui links para aplicativos de [Obtenção de informações de aplicativo](../application-insights/app-insights-overview.md) e soluções de gerenciamento de [Análise de Log](../log-analytics/log-analytics-overview.md) . Esses outros produtos da Microsoft têm profunda integração com o Monitor do Azure.

15. Se você não estiver usando o aplicativo ideias ou a análise de Log, chances são que Azure Monitor tem uma parceria com log de monitoramento, atual e produtos de alertas. Consulte nossa [página de parceiros](./monitoring-partners.md) para obter uma lista completa e instruções sobre como integrar.

Seguindo essas etapas e fixação de todos os blocos pertinentes a um painel, você pode criar modos de exibição abrangentes de seu aplicativo e infraestrutura como esta:

![Painel de Monitor Azure](./media/monitoring-get-started/monitor-final-dash.png)

## <a name="next-steps"></a>Próximas etapas
- Leia a [Visão geral do Monitor do Azure](./monitoring-overview.md)
