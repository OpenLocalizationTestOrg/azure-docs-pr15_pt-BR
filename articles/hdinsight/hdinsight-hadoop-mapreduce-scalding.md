<properties
 pageTitle="Desenvolver MapReduce Scalding trabalhos com Maven | Microsoft Azure"
 description="Saiba como usar Maven para criar um trabalho MapReduce Scalding, e em seguida, implantar e executar o trabalho em uma Hadoop em cluster HDInsight."
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
 ms.date="10/18/2016"
 ms.author="larryfr"/>

# <a name="develop-scalding-mapreduce-jobs-with-apache-hadoop-on-hdinsight"></a>Desenvolver MapReduce Scalding trabalhos com Apache Hadoop em HDInsight

Scalding é uma biblioteca de Scala que torna mais fácil criar Hadoop MapReduce trabalhos. Ele oferece uma sintaxe concisa, bem como integração com Scala.

Neste documento, saiba como usar Maven para criar um trabalho de MapReduce de contagem básico do word escrito em Scalding. Em seguida, você aprenderá como implantar e executar esse trabalho em um cluster de HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Hadoop em cluster HDInsight com base em um Windows ou Linux**. Consulte [baseados em Linux provisionar Hadoop em HDInsight](hdinsight-hadoop-provision-linux-clusters.md) ou [baseado no Windows de provisionar Hadoop em HDInsight](hdinsight-provision-clusters.md) para obter mais informações.

* **[Maven](http://maven.apache.org/)**

* **[Plataforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 ou posterior**

## <a name="create-and-build-the-project"></a>Crie e construa o projeto

1. Use o seguinte comando para criar um novo projeto Maven:

        mvn archetype:generate -DgroupId=com.microsoft.example -DartifactId=scaldingwordcount -DarchetypeGroupId=org.scala-tools.archetypes -DarchetypeArtifactId=scala-archetype-simple -DinteractiveMode=false

    Esse comando irá criar um novo diretório chamado **scaldingwordcount**e criar a estrutura de um aplicativo de Scala.

2. No diretório **scaldingwordcount** , abra o arquivo **pom.xml** e substitua o conteúdo com o seguinte:

        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
            <modelVersion>4.0.0</modelVersion>
            <groupId>com.microsoft.example</groupId>
            <artifactId>scaldingwordcount</artifactId>
            <version>1.0-SNAPSHOT</version>
            <name>${project.artifactId}</name>
            <properties>
            <maven.compiler.source>1.6</maven.compiler.source>
            <maven.compiler.target>1.6</maven.compiler.target>
            <encoding>UTF-8</encoding>
            </properties>
            <repositories>
            <repository>
                <id>conjars</id>
                <url>http://conjars.org/repo</url>
            </repository>
            <repository>
                <id>maven-central</id>
                <url>http://repo1.maven.org/maven2</url>
            </repository>
            </repositories>
            <dependencies>
            <dependency>
                <groupId>com.twitter</groupId>
                <artifactId>scalding-core_2.11</artifactId>
                <version>0.13.1</version>
            </dependency>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-core</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
            </dependencies>
            <build>
            <sourceDirectory>src/main/scala</sourceDirectory>
            <plugins>
                <plugin>
                <groupId>org.scala-tools</groupId>
                <artifactId>maven-scala-plugin</artifactId>
                <version>2.15.2</version>
                <executions>
                    <execution>
                    <id>scala-compile-first</id>
                    <phase>process-resources</phase>
                    <goals>
                        <goal>add-source</goal>
                        <goal>compile</goal>
                    </goals>
                    </execution>
                </executions>
                </plugin>
                <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                    </transformers>
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
                    <configuration>
                        <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>com.twitter.scalding.Tool</mainClass>
                        </transformer>
                        </transformers>
                    </configuration>
                    </execution>
                </executions>
                </plugin>
            </plugins>
            </build>
        </project>

    Este arquivo descreve o projeto, dependências e plug-ins. Aqui estão as entradas importantes:

    * **Maven.compiler.Source** e **maven.compiler.target**: define a versão de Java para esse projeto

    * **repositórios**: os repositórios que contêm os arquivos de dependência usados pelo projeto

    * **core_2.11 scalding** e **hadoop-core**: este projeto depende Scalding e Hadoop pacotes principais

    * **plugin do Maven scala**: plug-in para compilar scala aplicativos

    * **plugin do Maven-sombrear**: plug-in para criar sombreado jars (fat). Este plug-in aplica filtros e transformações; specificially:

        * **filtros**: os filtros aplicados modificar as informações de meta incluídas no arquivo jar. Para impedir que isso entrando exceções em tempo de execução, exclui vários arquivos de assinatura que podem ser incluídos com dependências.

        * **execuções**: A configuração de execução de fase do pacote especifica a classe de **com.twitter.scalding.Tool** como a classe principal para o pacote. Sem isso, você precisa especificar com.twitter.scalding.Tool, bem como a classe que contém a lógica do aplicativo, ao executar o trabalho com o comando hadoop.

3. Exclua o diretório **src/teste** , conforme você não criarão testes com este exemplo.

4. Abra o arquivo **src/main/scala/com/microsoft/example/App.scala** e substitua o conteúdo com o seguinte:

        package com.microsoft.example

        import com.twitter.scalding._

        class WordCount(args : Args) extends Job(args) {
            // 1. Read lines from the specified input location
            // 2. Extract individual words from each line
            // 3. Group words and count them
            // 4. Write output to the specified output location
            TextLine(args("input"))
            .flatMap('line -> 'word) { line : String => tokenize(line) }
            .groupBy('word) { _.size }
            .write(Tsv(args("output")))

            //Tokenizer to split sentance into words
            def tokenize(text : String) : Array[String] = {
            text.toLowerCase.replaceAll("[^a-zA-Z0-9\\s]", "").split("\\s+")
            }
        }

    Isso implementa um trabalho de contagem do word básica.

5. Salve e feche os arquivos.

6. Use o seguinte comando do diretório **scaldingwordcount** para criar e empacotar o aplicativo:

        mvn package

    Quando este trabalho estiver concluído, o pacote que contém o aplicativo WordCount pode ser encontrado no **target/scaldingwordcount-1.0-SNAPSHOT.jar**.

## <a name="run-the-job-on-a-linux-based-cluster"></a>Executar o trabalho em um cluster baseado em Linux

> [AZURE.NOTE] As etapas a seguir usam SSH e o comando Hadoop. Para outros métodos de MapReduce trabalhos em execução, consulte [Usar MapReduce em Hadoop em HDInsight](hdinsight-use-mapreduce.md).

1. Use o seguinte comando para carregar o pacote no seu cluster HDInsight:

        scp target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:

    Isso copia os arquivos do sistema local para o nó principal.

    > [AZURE.NOTE] Se você usou uma senha para proteger sua conta SSH, você será solicitado a senha. Se você usou uma chave SSH, você talvez precise usar o `-i` parâmetro e o caminho para a chave privada. Por exemplo,`scp -i /path/to/private/key target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:.`

2. Use o seguinte comando para conectar-se para o nó principal do cluster:

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] Se você usou uma senha para proteger sua conta SSH, você será solicitado a senha. Se você usou uma chave SSH, você talvez precise usar o `-i` parâmetro e o caminho para a chave privada. Por exemplo,`ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`

3. Uma vez conectado para o nó principal, use o seguinte comando para executar o trabalho de contagem do word

        yarn jar scaldingwordcount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount --hdfs --input wasbs:///example/data/gutenberg/davinci.txt --output wasbs:///example/wordcountout

    Isso executa a classe WordCount implementado anteriormente. `--hdfs`instrui o trabalho para usar HDFS. `--input`Especifica o arquivo de texto de entrada, enquanto `--output` Especifica o local de saída.

4. Após concluir o trabalho, use o seguinte para exibir a saída.

        hdfs dfs -text wasbs:///example/wordcountout/*

    Isso exibirá informações similares ao seguinte:

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="run-the-job-on-a-windows-based-cluster"></a>Executar o trabalho em um cluster baseado no Windows

As etapas a seguir usam o Windows PowerShell. Para outros métodos de MapReduce trabalhos em execução, consulte [Usar MapReduce em Hadoop em HDInsight](hdinsight-use-mapreduce.md).

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

2. Inicie o Azure PowerShell e faça logon em sua conta do Azure. Depois de fornecer suas credenciais, o comando retorna informações sobre sua conta.

        Add-AzureRMAccount

        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...

3. Se você tiver várias assinaturas, forneça a identificação de assinatura que você deseja usar para implantação.

        Select-AzureRMSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] Você pode usar `Get-AzureRMSubscription` para obter uma lista de todas as assinaturas associada a sua conta, que inclui a Id de inscrição para cada um deles.

4. Use o seguinte script para carregar e executar o trabalho WordCount. Substituir `CLUSTERNAME` com o nome do seu HDInsight cluster e verifique se `$fileToUpload` é o caminho correto para o arquivo de __scaldingwordcount-1.0-SNAPSHOT.jar__ .

        #Cluster name, file to be uploaded, and where to upload it
        $clustername = Read-Host -Prompt "Enter the HDInsight cluster name"
        $fileToUpload = Read-Host -Prompt "Enter the path to the scaldingwordcount-1.0-SNAPSHOT.jar file"
        $blobPath = "example/jars/scaldingwordcount-1.0-SNAPSHOT.jar"

        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential -Message "Enter the login credentials for the cluster"
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context
            
        #Create a job definition and start the job
        $jobDef=New-AzureRmHDInsightMapReduceJobDefinition `
            -JobName ScaldingWordCount `
            -JarFile wasbs:///example/jars/scaldingwordcount-1.0-SNAPSHOT.jar `
            -ClassName com.microsoft.example.WordCount `
            -arguments "--hdfs", `
                        "--input", `
                        "wasbs:///example/data/gutenberg/davinci.txt", `
                        "--output", `
                        "wasbs:///example/wordcountout"
        $job = Start-AzureRmHDInsightJob `
            -clustername $clusterName `
            -jobdefinition $jobDef `
            -HttpCredential $creds
        Write-Output "Job ID is: $job.JobId"
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        #Download the output of the job
        Get-AzureStorageBlobContent `
            -Blob example/wordcountout/part-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
        #Log any errors that occured
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

     Quando você executa o script, você será solicitado a digitar o nome de usuário de administrador e a senha para o seu cluster HDInsight. Todos os erros que ocorrem enquanto o trabalho é executado serão registrados no console.
     
6. Quando o trabalho estiver concluído, a saída será baixada para o arquivo __txt__ no diretório atual. Use o comando a seguir para exibir os resultados.

        cat output.txt

    O arquivo deve conter valores similares ao seguinte:

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como usar Scalding para criar trabalhos MapReduce para HDInsight, use os links a seguir para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Use a seção com HDInsight](hdinsight-use-hive.md)

* [Usar porco com HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce trabalhos com HDInsight](hdinsight-use-mapreduce.md)
