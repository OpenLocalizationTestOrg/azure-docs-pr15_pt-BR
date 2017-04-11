<properties
    pageTitle="Azure AD Xamarin Introdução | Microsoft Azure"
    description="Como criar um aplicativo de Xamarin que se integra ao Azure AD para entrar e chama Azure AD protegido APIs usando OAuth."
    services="active-directory"
    documentationCenter="xamarin"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="integrate-azure-ad-into-a-xamarin-app"></a>Integrar o Azure AD em um aplicativo de Xamarin

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Xamarin permite que você escreva aplicativos móveis em c# que podem ser executados em iOS, Android e Windows (dispositivos móveis e PCs). Se você estiver criando um aplicativo usando Xamarin, Azure AD torna mais simples e direto para você autenticar os usuários com suas contas do Active Directory. Ele também permite que seu aplicativo com segurança consumir qualquer web API protegido por Azure AD, como as APIs do Office 365 ou a API do Azure.

Para aplicativos de Xamarin que precise acessar recursos protegidos, Azure AD fornece a biblioteca de autenticação do Active Directory ou ADAL. Única finalidade do ADAL na vida é tornar mais fácil para o aplicativo obter tokens de acesso. Para demonstrar apenas como é fácil, aqui será criamos um aplicativo de "Pesquisador de diretório" que:

-   É executado no iOS, Android, área de trabalho do Windows, Windows Phone e Windows Store.
- Usa uma biblioteca de classes portátil único (PCL) para autenticar usuários e obter tokens para a API do Azure AD Graph
-   Procura um diretório usuários com um determinado UPN.

Para criar o aplicativo de trabalho concluído, você precisará:

2. Configurar o ambiente de desenvolvimento Xamarin
2. Registre seu aplicativo com o Azure AD.
3. Instalar e configurar ADAL.
5. Use ADAL para obter tokens do Azure AD.

Para começar a, [baixar um projeto reduzido](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) ou [baixar o exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Cada um é uma solução do Visual Studio 2013. Você também será preciso um locatário do Azure AD na qual você pode criar usuários e registrar um aplicativo. Se você ainda não tiver um locatário, [Saiba como obter uma](active-directory-howto-tenant.md).

## <a name="0-set-up-your-xamarin-development-environment"></a>*0. configurar seu ambiente de desenvolvimento Xamarin*
Porque este tutorial inclui projetos para iOS, Android e Windows, você precisará Visual Studio e Xamarin juntos. Para criar o ambiente necessário, siga as instruções completas na [instalação e instalar para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) no MSDN. Essas instruções incluem material que você possa examinar para saber mais sobre Xamarin enquanto você está aguardando os instaladores ser concluída. 

Uma vez que você concluiu a configuração necessária, abra a solução no Visual Studio para começar. Você encontrará projetos de seis: cinco projetos específicos de plataforma e uma biblioteca de classes portátil que será compartilhada todas as plataformas`DirectorySearcher.cs`

## <a name="1-register-the-directory-searcher-application"></a>*1. Registre o aplicativo do Pesquisador de diretório*
Para habilitar o aplicativo obter tokens, você precisará primeiro registrá-lo no seu locatário do Azure AD e conceder permissão para acessar a API do Azure AD gráfico:

-   Entrar no [Portal de gerenciamento do Azure](https://manage.windowsazure.com)
-   No painel de navegação à esquerda, clique no **Active Directory**
-   Selecione um locatário no qual deseja registrar o aplicativo.
-   Clique na guia **aplicativos** e clique em **Adicionar** na parte inferior registradora.
-   Siga os avisos e criar um novo **Aplicativo cliente nativo**.
    -   O **nome** do aplicativo descreverá seu aplicativo para usuários finais
    -   O **Uri redirecionar** é uma combinação de esquema e a cadeia de caracteres que Azure AD usará para retornar respostas token. Insira um valor, por exemplo, `http://DirectorySearcher`.
-   Após concluir registro, AAD atribuirá seu aplicativo um identificador de cliente exclusivo. Você precisará desse valor nas próximas seções, portanto copiá-lo na guia **Configurar** .
- Também na guia **Configurar** , localize a seção "Permissões para outros aplicativos". Para o aplicativo "Azure Active Directory", adicione a permissão de **diretório da organização o acesso** em **Permissões delegados**. Isso permitirá que seu aplicativo para consultar a API de gráfico para os usuários.

## <a name="2-install--configure-adal"></a>*2. instalar e configurar ADAL*
Agora que você tem um aplicativo no Azure AD, você pode instalar ADAL e escrever seu código relacionadas a identidade. Ordem de ADAL sejam capazes de se comunicar com o Azure AD, você precisa fornecê-lo com algumas informações sobre seu registro de aplicativo.
-   Comece adicionando ADAL para cada um dos projetos na solução usando o Package Manager Console.

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
`

- Você deve observar que duas referências de biblioteca são adicionadas a cada projeto - parte PCL de ADAL e uma parte específica de plataforma.

-   No projeto DirectorySearcherLib, abra `DirectorySearcher.cs`. Altere os valores de membro de classe para refletir os valores que você insere no Portal do Azure. Seu código fará referência esses valores sempre que ele usa ADAL.
    -   O `tenant` é o domínio de seu locatário do Azure AD, por exemplo, contoso.onmicrosoft.com
    -   O `clientId` é o clientId do aplicativo que você copiou do portal.
    - O `returnUri` é o redirectUri inseridas no portal do, por exemplo, `http://DirectorySearcher`.

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. usar ADAL para obter Tokens de AAD*
*Quase* todos lógica de autenticação do aplicativo reside em `DirectorySearcher.SearchByAlias(...)`. Tudo o que é necessário nos projetos específicos de plataforma é passar um parâmetro contextual para o `DirectorySearcher` PCL.

- Primeiro, abra `DirectorySearcher.cs` e adicionar um novo parâmetro para a `SearchByAlias(...)` método. `IPlatformParameters`é o parâmetro contextual que encapsula os objetos específicos da plataforma que ADAL precisa executar autenticação.

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

-   Em seguida, inicializar o `AuthenticationContext` -ADAL da classe principal. Isso é onde você passa ADAL as coordenadas que ele precisa se comunicar com o Azure AD. Ligue para `AcquireTokenAsync(...)`, que aceita a `IPlatformParameters` object e irá chamar o fluxo de autenticação necessário retornar um token para o aplicativo.

```C#
...
    AuthenticationResult authResult = null;
    try
    {
        AuthenticationContext authContext = new AuthenticationContext(authority);
        authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
    }
    catch (Exception ee)
    {
        results.Add(new User { error = ee.Message });
        return results;
    }
...
```
- `AcquireTokenAsync(...)`primeiro tentará retornar um símbolo para o recurso solicitado (o gráfico API neste caso) sem solicitar que o usuário insira suas credenciais (via cache ou atualizar tokens antigos). Somente se for necessário, ele mostrará o usuário o sinal do Azure AD na página antes de adquirir o token solicitado.


- Você pode anexar o token de acesso à solicitação de API de gráfico no cabeçalho de autorização:

```C#
...
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

Isso é tudo para o `DirectorySearcher` PCL e seu aplicativo do relacionadas a identidade código.  Tudo o que resta é chamar o `SearchByAlias(...)` método nos modos de exibição de cada plataforma, e onde necessário adicionar código para tratar corretamente o ciclo de vida de interface do usuário.

####<a name="android"></a>Android:
- Em `MainActivity.cs`, adicione uma chamada para `SearchByAlias(...)` manipulador de clique no botão:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
- Você também precisa substituir o `OnActivityResult` método de ciclo de vida para encaminhar qualquer autenticação redireciona volta para o método adequado.  ADAL fornece um método auxiliar para isso no Android:

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

####<a name="windows-desktop"></a>Área de trabalho do Windows:
- Em `MainWindow.xaml.cs`, basta fazer uma chamada para `SearchByAlias(...)` passando um `WindowInteropHelper` da área de trabalho `PlatformParameters` objeto:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####<a name="ios"></a>iOS:
- Em `DirSearchClient_iOSViewController.cs`, o iOS `PlatformParameters` objeto simplesmente utiliza uma referência para o controlador de exibição:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####<a name="windows-universal"></a>Universal do Windows:
- No Windows Universal, abra `MainPage.xaml.cs` e implementar o `Search` método, que usa um método auxiliar em um projeto compartilhado para atualizar a interface do usuário conforme necessário.

```C#
...
    List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

Parabéns! Agora você tem um aplicativo de Xamarin que tem a capacidade de autenticar os usuários e com segurança chamar APIs de Web usando OAuth 2.0 entre cinco diferentes plataformas de trabalho. Se você ainda não começou, agora é o tempo para preencher seu locatário com alguns usuários. Executar o aplicativo de DirectorySearcher e entrar em um desses usuários. Procurar por outros usuários com base em seu UPN.

ADAL torna mais fácil de incorporar recursos de identidade comuns em seu aplicativo. Cuida de todo o trabalho sujo para você - gerenciamento de cache, o suporte do protocolo de OAuth, apresentando o usuário com um logon UI, atualizando tokens expirados e muito mais. Tudo o que você realmente precisa saber é uma única chamada de API, `authContext.AcquireToken*(…)`.

Para referência, o exemplo concluído (sem os valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Agora você pode mover-se em cenários de identidade adicional. Talvez você queira experimentar:

[Proteger uma Web .NET API com Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
