<properties
    pageTitle="Criar um aplicativo de HBase usando Maven e Java, em seguida, implantar em baseados em Linux HDInsight | Microsoft Azure"
    description="Saiba como usar Apache Maven para construir um aplicativo baseado em Java Apache HBase, em seguida, implantá-lo ao HDInsight baseados em Linux na nuvem Azure."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="larryfr"/>

#<a name="use-maven-to-build-java-applications-that-use-hbase-with-linux-based-hdinsight-hadoop"></a>Usar Maven para construir aplicativos de Java que usam HBase com baseado em Linux HDInsight (Hadoop)

Aprenda a criar e criar um aplicativo de [Apache HBase](http://hbase.apache.org/) em Java usando Apache Maven. Use o aplicativo com um cluster de HDInsight baseados em Linux.

[Maven](http://maven.apache.org/) é uma ferramenta de compreensão que permite criar relatórios para projetos Java, documentação e software e gerenciamento de projetos de software. Neste artigo, você aprenderá a usá-lo para criar um aplicativo Java básico que cria, consultas e exclui uma tabela de HBase em um cluster de HDInsight baseados em Linux.

> [AZURE.NOTE] As etapas neste documento presumem que você está usando um cluster de HDInsight baseados em Linux. Para obter informações sobre como usar um cluster baseado no Windows HDInsight, consulte [Usar Maven para construir aplicativos de Java que usam HBase com HDInsight baseado no Windows](hdinsight-hbase-build-java-maven.md)

##<a name="requirements"></a>Requisitos

* [Plataforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 ou posterior

* [Maven](http://maven.apache.org/)

* [Um cluster baseado no Linux Azurehdinsight com HBase](../hdinsight-hbase-tutorial-get-started-linux.md#create-hbase-cluster)

    > [AZURE.NOTE] As etapas neste documento foram testadas com versões de cluster HDInsight 3,2, 3.3 e 3.4. Os valores padrão fornecidos nos exemplos são para um cluster de HDInsight 3.4.

* **Familiaridade com SSH e SCP**. Para obter mais informações sobre como usar SSH e SCP com HDInsight, consulte o seguinte:

    * **Clientes Linux, Unix ou OS X**: consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Clientes do Windows**: consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

##<a name="create-the-project"></a>Criar o projeto

1. Na linha de comando em seu ambiente de desenvolvimento, altere diretórios para o local onde você deseja criar o projeto, por exemplo, `cd code/hdinsight`.

2. Use o comando __mvn__ , que é instalado com Maven, para gerar a estrutura do projeto.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Isso cria uma nova pasta na pasta atual, com o nome especificado pelo parâmetro __artifactID__ (**hbaseapp** neste exemplo). Este diretório irá conter os seguintes itens:

    * __POM.XML__: O modelo de objeto do projeto ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contém detalhes de configuração e informações usados para construir o projeto.

    * __src__: A pasta que contém o diretório __principal/java/com/microsoft/exemplos__ , onde você vai criar o aplicativo.

3. Exclua o arquivo de __src/test/java/com/microsoft/examples/apptest.java__ porque ela não será usada neste exemplo.

##<a name="update-the-project-object-model"></a>Atualizar o modelo de objeto do projeto

1. Edite o arquivo __pom.xml__ e adicione o seguinte código dentro a `<dependencies>` seção:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    Isso informa Maven que o projeto requer __hbase-client__ versão __1.1.2__. Em tempo de compilação, isso será baixado do repositório Maven padrão. Você pode usar a [Pesquisa de repositório Central Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) para saber mais sobre essa dependência.

    > [AZURE.IMPORTANT] O número da versão deve corresponder à versão do HBase fornecida com o seu cluster HDInsight. Use a tabela a seguir para encontrar o número de versão correta.

  	| Versão de cluster HDInsight | Versão do HBase usar |
  	| ----- | ----- |
  	| 3,2 | 0.98.4-hadoop2 |
  	| 3.3 e 3.4 | 1.1.2 |

    Para obter mais informações sobre versões de HDInsight e componentes, veja [quais são os diferentes componentes de Hadoop disponíveis com HDInsight](hdinsight-component-versioning.md).

2. Se você estiver usando um HDInsight 3.3 ou 3.4 cluster, você também deve adicionar o seguinte para o `<dependencies>` seção:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    Isso carregará os componentes rio-core, que são necessárias com Hbase versão 1.1.

2. Adicione o seguinte código para o arquivo de __pom.xml__ . Isso deve estar dentro do `<project>...</project>` marcas no arquivo, por exemplo, entre `</dependencies>` e `</project>`.

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

    Isso configura um recurso (__hbase/conf-site.xml__), que contém informações de configuração para HBase.

    > [AZURE.NOTE] Você também pode definir valores de configuração por meio de código. Consulte os comentários no exemplo __CreateTable__ que segue sobre como fazer isso.

    Isso também configura o [Plug-in do compilador Maven](http://maven.apache.org/plugins/maven-compiler-plugin/) e [Maven sombrear plug-in](http://maven.apache.org/plugins/maven-shade-plugin/). O compilador plug-in é usado para compilar a topologia. O plug-in de sombreamento é usado para impedir a duplicação de licenças no pacote JAR que é desenvolvida por Maven. O motivo que é usado é que os arquivos de licença duplicadas causar um erro em tempo de execução no cluster HDInsight. Usando sombrear-plugin maven com o `ApacheLicenseResourceTransformer` implementação impede que esse erro.

    O maven-sombrear-plug-in também produz um jar de uber (ou jar fat,) que contém todas as dependências exigidas pelo aplicativo.

3. Salve o arquivo de __pom.xml__ .

4. Crie um novo diretório chamado __conf__ no diretório __hbaseapp__ . Isso será usado para armazenar informações de configuração para conexão com HBase.

5. Use o seguinte comando para copiar a configuração de HBase do servidor HDInsight no diretório de __conf__ . Substituir o **nome de usuário** o o nome do seu login SSH. Substitua o nome do seu cluster de HDInsight **CLUSTERNAME** :

        scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml

    > [AZURE.NOTE] Se você usou uma senha para a sua conta SSH, você será solicitado a digitar a senha. Se você usou uma chave SSH com a conta, você pode precisar usar o `-i` parâmetro para especificar o caminho para o arquivo de chave. O exemplo a seguir carregará a chave privada da `~/.ssh/id_rsa`:
    >
    > `scp -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml`

##<a name="create-the-application"></a>Criar o aplicativo

1. Vá para o diretório __hbaseapp/src/principal/java/com/microsoft/exemplos__ e renomeie o arquivo app.java para __CreateTable.java__.

2. Abra o arquivo __CreateTable.java__ e substitua o conteúdo existente com o seguinte:

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
            //
            //NOTE: Actual zookeeper host names can be found using Ambari:
            //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"
            
            //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
            config.set("zookeeper.znode.parent","/hbase-unsecure");

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

4. No diretório __hbaseapp/src/principal/java/com/microsoft/exemplos__ , crie um novo arquivo chamado __SearchByEmail.java__. Use o seguinte como o conteúdo deste arquivo:

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

6. No diretório __hbaseapp/src/principal/hava/com/microsoft/exemplos__ , crie um novo arquivo chamado __DeleteTable.java__. Use o seguinte como o conteúdo deste arquivo:

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

2. Do diretório __hbaseapp__ , use o seguinte comando para criar um arquivo JAR que contém o aplicativo:

        mvn clean package

    Isso limpa qualquer artefatos de compilação anterior, downloads quaisquer dependências que ainda não tiver sido instaladas, em seguida, cria e pacotes do aplicativo.

3. Quando o comando for concluído, o diretório de __hbaseapp/destino__ conterá um arquivo denominado __hbaseapp-1.0-SNAPSHOT.jar__.

    > [AZURE.NOTE] O arquivo __hbaseapp-1.0-SNAPSHOT.jar__ é um uber jar (às vezes chamado de um jar fat,) que contém todas as dependências necessárias para executar o aplicativo.

##<a name="upload-the-jar-file-and-run-jobs"></a>Carregue o arquivo JAR e executar trabalhos

1. Use o seguinte para carregar o jar ao cluster HDInsight. Substituir o **nome de usuário** o o nome do seu login SSH. Substitua o nome do seu cluster de HDInsight **CLUSTERNAME** :

        scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Isso carregará o arquivo para o diretório inicial para sua conta de usuário SSH.

    > [AZURE.NOTE] Se você usou uma senha para a sua conta SSH, você será solicitado a digitar a senha. Se você usou uma chave SSH com a conta, você pode precisar usar o `-i` parâmetro para especificar o caminho para o arquivo de chave. O exemplo a seguir carregará a chave privada da `~/.ssh/id_rsa`:
    >
    > `scp -i ~/.ssh/id_rsa ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

2. Use SSH para se conectar ao cluster HDInsight. Substituir o **nome de usuário** o o nome do seu login SSH. Substitua o nome do seu cluster de HDInsight **CLUSTERNAME** :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [AZURE.NOTE] Se você usou uma senha para a sua conta SSH, você será solicitado a digitar a senha. Se você usou uma chave SSH com a conta, você pode precisar usar o `-i` parâmetro para especificar o caminho para o arquivo de chave. O exemplo a seguir carregará a chave privada da `~/.ssh/id_rsa`:
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. Uma vez conectado, use o seguinte para criar uma nova tabela HBase usando o aplicativo de Java:

        hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable

    Isso cria uma nova tabela HBase denominada __pessoas__e preenchê-lo com dados.

4. Em seguida, use o seguinte para procurar endereços de email armazenados na tabela:

        hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com

    Você deve receber os seguintes resultados:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

##<a name="delete-the-table"></a>Excluir a tabela

Quando terminar com o exemplo, use o seguinte comando da sessão do PowerShell do Azure para excluir a tabela de __pessoas__ usada neste exemplo:

    hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable

