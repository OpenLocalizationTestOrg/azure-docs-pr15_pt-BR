<properties
   pageTitle="Implantar e gerenciar topologias Apache tempestade em HDInsight | Microsoft Azure"
   description="Saiba como implantar, monitorar e gerenciar topologias Apache tempestade usando o painel de tempestade em HDInsight. Use ferramentas de Hadoop para Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="deploy-and-manage-apache-storm-topologies-on-windows-based-hdinsight"></a>Implantar e gerenciar topologias Apache tempestade em HDInsight baseado no Windows

No painel de tempestade permite que você facilmente implantar e executar topologias Apache tempestade ao cluster HDInsight usando o navegador da web. Você também pode usar o painel para monitorar e gerenciar topologias em execução. Se você usar Visual Studio, as ferramentas de HDInsight para Visual Studio fornecem recursos semelhantes no Visual Studio.

No painel de tempestade e os recursos de tempestade nas ferramentas HDInsight contam com a API REST tempestade, que pode ser usado para criar suas próprias de monitoramento e soluções de gerenciamento.

> [AZURE.IMPORTANT] As etapas neste documento exigem uma tempestade baseado no Windows em cluster HDInsight. Para obter informações sobre como usar um cluster baseado em Linux, consulte [implantar e gerenciar topologias Apache tempestade em HDInsight baseado em Linux](hdinsight-storm-deploy-monitor-topology-linux.md)

##<a name="prerequisites"></a>Pré-requisitos

* **Apache tempestade em HDInsight** - consulte <a href="../hdinsight-storm-getting-started/" target="_blank">Introdução ao Apache tempestade em HDInsight</a> para obter etapas sobre como criar um cluster

* Para o **Painel de tempestade**: um navegador da web moderna que ofereça suporte a HTML5

* Para **Visual Studio** - SDK do Azure 2.5.1 ou mais recente e as ferramentas de HDInsight para Visual Studio. Consulte <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Introdução ao uso de ferramentas de HDInsight para Visual Studio</a> para instalar e configurar as ferramentas de HDInsight para Visual Studio.

    Uma das seguintes versões do Visual Studio:

    * O Visual Studio 2012 com <a href="http://www.microsoft.com/download/details.aspx?id=39305" target="_blank">atualização 4</a>

    * Visual Studio de 2013 com <a href="http://www.microsoft.com/download/details.aspx?id=44921" target="_blank">atualização 4</a> ou <a href="http://go.microsoft.com/fwlink/?LinkId=517284" target="_blank">Comunidade de 2013 do Visual Studio</a>

    * <a href="http://visualstudio.com/downloads/visual-studio-2015-ctp-vs" target="_blank">CTP6 de 2015 do Visual Studio</a>

    > [AZURE.NOTE] As ferramentas de HDInsight para Visual Studio somente suportamos tempestade na versão de cluster HDInsight 3,2.

##<a name="storm-dashboard"></a>Painel de tempestade

O painel de tempestade é uma página da web disponível em seu cluster tempestade. A URL é **https://&lt;clustername >.azurehdinsight.net/**, onde **clustername** é o nome do seu tempestade em cluster HDInsight.

Na parte superior do painel tempestade, selecione **Enviar topologia**. Siga as instruções na página para executar uma topologia de amostra ou para carregar e executar uma topologia que você criou.

![a página de topologia de enviar][storm-dashboard-submit]

###<a name="storm-ui"></a>Tempestade de interface do usuário

No painel de tempestade, selecione o link de **Tempestade de interface do usuário** . Isto irá exibir informações sobre o cluster, além de qualquer topologias em execução.

![a interface de usuário de tempestade][storm-dashboard-ui]

> [AZURE.NOTE] Com algumas versões do Internet Explorer, você pode descobrir que a IU tempestade não atualizadas após você visitou primeiro-lo. Por exemplo, ele não pode mostrar as novas topologias enviado ou pode exibir uma topologia como ativa quando você o desativado anteriormente. A Microsoft está ciente desse problema e está trabalhando em uma solução.

####<a name="main-page"></a>Página principal

A página principal da interface do usuário tempestade fornece as seguintes informações:

* **Cluster resumo**: informações básicas sobre o cluster tempestade.

* **Topologia de resumo**: uma lista de execução topologias. Use os links desta seção para exibir mais informações sobre topologias específico.

* **Supervisor resumo**: informações sobre o supervisor de tempestade.

* **Configuração de Nimbus**: configuração Nimbus para o cluster.

####<a name="topology-summary"></a>Resumo de topologia

Selecionando um link da seção **topologia resumo** exibe as seguintes informações sobre a topologia:

* **Resumo de topologia**: informações básicas sobre a topologia.

* **Ações de topologia**: ações de gerenciamento de que você pode executar para a topologia.

    * **Ativar**: processamento de currículos de uma topologia desativado.

    * **Desativar**: pausa uma topologia em execução.

    * **Rebalancear**: ajustará o paralelismo da topologia. Você deve rebalancear topologias em execução depois de alterar o número de nós no cluster. Isso permite a topologia ajustar paralelismo para compensar o número de aumento ou diminuição de nós no cluster.

        Para obter mais informações, consulte <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Noções básicas sobre o paralelismo de uma topologia de tempestade</a>.

    * **Eliminar**: termina uma topologia de tempestade após o tempo limite especificado.

* **Estatísticas de topologia**: estatísticas sobre a topologia. Use os links na coluna da **janela** para definir o intervalo de tempo para as entradas restantes na página.

* **Spouts**: os spouts usados pela topologia. Use os links desta seção para exibir mais informações sobre spouts específico.

* **Bolts**: os práticos usados pela topologia. Use os links desta seção para exibir mais informações sobre parafusos específico.

* **Configuração de topologia**: A configuração da topologia selecionada.

####<a name="spout-and-bolt-summary"></a>Resumo de raio e spout

Selecionando um spout as seções **Spouts** ou **Bolts** exibe as seguintes informações sobre o item selecionado:

* **Componente resumo**: informações básicas sobre a spout ou raio.

* **Estatísticas de spout/raio**: estatísticas sobre a spout ou raio. Use os links na coluna da **janela** para definir o intervalo de tempo para as entradas restantes na página.

* **Estatísticas de entrada** (somente para incluir): informações sobre os fluxos de entrada consumida pelo raio.

* **Estatísticas de saída**: informações sobre os fluxos emitida por isso spout ou incluir.

* **Executores**: informações sobre as instâncias da spout ou raio. Selecione a entrada de **porta** para um executor específico exibir um log de informações de diagnóstico produzidas dessa instância.

* **Erros**: qualquer informação de erro para isso spout ou incluir.

##<a name="hdinsight-tools-for-visual-studio"></a>Ferramentas de HDInsight para Visual Studio

As ferramentas de HDInsight pode ser usadas para enviar c# ou híbrido topologias ao cluster tempestade. As etapas a seguir usam um aplicativo de amostra. Para obter informações sobre como criar seus próprio topologias usando as ferramentas de HDInsight, consulte [desenvolver c# topologias usando as ferramentas de HDInsight para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Use as etapas a seguir para implantar uma amostra em seu tempestade em cluster de HDInsight, e em seguida, exibir e gerenciar a topologia.

1. Se você já não instalou a versão mais recente das ferramentas de HDInsight para Visual Studio, consulte <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Introdução ao uso de ferramentas de HDInsight para Visual Studio</a>.

2. Abra o Visual Studio, selecione **arquivo** > **novo** > **projeto**.

3. Na caixa de diálogo **Novo projeto** , expanda **instalados** > **modelos**e selecione **HDInsight**. Na lista de modelos, selecione **Tempestade amostra**. Na parte inferior da caixa de diálogo, digite um nome para o aplicativo.

    ![imagem](./media/hdinsight-storm-deploy-monitor-topology/sample.png)

1. No **Solution Explorer**, clique com botão direito do projeto e selecione **Enviar para tempestade em HDInsight**.

    > [AZURE.NOTE] Se solicitado, digite as credenciais de logon para sua assinatura do Azure. Se você tiver mais de uma assinatura, faça logon naquele que contém sua tempestade em cluster HDInsight.

2. Selecione seu tempestade em cluster HDInsight na lista suspensa de **Tempestade Cluster** e, em seguida, selecione **Enviar**. Você pode monitorar se o envio for bem-sucedida, usando a janela de **saída** .

3. Quando a topologia foi enviada com êxito, as **Topologias de tempestade** para o cluster deverá aparecer. Selecione a topologia na lista para exibir informações sobre a topologia em execução.

    ![monitor do Visual studio](./media/hdinsight-storm-deploy-monitor-topology/vsmonitor.png)

    > [AZURE.NOTE] Você também pode exibir **Tempestade topologias** do **Server Explorer** expandindo **Azure** > **HDInsight**, em seguida, clicando uma tempestade em cluster HDInsight e selecionando o **Modo de exibição tempestade topologias**.

    Selecione a forma da spouts ou parafusos para exibir informações sobre esses componentes. Uma nova janela será aberta para cada item selecionado.
    
    > [AZURE.NOTE] O nome da topologia é o nome da classe da topologia (neste caso, `HelloWord`,) com um carimbo de hora acrescentado.

4. No modo de exibição **Resumo da topologia** , selecione **eliminar** para interromper a topologia.

    > [AZURE.NOTE] Topologias de tempestade continuam executando até que eles são interrompidos ou cluster é excluído.

##<a name="rest-api"></a>API REST

A interface de usuário de tempestade baseia-se na parte superior da API REST, para que você possa realizar gerenciamento e funcionalidade de monitoramento usando a API REST semelhantes. Você pode usar a API REST para criar ferramentas personalizadas para gerenciar e monitorar topologias tempestade.

Para obter mais informações, consulte [Tempestade API de REST de interface do usuário](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md). As informações a seguir são específicas para usar a API REST com Apache tempestade em HDInsight.

###<a name="base-uri"></a>Base de URI

O URI de base para a API REST em clusters de HDInsight é **https://&lt;clustername >.azurehdinsight.net/stormui/api/v1/**, onde **clustername** é o nome do seu tempestade em cluster HDInsight.

###<a name="authentication"></a>Autenticação

Solicitações da a API REST devem usar **autenticação básica**, para que você usar o nome do administrador de cluster HDInsight e a senha.

> [AZURE.NOTE] Porque a autenticação básica é enviada usando texto não criptografado, você deve **sempre** usar HTTPS para proteger a comunicação com o cluster.

###<a name="return-values"></a>Valores de retorno

Informações que são retornadas da API REST só podem ser útil de dentro do cluster ou máquinas virtuais em uma rede Virtual Azure mesmo como cluster. Por exemplo, o nome de domínio totalmente qualificado (FQDN) retornado para os servidores de Zookeeper não será acessível pela Internet.

##<a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como implantar e monitorar topologias usando o painel de tempestade, saiba como:

* [Desenvolver c# topologias usando as ferramentas de HDInsight para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Desenvolver topologias baseado em Java usando Maven](hdinsight-storm-develop-java-topology.md)

Para obter uma lista das mais topologias de exemplo, consulte [topologias de exemplo para tempestade em HDInsight](hdinsight-storm-example-topology.md).

[hdinsight-dashboard]: ./media/hdinsight-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/hdinsight-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/hdinsight-storm-deploy-monitor-topology/storm-ui-summary.png
