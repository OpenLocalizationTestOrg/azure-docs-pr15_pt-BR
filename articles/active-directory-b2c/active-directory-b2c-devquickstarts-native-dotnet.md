<properties
    pageTitle="Active Directory do Azure B2C | Microsoft Azure"
    description="Como criar um aplicativo de área de trabalho do Windows que inclui entrar, inscrição, e gerenciamento de perfil usando B2C do Azure Active Directory."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>B2C do Azure AD: Criar um aplicativo de área de trabalho do Windows

Usando B2C Azure Active Directory (AD Azure), você pode adicionar recursos de gerenciamento de identidade de autoatendimento poderosos para o seu aplicativo da área de trabalho em algumas etapas curtos. Este artigo mostrará como criar um aplicativo de "lista de tarefas pendentes".NET Windows Presentation Foundation (WPF) que inclua o gerenciamento de usuários inscrição, entrar e perfil. O aplicativo incluirá suporte a inscrição e entrar usando um nome de usuário ou o email. Ele também incluirá suporte a inscrição e entrar usando contas sociais como o Facebook e do Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório de Azure AD B2C

Antes de poder usar Azure AD B2C, você deve criar um diretório ou de locatários.  Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e muito mais. Se você não tenha um, [Crie um diretório de B2C](active-directory-b2c-get-started.md) antes de continuar neste guia.

## <a name="create-an-application"></a>Criar um aplicativo

Em seguida, você precisa criar um aplicativo no seu diretório B2C. Isso fornece informações de Azure AD que ele precisa se comunicar com segurança com seu aplicativo. Para criar um aplicativo, siga [estas instruções](active-directory-b2c-app-registration.md).  Certifique-se de:

- Inclua um **cliente nativo** no aplicativo.
- Copiar a **Redirecionar URI** `urn:ietf:wg:oauth:2.0:oob`. É a URL padrão para este exemplo de código.
- Copie a **ID do aplicativo** que está atribuído a seu aplicativo. Você precisará dele mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar suas regras

No Azure AD B2C, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Este exemplo de código contém três experiências de identidade: inscrever-se, entrar e Editar perfil. Você precisa criar uma política para cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Quando você cria as três políticas, certifique-se a:

- Escolha o **ID de usuário inscrição** ou **Email inscrição** na lâmina de provedores de identidade.
- Escolha o **nome de exibição** e outros atributos de inscrição na sua política de inscrição.
- Escolha declarações de **nome para exibição** e a **ID do objeto** como declarações de aplicativo para cada política. Você pode escolher outras reivindicações também.
- Copie o **nome** de cada política depois de criá-lo. Ele deve ter o prefixo `b2c_1_`.  Posteriormente, você precisará esses nomes de política.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Após ter criado com êxito as três políticas, você está pronto para criar seu aplicativo.

## <a name="download-the-code"></a>Baixe o código

O código para este tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Para criar a amostra enquanto você trabalha, você pode [baixar um projeto reduzido como um arquivo. zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). Também é possível clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

O aplicativo concluído também está [disponível como um arquivo. zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) ou na `complete` ramificação do mesmo repositório.

Depois de baixar o código de exemplo, abra o arquivo. sln Visual Studio para começar. O `TaskClient` projeto é o aplicativo de área de trabalho do WPF que o usuário interage com. Para os fins deste tutorial, ele chama um web de back-end tarefa API, hospedado no Azure, que armazena a lista de tarefas pendentes de cada usuário.  Você não precisa criar web API, nós já estiver em execução para você.

Para saber como um web API autentica com segurança solicitações usando Azure AD B2C, confira o [web API Introdução artigo](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Executar políticas
Seu aplicativo se comunica com o Azure AD B2C enviando mensagens de autenticação que especificam a política que desejam executar como parte da solicitação HTTP. Para aplicativos de desktop do .NET, você pode usar a visualização de biblioteca de autenticação da Microsoft (MSAL) para enviar mensagens de autenticação OAuth 2.0, executar políticas e obter tokens que chamam APIs da web.

### <a name="install-msal"></a>Instalar o MSAL
Adicionar MSAL para o `TaskClient` projeto usando o Console do Gerenciador de pacote do Visual Studio.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Insira os detalhes de B2C
Abra o arquivo `Globals.cs` e substitua os valores de propriedade pelo seu próprio. Essa classe é usada em todo `TaskClient` aos valores de referência comumente usada.

```C#
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


### <a name="create-the-publicclientapplication"></a>Criar a PublicClientApplication
A classe principal da MSAL é `PublicClientApplication`. Esta classe representa seu aplicativo no sistema Azure AD B2C. Quando initalizes o aplicativo, criar uma instância de `PublicClientApplication` em `MainWindow.xaml.cs`. Isso pode ser usado em toda a janela.

```C#
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app, 
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };
    
    ...
```

### <a name="initiate-a-sign-up-flow"></a>Iniciar um fluxo de inscrição
Quando um usuário opta para sinais para cima, você deseja iniciar um fluxo de inscrição que usa a inscrição política que você criou. Usando MSAL, que você acabou de chamadas `pca.AcquireTokenAsync(...)`. Os parâmetros que você passa para `AcquireTokenAsync(...)` determinar quais token receber, a política usada na solicitação de autenticação e muito mais.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user 
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
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

### <a name="initiate-a-sign-in-flow"></a>Iniciar um fluxo de entrada
Você pode iniciar um fluxo de entrada da mesma maneira que você inicia um fluxo de inscrição. Quando um usuário entra, verifique a mesma chamada MSAL, desta vez usando sua política de entrada:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>Iniciar um fluxo de Editar perfil
Novamente, você pode executar uma política de Editar perfil da mesma maneira:

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

Em todos esses casos, MSAL ou retorna um token de `AuthenticationResult` ou gera uma exceção. Sempre que você obtenha um token do MSAL, você pode usar o `AuthenticationResult.User` objeto para atualizar os dados do usuário no aplicativo, como a interface do usuário. ADAL também armazena o token para uso em outras partes do aplicativo.


### <a name="check-for-tokens-on-app-start"></a>Verificar se há tokens em Iniciar aplicativo
Você também pode usar MSAL para controlar o estado do usuário entrar.  Nesse aplicativo, queremos que o usuário permaneça conectado no mesmo depois que eles feche o aplicativo e abra-o novamente.  Volta dentro do `OnInitialized` substituir, use do MSAL `AcquireTokenSilent` método para verificar se há tokens de cache:

```C#
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
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
```

## <a name="call-the-task-api"></a>Chamar a API de tarefa
Agora, você usou MSAL executar políticas e obter tokens.  Quando você quiser usar um esses tokens para chamar a API de tarefa, você pode usar novamente do MSAL `AcquireTokenSilent` método para verificar se há tokens de cache:

```C#
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
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
    ...
```

Quando a chamada para `AcquireTokenSilentAsync(...)` sucesso e um token for encontrado no cache, você pode adicionar o token para o `Authorization` cabeçalho da solicitação HTTP. Web tarefa API usará esse cabeçalho para autenticar a solicitação para ler a lista de tarefas pendentes do usuário:

```C#
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>Sair do usuário
Finalmente, você pode usar MSAL para encerrar uma sessão do usuário com o aplicativo quando o usuário seleciona **Sair**.  Ao usar MSAL, isso é feito desmarcando todos os tokens do cache de token:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>Executar o aplicativo de amostra

Finalmente, criar e executar o exemplo.  Inscreva-se para o aplicativo usando um nome de usuário ou endereço de email. Sair e entrar novamente o mesmo usuário. Edite perfil do usuário. Sair e inscrever-se usando um usuário diferente.

## <a name="add-social-idps"></a>Adicionar IDPs sociais

Atualmente, o aplicativo suporta somente inscrição de usuário e entrar que usam **contas locais**. Estas são contas armazenadas no seu diretório B2C que usam um nome de usuário e senha. Usando o Azure AD B2C, você pode adicionar suporte para outros provedores de identidade (IDPs) sem alterar nenhum código.

Para adicionar IDPs sociais ao seu aplicativo, comece seguindo as instruções detalhadas nesses artigos. Para cada IDP desejar oferecer suporte, você precisa registrar um aplicativo no sistema e obter uma identificação de cliente.

- [Configurar o Facebook como um IDP](active-directory-b2c-setup-fb-app.md)
- [Configurar o Google como um IDP](active-directory-b2c-setup-goog-app.md)
- [Configurar Amazon como um IDP](active-directory-b2c-setup-amzn-app.md)
- [Configurar LinkedIn como um IDP](active-directory-b2c-setup-li-app.md)

Depois de adicionar os provedores de identidade ao seu diretório B2C, você precisa editar cada uma das suas políticas de três para incluir as novas IDPs, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md). Depois de salvar suas políticas, execute o aplicativo novamente. Você deve ver os novo IDPs adicionados como entrar e opções de inscrição em cada uma das sua identidade experiências.

Você pode experimentar com suas políticas e observar os efeitos em seu aplicativo de amostra. Adicionar ou remover IDPs, manipular declarações de aplicativo ou alterar os atributos de inscrição. Experimente até que você possa ver como políticas, solicitações de autenticação e MSAL unir.

Para referência, o exemplo concluído [é fornecido como um arquivo. zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Você também pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
