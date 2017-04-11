<properties
    pageTitle="Visão geral de métricas no Microsoft Azure | Microsoft Azure"
    description="Saiba como personalizar gráficos de monitoramento no Azure."
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

# <a name="overview-of-metrics-in-microsoft-azure"></a>Visão geral de métricas no Microsoft Azure

Todos os serviços do Azure acompanhar métricas-chave que permitem monitorar a integridade, o desempenho, a disponibilidade e o uso dos seus serviços. Você pode exibir essas métricas no portal do Azure e você também pode usar a [API REST](https://msdn.microsoft.com/library/azure/dn931930.aspx) ou o [SDK do .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para acessar o conjunto completo de métricas programaticamente.

Para alguns serviços, talvez você precise ativar diagnóstico para ver métricas. Para que outras pessoas, como máquinas virtuais, você obterá um conjunto básico de métricas, mas precisa habilitar completo definir métricas de alta frequência. Consulte [Habilitar o monitoramento e diagnóstico](insights-how-to-use-diagnostics.md) para saber mais.

## <a name="using-monitoring-charts"></a>Usando gráficos de monitoramento

Você pode criar um gráfico qualquer uma das medições-los em qualquer período de tempo que você escolher.

1. No [Portal do Azure](https://portal.azure.com/), clique em **Procurar**e, em seguida, um recurso que você está interessado monitoramento.

2. A seção de **monitoramento** contém as métricas mais importantes para cada recurso Azure. Por exemplo, um aplicativo web tem **solicitações e erros**, onde como máquina virtual teria **porcentagem de CPU** e **disco de leitura e gravação**:  ![Lente de monitoramento](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3. Clicar em qualquer gráfico mostrará a lâmina **métrica** . Na lâmina, além de gráfico, é uma tabela que mostra as agregações das métricas (como média, mínima e máxima, sobre o intervalo de tempo que você escolheu). Abaixo, que são as regras de alerta para o recurso.
    ![Blade métrica](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4. Para personalizar as linhas que aparecem, clique no botão **Editar** no gráfico ou, o comando **Editar gráfico** na lâmina métrica.

5. Na lâmina Editar consulta, você pode fazer três coisas:
    - Alterar o intervalo de tempo
    - Alternar a aparência entre barra e linha
    - Escolha diferentes metics ![Editar consulta](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6. Alterar o intervalo de tempo é tão fácil quanto selecionar um intervalo diferente (como **Última hora**) e clicando em **Salvar** na parte inferior da lâmina. Você também pode escolher **personalizada**, que permite que você escolha qualquer período de tempo nas últimas 2 semanas. Por exemplo, você pode ver as duas semanas inteiras ou, apenas 1 hora de ontem. Digite na caixa de texto para inserir uma hora diferente.
    ![Intervalo de tempo personalizado](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

7. Abaixo do intervalo de tempo, o canal que você escolher qualquer número de métricas para mostrar no gráfico.

8. Quando você clicar em Salvar suas alterações serão salvos para esse recurso em particular. Por exemplo, se você tiver duas máquinas virtuais e você alterar um gráfico em um, ele não afetará a outra.

## <a name="creating-side-by-side-charts"></a>Criando gráficos lado a lado

Com a personalização avançada no portal do, você pode adicionar quantos gráficos como você deseja.

1. No menu **…** na parte superior da lâmina, clique em **Adicionar blocos**:  
    ![Menu adicionar](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Em seguida, você pode selecione um gráfico da **Galeria** no lado direito da tela:  ![Galeria](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Se você não vir a métrica desejado, você pode sempre adicionar uma das métricas predefinidas e **Editar** o gráfico para mostrar a métrica que você precisa.

## <a name="monitoring-usage-quotas"></a>Monitorar as cotas de uso

A maioria das métricas mostram tendências ao longo do tempo, mas certos dados, como as cotas de uso, são informações no momento com um limite.

Você também pode ver as cotas de uso na lâmina para recursos que têm cotas:

![Uso](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Como com métricas, você pode usar a [API REST](https://msdn.microsoft.com/library/azure/dn931963.aspx) ou o [SDK do .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para acessar o conjunto completo de cotas de uso programaticamente.

## <a name="next-steps"></a>Próximas etapas

* [Receber notificações de alerta](insights-receive-alert-notifications.md) sempre que uma métrica exceder um limite.
* [Habilitar o monitoramento e diagnóstico](insights-how-to-use-diagnostics.md) para coletar métricas de alta frequência detalhadas no seu serviço.
* [Dimensionar automaticamente a contagem de instância](insights-how-to-scale.md) para garantir que seu serviço está disponível e responde.
* [Monitorar o desempenho do aplicativo](../application-insights/app-insights-azure-web-apps.md) se você quiser entender exatamente como seu código é o desempenho na nuvem.
* Use [Ideias de aplicativo JavaScript aplicativos e páginas da web](../application-insights/app-insights-web-track-usage.md) para obter a análise de cliente sobre os navegadores visitar uma página da web.
* [Disponibilidade do monitor e a capacidade de resposta de qualquer página da web](../application-insights/app-insights-monitor-web-app-availability.md) com ideias de aplicativo para que você pode descobrir se a página está inoperante.
