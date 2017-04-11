<properties
    pageTitle="Usando SCIM para habilitar o fornecimento automático de usuários e grupos do Active Directory do Azure para aplicativos | Microsoft Azure"
    description="Active Directory do Azure automaticamente podem provisionar usuários e grupos para qualquer loja de aplicativos ou identidade que é apoiado por um serviço da Web com a interface definida na especificação de protocolo SCIM"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="using-scim-to-enable-automatic-provisioning-of-users-and-groups-from-azure-active-directory-to-applications"></a>Usando SCIM para habilitar o fornecimento automático de usuários e grupos do Active Directory do Azure para aplicativos

##<a name="overview"></a>Visão geral

Active Directory do Azure automaticamente podem provisionar usuários e grupos para qualquer loja de aplicativos ou identidade que é apoiado por um serviço da Web com a interface definida na [especificação de protocolo SCIM 2.0](https://tools.ietf.org/html/draft-ietf-scim-api-19). Active Directory do Azure pode enviar solicitações para criar, modificar e excluir atribuídos usuários e grupos para esse serviço Web, que, em seguida, pode traduzir essas solicitações para operações ao armazenamento de identidade de destino. 

![][1]
*Figura: Provisionamento do Azure Active Directory para um armazenamento de identidades por meio de um serviço da Web*

Esse recurso pode ser usado em conjunto com o recurso de "[trazer seu próprio aplicativo](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)" no Azure AD para habilitar o logon único e provisionamento de aplicativos que fornecem ou são apoiados por um serviço da web SCIM automático de usuário.

Há dois casos de uso para SCIM do Azure Active Directory:

* **Provisionar usuários e grupos para aplicativos que ofereçam suporte a SCIM** - aplicativos que suportam SCIM 2.0 e usar tokens de portador OAuth para autenticação funcionarão com o Azure AD da caixa.

* **Criar sua própria solução de provisionamento para aplicativos que ofereçam suporte a outros provisionamento baseado em API** - para aplicativos não-SCIM, você pode criar um ponto de extremidade SCIM para traduzir entre Azure AD ponto de extremidade SCIM e tudo o que oferece suporte a API do aplicativo para provisionamento do usuário.  Para auxiliar no desenvolvimento de um ponto de extremidade SCIM, fornecemos bibliotecas CLI juntamente com exemplos de código que mostram como fornecer um ponto de extremidade SCIM e traduzir mensagens SCIM.  

##<a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Usuários e grupos para aplicativos que suportam SCIM de provisionamento

Azure Active Directory pode ser configurado para automaticamente provisionar atribuído usuários e grupos para aplicativos que implementam um [sistema de gerenciamento de identidades entre domínios 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) da Web de serviço e aceitam tokens de portador OAuth para autenticação. Dentro da especificação de SCIM 2.0, aplicativos devem atender a estes requisitos:

* Suporta a criação de usuários e/ou em grupos, de acordo com as seção 3.3 do protocolo SCIM.  

* Suporta modificando usuários e/ou grupos com solicitações de patch de acordo com as seção 3.5.2 do protocolo SCIM.  

* Suporta recuperando um recurso conhecido de acordo com as seção 3.4.1 do protocolo SCIM.  

*  Suporta consultando usuários e/ou em grupos, de acordo com as seção 3.4.2 do protocolo SCIM.  Por padrão, os usuários serão consultados por externalId e grupos são consultados por displayName.  

* Suporta consultando usuário pela identificação e pelo Gerenciador de acordo com as seção 3.4.2 do protocolo SCIM.  

* Suporta consultando grupos por ID e por membro de acordo com as seção 3.4.2 do protocolo SCIM.  

* Aceita tokens de portador OAuth para autorização de acordo com as seção 2.1 do protocolo SCIM.

Você deve verificar com seu provedor de aplicativo ou a documentação do seu provedor de aplicativo para instruções de compatibilidade com esses requisitos.
 
###<a name="getting-started"></a>Guia de Introdução

Aplicativos que suportam o perfil SCIM descrito acima podem ser conectados ao Azure Active Directory usando o recurso de aplicativo "personalizado" na Galeria de aplicativo Azure AD. Uma vez conectado, o Azure AD executa um processo de sincronização cada 5 minutos onde ele consulta o ponto de extremidade do aplicativo SCIM para usuários atribuídos e grupos e cria ou modifica-los acordo com os detalhes da atribuição.

**Para conectar um aplicativo que ofereça suporte a SCIM:**

1.  Em um navegador da web, inicie o portal de gerenciamento Azure em https://manage.windowsazure.com.
2.  Navegue até **Active Directory > diretório > [seu diretório] > aplicativos**e selecione **Adicionar > Adicionar um aplicativo da Galeria**.
3.  Selecione a guia **personalizada** à esquerda, insira um nome para seu aplicativo e clique no ícone de marca de seleção para criar um objeto de aplicativo.

![][2]

4.  Na tela resultante, selecione o segundo botão de **provisionamento de contas de configurar** .
5.  No campo **URL de ponto de extremidade de provisionamento** , insira a URL do ponto de extremidade SCIM do aplicativo.
6.  Se o ponto de extremidade SCIM requer um token de portador OAuth de um emissor diferente Azure AD, copie o token de portador OAuth necessário para o campo de **Autenticação de Token (opcional)** . É este campo for deixado em branco, Azure AD incluirá um token de portador OAuth emitido do Azure AD com cada solicitação. Aplicativos que usam o Azure AD como um provedor de idenity pode validar este Azure AD-símbolo emitido.
7.  Clique em **Avançar**e clique no botão **Iniciar teste** ter Azure Active Directory tente se conectar com o ponto de extremidade SCIM. Se as tentativas falharem, informações de diagnóstico serão exibidas.  
8.  Se as tentativas de conexão para o sucesso do aplicativo, em seguida, clique em **Avançar** no restante telas e clique em **Concluir** para sair da caixa de diálogo.
9.  Na tela resultante, selecione o botão de **Atribuir contas** terceiro. Na seção usuários e grupos resultante, atribua usuários ou grupos que deseja provisionar para o aplicativo.
10. Depois que são atribuídos a usuários e grupos, clique na guia **Configurar** próximo à parte superior da tela.
11. Em **Provisionamento de contas**, confirme se o Status está definido como no. 
12. Em **Ferramentas**, clique em **Reiniciar provisionamento de contas** para inicie o processo de provisionamento.

Observe que 5 a 10 minutos pode devem transcorrer antes que o processo de provisionamento começará a enviar solicitações para o ponto de extremidade SCIM.  Um resumo de tentativas de conexão é fornecido na guia do painel de controle do aplicativo e um relatório de atividade de provisionamento e quaisquer erros de provisionamento podem ser baixados do guia de relatórios do diretório.

##<a name="building-your-own-provisioning-solution-for-any-application"></a>Criar sua própria solução de provisionamento para qualquer aplicativo

Criando um serviço da web SCIM que interage com o Active Directory do Azure, você pode habilitar o usuário automático e logon único provisionamento de praticamente qualquer aplicativo que fornece um usuário REST ou SOAP API de provisionamento.

Veja aqui como ele funciona:

1.  Azure AD fornece uma biblioteca de infraestrutura de linguagem comum denominada [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Os desenvolvedores e integradores podem usar esta biblioteca para criar e implantar uma empresa de serviço web baseados em SCIM capaz de se conectar Azure AD para armazenamento de identidade do qualquer aplicativo.
2.  Mapeamentos são implementados no serviço da web para mapear o esquema de usuário padronizado para o esquema de usuário e o protocolo exigidos pelo aplicativo.
3.  A URL do ponto de extremidade é registrada no Azure AD como parte de um aplicativo personalizado na Galeria de aplicativo.
4.  Usuários e grupos são atribuídos a este aplicativo no Azure AD. Durante a atribuição, eles são colocados em uma fila a serem sincronizados com o aplicativo de destino. O processo de sincronização manipulando fila é executado a cada 5 minutos.

###<a name="code-samples"></a>Exemplos de código

Para facilitar esse processo, um conjunto de [amostras de código](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) são fornecidas que criar um ponto de extremidade do serviço de web SCIM e demonstram provisionamento automático. É um exemplo de um provedor que mantém um arquivo com linhas de valores separados por vírgulas que representa a usuários e grupos.  A outra é de um provedor que funciona com o serviço de gerenciamento de acesso e identidade do Amazon Web Services.  

**Pré-requisitos**

* Visual Studio 2013 ou superior
* [SDK do Azure para .NET](https://azure.microsoft.com/downloads/)
* Computador Windows que ofereça suporte a estrutura do ASP.NET 4,5 a ser usado como o ponto de extremidade SCIM. Esta máquina deve ser acessível a partir da nuvem
* [Uma assinatura do Azure com uma versão de avaliação ou licenciada do Azure AD Premium](https://azure.microsoft.com/services/active-directory/)
* A amostra de Amazon AWS requer bibliotecas do [Kit de ferramentas de AWS para Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html). Consulte o arquivo Leiame inclusos no exemplo para obter detalhes adicionais

###<a name="getting-started"></a>Guia de Introdução

A maneira mais fácil para implementar um ponto de extremidade SCIM que pode aceitar solicitações de provisionamento do Azure AD é construir e implantar o código de exemplo que produz os usuários provisionados para um arquivo de valores separados por vírgula (CSV).

**Para criar um ponto de extremidade SCIM de amostra:**

1.  Baixe o pacote de amostra de código em [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2.  Descompactar o pacote e coloque-o no seu computador Windows em um local como C:\AzureAD-BYOA-Provisioning-Samples\.
3.  Nesta pasta, inicie a solução de FileProvisioningAgent no Visual Studio.
4.  Selecione **Ferramentas > Gerenciador de pacote de biblioteca > Package Manager Console**e executar os comandos abaixo do projeto FileProvisioningAgent para resolver as referências de solução:

    Pacote de instalação do pacote de instalação Microsoft.SystemForCrossDomainIdentityManagement pacote de instalação Microsoft.IdentityModel.Clients.ActiveDirectory pacote de instalação do Microsoft.Owin.Diagnostics Microsoft.Owin.Host.SystemWeb

5.  Crie o projeto FileProvisioningAgent.
6.  Iniciar o aplicativo do Prompt de comando do Windows (como administrador) e use o comando **cd** para alterar o diretório para a pasta **\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug** .
7.  Execute o comando abaixo, substituindo < endereço ip > com o nome de domínio ou IP da máquina Windows.

    FileAgnt.exe http://<ip-address>:9000 TargetFile.csv

8.  No Windows em **configurações do Windows > configurações de Internet & rede**, selecione o **Firewall do Windows > Configurações avançadas**e crie uma **Regra de entrada** que permita acesso de entrada à porta 9000.
9.  Se o computador Windows estiver atrás de um roteador, o roteador precisará ser configurado para executar a conversão de acesso de rede entre seu porta 9000 exposto à internet e porta 9000 na máquina Windows. Isso é necessário para o Azure AD poder acessar esse ponto de extremidade na nuvem.


**Para registrar o ponto de extremidade SCIM de amostra no Azure AD:**

1.  Em um navegador da web, inicie o portal de gerenciamento Azure em https://manage.windowsazure.com.
2.  Navegue até **Active Directory > diretório > [seu diretório] > aplicativos**e selecione **Adicionar > Adicionar um aplicativo da Galeria**.
3.  Selecione a guia **personalizada** à esquerda, insira um nome, como "SCIM o aplicativo de teste" e clique no ícone de marca de seleção para criar um objeto de aplicativo. Observe que o objeto de aplicativo criado é pretende representam o aplicativo de destino que você seria provisionamento e implementação single sign-on para e não apenas o ponto de extremidade SCIM.

![][2]

4.  Na tela resultante, selecione o segundo botão de **provisionamento de contas de configurar** .
5.  Na caixa de diálogo, digite a URL e porta do seu ponto de extremidade de SCIM expostas a internet. Isso seria algo como http://testmachine.contoso.com:9000 ou http://<ip-address>:9000/, onde < endereço ip > é a internet expostos IP endereço.  
6.  Clique em **Avançar**e clique no botão **Iniciar teste** ter Azure Active Directory tente se conectar com o ponto de extremidade SCIM. Se as tentativas falharem, informações de diagnóstico serão exibidas.  
7.  Se tiver êxito o tenta se conectar ao seu serviço da Web, clique em **Avançar** nas telas restantes e clique em **Concluir** para sair da caixa de diálogo.
8.  Na tela resultante, selecione o botão de **Atribuir contas** terceiro. Na seção usuários e grupos resultante, atribua usuários ou grupos que deseja provisionar para o aplicativo.
9.  Depois que são atribuídos a usuários e grupos, clique na guia **Configurar** próximo à parte superior da tela.
10. Em **Provisionamento de contas**, confirme se o Status está definido como no. 
11. Em **Ferramentas**, clique em **Reiniciar provisionamento de contas** para inicie o processo de provisionamento.

Observe que 5 a 10 minutos pode devem transcorrer antes que o processo de provisionamento começará a enviar solicitações para o ponto de extremidade SCIM.  Um resumo de tentativas de conexão é fornecido na guia do painel de controle do aplicativo e um relatório de atividade de provisionamento e quaisquer erros de provisionamento podem ser baixados do guia de relatórios do diretório.

A etapa final verificando a amostra é abrir o arquivo TargetFile.csv na pasta \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug no seu computador Windows. Depois que o processo de provisionamento é executado, esse arquivo mostra os detalhes de todos atribuídos e provisionado usuários e grupos.

###<a name="development-libraries"></a>Bibliotecas de desenvolvimento

Para desenvolver seu próprio serviço da Web em conformidade com a especificação de SCIM, primeiro você se familiarize com as seguintes bibliotecas fornecidas pela Microsoft para ajudar a acelerar o processo de desenvolvimento: 

**1:**  Bibliotecas de infraestrutura de linguagem comuns são oferecidas para uso com idiomas com base no que infraestrutura, como c#.  Uma dessas bibliotecas, [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), declara uma interface, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, mostrada na figura abaixo.  Um desenvolvedor usando as bibliotecas poderia implementar interface com uma classe que pode ser chamada, genericamente, como um provedor.  As bibliotecas permitem que o desenvolvedor facilmente implantar um serviço da Web que está em conformidade com a especificação de SCIM, seja hospedado dentro dos serviços de informações de Internet ou qualquer conjunto de Common Language Infrastructure executável.  Solicitações de serviço Web serão convertidas em chamadas para métodos do provedor, o que poderiam ser programados pelo desenvolvedor para operar em alguns armazenamento de identidade.    

![][3]

**2:** [Manipuladores de rotas Express](http://expressjs.com/guide/routing.html) estão disponíveis para analisar os objetos de solicitação de Node representando chamadas (conforme definido pela especificação SCIM), feitas em um serviço Web Node.     

###<a name="building-a-custom-scim-endpoint"></a>Criando um ponto de extremidade SCIM personalizado

Usando as bibliotecas descritas acima, os desenvolvedores usando essas bibliotecas podem hospedar seus serviços dentro qualquer assembly Common Language Infrastructure executável, ou em serviços de informações da Internet.  Aqui está o código de amostra para hospedar um serviço dentro de um assembly executável, no endereço http://localhost:9000: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  
    
    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

É importante observar que esse serviço deve ter um HTTP endereço e servidor de certificado de autenticação do qual autoridade de certificação raiz é um destes procedimentos: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Um certificado de autenticação do servidor pode ser vinculado a uma porta em um host do Windows usando o utilitário de shell de rede, assim: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  
 
Aqui, o valor fornecido para o argumento certhash é a impressão digital do certificado, enquanto o valor fornecido para o argumento appid é um identificador exclusivo globalmente aleatório.  

Para hospedar o serviço dentro dos serviços de informações de Internet, um desenvolvedor criaria um assembly de biblioteca de código Common Language Infrastructure com uma classe chamada inicialização no namespace padrão do assembly.  Aqui está um exemplo de tal classe: 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

###<a name="handling-endpoint-authentication"></a>Autenticação de ponto de extremidade de tratamento

Solicitações do Azure Active Directory incluem um token de portador OAuth 2.0.   Qualquer serviço recebe a solicitação deve autenticar o emissor como sendo Azure Active Directory em nome do locatário do Active Directory do Azure esperado, para obter acesso a serviço da Web de gráfico do Azure Active Directory.  O token, o emissor é identificado por uma declaração de iss, como, "iss": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  Neste exemplo, o endereço base do valor de declaração, https://sts.windows.net, identifica Azure Active Directory como o emissor, enquanto o segmento de endereço relativo, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, é um identificador exclusivo do locatário Azure Active Directory em nome do qual o token foi emitido.  Se o token foi emitido para acessar o serviço da Web de gráfico do Active Directory do Azure, então o identificador desse serviço, 00000002-0000-0000-c000-000000000000, deve ser no valor de declaração de aud do token.  

Desenvolvedores usando as bibliotecas de Common Language Infrastructure fornecidas pela Microsoft para a criação de um serviço SCIM podem autenticar solicitações do Azure Active Directory usando o pacote de Microsoft.Owin.Security.ActiveDirectory seguindo estas etapas: 

**1:**  Em um provedor, implemente a propriedade Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior fazendo com que ela retorne um método a ser chamado sempre que o serviço é iniciado: 

    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }

**2:**  Adicione o seguinte código para esse método para ter qualquer solicitação para qualquer um dos pontos de extremidade do serviço autenticados como que tenham um token emitido por Active Directory do Azure em nome de um locatário especificado, para obter acesso a serviço da Web de gráfico do Azure Active Directory: 

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }

##<a name="user-and-group-schema"></a>Esquema de grupo e de usuário

Active Directory do Azure podem provisionar dois tipos de recursos aos serviços da Web de SCIM.  Esses tipos de recursos são usuários e grupos.  

Recursos de usuário são identificados pelo identificador de esquema, urn: ietf:params:scim:schemas:extension:enterprise:2.0:User, que está incluído nesta especificação de protocolo: http://tools.ietf.org/html/draft-ietf-scim-core-schema.  O mapeamento de padrão de atributos de usuários no Active Directory do Azure nos atributos de recursos de urn: ietf:params:scim:schemas:extension:enterprise:2.0:User é fornecido na tabela 1, abaixo.  

Agrupar recursos são identificados pelo identificador de esquema, http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  Tabela 2, abaixo, mostra o mapeamento padrão dos atributos dos grupos do Active Directory do Azure para os atributos de recursos de http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  

###<a name="table-1-default-user-attribute-mapping"></a>Tabela 1: Mapeamento de atributo de usuário de padrão

| Usuário do Azure Active Directory | urn: ietf:params:scim:schemas:extension:enterprise:2.0:User |
| ------------- | ------------- |
| IsSoftDeleted | ativa |
| displayName | displayName |
| TelephoneNumber de fax | valorizam phoneNumbers [tipo eq "fax"] |
| givenName | name.givenName |
| jobTitle | título |
| mail | valorizam emails [tipo eq "trabalho"] |
| mailNickname | externalId |
| Gerenciador de | Gerenciador de |
| móvel | valorizam phoneNumbers [tipo eq "mobile"] |
| ID do objeto | ID |
| CEP | .postalCode de endereços [tipo eq "trabalho"] |
| Endereços de proxy | emails [Digite eq "outro"]. Valor |
| físico de entrega de OfficeName | endereços [Digite eq "outro"]. Formatado |
| streetAddress | .streetAddress de endereços [tipo eq "trabalho"] |
| Sobrenome | name.familyName |
| Número de telefone | valorizam phoneNumbers [tipo eq "trabalho"] |
| usuário PrincipalName | nome de usuário |


###<a name="table-2-default-group-attribute-mapping"></a>Tabela 2: Mapeamento de atributo de grupo de padrão

| Azure grupo do Active Directory | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| ------------- | ------------- |
| displayName | externalId |
| mail | valorizam emails [tipo eq "trabalho"] |
| mailNickname | displayName |
| membros | membros |
| ID do objeto | ID |
| proxyAddresses | emails [Digite eq "outro"]. Valor |


##<a name="user-provisioning-and-de-provisioning"></a>Fornecimento de usuário e cancelamento

A figura a seguir mostra as mensagens que Azure Active Directory enviará a um serviço SCIM para gerenciar o ciclo de vida de um usuário em outra identidade armazenam.  O diagrama também mostra como um serviço SCIM implementado usando as bibliotecas de Common Language Infrastructure fornecidas pela Microsoft para a criação de tais serviços traduzirá essas solicitações em chamadas para os métodos de um provedor.  

![][4]
*Figura: Fornecimento de usuário e cancelamento sequência*

**1:**  Active Directory do Azure irá consultar o serviço de um usuário com um valor de atributo externalId correspondente ao valor do atributo mailNickname de um usuário no Active Directory do Azure.  A consulta vai ser expresso como uma solicitação de Hypertext Transfer Protocol como este, na qual jyoung é um exemplo de um mailNickname de um usuário no Active Directory do Azure: 

    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...

Se o serviço foi criado usando as bibliotecas de Common Language Infrastructure fornecidas pela Microsoft para implementação de serviços SCIM, em seguida, a solicitação será convertida em uma chamada para o método de consulta do provedor do serviço.  Aqui está a assinatura do método: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);

Aqui está a definição da interface de Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 

    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }
    
    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }

No caso de amostra precedente de uma consulta para um usuário com um determinado valor para o atributo externalId, valores dos argumentos passados para o método de consulta ficará assim: 

* parâmetros. AlternateFilters.Count: 1
* parâmetros. AlternateFilters.ElementAt(0). AttributePath: "externalId"
* parâmetros. AlternateFilters.ElementAt(0). ComparisonOperator: ComparisonOperator.Equals
* parâmetros. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
* correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin. Identificação da solicitação"] 

**2:**  Se a resposta a uma consulta para o serviço de um usuário com um valor de atributo externalId correspondente ao valor do atributo mailNickname de um usuário no Active Directory do Azure não retornar todos os usuários, o Azure Active Directory irá solicitar que o serviço provisionar um usuário correspondente do Active Directory do Azure.  Aqui está um exemplo de tal uma solicitação: 

    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}

As bibliotecas de Common Language Infrastructure fornecidas pela Microsoft para implementação de serviços SCIM seriam traduzir solicitação em uma chamada para o método Create do provedor do serviço.  O método Create tem essa assinatura: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);

No caso de uma solicitação para provisionar um usuário, o valor do argumento recurso será uma instância da Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser classe, definida na biblioteca do Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Se a solicitação para provisionar o usuário é bem sucedida, a implementação do método deve retornar uma instância da a Microsoft.SystemForCrossDomainIdentityManagement. Classe de Core2EnterpriseUser, com o valor da propriedade identificador definido para o identificador exclusivo do usuário recentemente provisionado.  

**3:**  Para atualizar um usuário conhecido um armazenamento de identidades apoiado por um SCIM, Azure Active Directory continuará solicitando o estado atual do usuário do serviço com uma solicitação como esta: 

    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...

Em um serviço criado usando as bibliotecas de Common Language Infrastructure fornecidas pela Microsoft para implementação SCIM serviços, a solicitação será convertida em uma chamada para o método de recuperar do provedor do serviço.  Aqui está a assinatura do método recuperar: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);
    
    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }

No caso de exemplo acima de uma solicitação para recuperar o estado atual de um usuário, os valores das propriedades do objeto fornecidos como o valor do argumento parâmetros ficará assim: 

* Identificador: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

**4:**  Se um atributo de referência for a ser atualizado, Azure Active Directory irá consultar o serviço para determinar se ou não o valor atual do atributo referência na loja de identidade apoiado pelo serviço já corresponde ao valor do atributo no Active Directory do Azure.  No caso de usuários, o atributo somente do que o valor atual será consultado dessa maneira é o atributo de gerente.  Aqui está um exemplo de uma solicitação para determinar se o atributo de gerente de um objeto de determinado usuário atualmente tem um determinado valor: 

    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...

O valor do parâmetro de consulta de atributos, a id, significa que se existe um objeto de usuário que satisfaz a expressão fornecida como o valor do parâmetro de consulta de filtro, em seguida, o serviço é esperado a responder com um recurso urn: ietf:params:scim:schemas:core:2.0:User ou urn: ietf:params:scim:schemas:extension:enterprise:2.0:User, incluindo apenas o valor do atributo de id do recurso.  Claro, o valor do atributo id é conhecido ao solicitante — ele está incluído no valor do parâmetro de consulta de filtro; a finalidade do pedir a ele é realmente solicitar uma representação mínima de um recurso que satisfaz a expressão de filtro como uma indicação de se existe ou não tais objetos.   

Se o serviço foi criado usando as bibliotecas de Common Language Infrastructure fornecidas pela Microsoft para implementação de serviços SCIM, em seguida, a solicitação será convertida em uma chamada para o método de consulta do provedor do serviço.  O valor das propriedades do objeto fornecidos como o valor do argumento parâmetros será da seguinte maneira: 

* parâmetros. AlternateFilters.Count: 2
* parâmetros. AlternateFilters.ElementAt(x). AttributePath: "id"
* parâmetros. AlternateFilters.ElementAt(x). ComparisonOperator: ComparisonOperator.Equals
* parâmetros. AlternateFilter.ElementAt(x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parâmetros. AlternateFilters.ElementAt(y). AttributePath: "gerente"
* parâmetros. AlternateFilters.ElementAt(y). ComparisonOperator: ComparisonOperator.Equals
* parâmetros. AlternateFilter.ElementAt(y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parâmetros. RequestedAttributePaths.ElementAt(0): "id"
* parâmetros. SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

Aqui, o valor do índice x pode ser 0 e o valor de y índice pode ser 1, ou o valor de x pode ser 1 e o valor de y pode ser 0, dependendo a ordem das expressões do parâmetro de consulta de filtro.   

**5:**  Aqui está um exemplo de uma solicitação do Azure Active Directory em um serviço SCIM para atualizar um usuário: 

    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}

As bibliotecas do Microsoft Common Language Infrastructure para implementar serviços SCIM seriam traduzir a solicitação para uma chamada para o método de atualização do provedor do serviço.  Aqui está a assinatura do método: 

    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }
    
    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }
      
      public string Value
      { get; set; }
    }



No caso de exemplo acima de uma solicitação de atualização de um usuário, o objeto fornecido como o valor do argumento patch terá esses valores de propriedade: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest como PatchRequest2). Operations.Count: 1
* (PatchRequest como PatchRequest2). Operations.ElementAt(0). OperationName: OperationName.Add
* (PatchRequest como PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "gerente"
* (PatchRequest como PatchRequest2). Operations.ElementAt(0). Value.Count: 1
* (PatchRequest como PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Referência: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest como PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Valor: 2819c223-7f76-453a-919d-413861904646

**6:**  Para eliminação provisionar um usuário de uma loja de identidade apoiado por um serviço SCIM, o Active Directory do Azure enviará uma solicitação como esta: 

    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    
Se o serviço foi criado usando as bibliotecas de Common Language Infrastructure fornecidas pela Microsoft para implementação de serviços SCIM, em seguida, a solicitação será convertida em uma chamada para o método Delete do provedor do serviço.   Esse método tem essa assinatura: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
 
O objeto fornecido como o valor do argumento resourceIdentifier terá esses valores de propriedade no caso do exemplo precedente de uma solicitação de eliminação provisionar um usuário: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

##<a name="group-provisioning-and-de-provisioning"></a>Fornecimento de grupo e cancelamento

A figura a seguir mostra as mensagens que Azure Active Directory enviará a um serviço SCIM para gerenciar o ciclo de vida de um grupo em outra identidade armazenam.  As mensagens diferem das mensagens relativas aos usuários de três maneiras: 

* O esquema de um recurso de grupo será identificado como http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  
* Solicitações para recuperar grupos serão estipulam que o atributo de membros é a ser excluído da qualquer recurso fornecido em resposta à solicitação.  
* As solicitações para determinar se um atributo de referência tem um determinado valor será solicitações sobre o atributo de membros.  

![][5]
*Figura: Fornecimento de grupo e cancelamento sequência*

##<a name="related-articles"></a>Artigos relacionados

- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
- [Automatizar usuário provisionamento/desprovisionamento aos aplicativos SaaS](active-directory-saas-app-provisioning.md)
- [Personalizando os mapeamentos de atributo de provisionamento do usuário](active-directory-saas-customizing-attribute-mappings.md)
- [Escrevendo expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Escopo filtros para provisionamento do usuário](active-directory-saas-scoping-filters.md)
- [Notificações de provisionamento de conta](active-directory-saas-account-provisioning-notifications.md)
- [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)


    
<!--Image references-->
[1]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
