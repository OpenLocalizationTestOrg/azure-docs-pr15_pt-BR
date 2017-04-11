<properties
    pageTitle="Desenvolver programas Java MapReduce para baseados em Linux HDInsight | Microsoft Azure"
    description="Aprenda a desenvolver programas Java MapReduce e implantá-los ao HDInsight baseados em Linux."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="Blackmist"
    documentationCenter=""
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight-linux"></a>Desenvolver programas Java MapReduce para Hadoop no HDInsight Linux

Este documentos orientará usando Apache Maven para criar um aplicativo de MapReduce, e em seguida, implantar e executá-lo em um Hadoop baseado em Linux em cluster HDInsight.

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 ou posterior (ou um equivalente, como OpenJDK)

- [Apache Maven](http://maven.apache.org/)

- **Uma assinatura do Azure**

- **CLI Azure**

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a name="configure-environment-variables"></a>Configurar variáveis de ambiente

As seguintes variáveis de ambiente podem ser definidas quando você instala o Java e o JDK. No entanto, você deve verificar que eles existem e que contêm os valores corretos para seu sistema.

* **JAVA_HOME** - devem apontar para a pasta onde o ambiente de tempo de execução Java (JRE) está instalado. Por exemplo, em um sistema de OS X, Unix ou Linux, ele deve ter um valor semelhante a `/usr/lib/jvm/java-7-oracle`. No Windows, ele teria um valor semelhante a`c:\Program Files (x86)\Java\jre1.7`

* **Caminho** - deve conter os seguintes caminhos:

    * **JAVA_HOME** (ou o caminho equivalente)

    * **JAVA_HOME\bin** (ou o caminho equivalente)

    * O diretório onde Maven está instalado

##<a name="create-a-new-maven-project"></a>Criar um novo projeto Maven

1. De uma sessão de terminal, ou a linha de comando em seu ambiente de desenvolvimento, altere diretórios para o local em que você deseja armazenar esse projeto.

3. Use o comando __mvn__ , que é instalado com Maven, para gerar a estrutura do projeto.

        mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Isso criará uma nova pasta na pasta atual, com o nome especificado pelo parâmetro __artifactID__ (**wordcountjava** neste exemplo). Este diretório irá conter os seguintes itens:

    * __pom.xml__ - o [Modelo de objeto do projeto (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) que contém detalhes de configuração e informações usados para construir o projeto.

    * __src__ - o diretório que contém o diretório __principal/java/org/apache/hadoop/exemplos__ , onde você vai criar o aplicativo.

3. Exclua o arquivo de __src/test/java/org/apache/hadoop/examples/apptest.java__ , pois ela não será usada neste exemplo.

##<a name="add-dependencies"></a>Adicionar dependências

1. Edite o arquivo __pom.xml__ e adicione o seguinte dentro do `<dependencies>` seção:

        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-client-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>

    Isso informa Maven que o projeto requer as bibliotecas (listado em &lt;artifactId\>) com uma versão específica (listados dentro &lt;versão\>). Em tempo de compilação, isso será baixado do repositório Maven padrão. Você pode usar a [pesquisa de Repositório Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) para exibir mais.

    O `<scope>provided</scope>` informa Maven que essas dependências não devem ser empacotadas com o aplicativo, como eles serão fornecidos pelo cluster HDInsight em tempo de execução.

2. Adicione o seguinte para o arquivo de __pom.xml__ . Isso deve estar dentro do `<project>...</project>` marcas no arquivo; Por exemplo, entre `</dependencies>` e `</project>`.

        <build>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
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
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
          </plugins>
        </build>

    O plug-in primeiro configura o [Plug-in do Maven sombrear](http://maven.apache.org/plugins/maven-shade-plugin/), que é usada para construir um uberjar (às vezes chamado de um fatjar), que contém dependências exigidas pelo aplicativo. Ele também impede a duplicação de licenças dentro do pacote de jar, que pode causar problemas em alguns sistemas.

    O plug-in segundo configura o compilador Maven, que é usado para definir a versão do Java obrigatório por este aplicativo para a versão usada no cluster HDInsight.

3. Salve o arquivo de __pom.xml__ .

##<a name="create-the-mapreduce-application"></a>Criar o aplicativo MapReduce

1. Vá para o diretório __wordcountjava/src/principal/java/org/apache/hadoop/exemplos__ e renomeie o arquivo __App.java__ para __WordCount.java__.

2. Abra o arquivo __WordCount.java__ em um editor de texto e substituir o conteúdo com o seguinte:

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

    Observe o nome do pacote é **org.apache.hadoop.examples** e o nome da classe é **WordCount**. Você usará esses nomes quando você enviar o trabalho MapReduce.

3. Salve o arquivo.

##<a name="build-the-application"></a>Criar o aplicativo

1. Alterar para o diretório __wordcountjava__ , se você não ainda estiver lá.

2. Use o seguinte comando para criar um arquivo JAR contendo o aplicativo:

        mvn clean package

    Isso irá limpar qualquer artefatos de compilação anterior, baixe as dependências que ainda não tiver sido instaladas e, em seguida, criar e empacotar o aplicativo.

3. Quando o comando termina, o diretório de __wordcountjava/destino__ conterá um arquivo denominado __wordcountjava-1.0-SNAPSHOT.jar__.

    > [AZURE.NOTE] O arquivo __wordcountjava-1.0-SNAPSHOT.jar__ é um uberjar, que contém não apenas o trabalho WordCount, mas também dependências que requer o trabalho em tempo de execução.


##<a id="upload"></a>Carregar o jar

Use o seguinte comando para carregar o arquivo jar para o headnode HDInsight:

    scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

Isso copia os arquivos do sistema local para o nó principal.

> [AZURE.NOTE] Se você usou uma senha para proteger sua conta SSH, você será solicitado a senha. Se você usou uma chave SSH, você talvez precise usar o `-i` parâmetro e o caminho para a chave privada. Por exemplo, `scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

##<a name="run"></a>Executar o trabalho MapReduce

1. Conecte ao HDInsight usando SSH, conforme descrito nos seguintes artigos:

    - [Use SSH com baseado em Linux Hadoop em HDInsight Linux, Unix ou dos X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Da sessão SSH, use o seguinte comando para executar o aplicativo MapReduce:

        yarn jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/wordcountout

    Isso usar o aplicativo WordCount MapReduce para contar as palavras no arquivo davinci.txt e armazenar os resultados da __wasbs: / / / exemplo/dados/wordcountout__. O arquivo de entrada e saída são armazenados para o armazenamento padrão do cluster.

3. Quando o trabalho estiver concluído, use o seguinte para exibir os resultados:

        hdfs dfs -cat wasbs:///example/data/wordcountout/*

    Você deve receber uma lista de palavras e contagens, com valores similares ao seguinte:

        zeal    1
        zelus   1
        zenith  2

##<a id="nextsteps"></a>Próximas etapas

Neste documento, você aprendeu a desenvolver um trabalho MapReduce Java. Consulte os seguintes documentos para outras maneiras de trabalhar com HDInsight.

- [Use a seção com HDInsight][hdinsight-use-hive]
- [Usar porco com HDInsight][hdinsight-use-pig]
- [Usar MapReduce com HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações, consulte também o [Java Developer Center](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

