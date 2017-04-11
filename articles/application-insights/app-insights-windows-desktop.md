<properties 
    pageTitle="Uso de monitoramento e desempenho para aplicativos de desktop do Windows" 
    description="Analise uso e o desempenho do seu aplicativo de área de trabalho do Windows com HockeyApp e obtenção de informações do aplicativo." 
    services="application-insights" 
    documentationCenter="windows"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/26/2016" 
    ms.author="awills"/>

# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>Uso de monitoramento e desempenho em aplicativos de área de trabalho do Windows

*Obtenção de informações de aplicativo está no modo de visualização.*

[Ideias de aplicativo do Visual Studio](app-insights-overview.md) e [HockeyApp](https://hockeyapp.net) permitem monitorar seu aplicativo implantado para uso e desempenho.

> [AZURE.IMPORTANT] É recomendável [HockeyApp](https://hockeyapp.net) distribuir e monitorar aplicativos de desktop e dispositivo. Com HockeyApp, você pode gerenciar distribuição, teste ao vivo e comentários do usuário, bem como monitorar relatórios de uso e falha. Você também pode [Exportar e seu telemetria com a análise de consulta](app-insights-hockeyapp-bridge-app.md).

> Embora telemetria pode ser enviada para a obtenção de informações de aplicativo de um aplicativo da área de trabalho, isso é principalmente útil para fins de depuração e experimentais.


## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>Para enviar telemetria de obtenção de informações do aplicativo de um aplicativo do Windows

1. No [portal do Azure](https://portal.azure.com), [Crie um recurso de obtenção de informações do aplicativo](app-insights-create-new-resource.md). Para tipo de aplicativo, escolha o aplicativo do ASP.NET.
2. Faça uma cópia da chave de instrumentação. Encontre a chave no Essentials suspensa do novo recurso que você acabou de criar. 
3. No Visual Studio, edite os pacotes NuGet do seu projeto de aplicativo e adicionar Microsoft.ApplicationInsights.WindowsServer. (Ou escolha Microsoft.ApplicationInsights se quiser que o vazio API, sem os módulos de conjunto padrão de telemetria.)
4. Defina a chave de instrumentação tanto no seu código:

    `TelemetryConfiguration.Active.InstrumentationKey = "`*sua chave*`";` 

    ou em ApplicationInsights.config (se você tiver instalado um dos pacotes telemetria padrão):
 
    `<InstrumentationKey>`*sua chave*`</InstrumentationKey>` 

    Se você usar ApplicationInsights.config, verifique se suas propriedades no Solution Explorer são definidas como **Build Action = conteúdo, copiar para diretório de saída = copiar**.
5. [Usar a API](app-insights-api-custom-events-metrics.md) para enviar telemetria.
6. Executar o aplicativo e ver a telemetria do recurso que você criou no Portal do Azure.

## <a name="telemetry"></a>Exemplo de código

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>Próximas etapas

* [Criar um painel](app-insights-dashboards.md)
* [Pesquisa de diagnóstico](app-insights-diagnostic-search.md)
* [Explorar métricas](app-insights-metrics-explorer.md)
* [Escrever consultas de análise](app-insights-analytics.md)
 
