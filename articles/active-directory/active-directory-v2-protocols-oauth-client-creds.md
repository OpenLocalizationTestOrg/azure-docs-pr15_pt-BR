
<properties
    pageTitle="Fluxo de credenciais de cliente do Azure AD v 2.0 OAuth | Microsoft Azure"
    description="Criando aplicativos da web usando a implementação do Azure AD do protocolo de autenticação OAuth 2.0."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20-client-credentials-flow"></a>fluem de protocolos de v 2.0 - OAuth 2.0 Client credenciais

As [credenciais de cliente OAuth 2.0 conceder](http://tools.ietf.org/html/rfc6749#section-4.4), às vezes chamado de "duas pernas OAuth," pode ser usado para acessar os recursos da web hospedado usando a identidade de um aplicativo.  Ele geralmente é usado para interações de servidor para servidor que devem ser executado em segundo plano sem a presença imediata de um usuário final.  Esses tipos de aplicativos são conhecidos como **daemons** ou **contas de serviço**.

> [AZURE.NOTE]
    Nem todos os cenários do Active Directory do Azure e recursos são compatíveis com o ponto de extremidade de v 2.0.  Para determinar se você deve usar o ponto de extremidade de v 2.0, leia sobre as [limitações da versão 2.0](active-directory-v2-limitations.md).

Na mais comum três "com três derivações OAuth," um aplicativo cliente tem permissão para acessar um recurso em nome de um usuário específico.  A permissão é **delegada** do usuário para o aplicativo, normalmente durante o processo de [consentimento](active-directory-v2-scopes.md) .  No entanto, no fluxo de credenciais de cliente, permissões são concedidas **diretamente** ao próprio aplicativo.  Quando o aplicativo apresenta um token para um recurso, o recurso impõe que o próprio aplicativo tem autorização para executar alguma ação - não se algum usuário tem autorização.

## <a name="protocol-diagram"></a>Diagrama de protocolo
O fluxo de credenciais do cliente inteiro esta aparência: cada uma das etapas são descritas em detalhes abaixo.

![Fluxo de credenciais do cliente](../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Obter autorização direta 
Há duas maneiras que um aplicativo normalmente recebe autorização direta para acessar um recurso: por meio de uma lista de controle de acesso no recurso ou atribuição de permissão de aplicativo no Azure AD.  Há várias outras maneiras de que um recurso pode optar por autorizar seus clientes e servidor cada recurso pode escolher o método que faz mais sentido para seu aplicativo.  Esses dois métodos são mais comuns no Azure AD e recomendados para clientes e recursos que desejam executar o fluxo de credenciais do cliente.

### <a name="access-control-lists"></a>Listas de controle de acesso
Um provedor de determinado recurso pode impor uma verificação de autorização com base em uma lista de IDs de aplicativo que ele sabe e concede alguns determinado nível de acesso.  Quando o recurso recebe um token do ponto de extremidade v 2.0, ele pode decodificar o token e extrair a ID de aplicativo do cliente do `appid` e `iss` declarações.  Em seguida, ele pode comparar o aplicativo contra alguns lista de controle de acesso (ACL) que ele mantém.  O detalhamento e o método da lista de controle de acesso podem variar significativamente de um recurso para o recurso.

Um caso de uso comum para tais ACLs é testar corredores para um aplicativo web ou web api.  Web api talvez conceda apenas um subconjunto de suas permissões completos seus vários clientes.  Mas para executar testes de ponta a ponta na api, um cliente de teste é criado que adquire tokens do ponto de extremidade v 2.0 e envia à api.  A api pode ACL ID de aplicativo de teste do cliente para acesso completo a funcionalidade de inteiro da api.  Observe que se você tiver uma lista como essa em seu serviço, você deve ter certeza validar não apenas o chamador `appid`, mas também validar que o `iss` do token é confiável também.

Esse tipo de autorização é comum daemons e contas de serviço que precise acessar dados pertencentes consumidor usuários com contas pessoais do Microsoft.  Para dados pertencentes organizações, é recomendável que você adquirir a autorização necessária via perimssions de aplicativo.

### <a name="application-permissions"></a>Permissões de aplicativo
Em vez de usar ACLs, APIs pode expor um conjunto de **permissões de aplicativos** que podem ser concedidos a um aplicativo.  Uma permissão de aplicativo é concedida a um aplicativo por um administrador de uma organização e só pode ser usada para acessar dados pertencentes a organização e seus funcionários.  Por exemplo, o Microsoft Graph expõe várias permissões de aplicativo:

- Lendo emails em todas as caixas de correio
- Ler e gravar emails em todas as caixas de correio
- Enviar um email como qualquer usuário
- Ler dados de diretório
- [+ mais](https://graph.microsoft.io)

Para adquirir essas permissões em seu aplicativo, você pode executar as seguintes etapas.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicitar as permissões no portal de registro do aplicativo

- Navegue até seu aplicativo em [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou [criar um aplicativo](active-directory-v2-app-registration.md) se você ainda não fez isso.  Você precisará garantir que seu aplicativo tenha criado pelo menos um segredo do aplicativo.
- Localize a seção **Permissões de aplicativo diretas** e adicionar as permissões exigidas pelo seu aplicativo.
- Certifique-se de **Salvar** o registro de aplicativo

#### <a name="recommended-sign-the-user-into-your-app"></a>Recomendado: entrar do usuário no seu aplicativo

Normalmente quando criar um aplicativo que usa as permissões do aplicativo, o aplicativo precisa ter uma página/exibição que permite que o administrador aprovar permissões do aplicativo.  Esta página pode ser parte do fluxo de inscrição do aplicativo, parte das configurações do aplicativo ou um fluxo de "conectar" dedicado.  Em muitos casos, faz sentido para o aplicativo mostrar isso "conectar" Exibir somente depois que um usuário tem conectados com um trabalho ou escola conta da Microsoft.

Assinatura do usuário no aplicativo permite que você identifique a organização à qual o usuário pertence antes solicitando aprovem as permissões do aplicativo.  Enquanto não terminantemente necessário, ele pode ajudá-lo a criar uma experiência mais intuitiva para seus usuários organizacionais.  Para assinar o usuário na, siga nossos [tutoriais de protocolo v 2.0](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Solicitar as permissões de um administrador de diretório

Quando você estiver pronto para solicitar permissões de administrador da empresa, você pode redirecionar o usuário para o v 2.0 **Administração consentimento ponto de extremidade**.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| locatário | Necessário | O locatário de diretório que você deseja solicitar permissão de.  Pode ser fornecido no formato de nome amigável ou guid.  Se você não souber qual locatário o usuário pertence e quiser permitir que ela entrar com qualquer locatário, use `common`. |
| client_id | Necessário | A Id de aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuída a seu aplicativo. |
| redirect_uri | Necessário | O redirect_uri onde você deseja que a resposta a ser enviada para o aplicativo tratar.  Ele deve corresponder exatamente um da redirect_uris que você registrado no portal do, exceto deve ser codificada de url e pode ter segmentos de caminho adicionais. |
| estado | recomendados | Um valor incluído na solicitação que também será retornada na resposta token.  Pode ser uma cadeia de caracteres de qualquer conteúdo que desejar.  O estado é usado para codificar informações sobre o estado do usuário no aplicativo antes de que ocorreu a solicitação de autenticação, como a página ou o modo de exibição estivessem em. |

Neste ponto, o Azure AD aplicará somente um administrador locatário pode entrar para concluir a solicitação.  O administrador será solicitado a aprovar todas as permissões de aplicação direta solicitado para o aplicativo no portal do registro. 

##### <a name="successful-response"></a>Resposta bem-sucedida
Se o administrador aprova as permissões do aplicativo, a resposta bem-sucedida será:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| locatário | O locatário de diretório que as permissões seu aplicativo-solicitada, no formato de guid. |
| estado | Um valor incluído na solicitação que também será retornada na resposta token.  Pode ser uma cadeia de caracteres de qualquer conteúdo que desejar.  O estado é usado para codificar informações sobre o estado do usuário no aplicativo antes de que ocorreu a solicitação de autenticação, como a página ou o modo de exibição estivessem em. |
| admin_consent | Será definido como `True`. |


##### <a name="error-response"></a>Resposta de erro
Se o administrador não aprovar as permissões do aplicativo, a resposta falha será:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| Erro | Uma sequência de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| error_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa de um erro.  |

Depois que você recebeu resposta bem-sucedida do aplicativo ponto de extremidade de provisionamento, seu aplicativo obteve as permissões de aplicativo direct-solicitada.  Agora, você pode mover para solicitar um símbolo para o recurso desejado.

## <a name="get-a-token"></a>Obter um símbolo
Depois que você tiver adquirido a autorização necessária para seu aplicativo, você pode prosseguir com a aquisição de tokens de acesso para APIs.  Para obter um token usando o cliente conceder de credenciais, envie uma solicitação de POSTAGEM para o `/token` ponto de extremidade de v 2.0:

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| client_id | Necessário | A Id de aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuída a seu aplicativo. |
| escopo | Necessário | O valor passado o `scope` parâmetro nesta solicitação deve ser o identificador de recursos (URI de ID de aplicativo) do recurso desejado, fixado com a `.default` sufixo.  Para o exemplo do Microsoft Graph dado, o valor deve ser `https://graph.microsoft.com/.default`.  Este valor informa o ponto de extremidade de v 2.0 que de todas as permissões de aplicação direta que você configurou para o aplicativo, ele deve emitir um token para aquelas pertencentes ao recurso desejado. |
| client_secret | Necessário | O segredo de aplicativo que você gerou no portal do registro para o aplicativo. |
| grant_type | Necessário | Deve ser `client_credentials`. | 

#### <a name="successful-response"></a>Resposta bem-sucedida
Uma resposta bem-sucedida levará o formulário:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access_token | O token de acesso solicitado. O aplicativo pode usar esse token para autenticar o recurso protegido, como um web API. |
| token_type | Indica o valor de tipo de token. O único tipo que suporta Azure AD é `Bearer`.  |
| expires_in | Por quanto tempo o token de acesso é válido (em segundos). |

#### <a name="error-response"></a>Resposta de erro
Uma resposta de erro levará o formulário:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| Erro | Uma sequência de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| error_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa de um erro de autenticação.  |
| error_codes | Uma lista de códigos de erro específicos de STS que podem ajudar no diagnóstico.  |
| carimbo de hora | A hora em que o erro ocorreu. |
| trace_id | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico.  |
| correlation_id | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico nos componentes. |

## <a name="use-a-token"></a>Use um token
Agora que você já tiver adquirido um token, você pode usar esse token para fazer solicitações ao recurso.  Quando o token expira, basta repetir a solicitação para o `/token` ponto de extremidade adquirir um token de acesso clara.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro Tip: Try the below command out! (but replace the token with your own)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>Amostra de código
Para ver um exemplo de um aplicativo que implementa que o client_credentials conceder usando o administrador consentimento ponto de extremidade, consulte nosso [exemplo de código de daemon v 2.0](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
