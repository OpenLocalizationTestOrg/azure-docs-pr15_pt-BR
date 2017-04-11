<properties
    pageTitle="Azure AD Java Introdução | Microsoft Azure"
    description="Como criar um aplicativo de linha de comando Java que entrarem nele usuários acessem uma API."
    services="active-directory"
    documentationCenter="java"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
  ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>


# <a name="using-java-command-line-app-to-access-an-api-with-azure-ad"></a>Usando o aplicativo de linha de comando do Java para acessar uma API com o Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD torna simples e direto para terceirização de gerenciamento de identidades do web app, fornecendo única entrada e saída com apenas algumas linhas de código.  Em aplicativos da web de Java, você pode fazer isso usando a implementação do ADAL4J voltado para a comunidade da Microsoft.

  Aqui, usaremos ADAL4J para:
- Entrar do usuário no aplicativo usando o Azure AD como provedor de identidade.
- Exiba algumas informações sobre o usuário.
- Assine o usuário sair do aplicativo.

Para fazer isso, você precisará:

1. Registrar um aplicativo com o Azure AD
2. Configure seu aplicativo para usar a biblioteca de ADAL4J.
3. Use a biblioteca de ADAL4J para emitir solicitações de entrada e saídas para Azure AD.
4. Imprima dados sobre o usuário.

Para começar a, [Baixar a estrutura do aplicativo](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip) ou [baixar o exemplo concluído](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\/archive/complete.zip).  Você também será preciso um locatário do Azure AD no qual deseja registrar seu aplicativo.  Se você não tenha um, [Saiba como obter uma](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>1. registrar um aplicativo com o Azure AD
Para ativar seu aplicativo autenticar os usuários, você precisará primeiro registrar um novo aplicativo no seu locatário.

- Entrar no Portal de gerenciamento do Azure.
- No painel de navegação à esquerda, clique no **Active Directory**.
- Selecione o locatário onde você deseja registrar o aplicativo.
- Clique na guia **aplicativos** e clique em Adicionar na parte inferior registradora.
- Siga os avisos e criar um novo **aplicativo Web e/ou WebAPI**.
    - O **nome** do aplicativo descreverá seu aplicativo para usuários finais
    - A **URL de logon** é a URL base do aplicativo.  Padrão de esqueleto é `http://localhost:8080/adal4jsample/`.
    - O **URI de ID de aplicativo** é um identificador exclusivo para o seu aplicativo.  A convenção é usar `https://<tenant-domain>/<app-name>`, por exemplo,`http://localhost:8080/adal4jsample/`
- Após concluir registro, AAD atribuirá seu aplicativo um identificador de cliente exclusivo.  Você precisará desse valor nas próximas seções, portanto copiá-lo na guia Configurar.

Uma vez no portal para seu aplicativo crie um **Segredo do aplicativo** para o seu aplicativo e copiá-lo para baixo.  Você precisará dele em breve.


## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisites-using-maven"></a>2. configurar o seu aplicativo para usar a biblioteca de ADAL4J e pré-requisitos usando Maven
Aqui, estamos vai configurar ADAL4J para usar o protocolo de autenticação OpenID se conectar.  ADAL4J será usado para emitir solicitações de entrada e saídas, gerenciar a sessão do usuário e obter informações sobre o usuário, entre outras coisas.

-   No diretório raiz do seu projeto, abrir/criar `pom.xml` e localize o `// TODO: provide dependencies for Maven` e substitua a seguir:

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>public-client-adal4j-sample</artifactId>
    <packaging>jar</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>public-client-adal4j-sample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.nimbusds</groupId>
            <artifactId>oauth2-oidc-sdk</artifactId>
            <version>4.5</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20090211</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>
</dependencies>
    <build>
        <finalName>public-client-adal4j-sample</finalName>
        <plugins>
                <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>install</id>
                        <phase>install</phase>
                        <goals>
                            <goal>sources</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
        </plugins>
    </build>

</project>


```



## <a name="3-create-the-java-publicclient-file"></a>3. criar o arquivo de PublicClient java

Como indicado acima, usaremos a API do gráfico para obter dados sobre o usuário conectado. Para isso ser fácil para nós podemos deve criar um arquivo para representar um **Objeto de diretório** e um arquivo individual para representar o **usuário** para que o padrão OO de Java pode ser usado.

1. Crie um arquivo denominado `DirectoryObject.java` que usaremos para armazenar dados básicos sobre qualquer DirectoryObject (você pode fique à vontade para usar esta posteriormente para qualquer consultas de gráfico que você pode fazer). Você pode copiar/colar isso abaixo:

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


##<a name="compile-and-run-the-sample"></a>Compilar e executar o exemplo

Alterar novamente para seu diretório raiz e execute o seguinte comando para criar o exemplo você colocar apenas em conjunto usando `maven`. Isso usará o `pom.xml` arquivo que você escreveu para dependências.

`$ mvn package`

Agora você deve ter uma `adal4jsample.war` de arquivo no seu `/targets` diretório. Você pode implantar que em seu contêiner Tomcat e visite a URL 

`http://localhost:8080/adal4jsample/`


> [AZURE.NOTE] 
É muito fácil implantar uma guerra com os servidores Tomcat mais recentes. Basta navegar até `http://localhost:8080/manager/` e siga as instruções sobre como carregar seu ' adal4jsample.war' arquivo. Ele será deployment automático para você com o ponto de extremidade correto.

##<a name="next-steps"></a>Próximas etapas

Parabéns! Agora você tem um aplicativo Java que tem a capacidade de autenticar usuários, de forma segura em funcionamento chamar APIs Web usando OAuth 2.0 e obtenha informações básicas sobre o usuário.  Se você ainda não começou, agora é o tempo para preencher seu locatário com alguns usuários.

Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um. zip aqui](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

