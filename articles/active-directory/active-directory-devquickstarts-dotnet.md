<properties
    pageTitle="Azure AD .NET Introdução | Microsoft Azure"
    description="Como criar um aplicativo de área de trabalho do .NET Windows que se integra ao Azure AD para entrar e chama Azure AD protegido APIs usando OAuth."
    services="active-directory"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="integrate-azure-ad-into-a-windows-desktop-wpf-app"></a>Integrar o Azure AD em um aplicativo de Desktop do WPF do Windows

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Se você está desenvolvendo um aplicativo de desktop, Azure AD torna mais simples e direto para você autenticar os usuários com suas contas do Active Directory.  Ele também permite que seu aplicativo com segurança consumir qualquer web API protegido por Azure AD, como as APIs do Office 365 ou a API do Azure.

Para clientes nativos .NET que precise acessar recursos protegidos, Azure AD fornece a biblioteca de autenticação do Active Directory ou ADAL.  Única finalidade do ADAL na vida é tornar mais fácil para o aplicativo obter tokens de acesso.  Para demonstrar apenas como é fácil, aqui será criamos um aplicativo de lista de tarefas pendentes do .NET WPF que:

-   Obtém acesso tokens para chamar a API do Azure AD Graph usando o [protocolo de autenticação de OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Procura um diretório usuários com um alias de determinado.
-   Usuários de sinais check-out.

Para criar o aplicativo de trabalho concluído, você precisará:

2. Registre seu aplicativo com o Azure AD.
3. Instalar e configurar ADAL.
5. Use ADAL para obter tokens do Azure AD.

Para começar a, [Baixar a estrutura do aplicativo](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) ou [baixar o exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Você também será preciso um locatário do Azure AD na qual você pode criar usuários e registrar um aplicativo.  Se você ainda não tiver um locatário, [Saiba como obter uma](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>*1. Registre o aplicativo de DirectorySearcher*
Para habilitar o aplicativo obter tokens, você precisará primeiro registrá-lo no seu locatário do Azure AD e conceder permissão para acessar a API do Azure AD gráfico:

-   Entrar no Portal de gerenciamento do Azure
-   No painel de navegação à esquerda, clique no **Active Directory**
-   Selecione um locatário no qual deseja registrar o aplicativo.
-   Clique na guia **aplicativos** e clique em **Adicionar** na parte inferior registradora.
-   Siga os avisos e criar um novo **Aplicativo cliente nativo**.
    -   O **nome** do aplicativo descreverá seu aplicativo para usuários finais
    -   O **Uri redirecionar** é uma combinação de esquema e a cadeia de caracteres que Azure AD usará para retornar respostas token.  Insira um valor específico para seu aplicativo, por exemplo, `http://DirectorySearcher`.
-   Após concluir registro, AAD atribuirá seu aplicativo um identificador de cliente exclusivo.  Você precisará desse valor nas próximas seções, portanto copiá-lo na guia **Configurar** .
- Também na guia **Configurar** , localize a seção "Permissões para outros aplicativos".  Para o aplicativo "Azure Active Directory", adicione a permissão de **diretório da organização o acesso** em **Permissões delegados**.  Isso permitirá que seu aplicativo para consultar a API de gráfico para os usuários.

## <a name="2-install--configure-adal"></a>*2. instalar e configurar ADAL*
Agora que você tem um aplicativo no Azure AD, você pode instalar ADAL e escrever seu código relacionadas a identidade.  Ordem de ADAL sejam capazes de se comunicar com o Azure AD, você precisa fornecê-lo com algumas informações sobre seu registro de aplicativo.
-   Comece adicionando ADAL para o projeto de DirectorySearcher usando o Package Manager Console.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-   No projeto DirectorySearcher, abra `app.config`.  Substitua os valores dos elementos na `<appSettings>` seção para refletir os valores que você insere no Portal do Azure.  Seu código fará referência esses valores sempre que ele usa ADAL.
    -   O `ida:Tenant` é o domínio de seu locatário do Azure AD, por exemplo, contoso.onmicrosoft.com
    -   O `ida:ClientId` é o clientId do aplicativo que você copiou do portal.
    -   O `ida:RedirectUri` é o redirecionamento url registrado no portal.

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. usar ADAL para obter Tokens de AAD*
O princípio básico atrás ADAL é que sempre que seu aplicativo precisa de um token de acesso, ele simplesmente chama `authContext.AcquireTokenAsync(...)`, e ADAL faz o resto.  

-   No `DirectorySearcher` project, abrir `MainWindow.xaml.cs` e localize o `MainWindow()` método.  A primeira etapa é inicializar o aplicativo `AuthenticationContext` -ADAL da classe principal.  Isso é onde você passa ADAL as coordenadas que ele precisa se comunicar com o Azure AD e informá-lo como tokens de cache.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

- Agora localizar o `Search(...)` método, que será chamado quando os usuário cliks "Pesquisar" botão na interface do usuário do aplicativo.  Este método faz uma solicitação GET à API do Azure AD Graph a consulta para os usuários cuja UPN começa com o termo de pesquisa fornecidos.  Mas para consultar a API do gráfico, você precisa incluir uma access_token na `Authorization` cabeçalho da solicitação - é onde entra ADAL.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    // Validate the Input String
    if (string.IsNullOrEmpty(SearchText.Text))
    {
        MessageBox.Show("Please enter a value for the To Do item name");
        return;
    }

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
        UserNameLabel.Content = result.UserInfo.DisplayableId;
        SignOutButton.Visibility = Visibility.Visible;
    }
    catch (AdalException ex)
    {
        // An unexpected error occurred, or user canceled the sign in.
        if (ex.ErrorCode != "access_denied")
            MessageBox.Show(ex.Message);

        return;
    }

    ...
}
```
- Quando o seu aplicativo solicita um token chamando `AcquireTokenAsync(...)`, ADAL tentará retornar um token sem pedir a credenciais de usuário.  Se ADAL determinar que o usuário precisa entrar para obter um token, ele será exibir uma caixa de diálogo de login, coletar as credenciais do usuário e retornar um token durante a autenticação bem-sucedida.  Se ADAL for não é possível retornar um token por algum motivo, ele irá acionar um `AdalException`.
- Observe que o `AuthenticationResult` objeto contém um `UserInfo` objeto que pode ser usado para coletar informações sobre o seu aplicativo talvez seja necessário.  Em DirectorySearcher, `UserInfo` é usado para personalizar a interface do usuário do aplicativo com a id do usuário.

- Quando o usuário clica no botão "Sair", queremos Certifique-se de que a próxima chamada ao `AcquireTokenAsync(...)` solicitará que o usuário para entrar.  Com ADAL, isso é tão fácil quanto limpar o cache de token:

```C#
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

- No entanto, se o usuário não clicar no botão "Sair", você desejará manter a sessão do usuário para a próxima vez em que eles são executados a DirectorySearcher.  Quando o aplicativo é iniciado, você pode verificar o cache de token de ADAL para um token existente e atualizar a interface do usuário de acordo.  No `CheckForCachedToken()` método, faça outra chamada para `AcquireTokenAsync(...)`, dessa vez passando o `PromptBehavior.Never` parâmetro.  `PromptBehavior.Never`informará ADAL que o usuário não deve ser solicitado para entrar e ADAL em vez disso, deverá lançar uma exceção se não for possível retornar um token.

```C#
public async void CheckForCachedToken() 
{
    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "user_interaction_required")
        {
            // An unexpected error occurred.
            MessageBox.Show(ex.Message);
        }

        // If user interaction is required, proceed to main page without singing the user in.
        return;
    }

    // A valid token is in the cache
    SignOutButton.Visibility = Visibility.Visible;
    UserNameLabel.Content = result.UserInfo.DisplayableId;
}
```

Parabéns! Agora você tem um aplicativo WPF .NET que tem a capacidade de autenticar os usuários, com segurança chamar APIs de Web usando OAuth 2.0 de trabalho e obtenha informações básicas sobre o usuário.  Se você ainda não começou, agora é o tempo para preencher seu locatário com alguns usuários.  Executar o aplicativo de DirectorySearcher e entrar em um desses usuários.  Procurar por outros usuários com base em seu UPN.  Feche o aplicativo e execute-o novamente.  Observe como a sessão do usuário permanecerá intacta.  Sair e entrar novamente como outro usuário.

ADAL facilita a incorporar todos esses recursos de identidade comuns em seu aplicativo.  Cuida de todo o trabalho sujo para você - gerenciamento de cache, o suporte do protocolo de OAuth, apresentando o usuário com um logon UI, atualizando tokens expirados e muito mais.  Tudo o que você realmente precisa saber é uma única chamada de API, `authContext.AcquireTokenAsync(...)`.

Para referência, o exemplo concluído (sem os valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Agora você pode mover-se em cenários adicionais.  Talvez você queira experimentar:

[Proteger uma Web .NET API com Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
