<properties
    pageTitle="Acompanhar a integridade do serviço Azure usando Logs de atividade do Azure Monitor | Microsoft Azure"
    description="Descubra quando Azure passou interrupções de serviço ou degradação de desempenho. "
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="robb"/>

# <a name="track-azure-service-health-using-azure-monitor-activity-logs"></a>Acompanhar a integridade do serviço Azure usando Logs de atividades de Monitor do Azure

Azure publicizes sempre que não há uma degradação de desempenho ou interrupção do serviço. Você pode procurar esses eventos no portal do Azure e você também pode usar a [API REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) ou o [SDK do .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para acessar o conjunto completo de eventos programaticamente.

## <a name="browse-the-service-health-logs-for-your-subscription"></a>Procure os logs de integridade do serviço para sua assinatura

1. Entre [Portal do Azure](https://portal.azure.com/).

2. Na **página inicial** , você verá um bloco chamado **integridade do serviço**. Clique nela.

    ![Casa](./media/insights-service-health/Insights_Home.png)

3. Você verá uma lista de todas as regiões no Azure. Clique em qualquer região para exibir a Log de atividade de consulta que mostre ocorrências de serviço que têm impacto sobre qualquer uma das suas assinaturas nas últimas 24 horas.

    ![Integridade do serviço de assinatura de Log de atividade](./media/insights-service-health/AzureActivityLogServiceHealth3.png)

4. Você pode ver os detalhes de um incidente individual clicando esse evento na tabela.

5. Altere o **período de tempo** para ver um período mais longo.

## <a name="next-steps"></a>Próximas etapas

* [Disponibilidade do monitor e a capacidade de resposta de qualquer página da web](../application-insights/app-insights-monitor-web-app-availability.md) com ideias de aplicativo para que você pode descobrir se a página está inoperante.
