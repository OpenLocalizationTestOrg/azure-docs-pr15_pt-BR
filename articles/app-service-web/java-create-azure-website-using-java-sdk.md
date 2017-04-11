<properties 
    pageTitle="Criar um aplicativo Web no serviço de aplicativo do Azure usando o SDK do Azure para Java" 
    description="Saiba como criar um aplicativo Web no serviço de aplicativo do Azure programaticamente usando o SDK do Azure para Java." 
    tags="azure-classic-portal"
    services="app-service\web" 
    documentationCenter="Java" 
    authors="donntrenton" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="02/25/2016" 
    ms.author="v-donntr"/>


# <a name="create-a-web-app-in-azure-app-service-using-the-azure-sdk-for-java"></a>Criar um aplicativo Web no serviço de aplicativo do Azure usando o SDK do Azure para Java

<!-- Azure Active Directory workflow is not yet available on the Azure Portal -->

## <a name="overview"></a>Visão geral

Este passo a passo mostra como criar um SDK do Azure para aplicativo Java que cria um aplicativo Web no [Serviço de aplicativo do Azure][]e implantar um aplicativo para ele. Ele consiste em duas partes:

- Parte 1 demonstra como criar um aplicativo Java que cria um aplicativo web.
- Parte 2 demonstra como criar um JSP simple "Olá, mundo" aplicativo, em seguida, usar um FTP cliente para implantar o código de serviço de aplicativo.


## <a name="prerequisites"></a>Pré-requisitos

### <a name="software-installations"></a>Instalações de software

O código do aplicativo AzureWebDemo neste artigo foi escrito usando Azure Java SDK 0.7.0, que você pode instalar usando o [Web Platform Installer][] (WebPI). Além disso, verifique se usar a versão mais recente do [Kit de ferramentas do Azure para Eclipse][]. Depois de instalar o SDK, atualizar as dependências no seu projeto Eclipse executando **Atualizar índice** em **Repositórios de Maven**e adicionar novamente a versão mais recente de cada pacote na janela **dependências** . Você pode verificar a versão do seu software instalado no Eclipse clicando **Ajuda > detalhes da instalação**; Você deve ter pelo menos as seguintes versões:

- Pacote para as bibliotecas do Microsoft Azure de Java 0.7.0.20150309
- Eclipse IDE para Java EE desenvolvedores 4.4.2.20150219


### <a name="create-and-configure-cloud-resources-in-azure"></a>Criar e configurar os recursos de nuvem no Azure

Antes de iniciar este procedimento, é necessário ter uma assinatura ativa do Azure e configurar um Active Directory (AD) no Azure padrão.


### <a name="create-an-active-directory-ad-in-azure"></a>Criar um Active Directory (AD) no Azure

Se você não tiver um Active Directory (AD) na sua assinatura do Azure, faça logon no [portal de clássico Azure][] com sua conta da Microsoft. Se você tiver várias assinaturas, clique em **assinaturas** e selecione a pasta padrão para a assinatura que deseja usar para esse projeto. Clique em **Aplicar** para alternar para esse modo de exibição de assinatura.

1. Selecione o **Active Directory** no menu à esquerda. **Clique em Novo > diretório > Criar personalizado**.

2. No **Diretório de adicionar**, selecione **Criar nova pasta**.

3. Em **nome**, digite um nome de pasta.

4. Em **domínio**, digite um nome de domínio. Este é um nome de domínio básico que está incluído por padrão com o diretório; ela tem a forma `<domain_name>.onmicrosoft.com`. Você pode nomeá-lo com base no nome do diretório ou outro nome de domínio que você possui. Posteriormente, você pode adicionar outro nome de domínio que sua organização já usa.

5. Em **país ou região**, selecione sua localidade.

Para saber mais sobre AD, consulte [o que é um diretório do Azure AD][]?


### <a name="create-a-management-certificate-for-azure"></a>Criar um certificado de gerenciamento do Azure

O SDK do Azure para Java usa certificados gerenciamento para autenticar com assinaturas do Azure. Estas são x. 509 v3 certificados que você usar para autenticar um aplicativo cliente que usa a API de gerenciamento de serviço para agir em nome do proprietário da assinatura para gerenciar recursos de assinatura.

O código neste procedimento usa um certificado auto-assinado para autenticar com o Azure. Neste procedimento, é necessário criar um certificado e carregue-o para o [portal de clássico Azure][] antecipadamente. Isso envolve as seguintes etapas:

- Gerar um arquivo PFX representando seu certificado de cliente e salvá-lo localmente.
- Gere um certificado de gerenciamento (arquivo CER) a partir do arquivo PFX.
- Carregue o arquivo CER à sua assinatura do Azure.
- Converta o arquivo PFX em JKS, pois Java usa esse formato para autenticar usando certificados.
- Escreva código de autenticação do aplicativo, que se refere ao arquivo JKS local.

Quando você concluir esse procedimento, o certificado CER residirão em sua assinatura do Azure e o certificado JKS residirão na sua unidade local. Para obter mais informações sobre certificados de gerenciamento, consulte [criar e carregar um certificado de gerenciamento do Azure][].


#### <a name="create-a-certificate"></a>Criar um certificado

Para criar seu próprio certificado auto-assinado, abra um console de comando no seu sistema operacional e execute os seguintes comandos.

> **Observação:**  O computador em que você executar este comando deve ter o JDK instalado. Além disso, o caminho para o keytool depende o local em que você instale o JDK. Para obter mais informações, consulte [ferramenta de gerenciamento de certificado (keytool) e chave][] nos documentos onlinehttps Java.

Para criar o arquivo. pfx:

    <java-install-dir>/bin/keytool -genkey -alias <keystore-id>
     -keystore <cert-store-dir>/<cert-file-name>.pfx -storepass <password>
     -validity 3650 -keyalg RSA -keysize 2048 -storetype pkcs12
     -dname "CN=Self Signed Certificate 20141118170652"

Para criar o arquivo. cer:

    <java-install-dir>/bin/keytool -export -alias <keystore-id>
     -storetype pkcs12 -keystore <cert-store-dir>/<cert-file-name>.pfx
     -storepass <password> -rfc -file <cert-store-dir>/<cert-file-name>.cer

onde:

- `<java-install-dir>`é o caminho para o diretório em que você instalou Java.
- `<keystore-id>`é o identificador de entrada do repositório de chaves (por exemplo, `AzureRemoteAccess`).
- `<cert-store-dir>`é o caminho para a pasta na qual você deseja armazenar certificados (por exemplo `C:/Certificates`).
- `<cert-file-name>`é o nome do arquivo de certificado (por exemplo `AzureWebDemoCert`).
- `<password>`é a senha que você optar por proteger o certificado; ele deve ter pelo menos 6 caracteres. Você não pode digitar nenhuma senha, embora isso não é recomendado.
- `<dname>`é o nome diferenciado x. 500 ser associadas a alias e é usado como os campos emissor e assunto no certificado auto-assinado.

Para obter mais informações, consulte [criar e carregar um certificado de gerenciamento do Azure][].


#### <a name="upload-the-certificate"></a>Carregar o certificado

Para carregar um certificado autoassinado no Azure, vá para a página de **configurações** no portal do clássico e clique na guia **Gerenciamento de certificados** . Clique em **carregar** na parte inferior da página e navegue até o local do arquivo CER que você criou.


#### <a name="convert-the-pfx-file-into-jks"></a>Converter o arquivo PFX JKS

No Prompt de comando do Windows (executando como administrador), cd para a pasta que contém os certificados e execute o seguinte comando, onde `<java-install-dir>` é o diretório em que você instalou Java em seu computador:

    <java-install-dir>/bin/keytool.exe -importkeystore
     -srckeystore <cert-store-dir>/<cert-file-name>.pfx
     -destkeystore <cert-store-dir>/<cert-file-name>.jks
     -srcstoretype pkcs12 -deststoretype JKS

1. Quando solicitado, digite a senha de repositório de chaves de destino; Esta será a senha para o arquivo JKS.

2. Quando solicitado, digite a senha de repositório de chaves de origem; Esta é a senha que você especificou para o arquivo PFX.

As duas senhas não tem o mesmo. Você não pode digitar nenhuma senha, embora isso não é recomendado.


## <a name="build-a-web-app-creation-application"></a>Criar um aplicativo de criação do aplicativo Web

### <a name="create-the-eclipse-workspace-and-maven-project"></a>Criar o espaço de trabalho do Eclipse e projeto Maven

Nesta seção, você cria um espaço de trabalho e um projeto Maven para o aplicativo de criação do aplicativo web, chamado AzureWebDemo.

1. Crie um novo projeto Maven. Clique em **arquivo > Novo > projeto Maven**. No **Novo projeto Maven**, selecione **criar um projeto simple** e **local de espaço de trabalho do padrão de uso**.

2. Na segunda página do **Novo projeto Maven**, especifique o seguinte:

    - ID do grupo:`com.<username>.azure.webdemo`
    - ID de artefato: AzureWebDemo
    - Versão: 0.0.1-SNAPSHOT
    - Embalagem: jar
    - Nome: AzureWebDemo

    Clique em **Concluir**.

3. Abra o novo arquivo do projeto pom.xml no Explorador de projeto. Selecione a guia **dependências** . Este é um novo projeto, não há pacotes estão listados ainda.

4. Abra o modo de exibição de repositórios de Maven. **Clique em Janela > Mostrar modo de exibição > outros > Maven > Maven repositórios** e clique em **Okey**. O modo de exibição de **Repositórios de Maven** aparecerá na parte inferior do IDE.

5. Abrir **Repositórios globais**, o repositório **central** de atalho e selecione **Recriar o índice**.

    ![][1]
    
    Esta etapa pode levar alguns minutos, dependendo da velocidade da sua conexão. Quando recria o índice, você deve ver os pacotes do Microsoft Azure no repositório Maven **central** .

6. Em **dependências**, clique em **Adicionar**. No **Grupo de Enter identificação..** insira `azure-management`. Selecione os pacotes para gerenciamento de base e gerenciamento de aplicativo de serviço Web Apps:

        com.microsoft.azure  azure-management
        com.microsoft.azure  azure-management-websites

    > **Observação:** Se você estiver atualizando as dependências após uma nova versão de versão, você precisa adicionar cada uma das dependências nesta lista novamente.
    > Depois clique em **Adicionar** e selecione cada dependência, ele aparece com o novo número de versão na lista **dependências** .

Clique em **Okey**. Os pacotes Azure aparecem na lista **dependências** .


### <a name="writing-java-code-to-create-a-web-app-by-calling-the-azure-sdk"></a>Escrever código de Java para criar um aplicativo Web chamando o SDK do Azure

Em seguida, escreva o código que chama APIs no SDK do Azure para Java para criar o aplicativo de serviço web app.

1. Crie uma classe Java para conter o código de ponto de entrada principal. No Explorador de projeto, clique com botão direito no nó do projeto e selecione **Novo > classe**.

2. Na **Nova classe Java**, nomeie a classe `WebCreator` e marque a caixa de seleção **público principal anular estático** . As seleções devem aparecer da seguinte maneira:

    ![][2]

3. Clique em **Concluir**. O arquivo de WebCreator.java aparece no Explorador de projeto.


### <a name="calling-the-azure-api-to-create-an-app-service-web-app"></a>Chamando a API do Azure para criar um aplicativo de serviço Web App


#### <a name="add-necessary-imports"></a>Adicionar importações necessárias

No WebCreator.java, adicione as seguintes importações; Estas importações fornecem acesso a classes nas bibliotecas de gerenciamento para consumir Azure APIs:

    // General imports
    import java.net.URI;
    import java.util.ArrayList;
    
    // Imports for Exceptions
    import java.io.IOException;
    import java.net.URISyntaxException;
    import javax.xml.parsers.ParserConfigurationException;
    import com.microsoft.windowsazure.exception.ServiceException;
    import org.xml.sax.SAXException;
    
    // Imports for Azure App Service management configuration
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.management.configuration.ManagementConfiguration;
    
    // Service management imports for App Service Web Apps creation
    import com.microsoft.windowsazure.management.websites.*;
    import com.microsoft.windowsazure.management.websites.models.*;
    
    // Imports for authentication
    import com.microsoft.windowsazure.core.utils.KeyStoreType;


#### <a name="define-the-main-entry-point-class"></a>Definir a classe de ponto de entrada principal

Como a finalidade do aplicativo AzureWebDemo é criar um aplicativo de serviço Web App, nomeie a classe principal para este aplicativo `WebAppCreator`. Essa classe fornece o código de ponto de entrada principal que chama a API de gerenciamento de serviço do Azure para criar o aplicativo web.

Adicione as seguintes definições de parâmetro para o aplicativo da web e webspace. Você precisará fornecer suas próprias informações de identificação e o certificado de assinatura Azure.

    public class WebAppCreator {
    
        // Parameter definitions used for authentication.
        private static String uri = "https://management.core.windows.net/";
        private static String subscriptionId = "<subscription-id>";
        private static String keyStoreLocation = "<certificate-store-path>";
        private static String keyStorePassword = "<certificate-password>";
    
        // Define web app parameter values.
        private static String webAppName = "WebDemoWebApp";
        private static String domainName = ".azurewebsites.net";
        private static String webSpaceName = WebSpaceNames.WESTUSWEBSPACE;
        private static String appServicePlanName = "WebDemoAppServicePlan";

onde:

- `<subscription-id>`é a ID do Azure assinatura na qual você deseja criar o recurso.
- `<certificate-store-path>`é o caminho e o nome do arquivo para o arquivo JKS no seu diretório de repositório de certificados local. Por exemplo, `C:/Certificates/CertificateName.jks` para Linux e `C:\Certificates\CertificateName.jks` para Windows.
- `<certificate-password>`é a senha que você especificou quando você criou seu certificado JKS.
- `webAppName`pode ser qualquer nome que você escolher; Este procedimento usa o nome `WebDemoWebApp`. O nome de domínio completo é o `webAppName` com a `domainName` acrescentado, portanto nesse caso o domínio completo é `webdemowebapp.azurewebsites.net`.
- `domainName`deve ser especificado como mostrado acima.
- `webSpaceName`deve ser um dos valores definidos na classe [WebSpaceNames][] .
- `appServicePlanName`deve ser especificado como mostrado acima.

> **Observação:** Sempre que você executa esse aplicativo, você precisa alterar o valor de `webAppName` e `appServicePlanName` (ou excluir o aplicativo da web no Portal do Azure) antes de executar o aplicativo novamente. Caso contrário, a execução falhará porque o mesmo recurso já existe no Azure.


#### <a name="define-the-web-creation-method"></a>Definir o método de criação da web

Em seguida, defina um método para criar o aplicativo web. Esse método, `createWebApp`, especifica os parâmetros do web app e o webspace. Ele também cria e configura o cliente de gerenciamento de aplicativo de serviço Web Apps, que é definido pelo objeto [WebSiteManagementClient][] . O cliente de gerenciamento é chave para criar aplicativos Web. Ele fornece serviços web RESTful que permitem que aplicativos gerenciar aplicativos web (executar operações como criar, atualizar e excluir) chamando a API de gerenciamento do serviço.

    private static void createWebApp() throws Exception {

        // Specify configuration settings for the App Service management client.
        Configuration config = ManagementConfiguration.configure(
            new URI(uri),
            subscriptionId,
            keyStoreLocation,  // Path to the JKS file
            keyStorePassword,  // Password for the JKS file
            KeyStoreType.jks   // Flag that you are using a JKS keystore
        );

        // Create the App Service Web Apps management client to call Azure APIs
        // and pass it the App Service management configuration object.
        WebSiteManagementClient webAppManagementClient = WebSiteManagementService.create(config);

        // Create an App Service plan for the web app with the specified parameters.
        WebHostingPlanCreateParameters appServicePlanParams = new WebHostingPlanCreateParameters();
        appServicePlanParams.setName(appServicePlanName);
        appServicePlanParams.setSKU(SkuOptions.Free);
        webAppManagementClient.getWebHostingPlansOperations().create(webSpaceName, appServicePlanParams);

        // Set webspace parameters.
        WebSiteCreateParameters.WebSpaceDetails webSpaceDetails = new WebSiteCreateParameters.WebSpaceDetails();
        webSpaceDetails.setGeoRegion(GeoRegionNames.WESTUS);
        webSpaceDetails.setPlan(WebSpacePlanNames.VIRTUALDEDICATEDPLAN);
        webSpaceDetails.setName(webSpaceName);

        // Set web app parameters.
        // Note that the server farm name takes the Azure App Service plan name.
        WebSiteCreateParameters webAppCreateParameters = new WebSiteCreateParameters();
        webAppCreateParameters.setName(webAppName);
        webAppCreateParameters.setServerFarm(appServicePlanName);
        webAppCreateParameters.setWebSpace(webSpaceDetails);

        // Set usage metrics attributes.
        WebSiteGetUsageMetricsResponse.UsageMetric usageMetric = new WebSiteGetUsageMetricsResponse.UsageMetric();
        usageMetric.setSiteMode(WebSiteMode.Basic);
        usageMetric.setComputeMode(WebSiteComputeMode.Shared);

        // Define the web app object.
        ArrayList<String> fullWebAppName = new ArrayList<String>();
        fullWebAppName.add(webAppName + domainName);
        WebSite webApp = new WebSite();
        webApp.setHostNames(fullWebAppName);

        // Create the web app.
        WebSiteCreateResponse webAppCreateResponse = webAppManagementClient.getWebSitesOperations().create(webSpaceName, webAppCreateParameters);

        // Output the HTTP status code of the response; 200 indicates the request succeeded; 4xx indicates failure.
        System.out.println("----------");
        System.out.println("Web app created - HTTP response " + webAppCreateResponse.getStatusCode() + "\n");

        // Output the name of the web app that this application created.
        String shinyNewWebAppName = webAppCreateResponse.getWebSite().getName();
        System.out.println("----------\n");
        System.out.println("Name of web app created: " + shinyNewWebAppName + "\n");
        System.out.println("----------\n");
    }

O código será o status HTTP da resposta indicando sucesso ou falha de saída e se for bem-sucedido, produzirá o nome do aplicativo web criado.


#### <a name="define-the-main-method"></a>Definir o método Main)

Fornece o código do método Main () que chama createWebApp() para criar o aplicativo web.

Finalmente, chame `createWebApp` de `main`:

        public static void main(String[] args)
            throws IOException, URISyntaxException, ServiceException,
            ParserConfigurationException, SAXException, Exception {

            // Create web app
            createWebApp();

        }  // end of main()

    }  // end of WebAppCreator class


#### <a name="run-the-application-and-verify-web-app-creation"></a>Execute o aplicativo e verificar a criação do aplicativo web

Para verificar que o aplicativo é executado, clique **Executar > Executar**. Quando o aplicativo for concluído a execução, você verá a seguinte saída no console do Eclipse:

    ----------
    Web app created - HTTP response 200
    
    ----------
    
    Name of web app created: WebDemoWebApp
    
    ----------

Faça logon no portal de clássico do Azure e clique em **Aplicativos Web**. O novo aplicativo web deve aparecer na lista de aplicativos Web em poucos minutos.


## <a name="deploying-an-application-to-the-web-app"></a>Implantar um aplicativo para o aplicativo Web

Depois de executar AzureWebDemo e criado o novo aplicativo web, logon no portal clássico, clique em **Aplicativos Web**e selecione **WebDemoWebApp** na lista de **Aplicativos Web** . Na página de painel do aplicativo da web, clique em **Procurar** (ou clique em da URL, `webdemowebapp.azurewebsites.net`) para navegar até ele. Você verá uma página de espaço reservado em branco, pois nenhum conteúdo foi publicado o web app ainda.

Em seguida você criará um aplicativo "Hello World" e implantá-lo para o aplicativo web.


### <a name="create-a-jsp-hello-world-application"></a>Criar um aplicativo JSP Olá, mundo

#### <a name="create-the-application"></a>Criar o aplicativo

Para demonstrar como implantar um aplicativo na Web, o procedimento a seguir mostra como criar um aplicativo "Olá, mundo" Java simples e carregue-o para o aplicativo de serviço Web App criados pelo seu aplicativo.

1. Clique em **arquivo > Novo > projeto Web dinâmico**. Nomeie- `JSPHello`. Você não precisa alterar quaisquer outras configurações nesta caixa de diálogo. Clique em **Concluir**.

    ![][3]

2. No Explorador de projeto, expanda o projeto **JSPHello** , **conteúdo da Web**de atalho, clique em **Novo > arquivo JSP**. Na caixa de diálogo Novo arquivo JSP, nomeie o novo arquivo `index.jsp`. Clique em **Avançar**.

3. Na caixa de diálogo **Selecionar JSP modelo** , selecione **Novo arquivo JSP (html)** e clique em **Concluir**.

4. No JSP, adicione o seguinte código no `<head>` e `<body>` marcar seções:

        <head>
          ...
          java.util.Date date = new java.util.Date();
        </head>
    
        <body>
          Hello, the time is <%= date %> 
        </body>


#### <a name="run-the-hello-world-application-in-localhost"></a>Executar o aplicativo Hello World no host local

Antes de executar este aplicativo, você precisa configurar algumas propriedades.

1. Clique com botão direito no projeto **JSPHello** e selecione **Propriedades**.

2. Na caixa de diálogo **Propriedades** : selecione **Java construir caminho**, selecione a guia **ordem e exportar** , marque **JRE sistema biblioteca**e clique em **para cima** para movê-la para a parte superior da lista.

    ![][4]

3. Também na caixa de diálogo **Propriedades** : selecione **Direcionadas tempos de execução** e clique em **novo**.

4. Na caixa de diálogo **Novo ambiente de tempo de execução do servidor** , selecione um servidor como **v 7.0 Apache Tomcat** e clique em **Avançar**. Na caixa de diálogo **Servidor Tomcat** , defina o **nome** `Apache Tomcat v7.0`e configurar **O diretório de instalação do Tomcat** no diretório em que você instalou a versão do servidor Tomcat que você deseja usar.

    ![][5]

    Clique em **Concluir**.

5. Você retorne para a página de **Destino tempos de execução** da caixa de diálogo **Propriedades** . Selecione **v 7.0 Apache Tomcat**, clique em **Okey**.

    ![][6]

6. No menu Eclipse **Executar** , clique em **Executar**. Na caixa de diálogo **Executar como** , selecione **executar no servidor**. Na caixa de diálogo **executar no servidor** , selecione **Tomcat v 7.0 Server**:

    ![][7]

    Clique em **Concluir**.

7. Quando o aplicativo é executado, você verá a página **JSPHello** aparecem em uma janela de host local no Eclipse (`http://localhost:8080/JSPHello/`), exibindo a seguinte mensagem:

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### <a name="export-the-application-as-a-war"></a>Exportar o aplicativo como uma guerra

Exporte os arquivos de projeto da web como um arquivo da web (guerra) para que você pode implantá-lo para o aplicativo web. Os seguintes arquivos de projeto de web residem na pasta de conteúdo da Web:

    META-INF
    WEB-INF
    index.jsp

1. Clique com botão direito na pasta de conteúdo da Web e selecione **Exportar**.

2. Na caixa de diálogo **Exportar selecionar** , clique em **Web > guerra** de arquivo, em seguida, clique em **Avançar**.

3. Na caixa de diálogo **Exportar guerra** , selecione o diretório src no projeto atual e incluir o nome do arquivo guerra no final. Por exemplo:

    `<project-path>/JSPHello/src/JSPHello.war`

Para obter mais informações sobre como implantar arquivos de guerra, consulte [Adicionar um aplicativo Java para aplicativos do Azure aplicativo de serviço Web](web-sites-java-add-app.md).


### <a name="deploying-the-hello-world-application-using-ftp"></a>Implantando o aplicativo Hello World usando FTP

Selecione um cliente FTP de terceiros para publicar o aplicativo. Este procedimento descreve duas opções: o console Kudu integrado do Azure; e FileZilla, uma ferramenta popular com uma interface de usuário conveniente e gráfico.

> **Observação:** O Kit de ferramentas do Azure para Eclipse suporta implantação para contas de armazenamento e serviços de nuvem, mas não oferece suporte para implantação para aplicativos web. Você pode implantar para contas de armazenamento e serviços de nuvem usando um projeto de implantação do Azure, conforme descrito na [criação de um aplicativo Hello World Azure no Eclipse](http://msdn.microsoft.com/library/azure/hh690944.aspx), mas não para aplicativos web. Use outros métodos como FTP ou GitHub para transferir arquivos para seu aplicativo web.

> **Observação:** Não recomendamos o uso de FTP do prompt de comando do Windows (o linha de comando Utilitário FTP.EXE que acompanha o Windows). Clientes FTP que usam FTP ativo, como FTP.EXE, geralmente não funcionar em firewalls. FTP ativo Especifica um endereço de baseado em LAN interno, ao qual um servidor FTP provavelmente falhará se conectar.

Para obter mais informações sobre a implantação para um serviço de aplicativo web app usando FTP, consulte os tópicos a seguir:

- [Implantar usando um utilitário de FTP](web-sites-deploy.md)


#### <a name="set-up-deployment-credentials"></a>Configurar credenciais de implantação

Verifique se que você tiver executado o aplicativo de **AzureWebDemo** para criar um aplicativo web. Você irá transferir arquivos nesse local.

1. Faça logon no portal do clássico e clique em **Aplicativos Web**. Verifique se **que webdemowebapp** aparece na lista de aplicativos web e certifique-se de que ele seja executado. Clique em **WebDemoWebApp** para abrir sua página de **painel** .

2. Na página **painel** , em **Resumo rápido de geral**, clique em **configurar suas credenciais de implantação** (se você já tiver credenciais de implantação, lê **redefinir suas credenciais de implantação**).

    Credenciais de implantação são associadas uma conta da Microsoft. Você precisa especificar um nome de usuário e senha que você pode usar para implantar usando gito e FTP. Você pode usar essas credenciais para implantar qualquer aplicativo web todas as assinaturas Azure associada a sua conta da Microsoft. Fornecer credenciais de implantação gito e FTP na caixa de diálogo e registrar o nome de usuário e senha para uso futuro.


#### <a name="get-ftp-connection-information"></a>Obter informações de conexão de FTP

Para usar FTP para implantar arquivos do aplicativo para o aplicativo web recém-criado, você precisa obter informações de conexão. Há duas maneiras de obter informações de conexão. Uma maneira é visitar a página de **painel** do aplicativo da web; a outra maneira é baixar web aplicativo publicar perfil. O perfil de publicação é um arquivo XML que fornece informações como as credenciais de logon e o nome de host FTP para seus aplicativos da web em um serviço de aplicativo do Azure. Você pode usar esse nome de usuário e senha para implantar qualquer aplicativo web todas as assinaturas associados à conta Azure, não apenas este um.

Para obter informações de conexão de FTP da lâmina do aplicativo da web no [Portal do Azure][]:

1. Em **Essentials**, localize e copie o **nome do host FTP**. Este é um URI semelhante a `ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net`.

2. Em **Essentials**, localize e copie o **nome de usuário FTP/implantação**. Isso terá formulário *webappname\deployment-username*; Por exemplo `WebDemoWebApp\deployer77`.

Para obter informações de conexão de FTP do perfil publicar:

1. Em lâmina do aplicativo da web, clique em **obter publicar perfil**. Isso irá baixar um arquivo. publishsettings em sua unidade local.

2. Abra o arquivo. publishsettings em um editor de XML ou um editor de texto e localize o `<publishProfile>` elemento contendo `publishMethod="FTP"`. Ele deve parecer com o seguinte:

        <publishProfile
            profileName="WebDemoWebApp - FTP"
            publishMethod="FTP"
            publishUrl="ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net/site/wwwroot"
            ftpPassiveMode="True"
            userName="WebDemoWebApp\$WebDemoWebApp"
            userPWD="<deployment-password>"
            ...
        </publishProfile>

3. Observe que o web app `publishProfile` mapa de configurações para as configurações do Gerenciador de Site de FileZilla da seguinte forma:

- `publishUrl`é o mesmo **nome de host FTP**, o valor definido no **Host**.
- `publishMethod="FTP"`significa que você defina o **protocolo** **FTP - File Transfer Protocol**, e a **criptografia** usar **FTP simples**.
- `userName`e `userPWD` são chaves para os valores de senha especificado quando você redefinir as credenciais de implantação e o nome de usuário real. `userName`é a mesma que **implantação / FTP usuário**. Eles são mapeados para **usuário** e **senha** no FileZilla.
- `ftpPassiveMode="True"`significa que o site FTP usa transferência FTP passiva; Selecione **passivo** na guia **Configurações de transferência** .


#### <a name="configure-the-web-app-to-host-a-java-application"></a>Configurar o aplicativo Web para hospedar um aplicativo Java

Antes de publicar o aplicativo, você precisa alterar algumas configurações de configuração para que o aplicativo da web pode hospedar um aplicativo Java.

1. No portal do clássico, acesse a página de **painel** do aplicativo da web e clique em **Configurar**. Na página **Configurar** , especifique as configurações a seguir.

2. Na **versão de Java** padrão fica **desativado**; Selecione a versão de Java destinos seu aplicativo; Por exemplo, 1.7.0_51. Depois de fazer isso, também garantir que o **contêiner da Web** está definida para uma versão do servidor Tomcat.

3. Em **Documentos padrão**, adicione JSP e mova-o para cima até o topo da lista. (O arquivo padrão para aplicativos web é hostingstart.html.)

4. Clique em **Salvar**.


#### <a name="publish-your-application-using-kudu"></a>Publicar o aplicativo usando Kudu

Uma maneira para publicar o aplicativo é usar o console de depuração Kudu integrado do Azure. Kudu é conhecido por ser estável e consistente com o aplicativo de serviço Web Apps e servidor Tomcat. Você pode acessar o console para o aplicativo web navegando até uma URL do seguinte formato:

`https://<webappname>.scm.azurewebsites.net/DebugConsole`

1. Neste procedimento, o console de Kudu está localizado na seguinte URL; Navegue até o local:

    `https://webdemowebapp.scm.azurewebsites.net/DebugConsole`

2. No menu superior, selecione **Console depuração > CMD**.

3. Na linha de comando console, navegue até `/site/wwwroot` (ou clique em `site`, em seguida, `wwwroot` no modo de exibição de pasta na parte superior da página):

    `cd /site/wwwroot`

4. Depois que você especificar **versão Java**, servidor Tomcat deve criar um diretório de webapps. Na linha de comando console, navegue até o diretório webapps:

    `mkdir webapps`

    `cd webapps`

5. Arraste JSPHello.war de `<project-path>/JSPHello/src/` e solte-o para o modo de exibição de diretório Kudu em `/site/wwwroot/webapps`. Não arraste-a para a área "Arraste aqui para carregar e zip", pois Tomcat serão descompactados-lo.

  ![][8]

Na primeira JSPHello.war aparece na área de diretório por si só:

  ![][9]

Em uma hora abreviada (provavelmente menos de 5 minutos) servidor Tomcat serão descompactados o arquivo de guerra em um diretório de JSPHello descompactado. Clique na pasta raiz para ver se JSP foi descompactados e copiados lá. Em caso afirmativo, navegar de volta para a pasta webapps para ver se o diretório JSPHello descompactado foi criado. Se você não vir esses itens, aguarde e repita.

  ![][10]


#### <a name="publish-your-application-using-filezilla-optional"></a>Publicar o aplicativo usando FileZilla (opcional)

Outra ferramenta que você pode usar para publicar o aplicativo é FileZilla, um cliente FTP de terceiros popular com uma interface de usuário conveniente, gráfica. Você pode baixar e instalar FileZilla partir [http://filezilla-project.org/](http://filezilla-project.org/) se você ainda não tivê-lo. Para obter mais informações sobre como usar o cliente, consulte a [documentação de FileZilla](https://wiki.filezilla-project.org/Documentation) e essa entrada de blog em [clientes de FTP - parte 4: FileZilla](http://blogs.msdn.com/b/robert_mcmurray/archive/2008/12/17/ftp-clients-part-4-filezilla.aspx).

1. No FileZilla, clique em **arquivo > Gerenciador de Site**.
2. Na caixa de diálogo **Gerenciador de sites** , clique em **Novo Site**. Um novo site FTP em branco aparecerá na **Entrada selecione** solicitando que você forneça um nome. Neste procedimento, nomeie- `AzureWebDemo-FTP`.

    Na guia **Geral** , especifique as seguintes configurações:
    - **Host:** Insira o **Nome do Host de FTP** que você copiou do painel.
    - **Porta:** (Deixe em branco, como isso é uma transferência passivo e servidor determinará a porta usar.)
    - **Protocolo:** Protocolo de transferência de arquivo de FTP
    - **Criptografia:** Usar FTP simples
    - **Tipo de logon:** Normal
    - **Usuário:** Insira a implantação / FTP usuário que você copiou do painel. Este é o nome de usuário FTP completo, que tem o formulário *webappname\username*.
    - **Senha:** Insira a senha que você especificou quando você define as credenciais de implantação.

    Na guia **Configurações de transferência** , selecione **passivo**.

3. Clique em **Conectar**. Se for bem-sucedida, console do FileZilla exibirá uma `Status: Connected` problema e mensagem um `LIST` comando para listar conteúdo da pasta.

4. No painel de site **Local** , selecione a pasta de origem no qual o arquivo de JSPHello.war reside; o caminho será semelhante ao seguinte:

    `<project-path>/JSPHello/src/`

5. No painel de site **remoto** , selecione a pasta de destino. Você irá implantar o arquivo de guerra para o `webapps` diretório na raiz do aplicativo da web. Navegue até `/site/wwwroot`, clique com botão direito em `wwwroot`e selecione **criar diretório**. Nomeie o diretório `webapps` e insira o diretório.

6. Transferir JSPHello.war para `/site/wwwroot/webapps`. Selecione JSPHello.war na lista **Local** do arquivo, clique com botão direito nele e selecione **carregar**. Você deve ver-aparecem no `/site/wwwroot/webapps`.

7. Após copiar JSPHello.war ao diretório webapps, servidor Tomcat será automaticamente descompactar (Descompacte) os arquivos no arquivo guerra. Embora o servidor Tomcat começa descompactar quase imediatamente, poderá demorar um longo tempo (possivelmente horas) para os arquivos sejam exibidos no cliente de FTP.


#### <a name="run-the-hello-world-application-on-the-web-app"></a>Executar o aplicativo Hello World do aplicativo Web

1. Depois de ter carregado o arquivo de guerra e verificado que o servidor Tomcat criou um descompactados `JSPHello` diretório, navegue até `http://webdemowebapp.azurewebsites.net/JSPHello` para executar o aplicativo.

    > **Observação:** Se você clicar em **Procurar** a partir do portal clássico, você pode receber a página da Web padrão, dizendo "Este aplicativo da web de Java com base tiver sido criado com êxito." Talvez seja necessário atualizar a página da Web para exibir a saída de aplicativo em vez de página da Web padrão.

2. Quando o aplicativo é executado, você deverá ver uma página da web com o seguinte resultado:

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### <a name="clean-up-azure-resources"></a>Limpar recursos Azure

Esse procedimento cria um aplicativo de serviço web app. Você será cobrado para o recurso desde que ele existe. A menos que você planeja continuar a usar o aplicativo da web para teste ou desenvolvimento, você deve considerar interrompendo ou excluí-lo. Um aplicativo web que foi interrompido ainda provocará um encargo pequeno, mas você pode reiniciá-lo a qualquer momento. Excluir um aplicativo web apaga todos os dados que você carregou a ele.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

  [1]: ./media/java-create-azure-website-using-java-sdk/eclipse-maven-repositories-rebuild-index.png
  [2]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-java-class.png
  [3]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-dynamic-web-project.png
  [4]: ./media/java-create-azure-website-using-java-sdk/eclipse-java-build-path.png
  [5]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-tomcat-server.png
  [6]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-properties-page.png
  [7]: ./media/java-create-azure-website-using-java-sdk/eclipse-run-on-server.png
  [8]: ./media/java-create-azure-website-using-java-sdk/kudu-console-drag-drop.png
  [9]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-1.png
  [10]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-2.png
 

[Serviço de aplicativo do Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[Web Platform Installer]: http://go.microsoft.com/fwlink/?LinkID=252838
[Kit de ferramentas de Azure para Eclipse]: https://msdn.microsoft.com/library/azure/hh690946.aspx
[Azure portal clássico]: https://manage.windowsazure.com
[O que é um diretório do Azure AD]: http://technet.microsoft.com/library/jj573650.aspx
[Criar e carregar um certificado de gerenciamento do Azure]: ../cloud-services/cloud-services-certs-create.md
[Ferramenta de gerenciamento de certificado (keytool) e chave]: http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html
[WebSiteManagementClient]: http://azure.github.io/azure-sdk-for-java/com/microsoft/azure/management/websites/WebSiteManagementClient.html
[WebSpaceNames]: http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/models/WebSpaceNames.html
[Portal do Azure]: https://portal.azure.com
