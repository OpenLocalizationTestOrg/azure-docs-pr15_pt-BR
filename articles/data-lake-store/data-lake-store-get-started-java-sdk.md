<properties
   pageTitle="Usar dados Lucerne Store Java SDK para desenvolver aplicativos | Microsoft Azure"
   description="Usar o SDK do Azure dados Lucerne Store Java para desenvolver aplicativos"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/17/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-java"></a>Introdução ao armazenamento de Lucerne de dados do Azure usando Java

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node](data-lake-store-manage-use-nodejs.md)

Saiba como usar o SDK do Azure dados Lucerne Store Java para executar operações básicas, tais como criar pastas, carregar e baixar arquivos de dados, etc. Para saber mais sobre Lucerne de dados, consulte [Azure dados Lucerne Store](data-lake-store-overview.md).

Você pode acessar a documentos de API do SDK Java para armazenamento de Lucerne de dados do Azure em [documentos do Azure dados Lucerne Store Java API](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Pré-requisitos

* Java Development Kit (JDK 7 ou superior, usando Java versão 1.7 ou superior)
* Conta de armazenamento de Lucerne de dados do Azure. Siga as instruções contidas em [Introdução ao armazenamento de Lucerne de dados do Azure usando o Portal do Azure](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Este tutorial usa Maven para dependências build e project. Embora seja possível criar sem usar um sistema de compilação como Maven ou Gradle, verifique estes sistemas é muito mais fácil gerenciar dependências.
* (Opcional) E IDE como [IntelliJ IDEIA](https://www.jetbrains.com/idea/download/) ou [Eclipse](https://www.eclipse.org/downloads/) ou semelhantes.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como autenticam usando o Azure Active Directory?

Neste tutorial, usamos um segredo de cliente de aplicativo do Azure AD para recuperar um token do Azure Active Directory (serviço de autenticação). Usamos esse token para criar um objeto de cliente do armazenamento de Lucerne de dados para executar operações de arquivo e pasta de operações. Para obter instruções sobre como autenticar com armazenamento de Lucerne de dados do Azure usando o segredo de cliente, podemos executar as seguintes etapas de alto nível:

1. Criar um aplicativo web do Azure AD
2. Recupere a ID do cliente, segredo cliente e ponto de extremidade de token para o aplicativo da web de Azure AD.
3. Configure o acesso para o aplicativo da web de Azure AD na Data Lucerne Store arquivo/pasta que você deseja acessar do aplicativo Java que está sendo criada.

Para obter instruções sobre como executar essas etapas, consulte [criar um aplicativo do Active Directory](data-lake-store-authenticate-using-active-directory.md#create-an-active-directory-application).

Active Directory do Azure fornece outras opções, bem como para recuperar um token. Escolha de um número de mecanismos de autenticação diferentes de acordo com seu cenário, por exemplo, um aplicativo em execução em um navegador, um aplicativo distribuído como um aplicativo de área de trabalho ou um aplicativo de servidor em execução no local ou em uma máquina virtual Azure. Você também pode escolher entre diferentes tipos de credenciais, como senhas, certificados, autenticação de fator de 2, etc. Além disso, o Active Directory do Azure permite que você sincronizar seus usuários do Active Directory local com a nuvem. Para obter detalhes, consulte [Cenários de autenticação do Active Directory do Azure](../active-directory/active-directory-authentication-scenarios.md). 

## <a name="create-a-java-application"></a>Criar um aplicativo Java

O código de exemplo movimentações de [no GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) disponíveis você pelo processo de criação de arquivos no armazenamento, concatenando arquivos, download de um arquivo e excluindo alguns arquivos no repositório. Esta seção do artigo orientá-lo através das partes principais do código.

1. Crie um projeto Maven usando [arquétipo mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) na linha de comando ou usando um IDE. Para obter instruções sobre como criar um projeto de Java usando IntelliJ, consulte [aqui](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Para obter instruções sobre como criar um projeto usando o Eclipse, veja [aqui](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Adicione as seguintes dependências para o seu arquivo de **pom.xml** Maven. Adicionar o seguinte trecho do texto entre o ** \</version >** marca e o ** \</projeto >** marca:

        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.0.4-SNAPSHOT</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>

    A primeira dependência é usar o SDK do repositório de Lucerne dados (`azure-datalake-store`) do repositório maven. A segunda dependência (`slf4j-nop`) é especificar qual estrutura de log a ser usado para este aplicativo. O SDK do repositório de Lucerne dados usa fachada de log de [slf4j](http://www.slf4j.org/) , que permite que você escolha de um número de estruturas de log populares, como log4j, Java log, logback, etc., ou nenhum registro. Para este exemplo, podemos desativará o log, por isso, usaremos a ligação **slf4j-nop** . Para usar outras opções de log em seu aplicativo, consulte [aqui](http://www.slf4j.org/manual.html#projectDep).

### <a name="add-the-application-code"></a>Adicionar o código do aplicativo

Há três partes principais para o código.

1. Obter o token do Active Directory do Azure

2. Use o símbolo para criar um cliente de armazenamento de Lucerne de dados.

3. Use o cliente de armazenamento de Lucerne de dados para executar operações.

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>Etapa 1: Obter um token de Active Directory do Azure.

O SDK do repositório de Lucerne dados fornece métodos convenientes que permitem que você obtém os tokens de segurança necessários para conversar com a conta de armazenamento de Lucerne de dados. No entanto, o SDK não exigem que somente esses métodos seja usado. Você pode usar outros meios de obtenção de token também, como usando o [SDK do Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java)ou o seu próprio código personalizado.

Para usar o SDK do repositório de Lucerne dados para obter token para o aplicativo do Active Directory na Web que você criou anteriormente, use os métodos estáticos no `AzureADAuthenticator` classe. Substitua os valores reais para o aplicativo Web do Azure Active Directory **Preencher IN aqui** .

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AzureADToken token = AzureADAuthenticator.getTokenUsingClientCreds(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>Etapa 2: Criar um objeto de cliente (ADLStoreClient) de armazenamento de Lucerne de dados do Azure

Criando um objeto de [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) requer que você especifique o nome da conta de armazenamento de Lucerne de dados e o token do Active Directory do Azure gerado na última etapa. Observe que o nome da conta de armazenamento de Lucerne de dados precisa ser um nome de domínio totalmente qualificado. Por exemplo, substitua **Preencher IN aqui** algo parecido com **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, token);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>Etapa 3: Usar o ADLStoreClient para executar operações de arquivo e pasta

O código a seguir contém trechos de algumas operações comuns. Você pode examinar o completo [docs dados Lucerne Store Java SDK API](https://azure.github.io/azure-data-lake-store-java/javadoc/) do objeto **ADLStoreClient** para ver outras operações.
 
Observe que os arquivos são ler a partir e escritos em usando fluxos de Java padrão. Isso significa que você pode camada qualquer dos fluxos Java sobre os fluxos de dados Lucerne Store para tirar proveito da funcionalidade de Java padrão (por exemplo, imprimir fluxos de saída formatada ou qualquer dos fluxos compactação ou criptografia para funcionalidade adicional na parte superior, etc.).

    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>Etapa 4: Criar e executar o aplicativo

1. Para executar dentro um IDE, localize e pressione o botão **Executar** . Para executar na Maven, use o [executivo: executivo](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).

2. Para produzir um jar autônomo que você pode executar a partir da linha de comando constrói o jar com todas as dependências incluídas, usando o [plug-in do Maven assembly](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). O pom.xml no [código de origem de exemplo no github](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) tem um exemplo de como fazer isso.


## <a name="next-steps"></a>Próximas etapas

- [Proteger os dados no repositório de Lucerne de dados](data-lake-store-secure-data.md)
- [Usar a análise de dados Azure Lucerne com dados Lucerne Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com dados Lucerne Store](data-lake-store-hdinsight-hadoop-use-portal.md)
