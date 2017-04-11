<properties
 pageTitle="Topologias de Apache tempestade de exemplo em HDInsight | Microsoft Azure"
 description="Uma lista das topologias de tempestade de exemplo criado e testado com Apache tempestade em HDInsight incluindo topologias básicas de c# e Java e trabalhar com Hubs de evento."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
    tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="08/23/2016"
 ms.author="larryfr"/>

# <a name="example-storm-toplogies-and-components-for-apache-storm-on-hdinsight"></a>Exemplo tempestade toplogies e componentes para Apache tempestade em HDInsight

A seguir está uma lista de exemplos criadas e mantidas pela Microsoft para uso com Apache tempestade em HDInsight. Esses exemplos abrangem uma variedade de tópicos, criação de básicas c# e topologias Java para trabalhar com os serviços do Azure como Hubs de evento, DocumentDB, Power BI, banco de dados SQL, HBase em HDInsight e armazenamento do Azure. Alguns exemplos demonstram também como trabalhar com tecnologias não Azure ou até mesmo não sejam da Microsoft, como SignalR e Socket.IO

| Descrição                                                                                             | Demonstra                                         | Idioma/Framework         |
|:--------------------------------------------------------------------------------------------------------|:-----------------------------------------------------|:---------------------------|
| [Gravar Azure dados Lucerne armazenamento do Apache tempestade](hdinsight-storm-write-data-lake-store.md) | Escrevendo para armazenamento de Lucerne de dados do Azure | Java |
| [Fonte de evento Hub Spout e brilhante](https://github.com/apache/storm/tree/master/external/storm-eventhubs) | Fonte para o evento Hub Spout e brilhante | Java |
| [Desenvolva topologias baseado em Java para Apache tempestade em HDInsight][5797064f]                                 | Maven                                                | Java                       |
| [Desenvolva c# topologias para Apache tempestade em HDInsight usando o Visual Studio][16fce2d1]                     | Ferramentas de HDInsight para Visual Studio                    | C#, Java                   |
| [Criar vários fluxos de dados em uma topologia de c# tempestade][ec5a4064]                                         | Vários fluxos                                     | C#                         |
| [Determinar Twitter tópicos tendências com tempestade em HDInsight][3c86c7c8]                                   | Trident                                              | Java, Trident              |
| [Eventos de processo do Azure Hubs de evento com tempestade em HDInsight (c#)][844d1d81]                                | Hubs de evento                                           | C# e Java                |
| [Eventos de processo do Azure Hubs de evento com tempestade em HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md) | Hubs de evento | Java |
| [Usar o Power Bi para visualizar dados de uma topologia de tempestade][94d15238]                              | Power BI                                             | C#                         |
| [Analisar dados de sensor com tempestade e HBase em HDInsight][ab894747]                                     | Evento Hubs, HBase, Socket.IO, painel da Web          | C#, Java, JavaScript, HTML |
| [Processar dados de sensor de veículo de Hubs de evento usando tempestade em HDInsight][246ee964]                        | Evento Hubs, DocumentDb, armazenamento do Azure Blob (WASB)    | C#, Java                   |
| [Extração, transformação e carregamento (ETL) de Hubs de evento do Azure para HBase, usando tempestade em HDInsight][b4b68194] | Hubs de evento, HBase                                    | C#                         |
| [Projeto de topologia de c# tempestade de modelo para trabalhar com os serviços do Azure de tempestade em HDInsight][ce0c02a2]  | Evento Hubs, DocumentDb, SQL banco de dados, HBase, SignalR | C#, Java                   |
| [Avaliações de escalabilidade para leitura de Hubs de evento do Azure usando tempestade em HDInsight][d6c540e3]           | Transferência de mensagem, Hubs de evento, banco de dados SQL         | C#, Java                   |
| [Correlação eventos usando tempestade e HBase em HDInsight](hdinsight-storm-correlation-topology.md) | HBase | C# |
| [Usar Python com tempestade em HDInsight](hdinsight-storm-develop-python-topology.md) | Componentes de Python com Java e Clojure baseados topologias de tempestade | Python |

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Apache tempestade em HDInsight][2b8c3488]

* [Saiba como implantar e gerenciar topologias de tempestade com tempestade em HDInsight][6eb0d3b8]

  [2b8c3488]: hdinsight-apache-storm-tutorial-get-started-linux.md "Saiba como criar uma tempestade em cluster HDInsight e usar o painel de tempestade para implantar topologias de exemplo."
  [6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "Saiba como implantar e gerenciar topologias usando o painel de tempestade baseado na web e interface de usuário de tempestade ou as ferramentas de HDInsight para Visual Studio."
  [16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "Aprenda a criar topologias c# tempestade usando as ferramentas de HDInsight para Visual Studio."
  [5797064f]: hdinsight-storm-develop-java-topology.md "Aprenda a criar topologias de tempestade em Java, usando Maven, criando uma topologia wordcount básica."
  [94d15238]: hdinsight-storm-power-bi-topology.md "Demonstra como gravar dados no Power BI de uma topologia de c#, em seguida, criar um gráfico e um painel a partir dos dados."
  [ec5a4064]: https://github.com/Blackmist/csharp-storm-example "Demonstra uma topologia de tempestade básica que executa um wordcount, implementado em c#. Isso também demonstra como criar vários fluxos de dados dentro de uma topologia c#."
  [844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "Saiba como ler e gravar dados do Azure Hubs de evento com tempestade em HDInsight."
  [ab894747]: hdinsight-storm-sensor-data-analysis.md "Saiba como usar Apache tempestade em HDInsight para processar dados de sensor de Hubs de evento do Azure, visualize-la usando D3.js e (opcionalmente), armazená-lo em HBase."
  [3c86c7c8]: hdinsight-storm-twitter-trending.md "Saiba como usar Trident para criar uma topologia de tempestade que determina tendências tópicos (baseados em hashtags,) no Twitter."
  [246ee964]: hdinsight-storm-iot-eventhub-documentdb.md "Saiba como usar uma topologia de tempestade para ler mensagens de Hubs de evento do Azure, ler documentos do Azure DocumentDB para a referência de dados e salvar dados ao armazenamento do Azure."
  [d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Várias topologias para demonstrar produtividade quando ler do Azure evento Hubs e armazená banco de dados do SQL usando Apache tempestade em HDInsight."
  [b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Saiba como ler dados do Azure Hubs de evento, agregar e transformar os dados e armazená-lo em HBase em HDInsight."
  [ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Este projeto contém modelos para spouts, parafusos e topologias para interagir com vários serviços Azure como Hubs de evento, DocumentDB e o banco de dados SQL."
 
