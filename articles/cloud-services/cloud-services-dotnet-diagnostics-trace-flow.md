<properties
    pageTitle="Rastrear o fluxo em um aplicativo de serviços de nuvem com diagnóstico do Azure | Microsoft Azure"
    description="Adicione as mensagens de rastreamento para um aplicativo do Azure para ajudar a depuração, medir o desempenho, monitoramento, análise de tráfego e muito mais."
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/20/2016"
    ms.author="robb"/>



# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Rastrear o fluxo de um aplicativo de serviços de nuvem com diagnóstico do Azure

Rastreamento é uma maneira de você monitorar a execução de seu aplicativo enquanto ele é executado. Você pode usar as classes [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx)e [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) para gravar informações sobre erros e execução do aplicativo em logs, arquivos de texto ou outros dispositivos para análise posterior. Para obter mais informações sobre o rastreamento, consulte [rastreamento e instrumentação aplicativos](https://msdn.microsoft.com/library/zs6s4h68.aspx).


## <a name="use-trace-statements-and-trace-switches"></a>Usar instruções de rastreamento e opções de rastreamento

Implementar o rastreamento em seu aplicativo de serviços de nuvem, adicionando a [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) para a configuração do aplicativo e fazer chamadas para System.Diagnostics.Trace ou System.Diagnostics.Debug em seu código do aplicativo. Use o arquivo de configuração *App* para funções de trabalho e a *Web. config* para funções da web. Quando você cria um novo serviço hospedado usando um modelo Visual Studio, diagnóstico do Azure é adicionado automaticamente ao projeto e o DiagnosticMonitorTraceListener é adicionada ao arquivo de configuração apropriado para as funções que você adicionar.

Para obter informações sobre a inserção de instruções de rastreamento, consulte [como: adicionar instruções de rastreamento ao código do aplicativo](https://msdn.microsoft.com/library/zd83saa2.aspx).

Colocando [Opções de rastreamento](https://msdn.microsoft.com/library/3at424ac.aspx) em seu código, você pode controlar se rastreamento ocorre e como considerável é. Isso permite monitorar o status do seu aplicativo em um ambiente de produção. Isso é especialmente importante em um aplicativo de negócios que usa vários componentes executando em vários computadores. Para obter mais informações, consulte [como: configurar opções de rastreamento](https://msdn.microsoft.com/library/t06xyy08.aspx).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Configurar o ouvinte de rastreamento em um aplicativo do Azure

Rastreamento, depuração e TraceSource, exigem que você configure "ouvintes" para coletar e gravar as mensagens que são enviadas. Ouvintes coletam, armazenam e encaminhar mensagens de rastreamento. Eles direcionam a saída de rastreamento para um destino apropriado, como um log, uma janela ou um arquivo de texto. Diagnóstico do Azure usa a classe [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) .

Antes de concluir o procedimento a seguir, você deve inicializar o monitor de diagnóstico Azure. Para fazer isso, consulte [Habilitando diagnósticos no Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Observe que se você usar os modelos que são fornecidos pelo Visual Studio, a configuração do ouvinte é adicionada automaticamente para você.


### <a name="add-a-trace-listener"></a>Adicionar um ouvinte de rastreamento

1. Abra o arquivo Web. config ou App para sua função.
2. Adicione o seguinte código para o arquivo. Altere o atributo de versão para usar o número de versão do assembly que você está fazendo referência. A versão do assembly não altera necessariamente com cada versão SDK do Azure, a menos que há atualizações para ele.

    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                  <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
    >[AZURE.IMPORTANT] Verifique se que você tem uma referência de projeto ao assembly Microsoft.WindowsAzure.Diagnostics. Atualize o número de versão no xml acima para corresponder a versão do assembly Microsoft.WindowsAzure.Diagnostics referenciado.

3. Salve o arquivo de configuração.

Para obter mais informações sobre ouvintes, consulte [Ouvintes de rastreamento](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Depois de concluir as etapas para adicionar o ouvinte, você pode adicionar instruções de rastreamento ao seu código.


### <a name="to-add-trace-statement-to-your-code"></a>Para adicionar a instrução de rastreamento para o seu código

1. Abra um arquivo de origem para o seu aplicativo. Por exemplo, o <RoleName>arquivo. cs para a função de trabalho ou da web.
2. Adicione o seguinte usando instrução se ela ainda não tenha sido adicionada:
    ```
        using System.Diagnostics;
    ```
3. Adicione instruções de rastreamento onde você deseja capturar informações sobre o estado do seu aplicativo. Você pode usar uma variedade de métodos para formatar a saída da política de rastreamento. Para obter mais informações, consulte [como: adicionar instruções de rastreamento ao código do aplicativo](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Salve o arquivo de origem.
