<properties
    pageTitle="Usar o portal do Azure para criar alertas para os serviços do Azure | Microsoft Azure"
    description="Use o portal do Azure para criar alertas Azure, que podem disparar notificações ou automação quando as condições especificadas forem atendidas."
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
    ms.date="09/23/2016"
    ms.author="robb"/>

# <a name="use-azure-portal-to-create-alerts-for-azure-services"></a>Usar o portal do Azure para criar alertas para os serviços do Azure

> [AZURE.SELECTOR]
- [Portal](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Visão geral

Este artigo mostra como configurar alertas Azure usando o portal do Azure.   

Você pode receber um alerta com base em métricas de monitoramento para ou eventos em, seus serviços Azure.

- **Valores métricas** - os disparadores alertas quando o valor de uma métrica especificado exceder um limite que atribuir em qualquer direção. Ou seja, ele aciona ambos quando a condição é atendida primeiro e, em seguida, posteriormente quando que condição for não está mais sendo atendida.    
- **Eventos de log de atividade** - um alerta pode disparar em *todos os* eventos ou, somente quando ocorre um determinado número de eventos.


Você pode configurar um alerta para fazer o seguinte quando ele disparar:

- enviar notificações por email para o administrador do serviço e coadministradores
- Envie email emails adicionais que você especificar.
- chamar um webhook
- Iniciar a execução de um runbook Azure (apenas do portal do Azure)

Você pode configurar e obter informações sobre o uso de regras de alerta

- [Portal do Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [interface de linha (comando)](insights-alerts-command-line-interface.md)
- [API REST de Monitor Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)


## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Criar uma regra de alerta em uma métrica com o portal do Azure

1. No [portal](https://portal.azure.com/), localize o recurso que você está interessado em monitoramento e selecioná-lo.

2. Selecione **alertas** ou **regras de alerta** sob a seção de monitoramento. O texto e o ícone podem variar ligeiramente diferentes recursos.  

    ![Monitoramento](./media/insights-alerts-portal/AlertRulesButton.png)


3. Selecione o comando **Adicionar alerta** e preencha os campos.

    ![Adicionar alerta](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. O **nome do** seu alerta de regra e, em seguida, escolha uma **Descrição**, que também mostra em emails de notificação.
5. Selecione a **métrica** que você deseja monitorar, em seguida, escolha um valor de **condição** e **limite** para a métrica. Também escolheu o **período** de tempo que a regra métrica deve ser obedecida antes dos disparadores alertas. Por exemplo, se você usa o período "PT5M" e seu alerta procura CPU acima de 80%, o alerta aciona quando a CPU foi consistentemente acima 80% por 5 minutos. Após o primeiro disparador ocorre, ele novamente aciona quando a CPU permanece abaixo de 80% por 5 minutos. As unidades de medida da CPU ocorre cada 1 minuto.   

6. Verificar **os proprietários de Email …** se quiser administradores e colaboradores para ser enviado quando o alerta for acionado.

7. Se você quiser emails adicionais para receber uma notificação quando o alerta for acionado, adicioná-los no campo **email(s) adicionais do administrador** . Separe vários emails com ponto e vírgula-*email@contoso.com;email2@contoso.com*

8. Coloca em um URI válido no campo **Webhook** se quiser que ele chamado quando aciona o alerta.

9. Se você usar automação do Azure, você pode selecionar um Runbook para ser executado quando o alerta for acionado.

10. Selecione **Okey** quando terminar de criar o alerta.   

Em poucos minutos, o alerta está ativo e aciona conforme descrito anteriormente.

## <a name="managing-your-alerts"></a>Gerenciando seus alertas

Quando você tiver criado um alerta, você poderá selecioná-la e:

- Exiba um gráfico mostrando o limite de métrica e os valores reais do dia anterior.
- Editar ou excluí-la.
- **Desabilitar** ou **Habilitar a** ele se desejar temporariamente parar ou continuar recebendo notificações para o alerta.



## <a name="next-steps"></a>Próximas etapas

* [Obtenha uma visão geral de monitoramento Azure](monitoring-overview.md) , incluindo os tipos de informações você pode coletar e monitorar.
* Saiba mais sobre [Configurando webhooks em alertas](insights-webhooks-alerts.md).
* Saiba mais sobre [Runbooks de automação do Azure](..\automation\automation-starting-a-runbook.md).
* Obtenha uma [Visão geral dos logs de diagnóstico](monitoring-overview-of-diagnostic-logs.md) e coletar métricas de alta frequência detalhadas no seu serviço.
* Obtenha uma [Visão geral do conjunto de métricas](insights-how-to-customize-monitoring.md) para garantir que seu serviço está disponível e responde.
