<properties
    pageTitle="Logs de aplicativo do Access fio COLORIDO Hadoop programaticamente | Microsoft Azure"
    description="Aplicativo do Access programaticamente logs em um cluster de Hadoop em HDInsight."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Aplicativo do Access fio COLORIDO logon HDInsight baseado no Windows

Este tópico explica como acessar os logs de aplicativos de fio COLORIDO (ainda outro recurso Negotiator) que terminar em um cluster de Hadoop no Azure HDInsight

> [AZURE.NOTE] As informações neste documento se aplicam somente a clusters HDInsight baseados no Windows. Para obter informações sobre como acessar o fio COLORIDO fizer logon clusters baseados em Linux HDInsight, consulte [fio COLORIDO acesso aplicativo fizer logon baseado em Linux Hadoop em HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)

### <a name="prerequisites"></a>Pré-requisitos

- Um cluster baseado no Windows HDInsight.  Consulte [clusters baseados no Windows criar Hadoop em HDInsight](hdinsight-provision-clusters.md).


## <a name="yarn-timeline-server"></a>Servidor de linha do tempo de fio COLORIDO

O <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Servidor de linha do tempo de fio COLORIDO</a> fornece informações genéricas em aplicativos concluídos, bem como informações específicas do framework aplicativo por meio de duas interfaces diferentes. Especificamente:

* Armazenamento e recuperação de informações de aplicativo genérico em clusters de HDInsight foi ativado com versão 3.1.1.374 ou superior.
* O componente de informações específicas do framework aplicativo do servidor linha do tempo não está disponível atualmente nos clusters de HDInsight.


Informações genéricas em aplicativos incluem os seguintes tipos de dados:

* A ID do aplicativo, um identificador exclusivo de um aplicativo
* O usuário que iniciou o aplicativo
* Informações sobre tentativas feitas para concluir o aplicativo
* Os contêineres usados por qualquer tentativa de determinado aplicativo

Em seu clusters HDInsight, essas informações serão armazenadas pelo Gerenciador de recursos do Azure para um armazenamento de histórico no contêiner padrão da sua conta de armazenamento do Azure padrão. Estes dados genéricos em aplicativos concluídos podem ser recuperados por meio de uma API REST:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>Logs e aplicativos de fio COLORIDO

Fio COLORIDO oferece suporte a vários modelos de programação (MapReduce sendo um deles), separação de gerenciamento de recursos do aplicativo agendamento/monitoramento. Isso é feito por meio de um global *ResourceManager* (RM), por trabalhador-nó *NodeManagers* (NMs) e por aplicativo *ApplicationMasters* (AMs). O AM por aplicativo negocia recursos (CPU, memória, disco, rede) para executar o aplicativo com o mecanismo de retenção. O mecanismo de retenção funciona com NMs para conceder esses recursos, o que são concedidos como *contêineres*. O AM é responsável por controlar o andamento dos contêineres atribuído pelo mecanismo de retenção. Um aplicativo pode exigir muitos contêineres dependendo da natureza do aplicativo.

Além disso, cada aplicativo pode consistir em várias *tentativas de aplicativo* para término na presença de travamentos ou devido a perda de comunicação entre AM e um mecanismo de retenção. Portanto, contêineres são concedidos a uma tentativa de específico de um aplicativo. Em um sentido, um contêiner fornece o contexto da unidade básica do trabalho realizado por um aplicativo de fio COLORIDO e todo o trabalho que é feito dentro do contexto de um contêiner é executado no nó único trabalhador no qual o contêiner foi alocado. Consulte [Conceitos fio COLORIDO] [ YARN-concepts] para referência posterior.

Logs de aplicativo (e os logs de contêiner associado) são fundamentais na depuração problemático Hadoop aplicativos. Fio COLORIDO fornece uma estrutura lindas para coletar, agregar e armazenar logs de aplicativo com a [Agregação de Log] [ log-aggregation] recurso. O recurso de agregação de Log torna mais determinante, ao acessar logs de aplicativos, como ela agrega logs em todos os contêineres em um nó de trabalho e armazena-os como um agregado arquivo de log por nó de trabalho no sistema de arquivos padrão após a conclusão de um aplicativo. Seu aplicativo pode utilizar centenas ou milhares de contêineres, mas logs para todos os contêineres executados em um nó único trabalhador sempre serão agregados para um único arquivo, resultando em um arquivo de log por nó de trabalho usado pelo seu aplicativo. Agregação de log é habilitada por padrão em HDInsight clusters (versão 3.0 e acima), e agregados logs podem ser encontrados no contêiner padrão do seu cluster no seguinte local:

    wasbs:///app-logs/<user>/logs/<applicationId>

Nesse local, o *usuário* é o nome do usuário que iniciou o aplicativo e *applicationId* é o identificador exclusivo de um aplicativo como atribuído pelo mecanismo de retenção fio COLORIDO.

Os logs agregados não estão diretamente legíveis, como eles são gravados em um [TFile][T-file], [formato binário] [ binary-format] indexado pelo contêiner. Fio COLORIDO fornece ferramentas CLI para despejar esses logs como texto sem formatação para aplicativos ou contêineres de interesse. Você pode visualizar esses logs como texto sem formatação, execute um do fio COLORIDO seguinte comandos diretamente em nós do cluster (depois de conectar a ela por meio de RDP):

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>Interface de usuário de ResourceManager fio COLORIDO

A interface do usuário do fio COLORIDO ResourceManager é executado em headnode o cluster e podem ser acessado através do Azure dashboard portal: 

1. Entrar no [portal do Azure](https://portal.azure.com/). 
2. No menu à esquerda, clique em **Procurar**, clique em **Clusters de HDInsight**um cluster baseado no Windows que você deseja acessar os logs de aplicativo fio COLORIDO.
3. No menu superior, clique em **Painel de controle**. Você verá uma página aberta em um navegador novato guia chamada **Console de consulta de HDInsight**.
4. No **Console de consulta de HDInsight**, clique em **Fio colorido da interface do usuário**.




[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
