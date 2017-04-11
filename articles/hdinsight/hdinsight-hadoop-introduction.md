 <properties
    pageTitle="Introdução ao Hadoop - o que é Hadoop em HDInsight? | Microsoft Azure"
    description="Obtenha uma introdução ao Hadoop, processamento de dados de grande distribuído e análise e os componentes do ecossistema do Hadoop na nuvem, na HDInsight."
    keywords="análise de dados grande, Introdução ao hadoop, o que é hadoop, pilha de tecnologia do hadoop, hadoop ecossistema"
    services="hdinsight"
    documentationCenter=""
    authors="cjgronlund"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="cgronlun"/>


# <a name="an-introduction-to-the-hadoop-ecosystem-on-azure-hdinsight"></a>Uma introdução ao ecossistema Hadoop no Azure HDInsight

 Este artigo fornece uma introdução ao Hadoop em Azurehdinsight, seu ecossistema e grande volume. Saiba mais sobre os componentes Hadoop, terminologia comum e cenários para análise de dados grande.

## <a name="what-is-hadoop-on-hdinsight"></a>O que é Hadoop em HDInsight?

Hadoop se refere a um ecossistema de software de código-fonte aberto que é uma estrutura para processamento distribuído, armazenar e a análise de grandes conjuntos de dados em clusters de computadores. Azure HDInsight disponibiliza os componentes de Hadoop da distribuição **Hortonworks dados plataforma (HDP)** na nuvem e implanta e provisiona clusters gerenciados com alta confiabilidade e disponibilidade.  

Apache Hadoop foi o projeto de código-fonte original para o processamento de dados grande. A seguir foi o desenvolvimento de software relacionado e utilitários considerados parte da pilha de tecnologia Hadoop, incluindo Apache seção, Apache HBase, Apache Spark e várias outras. Consulte [Visão geral do ecossistema do Hadoop em HDInsight](#overview) para obter detalhes.

## <a name="what-is-big-data"></a>O que é grande volume?

Grandes dados descreve qualquer corpo grande de informações digitais, do texto em um Twitter feed, as informações de sensor de equipamento industrial, para obter informações sobre cliente navegação e compras em um site. Dados grandes podem ser histórica (significando que dados armazenados) ou em tempo real (significando transmitido diretamente da origem). Dados grandes estão sendo coletados em volumes crescentes, em cada vez mais altas velocidades e em uma expansão vários formatos.

Para grandes de dados fornecer inteligência acionável ou ideias, você deve coletar dados relevantes e faça as perguntas à direita. Você também deve verificar se que os dados estão acessíveis, limpos, analisadas e então apresentados de uma maneira útil. Que é onde pode ajudar a análise de dados grande em Hadoop em HDInsight.

## <a name="overview"></a>Visão geral do ecossistema do Hadoop em HDInsight

HDInsight é uma distribuição de nuvem no Microsoft Azure da pilha de tecnologia Apache Hadoop rápida expansão para análise de dados grande. Ele inclui implementações do Apache Spark, HBase, tempestade, porco, seção, Sqoop, Oozie, Ambari e assim por diante. HDInsight também integra ferramentas de business intelligence (BI) como Power BI, Excel, SQL Server Analysis Services e SQL Server Reporting Services.

### <a name="hadoop-hbase-spark-storm-and-customized-clusters"></a>Hadoop, HBase, Spark, tempestade e clusters personalizadas

HDInsight fornece configurações de cluster para Apache Hadoop, Spark, HBase ou tempestade. Ou, você pode [Personalizar clusters com ações de script](hdinsight-hadoop-customize-cluster-linux.md).

* **Hadoop**: fornece armazenamento de dados confiáveis com [HDFS](#hdfs)e um modelo de programação [MapReduce](#mapreduce) simple para processar e analisar dados em paralelo.

* **<a target="_blank" href="http://spark.apache.org/">Apache Spark</a>**: uma estrutura de processamento paralelo que suporta processamento na memória para melhorar o desempenho dos aplicativos de análise de dados grande, gerar works para SQL, fluxos de dados e aprendizado de máquina. Consulte [Visão geral: o que é Spark Apache no HDInsight?](hdinsight-apache-spark-overview.md)

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>**: um banco de dados NoSQL criado no Hadoop que fornece acesso aleatório e consistência forte para grandes quantidades de dados não estruturados e semiestruturados - potencialmente bilhões de linhas vezes milhões de colunas. Consulte [Visão geral de HBase em HDInsight](hdinsight-hbase-overview.md).

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache tempestade</a>**: um sistema de computação distribuído, em tempo real para processar grandes fluxos de dados rapidamente. Tempestade é oferecida como um cluster gerenciado no HDInsight. Consulte [dados de sensor em tempo real de análise usando tempestade e Hadoop](hdinsight-storm-sensor-data-analysis.md).

### <a name="example-customization-scripts"></a>Scripts de personalização de exemplo

Ações de script são scripts que executar durante a configuração de cluster e podem ser usados para instalar componentes adicionais no cluster. Para clusters baseados em Linux, estas são Bash scripts.

Os scripts de exemplo a seguir são fornecidos pela equipe de HDInsight:

* [Matiz](hdinsight-hadoop-hue-linux.md): um conjunto de aplicativos da web usado para interagir com um cluster. Somente clusters Linux.

* [Giraph](hdinsight-hadoop-giraph-install-linux.md): Graph processamento relações de modelo entre as coisas ou pessoas.

* [R](hdinsight-hadoop-r-scripts-linux.md): uma linguagem de código-fonte aberto e um ambiente para computação estatísticas usadas em aprendizado de máquina.

* [Solr](hdinsight-hadoop-solr-install-linux.md): uma plataforma de pesquisa de escala empresarial que permite a pesquisa de texto completo em dados.

Para obter informações sobre como desenvolver suas próprias ações de Script, consulte [desenvolvimento de ação de Script com HDInsight](hdinsight-hadoop-script-actions-linux.md).


## <a name="what-are-the-hadoop-components-and-utilities"></a>Quais são os componentes de Hadoop e utilitários?

Os seguintes componentes e utilitários estão incluídos em clusters de HDInsight.

* **[Ambari](#ambari)**: provisionamento, gerenciamento, monitoramento e utilitários de Cluster.

* **[Avro](#avro)** (Biblioteca do Microsoft .NET para Avro): serialização de dados para o ambiente do Microsoft .NET.

* **[Seção & HCatalog](#hive)**: Structured Query Language (SQL)-como consultar e uma camada de gerenciamento de armazenamento e tabela.

* **[Mahout](#mahout)**: para máquina scalable aplicativos de aprendizagem.

* **[MapReduce](#mapreduce)**: estrutura herdado para Hadoop distribuído processamento e gerenciamento de recursos. Consulte [fio COLORIDO](#yarn), a estrutura de recursos de última geração.

* **[Oozie](#oozie)**: gerenciamento de fluxo de trabalho.

* **[Rio](#phoenix)**: camada de banco de dados relacional sobre HBase.

* **[Porco](#pig)**: script simples para MapReduce transformações.

* **[Sqoop](#sqoop)**: dados importar e exportar.

* **[Tez](#tez)**: permite que os processos de dados intenso seja executado com eficiência em escala.

* **[Fio COLORIDO](#yarn)**: parte da biblioteca do Hadoop principal e próxima geração do framework MapReduce software.

* **[ZooKeeper](#zookeeper)**: coordenação de processos em sistemas distribuídos.

> [AZURE.NOTE] Para obter informações sobre os componentes específicos e informações de versão, consulte [componentes do Hadoop, controle de versão e ofertas de serviço em HDInsight][component-versioning]

### <a name="ambari"></a>Ambari

Apache Ambari é para provisionar, gerenciar e monitorar clusters Apache Hadoop. Ele inclui uma coleção intuitiva das ferramentas de operador e um conjunto robusto de APIs que ocultam a complexidade do Hadoop, simplificar a operação de clusters. HDInsight baseados em Linux clusters fornecem ambos o Ambari da web interface do usuário e a API REST Ambari, enquanto clusters baseados no Windows fornecem um subconjunto da API REST. Modos de exibição de Ambari em clusters de HDInsight permitir plug-in recursos de interface do usuário.

Consulte [Gerenciar HDInsight clusters usando Ambari](hdinsight-hadoop-manage-ambari.md) (somente Linux), [clusters Monitor Hadoop em HDInsight usando a API Ambari](hdinsight-monitor-use-ambari-api.md)e <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">referência Apache Ambari API</a>.

### <a name="avro"></a>Avro (biblioteca de Microsoft .NET para Avro)

A biblioteca do Microsoft .NET para Avro implementa o formato de troca de dados binários compacta Apache Avro para serialização para o ambiente do Microsoft .NET. Ele usa <a target="_blank" href="http://www.json.org/">JavaScript Object Notation (JSON)</a> para definir um esquema de idioma independente que subscreve interoperabilidade de linguagem, significando que os dados serializados em um idioma podem ser lido em outro. Informações detalhadas sobre o formato podem ser encontradas na < um destino = _ "em branco" href = "http://avro.apache.org/docs/current/spec.html" > Apache Avro especificação</a>.
O formato dos arquivos de Avro suporta o modelo de programação MapReduce distribuído. Os arquivos são "divisíveis", significando que você pode buscar qualquer ponto em um arquivo e iniciar a leitura de um bloco específico. Para descobrir como, consulte [Serialize dados com a biblioteca do Microsoft .NET para Avro](hdinsight-dotnet-avro-serialization.md).


### <a name="hdfs"></a>HDFS

Sistema de arquivo distribuído Hadoop (HDFS) é um sistema de arquivos distribuídos que, com MapReduce e fio COLORIDO, é o núcleo do ecossistema do Hadoop. HDFS é o sistema de arquivo padrão para clusters de Hadoop no HDInsight.

### <a name="hive"></a>Seção & HCatalog

<a target="_blank" href="http://hive.apache.org/">Seção Apache</a> é um software de depósito de dados criado em Hadoop que permite consultar e gerenciar grandes conjuntos de dados no armazenamento distribuído usando uma linguagem SQL semelhante chamada HiveQL. Ramificação, como porco, é uma abstração sobre MapReduce. Quando executado, seção converte consultas em uma série de MapReduce trabalhos. Seção é forma conceitual mais próxima um sistema de gerenciamento de banco de dados relacional que porco e, portanto, é adequada para uso com dados mais estruturados. Para dados não estruturados, porco é a melhor opção. Consulte [usar seção com Hadoop em HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> é uma camada de gerenciamento de armazenamento e tabela para Hadoop que apresenta aos usuários um modo de exibição relacional de dados. Em HCatalog, você pode ler e gravar arquivos em qualquer formato para o qual um SerDe seção (serializador-desserializador) podem ser gravada.

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> é uma biblioteca scalable de algoritmos que são executadas em Hadoop de aprendizado de máquina. Usando os princípios de estatísticas, aplicativos de aprendizado de máquina ensinam sistemas para aprender a partir de dados e usar passaram resultados para determinar o comportamento de futuro. Consulte [recomendações de filme gerar usando Mahout em Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
MapReduce é a estrutura de software herdado para Hadoop para escrever aplicativos ao lote processo grandes conjuntos de dados em paralelo. Um trabalho MapReduce divide grandes conjuntos de dados e organiza os dados em pares de chave-valor para processamento.

[Fio COLORIDO](#yarn) é a estrutura de gerente e aplicativo de recursos de última geração do Hadoop e é conhecido como MapReduce 2.0. MapReduce trabalhos executados em fio COLORIDO.

Para obter mais informações sobre MapReduce, consulte <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> no Wiki do Hadoop.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> é um sistema de coordenação do fluxo de trabalho que gerencia trabalhos de Hadoop. Ele é integrado com a pilha Hadoop e suporta Hadoop trabalhos para MapReduce, porco, seção e Sqoop. Ele também pode ser usado para agendar trabalhos específicos para um sistema, como programas Java ou scripts do shell. Consulte [usar Oozie com Hadoop](hdinsight-use-oozie.md).

### <a name="phoenix"></a>Rio
<a  target="_blank" href="http://phoenix.apache.org/">Rio de Apache</a> é uma camada de banco de dados relacional sobre HBase. Rio inclui um driver JDBC que permite aos usuários consultar e gerenciar tabelas SQL diretamente. Rio traduz consultas e outras instruções em chamadas de API de NoSQL nativas - em vez de usando MapReduce - permitindo aplicativos mais rápidos sobre NoSQL lojas. Consulte [usar Apache rio e esquilo com HBase clusters](hdinsight-hbase-phoenix-squirrel.md).


### <a name="pig"></a>Porco
<a  target="_blank" href="http://pig.apache.org/">Porco Apache</a> é uma plataforma de alto nível que permite executar transformações MapReduce complexas em muito grandes conjuntos de dados usando uma linguagem de script simple chamada porco latino. Porco converte os scripts porco latino para que eles executará dentro Hadoop. Você pode criar funções definidas pelo usuário (UDFs) para estender porco latino. Consulte [usar porco com Hadoop](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> é ferramenta que transferências em massa dados entre Hadoop e bancos de dados relacionais como um SQL ou outros armazenamentos de dados estruturados, maneira mais eficiente possível. Consulte [usar Sqoop com Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> é uma estrutura de aplicativo criada no fio COLORIDO Hadoop que executa gráficos complexos e acíclica geral do processamento de dados. É uma sucessora mais flexível e eficiente para o framework MapReduce que permite que os processos de volumes de dados, como ramificação, para executar com mais eficiência em escala. Consulte ["Usar a Tez para melhorar o desempenho do Apache" na seção de uso e HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>FIO COLORIDO
Fio COLORIDO Apache é a próxima geração de MapReduce (MapReduce 2.0 ou MRv2) e oferece suporte a cenários de processamento de dados além do lote MapReduce processamento com maior escalabilidade e processamento em tempo real. Fio COLORIDO fornece gerenciamento de recursos e uma estrutura de aplicativo distribuído. MapReduce trabalhos executados em fio COLORIDO.

Para saber mais sobre fio COLORIDO, consulte <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Fio-Apache Hadoop NextGen MapReduce (COLORIDO)</a>.


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> coordenadas processos em grandes sistemas distribuídos por meio de um namespace compartilhado hierárquico de registradores de dados (znodes). Znodes conter pequenas quantidades de informações de meta necessárias para coordenar processos: status, local, configuração e assim por diante.

## <a name="programming-languages-on-hdinsight"></a>Idiomas de programação em HDInsight

HDInsight clusters - Hadoop, HBase, tempestade e Spark clusters – suporte um número de linguagens de programação, mas alguns não são instalados por padrão. Para bibliotecas, módulos ou pacotes não instalados por padrão, use uma ação de script para instalar o componente. Consulte [desenvolvimento de ação de Script com HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="default-programming-language-support"></a>Padrão de suporte de linguagem de programação

Por padrão, HDInsight clusters de suporte:

* Java

* Python

Idiomas adicionais podem ser instalados usando ações de script: [desenvolvimento de ação de Script com HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Idiomas de máquina virtual (JVM) Java

Muitos idiomas diferentes Java podem ser executados usando uma máquina virtual de Java (JVM); No entanto, executar alguns destes idiomas pode exigir componentes adicionais instalados no cluster.

Essas linguagens baseadas em JVM são suportadas em clusters de HDInsight:

* Clojure

* Jython (Python para Java)

* Scala

### <a name="hadoop-specific-languages"></a>Linguagens específicas do Hadoop

HDInsight clusters suportam os seguintes idiomas específicos ecossistema do Hadoop:

* Porco latino para trabalhos de porco

* HiveQL para trabalhos de seção e SparkSQL


## <a name="advantage"></a>Vantagens do Hadoop na nuvem

Como parte do ecossistema de nuvem Azure, Hadoop em HDInsight oferece vários benefícios, entre elas:

* Provisionamento automático do Hadoop clusters. HDInsight clusters são muito mais fácil criar que configurar manualmente Hadoop clusters. Para obter detalhes, consulte [clusters de provisionar Hadoop em HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

* Componentes de Hadoop de estado-de-arte. Para obter detalhes, consulte [componentes do Hadoop, controle de versão e ofertas de serviço em HDInsight][component-versioning].

* Alta disponibilidade e confiabilidade de clusters. Para obter detalhes, consulte [disponibilidade e confiabilidade de clusters Hadoop em HDInsight](hdinsight-high-availability-linux.md) .

* Armazenamento de dados eficaz e econômica com o armazenamento de Blob do Azure, uma opção compatível com o Hadoop. Consulte o [armazenamento de Blob do uso do Azure com Hadoop em HDInsight](hdinsight-hadoop-use-blob-storage.md) para obter detalhes.

* Integração com outros serviços do Azure, incluindo [Web apps](https://azure.microsoft.com/documentation/services/app-service/web/) e [Banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/).

* Tipos de para execução de HDInsight clusters e tamanhos adicionais de máquina virtual. Consulte [componentes do Hadoop, controle de versão e ofertas de serviço em HDInsight] [ component-versioning] para obter detalhes.

* Dimensionamento de cluster. Dimensionamento de cluster permite que você altere o número de nós de um cluster de HDInsight em execução sem precisar excluir ou recriá-la.

* Suporte a rede virtual. HDInsight clusters podem ser usados com rede Virtual do Azure para dar suporte a isolamento de recursos de nuvem ou cenários híbrido que vincular recursos de nuvem com as de seu data center.

* Entrada de baixo custo. Iniciar uma [avaliação gratuita](https://azure.microsoft.com/free/)ou consulte [detalhes de preços de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Para ler mais sobre as vantagens em Hadoop em HDInsight, consulte a [página recursos do Azure HDInsight][marketing-page].

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard e Premium de HDInsight

HDInsight fornece ofertas de nuvem de grande volume em duas categorias, Standard e Premium. HDInsight padrão fornece um cluster de grande porte que as organizações podem usar para executar as cargas de trabalho de grande volume. HDInsight Premium baseia-se em que e fornece avançadas analíticas e recursos de segurança para um cluster de HDInsight. Para obter mais informações, consulte [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)


## <a id="resources"></a>Recursos para aprender mais sobre a análise de dados grande, Hadoop e HDInsight

Desenvolva nesta introdução ao Hadoop na nuvem e análise de dados grande com os recursos a seguir.


### <a name="hadoop-documentation-for-hdinsight"></a>Documentação do Hadoop para HDInsight

* [Documentação do HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/): A página de documentação para Hadoop em Azurehdinsight com links para artigos, vídeos e mais recursos.

* [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): um tutorial de início rápido para provisionamento clusters HDInsight Hadoop e executar consultas de seção de amostra.

* [Introdução ao Spark em HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): um tutorial de início rápido para criar um cluster de Spark e executar consultas SQL Spark interativas.

* [Usar o servidor de R em HDInsight](hdinsight-hadoop-r-server-get-started.md): começar a usar o servidor de R no HDInsight Premium.

* [Provisionar HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md): Saiba como provisionar um cluster de HDInsight Hadoop pelo portal Azure, Azure CLI ou Azure PowerShell.


### <a name="apache-hadoop"></a>Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: Saiba mais sobre a biblioteca de software Apache Hadoop, uma estrutura que permite para o processamento distribuído de grandes conjuntos de dados em clusters de computadores.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>: Saiba mais sobre a arquitetura e o design do Hadoop distribuído com o sistema de arquivos, o sistema de armazenamento principal usado por aplicativos de Hadoop.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce Tutorial</a>: Saiba mais sobre a estrutura de programação para escrever aplicativos Hadoop que processam rapidamente grandes quantidades de dados em paralelo em grandes clusters de nós de computação.


### <a name="microsoft-business-intelligence"></a>O Microsoft business intelligence

Ferramentas de familiar de business intelligence (BI) - como Excel, PowerPivot, SQL Server Analysis Services e SQL Server Reporting Services - recuperar, analisar e relatar dados integrados ao HDInsight usando o suplemento do Power Query ou o Driver de ODBC do Microsoft seção.

Essas ferramentas de BI podem ajudar sua análise de dados de grande:

* [Conectar o Excel ao Hadoop com o Power Query](hdinsight-connect-excel-power-query.md): Saiba como conectar o Excel para a conta de armazenamento do Azure que armazena os dados associados ao seu cluster HDInsight, usando o Microsoft Power Query para Excel. Estação de trabalho Windows obrigatória. Funciona com cluster baseado em Windows ou Linux.

* [Conectar o Excel ao Hadoop com o Driver de ODBC do Microsoft seção](hdinsight-connect-excel-hive-odbc-driver.md): Saiba como importar dados de HDInsight com o Driver de ODBC do Microsoft seção. Estação de trabalho Windows obrigatória. Funciona com cluster baseado em Windows ou Linux.

* [Plataforma de nuvem da Microsoft](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): Saiba mais sobre o Power BI para Office 365, baixe a versão de avaliação do SQL Server e configurar o SharePoint Server 2013 e BI do SQL Server.

* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx).

* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx).




[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
