<properties
    pageTitle="Usar o Powershell para definir alertas em ideias de aplicativo"
    description="Automatize configuração de obtenção de informações de aplicativo para acessar emails sobre alterações métricas."
    services="application-insights"
    documentationCenter=""
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/19/2016"
    ms.author="awills"/>

# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Usar o PowerShell para definir alertas em ideias de aplicativo

Você pode automatizar a configuração de [alertas](app-insights-alerts.md) no [Ideias de aplicativo do Visual Studio](app-insights-overview.md).

Além disso, você pode [Definir webhooks para automatizar sua resposta a um alerta](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="one-time-setup"></a>Configuração única

Se você nunca usou o PowerShell com sua assinatura do Azure antes:

Instale o módulo do Powershell do Azure na máquina onde você deseja executar os scripts.

 * Instalar o [Microsoft Web Platform Installer (v5 ou superior)](http://www.microsoft.com/web/downloads/platform.aspx).
 * Usá-lo para instalar o Microsoft Azure Powershell


## <a name="connect-to-azure"></a>Conectar ao Azure

Inicie o PowerShell do Azure e [Conecte-se à sua assinatura](../powershell-install-configure.md):

```PowerShell

    Add-AzureAccount
    Switch-AzureMode AzureResourceManager
```


## <a name="get-alerts"></a>Receber alertas

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Adicionar alerta


    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US"
     -RuleType Metric



## <a name="example-1"></a>Exemplo 1

Enviar email se a resposta do servidor para solicitações HTTP, a média de mais de 5 minutos, for menor do que 1 segundo. Meu recurso de obtenção de informações de aplicativo é chamado IceCreamWebApp e é no grupo de recursos Fabrikam. Eu sou o proprietário da assinatura do Azure.

O GUID é a ID da assinatura (não a chave de instrumentação do aplicativo).

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## <a name="example-2"></a>Exemplo 2

Eu tenho um aplicativo no qual usar [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) para relatar uma métrica denominada "salesPerHour". Envie que um email para meus colegas se "salesPerHour" ficar abaixo 100, média acima de 24 horas.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

A mesma regra pode ser usada para a métrica relatada usando o [parâmetro de medida](app-insights-api-custom-events-metrics.md#properties) da chamada de outro controle como TrackEvent ou trackPageView.

## <a name="metric-names"></a>Nomes de métricos

Nome de métrica | Nome de tela | Descrição
---|---|---
`basicExceptionBrowser.count`|Exceções do navegador|Contagem de exceções não detectadas lançada no navegador.
`basicExceptionServer.count`|Exceções do servidor|Contagem de exceções sem tratamento lançadas pelo aplicativo
`clientPerformance.clientProcess.value`|Tempo de processamento do cliente|Tempo entre recebendo o último byte de um documento até que o DOM é carregado. Ainda podem estar processando solicitações assíncrona.
`clientPerformance.networkConnection.value`|Tempo de conexão de rede de carregamento de página| Tempo navegador para se conectar à rede. Pode ser 0 se cache.
`clientPerformance.receiveRequest.value`|Tempo de resposta de recebimento| Tempo entre navegador Enviando solicitação para começar a receber resposta.
`clientPerformance.sendRequest.value`|Enviar o tempo de solicitação| Tempo gasto pelo navegador para enviar a solicitação.
`clientPerformance.total.value`|Tempo de carregamento de página do navegador|Tempo de solicitação de usuário até DOM, imagens, scripts e folhas de estilo são carregados.
`performanceCounter.available_bytes.value`|Memória disponível|Memória física imediatamente disponível para um processo ou uso do sistema.
`performanceCounter.io_data_bytes_per_sec.value`|Taxa de ES do processo|Total de bytes por segundo ler e gravar arquivos, rede e dispositivos.
`performanceCounter.number_of_exceps_thrown_per_sec`|taxa de exceção|Exceções geradas por segundo.
`performanceCounter.percentage_processor_time.value`|CPU do processo|A porcentagem de tempo decorrido todos os segmentos de processo usado pelo processador para executar instruções para o processo de aplicativos.
`performanceCounter.percentage_processor_total.value`|Tempo do processador|A porcentagem de tempo que o processador gasta em threads ocupados.
`performanceCounter.process_private_bytes.value`|Bytes particulares do processo|Memória atribuída exclusivamente a processos do aplicativo monitorar.
`performanceCounter.request_execution_time.value`|Tempo de execução de solicitação do ASP.NET|Tempo de execução da solicitação mais recente.
`performanceCounter.requests_in_application_queue.value`|Solicitações ASP.NET na fila de execução|Comprimento da fila de solicitação de aplicativo.
`performanceCounter.requests_per_sec`|Taxa de solicitação do ASP.NET|Taxa de todas as solicitações para o aplicativo por segundo do ASP.NET.
`remoteDependencyFailed.durationMetric.count`|Falhas de dependência|Contagem de chamadas com falha feitas pelo aplicativo servidor para recursos externos.
`request.duration`|Tempo de resposta do servidor|Tempo entre recebendo uma solicitação HTTP e terminando enviar a resposta.
`request.rate`|Taxa de solicitação|Taxa de todas as solicitações para o aplicativo por segundo.
`requestFailed.count`|Solicitações com falha|Solicitações de contagem de HTTP que resultou em um código de resposta > = 400
`view.count`|Modos de exibição de página|Contagem de solicitações de usuário do cliente para uma página da web. Tráfego sintético é filtrado.
{seu nome de métrica personalizado}|{Seu nome métrica}|O valor métrico relatado por [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) ou no [parâmetro de medidas de uma chamada de acompanhamento](app-insights-api-custom-events-metrics.md#properties).

As métricas são enviadas por módulos de telemetria diferentes:

Grupo métrico | Módulo de coletor
---|---
basicExceptionBrowser,<br/>no desempenhodo cliente,<br/>modo de exibição | [JavaScript do navegador](app-insights-javascript.md)
performanceCounter | [Desempenho](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3)
remoteDependencyFailed| [Dependência](app-insights-configuration-with-applicationinsights-config.md#nuget-package-1)
solicitação,<br/>requestFailed|[Solicitação de servidor](app-insights-configuration-with-applicationinsights-config.md#nuget-package-2)

## <a name="webhooks"></a>Webhooks

É possível [automatizar sua resposta a um alerta](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Azure chamará um endereço web da sua escolha quando um alerta é gerado.

## <a name="see-also"></a>Consulte também


* [Script para configurar a obtenção de informações de aplicativo](app-insights-powershell-script-create-resource.md)
* [Criar ideias de aplicativo e os recursos de teste da web a partir de modelos](app-insights-powershell.md)
* [Automatizar união diagnóstico do Microsoft Azure de obtenção de informações de aplicativo](app-insights-powershell-azure-diagnostics.md)
* [Automatizar sua resposta a um alerta](../monitoring-and-diagnostics/insights-webhooks-alerts.md)
