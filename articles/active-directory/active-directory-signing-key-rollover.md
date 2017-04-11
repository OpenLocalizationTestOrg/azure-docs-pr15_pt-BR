<properties
    pageTitle="Entrando sobreposição chave Azure AD | Microsoft Azure"
    description="Este artigo descreve as práticas recomendadas de sobreposição chave assinatura para o Active Directory do Azure"
    services="active-directory"
    documentationCenter=".net"
    authors="gsacavdm"
    manager="krassk"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="gsacavdm"/>

# <a name="signing-key-rollover-in-azure-active-directory"></a>Assinatura de chave sobreposição no Active Directory do Azure

Este tópico aborda o que você precisa saber sobre as chaves públicas que são usadas no Azure Active Directory (AD Azure) para assinar tokens de segurança. É importante observar que sobreposição estas teclas periodicamente e, em caso de emergência, poderia ser acumulados imediatamente. Todos os aplicativos que usam o Azure AD devem ser capazes de manipular o processo de sobreposição chave programaticamente ou estabelecer um processo de sobreposição manual periódicos. Continue a leitura para entender como funcionam as chaves como avaliar o impacto da sobreposição ao seu aplicativo e como atualizar seu aplicativo ou estabelecer um processo de sobreposição manual periódicos para lidar com sobreposição chave, se necessário.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Visão geral das chaves de assinatura no Azure AD

Azure AD usa criptografia de chave pública criada em padrões do setor para estabelecer confiança entre si mesmo e os aplicativos que usá-lo. Em termos práticos, isso funciona da seguinte maneira: Azure AD usa uma chave de assinatura que consiste em um par de chaves público e privado. Quando um usuário entra um aplicativo que usa o Azure AD para autenticação, o Azure AD cria um token de segurança que contém informações sobre o usuário. Esse símbolo é assinado pelo Azure AD usando sua chave privada antes que ela é enviada para o aplicativo. Para verificar se o token é válida e realmente originado do Azure AD, o aplicativo deve validar a assinatura do token usando a chave pública exposta pela Azure AD contida no [documento de descoberta OpenID conectar](http://openid.net/specs/openid-connect-discovery-1_0.html) ou WS/SAML-Fed [documento de metadados de Federação](active-directory-federation-metadata.md)do locatário.

Para fins de segurança, Azure AD assinatura rolos chaves periodicamente e, no caso de emergência, poderia ser acumulados imediatamente. Qualquer aplicativo que se integra ao Azure AD devo estar preparado para lidar com um evento de sobreposição chave independentemente frequência ele pode ocorrer. Se ele não e seu aplicativo tenta usar uma chave de expirado para verificar a assinatura em um token, a solicitação de entrada falhará.

Sempre há mais de uma chave válida disponíveis no documento discovery OpenID conectar e o documento de metadados de Federação. Seu aplicativo deve estar preparado para usar qualquer uma das chaves especificadas no documento, pois uma chave pode ser acumulada em breve, outro pode ser sua substituição e assim por diante.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Como avaliar se seu aplicativo será afetado e o que fazer sobre isso

Como o seu aplicativo lida com sobreposição chave depende de variáveis como o tipo de aplicativo ou qual protocolo de identidade e a biblioteca foi usada. As seções a seguir avaliam se os tipos mais comuns de aplicativos são afetados pela chave sobreposição e fornecem orientações sobre como atualizar o aplicativo para suportar sobreposição automática ou atualizar manualmente a chave.

* [Aplicativos cliente nativo acessando recursos](#nativeclient)
* [Aplicativos da Web / APIs acessando recursos](#webclient)
* [Aplicativos da Web / APIs protegem recursos e criados usando os serviços de aplicativo do Azure](#appservices)
* [Aplicativos da Web / APIs protegendo recursos usando .NET OWIN OpenID conectar, WS-Fed ou middleware WindowsAzureActiveDirectoryBearerAuthentication](#owin)
* [Aplicativos da Web / APIs protegendo recursos usando middleware .NET Core OpenID conectar ou JwtBearerAuthentication](#owincore)
* [Aplicativos da Web / APIs protegendo recursos usando o módulo de passport azure-ad Node](#passport)
* [Aplicativos da Web / APIs protegem recursos e criados com o Visual Studio de 2015](#vs2015)
* [Aplicativos da Web protegendo recursos e criados com o Visual Studio 2013](#vs2013)
* [APIs de Web protegem recursos e criados com o Visual Studio 2013](#vs2013_webapi)
* [Aplicativos da Web protegendo recursos e criados com o Visual Studio 2012](#vs2012)
* [Aplicativos da Web protegendo recursos e criados com o Visual Studio 2010, o 2008 usando Windows identidade Foundation](#vs2010)
* [Aplicativos da Web / APIs protegendo recursos usando qualquer outras bibliotecas ou manualmente implementar qualquer um dos protocolos suportados](#other)

Este guia **não** aplicável para:

* Aplicativos adicionados da Galeria de aplicativo Azure AD (incluindo personalizada) têm separada orientações sobre chaves de assinatura. [Obter mais informações.](active-directory-sso-certs.md)
* Locais aplicativos publicados via proxy do aplicativo não precisam se preocupar sobre chaves de assinatura.

### <a name="nativeclient"></a>Aplicativos cliente nativo acessando recursos

Aplicativos que estão apenas acessando recursos (ou seja Microsoft Graph, KeyVault, API do Outlook e outras APIs do Microsoft) geralmente somente obtém um token e passam-o para o proprietário do recurso. Considerando que eles não estão protegendo todos os recursos, eles não inspecionar o token e, portanto, não é necessário garantir que ele está conectado corretamente.

Aplicativos cliente nativo, se desktop ou mobile, enquadram nesta categoria e, portanto, não são afetados pela sobreposição.

### <a name="webclient"></a>Aplicativos da Web / APIs acessando recursos

Aplicativos que estão apenas acessando recursos (ou seja Microsoft Graph, KeyVault, API do Outlook e outras APIs do Microsoft) geralmente somente obtém um token e passam-o para o proprietário do recurso. Considerando que eles não estão protegendo todos os recursos, eles não inspecionar o token e, portanto, não é necessário garantir que ele está conectado corretamente.

Aplicativos e web APIs que estiver usando o fluxo de somente aplicativo (credenciais cliente / certificado de cliente), enquadram nesta categoria e, portanto, não são afetados pela sobreposição.

### <a name="appservices"></a>Aplicativos da Web / APIs protegem recursos e criados usando os serviços de aplicativo do Azure

Autenticação dos serviços de aplicativo Azure / funcionalidade de autorização (EasyAuth) já tem a lógica necessária para lidar com sobreposição chave automaticamente.

### <a name="owin"></a>Aplicativos da Web / APIs protegendo recursos usando .NET OWIN OpenID conectar, WS-Fed ou middleware WindowsAzureActiveDirectoryBearerAuthentication

Se seu aplicativo estiver usando o .NET OWIN OpenID conectar, WS-Fed ou middleware WindowsAzureActiveDirectoryBearerAuthentication, ele já tem a lógica necessária para lidar com sobreposição chave automaticamente.

Você pode confirmar que o seu aplicativo estiver usando qualquer um desses procurando por qualquer um dos seguintes trechos em seu aplicativo Startup.cs ou Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
    });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="owincore"></a>Aplicativos da Web / APIs protegendo recursos usando middleware .NET Core OpenID conectar ou JwtBearerAuthentication

Se seu aplicativo estiver usando o middleware .NET Core OWIN OpenID conectar ou JwtBearerAuthentication, ele já tem a lógica necessária para lidar com sobreposição chave automaticamente.

Você pode confirmar que o seu aplicativo estiver usando qualquer um desses procurando por qualquer um dos seguintes trechos em seu aplicativo Startup.cs ou Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
    });
```

### <a name="passport"></a>Aplicativos da Web / APIs protegendo recursos usando o módulo de passport azure-ad Node

Se seu aplicativo estiver usando o módulo do ad passport Node, ele já tem a lógica necessária para lidar com sobreposição chave automaticamente.

Você pode confirmar que seu aplicativo passport-ad pesquisando o trecho a seguir no app.js do seu aplicativo

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Aplicativos da Web / APIs protegem recursos e criados com o Visual Studio de 2015

Se seu aplicativo foi criado usando um modelo de aplicativo da web no Visual Studio 2015 e tiver selecionado **A contas escolares e trabalho** no menu de **Autenticação de alteração** , ela já tem a lógica necessária para lidar com sobreposição chave automaticamente. Essa lógica, incorporada em middleware OWIN OpenID conectar, recupera e armazena as chaves do documento de descoberta OpenID conectar e atualiza periodicamente-los.

Se você adicionou autenticação à sua solução manualmente, seu aplicativo não pode ter a lógica de sobreposição de chave necessário. Você precisará escrevê-lo mesmo ou siga as etapas em [aplicativos da Web / APIs usando qualquer outras bibliotecas ou manualmente implementar qualquer um dos protocolos suportados.](#other).

### <a name="vs2013"></a>Aplicativos da Web protegendo recursos e criados com o Visual Studio 2013

Se seu aplicativo foi criado usando um modelo de aplicativo da web no Visual Studio 2013 e tiver selecionado **Contas organizacionais** no menu de **Autenticação de alteração** , ela já tem a lógica necessária para lidar com sobreposição chave automaticamente. Essa lógica armazena o identificador exclusivo da sua organização e as informações da chave assinatura em duas tabelas de banco de dados associadas ao projeto. Você pode encontrar a cadeia de conexão do banco de dados no arquivo de Web. config do projeto.

Se você adicionou autenticação à sua solução manualmente, seu aplicativo não pode ter a lógica de sobreposição de chave necessário. Você precisará escrevê-lo mesmo ou siga as etapas em [aplicativos da Web / APIs usando qualquer outras bibliotecas ou manualmente implementar qualquer um dos protocolos suportados.](#other).

As etapas a seguir o ajudará a verificar se a lógica está funcionando corretamente em seu aplicativo.

1. No 2013 do Visual Studio, abra a solução e, em seguida, clique na guia **Server Explorer** na janela à direita.
2. Expanda **conexões de dados**, **DefaultConnection**e **tabelas**. Localize a tabela de **IssuingAuthorityKeys** , clique sobre ela e clique em **Mostrar dados da tabela**.
3. Na tabela **IssuingAuthorityKeys** , haverá pelo menos uma linha, o que corresponde ao valor impressão digital para a chave. Exclua todas as linhas na tabela.
4. A tabela de **locatários** de atalho e, em seguida, clique em **Mostrar dados da tabela**.
5. Na tabela **locatários** , haverá pelo menos uma linha, que corresponda a um identificador de locatário do diretório exclusivo. Exclua todas as linhas na tabela. Se você não excluir linhas de tabela de **IssuingAuthorityKeys** e de tabela de **locatários** , você obterá um erro em tempo de execução.
6. Criar e executar o aplicativo. Após você ter conectado à sua conta, você pode interromper o aplicativo.
7. Retorne ao **Server Explorer** e examine os valores na tabela **IssuingAuthorityKeys** e **locatários** . Você notará que eles tiveram sido automaticamente preenchida novamente com as informações apropriadas do documento de metadados de Federação.

### <a name="vs2013"></a>APIs de Web protegem recursos e criados com o Visual Studio 2013

Se você criou um aplicativo de API da web no Visual 2013 Studio usando o modelo de API da Web e, em seguida, selecionada **Contas organizacionais** no menu de **Autenticação de alteração** , já possui a lógica necessária em seu aplicativo.

Se você configurou manualmente autenticação, siga as instruções abaixo para saber como configurar seu Web API para atualizar automaticamente as suas informações importantes.

O trecho de código a seguir demonstra como obter as teclas mais recentes do documento de metadados de Federação e use o [Manipulador de Token de JWT](https://msdn.microsoft.com/library/dn205065.aspx) para validar o token. O trecho de código pressupõe que você usará seu próprio mecanismo de cache para persistir a chave para validar tokens futuras do Azure AD, se ele estar em um banco de dados, arquivo de configuração ou em outro lugar.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a>Aplicativos da Web protegendo recursos e criados com o Visual Studio 2012

Se seu aplicativo foi criado no Visual Studio 2012, você provavelmente usou a ferramenta de acesso e identidade para configurar seu aplicativo. Também é provável que você está usando o [Registro de nome de emissor Validando (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). O VINR é responsável por manter informações sobre os provedores de identidade confiável (Azure AD) e as chaves usadas para validar tokens emitidos por eles. O VINR também torna mais fácil atualizar automaticamente as informações da chave armazenadas em um arquivo Web. config baixando o documento de metadados de Federação mais recente associado ao seu diretório, verificando se a configuração é desatualizada com o documento mais recente e atualizar o aplicativo para usar a nova chave conforme necessário.

Se você criou seu aplicativo usando qualquer um dos exemplos de código ou documentação de explicação passo a passo fornecida pela Microsoft, a lógica de sobreposição chave já está incluída em seu projeto. Você notará que o código a seguir já existe em seu projeto. Se seu aplicativo não tiver essa lógica, siga as etapas abaixo para adicioná-lo e verificar se ele está funcionando corretamente.

1. No **Solution Explorer**, adicione uma referência ao assembly **IdentityModel** para o projeto apropriado.
2. Abra o arquivo **Global.asax.cs** e adicione o seguinte usando diretivas:
```
using System.Configuration;
using System.IdentityModel.Tokens;
```
3. Adicione o seguinte método para o arquivo **Global.asax.cs** :
```
protected void RefreshValidationSettings()
{
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
}
```
4. Chame o método **RefreshValidationSettings()** no método **Application_Start()** em **Global.asax.cs** conforme mostrado:
```
protected void Application_Start()
{
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
}
```

Depois que você tiver seguido essas etapas, Web. config do seu aplicativo será atualizado com as informações mais recentes do documento de metadados de federação, incluindo as chaves mais recentes. Essa atualização ocorrerá sempre que o pool de aplicativos reciclagem no IIS; Por padrão, IIS está definido como Lixeira aplicativos cada 29 horas.

Siga as etapas abaixo para verificar se a lógica de sobreposição chave está funcionando.

1. Após ter verificado que seu aplicativo está usando o código acima, abra o arquivo **Web. config** e navegue até o **<issuerNameRegistry>** bloco, procurando especificamente a seguir algumas linhas:
```
<issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
```
2. No **<add thumbprint=””>** definindo, altere o valor de impressão digital substituindo qualquer caractere por um diferente. Salve o arquivo **Web. config** .

3. Criar o aplicativo e execute-o. Se você pode concluir o processo de entrada, seu aplicativo com êxito está atualizando a chave baixando as informações necessárias do documento de metadados de Federação do seu diretório. Se você estiver tendo problemas para entrar, certifique-se de alterações em seu aplicativo estão corretas, leia o tópico [Adicionando logon para seu aplicativo Web usando o Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) , ou baixando e inspecionar o código de exemplo a seguir: [Aplicativo em nuvem vários locatários do Active Directory do Azure](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).


### <a name="vs2010"></a>Aplicativos da Web protegendo recursos e criados com o Visual Studio 2008 ou 2010 e v 1.0 Windows identidade Foundation WIF () para .NET 3.5

Se você tiver criado um aplicativo em v 1.0 WIF, não há nenhum mecanismo fornecido para atualizar automaticamente a configuração do seu aplicativo para usar uma nova chave.

- *Maneira mais fácil* Use as ferramentas de FedUtil incluídas no SDK WIF, que pode recuperar o documento de metadados mais recente e atualizar sua configuração.
- Atualize seu aplicativo para .NET 4,5, que inclui a versão mais recente do WIF localizado no namespace System. Você pode usar o [Registro de nome de emissor Validando (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) para executar atualizações automáticas de configuração do aplicativo.
- Execute uma sobreposição manual de acordo com as instruções no final deste documento de orientação.

Instruções para usar o FedUtil para atualizar sua configuração:

1. Verifique se você tem a versão 1.0 WIF SDK instalado em sua máquina de desenvolvimento para Visual Studio 2008 ou 2010. Você pode [baixá-lo a partir daqui](https://www.microsoft.com/en-us/download/details.aspx?id=4451) se você ainda não instalou-lo.
2. No Visual Studio, abra a solução e, em seguida, clique com botão direito do projeto aplicável e selecione **atualizar metadados de Federação**. Se essa opção não estiver disponível, FedUtil e/ou a versão de 1.0 WIF SDK não foi instalado.
3. No prompt, selecione **Atualizar** para começar a atualização de metadados de sua federação. Se você tiver acesso ao ambiente de servidor onde o aplicativo está hospedado, opcionalmente, você pode usar do FedUtil [Agendador de atualização automática de metadados](https://msdn.microsoft.com/library/ee517272.aspx).
4. Clique em **Concluir** para concluir o processo de atualização.

### <a name="other"></a>Aplicativos da Web / APIs protegendo recursos usando qualquer outras bibliotecas ou manualmente implementar qualquer um dos protocolos suportados

Se você estiver usando alguma outra biblioteca ou implementado manualmente qualquer um dos protocolos suportados, você precisará revisar a biblioteca ou a implementação para garantir que a chave está sendo recuperada o documento de descoberta OpenID conectar ou o documento de metadados de Federação. Uma maneira de verificar isso é fazer uma pesquisa em código ou código da biblioteca para qualquer chamadas check-out para o documento de descoberta de OpenID ou o documento de metadados de Federação.

Se eles chave é armazenada em outro local ou codificado em seu aplicativo, você pode manualmente recupere a chave e ele adequadamente ao realizar uma sobreposição manual de acordo com as instruções no final deste documento de orientação de atualização. **É altamente recomendável que você aprimorar seu aplicativo para dar suporte a sobreposição automática** usando qualquer uma das abordagens da estrutura de tópicos neste artigo para evitar interrupções futuras e sobrecarga se Azure AD aumenta está cadência de sobreposição ou tem uma sobreposição de fora da faixa de emergência.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Como testar seu aplicativo para determinar se ele será afetado

Você pode validar se seu aplicativo suporta sobreposição automática de chave baixando os scripts e seguindo as instruções em [este repositório GitHub.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-you-application-does-not-support-automatic-rollover"></a>Como executar uma sobreposição manual se seu aplicativo não oferece suporte para sobreposição automática

Se seu aplicativo **não** suporte sobreposição automática, você precisará estabelecer um processo que periodicamente monitora chaves de assinatura do Azure AD e executa uma sobreposição manual adequadamente. [Repositório de GitHub este](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) contém scripts e instruções sobre como fazer isso.
