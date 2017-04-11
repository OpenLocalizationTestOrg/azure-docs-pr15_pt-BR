<properties
    pageTitle="O que é HBase no HDInsight? | Microsoft Azure"
    description="Uma introdução ao Apache HBase em HDInsight, um banco de dados NoSQL desenvolver Hadoop. Aprenda sobre casos de uso e HBase se compara a outros clusters Hadoop."
    keywords="bigtable, nosql, o que é hbase"
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
    ms.topic="get-started-article"
    ms.date="09/14/2016"
    ms.author="jgao"/>



# <a name="what-is-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-hadoop"></a>O que é HBase no HDInsight: NoSQL de um banco de dados que fornece recursos semelhantes a BigTable para Hadoop

Apache HBase é um banco de dados de NoSQL de código-fonte aberto, que é criado no Hadoop e modelado Google BigTable. HBase fornece acesso aleatório e consistência forte para grandes quantidades de dados não estruturados e semi-estruturados em um banco de dados schemaless organizado por famílias de coluna.

Dados são armazenados nas linhas de uma tabela e dados dentro de uma linha são agrupados por família de coluna. HBase é um banco de dados schemaless no sentido que as colunas nem o tipo de dados armazenados em eles precisam ser definidos antes de usá-los. O código de código-fonte aberto linear escalas para lidar com petabytes de dados em milhares de nós. Ele pode depender redundância de dados, processamento em lotes e outros recursos que são fornecidos por aplicativos distribuídos no ecossistema do Hadoop.

## <a name="how-is-hbase-implemented-in-azure-hdinsight"></a>Como HBase é implementado no Azurehdinsight?

HDInsight HBase é oferecida como um cluster gerenciado que é integrado ao ambiente do Azure. Os clusters são configurados para armazenar dados diretamente no armazenamento de Blob do Azure, que oferece baixa latência e elasticidade aumento em Opções de desempenho e custo. Isso permite que os clientes criem sites interativos que funcionam com grandes conjuntos de dados, criar serviços que armazenam dados sensor e telemetria de milhões de pontos de extremidade e analisar dados com Hadoop trabalhos. HBase e Hadoop são bons pontos de partida para projetos de dados grande no Azure; em particular, ele podem habilitar aplicativos em tempo real trabalhar com grandes conjuntos de dados.

A implementação de HDInsight utiliza a arquitetura de escala-out de HBase para fornecer fragmentação automática de tabelas, consistência forte para leituras e gravações e failover automático. Melhora o desempenho na memória cache para leituras e alta produtividade streaming para gravações. Provisionamento de rede virtual também está disponível para HDInsight HBase. Para obter detalhes, consulte [clusters de provisionar HDInsight em rede Virtual do Azure] [hbase-provision-vnet].

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Como os dados são gerenciados no HDInsight HBase?

Dados podem ser gerenciados no HBase usando o `create`, `get`, `put`, e `scan` comandos do shell HBase. Dados são gravados ao banco de dados usando `put` e ler usando `get`. O `scan` comando é usado para obter dados de várias linhas em uma tabela. Dados também podem ser gerenciados usando a HBase c# API, que fornece uma biblioteca de cliente sobre a API REST HBase. Um banco de dados de HBase também pode ser consultado usando a seção. Para uma introdução a esses modelos de programação, consulte [Introdução ao uso HBase com Hadoop em HDInsight][hbase-get-started]. Processadores co também estão disponíveis, que permitem que o processamento de dados em nós que hospedam o banco de dados.


## <a name="scenarios-use-cases-for-hbase"></a>Cenários: Casos de uso HBase
O caso de uso canônico para qual BigTable (e por extensão, HBase) foi criado foi pesquisa na web. Mecanismos de pesquisa criar índices que mapeiam termos às páginas da web que os contêm. Mas há muitos outros casos de uso que HBase é adequado para — vários dos quais são detalhados nesta seção.

- Repositório de chave-valor

    HBase pode ser usado como um repositório de chave-valor, e ele é adequado para gerenciar os sistemas de mensagens. Facebook usa HBase para seu sistema de mensagens e é ideal para armazenamento e gerenciamento de comunicações da Internet. WebTable usa HBase para pesquisar e gerencie tabelas que são extraídas de páginas da Web.

- Dados

    HBase é útil para capturar dados coletados forma incremental de várias origens. Isso inclui análise social, série temporal, mantendo painéis interativos atualizado com as tendências e contadores e gerenciando sistemas de log de auditoria. Exemplos incluem Bloomberg Traders terminal e a hora série de banco de dados aberto (OpenTSDB), que armazena e fornece acesso a métricas coletadas sobre a saúde dos sistemas de servidor.

- Consulta em tempo real

    [Rio](http://phoenix.apache.org/) é um mecanismo de consulta SQL para Apache HBase. Ele é acessado como um driver JDBC, e ela permite consultar e gerenciando HBase tabelas usando SQL.

- HBase como uma plataforma

    Aplicativos podem ser executados na parte superior HBase usando-o como um armazenamento de dados. Alguns exemplos incluem rio, OpenTSDB, Kiji e Titan. Aplicativos também podem integrar com HBase. Alguns exemplos incluem seção, porco, Solr, tempestade, Flume, Impala, Spark, Ganglia e detalhada.


##<a name="next-steps"></a>Próximas etapas

- [Começar a usar HBase com Hadoop em HDInsight][hbase-get-started]
- [Provisionar HDInsight clusters em rede Virtual do Azure] [hbase-provision-vnet]
- [Configurar a duplicação de HBase em HDInsight](hdinsight-hbase-geo-replication.md)
- [Analisar sentimento Twitter com HBase em HDInsight][hbase-twitter-sentiment]
- [Usar Maven para construir aplicativos de Java que usam HBase com HDInsight (Hadoop)][hbase-build-java-maven]

##<a name="see-also"></a>Consulte também

- [Apache HBase](https://hbase.apache.org/)
- [Bigtable: Um sistema de armazenamento distribuído para dados estruturados](http://research.google.com/archive/bigtable.html)




[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
