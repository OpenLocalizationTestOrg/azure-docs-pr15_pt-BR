<properties
    pageTitle="Visão geral de alertas no Microsoft Azure | Microsoft Azure"
    description="Alertas permitem que você monitorar métricas de recurso Azure, eventos ou logs e ser notificado quando uma condição que você especificar."
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
    ms.date="09/24/2016"
    ms.author="robb"/>

# <a name="overview-of-alerts-in-microsoft-azure"></a>Visão geral de alertas no Microsoft Azure


Este artigo descreve quais alertas são, seus benefícios e como começar a usá-los.  

## <a name="what-are-alerts"></a>O que são alertas?
Alertas são um método de monitoramento métricas de recurso Azure, eventos, ou logs e então ser notificado quando uma condição que você especificar for atendida.

Você pode receber alertas com base em:

- **Valores métricas**: este alerta aciona quando o valor de uma métrica especificado exceder um limite que você atribui qualquer direção. Ou seja, ele aciona ambos quando a condição é atendida primeiro e, em seguida, posteriormente quando que condição for não está mais sendo atendida.
- **Eventos de log de atividade**: este alerta pode disparar em todos os eventos ou apenas quando ocorre um determinado número de eventos.

## <a name="alerts-in-different-azure-services"></a>Alertas no diferentes serviços do Azure

Alertas estão disponíveis em diferentes serviços, incluindo:

- **Obtenção de informações de aplicativo**: habilita alertas de teste e métrica da web. Consulte [definir alertas no aplicativo ideias](../application-insights/app-insights-alerts.md) e [disponibilidade do Monitor e a capacidade de resposta de qualquer site](../application-insights/app-insights-monitor-web-app-availability.md).
- **Análise de log (pacote de gerenciamento de operações)**: permite que o roteamento de logs de diagnóstico para análise de Log. Pacote de gerenciamento de operações permite métrica, log e outros tipos de alerta. Para obter mais informações, consulte [alertas no Log de análise](../log-analytics/log-analytics-alerts.md).  
- **Monitor de Azure**: habilita alertas com base em valores de métricas e eventos de log de atividade. Monitor Azure inclui a [API do Azure Monitor REST](https://msdn.microsoft.com/library/dn931943.aspx).  Para obter mais informações, consulte [usando o portal do Azure, PowerShell ou a interface de linha para criar alertas](insights-alerts-portal.md).

## <a name="alert-actions"></a>Ações de alerta
Você pode configurar um alerta para fazer o seguinte:

- Envie notificações por email para o administrador do serviço, coadministradores ou para endereços de email adicionais que você especificar.
- Chame um webhook, que permite que você inicie ações de automação adicional.

 ![Alertas explicadas](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)


## <a name="next-steps"></a>Próximas etapas

Obter informações sobre regras de alerta e configurá-los usando:

- [Portal do Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [Interface de linha (comando)](insights-alerts-command-line-interface.md)
- [API REST de Monitor Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)
