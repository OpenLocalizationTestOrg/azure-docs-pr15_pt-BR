<properties
    pageTitle="Monitoramento do desempenho para aplicativos web móvel com a análise de desenvolvedor | Microsoft Azure"
    description="Desempenho do aplicativo e monitoramento de uso para os desenvolvedores de aplicativo móvel. , área de trabalho, o serviço da web e aplicativos de back-end com HockeyApp e obtenção de informações do aplicativo."
    authors="alancameronwills"
    services="application-insights"
    documentationCenter=""
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="awills"/>

# <a name="mobile-analytics-with-hockeyapp-and-application-insights"></a>Análise de móvel com HockeyApp e obtenção de informações de aplicativo

Monitore o desempenho e o uso de seus testes beta e implantados aplicativos de desktop e móveis com [HockeyApp](https://hockeyapp.net/). Monitore suporte serviço e back-end aplicativos web com [Ideias de aplicativo do Visual Studio](app-insights-overview.md). Analisar dados de aplicativos cliente e servidor na análise e exibir os gráficos junto com os outros em um painel Azure.

Análise de desenvolvedor do Microsoft oferece duas soluções para monitoramento de desempenho do aplicativo:

* Aplicativos de desktop e **HockeyApp para celular** .
 * Distribua versões de teste para os usuários selecionados.
 * Análise de travamento.
 * Telemetria personalizada para análise de uso.
* Aplicativos de **Obtenção de informações de aplicativo para sites** e serviços e back-end.
 * Métricas de desempenho e o uso e alertas.
 * Rastreamento de diagnóstico e geração de relatórios de exceção.
 * Pesquisa diagnóstico com links de telemetria relacionados e filtragem.

Ambas as soluções oferecem:

 * Poderosa **[linguagem de consulta analítica](app-insights-analytics.md)** diagnóstico e análise.
 * **[Exportar dados](app-insights-export-telemetry.md)** para o seu próprio armazenamento.
 * Exibição do **Painel de controle integrado** de tabelas e gráficos analíticos.

## <a name="monitor-your-app-components"></a>Monitorar os componentes de aplicativo

Siga as instruções nessas páginas para instalar o SDK no seu código e iniciar o controle do seu aplicativo.

### <a name="web-apps---application-insights"></a>Web apps - ideias de aplicativo

* [Aplicativo web do ASP.NET](app-insights-asp-net.md) 
* [Java web app](app-insights-java-get-started.md)
* [Node web app](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Serviços de nuvem Azure](app-insights-cloudservices.md)

### <a name="mobile-apps---hockeyapp"></a>Aplicativos móveis - HockeyApp

* [aplicativo iOS](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Aplicativo do Mac OS X](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Aplicativo do Android](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [Aplicativo Windows universal](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Aplicativo do Windows Phone 8 e 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Windows Presentation Foundation aplicativo](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

Para aplicativos de área de trabalho do Windows, é recomendável HockeyApp. Mas você também pode [Enviar telemetria a partir de um aplicativo Windows de obtenção de informações do aplicativo](app-insights-windows-desktop.md). Talvez você queira fazer isso para experimentar a obtenção de informações do aplicativo.


## <a name="analytics-and-export-for-hockeyapp-telemetry"></a>Análise e exportação para HockeyApp Telemetria

Você pode investigar HockeyApp personalizado e faça logon usando os recursos de análise e exportar contínua de obtenção de informações de aplicativo configurando [uma ponte](app-insights-hockeyapp-bridge-app.md)de telemetria.




