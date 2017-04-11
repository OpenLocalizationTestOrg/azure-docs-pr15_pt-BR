<properties
    pageTitle="Active Directory do Azure B2C: Use o Graph API | Microsoft Azure"
    description="Como chamar a API de gráfico para um locatário B2C usando uma identidade de aplicativo para automatizar o processo."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-use-the-graph-api"></a>B2C do Azure AD: Usar o API do gráfico

Azure Active Directory (AD Azure) B2C locatários tendem a ser muito grandes. Isso significa que muitas tarefas comuns de gerenciamento de locatários precisam ser executada por programação. Exemplo primário é o gerenciamento de usuário. Talvez seja necessário migrar um armazenamento de usuário existente para um locatário B2C. Talvez você queira hospedar o registro do usuário em sua própria página e criar contas de usuário no Azure AD nos bastidores. Esses tipos de tarefas exigem a capacidade de criar, ler, atualizar e excluir contas de usuário. Você pode realizar estas tarefas usando a API do Azure AD Graph.

Para B2C locatários, há dois modos principais de comunicar-se com a API do gráfico.

- Tarefas interativos, execução única, você deve agir como uma conta de administrador no locatário B2C quando você executar as tarefas. Esse modo requer um administrador entrar com as credenciais para que esse administrador pode realizar qualquer chamadas para a API do gráfico.
- Tarefas automatizados e contínuos, você deve usar algum tipo de conta de serviço que você forneça os privilégios necessários para executar tarefas de gerenciamento. No Azure AD, você pode fazer isso por registrar um aplicativo e autenticar Azure AD. Isso é feito usando uma **ID de aplicativo** que usa as [credenciais de cliente OAuth 2.0 conceder](../active-directory/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). Nesse caso, o aplicativo atua como em si, não como um usuário, para chamar a API do gráfico.

Neste artigo, abordaremos como realizar o caso de uso automatizado. Para demonstrar, vai criamos uma 4,5 .NET `B2CGraphClient` que executa o usuário criar, ler, atualizar e excluir operações (CRUD). O cliente terá uma interface de linha de comando do Windows (CLI) que permite que você chame vários métodos. No entanto, o código é escrito para se comportam de maneira interativa e automatizada.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obter um locatário do Azure AD B2C

Antes de você pode criar aplicativos ou usuários ou interagir com o Azure AD todo, será necessário um locatário do Azure AD B2C e uma conta de administrador global no locatário. Se você não tiver um locatário já, [Introdução ao Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-a-service-application-in-your-tenant"></a>Registrar um aplicativo de serviço no seu locatário

Depois que um locatário B2C, você precisa criar seu aplicativo de serviço usando cmdlets do PowerShell do Azure AD.
Primeiro, baixe e instale o [Assistente Microsoft Online Services entrar](http://go.microsoft.com/fwlink/?LinkID=286152). Baixe e instale o [módulo do Active Directory do Azure de 64 bits para Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).

> [AZURE.IMPORTANT]
Para usar a API de gráfico com seu locatário B2C, você precisará registrar um aplicativo dedicado usando o PowerShell. Siga as instruções neste artigo fazer isso. Você não pode reutilizar os aplicativos B2C já existente que você registrou no portal do Azure.

Depois de instalar o módulo do PowerShell, abra o PowerShell e se conectar ao seu locatário B2C. Após a execução `Get-Credential`, você será solicitado para um nome de usuário e senha, insira o nome de usuário e senha da sua conta de administrador de locatário B2C.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

Antes de criar seu aplicativo, você precisa gerar um novo **segredo cliente**.  Seu aplicativo usará o segredo do cliente para autenticar ao Azure AD e adquira tokens de acesso. Você pode gerar um segredo válido no PowerShell:

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

O comando final deve imprimir seu novo segredo de cliente. Copie-o em um lugar seguro. Você precisará-la mais tarde. Agora você pode criar seu aplicativo, fornecendo o novo segredo de cliente como uma credencial para o aplicativo:

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

Se você criar o aplicativo com êxito, ele deve imprimir as propriedades do aplicativo como as mostradas acima. Você precisará ambos `ObjectId` e `AppPrincipalId`, então copie esses valores, também.

Depois de criar um aplicativo em seu locatário B2C, você precisa atribuir a ela as permissões necessárias para executar operações de CRUD do usuário. Atribuir as funções de três do aplicativo: leitores de diretório (para usuários de leitura), criadores de diretório (para criar e atualizar usuários) e um administrador de conta de usuário (para excluir usuários). Essas funções tem identificadores conhecidos, portanto, você pode substituir o `-RoleMemberObjectId` parâmetro com `ObjectId` acima e execute os seguintes comandos. Para ver a lista de todas as funções de diretório, tente executar `Get-MsolRole`.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```

Agora você tem um aplicativo que tenha permissão para criar, ler, atualizar e excluir usuários do seu locatário B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Baixar, configurar e criar o código de amostra

Primeiro, baixe o código de amostra e executá-lo. Em seguida, podemos terá uma visão detalhada-lo.  Você pode [baixar o código de exemplo como um arquivo. zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Você também pode cloná-lo em um diretório de sua escolha:

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Abrir o `B2CGraphClient\B2CGraphClient.sln` solução do Visual Studio no Visual Studio. No `B2CGraphClient` do projeto, abra o arquivo `App.config`. Substitua as configurações de três aplicativo com seus próprios valores:

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Em seguida, clique na `B2CGraphClient` solução e recriar a amostra. Se você for bem-sucedido, você agora deve ter um `B2C.exe` arquivo executável localizado em `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Construir operações de CRUD do usuário usando a API do gráfico

Para usar o B2CGraphClient, abra uma `cmd` Windows comando prompt e altere o diretório para o `Debug` diretório. Execute o `B2C Help` comando.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Isso exibirá uma breve descrição de cada comando. Sempre que você invoca um desses comandos, `B2CGraphClient` faz uma solicitação para a API do Azure AD Graph.

### <a name="get-an-access-token"></a>Obter um token de acesso

Qualquer solicitação à API Graph requer um token de acesso para autenticação. `B2CGraphClient`usa o Active Directory autenticação biblioteca (ADAL) código-fonte aberto para ajudar a adquira tokens de acesso. ADAL facilita a aquisição de token fornecendo uma API simples e tomando cuidado de alguns detalhes importantes, como o cache tokens de acesso. Você não precisa usar ADAL para obter tokens, apesar. Você também pode obter tokens criando solicitações HTTP.

> [AZURE.NOTE]
    Este exemplo de código usa ADAL v2 para se comunicar com a API do gráfico.  Você deve usar ADAL v2 ou v3 para obter tokens de acesso que podem ser usados com a API do Azure AD Graph.

Quando `B2CGraphClient` é executado, ele cria uma instância do `B2CGraphClient` classe. O construtor para essa classe configura uma estrutura de autenticação ADAL:

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Usaremos a `B2C Get-User` comando como exemplo. Quando `B2C Get-User` é invocado sem qualquer entradas adicionais, as chamadas CLI a `B2CGraphClient.GetAllUsers(...)` método. Este método chama `B2CGraphClient.SendGraphGetRequest(...)`, que envia uma solicitação GET HTTP à API do gráfico. Antes de `B2CGraphClient.SendGraphGetRequest(...)` envia GET solicitar, ele primeiro obtém um acesso token usando ADAL:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Você pode obter um acesso token de API do gráfico ligando para o ADAL `AuthenticationContext.AcquireToken(...)` método. ADAL retorna um `access_token` que representa a identidade do aplicativo.

### <a name="read-users"></a>Usuários de leitura

Quando você deseja obter uma lista de usuários ou obter um determinado usuário da API do gráfico, você pode enviar um HTTP `GET` solicitar a `/users` ponto de extremidade. Uma solicitação para todos os usuários em um locatário tem esta aparência:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver essa solicitação, execute:

 ```
 > B2C Get-User
 ```

Há duas coisas importantes nota:

- O token de acesso adquirido por meio de ADAL é adicionado à `Authorization` cabeçalho usando o `Bearer` esquema.
- Para B2C locatários, você deve usar o parâmetro de consulta `api-version=1.6`.

Ambos desses detalhes serão administrados no `B2CGraphClient.SendGraphGetRequest(...)` método:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Criar contas de usuário do consumidor

Quando você cria contas de usuário no seu locatário B2C, você pode enviar um HTTP `POST` solicitar a `/users` ponto de extremidade:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",              // a value that can be used for displaying to the end user
    "mailNickname": "joec",                     // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

A maioria dessas propriedades nesta solicitação é necessárias para criar usuários do consumidor. Para saber mais, clique [aqui](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Observe que o `//` comentários foram incluídos para ilustração. Não inclua-os em uma solicitação de real.

Para ver a solicitação, execute um dos seguintes comandos:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

O `Create-User` comando usa um arquivo de .json como um parâmetro de entrada. Esta página contém uma representação JSON de um objeto de usuário. Existem dois arquivos de .json de amostra no código de exemplo: `usertemplate-email.json` e `usertemplate-username.json`. Você pode modificar esses arquivos para atender às suas necessidades. Além dos campos obrigatórios acima, vários campos opcionais que você pode usar são incluídos nesses arquivos. Para obter detalhes sobre os campos opcionais podem ser encontradas na [referência de entidade de API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Você pode ver como a solicitação POST é construída em `B2CGraphClient.SendGraphPostRequest(...)`.

- Anexa um token de acesso para a `Authorization` cabeçalho da solicitação.
- Define `api-version=1.6`.
- Ele inclui o objeto de usuário JSON no corpo da solicitação.

> [AZURE.NOTE]
Se as contas que você deseja migrar de uma loja existente do usuário tiver força de senha menor que a [força de senha forte imposta pelo Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), você pode desabilitar o requisito de senha forte usando o `DisableStrongPassword` valor o `passwordPolicies` propriedade. Por exemplo, você pode modificar a solicitação do usuário criar fornecida acima da seguinte maneira: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>Atualizar contas de usuário do consumidor

Quando você atualiza os objetos de usuário, o processo é semelhante ao que você usa para criar objetos de usuário. Mas esse processo usa o HTTP `PATCH` método:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",              // this request updates only the user's displayName
}
```

Tente atualizar um usuário atualizando seus arquivos JSON com novos dados. Você pode usar `B2CGraphClient` para executar um destes comandos:

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Inspecione a `B2CGraphClient.SendGraphPatchRequest(...)` método para obter detalhes sobre como enviar esta solicitação.

### <a name="search-users"></a>Usuários de pesquisa

Você pode procurar por usuários em seu locatário B2C de algumas maneiras. Um, usando o usuário objeto ID ou duas, usando o identificador de entrada do usuário (ou seja, o `signInNames` propriedade).

Execute um dos seguintes comandos para procurar um usuário específico:

```
> B2C Get-User <user-object-id>
> B2C Get-User <filter-query-expression>
```

Aqui estão alguns exemplos:

```
> B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
> B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Excluir usuários

O processo para excluir um usuário é simples. Usar o HTTP `DELETE` método e construção a URL com o nome correto ID de objeto:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver um exemplo, digite este comando e exibir a solicitação de exclusão que é impresso no console:

```
> B2C Delete-User <object-id-of-user>
```

Inspecione a `B2CGraphClient.SendGraphDeleteRequest(...)` método para obter detalhes sobre como enviar esta solicitação.

Você pode executar muitas outras ações com a API do Azure AD Graph além de gerenciamento de usuários. A [referência da API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) fornece detalhes sobre cada ação, juntamente com solicitações de amostra.

## <a name="use-custom-attributes"></a>Usar atributos personalizados

A maioria dos aplicativos do consumidor precisa armazenar algum tipo de informações de perfil de usuário personalizadas. Uma maneira que você pode fazer isso é definir um atributo personalizado em seu locatário B2C. Em seguida, você pode tratar esse atributo da mesma maneira que você trate qualquer outra propriedade em um objeto de usuário. Você pode atualizar o atributo, excluir o atributo, pelo atributo de consulta, enviar o atributo como uma declaração na entrada tokens e muito mais.

Para definir um atributo personalizado em seu locatário B2C, consulte a [referência de atributo personalizado B2C](active-directory-b2c-reference-custom-attr.md).

Você pode exibir os atributos personalizados definidos em seu locatário B2C usando `B2CGraphClient`:

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

A saída dessas funções revela os detalhes de cada atributo personalizado, como:

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Você pode usar o nome completo, como `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, como uma propriedade em seus objetos de usuário.  Atualizar seu arquivo de .json com a nova propriedade e um valor para a propriedade e, em seguida, execute:

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

Usando `B2CGraphClient`, você tem um aplicativo de serviço que pode gerenciar seus usuários de locatário B2C programaticamente. `B2CGraphClient`usa sua própria identidade de aplicativo para autenticar à API do Azure AD Graph. Ele também adquire tokens usando um segredo de cliente. Conforme você incorporar essa funcionalidade em seu aplicativo, lembre-se alguns pontos principais para aplicativos B2C:

- Você precisa conceder ao aplicativo as permissões apropriadas no locatário.
- Por agora, você precisa usar v2 ADAL para obter tokens de acesso. (Você também pode enviar mensagens de protocolo diretamente, sem usar uma biblioteca.)
- Quando você chamar a API de gráfico, use `api-version=1.6`.
- Quando você cria e atualizar usuários do consumidor, algumas propriedades são necessárias, conforme descrito acima.

Se você tiver dúvidas ou solicitações de ações que você gostaria de executar usando a API do gráfico em seu locatário B2C, deixar um comentário sobre este artigo ou um problema de arquivo no repositório de amostra de código do GitHub.
