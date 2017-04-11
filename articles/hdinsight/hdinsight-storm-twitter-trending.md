<properties
   pageTitle="Twitter tópicos tendências com Apache tempestade em HDInsight | Microsoft Azure"
   description="Saiba como usar Trident para criar uma topologia de tempestade Apache que determina tendências tópicos no Twitter com base em hashtags."
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
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="determine-twitter-trending-topics-with-apache-storm-on-hdinsight"></a>Determinar Twitter tópicos tendências com Apache tempestade em HDInsight

Saiba como usar Trident para criar uma topologia de tempestade que determina tendências tópicos (marcas de hash) no Twitter.

Trident é uma abstração de alto nível que fornece ferramentas como junções, agregações, agrupamento, funções e filtros. Além disso, Trident adiciona primitivos para fazer o processamento de estado, incremental. Este exemplo demonstra como você pode criar uma topologia usando um spout personalizado, função e várias funções internas fornecidas pelo Trident.

> [AZURE.NOTE] Esse exemplo é muito baseado no exemplo [Trident tempestade](https://github.com/jalonsoramos/trident-storm) por Juan Alonso.

##<a name="requirements"></a>Requisitos

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java e o JDK 1.7</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Gito</a>

* Uma conta de desenvolvedor do Twitter

##<a name="download-the-project"></a>Baixar o projeto

Use o seguinte código para clonar o projeto localmente.

    git clone https://github.com/Blackmist/TwitterTrending

##<a name="topology"></a>Topologia

A topologia para este exemplo é da seguinte maneira:

![topologia](./media/hdinsight-storm-twitter-trending/trident.png)

> [AZURE.NOTE] Este é um modo de exibição simplificado da topologia. Várias instâncias dos componentes serão distribuídas entre os nós no cluster.

O código de Trident que implementa a topologia é da seguinte maneira:

    topology.newStream("spout", spout)
        .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
        .groupBy(new Fields("hashtag"))
        .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
        .newValuesStream()
        .applyAssembly(new FirstN(10, "count"))
        .each(new Fields("hashtag", "count"), new Debug());

Este código faz o seguinte:

1. Cria um novo fluxo da spout. O spout recupera tweets do Twitter e filtra para palavras-chave específicas (amor, músicas e café neste exemplo).

2. HashtagExtractor, uma função personalizada, é usado para extrair marcas de hash de cada tweet. Estes são emitidos para o fluxo.

3. O fluxo é agrupado por marca de hash e passado para um agregador. Este agregador cria uma contagem de quantas vezes cada marca de hash ocorreu. Estes dados são mantidos na memória. Finalmente, um novo fluxo é emitido que contém a marca de hash e a contagem.

4. Como só estamos interessados as marcas de hash mais populares para um determinado lote de tweets, o conjunto de **FirstN** é aplicado para retornar somente os 10 valores principais, com base no campo contagem.

> [AZURE.NOTE] Além de spout e HashtagExtractor, estamos usando funcionalidade Trident interna.
>
> Para obter informações sobre operações internas, consulte <a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">storm.trident.operation.builtin de pacote</a>.
>
> Para implementações Trident estados diferente de MemoryMapState, consulte o seguinte:
>
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">Pesquisa de elástica de Trident tempestade</a>
>
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">relacionada Trident</a>

###<a name="the-spout"></a>O spout

Spout, **TwitterSpout**, usa <a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a> para recuperar tweets do Twitter. Um filtro é criado (amor, músicas e café neste exemplo) e as entrada tweets (status) que correspondem ao filtro são armazenados em uma fila de bloqueio vinculada. (Para obter mais informações, consulte <a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank">Classe LinkedBlockingQueue</a>.) Por fim, os itens são puxados da fila e emitidas à topologia.

###<a name="the-hashtagextractor"></a>O HashtagExtractor

Para extrair marcas de hash, <a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities</a> é usado para recuperar todas as marcas de hash contidas a tweet. Em seguida, esses são emitidos para o fluxo.

##<a name="enable-twitter"></a>Habilitar o Twitter

Use as etapas a seguir para registrar um novo aplicativo do Twitter e obter as informações de token consumidor e o acesso necessárias para ler do Twitter:

1. Vá para <a href="https://apps.twitter.com" target="_blank">Aplicativos do Twitter</a> e clique no botão **Criar novo aplicativo** . Ao preencher o formulário, deixe o campo de **URL de retorno** vazio.

2. Quando o aplicativo é criado, clique na guia **chaves e Tokens de acesso** .

3. Copie as informações de **Chave do consumidor** e **Segredo do consumidor** .

4. Na parte inferior da página, selecione **Criar meu token de acesso** não se existirem nenhum tokens. Quando os tokens tem sido criados, copie as informações de **Token de acesso** e **Segredo de Token de acesso** .

5. No projeto **TwitterSpoutTopology** que você clonar anteriormente, abra o arquivo **resources/twitter4j.properties** , adicione as informações reunidas nas etapas anteriores e salve o arquivo.

##<a name="build-the-topology"></a>Criar uma topologia

Use o código a seguir para criar o projeto:

        cd [directoryname]
        mvn compile

##<a name="test-the-topology"></a>Testar a topologia

Use o seguinte comando para testar a topologia localmente:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

Depois que a topologia for iniciado, você deve ver informações de depuração que contém o hash marcas e contagem emitidas pela topologia. A saída deve aparecer semelhante ao seguinte:

    DEBUG: [Quicktellervalentine, 7]
    DEBUG: [GRAMMYs, 7]
    DEBUG: [AskSam, 7]
    DEBUG: [poppunk, 1]
    DEBUG: [rock, 1]
    DEBUG: [punkrock, 1]
    DEBUG: [band, 1]
    DEBUG: [punk, 1]
    DEBUG: [indonesiapunkrock, 1]

##<a name="next-steps"></a>Próximas etapas

Agora que você tenha testado a topologia localmente, descubra como implantar a topologia: [implantar e gerenciar topologias Apache tempestade em HDInsight](hdinsight-storm-deploy-monitor-topology.md).

Você também pode estar interessado nos seguintes tópicos de tempestade:

* [Desenvolva topologias Java para tempestade em HDInsight usando Maven](hdinsight-storm-develop-java-topology.md)

* [Desenvolva c# topologias para tempestade em HDInsight usando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Para obter mais exemplos de tempestade para HDinsight:

* [Topologias de exemplo para tempestade em HDInsight](hdinsight-storm-example-topology.md)
