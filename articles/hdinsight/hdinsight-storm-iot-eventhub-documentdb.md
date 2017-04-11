<properties
 pageTitle="Processar dados de sensor de veículo com Apache tempestade em HDInsight | Microsoft Azure"
 description="Saiba como processar dados de sensor de veículo de Hubs de evento usando Apache tempestade em HDInsight. Adicionar dados de modelo de DocumentDB e armazenar saída ao armazenamento."
 services="hdinsight,documentdb,notification-hubs"
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
ms.date="08/23/2016"
ms.author="larryfr"/>

#<a name="process-vehicle-sensor-data-from-azure-event-hubs-using-apache-storm-on-hdinsight"></a>Processar dados de sensor de veículo de Hubs de evento do Azure usando Apache tempestade em HDInsight

Saiba como processar dados de sensor de veículo de Hubs de evento do Azure usando Apache tempestade em HDInsight. Este exemplo lê dados do Azure evento Hubs, enriquece os dados consultando dados armazenados em DocumentDB do Azure e finalmente armazena os dados para o armazenamento do Azure usando o sistema de arquivo do Hadoop (HDFS).

![HDInsight e o diagrama de arquitetura de Internet das coisas (IoT)](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

##<a name="overview"></a>Visão geral

Adicionando sensores a veículos permite prever problemas de equipamentos com base nos dados históricos tendências, bem como aprimorar futuras versões com base em análise de padrão de uso. Enquanto tradicional processamento em lotes de MapReduce pode ser usado para esta análise, você deve ser capaz de forma rápida e eficiente carregar os dados de todos os veículos na Hadoop antes de processamento de MapReduce pode ocorrer. Além disso, talvez você queira fazer análise para caminhos de falha crítica (temperatura de mecanismo, freios, etc.) em tempo real.

Azure Hubs de evento são criados para lidar com o grande volume de dados gerados por sensores e Apache tempestade em HDInsight pode ser usado para carregar e processar os dados antes de armazená-lo em HDFS (feito pelo armazenamento do Azure) para processamento de MapReduce adicional.

##<a name="solution"></a>Solução

Dados de telemetria para mecanismo temperatura, temperatura ambiente e a velocidade de um veículo são registrados pelo sensores, então enviados ao evento Hubs além veículo identificação número VIN do carro () e um carimbo de hora. A partir daí, uma topologia de tempestade em execução em uma tempestade Apache em cluster HDInsight lê os dados, processa e armazena em HDFS.

Durante o processamento, VIN é usado para recuperar informações de modelo de DocumentDB do Azure. Isso é adicionado ao fluxo de dados antes de ser armazenado.

Os componentes usados na topologia tempestade são:

* **EventHubSpout** - lê os dados do Azure Hubs de evento

* **TypeConversionBolt** - converte a cadeia de caracteres JSON de Hubs de evento em uma tupla que contém os valores de dados individuais de temperatura de mecanismo, temperatura ambiente, velocidade, VIN e carimbo de hora

* **DataReferencBolt** - procura o modelo de um veículo de DocumentDB usando VIN

* **WasbStoreBolt** - armazena os dados HDFS (armazenamento do Azure)

A seguir é um diagrama desta solução:

![topologia de tempestade](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

> [AZURE.NOTE] Este é um diagrama simplificado e cada componente na solução pode ter várias instâncias. Por exemplo, de várias instâncias de cada componente na topologia são distribuídas entre os nós a tempestade em cluster HDInsight.

##<a name="implementation"></a>Implementação

Completa e solução automatizada para esse cenário está disponível como parte do repositório do [HDInsight-tempestade-exemplos](https://github.com/hdinsight/hdinsight-storm-examples) no GitHub. Para usar este exemplo, siga as etapas descritas na [IoTExample README. MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md).

## <a name="next-steps"></a>Próximas etapas

Para mais topologias de tempestade de exemplo, consulte [topologias de exemplo para tempestade em HDInsight](hdinsight-storm-example-topology.md).
