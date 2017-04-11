<properties
   pageTitle="Exemplos de código do Active Directory do Azure | Microsoft Azure"
   description="Um índice de exemplos de código do Active Directory do Azure, organizados por cenário."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="priyamohanram"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="azure-active-directory-code-samples"></a>Exemplos de código do Active Directory do Azure

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Você pode usar o Microsoft Azure Active Directory (AD Azure) para adicionar autenticação e autorização a seus aplicativos web e web APIs. Esta seção o links para exemplos de código que mostram como isso é feito e trechos de código que você pode usar em seus aplicativos. Na página de amostra de código, você encontrará leitura detalhada-me tópicos que ajudam com requisitos, instalação e configuração. E o código é comentado para ajudá-lo a compreender as seções críticas.

Para entender a situação básica para cada tipo de amostra, consulte cenários de autenticação para Azure AD.

Contribuem para nossos exemplos no GitHub: [Microsoft Azure Active Directory amostras e documentação](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="web-browser-to-web-application"></a>Navegador da Web para o aplicativo Web

Esses exemplos mostram como escrever um aplicativo web que direciona o navegador do usuário para entrá-los no Azure AD.



| Idioma/plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| C# / .NET | [Web App-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Use OpenID conectar (ASP.Net OpenID conectar OWIN middleware) para autenticar os usuários de um locatário do Azure AD.
| C# / .NET | [Web App-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | Um locatário vários aplicativo MVC .NET que usa OpenID conectar (ASP.Net OpenID conectar OWIN middleware) para autenticar os usuários de vários locatários do Azure AD.
| C# / .NET | [Web App-WSFederation-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | Use o Web Services Federation (ASP.Net Web Services Federation OWIN middleware) para autenticar os usuários de um locatário do Azure AD.






## <a name="single-page-application-spa"></a>Aplicativo de página única (SPA)

Este exemplo mostra como escrever um aplicativo de página única protegido com o Azure AD.  

| Idioma/plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| JavaScript, c# / .NET | [SinglePageApp-DotNet](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | Use ADAL para JavaScript e Azure AD para proteger um aplicativo de página única baseada em AngularJS implementado com um ASP.NET web API back-end.


## <a name="native-application-to-web-api"></a>Aplicativo nativo Web API

Esses exemplos de código mostram como criar aplicativos de cliente nativo que chamam web APIs que são protegidos por Azure AD. Eles usam [Biblioteca de autenticação do Azure AD (ADAL)](active-directory-authentication-libraries.md) e [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Idioma/plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| JavaScript | [NativeClient-MultiTarget-Cordova](https://github.com/Azure-Samples/active-directory-cordova-multitarget) | Use o plug-in ADAL para Apache Cordova para criar um aplicativo de Apache Cordova que chama um web API e usa Azure AD para autenticação.
| C# / .NET | [NativeClient-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) | Um aplicativo WPF .NET que chama um web API que é protegido usando Azure AD.
| C# / .NET | [NativeClient-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Um aplicativo da Windows Store que chama um web API que é protegido com Azure AD.
| C# / .NET | [NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) | Um aplicativo da Windows Store chamar um locatário vários web API protegido com o Azure AD.
| C# / .NET | [WebAPI-OnBehalfOf-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) | Um aplicativo de cliente nativo que chama um web API, que obtém um token para agir em nome de usuário original e, em seguida, usa o token para outra web API de chamadas.
| C# / .NET | [NativeClient-WindowsPhone8.1](https://github.com/Azure-Samples/active-directory-dotnet-windowsphone-8.1) | Um aplicativo da Windows Store para Windows Phone 8.1 que chama um web API que é protegido por Azure AD.
| ObjC | [NativeClient-iOS](https://github.com/Azure-Samples/active-directory-ios) | Um aplicativo iOS que chama um web API que requer Azure AD para autenticação.
| C# / .NET | [WebAPI-ManuallyValidateJwt-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation) | Um aplicativo de cliente nativo que inclui lógica para processar um token JWT em uma web API, em vez de usar OWIN middleware.
| C# / Xamarin | [NativeClient-Xamarin-Android](https://github.com/Azure-Samples/active-directory-xamarin-android) | Uma associação de Xamarin para o nativo Azure AD autenticação biblioteca (ADAL) para a biblioteca Android.
| C# / Xamarin | [NativeClient-Xamarin-iOS](https://github.com/Azure-Samples/active-directory-xamarin-ios) | Uma associação de Xamarin para o nativo Azure AD autenticação biblioteca (ADAL) para iOS.
| C# / Xamarin | [NativeClient-MultiTarget-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-multitarget) | Um projeto de Xamarin que direciona cinco plataformas e chama um web API que é protegido por Azure AD.
| C# / .NET | [NativeClient sem cabeça DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-headless) | Um aplicativo nativo que realiza a autenticação não interativo e chama um web API que é protegido por Azure AD.



## <a name="web-application-to-web-api"></a>Aplicativo Web Web API

Esses exemplos de código mostrar como usar [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) para criar aplicativos web que chamam web APIs que são protegidos por Azure AD.

| Idioma/plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| C# / .NET | [Web App-WebAPI-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect) | Chame um web API com permissões do usuário conectado.
|  C# / .NET | [Web App-WebAPI-OAuth2-AppIdentity-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity) | Chame um web API com as permissões do aplicativo.
| C# / .NET | [Web App-WebAPI-OAuth2-UserIdentity-Dotnet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | Adicione autorização com [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) para um aplicativo web existente para que ele pode chamar uma web API.
| JavaScript | [WebAPI-Nodejs](https://github.com/Azure-Samples/active-directory-node-webapi) | Configure um serviço de API REST que é integrado ao Azure AD para proteção de API. Inclui um servidor Node com uma API de Web.
| C# / .NET | [Web App-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-multitenant-openidconnect) |  Um locatário vários MVC web aplicativo que usa OpenID conectar (ASP.Net OpenID conectar OWIN middleware) para autenticar os usuários de um locatário do Azure AD. Usa um código de autorização para chamar a API do gráfico.

## <a name="server-or-daemon-application-to-web-api"></a>Servidor ou Daemon aplicativo Web API

Esses exemplos de código mostram como criar um aplicativo daemon ou servidor que recebe os recursos de uma web API usando [Biblioteca de autenticação do Azure AD (ADAL)](active-directory-authentication-libraries.md) e [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Idioma/plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| C# / .NET | [Daemon DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) | Um aplicativo de console chama um web API. A credencial do cliente é uma senha.
| C# / .NET | [Daemon-CertificateCredential-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) | Um aplicativo de console que chama um web API. A credencial do cliente é um certificado.


## <a name="calling-azure-ad-graph-api"></a>Chamando API do Azure AD Graph

Esses exemplos de código mostram como criar aplicativos que chamam a API do Azure AD Graph para ler e gravar dados de diretório.

| Idioma/plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| Java | [Web App-GraphAPI-Java](https://github.com/Azure-Samples/active-directory-java-graphapi-web) | Um aplicativo web que usa a API do gráfico para acessar os dados de diretório do Azure AD.
| PHP | [Web App-GraphAPI-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-web) | Um aplicativo web que usa a API do gráfico para acessar os dados de diretório do Azure AD.
| C# / .NET | [Web App-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Um aplicativo web que usa a API do gráfico para acessar os dados de diretório do Azure AD.
| C# / .NET | [ConsoleApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-console) | Este aplicativo de console demonstra chamadas comuns de leitura e gravação para a API do gráfico e mostra como executar atribuição de licença de usuário e atualizar foto miniatura e links de um usuário.
| C# / .NET | [ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-diffquery) | Um aplicativo de console que usa a consulta diferencial da API do gráfico para obter periódicos alterações em objetos de usuário em um locatário do Azure AD.
| C# / .NET | [Web App-GraphAPI-DirectoryExtensions-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-directoryextensions-web) | Um aplicativo MVC usa consultas de API do gráfico para gerar um organograma de empresa simples.
| PHP | [Web App-GraphAPI-DirectoryExtensions-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-directoryextensions-web) | Um aplicativo PHP que chama a API do gráfico para registrar uma extensão e, em seguida, ler, atualizar e excluir valores no atributo de extensão.


## <a name="authorization"></a>Autorização

Esses exemplos de código mostram como usar o Azure AD para autorização.

| Idioma/plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| C# / .NET | [Web App-GroupClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Execute o controle de acesso baseado em função (RBAC) usando declarações de grupo do Active Directory do Azure em um aplicativo integrado ao Azure AD.
| C# / .NET | [Web App-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Execute o controle de acesso baseado em função (RBAC) usando funções de aplicativo do Active Directory do Azure em um aplicativo integrado ao Azure AD.


## <a name="legacy-walkthroughs"></a>Orientações herdadas

Essas orientações usam tecnologia ligeiramente mais antiga, mas ainda podem ser de seu interesse.

| Idioma/plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| C# / .NET | [Autorização baseada em função e baseada em ACL em um aplicativo do Microsoft Azure AD](http://go.microsoft.com/fwlink/?LinkId=331694) | Execute autorização baseado em função (RBAC) e a autorização baseada em ACL em um aplicativo que é integrado ao Azure AD.
| C# / .NET |  [Autenticação de AAL - aplicativo da Windows Store para serviço REST-](http://go.microsoft.com/fwlink/?LinkId=330605) |  Use [Biblioteca de autenticação do Azure AD (ADAL)](active-directory-authentication-libraries.md) (antigo AAL) para Windows Store Beta para adicionar recursos de autenticação de usuário para um aplicativo da Windows Store.
| C# / .NET | [Autenticação de ADAL - aplicativo nativo ao serviço REST - com AAD por meio da caixa de diálogo navegador](http://go.microsoft.com/fwlink/?LinkId=259814) |  Use [Biblioteca de autenticação do Azure AD (ADAL)](active-directory-authentication-libraries.md) para adicionar recursos de autenticação de usuário para um cliente WPF.
| C# / .NET | [Autenticação de ADAL - aplicativo nativo ao serviço REST - com ACS por meio da caixa de diálogo navegador](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) |  Use [Biblioteca de autenticação do Azure AD (ADAL)](active-directory-authentication-libraries.md) e [2.0 de serviço de controle de acesso (ACS)](http://msdn.microsoft.com/library/azure/hh147631.aspx) para adicionar recursos de autenticação de usuário para um cliente WPF.
| C# / .NET | [ADAL - autenticação de servidor para servidor](http://go.microsoft.com/fwlink/?LinkId=259816) | Use [Biblioteca de autenticação do Azure AD (ADAL)](active-directory-authentication-libraries.md) para proteger chamadas de serviço de um processo de lado do servidor para um serviço de MVC4 Web API REST.
| C# / .NET | [Adicionando logon seu aplicativo Web usando o Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Configure um aplicativo .NET para executar web logon único contra diretório corporativo Azure AD.
| C# / .NET | [Desenvolver aplicativos Web do locatário vários com Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | Use o Azure AD para adicionar o logon único e recursos de acesso do diretório de aplicativos .NET para trabalhar por várias empresas.
JAVA | [Aplicativo de amostra Java do Azure AD Graph API](http://go.microsoft.com/fwlink/?LinkId=263969) | Use a API do gráfico para acessar os dados de diretório do Azure AD.
PHP | [Aplicativo de exemplo PHP para Azure AD Graph API](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) | Use a API do gráfico para acessar os dados de diretório do Azure AD.
| C# / .NET | [Aplicativo de amostra do Azure AD Graph API](http://go.microsoft.com/fwlink/?LinkID=262648) | Use a API do gráfico para acessar os dados de diretório do Azure AD.
| C# / .NET | [Aplicativo de amostra para consulta diferencial do Azure AD Graph](http://go.microsoft.com/fwlink/?LinkId=275398) | Use a consulta diferencial da API do gráfico para obter periódicas alterações em objetos de usuário em um locatário do Azure AD.
| C# / .NET | [Aplicativo de amostra para integrar o aplicativo em nuvem de vários locatários do Azure AD](http://go.microsoft.com/fwlink/?LinkId=275397) | Integre um aplicativo de vários locatário Azure AD.
| C# / .NET | [Proteger um aplicativo da Windows Store e o serviço da Web REST usando o Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Criar um recurso de web simples API e um aplicativo de cliente da Windows Store usando o Azure AD e a [Biblioteca de autenticação do Azure AD (ADAL)](active-directory-authentication-libraries.md).
| C# / .NET| [Usando o gráfico API para consultar Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Configure um aplicativo do Microsoft .NET para usar a API do Azure AD Graph para acessar dados de um diretório de locatário do Azure AD.

## <a name="see-also"></a>Consulte também

##### <a name="other-resources"></a>Outros recursos

[Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md)

[Azure AD Graph API conceitual e referência](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteca de auxiliar de API do Azure AD Graph](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

