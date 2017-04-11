<properties
   pageTitle="MapReduce com Hadoop em HDInsight | Microsoft Azure"
   description="Saiba como executar trabalhos de MapReduce em Hadoop em clusters de HDInsight. Você executará uma operação de contagem do word básicas implementada como um trabalho MapReduce Java."
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

# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>Usar MapReduce em Hadoop em HDInsight

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Neste artigo, você aprenderá como executar MapReduce trabalhos em Hadoop em clusters de HDInsight. Podemos executar uma operação de contagem do word básicas implementada como um trabalho Java MapReduce.

##<a id="whatis"></a>O que é MapReduce?

Hadoop MapReduce é uma estrutura de software para escrever trabalhos que processam grandes quantidades de dados. Dados de entrada são divididos em partes independentes, que, em seguida, são processados em paralelo em nós no seu cluster. Um trabalho MapReduce consistem em duas funções:

* **Mapeador**: consome dados de entrada, analisa-(normalmente com filtro e operações de classificação) e emite tuplas (pares de chave-valor)
* **Redutor**: consome tuplas emitidas pelo mapeador e executa uma operação de resumo que cria um resultado menor, combinado a partir dos dados de mapeador

Um exemplo de trabalho do word básicas contagem MapReduce é ilustrado no diagrama a seguir:

![HDI. WordCountDiagram][image-hdi-wordcountdiagram]

A saída deste trabalho é uma contagem de quantas vezes cada palavra ocorreu no texto que foi analisado.

* O mapeador leva cada linha do texto de entrada como uma entrada e divide em palavras. Emite um valor de chave/par cada vez que ocorre uma palavra da palavra é seguido por um 1. A saída é classificada antes de enviá-lo para Redutor.

* O Redutor soma essas contagens individuais para cada palavra e emite um par de chave/valor único que contém a palavra seguida pela soma das suas ocorrências.

MapReduce pode ser implementada em uma variedade de idiomas. Java é a implementação mais comuns e é usado para fins de demonstração neste documento.

### <a name="hadoop-streaming"></a>Hadoop Streaming

Idiomas ou estruturas que são baseadas em Java e máquina Virtual Java (por exemplo, Scalding ou em cascata,) pode ser executado diretamente como um trabalho MapReduce, semelhante a um aplicativo Java. Outras pessoas, como c# ou Python ou autônomo executáveis, devem usar Hadoop streaming.

Hadoop streaming se comunica com o mapeador e Redutor sobre STDIN e STDOUT - o mapeador Redutor ler dados de uma linha por vez de STDIN e escrever a saída para STDOUT. Cada linha ler ou emitido pelo mapeador e Redutor deve ser no formato de um par de chave/valor, delimitado por um charaacter de tabulação:

    [key]/t[value]

Para obter mais informações, consulte [Hadoop Streaming](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Para obter exemplos de como usar Hadoop streaming com HDInsight, consulte o seguinte:

* [Desenvolver Python MapReduce trabalhos](hdinsight-hadoop-streaming-python.md)

##<a id="data"></a>Sobre os dados de exemplo

Neste exemplo, para dados de exemplo, você usará os blocos de anotações de Leonardo Da Vinci, que são fornecidos como um documento de texto no seu cluster HDInsight.

Os dados de exemplo são armazenados no armazenamento de Blob do Azure, que HDInsight usa como o sistema de arquivo padrão para Hadoop clusters. HDInsight pode acessar arquivos armazenados em armazenamento de Blob usando o prefixo **wasb** . Por exemplo, para acessar o arquivo de sample.log, você usaria a seguinte sintaxe:

    wasbs:///example/data/gutenberg/davinci.txt

Como o armazenamento de Blob do Azure é o armazenamento de padrão para HDInsight, você também pode acessar o arquivo usando **/example/data/gutenberg/davinci.txt**.

> [AZURE.NOTE] Na sintaxe anterior, **wasbs: / / /** é usado para acessar os arquivos que estão armazenados no contêiner de armazenamento de padrão para o seu cluster HDInsight. Se você especificou contas de armazenamento adicional quando você provisionado seu cluster, e você deseja acessar arquivos armazenados nessas contas, você pode acessar os dados especificando o endereço de conta de armazenamento e o nome do contêiner. Por exemplo, **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/gutenberg/davinci.txt**.

##<a id="job"></a>Sobre o exemplo MapReduce

O trabalho de MapReduce que é usado neste exemplo está localizado em **wasbs://example/jars/hadoop-mapreduce-examples.jar**e ela é fornecida com o seu cluster HDInsight. Esta página contém um exemplo de contagem do word que você irá executar contra **davinci.txt**.

> [AZURE.NOTE] Em clusters de HDInsight 2.1, o local do arquivo é **wasbs:///example/jars/hadoop-examples.jar**.

Para referência, este é o código de Java para o trabalho de MapReduce de contagem do word:

    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;

    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

      public static class TokenizerMapper
           extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
          StringTokenizer itr = new StringTokenizer(value.toString());
          while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
          }
        }
      }

      public static class IntSumReducer
           extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                           Context context
                           ) throws IOException, InterruptedException {
          int sum = 0;
          for (IntWritable val : values) {
            sum += val.get();
          }
          result.set(sum);
          context.write(key, result);
        }
      }

      public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
          System.err.println("Usage: wordcount <in> <out>");
          System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
      }
    }

Para obter instruções escrever seu próprio trabalho MapReduce, consulte [MapReduce de Java desenvolver programas para HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md).

##<a id="run"></a>Executar o MapReduce

HDInsight pode executar HiveQL trabalhos usando uma variedade de métodos. Use a tabela a seguir para decidir qual método é adequado para você, em seguida, siga o link para um passo a passo.

| **Use isto**...                                                    | **… até fazer isso**                                       | … .with esse **sistema operacional de cluster** | …. E1. esse **sistema operacional cliente** |
|:-------------------------------------------------------------------|:--------------------------------------------------------|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md)                       | Use o comando Hadoop por meio do **SSH**                  | Linux                                     | Linux, Unix, Windows ou Mac OS X        |
| [Ondulação](hdinsight-hadoop-use-mapreduce-curl.md)                     | Envie o trabalho remotamente usando o **resto**               | Linux ou Windows                          | Linux, Unix, Windows ou Mac OS X        |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) | Envie o trabalho remotamente usando o **Windows PowerShell** | Linux ou Windows                          | Windows                                  |
| [Área de trabalho remota](hdinsight-hadoop-use-mapreduce-remote-desktop)    | Use o comando Hadoop por meio de **Área de trabalho remota**       | Windows                                   | Windows                                  |

##<a id="nextsteps"></a>Próximas etapas

Embora MapReduce fornece poderosos recursos de diagnósticos, ele pode ser um pouco desafiador mestre. Há várias estruturas baseado em Java que tornam mais fácil definir MapReduce aplicativos, bem como tecnologias como porco e ramificação, que fornecem uma maneira fácil de trabalhar com dados em HDInsight. Para saber mais, consulte os seguintes artigos:

* [Desenvolver programas Java MapReduce para HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)

* [Desenvolver Python streaming MapReduce programas para HDInsight](hdinsight-hadoop-streaming-python.md)

* [Desenvolver MapReduce Scalding trabalhos com Apache Hadoop em HDInsight](hdinsight-hadoop-mapreduce-scalding.md)

* [Use a seção com HDInsight][hdinsight-use-hive]

* [Usar porco com HDInsight][hdinsight-use-pig]

* [Executar os exemplos de HDInsight][hdinsight-samples]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[powershell-install-configure]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
