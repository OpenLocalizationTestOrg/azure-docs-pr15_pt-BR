<properties
    pageTitle="Habilitar o log de diagnóstico para web apps em um serviço de aplicativo do Azure"
    description="Saiba como habilitar o log de diagnóstico e adicionar instrumentação para seu aplicativo, bem como acessar as informações registradas pelo Azure."
    services="app-service"
    documentationCenter=".net"
    authors="cephalin"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/06/2016"
    ms.author="cephalin"/>

# <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a>Habilitar o log de diagnóstico para web apps em um serviço de aplicativo do Azure

## <a name="overview"></a>Visão geral

Azure fornece diagnóstico interno para ajudar na depuração de um [aplicativo de serviço web app](http://go.microsoft.com/fwlink/?LinkId=529714). Neste artigo, você aprenderá como habilitar o log de diagnóstico e adicionar instrumentação para seu aplicativo, bem como acessar as informações registradas pelo Azure.

Este artigo usa o [Portal do Azure](https://portal.azure.com), PowerShell do Azure e a Interface de linha do Azure (Azure comando) para trabalhar com os logs de diagnóstico. Para obter informações sobre como trabalhar com logs de diagnóstico usando o Visual Studio, consulte [Solução de problemas Azure no Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Diagnósticos do servidor Web e o diagnóstico de aplicativo

Aplicativos do serviço de aplicativo web fornecem funcionalidade de diagnóstico para informações de registro de servidor web e o aplicativo da web. Estes são separadas logicamente em **diagnósticos do servidor web** e o **Diagnóstico de aplicativo**.

### <a name="web-server-diagnostics"></a>Diagnósticos do servidor de Web

Você pode ativar ou desativar os seguintes tipos de logs:

- **Logs de erro detalhados** - informações de erro detalhadas para códigos de status HTTP que indicam uma falha (código de status 400 ou maior). Isso pode conter informações que podem ajudar a determinar por que o servidor retornou o código de erro.
- **Rastreamento de solicitação falha** - informações detalhadas sobre solicitações com falha, incluindo um rastreamento dos componentes IIS usado para processar a solicitação e o tempo gasto em cada componente. Isso pode ser útil se você estiver tentando aumentar o desempenho do site ou isolar o que está causando um erro HTTP específico a ser retornado.
- **Log de servidor de web** - informações sobre transações de HTTP usando o [formato de arquivo de log estendido W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Isso é útil quando determinando geral métricas de site, como o número de solicitações de manipulados ou quantas solicitações são de um endereço IP específico.

### <a name="application-diagnostics"></a>Diagnóstico do aplicativo

Diagnóstico do aplicativo permite que você capture informações produzidas por um aplicativo web. Aplicativos ASP.NET podem usar a classe [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) para registrar informações para o log de diagnóstico do aplicativo. Por exemplo:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Em tempo de execução, você pode recuperar esses logs para ajudar na solução de problemas. Para obter mais informações, consulte [solução de problemas Azure web apps no Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

Serviço de aplicativo web apps também registrar informações de implantação quando você publica conteúdo em um aplicativo web. Isso acontece automaticamente e não há nenhuma definições de configuração de log de implantação. Log de implantação permitirá determinar por que uma implantação falha. Por exemplo, se você estiver usando um script de implantação personalizada, você pode usar o log de implantação para determinar por que o script está falhando.

## <a name="enablediag"></a>Como habilitar o diagnóstico

Para habilitar o diagnóstico no [Portal do Azure](https://portal.azure.com), vá para a lâmina para seu aplicativo web e clique **Configurações > logs de diagnóstico**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Parte de logs](./media/web-sites-enable-diagnostic-log/logspart.png)

Quando você habilita o **Diagnóstico do aplicativo** , você também escolher o **nível**. Essa configuração permite filtrar as informações obtidas para informações **informativo**, **Aviso** ou **erro** . Essa configuração **detalhada** registrará todas as informações produzidas pelo aplicativo.

> [AZURE.NOTE] Ao contrário de alterar o arquivo Web. config, habilitar o diagnóstico de aplicativo ou alterando os níveis de log de diagnóstico Lixeira não o domínio de aplicativo que o aplicativo é executado dentro.

Na guia **Configurar** [portal clássico](https://manage.windowsazure.com) Web app, você pode selecionar **armazenamento** ou **sistema de arquivos** de **log do servidor web**. Selecionar **armazenamento** permite que você selecione uma conta de armazenamento e um contêiner de blob que os logs serão gravados. Todos os outros logs de **Diagnóstico do site** são gravados apenas o sistema de arquivos.

Guia de **Configurar** [portal clássico](https://manage.windowsazure.com) Web app também tem configurações adicionais de diagnóstico do aplicativo:

* **Sistema de arquivos** - armazena as informações de diagnóstico do aplicativo para o sistema de arquivos de aplicativo web. Esses arquivos podem ser acessados por FTP ou baixados como um arquivo Zip usando o PowerShell do Azure ou uma Interface de linha do Azure (Azure comando).
* **Armazenamento de tabela** - armazena as informações de diagnóstico do aplicativo no nome da conta de armazenamento do Azure e tabela especificado.
* **Armazenamento de blob** - armazena as informações de diagnóstico do aplicativo no contêiner de conta de armazenamento do Azure e blob especificado.
* **Período de retenção** - por padrão, logs não são automaticamente excluídos do **armazenamento**de blob. Selecione **Definir retenção** e insira o número de dias para manter logs se você deseja excluir automaticamente logs.

>[AZURE.NOTE] Se você [Gerar teclas de acesso da sua conta de armazenamento](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys), você deverá redefinir a configuração de log respectivos usar as teclas atualizadas. Para fazer isso:
>
> 1. Na guia **Configurar** , defina o recurso de log respectivos como **desativado**. Salve sua configuração.
> 2. Habilite o log do blob de conta de armazenamento ou da tabela novamente. Salve sua configuração.

Qualquer combinação de sistema de arquivos, armazenamento de tabela ou armazenamento de blob pode ser habilitada ao mesmo tempo e têm configurações de nível de log individuais. Por exemplo, você talvez queira log de erros e avisos para armazenamento de blob como uma solução de log de longo prazo, ao ativar o log de sistema de arquivos com um nível de detalhado.

Embora todos os três locais de armazenamento fornecem as mesmas informações básicas para eventos registrados, **armazenamento de tabela** e **armazenamento de blob** efetuar informações adicionais, como a ID da instância, identificação de segmento e um carimbo de hora mais granular (formato de escala) de log do **sistema de arquivos**.

> [AZURE.NOTE] Informações armazenadas em **armazenamento de tabela** ou o **armazenamento de blob** só podem ser acessadas usando um cliente de armazenamento ou um aplicativo que pode trabalhar diretamente com esses sistemas de armazenamento. Por exemplo, Visual Studio 2013 contém um Gerenciador de armazenamento que podem ser usados para explorar o armazenamento de tabela ou blob e HDInsight pode acessar dados armazenados em armazenamento de blob. Você também pode escrever um aplicativo que acessa o armazenamento do Azure usando um dos [SDKs do Azure](/downloads/#).

> [AZURE.NOTE] Diagnósticos também podem ser habilitados do PowerShell do Azure usando o cmdlet **Set-AzureWebsite** . Se você não tiver instalado o Azure PowerShell ou não tiver configurado para usar sua assinatura do Azure, veja [como usar o PowerShell do Azure](/develop/nodejs/how-to-guides/powershell-cmdlets/).

##<a name="download"></a>Como: baixar logs

Informações de diagnóstico armazenadas para o sistema de arquivos de aplicativo da web podem ser acessadas diretamente usando FTP. Ele também pode ser baixado como um arquivo Zip usando o PowerShell do Azure ou a Interface de linha do Azure.

A estrutura de diretório que os logs são armazenados na é da seguinte maneira:

* **Logs de aplicativos** - /LogFiles/aplicativo /. Esta pasta contém um ou mais arquivos de texto que contém informações produzidas pelo log do aplicativo.

* **Falha na solicitação de rastreamentos** - / arquivos de log/W3SVC # /. Esta pasta contém um arquivo XSL e um ou mais arquivos XML. Certifique-se de que você baixar o arquivo XSL no mesmo diretório como XML arquivo (s) porque o arquivo XSL fornece funcionalidade para formatação e filtrar o conteúdo dos arquivos XML quando visualizados no Internet Explorer.

* **Logs de erro detalhadas** - /LogFiles/DetailedErrors /. Esta pasta contém um ou mais arquivos. htm que fornecem informações extensa para os erros HTTP ocorridos.

* **Registros do servidor web** - /LogFiles/http/RawLogs. Esta pasta contém um ou mais arquivos de texto formatados com o [formato de arquivo de log estendido W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).

* **Logs de implantação** - / arquivos de log/gito. Esta pasta contém logs gerados pelos processos de implantação interna usados por aplicativos web Azure, bem como faz para implantações de gito.

### <a name="ftp"></a>FTP

Para acessar as informações de diagnóstico usando FTP, visite o **painel** de seu aplicativo web no [portal clássico](https://manage.windowsazure.com). Na seção **rapidamente** , use o link de **FTP Logs de diagnóstico** para acessar os arquivos de log usando FTP. A entrada do **Usuário de implantação/FTP** lista o nome de usuário que deve ser usado para acessar o site FTP.

> [AZURE.NOTE] Se a entrada de **Usuário de implantação/FTP** não está definida ou se você tiver esquecido a senha para esse usuário, você pode criar um novo usuário e senha usando o link **Redefinir credenciais de implantação** na seção **rapidamente** do **painel**.

### <a name="download-with-azure-powershell"></a>Baixar com PowerShell Azure

Para baixar os arquivos de log, inicie uma nova instância do PowerShell do Azure e use o seguinte comando:

    Save-AzureWebSiteLog -Name webappname

Isso salvará os logs para o aplicativo web especificado pela **-nome** parâmetro em um arquivo denominado **logs.zip** na pasta atual.

> [AZURE.NOTE] Se você não tiver instalado o Azure PowerShell ou não tiver configurado para usar sua assinatura do Azure, veja [como usar o PowerShell do Azure](/develop/nodejs/how-to-guides/powershell-cmdlets/).

### <a name="download-with-azure-command-line-interface"></a>Baixar com o Azure Interface de linha de comando

Para baixar os arquivos de log usando a Interface de linha de comando do Azure, abra um novo prompt de comando, PowerShell, Bash ou sessão de Terminal e digite o seguinte comando:

    azure site log download webappname

Isso salvará os logs do aplicativo web chamado 'webappname' para um arquivo denominado **diagnostics.zip** na pasta atual.

> [AZURE.NOTE] Se você não instalou a Interface de linha do Azure (Azure comando) ou não tiver configurado para usar sua assinatura do Azure, veja [como usar CLI do Azure](../xplat-cli-install.md).

## <a name="how-to-view-logs-in-application-insights"></a>Como: exibir logs na obtenção de informações de aplicativo

Ideias de aplicativo do Visual Studio oferece ferramentas para filtragem e pesquisa logs e para correlação os logs com solicitações e outros eventos.

1. Adicione o SDK de obtenção de informações do aplicativo ao seu projeto no Visual Studio.
 * No Solution Explorer, clique com botão direito seu projeto e escolha Adicionar ideias de aplicativo. Você será orientado etapas que incluem a criação de um recurso de obtenção de informações do aplicativo. [Saiba Mais](../application-insights/app-insights-asp-net.md)
2. Adicione o pacote de ouvinte de rastreamento ao seu projeto.
 * Clique com botão direito seu projeto e escolha gerenciar pacotes do NuGet. Selecione `Microsoft.ApplicationInsights.TraceListener` [Saiba mais](../application-insights/app-insights-asp-net-trace-logs.md)
3. Carregue seu projeto e executá-lo para gerar dados de log.
4. No [Portal do Azure](https://portal.azure.com/), navegue até o novo recurso de obtenção de informações do aplicativo e abrir a **pesquisa**. Você verá os dados de log, juntamente com a solicitação, uso e outra telemetria. Alguns telemetria, pode levar alguns minutos para chegar: clique em Atualizar. [Saiba Mais](../application-insights/app-insights-diagnostic-search.md)

[Saiba mais sobre o desempenho do controle com a obtenção de informações de aplicativo](../application-insights/app-insights-azure-web-apps.md)

##<a name="streamlogs"></a>Como: logs de fluxo

Ao desenvolver um aplicativo, geralmente é útil ver informações de log em quase em tempo real. Isso pode ser feito por streaming informações de registro para seu ambiente de desenvolvimento usando o PowerShell do Azure ou a Interface de linha do Azure.

> [AZURE.NOTE] Alguns tipos de registro em log buffer gravem o arquivo de log, que pode resultar em eventos de fora de ordem no fluxo. Por exemplo, uma entrada de log de aplicativo que ocorre quando um usuário visita uma página pode ser exibida no fluxo antes da entrada de log HTTP correspondente para a solicitação de página.

> [AZURE.NOTE] Log streaming também transmitir informações gravadas em qualquer arquivo de texto armazenado na **D:\\inicial\\arquivos de log\\ ** pasta.

### <a name="streaming-with-azure-powershell"></a>Streaming com o Azure PowerShell

Para transmitir informações de registro, inicie uma nova instância do PowerShell do Azure e use o seguinte comando:

    Get-AzureWebSiteLog -Name webappname -Tail

Se conectará aplicativo web especificado pela **-nome** parâmetro e comece a streaming informações na janela do PowerShell conforme ocorrem eventos de log do aplicativo web. Qualquer informação gravada nos arquivos que terminam em. htm,. log ou. txt que estão armazenados no diretório /LogFiles (d: / home/arquivos de log) serão ser transmitida no console local.

Para filtrar eventos específicos, como os erros, use o **-mensagem** parâmetro. Por exemplo:

    Get-AzureWebSiteLog -Name webappname -Tail -Message Error

Para filtrar os tipos de log específico, como HTTP, use o **-caminho** parâmetro. Por exemplo:

    Get-AzureWebSiteLog -Name webappname -Tail -Path http

Para ver uma lista de caminhos disponíveis, use o parâmetro - ListPath.

> [AZURE.NOTE] Se você não tiver instalado o Azure PowerShell ou não tiver configurado para usar sua assinatura do Azure, veja [como usar o PowerShell do Azure](/develop/nodejs/how-to-guides/powershell-cmdlets/).

### <a name="streaming-with-azure-command-line-interface"></a>Fluxo com o Azure Interface de linha de comando

Para transmitir informações de registro, abra um novo prompt de comando, PowerShell, Bash ou sessão de Terminal e digite o seguinte comando:

    azure site log tail webappname

Isso se conectará aplicativo web chamado 'webappname' e começar a streaming informações à janela conforme ocorrem eventos de log do aplicativo web. Qualquer informação gravada nos arquivos que terminam em. htm,. log ou. txt que estão armazenados no diretório /LogFiles (d: / home/arquivos de log) serão ser transmitida no console local.

Para filtrar eventos específicos, como os erros, use o **-filtro** parâmetro. Por exemplo:

    azure site log tail webappname --filter Error

Para filtrar os tipos de log específico, como HTTP, use o **– caminho** parâmetro. Por exemplo:

    azure site log tail webappname --path http

> [AZURE.NOTE] Se você não instalou a Interface de linha do Azure ou não tiver configurado para usar sua assinatura do Azure, veja [como para usar o Azure Interface de linha](../xplat-cli-install.md).

##<a name="understandlogs"></a>Como: Noções básicas sobre logs de diagnóstico

### <a name="application-diagnostics-logs"></a>Logs de diagnóstico do aplicativo

Diagnóstico do aplicativo armazena informações em um formato específico para aplicativos .NET, dependendo se você armazena logs para o sistema de arquivos, armazenamento de tabela ou armazenamento de blob. O conjunto base de dados armazenados é o mesmo em todos os três tipos de armazenamento - a data e a hora em que o evento ocorreu, a ID de processo que gerou o evento, o tipo de evento (informação, aviso, erro) e a mensagem de evento.

__Sistema de arquivos__

Cada linha conectado ao sistema de arquivos ou recebido usando streaming será no seguinte formato:

    {Date}  PID[{process id}] {event type/level} {message}

Por exemplo, um evento de erro apareceriam similar ao seguinte:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Registro em log para o sistema de arquivo fornece as informações mais básicas dos três métodos disponíveis, fornecendo somente a hora, id de processo, nível de eventos e mensagem.

__Armazenamento de tabela__

Quando o registro em log para o armazenamento de tabela, propriedades adicionais são usadas para facilitar a pesquisa os dados armazenados na tabela, bem como as informações mais granulares no evento. As seguintes propriedades (colunas) são usadas para cada entidade (linha) armazenada na tabela.

Nome da propriedade|Formato do valor
---|---
PartitionKey|Data/hora do evento no formato de yyyyMMddHH
RowKey|Um valor de GUID que identifica com exclusividade esta entidade
Carimbo de hora|A data e a hora em que o evento ocorreu
EventTickCount|A data e a hora em que o evento ocorreu, no formato de escala (maior precisão)
ApplicationName|O nome do aplicativo web
Nível|Nível de evento (por exemplo, erro, aviso, informações)
Iddoevento|A identificação deste evento<p><p>O padrão é 0 se nenhum especificado
Identificação da instância|Instância do aplicativo web que ocorreu o mesmo no
PID|ID do processo
TID|O ID de segmento do segmento que produziu o evento
Mensagem|Mensagem de detalhes do evento

__Armazenamento de blob__

Quando o registro em log para armazenamento de blob, os dados são armazenados no formato de valores separados por vírgula (CSV). Semelhante ao armazenamento de tabela, campos adicionais são registrados para fornecer informações mais granulares sobre o evento. As seguintes propriedades são usadas para cada linha no CSV:

Nome da propriedade|Formato do valor
---|---
Data|A data e a hora em que o evento ocorreu
Nível|Nível de evento (por exemplo, erro, aviso, informações)
ApplicationName|O nome do aplicativo web
Identificação da instância|Instância do que o evento ocorreu no aplicativo web
EventTickCount|A data e a hora em que o evento ocorreu, no formato de escala (maior precisão)
Iddoevento|A identificação deste evento<p><p>O padrão é 0 se nenhum especificado
PID|ID do processo
TID|O ID de segmento do segmento que produziu o evento
Mensagem|Mensagem de detalhes do evento

Os dados armazenados em um blob seria similares ao seguinte:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [AZURE.NOTE] A primeira linha do log conterá os cabeçalhos de coluna, conforme representado neste exemplo.

### <a name="failed-request-traces"></a>Falha na solicitação rastreamentos

Falha na solicitação de rastreamentos são armazenados em arquivos XML chamados __. XML do fr # # #__. Para tornar mais fácil de ler as informações registradas, uma folha de estilo XSL chamada __freb.xsl__ é fornecida na mesma pasta que os arquivos XML. Abrir um dos arquivos XML no Internet Explorer, você usará a folha de estilo XSL para fornecer uma exibição formatada as informações de rastreamento. Isso aparecerão similar ao seguinte:

![solicitação falha exibida no navegador](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### <a name="detailed-error-logs"></a>Logs de erro detalhadas

Os logs de erro detalhadas são documentos HTML que fornecem informações mais detalhadas sobre erros de HTTP ocorridos. Como são simplesmente documentos HTML, eles podem ser exibidos usando um navegador da web.

### <a name="web-server-logs"></a>Registros do servidor Web

Registros do servidor web são formatados usando o [formato de arquivo de log estendido W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Essas informações podem ser lidos usando um editor de texto ou analisados usando utilitários como o [Analisador de Log](http://go.microsoft.com/fwlink/?LinkId=246619).

> [AZURE.NOTE] Os logs produzidos por aplicativos web Azure não suportam os campos __s-computername__, __s-ip__ou __versão de cs__ .

##<a name="nextsteps"></a>Próximas etapas

- [Como monitorar aplicativos Web](/manage/services/web-sites/how-to-monitor-websites/)
- [Azure web apps no Visual Studio de solução de problemas](web-sites-dotnet-troubleshoot-visual-studio.md)
- [Analisar Logs de aplicativo web na HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

## <a name="whats-changed"></a>O que mudou
* Para um guia para a alteração de sites para o serviço de aplicativo consulte: [o serviço de aplicativo do Azure e seu impacto sobre serviços existentes do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para um guia para a alteração do portal do antigo para o novo portal consulte: [referência para navegar no portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715)
 
