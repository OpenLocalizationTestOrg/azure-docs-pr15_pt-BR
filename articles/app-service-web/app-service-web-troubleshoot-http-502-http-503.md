<properties
    pageTitle="Corrigir 502 gateway incorreto, 503 serviço indisponíveis erros | Microsoft Azure"
    description="Solucionar problemas 502 gateway incorreto 503 Serviço indisponível erros e em seu aplicativo web hospedado em um serviço de aplicativo do Azure."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="top-support-issue"
    keywords="502 gateway incorreto, 503 Serviço indisponível, o erro 503, o erro 502"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cephalin"/>

# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>Solucionar problemas de erros HTTP de "502 gateway incorreto" e "503 Serviço indisponível" em seus aplicativos web do Azure

"502 gateway incorreto" e "503 Serviço indisponível" são erros comuns em seu aplicativo web hospedado em um [Serviço de aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Este artigo ajuda você a solucionar esses erros.

Se precisar de mais ajuda a qualquer momento neste artigo, você pode contatar os Azure especialistas no [Azure MSDN e os fóruns de estouro de pilha](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode enviar um incidente de suporte Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter**suporte.

## <a name="symptom"></a>Sintoma

Quando você navega na web App, ela retorna um HTTP Erro de "502 Gateway incorreto" ou um HTTP "503 Serviço indisponível" erro.

## <a name="cause"></a>Causa

Este problema geralmente é causado por problemas de nível de aplicativo, tais como:

-   solicitações demorando muito
-   aplicativo usando memória alta/CPU
-   aplicativo está travando devido a uma exceção.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Etapas de solução de problemas para resolver erros de "503 Serviço indisponível" e "502 gateway incorreto"

Solução de problemas pode ser dividida em três tarefas distintas, em ordem sequencial:

1.  [Observar e monitorar o comportamento do aplicativo](#observe)
2.  [Coletar dados](#collect)
3.  [Atenuar o problema](#mitigate)

[Aplicativo de serviço Web Apps](/services/app-service/web/) fornece diversas opções em cada etapa.

<a name="observe" />
### <a name="1-observe-and-monitor-application-behavior"></a>1. observe e monitorar o comportamento do aplicativo

####    <a name="track-service-health"></a>Acompanhar a integridade do serviço

Microsoft Azure publicizes sempre que não há uma degradação de desempenho ou interrupção do serviço. Você pode controlar a integridade do serviço no [Portal do Azure](https://portal.azure.com/). Para obter mais informações, consulte [integridade do serviço de controle](../monitoring-and-diagnostics/insights-service-health.md).

####    <a name="monitor-your-web-app"></a>Monitorar seu aplicativo web

Essa opção permite descobrir se o seu aplicativo está tendo problemas. Em lâmina do web app, clique no bloco **solicitações e erros** . A lâmina **métrica** mostrará todas as métricas que você pode adicionar.

Algumas das métricas que você talvez queira monitorar para o aplicativo web são

-   Média memória conjunto de trabalho
-   Tempo médio de resposta
-   Tempo de CPU
-   Conjunto de trabalho de memória
-   Solicitações

![monitorar aplicativos web na solução de erros de HTTP de 502 gateway incorreto e 503 Serviço indisponível](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Para obter mais informações, consulte:

-   [Monitorar aplicativos da Web em um serviço de aplicativo do Azure](web-sites-monitor.md)
-   [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />
### <a name="2-collect-data"></a>2. coletar dados

####    <a name="use-the-azure-app-service-support-portal"></a>Usar o Portal de suporte do Azure serviço de aplicativo

Aplicativos Web oferece a capacidade de solucionar problemas relacionados ao seu aplicativo web examinando HTTP logs, logs de eventos, despejos de processo e muito mais. Você pode acessar todas essas informações usando nosso portal de suporte em **http://&lt;o nome do aplicativo >.scm.azurewebsites.net/Support**

O portal de suporte de serviço de aplicativo do Azure oferece três guias separadas para dar suporte as três etapas de um cenário de solução de problemas comuns:

1.  Observar comportamento atual
2.  Analisar coletar informações de diagnóstico e executando os analyzers internos
3.  Atenuar

Se o problema estiver ocorrendo no momento, clique em **Analisar** > **Diagnóstico** > **Diagnosticar agora** para criar uma sessão de diagnóstico para você, que coletará HTTP logs, logs do Visualizador de eventos, memória despejos, logs de erros PHP e PHP processam o relatório.

Depois que os dados são coletados, ele também execute uma análise nos dados e fornecer um relatório em HTML.

Caso você deseje baixar os dados, por padrão, ele seria armazenado na pasta D:\home\data\DaaS.

Para obter mais informações sobre o portal de suporte de serviço de aplicativo do Azure, consulte [Novas atualizações extensão do Site de suporte para sites do Azure](/blog/new-updates-to-support-site-extension-for-azure-websites).

####    <a name="use-the-kudu-debug-console"></a>Usar o Console de depuração de Kudu

Aplicativos Web vem com um console de depuração que você pode usar para depuração, explorando, upload de arquivos, bem como pontos de extremidade de JSON para obter informações sobre seu ambiente. Isso se chama o _Kudu Console_ ou o _Painel de SCM_ para o aplicativo web.

Você pode acessar esse painel indo para o link **https://&lt;o nome do aplicativo >.scm.azurewebsites.net/**.

Algumas das coisas que fornece Kudu são:

-   configurações de ambiente para seu aplicativo
-   fluxo de log
-   despejo de diagnóstico
-   depure console no qual você pode executar cmdlets do Powershell e comandos básicos DOS.


Outro recurso útil do Kudu é que, caso seu aplicativo é gerar exceções de primeira chance, você pode usar Kudu e a ferramenta SysInternals Procdump criar memória descarta. Esses despejos de memória são instantâneos do processo e podem geralmente ajudá-lo a solucionar problemas mais complicados com seu aplicativo web.

Para obter mais informações sobre recursos disponíveis no Kudu, consulte [Ferramentas online Azure sites, que você deve saber sobre](/blog/windows-azure-websites-online-tools-you-should-know-about/).

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

 ![Reinicie o aplicativo para resolver erros de HTTP de 502 gateway incorreto e 503 Serviço indisponível](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Você também pode gerenciar seu aplicativo web usando o Powershell do Azure. Para obter mais informações, consulte [Usando o PowerShell Azure com o Gerenciador de recursos do Azure](../powershell-azure-resource-manager.md).
