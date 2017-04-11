<properties
   pageTitle="Calcular opções de contexto do servidor de R em HDInsight (visualização) | Microsoft Azure"
   description="Saiba mais sobre as opções de contexto de computação diferentes disponíveis para usuários com R servidor na HDInsight (visualização)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="jhubbard"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="10/18/2016"
   ms.author="jeffstok"
/>

# <a name="compute-context-options-for-r-server-on-hdinsight-preview"></a>Calcular opções de contexto do servidor de R em HDInsight (prévia)

Servidor de R Microsoft no Azure HDInsight (visualização) fornece os recursos mais recentes para análise baseada em R. Ele usa dados armazenados em HDFS em um contêiner em sua conta de armazenamento do [Azure Blob](../storage/storage-introduction.md "armazenamento de Blob do Azure") ou o sistema de arquivos Linux local. Como R servidor é criado em Abrir origem R, aplicativos baseados em R que você cria podem aproveitar qualquer um dos pacotes Abrir origem R 8000 +. Eles também podem aproveitar as rotinas em [ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "ScaleR de análise de rotação"), pacote de análise de grande volume da Microsoft que acompanha o servidor de R.  

O nó de borda de um cluster de Premium fornece um local conveniente para se conectar ao cluster e executar os scripts de R. Com um nó de borda, você tem a opção de execução de funções de distribuído paralelizadas do ScaleR entre as cores do servidor de nó de borda. Você também tem a opção para executá-las em todos os nós do cluster por meio Hadoop mapa reduzir do ScaleR ou Spark calcular contextos.

## <a name="compute-contexts-for-an-edge-node"></a>Calcular contextos de um nó de borda

Em geral, um script de R que é executado no servidor de R o nó de borda é executado dentro o intérprete R nesse nó. A exceção é essas etapas que chamar uma função de ScaleR. As chamadas ScaleR executados em um ambiente de computação que é determinado por como você definir o contexto de computação ScaleR.  Quando você executa o script de R de um nó de borda, os valores possíveis do contexto de computação são local sequencial ('local'), paralelo local ('localpar'), reduzir o mapa e Spark.

As opções 'locais' e 'localpar' diferir somente em como chamadas de rxExec são executadas. Ambos executar outras chamadas de função rx de maneira paralela em cores disponíveis, a menos que especificado em contrário por meio do uso da opção numCoresToUse ScaleR, por exemplo, rxOptions(numCoresToUse=6). A seguir resume as diversas opções de contexto de computação

| Calcular o contexto  | Como definir                      | Contexto de execução                                                                     |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| Local sequencial | rxSetComputeContext('local')    | Execução paralelizada entre as cores do servidor de nó de borda, exceto para rxExec chama que são executados em série |
| Paralelo local   | rxSetComputeContext('localpar') | Execução paralelizada entre as cores do servidor de nó de borda                                 |
| Spark            | RxSpark()                       | Colocados em paralelo execução distribuída via Spark em nós do cluster HDI      |
| Reduzir o mapa       | RxHadoopMR()                    | Colocados em paralelo execução distribuída por meio de mapa reduzir em nós do cluster HDI |


Presumindo que você gostaria de execução paralelizada para fins de desempenho, há três opções. Opção que você escolher depende da natureza do seu trabalho de análise e o tamanho e o local dos seus dados.

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Diretrizes para decidir sobre um contexto de computação

Atualmente, não há nenhuma fórmula que informa que calcular contexto a ser usado. No entanto, há alguns princípios que podem ajudá-lo a fazer a escolha certa, ou pelo menos ajudarão restringir suas escolhas antes de executar uma avaliação de desempenho. Esses princípios incluem:

1.  O sistema de arquivos Linux local é mais rápido que HDFS.
2.  Análises repetidos são mais rápidas se os dados forem locais e se ela estiver na XDF.
3.  É preferível para transmitir pequenas quantidades de dados de uma fonte de dados de texto; Se a quantidade de dados for maior, convertê-lo XDF antes de análise.
4.  A sobrecarga de copiar ou streaming dos dados para o nó de borda para análise torna-se impossível para grandes quantidades de dados.
5.  Spark é mais rápido do mapa reduzir para análise no Hadoop.

Algumas regras gerais de bolso para selecionar um contexto de computação considerando esses princípios, são:

### <a name="local"></a>Local

- Se a quantidade de dados para analisar é pequena e não exige análise repetido, transfira diretamente para a rotina de análise e use 'local' ou 'localpar'.
- Se a quantidade de dados para analisar é para pequenas e médias e requer análise repetido, em seguida, copiá-lo para o sistema de arquivo local, importá-la para XDF e analisá-lo via 'local' ou 'localpar'.

### <a name="hadoop-spark"></a>Hadoop Spark

- Se a quantidade de dados para analisar for grande, importe-o para XDF no HDFS (a menos que armazenamento é um problema) e analisá-lo via 'Spark'.

### <a name="hadoop-map-reduce"></a>Mapa de Hadoop reduzir

- Use somente se você encontrar um problema insuperáveis com o uso do contexto de computação Spark desde que geralmente será mais lento.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Ajuda in-line em rxSetComputeContext

Para obter mais informações e exemplos de contextos de computação ScaleR, consulte o embutido Ajuda em R sobre o método de rxSetComputeContext, por exemplo:

    > ?rxSetComputeContext

Você também pode consultar o "ScaleR distribuído computação guia" que está disponível na biblioteca [MSDN de servidor de R](https://msdn.microsoft.com/library/mt674634.aspx "Servidor R no MSDN") .


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como criar um novo cluster HDInsight que inclui R Server. Você também aprendeu as Noções básicas de como usar o console de R a partir de uma sessão SSH. Agora você pode ler os artigos a seguir para descobrir outras maneiras de trabalhar com o servidor de R em HDInsight:

- [Visão geral do servidor de R para Hadoop](hdinsight-hadoop-r-server-overview.md)
- [Introdução ao servidor de R para Hadoop](hdinsight-hadoop-r-server-get-started.md)
- [Adicionar o servidor de RStudio HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [Azure opções de armazenamento do servidor de R em HDInsight Premium](hdinsight-hadoop-r-server-storage.md)
