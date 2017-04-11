<properties 
    pageTitle="Solução de problemas e perguntas sobre a obtenção de informações de aplicativo" 
    description="Algo no Visual Studio aplicativo ideias confusas ou não está funcionando? Tente aqui." 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="awills"/>
 
# <a name="questions---application-insights-for-aspnet"></a>Perguntas - ideias de aplicativo do ASP.NET

## <a name="configuration-problems"></a>Problemas de configuração

*Estou tendo problemas para configurar meu:*

* [Aplicativo .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Monitorar um aplicativo já em execução](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Diagnóstico do Azure](app-insights-azure-diagnostics.md)
* [Java web app](app-insights-java-troubleshoot.md)
* [Outras plataformas](app-insights-platforms.md)

*Não recebi nenhum dado do meu servidor*

* [Exceções do firewall do conjunto](app-insights-ip-addresses.md)
* [Configurar um servidor ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Configurar um servidor Java](app-insights-java-agent.md)


## <a name="can-i-use-application-insights-with-"></a>Posso usar a obtenção de informações de aplicativo com …?

[Consulte plataformas][platforms]


## <a name="is-it-free"></a>É gratuito?

* Sim, se você escolher a livre [preços camada](app-insights-pricing.md). Você recebe a maioria dos recursos e uma cota generosa de dados. 
* Você deve fornecer seus dados de cartão de crédito para registrar com o Microsoft Azure, mas sem encargos serão feitos, a menos que você use outro pagas Azure serviço ou atualizar explicitamente para um nível de pagamento.
* Se seu aplicativo envia mais dados do que a cota de mensal para a camada gratuita, ele deixará está sendo registrada. Se isso acontecer, você pode optar por iniciar pagando, ou aguarde até que a cota é redefinida no final do mês.
* Dados básicos de uso e a sessão não estão sujeito a uma cota.
* Também há uma avaliação gratuita de 30 dias, durante o qual você obtém os recursos pagos gratuitamente.
* Cada recurso de aplicativo possui uma cota separada e definir seu nível de preços independentemente dos outros.

#### <a name="what-do-i-get-if-i-pay"></a>O que faço para se posso pagar?

* Uma maior [mensal cota de dados](https://azure.microsoft.com/pricing/details/application-insights/).
* Opção de pagamento 'excedente' para continuar a coleta de dados sobre a cota mensal. Se seus dados apresenta cota, você é cobrado por Mb.
* [Exportar contínuo](app-insights-export-telemetry.md).


## <a name="q14"></a>O que a obtenção de informações de aplicativo modificar no meu projeto?

Os detalhes dependem do tipo de projeto. Para um aplicativo web:


+ Adiciona esses arquivos ao seu projeto:

 + ApplicationInsights.config. 
 + AI.js


+ Instala esses pacotes NuGet:

 -  *API de ideias de aplicativo* - API principal

 -  *API de obtenção de informações de aplicativo para aplicativos Web* - usado para enviar telemetria do servidor

 -  *API de obtenção de informações de aplicativo para aplicativos de JavaScript* - usado para enviar telemetria do cliente

    Os pacotes incluem esses conjuntos:

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Insere itens para:

 - Web. config

 - Packages.config

+ (Novos projetos somente - se você [Adicionar a obtenção de informações de aplicativo a um projeto existente][start], você precisa fazer isso manualmente.) Insere trechos o código de cliente e servidor para inicializá-los com a identificação do recurso de obtenção de informações do aplicativo. Por exemplo, em um aplicativo MVC, código é inserido na página mestra Views/Shared/_Layout.cshtml


## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Como atualizar de versões mais antigas do SDK?

Consulte as [notas de versão](app-insights-release-notes.md) para o SDK apropriado para o tipo de aplicativo. 



## <a name="update"></a>Como posso alterar qual recurso Azure meu projeto envia dados?

No Solution Explorer, clique com botão direito `ApplicationInsights.config` e escolha a **Obtenção de informações de aplicativo de atualização**. Você pode enviar os dados para um recurso novo ou existente no Azure. O Assistente de atualização altera a chave de instrumentação no ApplicationInsights.config, que determina onde o servidor SDK envia seus dados. A menos que você desmarque a opção "Atualizar tudo", ele também alterará a chave onde ele aparece em suas páginas da web.


#### <a name="data"></a>Por quanto tempo os dados são mantidos no portal? Ele está seguro?

Dê uma olhada [retenção de dados e privacidade][data].

## <a name="logging"></a>Registro em log

#### <a name="post"></a>Como posso ver os dados de POSTAGEM na pesquisa de diagnóstico?

Podemos não registrar dados POST automaticamente, mas você pode usar uma chamada de TrackTrace: colocar os dados do parâmetro da mensagem. Isso tem um limite de tamanho maior do que os limites em Propriedades de cadeia de caracteres, embora você não pode filtrar nele. 

## <a name="security"></a>Segurança

#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>Meus dados é seguro no portal? Por quanto tempo ele é mantido?

Consulte [dados retenção e privacidade][data].


## <a name="q17"></a>Eu tiver habilitado tudo em ideias de aplicativo?

<table border="1">
<tr><th>O que você verá</th><th>Como obter isso</th><th>Por que você deseja que ele</th></tr>
<tr><td>Gráficos de disponibilidade</td><td><a href="../app-insights-monitor-web-app-availability/">Testes da Web</a></td><td>Saber que seu aplicativo web é para cima</td></tr>
<tr><td>Desempenho de aplicativo do servidor: tempos de resposta,...
</td><td><a href="../app-insights-asp-net/">Adicionar a obtenção de informações de aplicativo ao projeto</a><br/>ou <br/><a href="../app-insights-monitor-performance-live-website-now/">Instalar o Monitor de Status de AI no servidor</a> (ou escrever seu próprio código para <a href="../app-insights-api-custom-events-metrics/#track-dependency">Rastrear dependências</a>)</td><td>Detectar problemas de desempenho</td></tr>
<tr><td>Telemetria de dependência</td><td><a href="../app-insights-monitor-performance-live-website-now/">Instalar o Monitor de Status de AI no servidor</a></td><td>Diagnosticar problemas com bancos de dados ou outros componentes externos</td></tr>
<tr><td>Obter rastreamentos de pilha de exceções</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">Inserir TrackException chamadas em seu código</a> (mas alguns são relatados automaticamente)</td><td>Detectar e diagnosticar exceções</td></tr>
<tr><td>Rastreamentos de log de pesquisa</td><td><a href="../app-insights-search-diagnostic-logs/">Adicionar um adaptador de registro em log</a></td><td>Diagnosticar exceções, problemas de desempenho</td></tr>
<tr><td>Noções básicas de uso do cliente: modos de exibição de página, sessões,...</td><td><a href="../app-insights-javascript/">Inicializador de JavaScript em páginas da web</a></td><td>Análise de uso</td></tr>
<tr><td>Métricas personalizadas do cliente</td><td><a href="../app-insights-api-custom-events-metrics/">Chamadas de acompanhamento em páginas da web</a></td><td>Aprimorar a experiência do usuário</td></tr>
<tr><td>Métricas personalizadas do servidor</td><td><a href="../app-insights-api-custom-events-metrics/">Chamadas de acompanhamento no código do servidor</a></td><td>Inteligência de negócios</td></tr>
</table>


## <a name="automation"></a>Automação

Você pode [escrever scripts do PowerShell](app-insights-powershell.md) para criar e atualizar os recursos de obtenção de informações do aplicativo.

## <a name="more-answers"></a>Mais respostas

* [Fórum do aplicativo de ideias](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)


<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 