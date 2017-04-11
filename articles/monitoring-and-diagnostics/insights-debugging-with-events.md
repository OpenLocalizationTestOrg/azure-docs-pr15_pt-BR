<properties
    pageTitle="Exibir eventos e logs de auditoria"
    description="Saiba como ver todos os eventos que acontecem em sua assinatura do Azure."
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
    ms.date="04/28/2015"
    ms.author="robb"/>

# <a name="view-events-and-audit-logs"></a>Exibir eventos e logs de auditoria

Todas as operações executadas em recursos Azure são totalmente auditadas pelo Gerenciador de recursos do Azure, desde a criação e exclusões para conceder ou revogar o acesso. Você pode procurar esses logs no portal do Azure e você também pode usar a [API REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) ou o [SDK do .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para acessar o conjunto completo de eventos programaticamente.

## <a name="browse-the-events-impacting-your-azure-subscription"></a>Procure os eventos afetando sua assinatura do Azure

1. Entre [Portal do Azure](https://portal.azure.com/).
2. Clique na **Procurar** e selecione **logs de auditoria**.  
    ![Procurar Hub](./media/insights-debugging-with-events/Insights_Browse.png)
3. Isso abrirá uma lâmina mostrando todos os eventos que têm impacto qualquer uma das suas assinaturas para dos últimos 7 dias. Na parte superior é um gráfico mostrando dados por nível e abaixo disso é a lista completa de logs:  ![todos os eventos](./media/insights-debugging-with-events/Insights_AllEvents.png)

>[AZURE.NOTE] Você só pode exibir os eventos mais recentes 500 para uma assinatura de determinado no portal do Azure.

4. Você pode clicar em qualquer entrada de log para ver os eventos que composto-lo. Por exemplo, quando você implanta algo para um grupo de recursos, muitos recursos diferentes podem ser criados ou modificados. Para cada entrada que você pode ver:
    * O **nível** do evento - por exemplo, ele pode ser algo para acompanhar (**informativo**), ou quando algo deu errado de que você precisa saber sobre (**erro**).
    * O **Status** - o status final geralmente será **êxito** ou **Falha**, mas também pode ser **aceitos** para operações de execução longa.
    * *Quando* o evento ocorreu.
    * *Quem* executou a operação, se qualquer pessoa. Nem todas as operações executadas pelos usuários, algumas são executadas pelos serviços de back-end para que ele não terá um **chamador**.
    * A **ID de correlação** do evento - este é o identificador exclusivo para este conjunto de operações.

5. A partir daí, você pode ir para a lâmina de detalhes para ver os detalhes do evento.

    ![Grupos de recursos](./media/insights-debugging-with-events/Insights_EventDetails.png)

    Para eventos de **Falha** , esta página normalmente inclui um **Substatus** e uma seção de **Propriedades** que incluem detalhes úteis para fins de depuração.

## <a name="filter-to-specific-logs"></a>Filtrar para logs específicos

Para ver os eventos que se aplicam a uma entidade específica ou de um tipo específico, você pode filtrar a lâmina de logs de auditoria clicando no comando de **filtro** . Você também pode usar a lâmina de filtro para alterar o **período de tempo** da lâmina de logs de auditoria.

Quando você clicar este comando, será aberto um novo blade:

![Filtro](./media/insights-debugging-with-events/Insights_EventFilter.png)

Há quatro tipos de filtros:

1. Por assinatura
2. Por um **grupo de recursos**
3. Por um **tipo de recurso**
4. Por um determinado **recurso** - para isso, você deve passaram a *Identificação do recurso* completo do recurso que você está interessado

Além disso, você também pode filtrar eventos por quem executou o evento ou, pelo nível do evento.

Após terminar de escolher o que você deseja ver, clique no botão **Atualizar** na parte inferior da lâmina.

## <a name="monitor-events-impacting-specific-resources"></a>Monitorar eventos afetar recursos específicos

1. Clique em **Procurar** para localizar o recurso que você está interessado. Você também pode ver todos os logs para todo um **grupo de recursos**.
2. Na lâmina do recurso, role para baixo até encontrar o bloco de **eventos** .  
    ![Bloco de eventos](./media/insights-debugging-with-events/Insights_EventsTile.png)
3. Clique na peça para ver os eventos filtrados para apenas o recurso que você selecionou. Você pode usar o comando **filtro** para alterar o intervalo de tempo ou aplicar filtros mais específicos.

## <a name="next-steps"></a>Próximas etapas

* [Receber notificações de alerta](insights-receive-alert-notifications.md) sempre que ocorre um evento.
* [Métricas de serviço do monitor](insights-how-to-customize-monitoring.md) para certificar-se de que seu serviço está disponível e responde.
* [Integridade do serviço de controle](insights-service-health.md) para saber quando Azure passou interrupções de serviço ou degradação de desempenho.  
