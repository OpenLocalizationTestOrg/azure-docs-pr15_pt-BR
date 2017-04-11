<properties
    pageTitle="Executar as amostras de Hadoop em HDInsight | Microsoft Azure"
    description="Começar a usar o serviço de Azurehdinsight com as amostras fornecidas. Use scripts do PowerShell que são executados MapReduce em clusters de dados."
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
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="jgao"/>

#<a name="run-hadoop-mapreduce-samples-in-windows-based-hdinsight"></a>Executar Hadoop MapReduce amostras em HDInsight baseado no Windows

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Um conjunto de amostras são fornecidas para ajudá-lo a obter trabalhos MapReduce em execução iniciados em clusters Hadoop usando Azurehdinsight. Esses exemplos são disponibilizados em cada um dos clusters HDInsight gerenciado que você criar. Executando estes exemplos vai familiarizar você com o uso de cmdlets do PowerShell do Azure para executar trabalhos em clusters de Hadoop.

- [**Contagem do Word**][hdinsight-sample-wordcount]: conta as ocorrências de palavras em um arquivo de texto.
- [**C# streaming contagem de palavras**][hdinsight-sample-csharp-streaming]: conta as ocorrências de palavras em um arquivo de texto usando a interface de streaming de Hadoop.
- [**Avaliador de pi**][hdinsight-sample-pi-estimator]: usa uma estatística (quase-Monte Carlo) método para estimar o valor de pi.
- [**10 GB Graysort**][hdinsight-sample-10gb-graysort]: executar um objetivo geral GraySort em um arquivo de 10GB usando HDInsight. Há três trabalhos para executar: Teragen para gerar os dados, Terasort para classificar os dados e Teravalidate para confirmar que os dados foram classificados corretamente.

>[AZURE.NOTE] O código-fonte pode ser encontrado no apêndice. 

Documentação muito adicional existe na web para tecnologias relacionadas Hadoop, como programação baseada em Java MapReduce streaming e documentação sobre os cmdlets usados no Windows PowerShell script. Para obter mais informações sobre esses recursos, consulte:

- [Desenvolver programas Java MapReduce para Hadoop em HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)
- [Enviar trabalhos de Hadoop em HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Introdução ao Azure HDInsight][hdinsight-introduction]

Atualmente, muitas pessoas Escolha seção e porco MapReduce.  Para obter mais informações, consulte:

- [Use a seção no HDInsight](hdinsight-use-hive.md)
- [Usar porco em HDInsight](hdinsight-use-pig.md)
 
**Pré-requisitos**:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **um cluster de HDInsight**. Para obter instruções sobre as diversas maneiras nas quais esses clusters podem ser criados, consulte [criar Hadoop clusters no HDInsight](hdinsight-provision-clusters.md).
- **Uma estação de trabalho com o PowerShell do Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a name="hdinsight-sample-wordcount"></a>Contagem - Java do Word 

Para enviar um projeto MapReduce, você primeiro criar uma definição de trabalho MapReduce. Na definição de trabalho, você pode especificar o arquivo de jar de programa MapReduce e o local do arquivo jar, que é **wasbs:///example/jars/hadoop-mapreduce-examples.jar**, o nome da classe e os argumentos.  O programa de MapReduce wordcount leva dois argumentos: o arquivo de origem que será usado para contar palavras e o local de saída.

O código-fonte pode ser encontrado no [Apêndice A](#apendix-a---the-word-count-MapReduce-program-in-java).

Para obter o procedimento de desenvolvimento de um programa Java MapReduce, veja - [desenvolver Java MapReduce programas para Hadoop em HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)
 
**Para enviar um trabalho de MapReduce de contagem do word**

1. Abra **O Windows PowerShell ISE**. Para obter instruções, consulte [instalar e configurar o Azure PowerShell][powershell-install-configure].
2. Cole o seguinte script do PowerShell:

        $subscriptionName = "<Azure Subscription Name>"
        $resourceGroupName = "<Resource Group Name>"
        $clusterName = "<HDInsight cluster name>"             # HDInsight cluster name
        
        Select-AzureRmSubscription -SubscriptionName $subscriptionName
        
        # Define the MapReduce job
        $mrJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                    -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
                                    -ClassName "wordcount" `
                                    -Arguments "wasbs:///example/data/gutenberg/davinci.txt", "wasbs:///example/data/WordCountOutput1"
        
        # Submit the job and wait for job completion
        $cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
        $mrJob = Start-AzureRmHDInsightJob `
                            -ResourceGroupName $resourceGroupName `
                            -ClusterName $clusterName `
                            -HttpCredential $cred `
                            -JobDefinition $mrJobDefinition 
        
        Wait-AzureRmHDInsightJob `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $clusterName `
            -HttpCredential $cred `
            -JobId $mrJob.JobId 
        
        # Get the job output
        $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
        $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
        $defaultStorageContainer = $cluster.DefaultStorageContainer
        
        Get-AzureRmHDInsightJobOutput `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $clusterName `
            -HttpCredential $cred `
            -DefaultStorageAccountName $defaultStorageAccount `
            -DefaultStorageAccountKey $defaultStorageAccountKey `
            -DefaultContainer $defaultStorageContainer  `
            -JobId $mrJob.JobId `
            -DisplayOutputType StandardError

        # Download the job output to the workstation
        $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
        Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
        
        # Display the output file
        cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    O trabalho MapReduce produz um arquivo denominado *parte-r-00000*, que contém palavras e as contagens. O script usa o comando **findstr** para listar todas as palavras que contenha *"lá"*.

3. Defina as 3 primeiras variáveis e execute o script.

## <a name="hdinsight-sample-csharp-streaming"></a>Contagem - c# streaming do Word

Hadoop fornece uma API streaming ao MapReduce, que permite que você escrever mapa e reduzir funções em idiomas diferentes Java.

> [AZURE.NOTE] As etapas neste tutorial se aplicam somente a clusters HDInsight baseados no Windows. Para obter um exemplo de fluxo para clusters baseados em Linux HDInsight, consulte [Python desenvolver programas streaming para HDInsight](hdinsight-hadoop-streaming-python.md).

No exemplo, o mapeador e o Redutor são executáveis que leia a entrada de [stdin] [ stdin-stdout-stderr] (linha por linha) e emita a saída para [stdout][stdin-stdout-stderr]. O programa calcula todas as palavras no texto.

Quando um arquivo executável é especificado para **mapeadores**, cada tarefa mapeador inicia o executável como um processo separado quando o mapeador for inicializado. Como a tarefa mapeador executa, ele converte sua entrada em linhas e feeds as linhas para o [stdin] [ stdin-stdout-stderr] do processo.

Enquanto isso, o mapeador coleta a saída orientados a linha de stdout do processo. Ele converterá cada linha em um par de chave/valor, que é coletado como a saída do mapeador. Por padrão, o prefixo de uma linha para cima até o primeiro caractere de tabulação é a chave e o resto da linha (excluindo o caractere de tabulação) é o valor. Se não houver nenhum caractere de tabulação na linha, linha inteira é considerada como a chave e o valor é nulo.

Quando um arquivo executável é especificado para **reducers**, cada tarefa Redutor inicia o executável como um processo separado quando o Redutor for inicializado. Enquanto a tarefa Redutor é executada, converte seus pares de chave/valores de entrada em linhas e ele feeds as linhas para o [stdin] [ stdin-stdout-stderr] do processo.

Enquanto isso, o Redutor coletará a saída orientados a linha do [stdout] [ stdin-stdout-stderr] do processo. Converte cada linha um par de chave/valor, que é coletado como a saída do Redutor. Por padrão, o prefixo de uma linha para cima até o primeiro caractere de tabulação é a chave e o resto da linha (excluindo o caractere de tabulação) é o valor.

Para obter mais informações sobre a interface do Hadoop Streaming, consulte [Hadoop Streaming] [streaming hadoop].

**Para enviar um fluxo de trabalho de contagem do word c#**

- Siga o procedimento em [contagem de palavras - Java](#word-count-java)e substitua a definição de trabalho com o seguinte:

        $mrJobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
                                -Files "/example/apps/cat.exe","/example/apps/wc.exe" `
                                -Mapper "cat.exe" `
                                -Reducer "wc.exe" `
                                -InputPath "/example/data/gutenberg/davinci.txt" `
                                -OutputPath "/example/data/StreamingOutput/wc.txt"  


    O arquivo de saída deverá ser:
    
        example/data/StreamingOutput/wc.txt/part-00000      
                                
## <a name="hdinsight-sample-pi-estimator"></a>Avaliador de PI

O estimador de pi usa uma estatística (quase-Monte Carlo) método para estimar o valor de pi. Pontos colocados aleatoriamente dentro de uma unidade quadrada também estão dentro de um círculo inscritas dentro esse quadrado com uma probabilidade igual para a área do círculo, pi/4. O valor de pi pode ser estimado do valor do 4R, onde R é a razão entre o número de pontos que estão dentro do círculo para o número total de pontos que estão dentro do quadrado. Quanto maior a amostra de pontos usados, melhor a estimativa é.

O script fornecido para esse exemplo envia um trabalho de jar Hadoop e estiver configurado para executar com um valor 16 mapas, cada um dos quais é necessária para calcular pontos de amostra de 10 milhões por valores de parâmetro. Esses valores de parâmetro podem ser alteradas para melhorar o valor estimado de pi. Para referência, as 10 primeiros casas decimais de pi são 3.1415926535.

**Para enviar um trabalho de avaliador de pi**

- Siga o procedimento em [contagem de palavras - Java](#word-count-java)e substitua a definição de trabalho com o seguinte:

        $mrJobJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                    -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
                                    -ClassName "pi" `
                                    -Arguments "16", "10000000"

## <a name="hdinsight-sample-10gb-graysort"></a>10 GB Graysort

Este exemplo utiliza um pouco mais 10GB de dados para que ele possa ser executado relativamente rapidamente. Ele usa os aplicativos de MapReduce desenvolvidos pela Owen O'Malley e Arun Murthy que conquistaram o parâmetro de comparação de classificação de terabytes geral ("daytona") anual em 2009 com uma taxa de 0.578 TB/min (100 TB em 173 minutos). Para obter mais informações sobre esta e outras avaliações de classificação, consulte o site [Sortbenchmark](http://sortbenchmark.org/) .

Este exemplo usa três conjuntos de MapReduce programas:

1. **TeraGen** é um programa de MapReduce que você pode usar para gerar as linhas de dados para classificar.
2. **TeraSort** amostras de dados de entrada e usa MapReduce para classificar os dados em uma ordem total. TeraSort é uma classificação padrão das funções de MapReduce, exceto para um particionador personalizado que usa uma lista classificada de chaves de N-1 de amostra que definem o intervalo de chave para cada reduzir. Em particular, todas as chaves tais que amostra [i-1] < = chave < amostra [i] são enviadas para reduzir i. Isso garante que as saídas do reduzem i é todos menor do que a saída de reduzir i + 1.
3. **TeraValidate** é um programa de MapReduce valida que a saída globalmente está classificada. Ele cria um mapa por arquivo no diretório de saída, e cada mapa garante que cada chave é menor ou igual ao anterior. A função map também gera registros das chaves e sobrenomes de cada arquivo e a função de reduzir garante que a primeira chave de arquivo i é maior do que a última chave i-1 do arquivo. Todos os problemas são relatados como uma saída da reduzir com as teclas que estão fora de ordem.

O formato de entrada e saído, usado por todos os três aplicativos, lê e grava os arquivos de texto no formato correto. A saída da reduzir tem replicação definida como 1, em vez do padrão 3, porque o concurso de referência não exige que os dados de saída ser replicados para vários nós.

Três tarefas são necessárias por exemplo, cada correspondente a um dos programas MapReduce descritos na introdução:

1. Gere os dados de classificação executando o trabalho de MapReduce **TeraGen** .
2. Classificar os dados executando o trabalho de MapReduce **TeraSort** .
3. Confirme que os dados foram classificados corretamente executando o trabalho **TeraValidate** MapReduce.

**Para enviar os trabalhos**

- Siga o procedimento em [contagem de palavras - Java](#word-count-java)e use as seguintes definições de trabalho:

    $teragen = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` 
   - nome da classe "teragen" '-argumentos "-Dmapred.map.tasks=50", "100000000", "/ exemplo / / 10 GB-classificar-a entrada de dados"
    
    $terasort = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` 
   - nome da classe "terasort" '-argumentos "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/ exemplo / / 10 GB-classificar-a entrada de dados", "/ exemplo/dados/10 GB-classificar-saída"
    
    $teravalidate = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` 
   - nome da classe "teravalidate" '-argumentos "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/ exemplo/dados/10 GB-classificar-saída", "/ exemplo/dados/10 GB-classificar-validar"


##<a name="next-steps"></a>Próximas etapas 

Este artigo e os artigos em cada um dos exemplos, você aprendeu como executar os exemplos incluídos clusters HDInsight usando o PowerShell do Azure. Para tutoriais sobre como usar porco, seção e MapReduce com HDInsight, consulte os tópicos a seguir:

* [Começar a usar Hadoop com seção no HDInsight para analisar o uso do celular][hdinsight-get-started]
* [Usar porco com Hadoop em HDInsight][hdinsight-use-pig]
* [Use a seção com Hadoop em HDInsight][hdinsight-use-hive]
* [Enviar trabalhos de Hadoop em HDInsight] [hdinsight-submit-jobs]
* [Documentação do Azure HDInsight SDK][hdinsight-sdk-documentation]
* [Depurar Hadoop no HDInsight: mensagens de erro] [hdinsight-errors]


## <a name="appendix-a---the-word-count-source-code"></a>Apêndice A - o código de fonte de contagem do Word

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


## <a name="appendix-b---the-word-count-streaming-source-code"></a>Apêndice B - a contagem de palavras streaming código-fonte

O programa de MapReduce usa o aplicativo de cat.exe como uma interface de mapeamento para transmitir o texto para o console e o aplicativo de wc.exe como a interface de reduzir para contar o número de palavras que são transmitidos de um documento. Mapeador tanto o Redutor ler caracteres, linha por linha, do fluxo de entrada padrão (stdin) e gravem o fluxo de saída padrão (stdout).

    // The source code for the cat.exe (Mapper).

    using System;
    using System.IO;

    namespace cat
    {
        class cat
        {
            static void Main(string[] args)
            {
                if (args.Length > 0)
                {
                    Console.SetIn(new StreamReader(args[0]));
                }

                string line;
                while ((line = Console.ReadLine()) != null)
                {
                    Console.WriteLine(line);
                }
            }
        }
    }



O código de mapeador no arquivo cat.cs usa um [StreamReader] [ streamreader] objeto para ler caracteres do fluxo de entrada para o console, que grava o fluxo para o fluxo de saída padrão com o estático [console. WriteLine] [ console-writeline] método.


    // The source code for wc.exe (Reducer) is:

    using System;
    using System.IO;
    using System.Linq;

    namespace wc
    {
        class wc
        {
            static void Main(string[] args)
            {
                string line;
                var count = 0;

                if (args.Length > 0){
                    Console.SetIn(new StreamReader(args[0]));
                }

                while ((line = Console.ReadLine()) != null) {
                    count += line.Count(cr => (cr == ' ' || cr == '\n'));
                }
                Console.WriteLine(count);
            }
        }
    }


O código de Redutor no arquivo wc.cs usa um [StreamReader] [ streamreader] objeto para ler caracteres do fluxo de entrada padrão que foram saída pelo mapeador cat.exe. Como ele lê os caracteres com o [console. WriteLine] [ console-writeline] método, ele contará as palavras contando espaços e caracteres de fim de linha no final de cada palavra. Ele grava o total para o fluxo de saída padrão com o [console. WriteLine] [ console-writeline] método.





## <a name="appendix-c---the-pi-estimator-source-code"></a>Apêndice C - o código-fonte estimador Pi

O estimador de pi código Java que contém as funções mapeador e Redutor está disponível para inspeção abaixo. O programa de mapeador gera um número especificado de pontos colocado aleatoriamente dentro de um quadrado de unidade e, em seguida, conta o número desses pontos que estão dentro do círculo. O programa Redutor acumula pontos contados pelas mapeadores estimativas de e, em seguida, o valor de pi do 4R fórmula, onde R é a razão entre o número de pontos contados dentro do círculo para o número total de pontos que estão dentro do quadrado.

    /**
    * Licensed to the Apache Software Foundation (ASF) under one
    * or more contributor license agreements. See the NOTICE file
    * distributed with this work for additional information
    * regarding copyright ownership. The ASF licenses this file
    * to you under the Apache License, Version 2.0 (the
    * "License"); you may not use this file except in compliance
    * with the License. You may obtain a copy of the License at
    *
    * http://www.apache.org/licenses/LICENSE-2.0
    *
    * Unless required by applicable law or agreed to in writing, software
    * distributed under the License is distributed on an "AS IS" BASIS,
    * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or   implied.
    * See the License for the specific language governing permissions and
    * limitations under the License.
    */

    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.math.BigDecimal;
    import java.util.Iterator;

    import org.apache.hadoop.conf.Configured;
    import org.apache.hadoop.fs.FileSystem;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.BooleanWritable;
    import org.apache.hadoop.io.LongWritable;
    import org.apache.hadoop.io.SequenceFile;
    import org.apache.hadoop.io.Writable;
    import org.apache.hadoop.io.WritableComparable;
    import org.apache.hadoop.io.SequenceFile.CompressionType;
    import org.apache.hadoop.mapred.FileInputFormat;
    import org.apache.hadoop.mapred.FileOutputFormat;
    import org.apache.hadoop.mapred.JobClient;
    import org.apache.hadoop.mapred.JobConf;
    import org.apache.hadoop.mapred.MapReduceBase;
    import org.apache.hadoop.mapred.Mapper;
    import org.apache.hadoop.mapred.OutputCollector;
    import org.apache.hadoop.mapred.Reducer;
    import org.apache.hadoop.mapred.Reporter;
    import org.apache.hadoop.mapred.SequenceFileInputFormat;
    import org.apache.hadoop.mapred.SequenceFileOutputFormat;
    import org.apache.hadoop.util.Tool;
    import org.apache.hadoop.util.ToolRunner;


    //A Map-reduce program to estimate the value of Pi
    //using quasi-Monte Carlo method.
    //
    //Mapper:
    //Generate points in a unit square
    //and then count points inside/outside of the inscribed circle of the square.
    //
    //Reducer:
    //Accumulate points inside/outside results from the mappers.
    //Let numTotal = numInside + numOutside.
    //The fraction numInside/numTotal is a rational approximation of
    //the value (Area of the circle)/(Area of the square),
    //where the area of the inscribed circle is Pi/4
    //and the area of unit square is 1.
    //Then, Pi is estimated value to be 4(numInside/numTotal).
    //

    public class PiEstimator extends Configured implements Tool {
    //tmp directory for input/output
    static private final Path TMP_DIR = new Path(
    PiEstimator.class.getSimpleName() + "_TMP_3_141592654");

    //2-dimensional Halton sequence {H(i)},
    //where H(i) is a 2-dimensional point and i >= 1 is the index.
    //Halton sequence is used to generate sample points for Pi estimation.
    private static class HaltonSequence {
    // Bases
    static final int[] P = {2, 3};
    //Maximum number of digits allowed
    static final int[] K = {63, 40};

    private long index;
    private double[] x;
    private double[][] q;
    private int[][] d;

    //Initialize to H(startindex),
    //so the sequence begins with H(startindex+1).
    HaltonSequence(long startindex) {
    index = startindex;
    x = new double[K.length];
    q = new double[K.length][];
    d = new int[K.length][];
    for(int i = 0; i < K.length; i++) {
    q[i] = new double[K[i]];
    d[i] = new int[K[i]];
    }

    for(int i = 0; i < K.length; i++) {
    long k = index;
    x[i] = 0;

    for(int j = 0; j < K[i]; j++) {
    q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];
    d[i][j] = (int)(k % P[i]);
    k = (k - d[i][j])/P[i];
    x[i] += d[i][j] * q[i][j];
    }
    }
    }

    //Compute next point.
    //Assume the current point is H(index).
    //Compute H(index+1).
    //@return a 2-dimensional point with coordinates in [0,1)^2
    double[] nextPoint() {
    index++;
    for(int i = 0; i < K.length; i++) {
    for(int j = 0; j < K[i]; j++) {
    d[i][j]++;
    x[i] += q[i][j];
    if (d[i][j] < P[i]) {
    break;
    }
    d[i][j] = 0;
    x[i] -= (j == 0? 1.0: q[i][j-1]);
    }
    }
    return x;
    }
    }

    //Mapper class for Pi estimation.
    //Generate points in a unit square and then
    //count points inside/outside of the inscribed circle of the square.
    public static class PiMapper extends MapReduceBase
    implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

    //Map method.
    //@param offset samples starting from the (offset+1)th sample.
    //@param size the number of samples for this map
    //@param out output {ture->numInside, false->numOutside}
    //@param reporter
    public void map(LongWritable offset,
    LongWritable size,
    OutputCollector<BooleanWritable, LongWritable> out,
    Reporter reporter) throws IOException {

    final HaltonSequence haltonsequence = new HaltonSequence(offset.get());
    long numInside = 0L;
    long numOutside = 0L;

    for(long i = 0; i < size.get(); ) {
    //generate points in a unit square
    final double[] point = haltonsequence.nextPoint();

    //count points inside/outside of the inscribed circle of the square
    final double x = point[0] - 0.5;
    final double y = point[1] - 0.5;
    if (x*x + y*y > 0.25) {
    numOutside++;
    } else {
    numInside++;
    }

    //report status
    i++;
    if (i % 1000 == 0) {
    reporter.setStatus("Generated " + i + " samples.");
    }
    }

    //output map results
    out.collect(new BooleanWritable(true), new LongWritable(numInside));
    out.collect(new BooleanWritable(false), new LongWritable(numOutside));
    }
    }


    //Reducer class for Pi estimation.
    //Accumulate points inside/outside results from the mappers.
    public static class PiReducer extends MapReduceBase
    implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

    private long numInside = 0;
    private long numOutside = 0;
    private JobConf conf; //configuration for accessing the file system

    //Store job configuration.
    @Override
    public void configure(JobConf job) {
    conf = job;
    }


    // Accumulate number of points inside/outside results from the mappers.
    // @param isInside Is the points inside?
    // @param values An iterator to a list of point counts
    // @param output dummy, not used here.
    // @param reporter

    public void reduce(BooleanWritable isInside,
    Iterator<LongWritable> values,
    OutputCollector<WritableComparable<?>, Writable> output,
    Reporter reporter) throws IOException {
    if (isInside.get()) {
    for(; values.hasNext(); numInside += values.next().get());
    } else {
    for(; values.hasNext(); numOutside += values.next().get());
    }
    }

    //Reduce task done, write output to a file.
    @Override
    public void close() throws IOException {
    //write output to a file
    Path outDir = new Path(TMP_DIR, "out");
    Path outFile = new Path(outDir, "reduce-out");
    FileSystem fileSys = FileSystem.get(conf);
    SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
    outFile, LongWritable.class, LongWritable.class,
    CompressionType.NONE);
    writer.append(new LongWritable(numInside), new LongWritable(numOutside));
    writer.close();
    }
    }

    //Run a map/reduce job for estimating Pi.
    //@return the estimated value of Pi.
    public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
    )
    throws IOException {
    //setup job conf
    jobConf.setJobName(PiEstimator.class.getSimpleName());

    jobConf.setInputFormat(SequenceFileInputFormat.class);

    jobConf.setOutputKeyClass(BooleanWritable.class);
    jobConf.setOutputValueClass(LongWritable.class);
    jobConf.setOutputFormat(SequenceFileOutputFormat.class);

    jobConf.setMapperClass(PiMapper.class);
    jobConf.setNumMapTasks(numMaps);

    jobConf.setReducerClass(PiReducer.class);
    jobConf.setNumReduceTasks(1);

    // turn off speculative execution, because DFS doesn't handle
    // multiple writers to the same file.
    jobConf.setSpeculativeExecution(false);

    //setup input/output directories
    final Path inDir = new Path(TMP_DIR, "in");
    final Path outDir = new Path(TMP_DIR, "out");
    FileInputFormat.setInputPaths(jobConf, inDir);
    FileOutputFormat.setOutputPath(jobConf, outDir);

    final FileSystem fs = FileSystem.get(jobConf);
    if (fs.exists(TMP_DIR)) {
     throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
     + " already exists. Please remove it first.");
     }
     if (!fs.mkdirs(inDir)) {
     throw new IOException("Cannot create input directory " + inDir);
     }

     //generate an input file for each map task
     try {
     for(int i=0; i < numMaps; ++i) {
     final Path file = new Path(inDir, "part"+i);
     final LongWritable offset = new LongWritable(i * numPoints);
     final LongWritable size = new LongWritable(numPoints);
     final SequenceFile.Writer writer = SequenceFile.createWriter(
     fs, jobConf, file,
     LongWritable.class, LongWritable.class, CompressionType.NONE);
     try {
     writer.append(offset, size);
     } finally {
     writer.close();
     }
     System.out.println("Wrote input for Map #"+i);
     }

     //start a map/reduce job
     System.out.println("Starting Job");
     final long startTime = System.currentTimeMillis();
     JobClient.runJob(jobConf);
     final double duration = (System.currentTimeMillis() - startTime)/1000.0;
     System.out.println("Job Finished in " + duration + " seconds");

     //read outputs
     Path inFile = new Path(outDir, "reduce-out");
     LongWritable numInside = new LongWritable();
     LongWritable numOutside = new LongWritable();
     SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
     try {
     reader.next(numInside, numOutside);
     } finally {
     reader.close();
     }

     //compute estimated value
     return BigDecimal.valueOf(4).setScale(20)
     .multiply(BigDecimal.valueOf(numInside.get()))
     .divide(BigDecimal.valueOf(numMaps))
     .divide(BigDecimal.valueOf(numPoints));
     } finally {
     fs.delete(TMP_DIR, true);
     }
     }

    //Parse arguments and then runs a map/reduce job.
    //Print output in standard out.
    //@return a non-zero if there is an error. Otherwise, return 0.
     public int run(String[] args) throws Exception {
     if (args.length != 2) {
     System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
     ToolRunner.printGenericCommandUsage(System.err);
     return -1;
     }

     final int nMaps = Integer.parseInt(args[0]);
     final long nSamples = Long.parseLong(args[1]);

     System.out.println("Number of Maps = " + nMaps);
     System.out.println("Samples per Map = " + nSamples);

     final JobConf jobConf = new JobConf(getConf(), getClass());
     System.out.println("Estimated value of Pi is "
     + estimate(nMaps, nSamples, jobConf));
     return 0;
     }

     //main method for running it as a stand alone command.
     public static void main(String[] argv) throws Exception {
     System.exit(ToolRunner.run(null, new PiEstimator(), argv));
     }
     }
     
## <a name="appendix-d---the-10gb-graysort-source-code"></a>Apêndice D - o código-fonte graysort 10gb

O código para o programa TeraSort MapReduce é apresentado para inspeção nesta seção.


    /**
     * Licensed to the Apache Software Foundation (ASF) under one
     * or more contributor license agreements.  See the NOTICE file
     * distributed with this work for additional information
     * regarding copyright ownership.  The ASF licenses this file
     * to you under the Apache License, Version 2.0 (the
     * "License"); you may not use this file except in compliance
     * with the License.  You may obtain a copy of the License at
     *
     *     http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

    package org.apache.hadoop.examples.terasort;

    import java.io.IOException;
    import java.io.PrintStream;
    import java.net.URI;
    import java.util.ArrayList;
    import java.util.List;

    import org.apache.commons.logging.Log;
    import org.apache.commons.logging.LogFactory;
    import org.apache.hadoop.conf.Configured;
    import org.apache.hadoop.filecache.DistributedCache;
    import org.apache.hadoop.fs.FileSystem;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.NullWritable;
    import org.apache.hadoop.io.SequenceFile;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapred.FileOutputFormat;
    import org.apache.hadoop.mapred.JobClient;
    import org.apache.hadoop.mapred.JobConf;
    import org.apache.hadoop.mapred.Partitioner;
    import org.apache.hadoop.util.Tool;
    import org.apache.hadoop.util.ToolRunner;

    /**
     * Generates the sampled split points, launches the job,
     * and waits for it to finish.
     * <p>
     * To run the program:
     * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
     */

    public class TeraSort extends Configured implements Tool {
      private static final Log LOG = LogFactory.getLog(TeraSort.class);

      /**
       * A partitioner that splits text keys into roughly equal
       * partitions in a global sorted order.
       */

      static class TotalOrderPartitioner implements Partitioner<Text,Text>{
        private TrieNode trie;
        private Text[] splitPoints;

        /**
         * A generic trie node
         */
        static abstract class TrieNode {
          private int level;
          TrieNode(int level) {
            this.level = level;
          }
          abstract int findPartition(Text key);
          abstract void print(PrintStream strm) throws IOException;
          int getLevel() {
            return level;
          }
        }

        /**
         * An inner trie node that contains 256 children based on the next
         * character.
         */
        static class InnerTrieNode extends TrieNode {
          private TrieNode[] child = new TrieNode[256];

          InnerTrieNode(int level) {
            super(level);
          }
          int findPartition(Text key) {
            int level = getLevel();
            if (key.getLength() <= level) {
              return child[0].findPartition(key);
            }
            return child[key.getBytes()[level]].findPartition(key);
          }
          void setChild(int idx, TrieNode child) {
            this.child[idx] = child;
          }
          void print(PrintStream strm) throws IOException {
            for(int ch=0; ch < 255; ++ch) {
              for(int i = 0; i < 2*getLevel(); ++i) {
                strm.print(' ');
              }
              strm.print(ch);
              strm.println(" ->");
              if (child[ch] != null) {
                child[ch].print(strm);
              }
            }
          }
        }

        /**
         * A leaf trie node that does string compares to figure out where the given
         * key belongs between lower..upper.
         */
        static class LeafTrieNode extends TrieNode {
          int lower;
          int upper;
          Text[] splitPoints;
          LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
            super(level);
            this.splitPoints = splitPoints;
            this.lower = lower;
            this.upper = upper;
          }
          int findPartition(Text key) {
            for(int i=lower; i<upper; ++i) {
              if (splitPoints[i].compareTo(key) >= 0) {
                return i;
              }
            }
            return upper;
          }
          void print(PrintStream strm) throws IOException {
            for(int i = 0; i < 2*getLevel(); ++i) {
              strm.print(' ');
            }
            strm.print(lower);
            strm.print(", ");
            strm.println(upper);
          }
        }


        /**
         * Read the cut points from the given sequence file.
         * @param fs the file system
         * @param p the path to read
         * @param job the job config
         * @return the strings to split the partitions on
         * @throws IOException
         */
        private static Text[] readPartitions(FileSystem fs, Path p,
                                             JobConf job) throws IOException {
          SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
          List<Text> parts = new ArrayList<Text>();
          Text key = new Text();
          NullWritable value = NullWritable.get();
          while (reader.next(key, value)) {
            parts.add(key);
            key = new Text();
          }
          reader.close();
          return parts.toArray(new Text[parts.size()]);  
        }

        /**
         * Given a sorted set of cut points, build a trie that will find the correct
         * partition quickly.
         * @param splits the list of cut points
         * @param lower the lower bound of partitions 0..numPartitions-1
         * @param upper the upper bound of partitions 0..numPartitions-1
         * @param prefix the prefix that we have already checked against
         * @param maxDepth the maximum depth we will build a trie for
         * @return the trie node that will divide the splits correctly
         */
        private static TrieNode buildTrie(Text[] splits, int lower, int upper,
                                          Text prefix, int maxDepth) {
          int depth = prefix.getLength();
          if (depth >= maxDepth || lower == upper) {
            return new LeafTrieNode(depth, splits, lower, upper);
          }
          InnerTrieNode result = new InnerTrieNode(depth);
          Text trial = new Text(prefix);
          // append an extra byte on to the prefix
          trial.append(new byte[1], 0, 1);
          int currentBound = lower;
          for(int ch = 0; ch < 255; ++ch) {
            trial.getBytes()[depth] = (byte) (ch + 1);
            lower = currentBound;
            while (currentBound < upper) {
              if (splits[currentBound].compareTo(trial) >= 0) {
                break;
              }
              currentBound += 1;
            }
            trial.getBytes()[depth] = (byte) ch;
            result.child[ch] = buildTrie(splits, lower, currentBound, trial,
                                         maxDepth);
          }
          // pick up the rest
          trial.getBytes()[depth] = 127;
          result.child[255] = buildTrie(splits, currentBound, upper, trial,
                                        maxDepth);
          return result;
        }

        public void configure(JobConf job) {
          try {
            FileSystem fs = FileSystem.getLocal(job);
            Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
            splitPoints = readPartitions(fs, partFile, job);
            trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
          } catch (IOException ie) {
            throw new IllegalArgumentException("can't read paritions file", ie);
          }
        }

        public TotalOrderPartitioner() {
        }

        public int getPartition(Text key, Text value, int numPartitions) {
          return trie.findPartition(key);
        }

      }

      public int run(String[] args) throws Exception {
        LOG.info("starting");
        JobConf job = (JobConf) getConf();
        Path inputDir = new Path(args[0]);
        inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
        Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
        URI partitionUri = new URI(partitionFile.toString() +
                                   "#" + TeraInputFormat.PARTITION_FILENAME);
        TeraInputFormat.setInputPaths(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        job.setJobName("TeraSort");
        job.setJarByClass(TeraSort.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(Text.class);
        job.setInputFormat(TeraInputFormat.class);
        job.setOutputFormat(TeraOutputFormat.class);
        job.setPartitionerClass(TotalOrderPartitioner.class);
        TeraInputFormat.writePartitionFile(job, partitionFile);
        DistributedCache.addCacheFile(partitionUri, job);
        DistributedCache.createSymlink(job);
        job.setInt("dfs.replication", 1);
        TeraOutputFormat.setFinalSync(job, true);
        JobClient.runJob(job);
        LOG.info("done");
        return 0;
      }

      /**
       * @param args
       */

      public static void main(String[] args) throws Exception {
        int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
        System.exit(res);
      }
    }














 




[hdinsight-errors]: hdinsight-debug-jobs.md

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md


[powershell-install-configure]: ../powershell-install-configure.md

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: #hdinsight-sample-10gb-graysort
[hdinsight-sample-csharp-streaming]: #hdinsight-sample-csharp-streaming
[hdinsight-sample-pi-estimator]: #hdinsight-sample-pi-estimator
[hdinsight-sample-wordcount]: #hdinsight-sample-wordcount

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: https://msdn.microsoft.com/library/3x292kth.aspx
