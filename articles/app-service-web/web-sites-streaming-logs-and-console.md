<properties 
    pageTitle="Console e logs de streaming" 
    description="Logs de streaming e visão geral do console" 
    authors="btardif" 
    manager="wpickett" 
    editor="" 
    services="app-service\web" 
    documentationCenter=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/12/2016" 
    ms.author="byvinyal"/>

# <a name="streaming-logs-and-the-console"></a>Logs de streaming e o Console

## <a name="streaming-logs"></a>Logs de streaming

O **portal do Azure** fornece um visualizador de log de streaming integrado que permite exibir eventos de rastreamento de aplicativos do **Serviço de aplicativo** em tempo real.  

Configurar esse recurso requer algumas etapas simples:

- Escrever rastreamentos em seu código
- Ativar **Logs de diagnóstico** do aplicativo para o aplicativo
- Exiba o fluxo de interface do usuário do interno **Logs de Streaming** no **portal do Azure**.

### <a name="how-to-write-traces-in-your-code"></a>Como escrever rastreamentos em seu código ###

É fácil escrever rastreamentos em seu código.  Em c# é tão fácil como escrever o seguinte código:

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

A classe de rastreamento reside no namespace System. Diagnostics.

Em um aplicativo de Node, você pode escrever este código para obter o mesmo resultado:

`````````````````````````
console.log("My trace statement").
`````````````````````````

### <a name="how-to-enable-and-view-the-streaming-logs"></a>Como ativar e exibir o streaming logs
![][BrowseSitesScreenshot]Diagnósticos estão ativados em uma base por aplicativo. Iniciar, navegando até o site que você gostaria de ativar esse recurso.  
  
![][DiagnosticsLogs]No menu Configurações, role até a seção de **monitoramento** e clique em **Logs de diagnóstico (1)**. Em seguida, **Habilitar (2)** **Aplicativo log (sistema de arquivos)** ou **Aplicativo efetuando (blob)** a opção **nível** permite que você altere o nível de gravidade de rastreamentos para capturar. Se você estiver tentando apenas para se familiarizar com o recurso, defina o nível para **detalhado** para garantir que todas as instruções de rastreamento são coletadas.

Clique em **Salvar** na parte superior da lâmina e você estará pronto para exibir logs.

>[AZURE.NOTE] Quanto maior o **nível de gravidade** mais recursos é consumido log e os rastreamentos mais são produzidas. Verifique se o **nível de gravidade** está configurado para o detalhamento correto para uma produção ou site de alto tráfego. 

![][StreamingLogsScreenshot]Para exibir os **logs de streaming** de dentro do portal Azure, clique no **fluxo de Log (1)** também na seção **monitoramento** do menu Configurações. Se seu aplicativo ativamente está escrevendo instruções de rastreamento, você deve vê-los no **streaming (2) logs de interface do usuário** na quase em tempo real.

## <a name="console"></a>Console
O **portal do Azure** fornece acesso ao seu aplicativo do console. Você pode explorar o sistema de arquivos do seu aplicativo e executar scripts do powershell/cmd. São vinculados pelas mesmas permissões definidas como seu código de aplicativo em execução ao executar comandos do console. Acesso a protegido diretórios ou execução de scripts que necessitam de permissões elevadas é bloqueado.  

![][ConsoleScreenshot]No menu Configurações, role para baixo até a seção de **Ferramentas de desenvolvimento** e clique no **Console (1)** e **(2) console** UI abre à direita.

Para se familiarizar com o **console**, tente comandos básicos como:

`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````

<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png
