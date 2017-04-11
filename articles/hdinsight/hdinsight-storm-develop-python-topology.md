<properties
   pageTitle="Usar componentes de Python em uma topologia de tempestade em HDinsight | Microsoft Azure"
   description="Saiba como você pode usar componentes de Python de com tempestade Apache no Azure HDInsight. Você aprenderá a usar componentes de Python de ambas as um Java com base e Clojure com base em topologia de tempestade."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Desenvolver topologias Apache tempestade usando Python em HDInsight

Apache tempestade oferece suporte a vários idiomas, mesmo que permite combinar componentes de vários idiomas em uma topologia. Neste documento, você aprenderá como usar componentes de Python em seu topologias Java e tempestade baseado em Clojure em HDInsight.

##<a name="prerequisites"></a>Pré-requisitos

* Python 2.7 ou superior

* Java JDK 1.7 ou superior

* [Leiningen](http://leiningen.org/)

##<a name="storm-multi-language-support"></a>Suporte a vários idiomas tempestade

Tempestade foi projetado para funcionar com componentes escritos usando qualquer linguagem de programação, mas isso exige que os componentes entenderem como trabalhar com a [definição e mais Próspero banco para tempestade](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Para Python, um módulo é fornecido como parte do projeto tempestade Apache que permite que você facilmente interface com tempestade. Você pode encontrar este módulo em [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Como tempestade Apache é um processo de Java que é executado na máquina Virtual Java (JVM), componentes escritos em outros idiomas são executados como subprocessos. Os bits de tempestade executando na JVM se comunica com esses subprocessos usando JSON mensagens enviadas pela stdin/stdout. Obter mais detalhes sobre a comunicação entre componentes podem ser encontradas na documentação do [protocolo de Multi-lang](https://storm.apache.org/documentation/Multilang-protocol.html) .

###<a name="the-storm-module"></a>O módulo de tempestade

O módulo de tempestade (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py), fornece os bits necessários para criar componentes de Python que funcionam com tempestade.

Isso fornece coisas como `storm.emit` emitir tuplas, e `storm.logInfo` para gravar os logs. Eu faria incentivar ler esse arquivo e entender o que ele oferece.

##<a name="challenges"></a>Desafios

Usando o módulo __storm.py__ , você pode criar spouts Python que consumir dados e parafusos que processam dados, no entanto, a definição de topologia de tempestade geral que conecta as comunicações entre componentes ainda escrita usando Java ou Clojure. Além disso, se você usar Java, você também deve criar componentes Java que atuam como uma interface para os componentes de Python.

Além disso, como clusters de tempestade executado no modo distribuído, você deve garantir que qualquer módulos necessários por seus componentes de Python estão disponíveis em todos os nós de trabalhador no cluster. Tempestade não oferece alguma maneira fácil de fazer isso para recursos de multi-lang - você tem que incluir todas as dependências como parte do arquivo jar para a topologia, ou instalar manualmente dependências em cada nó de trabalho no cluster.

###<a name="java-vs-clojure-topology-definition"></a>Definição de topologia de Java versus Clojure

Dos dois métodos para definir uma topologia, Clojure é de longe o mais fácil/mais limpo possível diretamente componentes de python de referência na definição de topologia. Para obter definições de topologia baseada em Java, você também deve definir componentes Java que lidar com coisas como declarar campos nos tuplas retornadas dos componentes de Python.

Ambos os métodos são descritos neste documento, juntamente com exemplos de projetos.

##<a name="python-components-with-a-java-topology"></a>Componentes de Python com uma topologia de Java

> [AZURE.NOTE] Este exemplo está disponível em [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) no diretório __JavaTopology__ . Este é um projeto Maven com base. Se você estiver familiarizado com Maven, consulte [baseado em Java desenvolver topologias com Apache tempestade em HDInsight](hdinsight-storm-develop-java-topology.md) para obter mais informações sobre como criar um projeto Maven para uma topologia de tempestade.

Uma topologia baseada em Java que usa Python (ou outros componentes de idioma JVM,) exibida inicialmente usar componentes Java; mas, se você analisar em cada uma dos Java spouts/práticos, você verá o código semelhante ao seguinte:

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Isso é onde Java invoca Python e executa o script que contém a lógica de raio real. Os Java spouts/parafusos (por exemplo,) simplesmente declarar os campos na tupla que será emitido pelo componente Python subjacente.

Os arquivos de Python reais são armazenados na `/multilang/resources` diretório neste exemplo. O `/multilang` diretório sejam referenciado na __pom.xml__:

<resources>
    <resource>
        <!-- Where the Python bits are kept -->
        <directory>${basedir} / multilang</directory>
    </resource>
</resources>

Isso inclui todos os arquivos a `/multilang` pasta no jar que será criado deste projeto.

> [AZURE.IMPORTANT] Observe que isso só especifica a `/multilang` diretório e não `/multilang/resources`. Tempestade espera recursos não JVM em uma `resources` directory, portanto é procurou internamente já. Colocar componentes nesta pasta permite referência apenas por nome no código Java. Por exemplo, `super("python", "countbolt.py");`. Outra maneira de pensar neles é que tempestade vê a `resources` diretório como raiz (/) quando estiver acessando recursos de multi-lang.
>
> Para este projeto de exemplo, o `storm.py` módulo está incluído na `/multilang/resources` diretório.

###<a name="build-and-run-the-project"></a>Criar e executar o projeto

Para executar esse projeto localmente, basta usar o seguinte comando Maven para construir e executar no modo local:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Use ctrl + c para interromper o processo.

Para implantar o projeto em um cluster de HDInsight executando o Apache tempestade, use as seguintes etapas:

1. Construir um jar uber:

        mvn package

    Isso criará um arquivo denominado __WordCount - 1.0-SNAPSHOT.jar__ no `/target` diretório para esse projeto.

2. Carregue o arquivo jar cluster Hadoop usando um dos seguintes métodos:

    * Para clusters de HDInsight __baseados em Linux__ : Use `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` para copiar o arquivo jar para cluster, substituindo o nome de usuário com seu nome de usuário SSH e CLUSTERNAME com o nome de cluster HDInsight.

        Quando o arquivo tiver terminado carregando, conectar-se ao cluster usando SSH e começar a usar a topologia`storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`

    * Para clusters de HDInsight __baseados no Windows__ : conectar-se ao painel de tempestade indo para HTTPS://CLUSTERNAME.azurehdinsight.net/ no seu navegador. Substitua CLUSTERNAME com o nome do seu cluster de HDInsight e fornecer o nome de administrador e a senha quando solicitado.

        Usando o formulário, execute as seguintes ações:

        * __Arquivo JAR__: selecione __Procurar__e selecione o arquivo __WordCount-1.0-SNAPSHOT.jar__
        * __Nome da classe__: insira`com.microsoft.example.WordCount`
        * __Parâmetros adicionais__: insira um nome amigável como `wordcount` para identificar a topologia

        Finalmente, selecione __Enviar__ para iniciar a topologia.

> [AZURE.NOTE] Após o início de uma topologia de tempestade executa até parar (eliminadas). Para interromper a topologia, use o `storm kill TOPOLOGYNAME` comando da linha de comando (SSH sessão para um cluster de Linux por exemplo,) ou usando a interface do usuário tempestade, selecione a topologia e selecione o botão __Finalizar__ .

##<a name="python-components-with-a-clojure-topology"></a>Componentes de Python com uma topologia de Clojure

> [AZURE.NOTE] Este exemplo está disponível em [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) no diretório __ClojureTopology__ .

Essa topologia foi criada usando [Leiningen](http://leiningen.org) para [criar um novo projeto de Clojure](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project). Depois disso, foram feitas as seguintes modificações no projeto scaffolded:

* __Project.CLJ__: adicionado dependências de tempestade e exclusões de itens que podem causar problemas quando implantado no servidor de HDInsight.
* __recursos/recursos__: cria um padrão de Leiningen `resources` diretório, no entanto, os arquivos armazenados aqui parecem seja adicionado à raiz do arquivo jar criado a partir deste projeto e tempestade espera arquivos em um diretório Sub chamado `resources`. Para que um diretório sub foi adicionado e os arquivos de Python são armazenados em `resources/resources`. Em tempo de execução, isso será tratado como raiz (/) para acessar componentes de Python.
* __src/WordCount/Core.CLJ__: este arquivo contém a definição de topologia e é referenciado do arquivo __project.clj__ . Para obter mais informações sobre como usar o Clojure para definir uma topologia de tempestade, consulte [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html).

###<a name="build-and-run-the-project"></a>Criar e executar o projeto

__Para criar e executar o projeto localmente__, use o seguinte comando:

    lein clean, run

Para interromper a topologia, use __Ctrl + C__.

__Para criar um uberjar e implantar ao HDInsight__, use as seguintes etapas:

1. Crie um uberjar que contém a topologia e as dependências necessárias:

        lein uberjar

    Isso criará um novo arquivo chamado `wordcount-1.0-SNAPSHOT.jar` no `target\uberjar+uberjar` diretório.
    
2. Use um dos seguintes métodos para implantar e executar a topologia para um cluster de HDInsight:

    * __HDInsight baseados em Linux__
    
        1. Copie o arquivo para o nó principal de cluster HDInsight usando `scp`. Por exemplo:
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            Substitua o nome de usuário por um usuário SSH para cluster e CLUSTERNAME com o nome do seu cluster de HDInsight.
            
        2. Depois que o arquivo foi copiado para o cluster, use SSH para se conectar ao cluster e submeter o trabalho. Para obter informações sobre como usar SSH com HDInsight, consulte um dos seguintes procedimentos:
        
            * [Use SSH com baseado em Linux HDInsight Linux, Unix ou dos X](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [Use SSH com baseado em Linux HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. Uma vez conectado, use o seguinte para iniciar a topologia:
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __HDInsight baseados no Windows__
    
        1. Conectar-se ao painel de tempestade indo para HTTPS://CLUSTERNAME.azurehdinsight.net/ no seu navegador. Substitua CLUSTERNAME com o nome do seu cluster de HDInsight e fornecer o nome de administrador e a senha quando solicitado.

        2. Usando o formulário, execute as seguintes ações:

            * __Arquivo JAR__: selecione __Procurar__e selecione o arquivo __wordcount-1.0-SNAPSHOT.jar__
            * __Nome da classe__: insira`wordcount.core`
            * __Parâmetros adicionais__: insira um nome amigável como `wordcount` para identificar a topologia

            Finalmente, selecione __Enviar__ para iniciar a topologia.

> [AZURE.NOTE] Após o início de uma topologia de tempestade executa até parar (eliminadas). Para interromper a topologia, use o `storm kill TOPOLOGYNAME` comando da linha de comando (SSH sessão para um cluster de Linux) ou usando a interface do usuário tempestade, selecione a topologia e selecione o botão __Finalizar__ .

##<a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu como usar componentes de Python de uma topologia de tempestade. Consulte os seguintes documentos para outras maneiras de usar Python com HDInsight:

* [Como usar Python para streaming MapReduce trabalhos](hdinsight-hadoop-streaming-python.md)
* [Como usar o Python definida pelo usuário funções (UDF) no porco e seção](hdinsight-python.md)
