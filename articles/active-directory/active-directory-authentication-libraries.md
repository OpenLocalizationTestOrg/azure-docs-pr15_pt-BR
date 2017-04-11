<properties
   pageTitle="Bibliotecas de autenticação do Active Directory do Azure | Microsoft Azure"
   description="A biblioteca de autenticação do Azure AD (ADAL) permite que o cliente desenvolvedores de aplicativos autenticar facilmente os usuários para a nuvem ou locais do Active Directory (AD) e então obter tokens de acesso para proteger chamadas API."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="mbaldwin" />

# <a name="azure-active-directory-authentication-libraries"></a>Bibliotecas de autenticação do Active Directory do Azure

A autenticação do Azure AD biblioteca (ADAL) permite que os desenvolvedores de aplicativo cliente autenticar facilmente os usuários para a nuvem ou local do Active Directory (AD) e, em seguida, obter tokens de acesso para proteger chamadas API. ADAL tem muitos recursos que a autenticação tornar mais fácil para os desenvolvedores, como o suporte assíncrono, um cache de token configurável que armazena os tokens de acesso e tokens de atualização, atualização automática de token quando um token de acesso expira e um token de atualização está disponível e muito mais. Manipulando maioria da complexidade, ADAL pode focalizar um desenvolvedor na lógica de negócios em seus aplicativos e proteger facilmente os recursos sem ser especialista em segurança.

ADAL está disponível em uma variedade de plataformas.

|Plataforma|Nome do pacote|Cliente/servidor|Baixar|Código-fonte|Documentação e amostras|
|---|---|---|---|---|---|
|Cliente do .NET, Windows Store, UWP, Xamarin iOS e Android|Biblioteca de autenticação do Active Directory (ADAL) para .NET v3 |Cliente|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)|[ADAL para .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet)|[Documentação](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory)|
|.NET cliente, da Windows Store, Windows Phone 8.1 |Biblioteca de autenticação do Active Directory (ADAL) para .NET v2 |Cliente|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.2)|[ADAL para .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.2)|[Documentação](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory)|
|JavaScript|Biblioteca de autenticação do Active Directory (ADAL) para JavaScript|Cliente|[ADAL para JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|[ADAL para JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|Exemplo: [SinglePageApp-DotNet (Github)](https://github.com/AzureADSamples/SinglePageApp-DotNet)|
|OS X, iOS|Biblioteca de autenticação do Active Directory (ADAL) para o objetivo-C|Cliente|[ADAL para o objetivo-C (CocoaPods)](http://cocoadocs.org/docsets/ADAL/)|[ADAL para o objetivo-C (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)|Exemplo: [NativeClient-iOS (Github)](https://github.com/AzureADSamples/NativeClient-iOS)|
|Android|Biblioteca de autenticação do Active Directory (ADAL) para Android|Cliente|[ADAL para Android (o repositório Central)](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/)|[ADAL para Android (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-android)|Exemplo: [NativeClient Android (Github)](https://github.com/AzureADSamples/NativeClient-Android)|
|Node|Biblioteca de autenticação do Active Directory (ADAL) para Node|Cliente|[ADAL para Node (npm)](https://www.npmjs.com/package/adal-node)|[ADAL para Node (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)|Exemplo: [WebAPI-Nodejs (Github)](https://github.com/AzureADSamples/WebAPI-Nodejs)|
|Node|Middleware de autenticação do Microsoft Azure Active Directory Passport para nó|Cliente|[Azure Active Passport de diretório para Node (npm)](https://www.npmjs.com/package/passport-azure-ad)|[Active Directory do Azure para Node (Github)](https://github.com/AzureAD/passport-azure-ad)||
|Java|Biblioteca de autenticação do Active Directory (ADAL) para Java|Cliente|[ADAL para Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)|[ADAL para Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)||
|.NET|Extensões de protocolo de identidade para o Microsoft .NET Framework 4,5|Servidor|[Microsoft.IdentityModel.Protocol.Extensions (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions)|[Extensões de modelo de identidade AD Azure para .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Manipulador de Token de Web JSON para o Microsoft .net Framework 4,5|Servidor|[System.IdentityModel.Tokens.Jwt (NuGet)](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt)|[Extensões de modelo de identidade AD Azure para .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Middleware OWIN que permite que um aplicativo para usar tecnologia da Microsoft para autenticação.|Servidor|[Microsoft.Owin.Security.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)||
|.NET|Middleware OWIN que permite que um aplicativo para usar OpenIDConnect para autenticação.|Servidor|[Microsoft.Owin.Security.OpenIdConnect (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Exemplo: [Web App-OpenIDConnecty-DotNet (Github)](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet)|
|.NET|Middleware OWIN que permite que um aplicativo para usar Web Services Federation para autenticação.|Servidor|[Microsoft.Owin.Security.WsFederation (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Exemplo: [Web App-WSFederation-DotNet (Github)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet)|

## <a name="scenarios"></a>Cenários

Aqui estão três cenários comuns na qual ADAL pode ser usado para autenticação.  

### <a name="authenticating-users-of-a-client-application-to-a-remote-resource"></a>Autenticação de usuários de um aplicativo cliente para um recurso remoto

Neste cenário, um desenvolvedor tem um cliente, como um aplicativo do WPF, que precise acessar um recurso remoto protegido por Azure AD, como um web API. Ele tem uma assinatura do Azure, sabe como invocar a API web downstream e sabe o locatário do Azure AD que usa a API da web. Como resultado, ele pode usar ADAL para facilitar a autenticação com Azure AD, delegando totalmente a experiência de autenticação para ADAL ou manipulando explicitamente as credenciais de usuário. ADAL torna fácil autenticar o usuário, obter um token de acesso e o token de atualização do Azure AD e depois use o token de acesso para fazer solicitações na Web API.

Para um exemplo de código que demonstra esse cenário usando autenticação do Azure AD, consulte [Aplicativo nativo de WPF do cliente para Web API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-server-application-to-a-remote-resource"></a>Autenticação de um aplicativo de servidor para um recurso remoto

Neste cenário, um desenvolvedor tem um aplicativo em execução em um servidor que precise acessar um recurso remoto protegido por Azure AD, como um web API. Ele tem uma assinatura do Azure, sabe como invocar o serviço downstream e sabe que o locatário do Azure AD a API web usa. Como resultado, ele pode usar ADAL para facilitar a autenticação com Azure AD manipulando explicitamente as credenciais do aplicativo. ADAL torna fácil recuperar um token de Azure AD usando credenciais de cliente do aplicativo e, em seguida, use esse token para fazer solicitações na Web API. ADAL também trata Gerenciando a vida útil do token de acesso armazenando em cache-lo e renová-lo conforme necessário. Para um exemplo de código que demonstra este cenário, consulte [Aplicativo de Console para Web API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-server-application-on-behalf-of-a-user-to-access-a-remote-resource"></a>Autenticação de um aplicativo de servidor em nome de um usuário para acessar um recurso remoto

Neste cenário, um desenvolvedor tem um aplicativo em execução em um servidor que precise acessar um recurso remoto protegido por Azure AD, como um web API. A solicitação também precisa ser feita em nome de um usuário no Azure AD. Ele tem uma assinatura do Azure, sabe como invocar downstream web API e sabe o Azure AD o serviço usa de locatários. Quando o usuário é autenticado para o aplicativo da web, o aplicativo pode obter um código de autorização para o usuário do Azure AD. O aplicativo da web pode usar ADAL para obter um token de acesso e atualize o token em nome de um usuário usando as credenciais de cliente e código de autorização associadas com o aplicativo do Azure AD. Depois que o aplicativo da web está em posse do token de acesso, ele pode chamar web API até expirar. Quando o token expira, o aplicativo da web pode usar ADAL para obter um novo token de acesso usando a atualização token que foi recebido anteriormente.


## <a name="see-also"></a>Consulte também

[Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md)

[Cenários de autenticação do Azure Active directory](active-directory-authentication-scenarios.md)

[Exemplos de código do Azure Active Directory](active-directory-code-samples.md)
