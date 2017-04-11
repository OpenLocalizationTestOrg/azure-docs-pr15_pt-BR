<properties
    pageTitle="O que aconteceu com meu projeto WebApi (Visual Studio Azure Active Directory conectado serviço) | Microsoft Azure "
    description="Descreve o que acontece ao projeto MVC WebApi você se conecta ao Azure AD usando Visual Studio"
  services="active-directory"
    documentationCenter=""
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

# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu com meu projeto WebApi (Visual Studio Azure Active Directory conectado serviço)

> [AZURE.SELECTOR]
> - [Guia de Introdução](vs-active-directory-webapi-getting-started.md)
> - [O que aconteceu](vs-active-directory-webapi-what-happened.md)

##<a name="references-have-been-added"></a>Referências foram adicionadas

###<a name="nuget-package-references"></a>Referências de pacote do NuGet

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

###<a name="net-references"></a>Referências de .NET

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

##<a name="code-changes"></a>Alterações de código

###<a name="code-files-were-added-to-your-project"></a>Arquivos de código foram adicionados ao seu projeto

Uma classe de inicialização de autenticação, **App_Start/Startup.Auth.cs** foi adicionada ao seu projeto que contém a lógica de inicialização para autenticação do Azure AD.

###<a name="startup-code-was-added-to-your-project"></a>Código de inicialização foi adicionado ao seu projeto

Se você já tinha uma classe de inicialização no seu projeto, o método de **configuração** foi atualizado para incluir uma chamada para `ConfigureAuth(app)`. Caso contrário, a classe de inicialização foi adicionada ao seu projeto.


###<a name="your-appconfig-or-webconfig-file-has-new-configuration-values"></a>Seu arquivo App. config ou Web. config tem novos valores de configuração.

As seguintes entradas de configuração foram adicionadas.
```
    `<appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

###<a name="an-azure-ad-app-was-created"></a>Um aplicativo do Azure AD foi criado

Um aplicativo do Azure AD foi criado no diretório que você selecionou no assistente.

[Saiba mais sobre o Active Directory do Azure](https://azure.microsoft.com/services/active-directory/)

##<a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Se eu marcada *Desabilitar a autenticação de contas de usuário individuais*, quais alterações adicionais foram feitas ao meu projeto?
Referências de pacote do NuGet foram removidas e arquivos foram removidos e backup. Dependendo o estado de seu projeto, você pode precisar remover referências adicionais ou arquivos, manualmente ou modificar o código conforme apropriado.

###<a name="nuget-package-references-removed-for-those-present"></a>Referências de pacote do NuGet removidas (para aqueles que presente)

- `Microsoft.AspNet.Identity.Core`
- `Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

###<a name="code-files-backed-up-and-removed-for-those-present"></a>Arquivos de código backup e removidos (para aqueles que presente)

Cada um dos seguintes arquivos foi feito o backup e removida do projeto. Arquivos de backup estão localizados em uma pasta de 'Backup' na raiz do diretório do projeto.

- `App_Start\IdentityConfig.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Providers\ApplicationOAuthProvider.cs`

###<a name="code-files-backed-up-for-those-present"></a>Arquivos de código backup (para aqueles que presente)

Cada um dos seguintes arquivos foi feita backup antes de serem substituídos. Arquivos de backup estão localizados em uma pasta de 'Backup' na raiz do diretório do projeto.

- `Startup.cs`
- `App_Start\Startup.Auth.cs`

##<a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Se eu marcada *ler dados de diretório*, quais alterações adicionais foram feitas ao meu projeto?

###<a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Foram feitas alterações adicionais à sua App ou Web. config

As seguintes entradas de configuração adicionais foram adicionadas.

```
    `<appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

###<a name="your-azure-active-directory-app-was-updated"></a>Seu aplicativo do Azure Active Directory foi atualizado
Seu aplicativo do Azure Active Directory foi atualizado para incluir a permissão de *leitura dados de diretório* e uma tecla adicional foi criada que foi usado como a *Ida: senha* na `web.config` arquivo.

[Saiba mais sobre o Active Directory do Azure](https://azure.microsoft.com/services/active-directory/)
