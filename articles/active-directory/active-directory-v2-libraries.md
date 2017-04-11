<properties
   pageTitle="Bibliotecas de autenticação do Azure Active Directory v 2.0 | Microsoft Azure"
   description="Bibliotecas de cliente compatível e bibliotecas de middleware de servidor e biblioteca relacionada, fonte e links de amostras, o ponto de extremidade do Active Directory do Azure v 2.0."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/30/2016"
   ms.author="skwan;bryanla"/>


# <a name="azure-active-directory-v20-authentication-libraries"></a>Bibliotecas de autenticação do Azure Active Directory v 2.0
O ponto de extremidade do Azure Active Directory (AD Azure) v 2.0 é compatível com os protocolos padrão do setor OAuth 2.0 e OpenID conectar 1.0. Você pode usar várias bibliotecas da Microsoft e outras organizações com o ponto de extremidade de v 2.0.

Quando você cria um aplicativo que usa o ponto de extremidade de v 2.0, recomendamos que você use bibliotecas escritas por especialistas de domínio de protocolo que sigam uma metodologia de ciclo de vida de desenvolvimento de segurança (SDL), como [o seguido pela Microsoft][Microsoft-SDL]. Se você decidir suporte para os protocolos de mão-código, recomendamos que você siga metodologia SDL e observe fechar as considerações de segurança na especificações padrões para cada protocolo.

## <a name="types-of-libraries"></a>Tipos de bibliotecas

V 2.0 de AD Azure funciona com dois tipos de bibliotecas:

- **Bibliotecas de cliente**. Servidores e clientes nativos usam bibliotecas de cliente para obter tokens de acesso para chamar um recurso, como Microsoft Graph.
- **Bibliotecas de middleware do servidor**. Aplicativos Web usam bibliotecas de middleware de servidor para o acesso do usuário. APIs de Web usam bibliotecas de middleware do servidor para validar tokens que são enviadas por clientes nativos ou por outros servidores.

## <a name="library-support"></a>Suporte de biblioteca
Porque você pode escolher qualquer biblioteca compatíveis com os padrões quando você usa o ponto de extremidade de v 2.0, é importante saber onde obter suporte. Para problemas e solicitações de recursos no código de biblioteca, contate o proprietário da biblioteca. Para problemas e solicitações de recursos na implementação do protocolo do lado do serviço, contate a Microsoft.

Bibliotecas ocorrer em duas categorias de suporte:

- **Suporte da Microsoft**. Microsoft fornece correções para essas bibliotecas e fez SDL auditoria detalhada sobre essas bibliotecas.
- **Compatível**. A Microsoft testou essas bibliotecas em cenários básicos e confirmado que funcionam com o ponto de extremidade de v 2.0. A Microsoft não fornece correções dessas bibliotecas e não tiver feito uma revisão dessas bibliotecas. Problemas e solicitações de recursos devem ser direcionadas ao projeto de código-fonte aberto da biblioteca.

Para obter uma lista de bibliotecas que funcionam com o ponto de extremidade de v 2.0, consulte as próximas seções deste artigo.

## <a name="microsoft-supported-client-libraries"></a>Bibliotecas de cliente com suporte da Microsoft
| Plataforma| Nome da biblioteca| Baixar | Código-fonte | Exemplo |
| :-: | :-: | :-: | :-: | :-: |
| .NET, da Windows Store, Xamarin | Biblioteca de autenticação da Microsoft (MSAL) para .NET | [Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] | [MSAL para .NET (GitHub)][ClientLib-NET-Repo] | [Amostra de nativo do cliente de desktop do Windows][ClientLib-NET-Sample] |
| Node | Plug-in do Microsoft Azure Active Directory Passport.js | [Passport Azure-AD (npm)][ClientLib-Node-Lib] | [Passport Azure-AD (GitHub)][ClientLib-Node-Repo] | Em breve |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## <a name="microsoft-supported-server-middleware-libraries"></a>Bibliotecas de middleware do servidor de suporte da Microsoft
| Plataforma| Nome da biblioteca| Baixar | Código-fonte | Exemplo |
| :-: | :-: | :-: | :-: | :-: |
| .NET 4. x | OWIN OpenID conectar Middleware para ASP.NET | [Microsoft.Owin.Security.OpenIdConnect (NuGet)][ServerLib-Net4-Owin-Oidc-Lib] | [Projeto de Katana (CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] | [Exemplo de aplicativo da Web][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4. x | OWIN portador OAuth Middleware para ASP.NET | [Microsoft.Owin.Security.OAuth (NuGet)][ServerLib-Net4-Owin-Oauth-Lib] | [Projeto de Katana (CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] | [Exemplo de Web API][ServerLib-Net4-Owin-Oauth-Sample] |
| Núcleo do .NET | OWIN OpenID conectar Middleware para .NET Core | [Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] | [Segurança do ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] | [Exemplo de aplicativo da Web][ServerLib-NetCore-Owin-Oidc-Sample] |
| Núcleo do .NET | OWIN portador OAuth Middleware para .NET Core | [Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] | [Segurança do ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] | Em breve |
| Node | Microsoft Azure Active Directory Passport.js plug-in | [Passport Azure-AD (npm)][ServerLib-Node-Lib] | [Passport Azure-AD (GitHub)][ServerLib-Node-Repo] | [Exemplo de aplicativo da Web][ServerLib-Node-Sample] |
<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .NET | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .NET (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## <a name="compatible-client-libraries"></a>Bibliotecas de cliente compatível
| Plataforma| Nome da biblioteca | Versão testada | Código-fonte | Exemplo |
| :-: | :-: | :-: | :-: | :-: |
| Android | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) | 0.2.1 | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) | [Amostra de aplicativo nativo](active-directory-v2-devquickstarts-android.md) |
| iOS | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | 1.2.8 | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | [Amostra de aplicativo nativo](active-directory-v2-devquickstarts-ios.md)|
| JavaScript | [Js](https://adodson.com/hello.js/) | 1.13.5 | [Js](https://github.com/MrSwitch/hello.js) | Em breve |
| Python Flask | [Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) | 0.9.3 | [Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) | Em breve |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth/wiki) | omniauth:1.3.1</br>omniauth-oauth2:1.4.0 | [OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) | Em breve |
<!--- REMOVING BRANDON'S FOR NOW
|  |  |  |  |  |
| Android | [OAuth2 Client](https://github.com/wuman/android-oauth-client) |   | [OAuth2 Client](https://github.com/wuman/android-oauth-client)  | Coming soon  |
| Java | [WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server) | [Version 5.2.0](http://wso2.com/products/identity-server/) | [Source](https://docs.wso2.com/display/IS500/Building+from+Source) | [Samples index](https://docs.wso2.com/display/IS500/Samples)  |
| Java | [Java Gluu Server](https://gluu.org/docs/) |   | [oxAuth](https://github.com/GluuFederation/oxAuth)  | Coming soon |
| Node.js | [NPM passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect) | 0.0.1  | [Passport-OpenID Connect](https://github.com/jaredhanson/passport-openidconnect) | Coming soon  |
| PHP | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP) |   | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)  | Coming soon  |
-->

## <a name="compatible-server-middleware-libraries"></a>Bibliotecas de middleware de servidor compatíveis
Em breve

## <a name="related-content"></a>Conteúdo relacionado
Para obter mais informações sobre o ponto de extremidade do Azure AD v 2.0, consulte [Visão geral do Azure AD app modelo v 2.0][AAD-App-Model-V2-Overview].

Para ajudar a refinar e nosso conteúdo de forma, use o recurso de comentários Disqus no final deste artigo para fornecer comentários.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:
[ClientLib-Iosmac-Lib]:
[ClientLib-Iosmac-Repo]:
[ClientLib-Iosmac-Sample]:
[ClientLib-Android-Lib]:
[ClientLib-Android-Repo]:
[ClientLib-Android-Sample]:
[ClientLib-Js-Lib]:
[ClientLib-Js-Repo]:
[ClientLib-Js-Sample]:
[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-node-web/
