## <a name="receive-messages-with-apache-storm"></a>Receber mensagens com Apache tempestade

[**Tempestade Apache**](https://storm.incubator.apache.org) é um sistema de computação em tempo real distribuído que simplifica confiável processamento de ilimitado fluxos de dados. Esta seção mostra como usar uma spout de tempestade de Hubs de evento para receber eventos de Hubs de evento. Usando Apache tempestade, você pode dividir eventos em vários processos hospedados em nós diferentes. A integração de Hubs de evento com tempestade simplifica consumo de evento transparente verificando seu progresso usando a instalação de Zookeeper do tempestade, gerenciar pontos de verificação persistentes e paralelo recebe de Hubs de evento.

Para obter mais informações sobre o evento Hubs receber padrões, consulte [Visão geral de Hubs de evento][].

Este tutorial usa uma instalação de [Tempestade de HDInsight][] , que vem com o spout de Hubs de evento já disponível.

1. Siga o procedimento de [Tempestade HDInsight - Introdução](../articles/hdinsight/hdinsight-storm-overview.md) para criar um novo cluster HDInsight e conectá-la por meio de área de trabalho remota.

2. Copiar o `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` arquivo para seu ambiente de desenvolvimento local. Esta página contém o eventos-tempestade-spout.

3. Use o seguinte comando para instalar o pacote na loja Maven local. Isso permite que você adicioná-lo como uma referência do projeto de tempestade em uma etapa posterior.

        mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

4. No Eclipse, crie um novo projeto Maven (clique em **arquivo**, em seguida, **novo**, então, **Project**).

    ![][12]

5. Selecione o **local de espaço de trabalho do padrão de uso**, em seguida, clique em **Avançar**

6. Selecione o arquétipo **maven-arquétipo-início rápido** , clique em **Avançar**

7. Insira uma **ID do grupo** e **ArtifactId**, em seguida, clique em **Concluir**

8. No **pom.xml**, adicione as seguintes dependências no `<dependency>` nó.

        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-core</artifactId>
            <version>0.9.2-incubating</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>com.microsoft.eventhubs</groupId>
            <artifactId>eventhubs-storm-spout</artifactId>
            <version>0.9</version>
        </dependency>
        <dependency>
            <groupId>com.netflix.curator</groupId>
            <artifactId>curator-framework</artifactId>
            <version>1.3.3</version>
            <exclusions>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
            </exclusions>
            <scope>provided</scope>
        </dependency>

9. Na pasta **src** , crie um arquivo chamado **config** e copie o seguinte conteúdo, substituindo os seguintes valores:

        eventhubspout.username = ReceiveRule

        eventhubspout.password = {receive rule key}

        eventhubspout.namespace = ioteventhub-ns

        eventhubspout.entitypath = {event hub name}

        eventhubspout.partitions.count = 16

        # if not provided, will use storm's zookeeper settings
        # zookeeper.connectionstring=localhost:2181

        eventhubspout.checkpoint.interval = 10

        eventhub.receiver.credits = 10

    O valor para **eventhub.receiver.credits** determina quantos eventos forem colocados em lote antes de liberá-los para o pipeline de tempestade. Para simplificar, este exemplo define esse valor para 10. Em produção, ele geralmente deve ser definido para valores mais altos; Por exemplo, 1024.

10. Crie uma nova classe chamada **LoggerBolt** com o seguinte código:

        import java.util.Map;
        import org.slf4j.Logger;
        import org.slf4j.LoggerFactory;
        import backtype.storm.task.OutputCollector;
        import backtype.storm.task.TopologyContext;
        import backtype.storm.topology.OutputFieldsDeclarer;
        import backtype.storm.topology.base.BaseRichBolt;
        import backtype.storm.tuple.Tuple;

        public class LoggerBolt extends BaseRichBolt {
            private OutputCollector collector;
            private static final Logger logger = LoggerFactory
                      .getLogger(LoggerBolt.class);

            @Override
            public void execute(Tuple tuple) {
                String value = tuple.getString(0);
                logger.info("Tuple value: " + value);

                collector.ack(tuple);
            }

            @Override
            public void prepare(Map map, TopologyContext context, OutputCollector collector) {
                this.collector = collector;
                this.count = 0;
            }

            @Override
            public void declareOutputFields(OutputFieldsDeclarer declarer) {
                // no output fields
            }

        }

    Este raio tempestade registra o conteúdo dos eventos recebidos. Isso pode ser estendido facilmente para armazenar tuplas em um serviço de armazenamento. O [tutorial de análise de sensor HDInsight] usa essa mesma abordagem para armazenar dados em HBase.

11. Crie uma classe chamada **LogTopology** com o seguinte código:

        import java.io.FileReader;
        import java.util.Properties;
        import backtype.storm.Config;
        import backtype.storm.LocalCluster;
        import backtype.storm.StormSubmitter;
        import backtype.storm.generated.StormTopology;
        import backtype.storm.topology.TopologyBuilder;
        import com.microsoft.eventhubs.samples.EventCount;
        import com.microsoft.eventhubs.spout.EventHubSpout;
        import com.microsoft.eventhubs.spout.EventHubSpoutConfig;

        public class LogTopology {
            protected EventHubSpoutConfig spoutConfig;
            protected int numWorkers;

            protected void readEHConfig(String[] args) throws Exception {
                Properties properties = new Properties();
                if (args.length > 1) {
                    properties.load(new FileReader(args[1]));
                } else {
                    properties.load(EventCount.class.getClassLoader()
                            .getResourceAsStream("Config.properties"));
                }

                String username = properties.getProperty("eventhubspout.username");
                String password = properties.getProperty("eventhubspout.password");
                String namespaceName = properties
                        .getProperty("eventhubspout.namespace");
                String entityPath = properties.getProperty("eventhubspout.entitypath");
                String zkEndpointAddress = properties
                        .getProperty("zookeeper.connectionstring"); // opt
                int partitionCount = Integer.parseInt(properties
                        .getProperty("eventhubspout.partitions.count"));
                int checkpointIntervalInSeconds = Integer.parseInt(properties
                        .getProperty("eventhubspout.checkpoint.interval"));
                int receiverCredits = Integer.parseInt(properties
                        .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                    // (opt)
                System.out.println("Eventhub spout config: ");
                System.out.println("  partition count: " + partitionCount);
                System.out.println("  checkpoint interval: "
                        + checkpointIntervalInSeconds);
                System.out.println("  receiver credits: " + receiverCredits);

                spoutConfig = new EventHubSpoutConfig(username, password,
                        namespaceName, entityPath, partitionCount, zkEndpointAddress,
                        checkpointIntervalInSeconds, receiverCredits);

                // set the number of workers to be the same as partition number.
                // the idea is to have a spout and a logger bolt co-exist in one
                // worker to avoid shuffling messages across workers in storm cluster.
                numWorkers = spoutConfig.getPartitionCount();

                if (args.length > 0) {
                    // set topology name so that sample Trident topology can use it as
                    // stream name.
                    spoutConfig.setTopologyName(args[0]);
                }
            }

            protected StormTopology buildTopology() {
                TopologyBuilder topologyBuilder = new TopologyBuilder();

                EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
                topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                        spoutConfig.getPartitionCount()).setNumTasks(
                        spoutConfig.getPartitionCount());
                topologyBuilder
                        .setBolt("LoggerBolt", new LoggerBolt(),
                                spoutConfig.getPartitionCount())
                        .localOrShuffleGrouping("EventHubsSpout")
                        .setNumTasks(spoutConfig.getPartitionCount());
                return topologyBuilder.createTopology();
            }

            protected void runScenario(String[] args) throws Exception {
                boolean runLocal = true;
                readEHConfig(args);
                StormTopology topology = buildTopology();
                Config config = new Config();
                config.setDebug(false);

                if (runLocal) {
                    config.setMaxTaskParallelism(2);
                    LocalCluster localCluster = new LocalCluster();
                    localCluster.submitTopology("test", config, topology);
                    Thread.sleep(5000000);
                    localCluster.shutdown();
                } else {
                    config.setNumWorkers(numWorkers);
                    StormSubmitter.submitTopology(args[0], config, topology);
                }
            }

            public static void main(String[] args) throws Exception {
                LogTopology topology = new LogTopology();
                topology.runScenario(args);
            }
        }


    Essa classe cria um novo Hubs de evento spout, usando as propriedades na configuração de arquivo para instatiate-lo. É importante observar que este exemplo cria quantas tarefas spouts como o número de partições no Hub do evento, para poder usar o paralelismo máximo permitido pelo Hub evento.

<!-- Links -->
[Visão geral de Hubs de evento]: ../articles/event-hubs/event-hubs-overview.md
[HDInsight tempestade]: ../articles/hdinsight/hdinsight-storm-overview.md
[Tutorial de análise de sensor HDInsight]: ../articles/hdinsight/hdinsight-storm-sensor-data-analysis.md

<!-- Images -->

[12]: ./media/service-bus-event-hubs-get-started-receive-storm/create-storm1.png