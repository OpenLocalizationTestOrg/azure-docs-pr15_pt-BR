<properties
   pageTitle="Depurar seu aplicativo no Visual Studio | Microsoft Azure"
   description="Melhore a confiabilidade e o desempenho dos seus serviços desenvolvendo e depuração-los no Visual Studio em um cluster de desenvolvimento local."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/21/2016"
   ms.author="vturecek;mikhegn"/>

# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Depurar seu aplicativo de serviço tecidos usando Visual Studio

## <a name="debug-a-local-service-fabric-application"></a>Depurar um aplicativo de serviço tecidos local

Você pode economizar tempo e dinheiro Implantando e depuração do aplicativo do Azure serviço tecidos em um cluster de desenvolvimento do computador local. Visual Studio pode implantar o aplicativo para o cluster local e conectar o depurador automaticamente a todas as instâncias do seu aplicativo.

1. Inicie um cluster de desenvolvimento local seguindo as etapas na [configuração do seu ambiente de desenvolvimento do serviço tecidos](service-fabric-get-started.md).

2. Pressione **F5** ou clique **Depurar** > **Iniciar depuração**.

    ![Iniciar a depuração de um aplicativo][startdebugging]

3. Definir pontos de interrupção no seu código e percorrer o aplicativo clicando nos comandos no menu **Depurar** .

    > [AZURE.NOTE] O Visual Studio anexa a todas as instâncias do seu aplicativo. Enquanto você estiver percorrendo o código, pontos de interrupção podem obter atingidos por vários processos resultando em sessões simultâneas. Tente desabilitar os pontos de interrupção após estiver cheguem, fazendo cada ponto de interrupção condicional na identificação de segmento ou usando eventos de diagnóstico.

4. A janela de **Eventos de diagnóstico** abrirá automaticamente para poder visualizar eventos de diagnóstico em tempo real.

    ![Exibir eventos de diagnóstico em tempo real][diagnosticevents]

5. Você também pode abrir a janela de **Eventos de diagnóstico** no Explorador de nuvem.  Em **Tecidos do serviço**, clique com botão direito qualquer nó e escolha **Rastreamentos de Streaming de modo de exibição**.

    ![Abrir a janela de eventos de diagnóstico][viewdiagnosticevents]

    Se você deseja filtrar seus rastreamentos para um aplicativo ou serviço específico, basta habilite streaming rastreamentos desse serviço ou aplicativo específico.

6. Os eventos de diagnósticos podem ser vistos no arquivo **ServiceEventSource.cs** gerado automaticamente e são chamados de código do aplicativo.

    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```

7. A janela de **Eventos de diagnóstico** dá suporte à filtragem, pausando e inspecionar eventos em tempo real.  O filtro é uma pesquisa de cadeia de caracteres simples da mensagem do evento, incluindo seu conteúdo.

    ![Filtrar, pausar e retomar ou inspecionar eventos em tempo real][diagnosticeventsactions]

8. Serviços de depuração é semelhante a depuração de qualquer outro aplicativo. Normalmente, você definirá pontos de interrupção pelo Visual Studio para depuração fácil. Apesar de conjuntos de confiável replicar em vários nós, eles ainda implementam IEnumerable. Isso significa que você pode usar o modo de exibição de resultados no Visual Studio durante a depuração para ver o que você armazenou dentro. Basta defina um ponto de interrupção de qualquer lugar no seu código.

    ![Iniciar a depuração de um aplicativo][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Depurar um aplicativo de serviço tecidos remoto

Se seus aplicativos de serviço tecidos estiver executando em um cluster de estrutura de serviço no Azure, será capaz de depurar remotamente esses, diretamente do Visual Studio.

> [AZURE.NOTE] O recurso requer o [serviço tecidos SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [SDK do Azure para .NET 2,9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [AZURE.WARNING] Depuração remota serve para cenários de desenvolvimento/teste e não deve ser usado em ambientes de produção, devido o impacto sobre os aplicativos em execução.

1. Navegue até seu cluster no **Explorador de nuvem**, clique com botão direito e escolha **Ativar depuração**

    ![Ativar depuração remota][enableremotedebugging]

    Isto irá inicial no processo de habilitar a extensão de depuração remota em nós de cluster, bem como as configurações de rede necessárias.

2. O nó do cluster no **Explorador de nuvem**de atalho e, em seguida, escolha **Anexar depurador**

    ![Anexar depurador][attachdebugger]

3. Na caixa de diálogo **Anexar para processar** , escolha o processo que você deseja depurar e clique em **Anexar**

    ![Escolha processo][chooseprocess]

    O nome do processo que você deseja anexar, é igual ao nome de seu nome de assembly de projeto de serviço.

    O depurador será anexado a todos os nós executando o processo.
    - No caso onde você é depuração de um serviço sem estado, todas as instâncias do serviço em todos os nós fazem parte da sessão de depuração.
    - Se você estiver depuração de um serviço de estado, somente a réplica principal de qualquer partição será ativa e, portanto, identificada pelo depurador. Se a réplica principal move durante a sessão de depuração, o processamento de réplica ainda fará parte da sessão de depuração.
    - Para capturar somente partições relevantes ou instâncias de um determinado serviço, você pode usar pontos de interrupção condicionais interromper somente uma partição específica ou instância.

    ![Ponto de interrupção condicional][conditionalbreakpoint]

    > [AZURE.NOTE] Atualmente não há suporte para depuração de um cluster de serviço tecidos com várias instâncias o mesmo nome executável do serviço.

4. Depois que você concluir a depuração de seu aplicativo, você pode desabilitar a extensão de depuração remota clicando com o cluster no **Explorador de nuvem** e escolha **Desativar depuração**

    ![Desabilitar a depuração remota][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Streaming rastreamentos de um nó de cluster remoto

Você também é capazes de rastreamentos de fluxo diretamente de um nó de cluster remoto para Visual Studio. Esse recurso permite eventos de rastreamento ETW fluxo, produzidos em um nó de cluster do serviço tecidos, diretamente no Visual Studio.

> [AZURE.NOTE] O recurso requer o [serviço tecidos SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [SDK do Azure para .NET 2,9](https://azure.microsoft.com/downloads/).

<!-- -->
> [AZURE.WARNING]Streaming rastreamentos serve para cenários de desenvolvimento/teste e não deve ser usado em ambientes de produção, devido o impacto sobre os aplicativos em execução.
> Em um cenário de produção, você deve confiar em encaminhamento de eventos usando o diagnóstico do Azure.

1. Navegue até seu cluster no **Explorador de nuvem**, clique com botão direito e escolha **Habilitar o Streaming rastreamentos**

    ![Ativar rastreamentos de streaming remotos][enablestreamingtraces]

    Isto irá inicial no processo de habilitar a extensão de rastreamentos streaming em nós de cluster, bem como as configurações de rede necessárias.

2. Expanda o elemento de **nós** no **Explorador de nuvem**, o nó que você deseja transmitir rastreamentos de e escolha **Rastreamentos de Streaming de modo de exibição** de atalho

    ![Modo de exibição remoto streaming rastreamentos][viewremotestreamingtraces]

    Repita a etapa 2 para nós quantas como você deseja ver rastreamentos de. Cada fluxo de nós aparecerá em uma janela dedicada.

    Você agora é capazes de ver os rastreamentos emitidos por estrutura de serviço e seus serviços. Se você quiser filtrar os eventos para mostrar apenas um aplicativo específico, basta digite na caixa Nome do aplicativo no filtro.

    ![Exibindo streaming rastreamentos][viewingstreamingtraces]

4. Depois que terminar de streaming rastreamentos do seu cluster, você pode desabilitar rastreamentos de streaming remotos, clicando com o cluster no **Explorador de nuvem** e escolha **Desativar rastreamentos de Streaming**

    ![Desabilitar rastreamentos streaming remotos][disablestreamingtraces]

## <a name="next-steps"></a>Próximas etapas

- [Um serviço de estrutura de serviço de teste](service-fabric-testability-overview.md).
- [Gerenciar seus aplicativos de serviço tecidos no Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
