<properties
    pageTitle="Configurar alertas para consultas no fluxo Analytics | Microsoft Azure"
    description="Noções básicas sobre a análise de fluxo de alerta"
    keywords="Configurar alertas"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Configurar alertas para trabalhos de análise de fluxo do Azure

## <a name="introduction-monitor-page"></a>Introdução: Página de Monitor

Você pode configurar alertas para acionar um alerta quando uma métrica alcança uma condição que você especificar.

Por exemplo, "se eventos de saída para últimos 15 minutos é < 100, enviar notificação por email a id de email: xyz@company.com”.

Regras podem ser configuradas em métricas por meio do portal, ou podem ser configurado [programaticamente](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sobre os dados de Logs de operação.

## <a name="set-up-alerts-through-the-azure-classic-portal"></a>Configure alertas por meio do Portal de clássico do Azure

Há duas maneiras de configurar alertas no portal do Azure clássico:  

1.  Na guia **Monitor** do seu trabalho de análise de fluxo  
2.  O Log de operações em serviços de gerenciamento de  

## <a name="set-up-alert-through-the-monitor-tab-of-the-job-in-the-portal"></a>Configurar o alerta por meio da guia Monitor do trabalho no portal

1.  Selecione a métrica na guia monitor e clique no botão **Adicionar regra** na parte inferior do painel de controle e as regras de instalação.  

    ![Painel de controle](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)  

2.  Definir o nome e descrição do alerta  

    ![Criar regra](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)  

3.  Insira os limites, janela de alerta de avaliação e as ações de alerta  

    ![Definir condições](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-through-the-operations-logs"></a>Configure alertas por meio dos logs de operações

1.  Vá para a guia **alertas** em serviços de gerenciamento no [Portal de clássico do Azure](https://manage.windowsazure.com).  
2.  Clique em **Adicionar regra**  

    ![Critérios](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)  

3.  Defina o nome e descrição do alerta. Selecione 'Fluxo Analytics' como tipo de serviço e o nome de trabalho como o nome do serviço.  

    ![Definir um alerta](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-in-the-azure-portal"></a>Configurar alertas no portal do Azure ##

No portal do Azure, navegue até o trabalho de análise de fluxo que você estiver interessado em alertas e clique na seção de **monitoramento** .  Na lâmina **métrica** que é aberta, clique no comando de **alerta de adicionar** .

  ![Configuração de portal Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

Você pode nomear sua regra de alerta e escolha uma descrição que aparecerá no email de notificação.

Quando você seleciona métricas você vai escolher uma condição e o limite de valor para a métrica.

  ![Azure métrica selecione portal](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)  

Para obter mais detalhes sobre a configuração de alertas no portal do Azure, consulte [notificações de alerta de recebimento](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [Fórum de análise de fluxo do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
