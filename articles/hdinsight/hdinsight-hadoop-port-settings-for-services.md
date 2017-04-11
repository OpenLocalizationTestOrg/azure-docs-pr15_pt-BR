<properties
pageTitle="Portas usadas pelo HDInsight | Azure"
description="Uma lista de portas usadas pelos serviços de Hadoop executados em HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/03/2016"
ms.author="larryfr"/>

# <a name="ports-and-uris-used-by-hdinsight"></a>Portas e URIs usados pelo HDInsight

Este documento fornece uma lista das portas usadas pelos serviços de Hadoop executados em clusters de HDInsight baseados em Linux. Ele também fornece informações sobre portas usadas para se conectar ao cluster usando SSH.

## <a name="public-ports-vs-non-public-ports"></a>Portas públicas versus portas não-públicos

Clusters de HDInsight baseados em Linux expõe apenas três portas publicamente na internet; 22, 23 e 443. Estes são usadas para acessar o cluster usando SSH e serviços expostos através do protocolo HTTPS seguro com segurança.

Internamente, HDInsight é implementado por várias máquinas virtuais do Azure (os nós dentro do cluster,) em uma rede Virtual do Azure. A rede virtual, você pode acessar portas não expostas pela internet. Por exemplo, se você se conectar a um de nós cabeça usando SSH, de nó principal você pode, em seguida, acessar diretamente services em execução em nós do cluster.

> [AZURE.IMPORTANT] Quando você cria um cluster de HDInsight, se você não especificar uma rede Virtual do Azure como uma opção de configuração, um é criado; No entanto, não poderá ingressar em outros computadores (como outras máquinas virtuais do Azure ou sua máquina de desenvolvimento do cliente,) para isto criada automaticamente rede virtual. 

Para ingressar em máquinas adicionais à rede virtual, você deve criar a rede virtual primeiro e depois especificá-lo ao criar seu cluster HDInsight. Para obter mais informações, consulte [os recursos de HDInsight estender usando uma rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Portas públicas

Todos os nós em um cluster de HDInsight estão localizados em uma rede Virtual do Azure e não podem ser acessados diretamente da internet. Um gateway público fornece acesso à internet para as seguintes portas, que são comuns em todos os tipos de cluster HDInsight.

| Serviço | Porta | Protocolo | Descrição |
| ---- | ---------- | -------- | ----------- | ----------- |
| sshd | 22 | SSH | Conecta clientes a sshd na headnode principal. Consulte [usar SSH com HDInsight baseados em Linux](hdinsight-hadoop-linux-use-ssh-windows.md) |
| sshd | 22 | SSH | Conecta clientes a sshd no nó borda (somente HDInsight Premium). Consulte [começar a usar o servidor de R em HDInsight](hdinsight-hadoop-r-server-get-started.md) |
| sshd | 23 | SSH | Conecta clientes a sshd na headnode secundário. Consulte [usar SSH com HDInsight baseados em Linux](hdinsight-hadoop-linux-use-ssh-windows.md) |
| Ambari | 443 | HTTPS | Ambari UI da web. Consulte [Gerenciar HDInsight usando a interface do usuário do Ambari da Web](hdinsight-hadoop-manage-ambari.md) |
| Ambari | 443 | HTTPS | API de REST Ambari. Consulte [Gerenciar HDInsight usando a API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat | 443 | HTTPS | API de REST HCatalog. Consulte [usar seção com ondulação](hdinsight-hadoop-use-pig-curl.md), [Use porco com ondulação](hdinsight-hadoop-use-pig-curl.md), [Use MapReduce com ondulação](hdinsight-hadoop-use-mapreduce-curl.md) |
| HiveServer2 | 443 | ODBC | Conecta a seção usando ODBC. Consulte [Conectar o Excel ao HDInsight com o driver ODBC da Microsoft](hdinsight-connect-excel-hive-odbc-driver.md). |
| HiveServer2 | 443 | JDBC | Conecta a seção utilizando JDBC. Consulte [conectar ao seção em HDInsight usando o driver JDBC seção](hdinsight-connect-hive-jdbc-driver.md) |

A seguir estão disponíveis para tipos específicos de cluster:

| Serviço | Porta | Protocolo |Tipo de cluster | Descrição |
| ------------ | ---- |  ----------- | --- | ----------- |
| STARGATE | 443 | HTTPS | HBase | API de REST HBase. Consulte [Introdução ao uso HBase](hdinsight-hbase-tutorial-get-started-linux.md) |
| Livy | 443 | HTTPS |  Spark | API de REST Spark. Consulte [trabalhos de enviar Spark remotamente usando Livy](hdinsight-apache-spark-livy-rest-interface.md) |
| Tempestade | 443 | HTTPS | Tempestade | Tempestade UI da web. Consulte [implantar e gerenciar topologias de tempestade em HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="authentication"></a>Autenticação

Todos os serviços publicamente expostos na internet devem ser autenticados:

| Porta | Credenciais |
| ---- | ----------- |
| 22 ou 23 | As credenciais do usuário SSH especificadas durante a criação de cluster |
| 443 | O nome de login (padrão: administrador,) e a senha que foram definidas durante a criação de cluster |

## <a name="non-public-ports"></a>Portas não-pública

> [AZURE.NOTE] Alguns serviços só estão disponíveis em tipos de cluster específico. Por exemplo, HBase só está disponível em tipos de cluster HBase.

### <a name="hdfs-ports"></a>Portas HDFS

| Serviço | Nós | Porta | Protocolo | Descrição |
| ------- | ------- | ---- | -------- | ----------- | 
| Web NameNode da interface do usuário | Nós de cabeça | 30070 | HTTPS | Interface de usuário para exibir o status atual da Web |
| Serviço de metadados de NameNode | nós de cabeça | 8020 | IPC | Metadados do sistema de arquivos 
| DataNode | Todos os nós de operador | 30075 | HTTPS | UI da Web para exibir o status, logs, etc. |
| DataNode | Todos os nós de operador | 30010 | &nbsp; | Transferência de dados |
| DataNode | Todos os nós de operador | 30020 | IPC | Operações de metadados |
| NameNode secundário | Nós de cabeça | 50090 | HTTP | Ponto de verificação para metadados de NameNode |

### <a name="yarn-ports"></a>Portas de fio COLORIDO

| Serviço | Nós | Porta | Protocolo | Descrição |
| ------- | ------- | ---- | -------- | ----------- |
| IU da web do Gerenciador de recursos | Nós de cabeça | 8088 | HTTP | IU do Gerenciador de recursos da Web |
| IU da web do Gerenciador de recursos | Nós de cabeça | 8090 | HTTPS | IU do Gerenciador de recursos da Web |
| Interface de administração do Gerenciador de recursos | nós de cabeça | 8141 | IPC | Para envios de aplicativo (seção, servidor de ramificação, porco, etc.) |
| Agendador do Gerenciador de recursos | nós de cabeça | 8030 | HTTP | Interface administrativa |
| Interface de aplicativo do Gerenciador de recursos | nós de cabeça | 8050 | HTTP |Endereço da interface do Gerenciador de aplicativos |
| NodeManager | Todos os nós de operador | 30050 | &nbsp; | O endereço do Gerenciador de contêiner |
| Web NodeManager interface do usuário | Todos os nós de operador | 30060 | HTTP | Interface do Gerenciador de recursos |
| Endereço de linha do tempo | Nós de cabeça | 10200 | RPC | O serviço RPC do serviço de linha do tempo. |
| IU da web do cronograma | Nós de cabeça | 8181 | HTTP | A web de serviço de linha do tempo da interface do usuário |

### <a name="hive-ports"></a>Portas de seção

| Serviço | Nós | Porta | Protocolo | Descrição |
| ------- | ------- | ---- | -------- | ----------- |
| HiveServer2 | Nós de cabeça | 10001 | E mais Próspero banco | Serviço de conexão programaticamente com seção (e mais Próspero banco/JDBC) |
| HiveServer | Nós de cabeça | 10000 | E mais Próspero banco | Serviço de conexão programaticamente com seção (e mais Próspero banco/JDBC) |
| Seção Metastore | Nós de cabeça | 9083 | E mais Próspero banco | Serviço de conexão programaticamente a seção metadados (e mais Próspero banco/JDBC) |

### <a name="webhcat-ports"></a>Portas WebHCat

| Serviço | Nós | Porta | Protocolo | Descrição |
| ------- | ------- | ---- | -------- | ----------- |
| Servidor de WebHCat | Nós de cabeça | 30111 | HTTP | API da Web na parte superior HCatalog e outros serviços de Hadoop |

### <a name="mapreduce-ports"></a>Portas MapReduce

| Serviço | Nós | Porta | Protocolo | Descrição |
| ------- | ------- | ---- | -------- | ----------- |
| JobHistory | Nós de cabeça | 19888 | HTTP | Web MapReduce JobHistory interface do usuário |
| JobHistory | Nós de cabeça | 10020 | &nbsp; | Servidor de MapReduce JobHistory |
| ShuffleHandler | &nbsp; | 13562 | &nbsp; | Mapa de intermediária transferências saídas à solicitação de Reducers |

### <a name="oozie"></a>Oozie

| Serviço | Nós | Porta | Protocolo | Descrição |
| ------- | ------- | ---- | -------- | ----------- |
| Servidor de Oozie | Nós de cabeça | 11000 | HTTP | URL de serviço de Oozie |
| Servidor de Oozie | Nós de cabeça | 11001 | HTTP | Porta do administrador de Oozie |

### <a name="ambari-metrics"></a>Métricas de Ambari

| Serviço | Nós | Porta | Protocolo | Descrição |
| ------- | ------- | ---- | -------- | ----------- |
| Linha do tempo (histórico de aplicativo) | Nós de cabeça | 6188 | HTTP | A web de serviço de linha do tempo da interface do usuário |
| Linha do tempo (histórico de aplicativo) | Nós de cabeça | 30200 | RPC | A web de serviço de linha do tempo da interface do usuário |

### <a name="hbase-ports"></a>Portas HBase

| Serviço | Nós | Porta | Protocolo | Descrição |
| ------- | ------- | ---- | -------- | ----------- |
| HMaster | Nós de cabeça | 16000 | &nbsp; | &nbsp; |
| Informações de HMaster Web UI | Nós de cabeça | 16010 | HTTP | A porta para web HBase mestre da interface do usuário |
| Servidor de região | Todos os nós de operador | 16020 | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | 2181 | &nbsp; | A porta que os clientes usam para se conectar ao ZooKeeper |

### <a name="kafka-ports"></a>Portas Kafka

| Serviço | Nós | Porta | Protocolo | Descrição |
| ------- | ------- | ---- | -------- | ----------- |
| Agente  | Nós de operador | 9092 | [Protocolo Kafka](http://kafka.apache.org/protocol.html) | Usado para comunicação de cliente |
| &nbsp; | Nós zookeeper | 2181 | &nbsp; | A porta que os clientes usam para se conectar ao Zookeeper |
