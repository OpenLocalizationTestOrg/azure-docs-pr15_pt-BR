

<properties
    pageTitle="Fluxo de código do Azure AD v 2.0 OAuth autorização | Microsoft Azure"
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
    ms.date="08/08/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20-authorization-code-flow"></a>v 2.0 protocolos - fluxo de código de autorização OAuth 2.0

A concessão de código de autorização OAuth 2.0 pode ser usada nos aplicativos que são instalados em um dispositivo para obter acesso aos recursos protegidos, como web APIs.  Usando a implementação do aplicativo modelo v 2.0 do OAuth 2.0, você pode adicionar entrar e API acessar seus aplicativos de desktop e móveis.  Este guia é independente de idioma e descreve como enviar e receber mensagens HTTP sem usar qualquer um dos nossas bibliotecas de código-fonte aberto.

<!-- TODO: Need link to libraries -->

> [AZURE.NOTE]
    Nem todos os cenários do Active Directory do Azure e recursos são compatíveis com o ponto de extremidade de v 2.0.  Para determinar se você deve usar o ponto de extremidade de v 2.0, leia sobre as [limitações da versão 2.0](active-directory-v2-limitations.md).

O fluxo de código de autorização OAuth 2.0 é descrito na seção [4.1 da especificação OAuth 2.0](http://tools.ietf.org/html/rfc6749).  Ele é usado para executar a autenticação e a autorização na maioria dos tipos de aplicativo, incluindo [web apps](active-directory-v2-flows.md#web-apps) e [instalado nativamente aplicativos](active-directory-v2-flows.md#mobile-and-native-apps).  Ele permite que aplicativos com segurança adquirir access_tokens que podem ser usadas para acessar recursos que são protegidos usando o ponto de extremidade de v 2.0.  

## <a name="protocol-diagram"></a>Diagrama de protocolo
Em um alto nível, o fluxo de autenticação inteiros de um aplicativo nativo/mobile um pouco esta aparência:

![Fluxo de código de Auth OAuth](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="request-an-authorization-code"></a>Solicitar um código de autorização
O fluxo de código de autorização começa com o cliente direciona o usuário para o `/authorize` ponto de extremidade.  Nesta solicitação, o cliente indica as permissões que ele precisa adquirir do usuário:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [AZURE.TIP] Clique no link abaixo para executar esta solicitação! Após entrar, seu navegador deve ser redirecionado para `https://localhost/myapp/` com um `code` na barra de endereços.
    <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/Common/oauth2/v2.0/Authorize...</a>

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| locatário | Necessário | O `{tenant}` valor no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo.  Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de locatários.  Para obter mais detalhes, consulte [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id | Necessário | A Id de aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuída a seu aplicativo. |
| response_type | Necessário | Deve incluir `code` para o fluxo de código de autorização. |
| redirect_uri | recomendados | O redirect_uri do aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas por seu aplicativo.  Ele deve corresponder exatamente um da redirect_uris que você registrado no portal do, exceto deve ser codificada de url.  Para aplicativos nativos e móveis, você deve usar o valor padrão de `https://login.microsoftonline.com/common/oauth2/nativeclient`. |
| escopo | Necessário | Uma lista separada de [escopos](active-directory-v2-scopes.md) que deseja que o usuário concorda com.  |
| response_mode | recomendados | Especifica o método que deve ser usado para enviar o back token resultante para seu aplicativo.  Pode ser `query` ou `form_post`.  |
| estado | recomendados | Um valor incluído na solicitação que também será retornada na resposta token.  Pode ser uma cadeia de caracteres de qualquer conteúdo que desejar.  Um valor exclusivo gerado aleatoriamente normalmente é usado para [prevenir ataques de falsificação de solicitação intersite](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes de que ocorreu a solicitação de autenticação, como a página ou o modo de exibição estivessem em. |
| prompt | opcional | Indica o tipo de interação do usuário é necessária.  Os valores válidos somente neste momento são 'login', 'Nenhum' e 'consentimento'.  `prompt=login`forçar o usuário inserir suas credenciais nessa solicitação, eliminando single sign-no.  `prompt=none`é o oposto - garantirá que o usuário não será apresentado qualquer prompt interativo qualquer.  Se a solicitação não pode ser concluída silenciosamente por meio do logon único no, o ponto de extremidade de v 2.0 retornará um erro.  `prompt=consent`acionará a caixa de diálogo de consentimento OAuth depois que o usuário entra no, solicitando que o usuário para conceder permissões para o aplicativo. |
| login_hint | opcional | Pode ser usado para preencher previamente o campo de endereço de email/nome de usuário da página de entrada do usuário, se você souber o nome de usuário antecedência.  Muitas vezes aplicativos usará esse parâmetro durante nova autenticação, já ter extraído o nome de usuário de uma entrada usando anterior a `preferred_username` reivindicar. |
| domain_hint | opcional | Pode ser uma das `consumers` ou `organizations`.  Se incluída, ele ignorará o processo de descoberta baseada em email esse usuário percorre na página, entrar v 2.0 levam a uma experiência de usuário simplificada um pouco mais.  Muitas vezes aplicativos usará esse parâmetro durante nova autenticação, extraindo a `tid` de um entrar anterior.  Se o `tid` reivindicar valor é `9188040d-6c67-4c5b-b112-36a304b66dad`, você deve usar `domain_hint=consumers`.  Caso contrário, use `domain_hint=organizations`. |

Neste ponto, o usuário será solicitado a digitar suas credenciais e concluir a autenticação.  O ponto de extremidade de v 2.0 garantirá que o usuário aceitou as permissões indicadas na `scope` parâmetro de consulta.  Se o usuário não aceitou a qualquer uma das permissões, ele perguntará ao usuário concordar com as permissões necessárias.  Detalhes de [permissões, consentimento e locatários vários aplicativos são fornecidos aqui](active-directory-v2-scopes.md).

Depois que o usuário faz a autenticação e concede consentimento, o ponto de extremidade de v 2.0 retornará uma resposta para o seu aplicativo no indicado `redirect_uri`, usando o método especificado na `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta bem-sucedida
Uma resposta bem-sucedida usando `response_mode=query` aparência semelhante a:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| código | O authorization_code que o aplicativo solicitado. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino.  Authorization_codes são vida muito curta, normalmente expirarem após cerca de 10 minutos. |
| estado | Se um parâmetro de estado for incluído na solicitação, o mesmo valor deve aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro
Respostas de erro também podem ser enviadas para o `redirect_uri` para que o aplicativo pode manipulá-los adequadamente:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| Erro | Uma sequência de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| error_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa de um erro de autenticação.  |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto de extremidade de autorização

A tabela a seguir descreve os diversos códigos de erro que podem ser retornados na `error` parâmetro da resposta erro.

| Código de erro | Descrição | Ação de cliente |
|------------|-------------|---------------|
| invalid_request | Erro de protocolo, como um parâmetro necessário ausente. | Corrija e reenviar a solicitação. Este é um desenvolvimento erro geralmente é detectado durante o teste inicial.|
| unauthorized_client | O aplicativo cliente não é permitido para solicitar um código de autorização. | Isso geralmente ocorre quando o aplicativo cliente não estiver registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar o usuário com instruções para instalar o aplicativo e adicioná-lo ao Azure AD. |
| ACCESS_DENIED | Proprietário do recurso negado consentimento | O aplicativo cliente pode notificar o usuário que ele não pode continuar a menos que o usuário. |
| unsupported_response_type | O servidor de autorização não suporta o tipo de resposta na solicitação. | Corrija e reenviar a solicitação. Este é um desenvolvimento erro geralmente é detectado durante o teste inicial.|
|server_error | O servidor encontrou um erro inesperado. | Repita a solicitação. Esses erros podem resultar de condições temporárias. O aplicativo cliente pode explicar para o usuário que sua resposta estiver atrasada há um erro temporário de conclusão. |
| temporarily_unavailable | O servidor está temporariamente ocupado para lidar com a solicitação. | Repita a solicitação. O aplicativo cliente pode explicar para o usuário que sua resposta é atrasada devido uma condição temporária. |
| invalid_resource |O recurso de destino é inválido porque ela não existir, Azure AD não consegue encontrar ou não está configurado corretamente.| Isso indica que o recurso, se ele existir, não foi configurado no locatário. O aplicativo pode solicitar o usuário com instruções para instalar o aplicativo e adicioná-lo ao Azure AD. |

## <a name="request-an-access-token"></a>Solicitar um token de acesso
Agora que você já tiver adquirido uma authorization_code e receberam permissão pelo usuário, você pode recuperar o `code` para um `access_token` para o recurso desejado, enviando um `POST` solicitar a `/token` ponto de extremidade:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] Tente executar esta solicitação no carteiro! (Não se esqueça de substituir o `code`)     [ ![Executados no carteiro](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------------- |
| locatário | Necessário | O `{tenant}` valor no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo.  Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de locatários.  Para obter mais detalhes, consulte [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id | Necessário | A Id de aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuída a seu aplicativo. |
| grant_type | Necessário | Deve ser `authorization_code` para o fluxo de código de autorização. |
| escopo | Necessário | Uma lista separada de escopos.  Os escopos solicitados neste trecho devem ser equivalente a ou um subconjunto dos escopos solicitado no trecho do primeiro.  Se os escopos especificados nesta solicitação abrangem vários servidores do recurso, o ponto de extremidade de v 2.0 retornará um símbolo para o recurso especificado no primeiro escopo.  Para obter uma explicação mais detalhada dos escopos, consulte [permissões, consentimento e escopos](active-directory-v2-scopes.md).  |
| código | Necessário | O authorization_code que você adquiriu no primeiro trecho do fluxo.   |
| redirect_uri | Necessário | O mesmo valor de redirect_uri que foi usado para adquirir a authorization_code. |
| client_secret | necessário para aplicativos web | O segredo de aplicativo que você criou no portal de registro do aplicativo para o aplicativo.  Ele não deve ser usado em um aplicativo nativo, porque client_secrets não podem ser confiavelmente armazenadas em dispositivos.  É necessário para aplicativos web e web APIs, que têm a capacidade para armazenar a client_secret com segurança no lado do servidor. |

#### <a name="successful-response"></a>Resposta bem-sucedida
Uma resposta de token bem-sucedida será parecida com:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access_token | O token de acesso solicitado. O aplicativo pode usar esse token para autenticar o recurso protegido, como um web API. |
| token_type | Indica o valor de tipo de token. O único tipo que ofereça suporte Azure AD é portador  |
| expires_in | Por quanto tempo o token de acesso é válido (em segundos). |
| escopo | Os escopos que o access_token é válido para. |
| refresh_token |  Um token de atualização OAuth 2.0. O aplicativo pode usar esse token adquira tokens de acesso adicionais após expiração do token de acesso atual.  Refresh_tokens são de vida longa e pode ser usado para manter o acesso aos recursos por longos períodos de tempo.  Para obter mais detalhes, consulte a [referência de token de v 2.0](active-directory-v2-tokens.md).  |
| id_token | Um unsigned JSON Web Token (JWT). O aplicativo pode base64Url decodificar os segmentos desse token solicite informações sobre o usuário que conectado. O aplicativo pode armazenar em cache os valores e exibi-los, mas ele não deve dependê-las para qualquer autorização ou limites de segurança.  Para obter mais informações sobre id_tokens consulte a [referência de token de ponto de extremidade de v 2.0](active-directory-v2-tokens.md). |

#### <a name="error-response"></a>Resposta de erro
Respostas de erro serão parecida com:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
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

#### <a name="error-codes-for-token-endpoint-errors"></a>Códigos de erro para erros de ponto de extremidade de token

| Código de erro | Descrição | Ação de cliente |
|------------|-------------|---------------|
| invalid_request | Erro de protocolo, como um parâmetro necessário ausente. | Corrigir e reenviar a solicitação |
| invalid_grant | O código de autorização é inválido ou expirou. | Tente uma nova solicitação para o `/authorize` ponto de extremidade |
| unauthorized_client | O cliente autenticado não está autorizado a usar este tipo de conceder autorização. | Isso geralmente ocorre quando o aplicativo cliente não estiver registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar o usuário com instruções para instalar o aplicativo e adicioná-lo ao Azure AD. |
| invalid_client | Falha de autenticação de cliente. | As credenciais do cliente não são válidas. Para corrigir, o administrador do aplicativo atualiza as credenciais. |
| unsupported_grant_type | O servidor de autorização não suporta o tipo de conceder autorização. | Altere o tipo de conceder na solicitação. Esse tipo de erro deve ocorrer somente durante o desenvolvimento e detectado durante o teste inicial. |
| invalid_resource | O recurso de destino é inválido porque ela não existir, Azure AD não consegue encontrar ou não está configurado corretamente. | Isso indica que o recurso, se ele existir, não foi configurado no locatário. O aplicativo pode solicitar o usuário com instruções para instalar o aplicativo e adicioná-lo ao Azure AD. |
| interaction_required | A solicitação exige interação do usuário. Por exemplo, é necessária uma etapa de autenticação adicionais. | Repita a solicitação com o mesmo recurso. |
| temporarily_unavailable | O servidor está temporariamente ocupado para lidar com a solicitação. | Repita a solicitação. O aplicativo cliente pode explicar para o usuário que sua resposta é atrasada devido uma condição temporária.|

## <a name="use-the-access-token"></a>Use o token de acesso
Agora que você já adquiriu com êxito um `access_token`, você pode usar o token em solicitações de APIs da Web, incluindo-o na `Authorization` cabeçalho:

> [AZURE.TIP] Execute esta solicitação no carteiro! (Substituir o `Authorization` cabeçalho primeiro)     [ ![Executados no carteiro](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Atualizar o token de acesso
Access_tokens são curtas vivia e atualize-os depois que elas expirarem para continuar a acessar recursos.  Você pode fazer isso enviando outra `POST` solicitar a `/token` ponto de extremidade, dessa vez fornecendo a `refresh_token` , em vez da `code`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] Tente executar esta solicitação no carteiro! (Não se esqueça de substituir o `refresh_token`)     [ ![Executados no carteiro](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | -------- |
| locatário | Necessário | O `{tenant}` valor no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo.  Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de locatários.  Para obter mais detalhes, consulte [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id | Necessário | A Id de aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuída a seu aplicativo. |
| grant_type | Necessário | Deve ser `refresh_token` para esse trecho do fluxo de código de autorização. |
| escopo | Necessário | Uma lista separada de escopos.  Os escopos solicitados neste trecho devem ser equivalente a ou um subconjunto dos escopos solicitado no trecho de solicitação de authorization_code original.  Se os escopos especificados nesta solicitação abrangem vários servidores do recurso, o ponto de extremidade de v 2.0 retornará um símbolo para o recurso especificado no primeiro escopo.  Para obter uma explicação mais detalhada dos escopos, consulte [permissões, consentimento e escopos](active-directory-v2-scopes.md).  |
| refresh_token | Necessário | O refresh_token que você adquiriu no segundo trecho do fluxo.   |
| redirect_uri | Necessário | O mesmo valor de redirect_uri que foi usado para adquirir a authorization_code. |
| client_secret | necessário para aplicativos web | O segredo de aplicativo que você criou no portal de registro do aplicativo para o aplicativo.  Ele não deve ser usado em um aplicativo nativo, porque client_secrets não podem ser confiavelmente armazenadas em dispositivos.  É necessário para aplicativos web e web APIs, que têm a capacidade para armazenar a client_secret com segurança no lado do servidor. |

#### <a name="successful-response"></a>Resposta bem-sucedida
Uma resposta de token bem-sucedida será parecida com:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access_token | O token de acesso solicitado. O aplicativo pode usar esse token para autenticar o recurso protegido, como um web API. |
| token_type | Indica o valor de tipo de token. O único tipo que ofereça suporte Azure AD é portador  |
| expires_in | Por quanto tempo o token de acesso é válido (em segundos). |
| escopo | Os escopos que o access_token é válido para. |
| refresh_token |  Um novo token de atualização de OAuth 2.0. Você deve substituir o antigo token de atualização com esse token de atualização recentemente comprada para garantir que seu tokens de atualização permanecem válidos para contanto que possível.  |
| id_token | Um unsigned JSON Web Token (JWT). O aplicativo pode base64Url decodificar os segmentos desse token solicite informações sobre o usuário que conectado. O aplicativo pode armazenar em cache os valores e exibi-los, mas ele não deve dependê-las para qualquer autorização ou limites de segurança.  Para obter mais informações sobre id_tokens consulte a [referência de token de ponto de extremidade de v 2.0](active-directory-v2-tokens.md). |

#### <a name="error-response"></a>Resposta de erro
```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
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

Para obter uma descrição de códigos de erro e a ação de cliente recomendado, consulte [códigos de erro para erros de token de ponto de extremidade](#error-codes-for-token-endpoint-errors).
