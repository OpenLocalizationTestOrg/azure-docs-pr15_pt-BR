<properties
    pageTitle="Habilitar monitoramento e diagnóstico no Microsoft Azure | Microsoft Azure "
    description="Saiba como configurar o diagnóstico para seus recursos no Azure."
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

# <a name="enable-monitoring-and-diagnostics"></a>Habilitar o monitoramento e diagnóstico

No [Portal do Azure](https://portal.azure.com), você pode configurar dados de diagnóstico e monitoramento sofisticados e frequente, sobre seus recursos. Você também pode usar a [API REST](https://msdn.microsoft.com/library/azure/dn931932.aspx) ou [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) configurar diagnósticos programaticamente.

Dados de diagnóstico, monitoramento e métrica no Azure são salvos em uma conta de armazenamento de sua escolha. Isso permite que você use qualquer ferramentas que você deseja ler os dados, de um Gerenciador de armazenamento, Power BI para ferramentas de terceiros.

## <a name="when-you-create-a-resource"></a>Quando você cria um recurso

A maioria dos serviços permite que você habilitar o diagnóstico quando você os cria primeiro no [Portal do Azure](https://portal.azure.com).

1. Vá para **novo** e escolha o recurso que você está interessado.

2. Selecione **configuração opcional**.
    ![Blade de diagnóstico](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)

3. Selecione **diagnósticos**e clique **em**. Você precisará escolher a conta de armazenamento que você deseja que o diagnóstico para ser salvo. Você será cobrado taxas de dados normal para armazenamento e transações ao enviar diagnósticos para uma conta de armazenamento.

4. Clique em **Okey** e criar o recurso.

## <a name="change-settings-for-an-existing-resource"></a>Alterar as configurações de um recurso existente

Se você já tiver criado um recurso e você deseja alterar as configurações do diagnóstico (para alterar o nível do conjunto de dados, por exemplo), você pode fazer esse diretamente no Portal do Azure.

1. Vá para o recurso e clique no comando **configurações** .

2. Selecione **Diagnóstico**.

3. A lâmina de **Diagnóstico** tem todas as possível diagnóstico e de dados de conjunto de monitoramento para esse recurso. Para alguns recursos, você também pode escolher uma política de **retenção** para os dados, para limpar da sua conta de armazenamento.
    ![Diagnóstico do armazenamento](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)

4. Depois de escolher as configurações, clique no comando **Salvar** . Poderá demorar um pouco enquanto para monitoramento de dados para mostrar se você estiver ativando-lo pela primeira vez.

### <a name="categories-of-data-collection-for-virtual-machines"></a>Categorias de coleta de dados para máquinas virtuais
Para máquinas virtuais todos os logs e métricas serão registrados em intervalos de um minuto, para que você sempre tenha as informações mais atualizadas sobre sua máquina.

- **Métricas básicas** : métricas de saúde sobre sua máquina virtual como processador e memória
- **Métricas de rede e da web** : métricas sobre suas conexões de rede e serviços da web
- **Métricas de .NET** : métricas sobre os aplicativos .NET e ASP.NET executando em sua máquina virtual
- **Métricas SQL** : se você estiver executando o serviço do Microsoft SQL, suas métricas de desempenho
- **Logs de aplicativo de eventos do Windows** : eventos do Windows que são enviados para o canal de aplicativo
- **Logs de sistema de eventos do Windows** : eventos do Windows que são enviados para o canal de sistema. Isso também inclui todos os eventos de [Antimalware da Microsoft](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409).
- **Logs de segurança de eventos do Windows** : eventos do Windows que são enviados para o canal de segurança
- **Logs de infraestrutura de diagnóstico** : log sobre a infraestrutura de conjunto de diagnóstico
- **Logs do IIS** : Logs sobre seu servidor IIS

Observe que neste momento determinadas distribuições do Linux não são suportadas e o agente de convidado deve ser instalado na máquina virtual.

## <a name="next-steps"></a>Próximas etapas

* [Notificações de alerta de recebimento](insights-receive-alert-notifications.md) sempre que ocorrerem eventos operacionais ou métricas cross um limite.
* [Métricas de serviço do monitor](insights-how-to-customize-monitoring.md) para certificar-se de que seu serviço está disponível e responde.
* [Dimensionar automaticamente a contagem de instância](insights-how-to-scale.md) para garantir que sua escala de serviço com base na demanda.
* [Monitorar o desempenho do aplicativo](../application-insights/app-insights-azure-web-apps.md) se você quiser entender exatamente como seu código é o desempenho na nuvem.
* [Exibir eventos e logs de auditoria](insights-debugging-with-events.md) para saber tudo o que aconteceu no seu serviço.
* [Integridade do serviço de controle](insights-service-health.md) para saber quando Azure passou interrupções de serviço ou degradação de desempenho.
