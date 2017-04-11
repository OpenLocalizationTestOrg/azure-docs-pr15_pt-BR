<properties
    pageTitle="Visão geral do Azure AD .NET protocolo | Microsoft Azure"
    description="Este artigo descreve como usar mensagens HTTP para autorizar o acesso a aplicativos da web e web APIs no seu locatário usando o Active Directory do Azure e OAuth 2.0."
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="priyamo"/>


# <a name="authorize-access-to-web-applications-using-oauth-20-and-azure-active-directory"></a>Autorize acesso aos aplicativos web usando OAuth 2.0 e Active Directory do Azure

Azure Active Directory (AD Azure) usa OAuth 2.0 para que você possa autorizar o acesso a aplicativos da web e web APIs em seu locatário do Azure AD. Este guia é independente de linguagem e descreve como enviar e receber mensagens HTTP sem usar qualquer um dos nossas bibliotecas de código-fonte aberto.

O fluxo de código de autorização OAuth 2.0 é descrito na [seção 4.1 da especificação OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1) . Ele é usado para executar a autenticação e a autorização na maioria dos tipos de aplicativo, incluindo aplicativos web e instalado nativamente aplicativos.

[AZURE.INCLUDE [active-directory-protocols-getting-started](../../includes/active-directory-protocols-getting-started.md)]


## <a name="oauth-20-authorization-flow"></a>Fluxo de autorização OAuth 2.0

Em um alto nível, o fluxo de autorização inteira para um aplicativo um pouco esta aparência:

![Fluxo de código de Auth OAuth](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)


## <a name="request-an-authorization-code"></a>Solicitar um código de autorização

O fluxo de código de autorização começa com o cliente direciona o usuário para o `/authorize` ponto de extremidade. Nesta solicitação, o cliente indica as permissões que ele precisa adquirir do usuário. Você pode acessar os pontos de extremidade OAuth 2.0 de página do seu aplicativo no Portal de clássico do Azure, no botão de **Pontos de extremidade do modo de exibição** na parte inferior registradora.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| locatário | Necessário | O `{tenant}` valor no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo.  Os valores permitidos são identificadores de locatário, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para tokens de locatário independente |
| client_id | Necessário | A Id de aplicativo atribuído ao seu aplicativo quando você registrou Azure AD. Você pode encontrar isso no Portal de gerenciamento do Azure. Clique no **Active Directory**, clique no diretório, clique no aplicativo e, em seguida, clique em **Configurar** |
| response_type | Necessário | Deve incluir `code` para o fluxo de código de autorização. |
| redirect_uri | recomendados | O redirect_uri do aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas por seu aplicativo.  Ele deve corresponder exatamente um da redirect_uris que você registrado no portal do, exceto deve ser codificada de url.  Para aplicativos nativos e móveis, você deve usar o valor padrão de `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode | recomendados | Especifica o método que deve ser usado para enviar o back token resultante para seu aplicativo.  Pode ser `query` ou `form_post`.  |
| estado | recomendados | Um valor incluído na solicitação que também será retornada na resposta token. Um valor exclusivo gerado aleatoriamente normalmente é usado para [prevenir ataques de falsificação de solicitação intersite](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes de que ocorreu a solicitação de autenticação, como a página ou o modo de exibição estivessem em. |
| recurso | opcional | O URI de ID do aplicativo da web API (recurso protegido). Para localizar o URI de ID do aplicativo da web API, no Portal de gerenciamento do Azure, clique em **Active Directory**, clique no diretório, clique no aplicativo e clique em **Configurar**. |
| prompt | opcional |  Indica o tipo de interação do usuário é necessária.<p> Valores válidos são: <p> *logon*: O usuário deve ser solicitado a autenticar novamente. <p> *consentimento*: consentimento do usuário foi concedido, mas precisa ser atualizado. O usuário deve ser solicitado para consentimento. <p> *admin_consent*: um administrador deve ser solicitado a consentimento em nome de todos os usuários em sua organização |
| login_hint | opcional | Pode ser usado para preencher previamente o campo de endereço de email/nome de usuário da página de entrada do usuário, se você souber o nome de usuário antecedência.  Muitas vezes aplicativos usará esse parâmetro durante nova autenticação, já ter extraído o nome de usuário de uma entrada usando anterior a `preferred_username` reivindicar. |
| domain_hint | opcional | Fornece uma dica sobre o locatário ou o domínio que o usuário deve usar para entrar. O valor do domain_hint é um domínio registrado do locatário. Se o locatário federado para um diretório local, AAD redireciona para o servidor de Federação do locatário especificado. |

> [AZURE.NOTE] Se o usuário for parte de uma organização, um administrador da organização pode consentimento recusar em nome do usuário ou permitir que o usuário consentimento. O usuário terá a opção de consentimento somente quando o administrador permita.

Neste ponto, o usuário será solicitado a inserir suas credenciais e consentimento para as permissões indicadas na `scope` parâmetro de consulta. Depois que o usuário faz a autenticação e concede consentimento, Azure AD envia uma resposta para o seu aplicativo na `redirect_uri` endereço em sua solicitação.

### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida pode ter esta aparência:

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parâmetro | Descrição |
| -----------------------| --------------- |
| admin_consent | O valor é verdadeiro se um administrador consentiu um prompt de solicitação de consentimento.|
| código | O código de autorização que o aplicativo solicitado. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. |
| session_state | Um valor exclusivo que identifica a sessão de usuário atual. Esse valor é um GUID, mas deve ser tratado como um valor opaco que é passado sem exame. |
| estado | Se um parâmetro de estado for incluído na solicitação, o mesmo valor deve aparecer na resposta. É uma boa prática para o aplicativo verificar se os valores de estado na solicitação e resposta são idênticos antes de usar a resposta. Isso ajuda a detectar [ataques de falsificação de solicitação intersite (CSRF)](https://tools.ietf.org/html/rfc6749#section-10.12) contra o cliente.  

### <a name="error-response"></a>Resposta de erro

Respostas de erro também podem ser enviadas para o `redirect_uri` para que o aplicativo pode manipulá-los adequadamente.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
|-----------|-------------|
| Erro | Um valor de código de erro definido na seção 5.2 do [OAuth 2.0 autorização Framework](http://tools.ietf.org/html/rfc6749). A tabela seguinte descreve os códigos de erro que retorna do Azure AD. |
| error_description | Uma descrição mais detalhada do erro. Essa mensagem não tem a intenção de ser amigável de usuário final. |
| estado | O valor de estado é um valor não reutilizadas gerado aleatoriamente que é enviado na solicitação e retornado na resposta para impedir ataques de falsificação (CSRF) de solicitação intersite. |

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

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Usar o código de autorização para solicitar um token de acesso

Agora que você já tiver adquirido um código de autorização e receberam permissão pelo usuário, você pode recuperar o código para um token de acesso ao recurso desejado, enviando uma solicitação POST para o `/token` ponto de extremidade:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%2Fmyapp%2F
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------------- |
| locatário | Necessário |  O `{tenant}` valor no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo.  Os valores permitidos são identificadores de locatário, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para tokens de locatário independente |
| client_id | Necessário | A Id de aplicativo atribuído ao seu aplicativo quando você registrou Azure AD. Você pode encontrar isso no Portal de clássico do Azure. Clique no **Active Directory**, clique no diretório, clique no aplicativo e, em seguida, clique em **Configurar** |
| grant_type | Necessário | Deve ser `authorization_code` para o fluxo de código de autorização. |
| código | Necessário | O `authorization_code` que você adquiriu na seção anterior   |
| redirect_uri | Necessário | A mesma `redirect_uri` valor que foi usado para adquirir a `authorization_code`. |
| client_secret | necessário para aplicativos web | O segredo de aplicativo que você criou no portal de registro do aplicativo para o aplicativo.  Ele não deve ser usado em um aplicativo nativo, porque client_secrets não podem ser confiavelmente armazenadas em dispositivos.  É necessário para aplicativos web e web APIs, que têm a capacidade para armazenar os `client_secret` com segurança no lado do servidor. |
| recurso | obrigatório se especificado na solicitação de código de autorização, else opcional | O URI de ID do aplicativo da web API (recurso protegido).
Para localizar o URI de ID do aplicativo, no Portal de gerenciamento do Azure, clique em **Active Directory**, clique no diretório, clique no aplicativo e clique em **Configurar**.

### <a name="successful-response"></a>Resposta bem-sucedida

Azure AD retorna um token de acesso durante uma resposta bem-sucedida. Para minimizar as chamadas de rede do aplicativo cliente e seu associados a latência, o aplicativo cliente deve armazenar em cache tokens de acesso para a vida útil do token especificado na resposta OAuth 2.0. Para determinar a vida útil do token, use o `expires_in` ou `expires_on` valores de parâmetro.

Se um recurso de API da web retorna um `invalid_token` código de erro, isso pode indicar que o recurso tiver determinado que o token expirou. Se os tempos de relógio do cliente e recurso são diferentes (conhecido como um "hora distorção"), o recurso pode considerar o token expirar antes do token está desmarcado do cache de cliente. Se isso acontecer, desmarque o token do cache, mesmo se ele ainda estiver dentro de seu ciclo de vida de cálculo.

Uma resposta bem-sucedida pode ter esta aparência:

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
"id_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.”
}

```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access_token | O token de acesso solicitado. O aplicativo pode usar esse token para autenticar o recurso protegido, como um web API. |
| token_type | Indica o valor de tipo de token. O único tipo que ofereça suporte Azure AD é portador. Para obter mais informações sobre tokens de portador, consulte [OAuth2.0 autorização Framework: uso de Token de portador (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)  |
| expires_in | Por quanto tempo o token de acesso é válido (em segundos). |
| expires_on | O tempo quando o token de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até o tempo de expiração. Esse valor é usado para determinar o ciclo de vida de tokens em cache. |
| recurso | O URI de ID do aplicativo da web API (recurso protegido).|
| escopo | Permissões de representação concedidas para o aplicativo cliente. A permissão padrão é `user_impersonation`. O proprietário do recurso protegido pode registrar valores adicionais no Azure AD.|
| refresh_token |  Um token de atualização OAuth 2.0. O aplicativo pode usar esse token adquirir tokens de acesso adicionais após expiração do token de acesso atual.  Atualizar tokens são de vida longa e pode ser usados para manter o acesso aos recursos por longos períodos de tempo. |
| id_token | Um unsigned JSON Web Token (JWT). O aplicativo pode base64Url decodificar os segmentos desse token solicite informações sobre o usuário que conectado. O aplicativo pode armazenar em cache os valores e exibi-los, mas ele não deve dependê-las para qualquer autorização ou limites de segurança. |

### <a name="jwt-token-claims"></a>Declarações do Token de JWT
O token JWT no valor da `id_token` parâmetro pode ser decodificado em declarações a seguir:

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

O `id_token` parâmetro inclui os seguintes tipos de declaração. Para obter mais informações sobre tokens de web JSON, consulte a [especificação de rascunho IETF JWT](http://go.microsoft.com/fwlink/?LinkId=392344). Para obter mais informações sobre os tipos de token e declarações, leia o [Token de suporte e tipos de declaração](active-directory-token-and-claims.md)

| Tipo de declaração | Descrição |
|------------|-------------|
| AUD | Audiência do token. Quando o token emitido para um aplicativo cliente, a audiência é o `client_id` do cliente.
| EXP | Tempo de expiração. O tempo quando o token expira. Para que o token válido, a data/hora atual deve ser menor ou igual a `exp` valor. O tempo é representado como o número de segundos de 1 de janeiro de 1970 (1970-01-01T0:0:0Z) UTC até o momento em que o token foi emitido. |
| family_name | Do usuário último nome ou sobrenome. O aplicativo pode exibir esse valor. |
| given_name | Nome do usuário. O aplicativo pode exibir esse valor. |
| IAT | Emitido momento. A hora em que o JWT foi emitido. O tempo é representado como o número de segundos de 1 de janeiro de 1970 (1970-01-01T0:0:0Z) UTC até o momento em que o token foi emitido. |
| ISS | Identifica o emissor do token |
| NBF | Não antes do tempo. O tempo quando o token entra em vigor. Para o token válido, a data/hora atual deve ser maior ou igual ao valor Nbf. O tempo é representado como o número de segundos de 1 de janeiro de 1970 (1970-01-01T0:0:0Z) UTC até o momento em que o token foi emitido. |
| identificação de objeto | Identificador de objeto (ID) do objeto do usuário no Azure AD. |
| sub | Identificador de token de assunto. Este é um identificador persistente e imutável para o usuário que descreva o token. Use esse valor em cache lógica. |
| TID | Identificador (ID) do locatário Azure AD que emitiu o token do locatário. |
| unique_name | Um identificador exclusivo para que pode ser exibido para o usuário. Isso geralmente é um nome de usuário principal (UPN). |
| UPN | UPN do usuário. |
| ver | Versão. A versão do token JWT, normalmente 1.0. |

### <a name="error-response"></a>Resposta de erro

Os erros de ponto de extremidade de emissão de token são códigos de erro HTTP, porque o cliente chama o ponto de extremidade de emissão de token diretamente. Além do código de status HTTP, o ponto de extremidade de emissão de token do Azure AD também retorna um documento JSON com objetos que descrevem o erro.

Uma resposta de erro de exemplo poderia ter esta aparência:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| Erro | Uma sequência de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| error_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa de um erro de autenticação.  |
| error_codes | Uma lista de códigos de erro específicos de STS que podem ajudar no diagnóstico. |
| carimbo de hora | A hora em que o erro ocorreu. |
| trace_id | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico.  |
| correlation_id | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico nos componentes.|

#### <a name="http-status-codes"></a>Códigos de status HTTP

A tabela a seguir lista os códigos de status HTTP que retorna o ponto de extremidade de emissão de token. Em alguns casos, o código de erro não é suficiente para descrever a resposta, mas no caso de erros, você precisará analisar o documento JSON acompanha e examine seu código de erro.

| Código de HTTP | Descrição |
|-----------|-------------|
| 400       | Código HTTP padrão. Usado na maioria dos casos e é normalmente devido a uma solicitação incorreta. Corrija e reenviar a solicitação. |
| 401       | Falha de autenticação. Por exemplo, a solicitação está faltando o parâmetro client_secret.|
| 403       | Falha de autorização. Por exemplo, o usuário não tem permissão para acessar o recurso. |
| 500       | Ocorreu um erro interno no serviço. Repita a solicitação. |

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

## <a name="use-the-access-token-to-access-the-resource"></a>Use o token de acesso para acessar o recurso

Agora que você já adquiriu com êxito um `access_token`, você pode usar o token em solicitações de APIs da Web, incluindo-o na `Authorization` cabeçalho. A especificação [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) explica como usar tokens de portador em solicitações HTTP para acessar recursos protegidos.

### <a name="sample-request"></a>Solicitação de amostra

```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Resposta de erro

Recursos protegidos que implementar códigos de status HTTP RFC 6750 problema. Se a solicitação não inclui as credenciais de autenticação ou está faltando o token, a resposta inclui um `WWW-Authenticate` cabeçalho. Quando uma solicitação falha, o servidor de recurso responde com o código de status de HTTP e um código de erro.

Este é um exemplo de uma resposta de êxito quando a solicitação do cliente não inclui o token de portador:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.window.net/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Parâmetros de erro

| Parâmetro | Descrição |
|-----------|-------------|
| authorization_uri | O URI (ponto de extremidade físico) do servidor autorização. Este valor também é usado como uma chave de pesquisa para obter mais informações sobre o servidor de um ponto de extremidade de descoberta. <p><p> O cliente deve validar que o servidor de autorização é confiável. Quando o recurso estiver protegido por Azure AD, é suficiente para verificar se a URL começa com https://login.windows.net ou outro nome de host que ofereça suporte Azure AD. Um recurso de locatário específicos sempre deve retornar uma locatário específicas autorização URI. |
| Erro | Um valor de código de erro definido na seção 5.2 do [OAuth 2.0 autorização Framework](http://tools.ietf.org/html/rfc6749).|
| error_description | Uma descrição mais detalhada do erro. Essa mensagem não tem a intenção de ser amigável de usuário final.|
| id_do_recurso | Retorna o identificador exclusivo do recurso. O aplicativo cliente pode usar esse identificador como o valor da `resource` parâmetro quando ele solicita um símbolo para o recurso. <p><p> É muito importante para o aplicativo cliente verificar esse valor, caso contrário, um serviço mal-intencionado pode ser capaz de induzi um ataque de **elevação de privilégio** <p><p> A estratégia recomendada para prevenir um ataque é verificar se o `resource_id` corresponde a base de web API URL que está sendo acessado. Por exemplo, se https://service.contoso.com/data está sendo acessado, o `resource_id` pode ser htttps://service.contoso.com/. O aplicativo cliente deve rejeitar um `resource_id` que não começam com a URL base a menos que haja uma maneira alternativa confiável para verificar a identificação. |

#### <a name="bearer-scheme-error-codes"></a>Códigos de erro de esquema de portador

A especificação de RFC 6750 define os seguintes erros para recursos que usam usando o autenticar WWW cabeçalho e o esquema de portador na resposta.

| Código de Status HTTP | Código de erro | Descrição | Ação de cliente |
|------------------|------------|-------------|---------------|
| 400 | invalid_request | A solicitação não é válida. Por exemplo, ele pode ser falta um parâmetro ou usando o mesmo parâmetro duas vezes. | Corrigir o erro e repita a solicitação. Esse tipo de erro deve ocorrer somente durante o desenvolvimento e ser detectado no teste inicial. |
| 401 | invalid_token   | O token de acesso está ausente, inválida, ou foi revogado. O valor do parâmetro error_description fornece detalhes adicionais. |  Solicite um novo token do servidor autorização. Se o novo token falhar, ocorreu um erro inesperado. Envie uma mensagem de erro para o usuário e tente novamente após atrasos aleatórios. |
| 403 | insufficient_scope | O token de acesso não contém as permissões de representação necessárias para acessar o recurso. | Envie uma nova solicitação de autorização para o ponto de extremidade de autorização. Se a resposta contém o parâmetro de escopo, use o valor de escopo na solicitação ao recurso. |
| 403 | insufficient_access | O assunto do token não tem as permissões necessárias para acessar o recurso. | Solicita ao usuário para usar uma conta diferente ou para solicitar permissões para o recurso especificado. |

## <a name="refreshing-the-access-tokens"></a>Atualizando os tokens de acesso

Tokens de acesso são curta duração e devem ser atualizados depois que elas expirarem para continuar a acessar recursos. Você pode atualizar o `access_token` enviando outra `POST` solicitar a `/token` ponto de extremidade, mas esse tempo fornecendo a `refresh_token` , em vez da `code`.

Atualizar tokens não têm vida útil especificado. Geralmente, os tempos de vida de tokens de atualização são relativamente longos. No entanto, em alguns casos, tokens de atualização expirarem, são revogados ou tenham privilégios suficientes para a ação desejada. Seu aplicativo precisa esperar e tratar erros retornados pelo ponto de extremidade de emissão de token corretamente.

Quando você recebe uma resposta com um erro de token de atualização, descartar o símbolo de atualização atual e solicitar um novo código de autorização ou token de acesso. Em particular, quando usando uma atualização token no fluxo de concessão de código de autorização, se você receber uma resposta com a `interaction_required` ou `invalid_grant` códigos de erro, descartar o token de atualização e solicitar um novo código de autorização.

Uma amostra de solicitação para o ponto de extremidade do **locatário específicas** (você também pode usar o ponto de extremidade **comuns** ) para obter um novo acesso token usando um token de atualização tem esta aparência:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```
| Parâmetro | Descrição |
|-----------|-------------|
| access_token | O novo token de acesso que foi solicitado.|
| expires_in   | A duração restante do token em segundos. Um valor típico é 3600 (uma hora). |
| expires_on   | A data e a hora em que o token expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até o tempo de expiração. |
| refresh_token | Um novo refresh_token de OAuth 2.0 que pode ser usado para solicitar uma nova tokens de acesso quando aquele nesta resposta expira. |
| recurso     | Identifica o recurso protegido que o token de acesso pode ser usados para o access. |
| escopo        | Permissões de representação concedidas para o aplicativo cliente nativo. A permissão padrão é **user_impersonation**. O proprietário do recurso destino pode registrar valores alternativos no Azure AD. |
| token_type   | O tipo de token. O único valor com suporte é **portador**. |

### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta de token bem-sucedida será parecida com:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```

### <a name="error-response"></a>Resposta de erro

Uma resposta de erro de exemplo poderia ter esta aparência:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| Erro | Uma sequência de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| error_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa de um erro de autenticação.  |
| error_codes | Uma lista de códigos de erro específicos de STS que podem ajudar no diagnóstico. |
| carimbo de hora | A hora em que o erro ocorreu. |
| trace_id | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico.  |
| correlation_id | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico nos componentes.|

Para obter uma descrição de códigos de erro e a ação de cliente recomendado, consulte [códigos de erro para erros de token de ponto de extremidade](#error-codes-for-token-endpoint-errors).
