<properties
    pageTitle="Windows Phone do Azure AD Introdução | Microsoft Azure"
    description="Como criar um aplicativo do Windows Phone que se integra ao Azure AD para entrar e chama Azure AD protegido APIs usando OAuth."
    services="active-directory"
    documentationCenter="windows"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>



# <a name="integrate-azure-ad-with-a-windows-phone-app"></a>Integrar o Azure AD com um aplicativo do Windows Phone

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Se você está desenvolvendo um aplicativo do Windows Phone 8.1, Azure AD torna mais simples e direto para você autenticar os usuários com suas contas do Active Directory.  Ele também permite que seu aplicativo com segurança consumir qualquer web API protegido por Azure AD, como as APIs do Office 365 ou a API do Azure.

> [AZURE.NOTE] Este exemplo de código usa v 2.0 ADAL.  Para a tecnologia mais recente, talvez você queira em vez disso, tente nosso [Tutorial Universal do Windows usando v ADAL 3.0](active-directory-devquickstarts-windowsstore.md).  Se você estiver realmente criando um aplicativo para Windows Phone 8.1, este é o lugar certo.  V 2.0 ADAL é ainda totalmente suportada e é a maneira recomendada de desenvolvimento de aplicativos agianst Windows Phone 8.1 usando Azure AD.

Para clientes nativos .NET que precise acessar recursos protegidos, Azure AD fornece a biblioteca de autenticação do Active Directory ou ADAL.  Única finalidade do ADAL na vida é tornar mais fácil para o aplicativo obter tokens de acesso.  Para demonstrar apenas como é fácil, aqui será criamos uma "Pesquisador de diretório" aplicativo do Windows Phone 8.1 que:

-   Obtém acesso tokens para chamar a API do Azure AD Graph usando o [protocolo de autenticação de OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Procura um diretório usuários com um determinado UPN.
-   Usuários de sinais check-out.

Para criar o aplicativo de trabalho concluído, você precisará:

2. Registre seu aplicativo com o Azure AD.
3. Instalar e configurar ADAL.
5. Use ADAL para obter tokens do Azure AD.

Para começar a, [baixar um projeto reduzido](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/skeleton.zip) ou [baixar o exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip).  Cada um é uma solução do Visual Studio 2013.  Você também será preciso um locatário do Azure AD na qual você pode criar usuários e registrar um aplicativo.  Se você ainda não tiver um locatário, [Saiba como obter uma](active-directory-howto-tenant.md).

## <a name="1-register-the-directory-searcher-application"></a>*1. Registre o aplicativo do Pesquisador de diretório*
Para habilitar o aplicativo obter tokens, você precisará primeiro registrá-lo no seu locatário do Azure AD e conceder permissão para acessar a API do Azure AD gráfico:

-   Entrar no [Portal de gerenciamento do Azure](https://manage.windowsazure.com)
-   No painel de navegação à esquerda, clique no **Active Directory**
-   Selecione um locatário no qual deseja registrar o aplicativo.
-   Clique na guia **aplicativos** e clique em **Adicionar** na parte inferior registradora.
-   Siga os avisos e criar um novo **Aplicativo cliente nativo**.
    -   O **nome** do aplicativo descreverá seu aplicativo para usuários finais
    -   O **Uri redirecionar** é uma combinação de esquema e a cadeia de caracteres que Azure AD usará para retornar respostas token.  Insira um valor de espaço reservado por agora, por exemplo, `http://DirectorySearcher`.  Podemos vai substituir esse valor mais tarde.
-   Após concluir registro, AAD atribuirá seu aplicativo um identificador de cliente exclusivo.  Você precisará desse valor nas próximas seções, portanto copiá-lo na guia **Configurar** .
- Também na guia **Configurar** , localize a seção "Permissões para outros aplicativos".  Para o aplicativo "Azure Active Directory", adicione a permissão de **diretório da organização o acesso** em **Permissões delegados**.  Isso permitirá que seu aplicativo para consultar a API de gráfico para os usuários.

## <a name="2-install--configure-adal"></a>*2. instalar e configurar ADAL*
Agora que você tem um aplicativo no Azure AD, você pode instalar ADAL e escrever seu código relacionadas a identidade.  Ordem de ADAL sejam capazes de se comunicar com o Azure AD, você precisa fornecê-lo com algumas informações sobre seu registro de aplicativo.
-   Comece adicionando ADAL para o projeto de DirectorySearcher usando o Package Manager Console.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-   No projeto DirectorySearcher, abra `MainPage.xaml.cs`.  Substitua os valores no `Config Values` região para refletir os valores que você insere no Portal do Azure.  Seu código fará referência esses valores sempre que ele usa ADAL.
    -   O `tenant` é o domínio de seu locatário do Azure AD, por exemplo, contoso.onmicrosoft.com
    -   O `clientId` é o clientId do aplicativo que você copiou do portal.
-   Agora, você precisa descobrir o uri de retorno de chamada para seu aplicativo do Windows Phone.  Definir um ponto de interrupção nessa linha no `MainPage` método:

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
- Executar o aplicativo e reserve um copiar o valor do `redirectUri` quando o ponto de interrupção é atingido.  Ele deve parecer com

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

- Na guia **Configurar** do seu aplicativo no Portal de gerenciamento do Azure, substitua o valor da **RedirectUri** com este valor.  

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. usar ADAL para obter Tokens de AAD*
O princípio básico atrás ADAL é que sempre que seu aplicativo precisa de um token de acesso, ele simplesmente chama `authContext.AcquireToken(…)`, e ADAL faz o resto.  

-   A primeira etapa é inicializar o aplicativo `AuthenticationContext` -ADAL da classe principal.  Isso é onde você passa ADAL as coordenadas que ele precisa se comunicar com o Azure AD e informá-lo como tokens de cache.

```C#
public MainPage()
{
    ...

    // ADAL for Windows Phone 8.1 builds AuthenticationContext instances through a factory
    authContext = AuthenticationContext.CreateAsync(authority).GetResults();
}
```

- Agora localizar o `Search(...)` método, que será chamado quando os usuário cliks "Search" botão na interface do usuário do aplicativo.  Este método faz uma solicitação GET à API do Azure AD Graph a consulta para os usuários cuja UPN começa com o termo de pesquisa fornecidos.  Mas para consultar a API do gráfico, você precisa incluir uma access_token na `Authorization` cabeçalho da solicitação - é onde entra ADAL.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...

    // Try to get a token without triggering any user prompt.
    // ADAL will check whether the requested token is in ADAL's token cache or can otherwise be obtained without user interaction.
    AuthenticationResult result = await authContext.AcquireTokenSilentAsync(graphResourceId, clientId);
    if (result != null && result.Status == AuthenticationStatus.Success)
    {
        // A token was successfully retrieved.
        QueryGraph(result);
    }
    else
    {
        // Acquiring a token without user interaction was not possible.
        // Trigger an authentication experience and specify that once a token has been obtained the QueryGraph method should be called
        authContext.AcquireTokenAndContinue(graphResourceId, clientId, redirectURI, QueryGraph);
    }
}
```
- Se autenticação interativa for necessária, ADAL usará o agente de autenticação de Web do Windows Phone (WAB) e [modelo de continuação](http://www.cloudidentity.com/blog/2014/06/16/adal-for-windows-phone-8-1-deep-dive/) para exibir o Azure AD entre na página.  Quando o usuário entra, seu aplicativo precisa passar ADAL os resultados da interação WAB.  Isso é tão simple quanto Implementando o `ContinueWebAuthentication` interface:

```C#
// This method is automatically invoked when the application
// is reactivated after an authentication interaction through WebAuthenticationBroker.
public async void ContinueWebAuthentication(WebAuthenticationBrokerContinuationEventArgs args)
{
    // pass the authentication interaction results to ADAL, which will
    // conclude the token acquisition operation and invoke the callback specified in AcquireTokenAndContinue.
    await authContext.ContinueAcquireTokenAsync(args);
}
```

- Agora é hora de usar o `AuthenticationResult` que ADAL retornado para o seu aplicativo.  No `QueryGraph(...)` retorno de chamada, anexar a access_token você adquiriu a solicitação GET no cabeçalho de autorização:

```C#
private async void QueryGraph(AuthenticationResult result)
{
    if (result.Status != AuthenticationStatus.Success)
    {
        MessageDialog dialog = new MessageDialog(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", result.Error, result.ErrorDescription), "Sorry, an error occurred while signing you in.");
        await dialog.ShowAsync();
    }

    // Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

    ...
}
```
- Você também pode usar o `AuthenticationResult` objeto para exibir informações sobre o usuário do seu aplicativo. No `QueryGraph(...)` método, use o resultado para mostrar a id do usuário na página:

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
- Finalmente, você pode usar ADAL entre o usuário sair do aplicativo também.  Quando o usuário clica no botão "Sair", queremos Certifique-se de que a próxima chamada ao `AcquireTokenSilentAsync(...)` falhará.  Com ADAL, isso é tão fácil quanto limpar o cache de token:

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

Parabéns! Agora você tem um aplicativo do Windows Phone tem a capacidade de autenticar os usuários, com segurança chamar APIs de Web usando OAuth 2.0 de trabalho e obtenha informações básicas sobre o usuário.  Se você ainda não começou, agora é o tempo para preencher seu locatário com alguns usuários.  Executar o aplicativo de DirectorySearcher e entrar em um desses usuários.  Procurar por outros usuários com base em seu UPN.  Feche o aplicativo e execute-o novamente.  Observe como a sessão do usuário permanecerá intacta.  Sair e entrar novamente como outro usuário.

ADAL facilita a incorporar todos esses recursos de identidade comuns em seu aplicativo.  Cuida de todo o trabalho sujo para você - gerenciamento de cache, o suporte do protocolo de OAuth, apresentando o usuário com um logon UI, atualizando tokens expirados e muito mais.  Tudo o que você realmente precisa saber é uma única chamada de API, `authContext.AcquireToken*(…)`.

Para referência, o exemplo concluído (sem os valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip).  Agora você pode mover-se em cenários de identidade adicional.  Talvez você queira experimentar:

[Proteger uma Web .NET API com Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]