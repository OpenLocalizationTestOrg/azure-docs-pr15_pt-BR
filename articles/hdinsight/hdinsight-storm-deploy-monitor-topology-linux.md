<properties
   pageTitle="Implantar e gerenciar topologias Apache tempestade em baseados em Linux HDInsight | Microsoft Azure"
   description="Saiba como implantar, monitorar e gerenciar topologias Apache tempestade usando o painel de tempestade em HDInsight baseado em Linux. Use ferramentas de Hadoop para Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/07/2016"
   ms.author="larryfr"/>

# <a name="deploy-and-manage-apache-storm-topologies-on-linux-based-hdinsight"></a>Implantar e gerenciar topologias Apache tempestade em HDInsight baseado em Linux

Neste documento, aprenda as Noções básicas de gerenciamento e monitoração topologias tempestade em execução no baseados em Linux tempestade em clusters de HDInsight.

> [AZURE.IMPORTANT] As etapas neste artigo exigem uma tempestade baseados em Linux em cluster HDInsight. Para obter informações sobre como implantar e monitoramento topologias em HDInsight baseado no Windows, consulte [implantar e gerenciar topologias Apache tempestade em HDInsight baseado no Windows](hdinsight-storm-deploy-monitor-topology.md)

## <a name="prerequisites"></a>Pré-requisitos

* **Baseado em Linux A tempestade em cluster HDInsight**: consulte [Introdução ao Apache tempestade em HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md) para obter etapas sobre como criar um cluster

* **Familiaridade com SSH e SCP**: para obter mais informações sobre como usar SSH e SCP com HDInsight, consulte o seguinte:

    * **Clientes Linux, Unix ou OS X**: consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Clientes do Windows**: consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* **SCP um cliente**: isso é fornecido com todos os sistemas Linux, Unix e OS X. Para clientes do Windows, é recomendável PSCP, que está disponível na [página de download de acabamento](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a name="start-a-storm-topology"></a>Iniciar uma topologia de tempestade

### <a name="using-ssh-and-the-storm-command"></a>Usando SSH e o comando de tempestade

1. Use SSH para se conectar ao cluster HDInsight. Substituir o **nome de usuário** o o nome do seu login SSH. Substitua o nome do seu cluster de HDInsight **CLUSTERNAME** :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Para obter mais informações sobre como usar o SSH para se conectar ao seu cluster HDInsight, consulte os seguintes documentos:
    
    * **Clientes Linux, Unix ou OS X**: consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * **Clientes do Windows**: consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Use o seguinte comando para iniciar um exemplo de topologia:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology WordCount

    Isso iniciará a topologia de WordCount de exemplo no cluster. Ele aleatoriamente gerar sentenças e contar a ocorrência de cada palavra nas frases.

    > [AZURE.NOTE] Ao enviar topologia ao cluster, primeiro você deve copiar o arquivo jar que contém o cluster antes de usar o `storm` comando. Isso pode ser feito usando o `scp` comando do cliente onde existe o arquivo. Por exemplo,`scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > O exemplo WordCount e outros exemplos de starter tempestade, já estão incluídos em seu cluster em `/usr/hdp/current/storm-client/contrib/storm-starter/`.

### <a name="programmatically"></a>Programaticamente

Para implantar programaticamente uma topologia para tempestade em HDInsight se comunicar com o serviço de Nimbus hospedado no seu cluster. [https://GitHub.com/Azure-Samples/hdinsight-Java-Deploy-Storm-Topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) fornece um aplicativo Java que demonstra como implantar e iniciar uma topologia através do serviço de Nimbus de exemplo.

## <a name="monitor-and-manage-using-the-storm-command"></a>Monitorar e gerenciar usando o comando de tempestade

O `storm` utilitário permite que você trabalhe com a execução topologias da linha de comando. A seguir está uma lista de comandos mais usados. Use `storm -h` para uma lista completa de comandos.

### <a name="list-topologies"></a>Topologias de lista

Use o seguinte comando para listar todos executando topologias:

    storm list
    
Isso retornará informações similar ao seguinte:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Desativar e reativar

Desativar uma topologia pausa-lo até ele ser eliminado ou reativado. Use este procedimento para desativar e reativar:

    storm Deactivate TOPOLOGYNAME
    
    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Eliminar uma topologia em execução

Topologias de tempestade, uma vez iniciadas, continuará em execução até parar. Para interromper uma topologia, use o seguinte comando:

    storm stop TOPOLOGYNAME

### <a name="rebalance"></a>Rebalancear

Redistribuição uma topologia permite que o sistema de revisar o paralelismo da topologia. Por exemplo, se você tiver redimensionado cluster para adicionar mais anotações, redistribuição permitirá uma topologia em execução tornar usar os novos nós.

> [AZURE.WARNING] Redistribuição uma topologia primeiro desativa a topologia, depois redistribui trabalhadores uniformemente no cluster e depois finalmente retorna a topologia ao estado em que se encontrava antes de redistribuição ocorreu. Portanto se a topologia estava ativa, ele ficará ativo novamente. Se ela foi desativada, ela permanecerá desativada.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-using-the-storm-ui"></a>Monitorar e gerenciar usando a interface de usuário de tempestade

A interface de usuário de tempestade fornece uma interface da web para trabalhar com executando topologias e está incluído em seu cluster HDInsight. Para exibir a interface do usuário tempestade, use um navegador da web para abrir __https://CLUSTERNAME.azurehdinsight.net/stormui__, onde __CLUSTERNAME__ é o nome do seu cluster.

> [AZURE.NOTE] Se solicitado a fornecer um nome de usuário e senha, insira o administrador de cluster (admin) e senha que você usou quando criar o cluster.


### <a name="main-page"></a>Página principal

A página principal da interface do usuário tempestade fornece as seguintes informações:

* **Cluster resumo**: informações básicas sobre o cluster tempestade.

* **Topologia de resumo**: uma lista de execução topologias. Use os links desta seção para exibir mais informações sobre topologias específico.

* **Supervisor resumo**: informações sobre o supervisor de tempestade.

* **Configuração de Nimbus**: configuração Nimbus para o cluster.

### <a name="topology-summary"></a>Resumo de topologia

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

### <a name="spout-and-bolt-summary"></a>Resumo de raio e spout

Selecionando um spout as seções **Spouts** ou **Bolts** exibe as seguintes informações sobre o item selecionado:

* **Componente resumo**: informações básicas sobre a spout ou raio.

* **Estatísticas de spout/raio**: estatísticas sobre a spout ou raio. Use os links na coluna da **janela** para definir o intervalo de tempo para as entradas restantes na página.

* **Estatísticas de entrada** (somente para incluir): informações sobre os fluxos de entrada consumida pelo raio.

* **Estatísticas de saída**: informações sobre os fluxos emitida por isso spout ou incluir.

* **Executores**: informações sobre as instâncias da spout ou raio. Selecione a entrada de **porta** para um executor específico exibir um log de informações de diagnóstico produzidas dessa instância.

* **Erros**: qualquer informação de erro para isso spout ou incluir.

## <a name="rest-api"></a>API REST

A interface de usuário de tempestade baseia-se na parte superior da API REST, para que você possa realizar gerenciamento e funcionalidade de monitoramento usando a API REST semelhantes. Você pode usar a API REST para criar ferramentas personalizadas para gerenciar e monitorar topologias tempestade.

Para obter mais informações, consulte [Tempestade API de REST de interface do usuário](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). As informações a seguir são específicas para usar a API REST com Apache tempestade em HDInsight.

> [AZURE.IMPORTANT] A API REST tempestade não está disponível publicamente pela internet e devem ser acessado através de um túnel SSH para o nó principal de cluster HDInsight. Para obter informações sobre como criar e usar um túnel SSH, consulte [Usar SSH túnel para acessar Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie e outra web da interface do usuário](hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Base de URI

O URI de base para a API REST em clusters baseados em Linux HDInsight está disponível no nó principal no **api/https://HEADNODEFQDN:8744/v1/**; No entanto, o nome de domínio do nó principal é gerado durante a criação de cluster e não é estático.

Você pode encontrar o nome de domínio totalmente qualificado (FQDN) para o nó de cabeça cluster de várias maneiras diferentes:

* __Sessão de um SSH__: Use o comando `headnode -f` de uma sessão SSH ao cluster.

* __Da Web de Ambari__: selecione __Serviços__ da parte superior da página, depois selecione __tempestade__. Na guia __Resumo__ , selecione __Tempestade interface de usuário do servidor__. O FQDN do nó que tempestade UI e API REST estiver em execução no estará na parte superior da página.

* __Da API REST do Ambari__: Use o comando `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` para recuperar informações sobre o nó tempestade UI e API REST em execução no. Substitua a __senha__ com a senha de administrador para o cluster. Substitua o nome do cluster __CLUSTERNAME__ . Na resposta, a entrada de "nome_do_host" contém o FQDN do nó.

### <a name="authentication"></a>Autenticação

Solicitações da a API REST devem usar **autenticação básica**, para que você usar o nome do administrador de cluster HDInsight e a senha.

> [AZURE.NOTE] Porque a autenticação básica é enviada usando texto não criptografado, você deve **sempre** usar HTTPS para proteger a comunicação com o cluster.

### <a name="return-values"></a>Valores de retorno

Informações que são retornadas da API REST só podem ser útil de dentro do cluster ou máquinas virtuais em uma rede Virtual Azure mesmo como cluster. Por exemplo, o nome de domínio totalmente qualificado (FQDN) retornado para os servidores de Zookeeper não será acessível pela Internet.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como implantar e monitorar topologias usando o painel de tempestade, saiba como [topologias baseado em Java desenvolver usando Maven](hdinsight-storm-develop-java-topology.md).

Para obter uma lista das mais topologias de exemplo, consulte [topologias de exemplo para tempestade em HDInsight](hdinsight-storm-example-topology.md).
