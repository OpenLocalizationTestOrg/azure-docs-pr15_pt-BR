<properties
    pageTitle="Introdução ao Azure CDN biblioteca para .NET | Microsoft Azure"
    description="Aprenda como escrever aplicativos .NET para gerenciar CDN Azure usando o Visual Studio."
    services="cdn"
    documentationCenter=".net"
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="casoper"/>

# <a name="get-started-with-azure-cdn-development"></a>Introdução ao desenvolvimento de CDN do Azure

> [AZURE.SELECTOR]
- [Node](cdn-app-dev-node.md)
- [.NET](cdn-app-dev-net.md)

Você pode usar a [Biblioteca de CDN do Azure para .NET](https://msdn.microsoft.com/library/mt657769.aspx) para automatizar a criação e gerenciamento de perfis CDN e pontos de extremidade.  Este tutorial percorre a criação de um aplicativo de console .NET simples que demonstra várias das operações disponíveis.  Este tutorial não pretende descrevem todos os aspectos da biblioteca de CDN do Azure para .NET em detalhes.

É necessário o Visual Studio de 2015 para concluir este tutorial.  [2015 de comunidade do Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) está disponível gratuitamente para download.

> [AZURE.TIP] A [concluiu projeto a partir deste tutorial](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) está disponível para download no MSDN.

[AZURE.INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Crie seu projeto e adicione pacotes do Nuget

Agora que podemos criar um grupo de recursos para nossos perfis CDN e nossa permissão de aplicativo do Azure AD para gerenciar perfis CDN e pontos de extremidade dentro desse grupo, podemos começar criando nosso aplicativo.

Na Visual Studio de 2015, clique em **arquivo**, **novo** **projeto …** para abrir a caixa de diálogo Novo projeto.  Expanda **Visual c#**e, em seguida, selecione **Windows** no painel à esquerda.  Clique em **Aplicativo Console** no painel central.  Nome do seu projeto, clique em **Okey**.  

![Novo projeto](./media/cdn-app-dev-net/cdn-new-project.png)

Nosso projeto vai usar algumas bibliotecas Azure contidas em pacotes do Nuget.  Vamos adicionar aqueles ao projeto.

1. Clique no menu **Ferramentas** , **Gerenciador de pacotes do Nuget**, **Package Manager Console**.

    ![Gerenciar pacotes do Nuget](./media/cdn-app-dev-net/cdn-manage-nuget.png)

2. No Console do Gerenciador de pacote, execute o seguinte comando para instalar a **Biblioteca de autenticação do Active Directory (ADAL)**:

    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`

3. Execute o seguinte procedimento para instalar a **Biblioteca de gerenciamento do Azure CDN**:

    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Diretivas, constantes, método principal e métodos de auxiliar

Vamos dar início a estrutura básica de nosso programa escrito.

1. Novamente na guia Program.cs, substitua o `using` diretivas na parte superior com o seguinte:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

2. Precisamos definir algumas constantes que nossos métodos usará.  No `Program` classe, mas antes o `Main` método, adicione o seguinte.  Certifique-se de substituir os espaços reservados, incluindo o ** &lt;colchetes&gt;**, com seus próprios valores conforme necessário.

    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";

    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```

3. Também no nível de classe, defina essas duas variáveis.  Usaremos essas posteriormente para determinar se nosso perfil e o ponto de extremidade já existirem.

    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```

4.  Substituir o `Main` método da seguinte forma:

    ```csharp
    static void Main(string[] args)
    {
        //Get a token
        AuthenticationResult authResult = GetAccessToken();

        // Create CDN client
        CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
            { SubscriptionId = subscriptionId };

        ListProfilesAndEndpoints(cdn);

        // Create CDN Profile
        CreateCdnProfile(cdn);

        // Create CDN Endpoint
        CreateCdnEndpoint(cdn);
        
        Console.WriteLine();

        // Purge CDN Endpoint
        PromptPurgeCdnEndpoint(cdn);

        // Delete CDN Endpoint
        PromptDeleteCdnEndpoint(cdn);

        // Delete CDN Profile
        PromptDeleteCdnProfile(cdn);

        Console.WriteLine("Press Enter to end program.");
        Console.ReadLine();
    }
    ```

5. Alguns dos nossos outros métodos estiverem indo para solicitar que o usuário com perguntas de "Sim/não".  Adicione o seguinte método para simplificar a que:

    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

Agora que a estrutura básica de nosso programa escrita, podemos deve criar os métodos chamados pela `Main` método.

## <a name="authentication"></a>Autenticação

Antes que podemos usar a biblioteca de gerenciamento do Azure CDN, precisamos autenticar nosso serviço principal e obter um token de autenticação.  Este método usa ADAL para recuperar o token.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority); 
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult = 
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

Se você estiver usando autenticação de usuário individual, o `GetAccessToken` método terá uma aparência um pouco diferente.

>[AZURE.IMPORTANT] Use somente este exemplo de código se você estiver escolhendo ter autenticação de usuário individual, em vez de um serviço principal.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

Certifique-se de substituir `<redirect URI>` com o redirecionamento URI que você inseriu quando você registrou o aplicativo no Azure AD.

## <a name="list-cdn-profiles-and-endpoints"></a>Lista CDN perfis e pontos de extremidade

Agora, podemos está prontos para executar operações de CDN.  A primeira coisa que nosso método faz é lista todos os perfis e pontos de extremidade no nosso grupo de recursos e se ele encontrar uma correspondência para os nomes de perfil e o ponto de extremidade especificado em nossas constantes, faz uma anotação de que posteriormente para que não tentamos criar duplicatas.

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Criar pontos de extremidade e perfis CDN

Em seguida, vamos criar um perfil.

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

Depois de criado o perfil, vamos criar um ponto de extremidade.

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

>[AZURE.NOTE] O exemplo acima atribui o ponto de extremidade uma origem chamada *Contoso* com um nome de host `www.contoso.com`.  Você deve alterar isso para apontar para o nome do host do seu próprio origem.

## <a name="purge-an-endpoint"></a>Limpar um ponto extremo

Considerando que o ponto de extremidade tiver sido criado, uma tarefa comum que queremos executar no nosso programa está limpando o conteúdo no nosso ponto de extremidade.

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

>[AZURE.NOTE] No exemplo acima, a cadeia de caracteres `/*` indica que desejo remover tudo na raiz do caminho do ponto de extremidade.  Isso equivale a verificação **Limpar tudo** na caixa de diálogo do portal Azure "Limpar". No `CreateCdnProfile` método, criado nosso perfil como um perfil do **Azure CDN da Verizon** usando o código `Sku = new Sku(SkuName.StandardVerizon)`, isso será bem-sucedido.  No entanto, perfis do **Azure CDN do Akamai** não oferecem suporte para **Limpar tudo**, portanto se eu estava usando um perfil Akamai para este tutorial, eu precisaria incluir caminhos específicos para limpar.

## <a name="delete-cdn-profiles-and-endpoints"></a>Excluir pontos de extremidade e perfis CDN

Os últimos métodos excluirá nosso ponto de extremidade e no perfil.

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>Executar o programa

Agora podemos compilar e executar o programa clicando no botão **Iniciar** no Visual Studio.

![Programa em execução](./media/cdn-app-dev-net/cdn-program-running-1.png)

Quando o programa atinge o prompt acima, você poderá retornar ao seu grupo de recursos no portal do Azure e ver que o perfil foi criado.

![Sucesso!](./media/cdn-app-dev-net/cdn-success.png)

Em seguida, pode confirmamos os prompts para executar o restante do programa.

![Concluindo de programa](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Próximas etapas

Para ver o projeto deste passo a passo, [Baixe o exemplo](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c)concluído.

Para localizar a documentação adicional sobre a biblioteca de gerenciamento do Azure CDN para .NET, veja a [referência no MSDN](https://msdn.microsoft.com/library/mt657769.aspx).

Gerencie seus recursos CDN com [PowerShell](./cdn-manage-powershell.md).


