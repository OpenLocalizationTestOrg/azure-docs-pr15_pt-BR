<properties
   pageTitle="Desenvolva topologias baseado em Java para Apache tempestade | Microsoft Azure"
   description="Aprenda a criar topologias de tempestade em Java Criando uma topologia de contagem do word simples."
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
   ms.date="09/14/2016"
   ms.author="larryfr"/>

#<a name="develop-java-based-topologies-for-a-basic-word-count-application-with-apache-storm-and-maven-on-hdinsight"></a>Desenvolver topologias baseado em Java para um aplicativo de básicas de contagem de palavras com tempestade Apache e Maven em HDInsight

Aprenda a criar uma topologia baseada em Java para Apache tempestade em HDInsight usando Maven. Você orientará durante o processo de criação de um aplicativo de contagem de palavras básicas usando Maven e Java, onde a topologia é definida em Java. Em seguida, você aprenderá como definir a topologia usando a estrutura do fluxo.

> [AZURE.NOTE] A estrutura de fluxo está disponível no tempestade 0.10.0 ou posterior. Tempestade 0.10.0 está disponível com HDInsight 3.3 e 3.4.

Depois de concluir as etapas neste documento, você terá uma topologia básica que você pode implantar em Apache tempestade em HDInsight.

> [AZURE.NOTE] Uma versão completa das topologias criado neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount).

##<a name="prerequisites"></a>Pré-requisitos

* <a href="https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html" target="_blank">Kit de desenvolvedor Java (JDK) versão 7</a>

* <a href="https://maven.apache.org/download.cgi" target="_blank">Maven</a>: Maven é um sistema de compilação do projeto para projetos Java.

* Um editor de texto como o bloco de notas, <a href="http://www.gnu.org/software/emacs/" target="_blank">Emacs<a>, <a href="http://www.sublimetext.com/" target="_blank">Texto Sublime</a>, <a href="https://atom.io/" target="_blank">Atom.io</a>, <a href="http://brackets.io/" target="_blank">Brackets.io</a>. Ou você pode usar um ambiente de desenvolvimento integrado (IDE) como <a href="https://eclipse.org/" target="_blank">Eclipse</a> (versão Luna ou posterior).

    > [AZURE.NOTE] O editor ou o IDE pode ter funcionalidade específica para trabalhar com Maven não endereçada neste documento. Para obter informações sobre os recursos de seu ambiente de edição, consulte a documentação do produto que você está usando.

##<a name="configure-environment-variables"></a>Configurar variáveis de ambiente

As seguintes variáveis de ambiente podem ser definidas quando você instala o Java e o JDK. No entanto, você deve verificar que eles existem e que contêm os valores corretos para seu sistema.

* **JAVA_HOME** - devem apontar para a pasta onde o ambiente de tempo de execução Java (JRE) está instalado. Por exemplo, em uma distribuição Unix ou Linux, ele deve ter um valor semelhante a `/usr/lib/jvm/java-7-oracle`. No Windows, ele teria um valor semelhante a`c:\Program Files (x86)\Java\jre1.7`

* **Caminho** - deve conter os seguintes caminhos:

    * **JAVA_HOME** (ou o caminho equivalente)

    * **JAVA_HOME\bin** (ou o caminho equivalente)

    * O diretório onde Maven está instalado

##<a name="create-a-new-maven-project"></a>Criar um novo projeto Maven

Na linha de comando, use o código a seguir para criar um novo projeto de Maven chamado **WordCount**:

    mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

Isso criará um novo diretório chamado **WordCount** no local atual, que contém um projeto Maven básico.

O diretório de **WordCount** conterá os seguintes itens:

* **POM.XML**: contém configurações para o projeto Maven.

* **src\main\java\com\microsoft\example**: contém o código do seu aplicativo.

* **src\test\java\com\microsoft\example**: contém testes para seu aplicativo. Neste exemplo, podemos não criarão testes.

###<a name="remove-the-example-code"></a>Remover o código de exemplo

Como podemos criarão nosso aplicativo, exclua o teste gerado e os arquivos do aplicativo:

*  **src\test\java\com\microsoft\example\AppTest.Java**

*  **src\main\java\com\microsoft\example\App.Java**

##<a name="add-properties"></a>Adicionar propriedades

Maven permite que você defina valores de nível de projeto chamado de propriedades. Adicione o seguinte após o `<url>http://maven.apache.org</url>` linha:

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <!--
        Storm 0.10.0 is for HDInsight 3.3 and 3.4.
        To find the version information for earlier HDInsight cluster
        versions, see https://azure.microsoft.com/en-us/documentation/articles/hdinsight-component-versioning/
        -->
        <storm.version>0.10.0</storm.version>
    </properties>

Agora podemos usar esses valores em outras seções. Por exemplo, ao especificar a versão dos componentes de tempestade, podemos usar `${storm.version}` em vez de codificação um valor.

##<a name="add-dependencies"></a>Adicionar dependências

Como isso é uma topologia de tempestade, é necessário adicionar uma dependência de componentes de tempestade. Abra o arquivo **pom.xml** e adicione o seguinte código na ** &lt;dependências >** seção:

    <dependency>
      <groupId>org.apache.storm</groupId>
      <artifactId>storm-core</artifactId>
      <version>${storm.version}</version>
      <!-- keep storm out of the jar-with-dependencies -->
      <scope>provided</scope>
    </dependency>

Em tempo de compilação, Maven usa essas informações para procurar **tempestade-core** no repositório Maven. Primeiro, ele procura no repositório no computador local. Se os arquivos não estiverem lá, ele será baixá-los do repositório Maven público e armazená-los no repositório local.

> [AZURE.NOTE] Observe o `<scope>provided</scope>` linha na seção adicionamos. Isso informa Maven excluir **tempestade-core** de quaisquer arquivos JAR que criamos, porque ele será fornecido pelo sistema. Isso permite que os pacotes criados para ser um pouco menores e garante que eles usarão os bits de **tempestade-core** que estão incluídos na tempestade em cluster HDInsight.

##<a name="build-configuration"></a>Criar configuração

Plug-ins do Maven permitem personalizar os estágios de compilação do projeto, como como o projeto é compilado ou como compactá-la em um arquivo JAR. Abra o arquivo **pom.xml** e adicione o seguinte código diretamente acima do `</project>` linha.

    <build>
      <plugins>
      </plugins>
      <resources>
      </resources>
    </build>

Esta seção será usada para adicionar plug-ins, recursos e outras opções de configuração de compilação. Para obter uma referência completa do arquivo __pom.xml__ , consulte [http://maven.apache.org/pom.html](http://maven.apache.org/pom.html).

###<a name="add-plug-ins"></a>Adicionar plug-ins

Para topologias de tempestade, o <a href="http://mojo.codehaus.org/exec-maven-plugin/" target="_blank">Plug-in do executivo Maven</a> é útil porque ela permite que você execute facilmente a topologia localmente em seu ambiente de desenvolvimento. Adicione o seguinte para o `<plugins>` seção do arquivo **pom.xml** para incluir o plug-in executivo Maven:

    <plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <version>1.4.0</version>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

> [AZURE.NOTE] Observe que o `<mainClass>` usos de entrada `${storm.topology}`. Não definimos isso anteriormente na seção Propriedades (mas poderia temos.) Em vez disso, podemos definirá esse valor na linha de comando quando a topologia em seu ambiente de desenvolvimento em uma etapa posterior.

Outro útil plug-in é o <a href="http://maven.apache.org/plugins/maven-compiler-plugin/" target="_blank">Plug-in do Apache Maven compilador</a>, que é usada para alterar as opções de compilação. O principal motivo que precisamos isso é alterar a versão de Java que Maven usa para a origem e destino para o seu aplicativo. Queremos versão 1.7.

Adicione o seguinte no `<plugins>` seção do arquivo **pom.xml** para incluir o plug-in Apache Maven compilador e definir as versões de origem e destino para 1.7.

    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.3</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

###<a name="configure-resources"></a>Configurar recursos

A seção recursos permite que você inclua recursos não de código como arquivos de configuração necessários pelos componentes da topologia. Para este exemplo, adicione o seguinte no `<resources>` seção do arquivo **pom.xml** .

    <resource>
        <directory>${basedir}/resources</directory>
        <filtering>false</filtering>
        <includes>
          <include>log4j2.xml</include>
        </includes>
    </resource>

Isso adiciona o diretório de recursos na raiz do projeto (`${basedir}`) como um local que contém recursos e inclui o arquivo chamado __log4j2.xml__. Esse arquivo é usado para configurar quais informações são registradas pela topologia.

##<a name="create-the-topology"></a>Criar a topologia

Uma topologia de tempestade baseado em Java consiste em três componentes que você deve criar (ou referência) como uma dependência.

* **Spouts**: lê fontes de dados externos e emite fluxos de dados para a topologia.

* **Bolts**: executa o processamento em fluxos emitidos por spouts ou outros parafusos e emite um ou mais fluxos.

* **Topologia**: define como o spouts parafusos são organizados e fornece o ponto de entrada para a topologia.

###<a name="create-the-spout"></a>Criar a spout

Para reduzir os requisitos de configuração de fontes de dados externos, a seguinte spout simplesmente emite sentenças aleatórias. É uma versão modificada de um spout que é fornecida com os [exemplos de tempestade Starter](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).

> [AZURE.NOTE] Para um exemplo de uma spout que lê a partir de uma fonte de dados externos, consulte um dos seguintes exemplos:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): um spout de exemplo que lê do Twitter
>
> * [Tempestade Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): um spout que lê de Kafka

Para o spout, crie um novo arquivo chamado **RandomSentenceSpout.java** no diretório **src\main\java\com\microsoft\example** e use o seguinte conteúdo:

    /**
     * Licensed to the Apache Software Foundation (ASF) under one
     * or more contributor license agreements.  See the NOTICE file
     * distributed with this work for additional information
     * regarding copyright ownership.  The ASF licenses this file
     * to you under the Apache License, Version 2.0 (the
     * "License"); you may not use this file except in compliance
     * with the License.  You may obtain a copy of the License at
     *
     * http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

     /**
      * Original is available at https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/RandomSentenceSpout.java
      */

    package com.microsoft.example;

    import backtype.storm.spout.SpoutOutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichSpout;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Values;
    import backtype.storm.utils.Utils;

    import java.util.Map;
    import java.util.Random;

    //This spout randomly emits sentences
    public class RandomSentenceSpout extends BaseRichSpout {
      //Collector used to emit output
      SpoutOutputCollector _collector;
      //Used to generate a random number
      Random _rand;

      //Open is called when an instance of the class is created
      @Override
      public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
      //Set the instance collector to the one passed in
        _collector = collector;
        //For randomness
        _rand = new Random();
      }

      //Emit data to the stream
      @Override
      public void nextTuple() {
      //Sleep for a bit
        Utils.sleep(100);
        //The sentences that will be randomly emitted
        String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
            "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
        //Randomly pick a sentence
        String sentence = sentences[_rand.nextInt(sentences.length)];
        //Emit the sentence
        _collector.emit(new Values(sentence));
      }

      //Ack is not implemented since this is a basic example
      @Override
      public void ack(Object id) {
      }

      //Fail is not implemented since this is a basic example
      @Override
      public void fail(Object id) {
      }

      //Declare the output fields. In this case, an sentence
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("sentence"));
      }
    }

Reserve um momento para ler os comentários de código para entender como funciona este spout.

> [AZURE.NOTE] Embora essa topologia Use apenas uma spout, outras pessoas podem ter vários que alimentar dados de diferentes fontes na topologia.

###<a name="create-the-bolts"></a>Criar os práticos

Parafusos manipulam o processamento de dados. Essa topologia, temos dois parafusos:

* **SplitSentence**: divide as frases emitidas por **RandomSentenceSpout** em palavras individuais.

* **WordCount**: conta quantas vezes cada palavra ocorreu.

> [AZURE.NOTE] Parafusos podem fazer literalmente algo, por exemplo, computação, persistência ou conversando componentes externos.

Crie dois novos arquivos, **SplitSentence.java** e **WordCount.Java** no diretório **src\main\java\com\microsoft\example** . Use o seguinte como o conteúdo para os arquivos:

**SplitSentence**

    package com.microsoft.example;

    import java.text.BreakIterator;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class SplitSentence extends BaseBasicBolt {

      //Execute is called to process tuples
      @Override
      public void execute(Tuple tuple, BasicOutputCollector collector) {
        //Get the sentence content from the tuple
        String sentence = tuple.getString(0);
        //An iterator to get each word
        BreakIterator boundary=BreakIterator.getWordInstance();
        //Give the iterator the sentence
        boundary.setText(sentence);
        //Find the beginning first word
        int start=boundary.first();
        //Iterate over each word and emit it to the output stream
        for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
          //get the word
          String word=sentence.substring(start,end);
          //If a word is whitespace characters, replace it with empty
          word=word.replaceAll("\\s+","");
          //if it's an actual word, emit it
          if (!word.equals("")) {
            collector.emit(new Values(word));
          }
        }
      }

      //Declare that emitted tuples will contain a word field
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("word"));
      }
    }

**WordCount**

    package com.microsoft.example;

    import java.util.HashMap;
    import java.util.Map;
    import java.util.Iterator;

    import backtype.storm.Constants;
    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;
    import backtype.storm.Config;

    // For logging
    import org.apache.logging.log4j.Logger;
    import org.apache.logging.log4j.LogManager;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class WordCount extends BaseBasicBolt {
        //Create logger for this class
        private static final Logger logger = LogManager.getLogger(WordCount.class);
        //For holding words and counts
        Map<String, Integer> counts = new HashMap<String, Integer>();
        //How often we emit a count of words
        private Integer emitFrequency;

        // Default constructor
        public WordCount() {
            emitFrequency=5; // Default to 60 seconds
        }

        // Constructor that sets emit frequency
        public WordCount(Integer frequency) {
            emitFrequency=frequency;
        }

        //Configure frequency of tick tuples for this bolt
        //This delivers a 'tick' tuple on a specific interval,
        //which is used to trigger certain actions
        @Override
        public Map<String, Object> getComponentConfiguration() {
            Config conf = new Config();
            conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
            return conf;
        }

        //execute is called to process tuples
        @Override
        public void execute(Tuple tuple, BasicOutputCollector collector) {
            //If it's a tick tuple, emit all words and counts
            if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
                    && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
                for(String word : counts.keySet()) {
                    Integer count = counts.get(word);
                    collector.emit(new Values(word, count));
                    logger.info("Emitting a count of " + count + " for word " + word);
                }
            } else {
                //Get the word contents from the tuple
                String word = tuple.getString(0);
                //Have we counted any already?
                Integer count = counts.get(word);
                if (count == null)
                    count = 0;
                //Increment the count and store it
                count++;
                counts.put(word, count);
            }
        }

        //Declare that we will emit a tuple containing two fields; word and count
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            declarer.declare(new Fields("word", "count"));
        }
    }

Reserve um tempo para ler os comentários de código para entender como funciona a cada raio.

###<a name="define-the-topology"></a>Definir a topologia

A topologia vincula as spouts e bolts juntos em um gráfico, que define como os dados fluem entre os componentes. Ele também fornece dicas de paralelismo que tempestade usa ao criar instâncias dos componentes de dentro do cluster.

O que vem a seguir é um diagrama básico do gráfico de componentes para essa topologia.

![Diagrama mostrando a organização spouts e parafusos](./media/hdinsight-storm-develop-java-topology/wordcount-topology.png)

Para implementar a topologia, crie um novo arquivo chamado **WordCountTopology.java** no diretório **src\main\java\com\microsoft\example** . Use o seguinte como o conteúdo para o arquivo:

    package com.microsoft.example;

    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.topology.TopologyBuilder;
    import backtype.storm.tuple.Fields;

    import com.microsoft.example.RandomSentenceSpout;

    public class WordCountTopology {

      //Entry point for the topology
      public static void main(String[] args) throws Exception {
      //Used to build the topology
        TopologyBuilder builder = new TopologyBuilder();
        //Add the spout, with a name of 'spout'
        //and parallelism hint of 5 executors
        builder.setSpout("spout", new RandomSentenceSpout(), 5);
        //Add the SplitSentence bolt, with a name of 'split'
        //and parallelism hint of 8 executors
        //shufflegrouping subscribes to the spout, and equally distributes
        //tuples (sentences) across instances of the SplitSentence bolt
        builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
        //Add the counter, with a name of 'count'
        //and parallelism hint of 12 executors
        //fieldsgrouping subscribes to the split bolt, and
        //ensures that the same word is sent to the same instance (group by field 'word')
        builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

        //new configuration
        Config conf = new Config();
        //Set to false to disable debug information
        // when running in production mode.
        conf.setDebug(false);

        //If there are arguments, we are running on a cluster
        if (args != null && args.length > 0) {
          //parallelism hint to set the number of workers
          conf.setNumWorkers(3);
          //submit the topology
          StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
        }
        //Otherwise, we are running locally
        else {
          //Cap the maximum number of executors that can be spawned
          //for a component to 3
          conf.setMaxTaskParallelism(3);
          //LocalCluster is used to run locally
          LocalCluster cluster = new LocalCluster();
          //submit the topology
          cluster.submitTopology("word-count", conf, builder.createTopology());
          //sleep
          Thread.sleep(10000);
          //shut down the cluster
          cluster.shutdown();
        }
      }
    }

Reserve um tempo para ler os comentários de código para entender como a topologia é definida e, em seguida, enviada ao cluster.

###<a name="configure-logging"></a>Configurar log

Tempestade usa Apache Log4j para informações de log. Se você não configurar o registro em log, a topologia será emitir muitas informações de diagnóstico, que podem ser difícil de ler. Para controlar o que está conectado, crie um arquivo denominado __log4j2.xml__ no diretório de __recursos__ . Use o seguinte como o conteúdo do arquivo.

    <?xml version="1.0" encoding="UTF-8"?>
    <Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.microsoft.example" level="trace" additivity="false">
            <AppenderRef ref="STDOUT"/>
        </Logger>
        <Root level="error">
            <Appender-Ref ref="STDOUT"/>
        </Root>
    </Loggers>
    </Configuration>

Isso configura um novo agente para a classe __com.microsoft.example__ , que inclui os componentes nesta topologia de exemplo. O nível é definido para rastrear para esse agente, que irá capturar qualquer informação de log emitida por componentes nessa topologia. Se você examinar novamente o código para esse projeto, você notará que apenas o arquivo de WordCount.java implementa log; ele registrará a contagem de cada palavra.

O `<Root level="error">` seção configura o nível de raiz de log (não no __com.microsoft.example__, tudo) para registrar apenas informações de erro.

> [AZURE.IMPORTANT] Enquanto isso reduz muito as informações registradas ao testar uma topologia no seu ambiente de desenvolvimento, ele não remove todas as informações de depuração produzidas durante a execução em um cluster em produção. Para reduzir essas informações, você também deverá definir a depuração como false na configuração enviada ao cluster. Ver o código de WordCountTopology.java neste documento para obter um exemplo. 

Para obter mais informações sobre como configurar o registro em log para Log4j, consulte [http://logging.apache.org/log4j/2.x/manual/configuration.html](http://logging.apache.org/log4j/2.x/manual/configuration.html).

> [AZURE.NOTE] Versão de tempestade 0.10.0 usa Log4j 2. x. Versões mais antigas do tempestade usaram Log4j 1. x, que usado um formato diferente para a configuração de log. Para obter informações sobre a configuração mais antiga, consulte [http://wiki.apache.org/logging-log4j/Log4jXmlFormat](http://wiki.apache.org/logging-log4j/Log4jXmlFormat).

##<a name="test-the-topology-locally"></a>Testar a topologia localmente

Depois de salvar os arquivos, use o seguinte comando para testar a topologia localmente.

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology

Como ele é executado, a topologia exibirá informações de inicialização. Em seguida, ele começa a linhas semelhantes aos seguintes são exibidas como frases são emitidas da spout e processadas pelos práticos.

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Examinando o log emitido pelo raio WordCount, podemos pode ver que ' e ' tenha sido emitido 113 vezes. A contagem de continuará subir desde que a topologia executado porque o spout continuamente emite as frases mesmas.

Também haverá um segundo intervalo 5 entre a emissão de palavras e contagens. Isso ocorre porque o componente __WordCount__ está configurado para emitir apenas informações quando chega uma tupla de escala e ele solicita que tal tuplas sejam entregues somente 5 segundos por padrão.

## <a name="convert-the-topology-to-flux"></a>Converter a topologia em fluxo

Fluxo é uma nova estrutura disponível com tempestade 0.10.0, que permite que você separe configuração da implementação. Os componentes (parafusos e spouts,) ainda são definidos no Java, mas a topologia é definida usando um arquivo YAML.

O arquivo YAML define os componentes para usar para a topologia, como os dados fluem entre eles, e os valores a serem para usar ao inicializar os componentes. Você pode incluir um arquivo YAML como parte do arquivo jar que contém o projeto quando você implantá-lo, ou você pode usar um arquivo YAML externo quando você começa a topologia.

1. Mova o arquivo de __WordCountTopology.java__ fora do projeto. Anteriormente, isso definido a topologia, mas podemos não usá-lo para o fluxo.

2. No diretório de __recursos__ , crie um novo arquivo chamado __topology.yaml__. Use o seguinte como o conteúdo deste arquivo.

        # topology definition

        # name to be used when submitting. This is what shows up...
        # in the Storm UI/storm command-line tool as the topology name
        # when submitted to Storm
        name: "wordcount"

        # Topology configuration
        config:
        # Hint for the number of workers to create
        topology.workers: 1

        # Spout definitions
        spouts:
        - id: "sentence-spout"
            className: "com.microsoft.example.RandomSentenceSpout"
            # parallelism hint
            parallelism: 1

        # Bolt definitions
        bolts:
        - id: "splitter-bolt"
            className: "com.microsoft.example.SplitSentence"
            parallelism: 1

        - id: "counter-bolt"
            className: "com.microsoft.example.WordCount"
            constructorArgs:
            - 10
            parallelism: 1

        # Stream definitions
        streams:
        - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
            # The stream emitter
            from: "sentence-spout"
            # The stream consumer
            to: "splitter-bolt"
            # Grouping type
            grouping:
            type: SHUFFLE

        - name: "Splitter -> Counter"
            from: "splitter-bolt"
            to: "counter-bolt"
            grouping:
            type: FIELDS
            # field(s) to group on
            args: ["word"]

    Reserve um tempo para ler e entender o que faz cada seção e como ele se relaciona a definição baseada em Java no arquivo __WordCountTopology.java__ .

3. Faça as seguintes alterações no arquivo __pom.xml__ .

    * Adicionar a seguinte nova dependência no `<dependencies>` seção:

            <!-- Add a dependency on the Flux framework -->
            <dependency>
                <groupId>org.apache.storm</groupId>
                <artifactId>flux-core</artifactId>
                <version>${storm.version}</version>
            </dependency>

    * Adicionar o plug-in seguir o `<plugins>` seção. Este plug-in trata a criação de um pacote (arquivo jar) para o projeto e aplica algumas transformações específicas para fluxo ao criar o pacote.

            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <transformers>
                        <!-- Keep us from getting a "can't overwrite file error" -->
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                        <!-- We're using Flux, so refer to it as main -->
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>org.apache.storm.flux.Flux</mainClass>
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>

    * O __Executivo de plugin maven__ `<configuration>` seção, altere o valor de `<mainClass>` para `org.apache.storm.flux.Flux`. Isso permite que o fluxo tratar executando a topologia quando podemos executá-la localmente em desenvolvimento.

    * No `<resources>` seção, adicione o seguinte para o `<includes>`. Isso inclui o arquivo YAML que define a topologia como parte do projeto.
    
            <include>topology.yaml</include>

## <a name="test-the-flux-topology-locally"></a>Testar a topologia de fluxo localmente

1. Use o seguinte para compilar e executar a topologia de fluxo usando Maven.

        mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    
    Se você estiver usando o PowerShell, use o seguinte:
    
        mvn compile exec:java "-Dexec.args=--local -R /topology.yaml"

    Se você estiver em um sistema Unix/Linux/OS X e tiver [instalado tempestade em seu ambiente de desenvolvimento](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html), você pode usar em vez disso, os seguintes comandos:

        mvn compile package
        storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml

    O `--local` parâmetro executa a topologia no modo local no seu ambiente de desenvolvimento. O `-R /topology.yaml` parâmetro usa o `topology.yaml` recurso de arquivo do arquivo jar para definir a topologia.

    Como ele é executado, a topologia exibirá informações de inicialização. Em seguida, ele começa a linhas semelhantes aos seguintes são exibidas como frases são emitidas da spout e processadas pelos práticos.

        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    
    Haverá um atraso de segunda 10 entre lotes de informações registradas, como o `topology.yaml` arquivo passa um valor de `10` quando o componente de WordCount é criado. Define o intervalo de atraso para a tupla de escala para 10 segundos.

2.  Faça uma cópia da `topology.yaml` arquivo do projeto. Chamar algo parecido com `newtopology.yaml`. No arquivo, localize a seguinte seção e altere o valor de `10` para `5`. Esse procedimento altera o intervalo entre a emissão lotes de contagem de palavras de 10 segundos para 5.

          - id: "counter-bolt"
            className: "com.microsoft.example.WordCount"
            constructorArgs:
            - 5
            parallelism: 1

3. Para executar a topologia, use o seguinte comando:

        mvn exec:java -Dexec.args="--local /path/to/newtopology.yaml"

    Ou, se você tiver tempestade em seu ambiente de desenvolvimento Unix/Linux/OS X:

        storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local /path/to/newtopology.yaml

    Alterar a `/path/to/newtopology.yaml` como o caminho para o arquivo de newtopology.yaml que você criou na etapa anterior. Este comando usará o newtopology.yaml como a definição de topologia. Como podemos não incluem o `compile` parâmetro, Maven for reutilizar a versão do projeto criado nas etapas anteriores.

    Depois que a topologia for iniciado, você deve observar que o tempo entre lotes emitidos foi alterado para refletir o valor em newtopology.yaml. Para que você possa ver o que você pode alterar a configuração através de um arquivo YAML sem precisar recompilar a topologia.

Há vários outros recursos que fluxo fornece que não são discutidos aqui, como substituição de variáveis no arquivo YAML com base nos parâmetros passados em tempo de execução ou de variáveis de ambiente. Para obter mais informações sobre esses e outros recursos do framework fluxo, consulte [fluxo (https://storm.apache.org/releases/0.10.0/flux.html)](https://storm.apache.org/releases/0.10.0/flux.html).

##<a name="trident"></a>Trident

Trident é uma abstração de alto nível fornecidos pelo tempestade. Ele oferece suporte processamento com estado. A principal vantagem de Trident é que ela pode garantir que todas as mensagens que insere a topologia é processada apenas uma vez. Isso é difícil atingir em uma topologia Java bruta, quais garantia que as mensagens será processada pelo menos uma vez. Também existem outras diferenças, como componentes internos que podem ser usados em vez de criar parafusos. Na verdade, parafusos completamente são substituídos pelos componentes de menos genérico, como filtros, projeções e funções.

Aplicativos Trident podem ser criados usando projetos Maven. Você usa as mesmas etapas básicas, conforme apresentado anteriormente neste artigo — somente o código é diferente. Trident também (atualmente) não pode ser usado com a estrutura do fluxo.

Para obter mais informações sobre Trident, consulte <a href="http://storm.apache.org/documentation/Trident-API-Overview.html" target="_blank">Visão geral da API Trident</a>.

Para obter um exemplo de um aplicativo de Trident, consulte [Twitter tópicos tendências com Apache tempestade em HDInsight](hdinsight-storm-twitter-trending.md).

##<a name="next-steps"></a>Próximas etapas

Você aprendeu a criar uma topologia de tempestade usando Java. Agora, saiba como:

* [Implantar e gerenciar topologias Apache tempestade em HDInsight](hdinsight-storm-deploy-monitor-topology.md)

* [Desenvolva c# topologias para Apache tempestade em HDInsight usando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Você pode encontrar mais um exemplo topologias tempestade visitando [topologias de exemplo para tempestade em HDInsight](hdinsight-storm-example-topology.md).
