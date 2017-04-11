<properties
pageTitle="Usar uma função de definidos pelo usuário em Java (UDF) com seção em HDInsight | Microsoft Azure"
description="Saiba como criar e usar uma função definida pelo usuário do Java (UDF) de seção no HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="09/27/2016"
ms.author="larryfr"/>

#<a name="use-a-java-udf-with-hive-in-hdinsight"></a>Use um Java UDF com seção no HDInsight

Seção é ideal para trabalhar com dados de HDInsight, mas, às vezes, você precisa mais geral finalidade idioma. Seção permite que você crie funções definidas pelo usuário (UDF) usando uma variedade de linguagens de programação. Neste documento, você aprenderá como usar um UDF Java da seção.

## <a name="requirements"></a>Requisitos

* Uma assinatura do Azure

* Um cluster de HDInsight (Windows ou baseado em Linux)

    > [AZURE.NOTE] A maioria das etapas deste documento funcionarão em ambos os tipos de cluster; No entanto, as etapas usadas para carregar o UDF compilado ao cluster e executá-lo são específicas para clusters baseados em Linux. São fornecidos links para informações que podem ser usadas com clusters baseados no Windows.

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 ou posterior (ou um equivalente, como OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* Um editor de texto ou IDE Java

    > [AZURE.IMPORTANT] Se você estiver usando um servidor baseado em Linux HDInsight, mas criar os arquivos de Python em um cliente do Windows, você deve usar um editor que usa LF como um fim de linha. Se você não tiver certeza se seu editor usa LF ou CRLF, consulte a seção de [solução de problemas](#troubleshooting) para obter as etapas sobre como remover o caractere de CR usando utilitários no cluster HDInsight.

## <a name="create-an-example-udf"></a>Criar um exemplo UDF

1. Uma linha de comando, use o seguinte para criar um novo projeto Maven:

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    > [AZURE.NOTE] Se você estiver usando o PowerShell, você deve colocar os parâmetros entre aspas. Por exemplo, `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Isso criará um novo diretório chamado __exampleudf__, que conterá o projeto Maven.

2. Depois que o projeto tiver sido criado, exclua o diretório __src/exampleudf/teste__ que foi criado como parte do projeto; ele não será usado para este exemplo.

3. Abra o __exampleudf/pom.xml__e substituir o `<dependencies>` entrada com o seguinte:

        <dependencies>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-client</artifactId>
                <version>2.7.1</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.hive</groupId>
                <artifactId>hive-exec</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>

    Essas entradas especificam a versão do Hadoop e seção incluído no HDInsight 3.3 e 3,4 clusters. Você pode encontrar informações sobre as versões do Hadoop e seção fornecidos com HDInsight do documento de [controle de versão de componente HDInsight](hdinsight-component-versioning.md) .

    Adicionar um `<build>` seção antes do `</project>` linha ao final do arquivo. Esta seção deve conter o seguinte:

        <build>
            <plugins>
                <!-- build for Java 1.7, even if you're on a later version -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
                    <configuration>
                        <source>1.7</source>
                        <target>1.7</target>
                    </configuration>
                </plugin>
                <!-- build an uber jar -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-shade-plugin</artifactId>
                    <version>2.3</version>
                    <configuration>
                        <!-- Keep us from getting a can't overwrite file error -->
                        <transformers>
                            <transformer
                                    implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                            </transformer>
                            <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
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
            </plugins>
        </build>
    
    Essas entradas definem como construir o projeto. Especificamente, a versão do Java que usa o projeto e como criar um uberjar para implantação ao cluster.

    Salve o arquivo depois que as alterações foram feitas.

4. Renomeie __exampleudf/src/main/java/com/microsoft/examples/App.java__ para __ExampleUDF.java__e, em seguida, abra o arquivo no editor.

5. Substitua o conteúdo do arquivo __ExampleUDF.java__ a seguir, em seguida, salve o arquivo.

        package com.microsoft.examples;

        import org.apache.hadoop.hive.ql.exec.Description;
        import org.apache.hadoop.hive.ql.exec.UDF;
        import org.apache.hadoop.io.*;

        // Description of the UDF
        @Description(
            name="ExampleUDF",
            value="returns a lower case version of the input string.",
            extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
        )
        public class ExampleUDF extends UDF {
            // Accept a string input
            public String evaluate(String input) {
                // If the value is null, return a null
                if(input == null)
                    return null;
                // Lowercase the input string and return it
                return input.toLowerCase();
            }
        }

    Isso implementa uma UDF que aceita um valor de cadeia de caracteres e retorna uma versão minúsculas da cadeia de caracteres.

## <a name="build-and-install-the-udf"></a>Criar e instalar o UDF

1. Use o seguinte comando para compilar e empacotar UDF:

        mvn compile package

    Isso irá criar e empacotar UDF em __exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar__.

2. Use o `scp` comando para copiar o arquivo para o cluster HDInsight.

        scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight

    Substitua __myuser__ com a conta de usuário do SSH para o seu cluster. Substitua o nome do cluster __meucluster__ . Se você usou uma senha para proteger a conta SSH, você será solicitado a digitar a senha. Se você usou um certificado, você pode precisar usar o `-i` parâmetro para especificar o arquivo de chave privado.

3. Conectar-se ao cluster usando SSH. 

        ssh myuser@mycluster-ssh.azurehdinsight.net

    Para obter mais informações sobre como usar SSH com HDInsight, consulte os seguintes documentos.

    * [Use SSH com baseado em Linux Hadoop em HDInsight Linux, Unix ou dos X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

4. Da sessão SSH, copie o arquivo jar ao armazenamento de HDInsight.

        hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars

## <a name="use-the-udf-from-hive"></a>Usar o UDF da seção

1. Use o seguinte para iniciar o cliente de Beeline da sessão SSH.

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    Esse comando pressupõe que você usou o padrão de __administrador__ para a conta de logon para o seu cluster.

2. Quando chegar à `jdbc:hive2://localhost:10001/>` solicitar, insira o seguinte para adicionar o UDF à seção e expô-la como uma função.

        ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
        CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';

3. Use o UDF para converter valores recuperados de uma tabela de cadeias de caracteres de letras minúsculas.

        SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;

    Isto irá selecione a plataforma do dispositivo (Android, Windows, iOS, etc.) da tabela, converter a cadeia de caracteres em minúsculas e exibi-los. A saída aparecerão semelhante à seguinte.

        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Próximas etapas

Para outras maneiras de trabalhar com seção, consulte a [Seção uso com HDInsight](hdinsight-use-hive.md).

Para obter mais informações sobre funções de Hive User-Defined, consulte a seção [seção operadores e funções definidas pelo usuário](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) do wiki seção em apache.org.
