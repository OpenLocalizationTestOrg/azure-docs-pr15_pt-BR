<properties 
   pageTitle="Autenticação do Active Directory e Resource Manager | Microsoft Azure"
   description="Um guia do desenvolvedor para a autenticação com a API do Gerenciador de recursos do Azure e o Active Directory para integrar um aplicativo com outras assinaturas do Azure."
   services="azure-resource-manager,active-directory"
   documentationCenter="na"
   authors="dushyantgill"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2016"
   ms.author="dugill;tomfitz" />

# <a name="how-to-use-azure-active-directory-and-resource-manager-to-manage-a-customers-resources"></a>Como usar o Active Directory do Azure e Gerenciador de recursos para gerenciar recursos do cliente

## <a name="introduction"></a>Introdução

Se você for um desenvolvedor de software que precisa criar um aplicativo que gerencia recursos Azure do cliente, este tópico mostra como autenticar com as APIs do Gerenciador de recursos do Azure e obter acesso aos recursos em outras assinaturas. 

Seu aplicativo pode acessar as APIs do Gerenciador de recursos em duas maneiras:

1. **Usuário + app acesse**: para os aplicativos que acessam recursos em nome de um usuário conectado. Essa abordagem funciona para aplicativos, como aplicativos web e ferramentas de linha de comando, que lidam com apenas "gerenciamento interativo" de recursos Azure.
1. **Acesso somente App**: para os aplicativos que executam serviços de daemon e trabalhos agendados. Identidade do aplicativo é concedida acesso direto aos recursos. Essa abordagem funciona para os aplicativos que precisam de longo prazo "acesso offline" ao Azure.

Este tópico fornece instruções passo a passo para criar um aplicativo que utiliza esses dois métodos de autorização. Ele mostra como executar cada etapa com API REST ou c#. O aplicativo ASP.NET MVC completo está disponível em [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

Todo o código para este tópico está executando como um aplicativo web que você pode experimentar em [http://vipswapper.azurewebsites.net/cloudsense](http://vipswapper.azurewebsites.net/cloudsense). 

## <a name="what-the-web-app-does"></a>O que significa o web app

O aplicativo web:

1. Sinais de um usuário do Azure.
2. Pede ao usuário para conceder o acesso de aplicativo web ao Gerenciador de recursos.
3. Obtém o usuário + token de acesso do aplicativo para acessar o Gerenciador de recursos.
4. Usa o token (da etapa 3) para chamar o Gerenciador de recursos e atribuir entidade de serviço do aplicativo a uma função na assinatura, que fornece o acesso de longo prazo de aplicativo para a assinatura.
5. Obtém acesso somente aplicativo token.
6. Usa o token (da etapa 5) para gerenciar recursos na assinatura por meio do Gerenciador de recursos.

Aqui está o fluxo de ponta a ponta do aplicativo da web.

![Fluxo de autenticação do Gerenciador de recursos](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Como um usuário, você pode fornecer a identificação de assinatura para a assinatura que você deseja usar:

![Forneça a id da assinatura](./media/resource-manager-api-authentication/sample-ux-1.png)

Selecione a conta a ser usado para fazer logon.

![Selecione a conta](./media/resource-manager-api-authentication/sample-ux-2.png)

Fornece suas credenciais.

![fornecer credenciais](./media/resource-manager-api-authentication/sample-ux-3.png)

Conceda acesso para suas assinaturas Azure aplicativo:
 
![Conceder acesso](./media/resource-manager-api-authentication/sample-ux-4.png)
 
Gerencie suas assinaturas conectadas:

![Conectar-se a assinatura](./media/resource-manager-api-authentication/sample-ux-7.png)


## <a name="register-application"></a>Registrar aplicativo

Antes de iniciar a codificação, registre seu aplicativo web com o Azure Active Directory (AD). O registro de aplicativo cria uma identidade central para seu aplicativo no Azure AD. Ele contém informações básicas sobre seu aplicativo como o ID do cliente OAuth, URLs de resposta e as credenciais que o aplicativo usa para autenticar e APIs do Azure Gerenciador de recursos de acesso. O registro de aplicativo também registra as diversas permissões delegadas que seu aplicativo precisa ao acessar o Microsoft APIs em nome de usuário. 

Como seu aplicativo acessa outra assinatura, você deve configurá-lo como um aplicativo de vários locatário. Para passar validação, forneça um domínio associado ao seu Active Directory. Para ver os domínios associados ao seu Active Directory, faça logon no [portal clássico](https://manage.windowsazure.com). Selecione seu Active Directory e clique em **domínios**.

O exemplo a seguir mostra como registrar o aplicativo usando o PowerShell do Azure. Você deve ter a versão mais recente (agosto de 2016) do PowerShell do Azure para esse comando funcionar. 

    $app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true
    
Para efetuar login como o aplicativo do AD, é necessário o id do aplicativo e a senha. Para ver a id do aplicativo que é retornada o comando anterior, use:

    $app.ApplicationId

O exemplo a seguir mostra como registrar o aplicativo usando CLI do Azure. 

    azure ad app create --name {app name} --home-page https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available true

Os resultados incluem o AppId, que você precisa ao autenticar como o aplicativo.

### <a name="optional-configuration---certificate-credential"></a>Configuração opcional - credenciais de certificado

Azure AD também dá suporte para credenciais de certificado para aplicativos: você criar um certificado autoassinado, manter a chave privada e adicionar a chave pública ao seu registro de aplicativo do Azure AD. Para autenticação, seu aplicativo envia uma carga pequena ao Azure AD assinado usando sua chave privada e Azure AD valida a assinatura usando a chave pública que você registrou.

Para obter informações sobre como criar um aplicativo do AD com um certificado, consulte [Usar o PowerShell do Azure para criar um serviço principal para acessar recursos](resource-group-authenticate-service-principal.md#create-service-principal-with-certificate) ou [Uso do Azure CLI para criar um serviço principal para acessar recursos](resource-group-authenticate-service-principal-cli.md#create-service-principal-with-certificate).

## <a name="get-tenant-id-from-subscription-id"></a>Obter identificação de locatário do id da assinatura

Para solicitar um token que pode ser usado para chamar o Gerenciador de recursos, o seu aplicativo precisa saber a identificação de locatário do locatário Azure AD que hospeda a assinatura do Azure. Provavelmente, seus usuários saber suas ids de assinatura, mas eles não podem saber ids de seu locatário do Active Directory. Para obter a id do usuário locatário, peça ao usuário para a identificação de assinatura. Forneça essa id da assinatura ao enviar uma solicitação sobre a assinatura:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

A solicitação falhará porque o usuário não tiver conectado ainda, mas você pode recuperar a id de locatário da resposta. Essa exceção, recupere a id de locatário do valor de cabeçalho de resposta para **Autenticar WWW**. Você vê essa implementação no método [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) .

## <a name="get-user--app-access-token"></a>Obter usuário + token de acesso do aplicativo

Seu aplicativo redireciona o usuário para o Azure AD com uma OAuth 2.0 autorizar solicitação - para autenticar as credenciais do usuário e voltar um código de autorização. Seu aplicativo usa o código de autorização para acessar um token do Gerenciador de recursos. O método [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) cria a solicitação de autorização.

Este tópico mostra as solicitações de API REST para autenticar o usuário. Você também pode usar bibliotecas de auxiliar para executar a autenticação no seu código. Para obter mais informações sobre essas bibliotecas, consulte [Azure Active Directory autenticação bibliotecas](./active-directory/active-directory-authentication-libraries.md). Para obter orientações sobre a integração do gerenciamento de identidades em um aplicativo, consulte o [Guia do desenvolvedor do Active Directory do Azure](./active-directory/active-directory-developers-guide.md).

### <a name="auth-request-oauth-20"></a>Solicitação de AUTH (OAuth 2.0)

Execute uma abrir ID conectar/OAuth2.0 autorizar solicitação para o ponto de extremidade do Azure AD autorizar:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Os parâmetros de cadeia de caracteres de consulta que estão disponíveis para esta solicitação são descritos no tópico da [solicitação de um código de autorização](./active-directory/active-directory-protocols-oauth-code.md#request-an-authorization-code) .

O exemplo a seguir mostra como solicitar autorização de OAuth2.0:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD autentica o usuário e, se necessário, pede ao usuário para conceder permissão para o aplicativo. Ela retorna o código de autorização para a URL de resposta do seu aplicativo. Dependendo do response_mode solicitada, Azure AD ou envia de volta os dados na cadeia de caracteres de consulta ou como dados post.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Solicitação de AUTH (ID abrir Conectar)

Se você não apenas deseja acessar o Gerenciador de recursos do Azure em nome de usuário, mas também permitir que o usuário entrar em seu aplicativo usando sua conta do Azure AD, execute um abrir ID autorizar solicitação de conexão. Com abrir ID Connect, seu aplicativo também recebe um id_token do Azure AD que seu aplicativo pode usar para entrar no usuário.

Os parâmetros de cadeia de caracteres de consulta que estão disponíveis para esta solicitação são descritos no tópico [Enviar a solicitação de entrada](./active-directory/active-directory-protocols-openid-connect-code.md#send-the-sign-in-request) .

Um exemplo de solicitação conectar abertas de identificação é:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD autentica o usuário e, se necessário, pede ao usuário para conceder permissão para o aplicativo. Ela retorna o código de autorização para a URL de resposta do seu aplicativo. Dependendo do response_mode solicitada, Azure AD ou envia de volta os dados na cadeia de caracteres de consulta ou como dados post.

Um exemplo abrir ID conectar resposta é:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Solicitação de token (OAuth2.0 código conceder fluxo)

Agora que seu aplicativo recebeu o código de autorização do Azure AD, é hora de obter acesso de token para o Gerenciador de recursos do Azure.  Poste uma OAuth2.0 código conceder Token solicitação para o ponto de extremidade do Azure AD Token: 

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Os parâmetros de cadeia de caracteres de consulta que estão disponíveis para esta solicitação são descritos no tópico [usar o código de autorização](./active-directory/active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) .

O exemplo a seguir mostra uma solicitação de token de concessão de código com credencial de senha:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Ao trabalhar com as credenciais de certificado, crie um Token de Web de JSON (JWT) e o sinal (RSA SHA256) usando a chave privada de credencial de certificado do seu aplicativo. Os tipos de declarações para o token são mostrados em [declarações do token de JWT](./active-directory/active-directory-protocols-oauth-code.md#jwt-token-claims). Para referência, consulte o [código de biblioteca de Auth do Active Directory (.NET)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) para assinar tokens de JWT de declaração do cliente.

Consulte a [especificação aberta ID conectar](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) para obter detalhes sobre autenticação de cliente. 

O exemplo a seguir mostra uma solicitação de token de concessão de código com credenciais de certificado:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1
    
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012
    
    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Uma resposta de exemplo para código conceder token: 

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Lidar com resposta de token de concessão de código

Uma resposta de token bem-sucedida contém (usuário + aplicativo) token de acesso para o Gerenciador de recursos do Azure. Seu aplicativo usa esse token de acesso para acessar o Gerenciador de recursos em nome de usuário. O ciclo de vida de tokens de acesso emitido por Azure AD é uma hora. É provável que seu aplicativo web precisa renovar (usuário + aplicativo) token de acesso. Se precisar renovar o token de acesso, use o símbolo de atualização de seu aplicativo recebe na resposta token. Poste uma OAuth2.0 Token solicitação para o ponto de extremidade do Azure AD Token: 

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Os parâmetros para usar com a solicitação de atualização são descritos no [Atualizando o token de acesso](./active-directory/active-directory-protocols-oauth-code.md#refreshing-the-access-tokens).

O exemplo a seguir mostra como usar a atualização token:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Embora tokens de atualização podem ser usados para obter novos tokens de acesso para o Gerenciador de recursos do Azure, não são adequados para acesso offline pelo seu aplicativo. O ciclo de vida de tokens de atualização está limitado e tokens de atualização são vinculados ao usuário. Se o usuário deixa a organização, o aplicativo usando o token de atualização perde o acesso. Essa abordagem não é adequada para aplicativos que são usados pelas equipes a gerenciar seus recursos Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Verifique se o usuário pode atribuir acesso à assinatura

Seu aplicativo agora tem um token para acessar o Gerenciador de recursos do Azure em nome de usuário. A próxima etapa é conectar seu aplicativo para a assinatura. Depois de conectar, seu aplicativo pode gerenciar essas assinaturas mesmo quando o usuário não estiver presente (acesso offline a longo prazo). 

Para cada assinatura para se conectar, ligue para as [permissões de lista do Gerenciador de recursos de](https://msdn.microsoft.com/library/azure/dn906889.aspx) API para determinar se o usuário tem direitos de gerenciamento de acesso para a assinatura.

O método [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) do aplicativo ASP.NET MVC amostra implementa essa chamada.

O exemplo a seguir mostra como solicitar permissões de um usuário em uma assinatura. 83cfe939-2402-4581-b761-4f59b0a041e4 é a id da assinatura.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Um exemplo da resposta para acessar permissões do usuário em assinatura é:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

As permissões API retorna várias permissões. Cada permissão consiste ações permitidas (ações) e não permitidas ações (notactions). Se uma ação está presente na lista de ações permitidas de qualquer permissão e não estejam presentes na lista notactions dessa permissão, o usuário tem permissão para executar essa ação. **Microsoft.Authorization/RoleAssignments/Write** é a ação que que concede acesso direitos de gerenciamento. Seu aplicativo deve analisar o resultado de permissões para procurar uma correspondência regex nessa cadeia de caracteres de ação em ações e notactions de cada permissão.

## <a name="get-app-only-access-token"></a>Obter acesso somente aplicativo token

Agora, você sabe se o usuário pode atribuir acesso à assinatura Azure. As próximas etapas são:

1. Atribua a função RBAC apropriada a identidade do seu aplicativo na assinatura.
2. Valide a atribuição do access consultando permissão do aplicativo a assinatura ou acessando o Gerenciador de recursos usando somente aplicativo token.
1. Grave a conexão em sua estrutura de dados de aplicativos "assinaturas conectada" - persistir a id da assinatura.

Vamos dar uma olhada mais atenta na primeira etapa. Para atribuir a função RBAC apropriada para a identidade do aplicativo, você deve determinar:

- O id do objeto da identidade do seu aplicativo do Azure Active Directory do usuário
- O identificador da função RBAC que seu aplicativo requer a assinatura

Quando o aplicativo autentica um usuário de um anúncio Azure, ele cria um objeto de serviço para o seu aplicativo no que Azure AD. Azure permite funções RBAC para serem atribuídos a objetos de serviço para conceder acesso direto aos aplicativos correspondentes em recursos Azure. Esta ação é exatamente o que deseja fazer. Consulta a API do Azure AD Graph para determinar o identificador do princípio de serviço do seu aplicativo no usuário conectado é Azure AD.

Você só tem um token de acesso para o Gerenciador de recursos do Azure - você precisa de um novo token de acesso para chamar a API do Azure AD Graph. Cada aplicativo no Azure AD tem permissão para consultar seu próprio objeto principal de serviço, portanto, um token de acesso somente aplicativo é suficiente.

<a id="app-azure-ad-graph">
### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Obter acesso somente aplicativo token API do Azure AD Graph

Para autenticar seu aplicativo e obter um token à API do Azure AD Graph, emita uma solicitação de token de fluxo de OAuth2.0 do cliente credencial conceder ao ponto de extremidade token do Azure AD (**https://login.microsoftonline.com/ {directory_domain_name} / OAuth2/Token**).

O método [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) do aplicativo de exemplo ASP.net MVC obtém um acesso somente aplicativo token para API do gráfico usando a biblioteca de autenticação do Active Directory para .NET.

Os parâmetros de cadeia de caracteres de consulta que estão disponíveis para esta solicitação são descritos no tópico da [solicitação de um Token de acesso](./active-directory/active-directory-protocols-oauth-service-to-service.md#request-an-access-token) .

Um exemplo de solicitação de credencial do cliente conceder token: 

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Uma resposta de exemplo para credencial de cliente conceder token: 

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Obtenha o ID de objeto de capital de serviço de aplicativo no Azure AD do usuário

Agora, use o token de acesso somente aplicativo para consultar os [objetos de serviço de gráfico do Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API para determinar a identificação de objeto de serviço do aplicativo principal no diretório.

O método [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) do aplicativo de exemplo ASP.net MVC implementa essa chamada.

O exemplo a seguir mostra como solicitar principal do serviço de um aplicativo. a0448380-c346-4f9f-b897-c18733de9394 é a id do cliente do aplicativo.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

O exemplo a seguir mostra uma resposta à solicitação de serviço do aplicativo principal 

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Obter o identificador da função RBAC do Azure

Para atribuir a função RBAC apropriada ao seu serviço principal, você deve determinar o identificador da função RBAC do Azure.

A função RBAC certa para seu aplicativo:

- Se seu aplicativo monitora apenas a assinatura, sem fazer alterações, ela requer permissões de leitor somente a assinatura. Atribua a função de **leitor** .
- Se o seu aplicativo gerencia Azure da assinatura, criação/modificação/exclusão de entidades, ele requer uma das permissões Colaborador.
  - Para gerenciar um determinado tipo de recurso, atribuir as funções específicas do recurso colaborador (colaborador de máquina Virtual Colaborador de rede Virtual, colaborador de conta de armazenamento, etc.)
  - Para gerenciar qualquer tipo de recurso, atribua a função **Colaborador** .

A atribuição de função para seu aplicativo é visível para os usuários, escolha o privilégio mínimo necessário.

Chame a [API de definição de função do Gerenciador de recursos](https://msdn.microsoft.com/library/azure/dn906879.aspx) para listar todas as funções RBAC do Azure e pesquisa depois iteramos sobre o resultado para localizar a definição de função desejada por nome.

O método [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) do aplicativo ASP.net MVC amostra implementa essa chamada.

O exemplo de solicitação a seguir mostra como obter o identificador da função RBAC do Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb é a id da assinatura.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

A resposta é no seguinte formato: 

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Você não precisa chamar esta API continuamente. Depois de determinar o GUID conhecida da definição de função, você pode construir a id de definição de função como:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Aqui estão os guids conhecidos de funções internas comumente usados:

| Função | GUID |
| ----- | ------ |
| Leitor | acdd72a7-3385-48ef-bd42-f606fba81ae7
| Colaborador | b24988ac-6180-42a0-ab88-20f7382dd24c
| Colaborador de máquina virtual | d73bb868-a0df-4d4d-bd69-98a00b01fccb
| Rede virtual Colaborador | b34d265f-36f7-4a0d-a4d4-e158ca92e90f
| Colaborador de conta de armazenamento | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25
| Colaborador de site | de139f84-1756-47ae-9be6-808fbbe84772
| Colaborador do plano de Web | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b
| SQL Server Colaborador | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437
| SQL DB Colaborador | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec

### <a name="assign-rbac-role-to-application"></a>Atribuir função RBAC ao aplicativo

Você terá tudo o que você precisa atribuir a função RBAC apropriada ao seu serviço principal usando o [Gerenciador de recursos de criar a atribuição de função](https://msdn.microsoft.com/library/azure/dn906887.aspx) API.

O método [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) do aplicativo ASP.net MVC amostra implementa essa chamada.

Uma solicitação de exemplo para atribuir função RBAC ao aplicativo: 

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

Na solicitação, os seguintes valores são usados:

| GUID | Descrição |
| ------ | --------- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb | a id da assinatura
| c3097b31-7309-4c59-b4e3-770f8406bad2 | id de objeto de capital de serviço do aplicativo
| acdd72a7-3385-48ef-bd42-f606fba81ae7 | a id da função Leitor
| 4f87261d-2816-465d-8311-70a27558df4c | um novo guid criado para a nova atribuição de função

A resposta é no seguinte formato: 

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Obter acesso somente aplicativo token para o Gerenciador de recursos do Azure

Para validar esse aplicativo tem desejado acessar com assinaturas, executar uma tarefa de teste na assinatura usando um token somente aplicativo.

Para obter um aplicativo somente acesso de token, siga instruções da seção [obter acesso somente aplicativo token de API do Azure AD Graph](#app-azure-ad-graph), com um valor diferente para o parâmetro de recurso: 

    https://management.core.windows.net/

O método [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) do aplicativo de exemplo ASP.NET MVC obtém um acesso somente aplicativo token para o Gerenciador de recursos do Azure usando a biblioteca de autenticação do Active Directory para .net.

#### <a name="get-applications-permissions-on-subscription"></a>Obter permissões do aplicativo na assinatura

Para verificar se o seu aplicativo tem o acesso desejado em uma assinatura do Azure, você também pode chamar as [Permissões de gerente de recursos da](https://msdn.microsoft.com/library/azure/dn906889.aspx) API. Essa abordagem é semelhante a como você determinado se o usuário tem direitos de gerenciamento de acesso para a assinatura. No entanto, dessa vez, ligue para as permissões de API com o token de acesso somente aplicativo que você tenha recebido na etapa anterior.

O método [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) do aplicativo ASP.NET MVC amostra implementa essa chamada.

## <a name="manage-connected-subscriptions"></a>Gerenciar assinaturas conectadas

Quando a função RBAC apropriada é atribuída ao serviço do seu aplicativo principal com assinaturas, seu aplicativo pode manter o monitoramento/Gerenciando usando tokens de acesso somente aplicativo para o Gerenciador de recursos do Azure.

Se um proprietário de assinatura remove a atribuição de função do seu aplicativo usando o portal clássico ou ferramentas de linha de comando, seu aplicativo não é mais possível acessar essa assinatura. Nesse caso, você deve notificar o usuário que a conexão com a assinatura foi danificado de fora do aplicativo e dar a elas uma opção para "corrigir" a conexão. "Reparo" simplesmente criaria novamente a atribuição de função que foi excluída offline.

Assim como você ativou o usuário se conecte assinaturas ao seu aplicativo, você deve permitir que o usuário desconectar assinaturas demais. Do access gerenciamento ponto de Vista, desconecte significa remover a atribuição de função principal do serviço do aplicativo com a assinatura. Opcionalmente, qualquer estado no aplicativo para a assinatura pode ser removido muito. Apenas usuários com permissão de gerenciamento de acesso na assinatura são capazes de desconectar a assinatura.

O [método de RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) do aplicativo ASP.net MVC amostra implementa essa chamada.

Isso - os usuários podem agora facilmente conectar e gerenciar suas assinaturas Azure com seu aplicativo.

