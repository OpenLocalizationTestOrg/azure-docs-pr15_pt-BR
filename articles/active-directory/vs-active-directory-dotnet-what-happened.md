<properties
    pageTitle="O que aconteceu com meu projeto MVC (Visual Studio Azure Active Directory conectado serviço) | Microsoft Azure "
    description="Descreve o que acontece ao projeto MVC quando você se conecta ao Azure AD usando os serviços de Visual Studio conectado"
    services="active-directory"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu com meu projeto MVC (Visual Studio Azure Active Directory conectado serviço)?

> [AZURE.SELECTOR]
> - [Guia de Introdução](vs-active-directory-dotnet-getting-started.md)
> - [O que aconteceu](vs-active-directory-dotnet-what-happened.md)



## <a name="references-have-been-added"></a>Referências foram adicionadas

### <a name="nuget-package-references"></a>Referências de pacote do NuGet

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel.Tokens.Jwt**

### <a name="net-references"></a>Referências de .NET

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **IdentityModel**
- **System.IdentityModel.Tokens.Jwt**
- **System.Runtime.Serialization**

## <a name="code-has-been-added"></a>Código foi adicionado

### <a name="code-files-were-added-to-your-project"></a>Arquivos de código foram adicionados ao seu projeto

Uma classe de inicialização de autenticação, **App_Start/Startup.Auth.cs** foi adicionada ao seu projeto que contém a lógica de inicialização para autenticação do Azure AD. Além disso, uma classe de controlador, Controllers/AccountController.cs foi adicionada que contém métodos **SignIn()** e **SignOut()** . Finalmente, uma exibição parcial, **Views/Shared/_LoginPartial.cshtml** foi adicionada contendo um link de ação para entrar/SignOut.

### <a name="startup-code-was-added-to-your-project"></a>Código de inicialização foi adicionado ao seu projeto

Se você já tinha uma classe de inicialização no seu projeto, o método de **configuração** foi atualizado para incluir uma chamada para **ConfigureAuth(app)**. Caso contrário, a classe de inicialização foi adicionada ao seu projeto.

### <a name="your-appconfig-or-webconfig-has-new-configuration-values"></a>Seu App ou Web. config tem novos valores de configuração

As seguintes entradas de configuração foram adicionadas.


    <appSettings>
        <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="The selected Azure AD Domain" />
        <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
        <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
    </appSettings>

### <a name="an-azure-active-directory-ad-app-was-created"></a>Um aplicativo do Azure Active Directory (AD) foi criado
Um aplicativo do Azure AD foi criado no diretório que você selecionou no assistente.

##<a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Se eu marcada *Desabilitar a autenticação de contas de usuário individuais*, quais alterações adicionais foram feitas ao meu projeto?
Referências de pacote do NuGet foram removidas e arquivos foram removidos e backup. Dependendo o estado de seu projeto, você pode precisar remover referências adicionais ou arquivos, manualmente ou modificar o código conforme apropriado.

### <a name="nuget-package-references-removed-for-those-present"></a>Referências de pacote do NuGet removidas (para aqueles que presente)

- **Microsoft.AspNet.Identity.Core**
- **Microsoft.AspNet.Identity.EntityFramework**
- **Microsoft.AspNet.Identity.Owin**

### <a name="code-files-backed-up-and-removed-for-those-present"></a>Arquivos de código backup e removidos (para aqueles que presente)

Cada um dos seguintes arquivos foi feito o backup e removida do projeto. Arquivos de backup estão localizados em uma pasta de 'Backup' na raiz do diretório do projeto.

- **App_Start\IdentityConfig.cs**
- **Controllers\ManageController.cs**
- **Models\IdentityModels.cs**
- **Models\ManageViewModels.cs**

### <a name="code-files-backed-up-for-those-present"></a>Arquivos de código backup (para aqueles que presente)

Cada um dos seguintes arquivos foi feita backup antes de serem substituídos. Arquivos de backup estão localizados em uma pasta de 'Backup' na raiz do diretório do projeto.

- **Startup.cs**
- **App_Start\Startup.AUTH.cs**
- **Controllers\AccountController.cs**
- **Views\Shared\_LoginPartial.cshtml**

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Se eu marcada *ler dados de diretório*, quais alterações adicionais foram feitas ao meu projeto?

Referências adicionais foram adicionadas.

###<a name="additional-nuget-package-references"></a>Referências de pacote NuGet adicionais

- **EntityFramework**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **System.Spatial**

###<a name="additional-net-references"></a>Referências de .NET adicionais

- **EntityFramework**
- **EntityFramework.SqlServer**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
- **System.Spatial**

###<a name="additional-code-files-were-added-to-your-project"></a>Arquivos de código adicionais foram adicionados ao seu projeto

Dois arquivos foram adicionados ao cache de token de suporte: **Models\ADALTokenCache.cs** e **Models\ApplicationDbContext.cs**.  Um controlador adicional e exibir foram adicionados para ilustrar acessando informações de perfil de usuário usando graph Azure APIs.  Esses arquivos são **Controllers\UserProfileController.cs** e **Views\UserProfile\Index.cshtml**.

###<a name="additional-startup-code-was-added-to-your-project"></a>Código de inicialização adicional foi adicionado ao seu projeto

No arquivo **startup.auth.cs** , um novo objeto **OpenIdConnectAuthenticationNotifications** foi adicionado ao membro **notificações** da **OpenIdConnectAuthenticationOptions**.  Isso é para habilitar a receber o código de OAuth e exchange-lo para um token de acesso.

###<a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Foram feitas alterações adicionais à sua App ou Web. config

As seguintes entradas de configuração adicionais foram adicionadas.

    <appSettings>
        <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
    </appSettings>

As seguintes seções de configuração e a cadeia de conexão foram adicionados.

    <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>
    <connectionStrings>
        <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    <entityFramework>
        <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
          <parameters>
            <parameter value="mssqllocaldb" />
          </parameters>
        </defaultConnectionFactory>
        <providers>
          <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
        </providers>
    </entityFramework>


###<a name="your-azure-active-directory-app-was-updated"></a>Seu aplicativo do Azure Active Directory foi atualizado
Seu aplicativo do Azure Active Directory foi atualizado para incluir a permissão de *leitura dados de diretório* e uma tecla adicional foi criada que foi usado como o *ida: ClientSecret* no arquivo **Web. config** .

[Saiba mais sobre o Active Directory do Azure](https://azure.microsoft.com/services/active-directory/)
