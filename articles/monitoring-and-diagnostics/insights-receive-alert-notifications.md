<properties
    pageTitle="Receber notificações de alerta para serviços do Azure | Microsoft Azure"
    description="Ser notificado quando as regras de alerta condições forem atendidas."
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
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="receive-alert-notifications"></a>Receber notificações de alerta

Você pode receber um alerta com base em métricas de monitoramento para ou eventos em, seus serviços Azure.

Para uma regra de alerta em um valor métrico, quando o valor de uma métrica especificado exceder um limite atribuído, a regra de alerta fica ativa e pode enviar uma notificação. Para uma regra de alerta eventos, uma regra pode enviar uma notificação em *todos os* eventos ou, somente quando ocorrer um determinado número de eventos.

Quando você cria uma regra de alerta, você pode selecionar opções para enviar uma notificação por email para o administrador do serviço e coadministradores ou para outro administrador que você pode especificar. Um email de notificação será enviado quando a regra se torna ativa e quando uma condição de alerta é resolvida.

Você pode usar a [API REST](https://msdn.microsoft.com/library/azure/dn931945.aspx) ou [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para configurar e obter informações sobre regras de alerta por programação.

## <a name="create-an-alert-rule"></a>Criar uma regra de alerta

1. No [portal](https://portal.azure.com/), clique em **Procurar**e, em seguida, um recurso que você está interessado monitoramento.

2. Clique no bloco **regras de alerta** nas lentes de **operações** .

3. Clique no comando de **alerta de adicionar** .

    ![Adicionar alerta](./media/insights-receive-alert-notifications/Insights_AddAlert.png)

4. Você pode nomear sua regra de alerta e escolha uma descrição que aparecerá no email de notificação.

5. Quando você seleciona **métricas** você vai escolher uma condição e o limite de valor para a métrica. Este é o período de tempo que o Azure usa a atividade de alerta monitor e plotagem.

    ![Condição e limite](./media/insights-receive-alert-notifications/Insights_ConditionAndThreshold.png)

6. Você também pode escolher **eventos**e recebe uma notificação quando um determinado evento acontece.

    ![Eventos](./media/insights-receive-alert-notifications/Insights_Events.png)

7. Por fim, você pode optar por enviar notificação por email para administradores responsáveis.

Depois de clicar em **Salvar**, em poucos minutos será informado sempre que a métrica que você escolher excede o limite.

## <a name="managing-your-alert-rules"></a>Gerenciando suas regras de alerta

Quando você tiver criado uma regra de alerta, você pode exibir uma visualização do seu alerta limite comparada a métrica do dia anterior.

![Eventos](./media/insights-receive-alert-notifications/Insights_EditAlert.png)


Claro, você pode editar esta regra de alerta e **Habilitar** ou **desabilitá** -lo se desejar temporariamente parar de receber notificações sobre ele.

## <a name="next-steps"></a>Próximas etapas

* [Configurar webhooks em seus alertas](insights-webhooks-alerts.md) para notificações de rota de canais
* [Métricas de serviço do monitor](insights-how-to-customize-monitoring.md) para certificar-se de que seu serviço está disponível e responde.
* [Habilitar o monitoramento e diagnóstico](insights-how-to-use-diagnostics.md) para coletar métricas de alta frequência detalhadas no seu serviço.
* [Disponibilidade do monitor e a capacidade de resposta de qualquer página da web](../application-insights/app-insights-monitor-web-app-availability.md) com ideias de aplicativo para que você pode descobrir se a página está inoperante.
* [Monitorar o desempenho do aplicativo](../application-insights/app-insights-azure-web-apps.md) se você quiser entender exatamente como seu código é o desempenho na nuvem.
* [Exibir eventos e logs de auditoria](insights-debugging-with-events.md) para saber tudo o que aconteceu no seu serviço.
* [Integridade do serviço de controle](insights-service-health.md) para saber quando Azure passou interrupções de serviço ou degradação de desempenho.
