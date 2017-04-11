<properties
pageTitle="Criar um aplicativo de HBase usando Maven e implantar ao baseado no Windows HDInsight | Microsoft Azure"
description="Saiba como usar Apache Maven para construir um aplicativo baseado em Java Apache HBase, em seguida, implantá-lo em um cluster baseado no Windows Azure HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"
tags="azure-portal"/>

<tags
ms.service="hdinsight"
ms.workload="big-data"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="10/03/2016"
ms.author="larryfr"/>

#<a name="use-maven-to-build-java-applications-that-use-hbase-with-windows-based-hdinsight-hadoop"></a>Usar Maven para construir aplicativos de Java que usam HBase com HDInsight baseados no Windows (Hadoop)

Aprenda a criar e criar um aplicativo de [Apache HBase](http://hbase.apache.org/) em Java usando Apache Maven. Use o aplicativo com Azurehdinsight (Hadoop).

[Maven](http://maven.apache.org/) é uma ferramenta de compreensão que permite criar relatórios para projetos Java, documentação e software e gerenciamento de projetos de software. Neste artigo, você aprenderá usá-lo para criar um aplicativo Java básico que que cria, consultas e exclui uma tabela de HBase em um cluster de Azurehdinsight.

> [AZURE.NOTE] As etapas neste documento presumem que você está usando um cluster de HDInsight baseados no Windows. Para obter informações sobre como usar um cluster baseado no Linux HDInsight, consulte [Usar Maven para construir aplicativos de Java que usam HBase com HDInsight baseados em Linux](hdinsight-hbase-build-java-maven-linux.md)

##<a name="requirements"></a>Requisitos

* [Plataforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 ou posterior

* [Maven](http://maven.apache.org/)


* [Um cluster baseado no Windows HDInsight com HBase](hdinsight-hbase-tutorial-get-started.md#create-hbase-cluster)


    > [AZURE.NOTE] As etapas neste documento foram testadas com as versões de cluster HDInsight 3,2 e 3,3. Os valores padrão fornecidos nos exemplos são para um cluster de HDInsight 3.3.

##<a name="create-the-project"></a>Criar o projeto

1. Na linha de comando em seu ambiente de desenvolvimento, altere diretórios para o local onde você deseja criar o projeto, por exemplo, `cd code\hdinsight`.

2. Use o comando __mvn__ , que é instalado com Maven, para gerar a estrutura do projeto.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Esse comando cria uma pasta no local atual, com o nome especificado pelo parâmetro __artifactID__ (**hbaseapp** neste exemplo). Este diretório contém os seguintes itens:

    * __POM.XML__: O modelo de objeto do projeto ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contém detalhes de configuração e informações usados para construir o projeto.

    * __src__: A pasta que contém o diretório __main\java\com\microsoft\examples__ , onde você vai criar o aplicativo.

3. Exclua o arquivo de __src\test\java\com\microsoft\examples\apptest.java__ porque ela não é usada neste exemplo.

##<a name="update-the-project-object-model"></a>Atualizar o modelo de objeto do projeto

1. Edite o arquivo __pom.xml__ e adicione o seguinte código dentro a `<dependencies>` seção:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    Esta seção informa Maven que o projeto requer __hbase-client__ versão __1.1.2__. Em tempo de compilação, essa dependência é baixada do repositório Maven padrão. Você pode usar a [Pesquisa de repositório Central Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) para saber mais sobre essa dependência.

    > [AZURE.IMPORTANT] O número da versão deve corresponder à versão do HBase fornecida com o seu cluster HDInsight. Use a tabela a seguir para encontrar o número de versão correta.

  	| Versão de cluster HDInsight | Versão do HBase usar |
  	| ----- | ----- |
  	| 3,2 | 0.98.4-hadoop2 |
  	| 3.3 | 1.1.2 |

    Para obter mais informações sobre versões de HDInsight e componentes, veja [quais são os diferentes componentes de Hadoop disponíveis com HDInsight](hdinsight-component-versioning.md).

2. Se você estiver usando um cluster de HDInsight 3.3, você também deve adicionar o seguinte para o `<dependencies>` seção:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    Essa dependência carregará os componentes rio-core, que são usados pelo Hbase versão 1.1.

2. Adicione o seguinte código para o arquivo de __pom.xml__ . Esta seção deve estar dentro do `<project>...</project>` marcas no arquivo, por exemplo, entre `</dependencies>` e `</project>`.

        <build>
          <sourceDirectory>src</sourceDirectory>
          <resources>
              <resource>
                <directory>${basedir}/conf</directory>
                <filtering>false</filtering>
                <includes>
                  <include>hbase-site.xml</include>
                </includes>
              </resource>
            </resources>
          <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                </configuration>
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

    O `<resources>` seção configura um recurso (__conf\hbase-site.xml__) que contém informações de configuração para HBase.

    > [AZURE.NOTE] Você também pode definir valores de configuração por meio de código. Consulte os comentários no exemplo __CreateTable__ que segue sobre como fazer isso.

    Isso `<plugins>` seção configura o [Plug-in do compilador Maven](http://maven.apache.org/plugins/maven-compiler-plugin/) e [Maven sombrear plug-in](http://maven.apache.org/plugins/maven-shade-plugin/). O compilador plug-in é usado para compilar a topologia. O plug-in de sombreamento é usado para impedir a duplicação de licenças no pacote JAR que é desenvolvida por Maven. O motivo que é usado é que os arquivos de licença duplicadas causar um erro em tempo de execução no cluster HDInsight. Usando sombrear-plugin maven com o `ApacheLicenseResourceTransformer` implementação impede que esse erro.

    O maven-sombrear-plug-in também produz um jar de uber (ou jar fat) que contém todas as dependências exigidas pelo aplicativo.

3. Salve o arquivo de __pom.xml__ .

4. Crie um novo diretório chamado __conf__ no diretório __hbaseapp__ . No diretório __conf__ , crie um arquivo denominado __hbase-site.xml__. Use o seguinte como o conteúdo do arquivo:

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
          * Copyright 2010 The Apache Software Foundation
          *
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
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

    Este arquivo será usado para carregar a configuração de HBase para um cluster de HDInsight.

    > [AZURE.NOTE] Este é um arquivo de hbase-site.xml mínimo e contém as configurações mínimas vazia para o cluster HDInsight.

3. Salve o arquivo __hbase-site.xml__ .

##<a name="create-the-application"></a>Criar o aplicativo

1. Vá para o diretório __hbaseapp\src\main\java\com\microsoft\examples__ e renomeie o arquivo app.java para __CreateTable.java__.

2. Abra o arquivo __CreateTable.java__ e substituir o conteúdo existente com o seguinte código:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;

        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Example of setting zookeeper values for HDInsight
            // in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");
            // The following sets the znode root for Linux-based HDInsight
            //config.set("zookeeper.znode.parent","/hbase-unsecure");

            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);

            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

            HTable table = new HTable(config, "people");

            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }

    Esta é a classe de __CreateTable__ , que irá criar uma tabela chamada __pessoas__ e preenchê-lo com alguns usuários predefinidos.

3. Salve o arquivo de __CreateTable.java__ .

4. No diretório __hbaseapp\src\main\java\com\microsoft\examples__ , crie um novo arquivo chamado __SearchByEmail.java__. Use o código a seguir conforme o conteúdo deste arquivo:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Scan;
        import org.apache.hadoop.hbase.client.ResultScanner;
        import org.apache.hadoop.hbase.client.Result;
        import org.apache.hadoop.hbase.filter.RegexStringComparator;
        import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
        import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
        import org.apache.hadoop.hbase.util.Bytes;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class SearchByEmail {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Use GenericOptionsParser to get only the parameters to the class
            // and not all the parameters passed (when using WebHCat for example)
            String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
            if (otherArgs.length != 1) {
              System.out.println("usage: [regular expression]");
              System.exit(-1);
            }

            // Open the table
            HTable table = new HTable(config, "people");

            // Define the family and qualifiers to be used
            byte[] contactFamily = Bytes.toBytes("contactinfo");
            byte[] emailQualifier = Bytes.toBytes("email");
            byte[] nameFamily = Bytes.toBytes("name");
            byte[] firstNameQualifier = Bytes.toBytes("first");
            byte[] lastNameQualifier = Bytes.toBytes("last");

            // Create a new regex filter
            RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
            // Attach the regex filter to a filter
            //   for the email column
            SingleColumnValueFilter filter = new SingleColumnValueFilter(
              contactFamily,
              emailQualifier,
              CompareOp.EQUAL,
              emailFilter
            );

            // Create a scan and set the filter
            Scan scan = new Scan();
            scan.setFilter(filter);

            // Get the results
            ResultScanner results = table.getScanner(scan);
            // Iterate over results and print  values
            for (Result result : results ) {
              String id = new String(result.getRow());
              byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
              String firstName = new String(firstNameObj);
              byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
              String lastName = new String(lastNameObj);
              System.out.println(firstName + " " + lastName + " - ID: " + id);
              byte[] emailObj = result.getValue(contactFamily, emailQualifier);
              String email = new String(emailObj);
              System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
            }
            results.close();
            table.close();
          }
        }

    A classe __SearchByEmail__ pode ser usada para consultar linhas por endereço de email. Porque ele usa um filtro de expressões regulares, você pode fornecer uma cadeia de caracteres ou uma expressão regular ao usar a classe.

5. Salve o arquivo de __SearchByEmail.java__ .

6. No diretório __hbaseapp\src\main\hava\com\microsoft\examples__ , crie um novo arquivo chamado __DeleteTable.java__. Use o código a seguir conforme o conteúdo deste arquivo:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;

        public class DeleteTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // Disable, and then delete the table
            admin.disableTable("people");
            admin.deleteTable("people");
          }
        }

    Essa classe é para limpar este exemplo desabilitando e soltando a tabela criada pela classe __CreateTable__ .

7. Salve o arquivo de __DeleteTable.java__ .

##<a name="build-and-package-the-application"></a>Criar e empacotar o aplicativo

1. Abra um prompt de comando e altere os diretórios para o diretório __hbaseapp__ .

2. Use o seguinte comando para criar um arquivo JAR que contém o aplicativo:

        mvn clean package

    Isso limpa qualquer artefatos de compilação anterior, downloads quaisquer dependências que ainda não tiver sido instaladas, em seguida, cria e pacotes do aplicativo.

3. Quando o comando for concluído, o diretório __hbaseapp\target__ contém um arquivo denominado __hbaseapp-1.0-SNAPSHOT.jar__.

    > [AZURE.NOTE] O arquivo __hbaseapp-1.0-SNAPSHOT.jar__ é um uber jar (às vezes chamado de um jar fat,) que contém todas as dependências necessárias para executar o aplicativo.

##<a name="upload-the-jar-file-and-start-a-job"></a>Carregue o arquivo JAR e iniciar um trabalho

Há várias maneiras para carregar um arquivo no seu cluster HDInsight, conforme descrito em [carregar dados para trabalhos de Hadoop em HDInsight](hdinsight-upload-data.md). As etapas a seguir usam o PowerShell do Azure.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


1. Após instalar e configurar o Azure PowerShell, crie um novo arquivo chamado __hbase-runner.psm1__. Use o seguinte como o conteúdo deste arquivo:

        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.CreateTable"
        -clusterName "MyHDInsightCluster"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "contoso.com"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "^r" -showErr
        #>
        
        function Start-HBaseExample {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
        #The class to run
        [Parameter(Mandatory = $true)]
        [String]$className,
        
        #The name of the HDInsight cluster
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        
        #Only used when using SearchByEmail
        [Parameter(Mandatory = $false)]
        [String]$emailRegex,
        
        #Use if you want to see stderr output
        [Parameter(Mandatory = $false)]
        [Switch]$showErr
        )
        
        Set-StrictMode -Version 3
        
        # Is the Azure module installed?
        FindAzure
        
        # Get the login for the HDInsight cluster
        $creds = Get-Credential
        
        # Get storage information
        $storage = GetStorage -clusterName $clusterName
        
        # The JAR
        $jarFile = "wasbs:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"
        
        # The job definition
        $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile $jarFile `
            -ClassName $className `
            -Arguments $emailRegex
        
        # Get the job output
        $job = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $jobDefinition `
            -HttpCredential $creds
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        if($showErr)
        {
        Write-Host "STDERR"
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccount `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds `
                    -DisplayOutputType StandardError
        }
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccount `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds
        }
        
        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        -Container "MyContainer"
        #>
        
        function Add-HDInsightFile {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The path to the local file.
                [Parameter(Mandatory = $true)]
                [String]$localPath,
                
                #The destination path and file name, relative to the root of the container.
                [Parameter(Mandatory = $true)]
                [String]$destinationPath,
                
                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,
                
                #If specified, overwrites existing files without prompting
                [Parameter(Mandatory = $false)]
                [Switch]$force
            )
            
            Set-StrictMode -Version 3
            
            # Is the Azure module installed?
            FindAzure
            
            # Get authentication for the cluster
            $creds=Get-Credential
            
            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }
            
            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName
            
            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath `
                -Blob $destinationPath `
                -force:$force `
                -Container $storage.container `
                -Context $storage.context
        }
        
        function FindAzure {
            # Is there an active Azure subscription?
            $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
            }
        }
        
        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
            
            # Get storage information
            $resourceGroup = $hdi.ResourceGroup
            $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
            $container=$hdi.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value
            # Get the resource group, in case we need that
            $return.resourceGroup = $resourceGroup
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $container
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccount = $storageAccountName
            $return.storageAccountKey = $storageAccountKey
            
            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

    Este arquivo contém dois módulos:

    * __Adicionar HDInsightFile__ - usado para carregar arquivos para HDInsight

    * __Início-HBaseExample__ - usado para executar as classes criadas anteriormente

2. Salve o arquivo __hbase-runner.psm1__ .

3. Abrir uma nova janela do PowerShell do Azure, altere diretórios para o diretório __hbaseapp__ e, em seguida, execute o seguinte comando.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Altere o caminho para o local do arquivo __hbase-runner.psm1__ criado anteriormente. Isso registra o módulo para esta sessão do PowerShell do Azure.

2. Use o seguinte comando para carregar o __hbaseapp-1.0-SNAPSHOT.jar__ no seu cluster HDInsight.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Substitua o nome do seu cluster HDInsight __hdinsightclustername__ . O comando carrega o __hbaseapp-1.0-SNAPSHOT.jar__ para o local de __exemplo/jars__ no armazenamento principal para o seu cluster HDInsight.

3. Depois que os arquivos são carregados, use o código a seguir para criar uma tabela usando o __hbaseapp__:

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Substitua o nome do seu cluster HDInsight __hdinsightclustername__ .

    Esse comando cria uma nova tabela denominada __pessoas__ no seu cluster HDInsight. Este comando não mostrar qualquer saída na janela do console.

2. Para pesquisar entradas na tabela, use o seguinte comando:

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Substitua o nome do seu cluster HDInsight __hdinsightclustername__ .

    Esse comando usa a classe **SearchByEmail** para pesquisar qualquer linhas onde a família de coluna de __contactinformation__ e a coluna de __email__ , contém a cadeia de caracteres __contoso.com__. Você deve receber os seguintes resultados:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Usando __fabrikam.com__ para o `-emailRegex` valor retorna os usuários que têm __fabrikam.com__ no campo email. Desde que esta pesquisa é implementada usando um filtro de baseada em expressões regular, você também pode inserir expressões regulares, como __^ r__, quais entradas retorna onde o email começa com a letra 'r'.

##<a name="delete-the-table"></a>Excluir a tabela

Quando terminar com o exemplo, use o seguinte comando da sessão do PowerShell do Azure para excluir a tabela de __pessoas__ usada neste exemplo:

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Substitua o nome do seu cluster HDInsight __hdinsightclustername__ .

##<a name="troubleshooting"></a>Solução de problemas

###<a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Nenhum resultado ou resultados inesperados ao usar HBaseExample de início

Use o `-showErr` parâmetro para exibir o erro padrão (STDERR) que é gerado ao executar o trabalho.
