<properties
    pageTitle="Diminuir o desempenho do aplicativo da web no aplicativo de serviço | Microsoft Azure"
    description="Este artigo ajuda você a solucionar problemas de desempenho de aplicativo web lenta no serviço de aplicativo do Azure."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="top-support-issue"
    keywords="desempenho da Web app, app lenta, aplicativo lento"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cephalin"/>

# <a name="troubleshoot-slow-web-app-performance-issues-in-azure-app-service"></a>Solucionar problemas de desempenho de aplicativo web lenta no serviço de aplicativo do Azure

Este artigo ajuda você a solucionar problemas de desempenho de aplicativo web lenta no [Serviço de aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Se precisar de mais ajuda a qualquer momento neste artigo, você pode contatar os Azure especialistas no [Azure MSDN e os fóruns de estouro de pilha](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode enviar um incidente de suporte Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter**suporte.

## <a name="symptom"></a>Sintoma

Quando você navega o web app, a carga de páginas lentamente e às vezes, tempo limite.

## <a name="cause"></a>Causa

Este problema geralmente é causado por problemas de nível de aplicativo, tais como:

-   solicitações demorando muito
-   aplicativo usando memória alta/CPU
-   aplicativo está travando devido a uma exceção.

## <a name="troubleshooting-steps"></a>Etapas de solução de problemas

Solução de problemas pode ser dividida em três tarefas distintas, em ordem sequencial:

1.  [Observar e monitorar o comportamento do aplicativo](#observe)
2.  [Coletar dados](#collect)
3.  [Atenuar o problema](#mitigate)

[Aplicativo de serviço Web Apps](/services/app-service/web/) fornece diversas opções em cada etapa.

<a name="observe" />
### <a name="1-observe-and-monitor-application-behavior"></a>1. observe e monitorar o comportamento do aplicativo

#### <a name="track-service-health"></a>Acompanhar a integridade do serviço

Microsoft Azure publicizes sempre que não há uma degradação de desempenho ou interrupção do serviço. Você pode controlar a integridade do serviço no [Portal do Azure](https://portal.azure.com/). Para obter mais informações, consulte [integridade do serviço de controle](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>Monitorar seu aplicativo web

Essa opção permite descobrir se o seu aplicativo está tendo problemas. Em lâmina do web app, clique no bloco **solicitações e erros** . A lâmina **métrica** mostrará todas as métricas que você pode adicionar.

Algumas das métricas que você talvez queira monitorar para o aplicativo web são

-   Média memória conjunto de trabalho
-   Tempo médio de resposta
-   Tempo de CPU
-   Conjunto de trabalho de memória
-   Solicitações

![monitorar o desempenho do aplicativo web](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Para obter mais informações, consulte:

-   [Monitorar aplicativos da Web em um serviço de aplicativo do Azure](web-sites-monitor.md)
-   [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Monitorar o status de ponto de extremidade da web

Se você estiver executando o aplicativo web no **padrão** preços camada, Web Apps permite monitorar pontos de extremidade de 2 de 3 locais geográficos.

Ponto de extremidade monitoramento configura testes da web de locais distribuídos geográfica que testar o tempo de resposta e tempo de atividade de URLs de web. O teste executa uma operação GET HTTP na URL da web para determinar o tempo de resposta e o tempo de atividade de cada local. Cada local configurado executa um teste cada cinco minutos.

Atividade é monitoramento usando códigos de resposta HTTP, e o tempo de resposta é medido em milissegundos. Um teste de monitoramento falhará se o código de resposta HTTP for maior que ou igual a 400 ou se a resposta demora mais de 30 segundos. Um ponto de extremidade é considerado disponível se seus testes monitoramento bem-sucedida de todos os locais especificados.

Para configurar, consulte [como: monitorar o status de ponto de extremidade do web](web-sites-monitor.md#webendpointstatus).

Consulte também [mantendo Sites do Azure para cima além do ponto de extremidade de monitoramento - com Nuno se Schackow](/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/) para um vídeo sobre monitoramento de ponto de extremidade.

#### <a name="application-performance-monitoring-using-extensions"></a>Monitoramento de desempenho de aplicativo usando extensões

Você também pode monitorar o desempenho do aplicativo utilizando _extensões de site_.

Cada serviço de aplicativo web app oferece um ponto de extremidade de gerenciamento extensível que permite que você aproveite um poderoso conjunto de ferramentas implantado como extensões de site. Essas ferramentas abrangem desde editores de código de origem como o [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx) para ferramentas de gerenciamento de recursos conectados como um banco de dados MySQL conectada a um aplicativo web.

[Obtenção de informações de aplicativo do Azure](/services/application-insights/) e [Novo Relíquia](/marketplace/partners/newrelic/newrelic/) são dois do desempenho monitoramento extensões de sites que estão disponíveis. Para usar o novo Relíquia, você instalar um agente no tempo de execução. Para usar ideias de aplicativo do Azure, você recriar seu código com um SDK e também é possível instalar uma extensão que fornece acesso a dados adicionais. O SDK permite que você escrever código para monitorar o uso e o desempenho do seu aplicativo mais detalhadamente.

Para usar a obtenção de informações do aplicativo, consulte [monitorar o desempenho em aplicativos da web](../application-insights/app-insights-web-monitor-performance.md).

Para usar o novo Relíquia, consulte [Novo gerenciamento de desempenho de aplicativo Relíquia no Azure](../store-new-relic-cloud-services-dotnet-application-performance-management.md).

<a name="collect" />
### <a name="2-collect-data"></a>2. coletar dados

####    <a name="enable-diagnostics-logging-for-your-web-app"></a>Habilitar o log de diagnóstico para o aplicativo web

O ambiente de aplicativos Web fornece funcionalidade de diagnóstico para informações de registro de servidor web e o aplicativo da web. Estes são separadas logicamente em diagnósticos do servidor web e o diagnóstico de aplicativo.

##### <a name="web-server-diagnostics"></a>Diagnósticos do servidor de Web

Você pode ativar ou desativar os seguintes tipos de logs:

-   **Logs de erro detalhados** - informações de erro detalhadas para códigos de status HTTP que indicam uma falha (código de status 400 ou maior). Isso pode conter informações que podem ajudar a determinar por que o servidor retornou o código de erro.
-   **Rastreamento de solicitação falha** - informações detalhadas sobre solicitações com falha, incluindo um rastreamento dos componentes IIS usado para processar a solicitação e o tempo gasto em cada componente. Isso pode ser útil se você estiver tentando melhorar o desempenho do aplicativo web ou isolar o que está causando um erro HTTP específico.
-   **Log de servidor de web** - informações sobre transações de HTTP usando o formato de arquivo de log estendido W3C. Isso é útil ao determinar métricas geral de aplicativo web, como o número de solicitações de manipulados ou quantas solicitações são de um endereço IP específico.

##### <a name="application-diagnostics"></a>Diagnóstico do aplicativo

Diagnóstico do aplicativo permite capturar informações produzidas por um aplicativo web. Aplicativos ASP.NET podem usar a `System.Diagnostics.Trace` classe para registrar informações para o log de diagnóstico do aplicativo.

Para obter instruções detalhadas sobre como configurar seu aplicativo para o registro em log, consulte [Habilitar o registro em log para web apps em um serviço de aplicativo do Azure de diagnóstico](web-sites-enable-diagnostic-log.md).

#### <a name="use-remote-profiling"></a>Usar criação de perfil remoto

No serviço de aplicativo do Azure, aplicativos Web, aplicativos de API e WebJobs pode ser atribuído remotamente. Se o processo está sendo executado mais lentamente do que o esperado, ou a latência de solicitações HTTP são superiores normal e o uso da CPU do processo também está alto, você pode remotamente perfil seu processo e obter as pilhas de chamadas de amostragem de CPU para analisar a atividade de processo e caminhos de código quente.

Para obter mais informações, consulte [suporte remoto de criação de perfil no serviço de aplicativo do Azure](/blog/remote-profiling-support-in-azure-app-service).


#### <a name="use-the-azure-app-service-support-portal"></a>Usar o Portal de suporte do Azure serviço de aplicativo

Aplicativos Web oferece a capacidade de solucionar problemas relacionados ao seu aplicativo web examinando HTTP logs, logs de eventos, despejos de processo e muito mais. Você pode acessar todas essas informações usando nosso portal de suporte em **http://&lt;o nome do aplicativo >.scm.azurewebsites.net/Support**

O portal de suporte de serviço de aplicativo do Azure oferece três guias separadas para dar suporte as três etapas de um cenário de solução de problemas comuns:

1.  Observar comportamento atual
2.  Analisar coletar informações de diagnóstico e executando os analyzers internos
3.  Atenuar

Se o problema estiver ocorrendo no momento, clique em **Analisar** > **Diagnóstico** > **Diagnosticar agora** para criar uma sessão de diagnóstico para você, que coletará HTTP logs, logs do Visualizador de eventos, memória despejos, logs de erros PHP e PHP processam o relatório.

Depois que os dados são coletados, ele também execute uma análise nos dados e fornecer um relatório em HTML.

Caso você deseje baixar os dados, por padrão, ele seria armazenado na pasta D:\home\data\DaaS.

Para obter mais informações sobre o portal de suporte de serviço de aplicativo do Azure, consulte [Novas atualizações extensão do Site de suporte para sites do Azure](/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>Usar o Console de depuração de Kudu

Aplicativos Web vem com um console de depuração que você pode usar para depuração, explorando, upload de arquivos, bem como pontos de extremidade de JSON para obter informações sobre seu ambiente. Isso se chama o _Kudu Console_ ou o _Painel de SCM_ para o aplicativo web.

Você pode acessar esse painel indo para o link **https://&lt;o nome do aplicativo >.scm.azurewebsites.net/**.

Algumas das coisas que fornece Kudu são:

-   configurações de ambiente para seu aplicativo
-   fluxo de log
-   despejo de diagnóstico
-   depure console no qual você pode executar cmdlets do Powershell e comandos básicos DOS.


Outro recurso útil do Kudu é que, caso seu aplicativo é gerar exceções de primeira chance, você pode usar Kudu e a ferramenta SysInternals Procdump criar memória descarta. Esses despejos de memória são instantâneos do processo e podem geralmente ajudá-lo a solucionar problemas mais complicados com seu aplicativo web.

Para obter mais informações sobre recursos disponíveis no Kudu, consulte [Ferramentas de serviços de equipe do Azure sites, que você deve saber sobre](/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />
### <a name="3-mitigate-the-issue"></a>3. atenuar o problema

####    <a name="scale-the-web-app"></a>Dimensionar o web app

No serviço de aplicativo do Azure, para melhor desempenho e produtividade, você pode ajustar a escala na qual você está executando o aplicativo. Dimensionamento de um aplicativo web envolve duas ações relacionadas: alterando seu plano de serviço de aplicativo para um nível de preços mais alto e configurando certas configurações depois que você tenha alternado para o nível de preços mais alto.

Para obter mais informações sobre dimensionamento, consulte [escala de um aplicativo web no serviço de aplicativo do Azure](web-sites-scale.md).

Além disso, você pode optar por executar o aplicativo mais de uma instância. Isso não só oferece mais capacidade de processamento, mas também oferece alguma quantidade de tolerância. Se o processo falhar em uma instância, a outra instância continuarão atendendo solicitações.

Você pode definir o dimensionamento para ser Manual ou automática.

####    <a name="use-autoheal"></a>Use AutoHeal

AutoHeal reciclagem o processo de trabalho para o aplicativo com base nas configurações que você escolher (como alterações de configuração, solicitações, limites baseados em memória ou o tempo necessário para executar uma solicitação). Na maioria das vezes, o processo de reciclagem é a maneira mais rápida para resolver um problema. Embora você sempre poderá reiniciar o aplicativo web da diretamente no Portal do Azure, AutoHeal fará automaticamente para você. Tudo o que você precisa fazer é adicionar alguns disparadores no Web. config raiz para o aplicativo web. Observe que essas configurações seriam funciona da mesma maneira, mesmo se o seu aplicativo não é um .net um.

Para obter mais informações, consulte [reparo automático Sites do Azure](/blog/auto-healing-windows-azure-web-sites/).

####    <a name="restart-the-web-app"></a>Reiniciar o aplicativo web

Isso geralmente é a maneira mais simples para recuperar de problemas de uso únicos. No [Portal do Azure](https://portal.azure.com/), em lâmina do web app, você tem as opções para interromper ou reinicie o aplicativo.

 ![Reinicie o aplicativo da web para solucionar problemas de desempenho](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Você também pode gerenciar seu aplicativo web usando o Powershell do Azure. Para obter mais informações, consulte [Usando o PowerShell Azure com o Gerenciador de recursos do Azure](../powershell-azure-resource-manager.md).
