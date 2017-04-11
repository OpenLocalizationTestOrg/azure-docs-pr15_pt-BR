<properties
    pageTitle="Carga testar seu aplicativo usando os serviços de equipe do Visual Studio | Microsoft Azure"
    description="Aprenda a enfatizar teste seus aplicativos do Azure serviço tecidos usando os serviços de equipe do Visual Studio."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="07/29/2016"
    ms.author="cawa" />

# <a name="load-test-your-application-by-using-visual-studio-team-services"></a>Carga testar seu aplicativo usando os serviços de equipe do Visual Studio

Este artigo mostra como usar recursos de teste de carga do Microsoft Visual Studio para testar a sobrecarga de um aplicativo. Ele usa um estrutura de serviço do Azure com informações de estado de serviço back-end e um estado de serviço web front-end. O aplicativo de exemplo usado aqui é um simulador de localização de avião. Você fornecer um ID de avião, horário de partidas e destino. Back-end do aplicativo processa as solicitações e o front-end exibe em um mapa o avião que corresponde a um critério.

O diagrama a seguir ilustra o aplicativo de serviço tecidos que você vai ser testando.

![Diagrama do aplicativo exemplo avião local][0]

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, você precisa fazer o seguinte:

- Obtenha uma conta de serviços de equipe do Visual Studio. Você pode obter uma gratuitamente em [Serviços de equipe do Visual Studio](https://www.visualstudio.com).
- Obtenha e instale o Visual Studio 2013 ou Visual Studio 2015. Este artigo usa o Visual Studio 2015 Enterprise edition, mas o Visual Studio 2013 e outras edições devem funcionar da mesma forma.
- Implante seu aplicativo para um ambiente de teste. Veja [como implantar aplicativos para um cluster remoto usando o Visual Studio](service-fabric-publish-app-remote-cluster.md) para obter informações sobre isso.
- Compreenda o padrão de uso do aplicativo. Essas informações são usadas para simular o padrão de carga.
- Compreenda a meta para seus testes de carga. Isso ajuda você a interpretar e analisar os resultados de teste de carga.

## <a name="create-and-run-the-web-performance-and-load-test-project"></a>Criar e executar o projeto de teste de carga e desempenho da Web

### <a name="create-a-web-performance-and-load-test-project"></a>Criar um projeto de teste de carga e desempenho da Web

1. Abra o Visual Studio 2015. Escolha **arquivo** > **novo** > **projeto** na barra de menus para abrir a caixa de diálogo **Novo projeto** .

2. Expanda o nó **Visual c#** e escolha **teste** > **desempenho da Web e o projeto de teste de carga**. Dê um nome para o projeto e, em seguida, escolha o botão de **Okey** .

    ![Captura de tela da caixa de diálogo Novo projeto][1]

    Você deve ver um novo projeto de teste de carga e desempenho da Web no Solution Explorer.

    ![Captura de tela do Gerenciador de soluções mostrando o novo projeto][2]

### <a name="record-a-web-performance-test"></a>Registro de um teste de desempenho da web

1. Abra o projeto de webtest.

2. Escolha o ícone **Adicionar gravação** para iniciar uma sessão de gravação no seu navegador.

    ![Captura de tela do ícone Adicionar gravação em um navegador][3]

    ![Captura de tela do botão gravar em um navegador][4]

3. Navegue até o aplicativo de serviço tecidos. O painel de gravação deve mostrar as solicitações da web.

    ![Captura de tela das solicitações da web no painel de gravação][5]

4. Execute uma sequência de ações que você espera que os usuários devem executar. Essas ações são usadas como um padrão para gerar a carga.

5. Quando terminar, escolha o botão **Parar** para interromper a gravação.

    ![Captura de tela do botão Parar][6]

    O projeto de WebTest no Visual Studio deve ter capturado uma série de solicitações. Parâmetros dinâmicos são substituídos automaticamente. Neste ponto, você pode excluir qualquer solicitações de dependência extra e repetidos que não fazem parte de seu cenário de teste.

6. Salve o projeto e, em seguida, escolha o comando **Executar teste** para executar o teste de desempenho da web localmente e garantir que tudo funciona corretamente.

    ![Captura de tela do comando Executar teste][7]

### <a name="parameterize-the-web-performance-test"></a>Parametrizar o teste de desempenho da web

Você pode parametrizar o teste de desempenho de web convertê-la para um teste de desempenho da web codificado e editando o código. Como alternativa, você pode vincular o teste de desempenho da web para uma lista de dados para que o teste itera através dos dados. Consulte [gerar e executar um teste de desempenho da web codificado](https://msdn.microsoft.com/library/ms182552.aspx) para obter detalhes sobre como converter o teste de desempenho da web em um teste codificado. Consulte [Adicionar uma fonte de dados para um desempenho web testar](https://msdn.microsoft.com/library/ms243142.aspx) para obter informações sobre como vincular dados a um teste de desempenho da web.

Neste exemplo, podemos vai converter o teste de desempenho da web em um teste codificado para que possa substituir a ID de avião com um GUID gerado e adicionar mais solicitações de enviar voos para locais diferentes.

### <a name="create-a-load-test-project"></a>Criar um projeto de teste de carga

Um projeto de teste de carga é composto por um ou mais cenários descritos pelo teste de desempenho da web e teste de unidade, juntamente com as configurações de teste de carga especificado adicional. As etapas a seguir mostram como criar um projeto de teste de carga:

1. No menu de atalho do seu projeto de teste de carga e desempenho da Web, escolha **Adicionar** > **Teste de carga**. No Assistente de **Teste de carga** , escolha o botão **Avançar** para definir as configurações de teste.

2. Na seção **Padrão de carga** , escolha se você deseja uma carga de usuário constante ou uma carga por etapa, que começa com alguns usuários e aumenta os usuários ao longo do tempo.

    Se você tiver uma boa estimativa da quantidade de carga de usuário e deseja ver como o sistema atual executa, escolha **Carregar constante**. Se seu objetivo for saber se o sistema executa consistentemente em várias cargas, escolha **Carga por etapa**.

3. Na seção **Test Mix** , escolha o botão **Adicionar** e selecione o teste que você deseja incluir no teste de carga. Você pode usar a coluna de **distribuição** para especificar a porcentagem do totais testes executado para cada teste.

4. Na seção **Configurações de executar** , especifique a duração de teste de carga.

    >[AZURE.NOTE] A opção **Test Iterations** está disponível apenas quando você executa um teste de carga localmente usando o Visual Studio.

5. Na seção **local** **Executar**configurações, especifique o local onde as solicitações de teste de carga são geradas. O assistente pode solicitar que você faça logon em sua conta de serviços de equipe. Faça logon e escolha uma localização geográfica. Quando terminar, escolha o botão **Concluir** .

6. Após o teste de carga é criado, abra o projeto LoadTest e escolha execução configuração, como **Configurações de executar**atual > **Settings1 executar [Active]**. Isso abre as configurações de execução na janela de **Propriedades** .

7. Na seção **resultados** da janela de propriedades **Executar configurações** , a configuração de **Armazenamento de detalhes de intervalo** deve ter **Nenhum** como seu valor padrão. Altere esse valor para **Todos os detalhes individuais** para obter mais informações sobre a carga resultados de teste. Para obter mais informações sobre como se conectar aos serviços de equipe do Visual Studio e executar um teste de carga, consulte [Carregar testes](https://www.visualstudio.com/load-testing.aspx) .

### <a name="run-the-load-test-by-using-visual-studio-team-services"></a>Executar o teste de carga usando os serviços de equipe do Visual Studio

Escolha o comando **Executar teste de carga** para iniciar a execução de teste.

![Captura de tela do comando Executar teste de carga][8]

## <a name="view-and-analyze-the-load-test-results"></a>Exibir e analisar os resultados de teste de carga

Como a carga de teste progride, as informações de desempenho será incluída no gráfico. Você verá algo semelhante ao gráfico a seguir.

![Captura de tela do gráfico de desempenho de resultados de teste de carga][9]

1. Escolha o link **baixar relatório** próximo à parte superior da página. Após baixar o relatório, escolha o botão **Exibir relatório** .

    Na guia **gráfico** , você pode ver gráficos para vários contadores de desempenho. Na guia **Resumo** , os resultados de teste geral aparecem. Na guia **tabelas** mostra o número total de testes de carga aprovados e reprovados.

2. Escolha os links de número no **teste** > **Falha** e os **erros** > **contagem** de colunas para ver os detalhes do erro.

    Na guia **detalhes** mostra informações virtuais de cenário de usuário e teste para solicitações com falha. Esses dados podem ser úteis se o teste de carga inclui vários cenários.

Consulte [Analisando carregar resultados de teste no modo de exibição de gráficos de análise de teste carregar](https://www.visualstudio.com/load-testing.aspx) para obter mais informações sobre como exibir resultados de teste de carga.

## <a name="automate-your-load-test"></a>Automatizar o teste de carga

Visual Studio Team Services carregar Test fornece APIs para ajudá-lo a gerenciar testes de carga e analisar os resultados de uma conta de serviços de equipe. Consulte [APIs do restante de teste de carga de nuvem](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas
- [Monitoramento e diagnosticar serviços em uma configuração de desenvolvimento de máquina local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png
