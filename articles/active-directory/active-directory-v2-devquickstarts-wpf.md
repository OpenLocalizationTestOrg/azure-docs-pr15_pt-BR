<properties
pageTitle="Aplicativo nativo do Active Directory do Azure v 2.0 .NET | Microsoft Azure"
description="Como criar um aplicativo nativo de .NET que assina contas de trabalho ou da escola e usuários com os dois Account pessoal da Microsoft."
services="active-directory"
documentationCenter=""
authors="dstrockis"
manager="mbaldwin"
editor=""/>

<tags
ms.service="active-directory"
ms.workload="identity"
ms.tgt_pltfrm="na"
ms.devlang="dotnet"
ms.topic="article"
ms.date="07/30/2016"
ms.author="dastrock; vittorib"/>

# <a name="add-sign-in-to-a-windows-desktop-app"></a>Adicionar entrada a um aplicativo de área de trabalho do Windows

Com o ponto de extremidade v 2.0, você pode adicionar contas de trabalho ou da escola e autenticação para seus aplicativos de desktop com suporte para ambas as contas pessoais do Microsoft rapidamente.  Ele também permite que seu aplicativo para se comunicar com segurança com uma web de back-end api, bem como [O Microsoft Graph](https://graph.microsoft.io) e algumas das [APIs de Unificação do Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [AZURE.NOTE] Nem todos os recursos e cenários do Azure Active Directory (AD) são suportados pelo ponto de extremidade v 2.0.  Para determinar se você deve usar o ponto de extremidade de v 2.0, leia sobre as [limitações da versão 2.0](active-directory-v2-limitations.md).

Para [aplicativos nativos .NET que executam em um dispositivo](active-directory-v2-flows.md#mobile-and-native-apps), o Azure AD fornece a biblioteca de autenticação de identidade do Microsoft ou MSAL.  Única finalidade do MSAL na vida é tornar mais fácil para seu aplicativo obter tokens para chamar serviços da web.  Para demonstrar apenas como é fácil, aqui será criamos um aplicativo de lista de tarefas pendentes do .NET WPF que:

- O usuário se inscreve & obtém acesso tokens usando o [protocolo de autenticação de OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow).
- Com segurança chama um serviço da web de lista de tarefas pendentes, que também é protegido pelo OAuth 2.0 de back-end.
- Sair do usuário.

## <a name="download-sample-code"></a>Baixe o código de amostra

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet).  Para acompanhar, você pode [Baixar esqueleto do aplicativo como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) ou clonar a estrutura:

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

O aplicativo concluído é fornecido no final deste tutorial também.

## <a name="register-an-app"></a>Registrar um aplicativo
Criar um novo aplicativo no [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou siga estas [etapas detalhadas](active-directory-v2-app-registration.md).  Certifique-se de:

- Copiar a **Id de aplicativo** atribuída ao seu aplicativo, você precisará dela breve.
- Adicione **a plataforma para o aplicativo** .

## <a name="install--configure-msal"></a>Instalar e configurar MSAL
Agora que você tem um aplicativo registrado com a Microsoft, você pode instalar MSAL e escrever seu código relacionadas a identidade.  Em ordem para MSAL sejam capazes de se comunicar o ponto de extremidade de v 2.0, você precisa fornecê-lo com algumas informações sobre seu registro de aplicativo.

-   Comece adicionando MSAL ao projeto TodoListClient usando o Package Manager Console.

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

-   No projeto TodoListClient, abra `app.config`.  Substitua os valores dos elementos na `<appSettings>` seção para refletir os valores de entrada para o portal de registro do aplicativo.  Seu código fará referência esses valores sempre que ele usa MSAL.
    -   O `ida:ClientId` é a **Id de aplicativo** do aplicativo que você copiou do portal.

- No projeto de serviço de lista de tarefas pendentes, abra `web.config` na raiz do projeto.  
    - Substituir o `ida:Audience` valor com a mesma **Id do aplicativo** a partir do portal.

## <a name="use-msal-to-get-tokens"></a>Use MSAL para obter tokens
O princípio básico atrás MSAL é que sempre que seu aplicativo precisa de um token de acesso, você simplesmente chamar `app.AcquireToken(...)`, e MSAL faz o resto.  

-   No `TodoListClient` project, abrir `MainWindow.xaml.cs` e localize o `OnInitialized(...)` método.  A primeira etapa é inicializar o aplicativo `PublicClientApplication` -classe principal do MSAL representando aplicativos nativos.  Isso é onde você passa MSAL as coordenadas que ele precisa se comunicar com o Azure AD e informá-lo como tokens de cache.

```C#
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

- Quando o aplicativo for iniciado, queremos verificar e veja se o usuário já estiver conectado no aplicativo.  No entanto, não queremos invocar uma interface do usuário entrar ainda - que vamos fazer o usuário clique em "Entrar" para fazê-lo.  Também na `OnInitialized(...)` método:

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.
    
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

- Se o usuário não está conectado e clicam no botão "Entrar", queremos invocar um logon UI e deixar o usuário inserir suas credenciais.  Implemente o manipulador de botão entrar:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }

        MessageBox.Show(message);
    }

    return;
}


}
```

- Se o usuário com êxito sinais-in, MSAL receberá e um token de cache para você e você poderá ligar para o `GetTodoList()` método com confiança.  Tudo o que resta para realizar tarefas de um usuário é implementar o `GetTodoList()` método.

```C#
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>Executar

Parabéns! Agora você tem um aplicativo de .NET WPF de trabalho que tem a capacidade de autenticar usuários & com segurança chamar APIs de Web usando OAuth 2.0.  Executar os dois projetos e entre com uma conta da Microsoft ou uma conta corporativa ou escolar.  Adicione tarefas à lista de tarefas pendentes do usuário.  Sair e entrar novamente como outro usuário para exibir sua lista de tarefas pendentes.  Feche o aplicativo e execute-o novamente.  Observe como a sessão do usuário permanecerá intacta, esse é porque o aplicativo armazena tokens em um arquivo local.

MSAL torna mais fácil de incorporar recursos de identidade comuns em seu aplicativo, usando contas de pessoais e de trabalho.  Cuida de todo o trabalho sujo para você - gerenciamento de cache, o suporte do protocolo de OAuth, apresentando o usuário com um logon UI, atualizando tokens expirados e muito mais.  Tudo o que você realmente precisa saber é uma única chamada de API, `app.AcquireTokenAsync(...)`.

Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um. zip aqui](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>Próximas etapas

Agora, você pode mover em tópicos mais avançados.  Talvez você queira experimentar:

- [Protegendo a API do Web TodoListService com o ponto de extremidade de v 2.0](active-directory-v2-devquickstarts-dotnet-api.md)

Para obter recursos adicionais, consulte:  

- [O guia do desenvolvedor v 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Marca de "msal" StackOverflow >>](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para nossos produtos

Recomendamos que você obtenha as notificações de quando ocorrências de segurança ocorrerem visitando [esta página](https://technet.microsoft.com/security/dd252948) e assinar alertas de segurança comunicado.
