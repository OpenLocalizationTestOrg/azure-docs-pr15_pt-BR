<properties
    pageTitle="Visão geral do Azure AD .NET protocolo | Microsoft Azure"
    description="Este artigo descreve como usar mensagens HTTP para autorizar o acesso a aplicativos da web e web APIs no seu locatário usando o Active Directory do Azure e OpenID se conectar."
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


# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autorize acesso aos aplicativos web usando OpenID conectar e Active Directory do Azure

[Conectar-se OpenID](http://openid.net/specs/openid-connect-core-1_0.html) é uma camada de identidade simples construída sobre o protocolo OAuth 2.0. OAuth 2.0 define mecanismos para obter e usar **os tokens de acesso** para acessar recursos protegidos, mas eles não definem métodos padrão para fornecer informações de identidade. Conectar OpenID implementa autenticação como uma extensão para o processo de autorização OAuth 2.0, fornecendo informações sobre o usuário final na forma de um `id_token` que verifica a identidade do usuário, bem como fornece informações de perfil básico sobre o usuário.

Conectar-se OpenID é a nossa recomendação se você estiver criando um aplicativo web que está hospedado em um servidor e acessado por meio de um navegador.

## <a name="authentication-flow-using-openid-connect"></a>Fluxo de autenticação usando a conexão de OpenID

O fluxo de entrada mais básico contém as etapas a seguir, cada um deles é descrita detalhadamente abaixo.

![OpenId conectar o fluxo de autenticação](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)


## <a name="send-the-sign-in-request"></a>Enviar a solicitação de entrada

Quando seu aplicativo web precisa autenticar o usuário, ele deve direcionar o usuário para o `/authorize` ponto de extremidade. Essa solicitação é semelhante ao primeiro segmento do [Fluxo de código de autorização do OAuth 2.0](active-directory-protocols-oauth-code.md), com algumas diferenças importantes:

- A solicitação deve incluir o escopo `openid` no `scope` parâmetro.
- O `response_type` parâmetro deve incluir `id_token`.
- A solicitação deve incluir o `nonce` parâmetro.

Para que uma solicitação de exemplo teria esta aparência:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| locatário | Necessário | O `{tenant}` valor no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo.  Os valores permitidos são identificadores de locatário, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para tokens de locatário independente |
| client_id | Necessário | A Id de aplicativo atribuído ao seu aplicativo quando você registrou Azure AD. Você pode encontrar isso no Portal de clássico do Azure. Clique no **Active Directory**, clique no diretório, clique no aplicativo e, em seguida, clique em **Configurar** |
| response_type | Necessário | Deve incluir `id_token` para conectar OpenID entrar.  Ele também pode incluir outros response_types, tais como `code`. |
| escopo | Necessário | Uma lista separada de escopos.  Para se conectar OpenID, ele deve incluir o escopo `openid`, que converte para a permissão "Entrar" no consentimento da interface do usuário.  Você também pode incluir outros escopos nesta solicitação para solicitar consentimento. |
| valor de uso único | Necessário | Um valor incluído na solicitação, gerada pelo aplicativo, que será incluído no resultante `id_token` como uma declaração.  O aplicativo pode verificar esse valor para atenuar ataques de repetição de token.  O valor é geralmente uma cadeia de caracteres de aleatório, exclusiva ou GUID que pode ser usado para identificar a origem da solicitação.  |
| redirect_uri | recomendados | O redirect_uri do aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas por seu aplicativo.  Ele deve corresponder exatamente um da redirect_uris que você registrado no portal do, exceto deve ser codificada de url. |
| response_mode | recomendados | Especifica o método que deve ser usado para enviar a authorization_code resultante volta para seu aplicativo.  Valores suportados são `form_post` do *formulário HTTP postar* ou `fragment` fragmento de *URL*.  Para aplicativos web, é recomendável usar `response_mode=form_post` para garantir a transferência mais segura de tokens para seu aplicativo.  
| estado | recomendados | Um valor incluído na solicitação que também será retornada na resposta token.  Pode ser uma cadeia de caracteres de qualquer conteúdo que desejar.  Um valor exclusivo gerado aleatoriamente normalmente é usado para [prevenir ataques de falsificação de solicitação intersite](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes de que ocorreu a solicitação de autenticação, como a página ou o modo de exibição estivessem em. |
| prompt | opcional | Indica o tipo de interação do usuário é necessária.  Os valores válidos somente neste momento são 'login', 'Nenhum' e 'consentimento'.  `prompt=login`forçar o usuário inserir suas credenciais nessa solicitação, eliminando single sign-no.  `prompt=none`é o oposto - garantirá que o usuário não será apresentado qualquer prompt interativo qualquer.  Se a solicitação não pode ser concluída silenciosamente por meio do logon único no, o ponto de extremidade retornará um erro.  `prompt=consent`acionará a caixa de diálogo de consentimento OAuth depois que o usuário entra no, solicitando que o usuário para conceder permissões para o aplicativo. |
| login_hint | opcional | Pode ser usado para preencher previamente o campo de endereço de email/nome de usuário da página de entrada do usuário, se você souber o nome de usuário antecedência.  Muitas vezes aplicativos usará esse parâmetro durante nova autenticação, já ter extraído o nome de usuário de uma entrada usando anterior a `preferred_username` reivindicar. |


Neste ponto, o usuário será solicitado a digitar suas credenciais e concluir a autenticação.

### <a name="sample-response"></a>Resposta de exemplo

Uma resposta de amostra, depois que o usuário autenticado, pode ter esta aparência:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| id_token | O `id_token` que o aplicativo solicitado. Você pode usar o `id_token` para verificar a identidade do usuário e iniciar uma sessão com o usuário.  |
| estado | Um valor incluído na solicitação que também será retornada na resposta token. Um valor exclusivo gerado aleatoriamente normalmente é usado para [prevenir ataques de falsificação de solicitação intersite](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes de que ocorreu a solicitação de autenticação, como a página ou o modo de exibição estivessem em. |

### <a name="error-response"></a>Resposta de erro
Respostas de erro também podem ser enviadas para o `redirect_uri` para que o aplicativo pode manipulá-los adequadamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| Erro | Uma sequência de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| error_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa de um erro de autenticação.  |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto de extremidade de autorização

A tabela a seguir descreve os diversos códigos de erro que podem ser retornados na `error` parâmetro da resposta erro.

| Código de erro | Descrição | Ação de cliente |
|------------|-------------|---------------|
| invalid_request | Erro de protocolo, como um parâmetro necessário ausente. | Corrija e reenviar a solicitação. Este é um desenvolvimento erro geralmente é capturado durante o teste inicial.|
| unauthorized_client | O aplicativo cliente não é permitido para solicitar um código de autorização. | Isso geralmente ocorre quando o aplicativo cliente não estiver registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar o usuário com instruções para instalar o aplicativo e adicioná-lo ao Azure AD. |
| ACCESS_DENIED | Proprietário do recurso negado consentimento | O aplicativo cliente pode notificar o usuário que ele não pode continuar a menos que o usuário. |
| unsupported_response_type | O servidor de autorização não suporta o tipo de resposta na solicitação. | Corrija e reenviar a solicitação. Este é um desenvolvimento erro geralmente é detectado durante o teste inicial.|
|server_error | O servidor encontrou um erro inesperado. | Repita a solicitação. Esses erros podem resultar de condições temporárias. O aplicativo cliente pode explicar para o usuário que sua resposta estiver atrasada há um erro temporário de conclusão. |
| temporarily_unavailable | O servidor está temporariamente ocupado para lidar com a solicitação. | Repita a solicitação. O aplicativo cliente pode explicar para o usuário que sua resposta é atrasada devido uma condição temporária. |
| invalid_resource |O recurso de destino é inválido porque ela não existir, Azure AD não consegue encontrar ou não está configurado corretamente.| Isso indica que o recurso, se ele existir, não foi configurado no locatário. O aplicativo pode solicitar o usuário com instruções para instalar o aplicativo e adicioná-lo ao Azure AD. |

## <a name="validate-the-idtoken"></a>Validar o id_token

Basta recebendo um `id_token` não é suficiente para autenticar o usuário; Você deve validar a assinatura e verifique se as declarações no `id_token` por requisitos do seu aplicativo. O ponto de extremidade do Azure AD usa JSON Web Tokens (JWTs) e criptografia de chave pública para assinar tokens e verificar se eles são válidos.

Você pode escolher validar a `id_token` no cliente de código, mas uma prática comum é enviar a `id_token` em um servidor back-end e realize a validação lá. Depois que você já validados a assinatura da `id_token`, existem algumas declarações, você será solicitado para confirmar.

Talvez você também queira validar declarações adicionais dependendo do cenário. Algumas validações comuns incluem:

- Garantindo que a organização do usuário/inscreveu para o aplicativo.
- Garantindo que o usuário tem autorização/privilégios apropriados
- Garantindo que um determinado nível de autenticação ocorreu, como autenticação multifator.

Depois que você tiver validados completamente o `id_token`, você pode iniciar uma sessão com o usuário e usar as declarações no `id_token` para obter informações sobre o usuário em seu aplicativo. Essas informações podem ser usadas para exibição, registros, autorizações, etc. Para obter mais informações sobre os tipos de token e declarações, leia [Token de suporte e tipos de declaração](active-directory-token-and-claims.md).

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de sair

Quando você deseja assinar o usuário sair do aplicativo, não é suficiente limpar seu aplicativo cookies ou final caso contrário, a sessão com o usuário.  Você também deve redirecionar o usuário para o `end_session_endpoint` para sair.  Se você não conseguir fazer isso, o usuário será capaz de autenticar se novamente seu aplicativo sem inserir suas credenciais novamente, pois eles terão uma única logon sessão válida com o ponto de extremidade do Azure AD.

Você pode simplesmente redirecionar o usuário para o `end_session_endpoint` listados no documento metadados OpenID conectar:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recomendados | A URL que o usuário deve ser redirecionado para após logout bem-sucedido.  Se não incluído, o usuário será mostrado uma mensagem genérica.  |

## <a name="token-acquisition"></a>Aquisição de token

Vários aplicativos web precisam não apenas assinar o usuário na, mas também acessar um serviço da web em nome de usuário usando OAuth. Este cenário combina OpenID conectar para autenticação do usuário enquanto simultaneamente ao adquirir um `authorization_code` que podem ser usados para obter `access_tokens` usando o fluxo de código de autorização OAuth.

## <a name="get-access-tokens"></a>Obter Tokens de acesso

Para adquirir tokens de acesso, você precisará modificar a solicitação de entrada do acima:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e      // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // form_post', or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F                                   
&state=12345                                         // Any value, provided by your app
&nonce=678910                                        // Any value, provided by your app
```

Incluindo escopos de permissão na solicitação e usando `response_type=code+id_token`, o `authorize` ponto de extremidade garantirá que o usuário aceitou as permissões indicadas na `scope` parâmetro de consulta e retornar um código de autorização para trocar para um token de acesso de seu aplicativo.

### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida usando `response_mode=form_post` aparência semelhante a:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| id_token | O `id_token` que o aplicativo solicitado. Você pode usar o `id_token` para verificar a identidade do usuário e iniciar uma sessão com o usuário. |
| código | O authorization_code que o aplicativo solicitado. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. Authorization_codes são vida muito curta, normalmente expirarem após cerca de 10 minutos. |
| estado | Se um parâmetro de estado for incluído na solicitação, o mesmo valor deve aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

Respostas de erro também podem ser enviadas para o `redirect_uri` para que o aplicativo pode manipulá-los adequadamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| Erro | Uma sequência de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| error_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa de um erro de autenticação.  |

Para obter uma descrição de possíveis códigos de erro e suas ações de cliente recomendado, consulte [códigos de erro para erros de ponto de extremidade de autorização](#error-codes-for-authorization-endpoint-errors).

Depois que uma autorização `code` e um `id_token`, você pode entrar o usuário e obter tokens de acesso em seu nome.  Para assinar o usuário no, você deve validar a `id_token` exatamente conforme descrito acima. Para obter tokens de acesso, você pode seguir as etapas descritas em nossa [documentação de protocolo OAuth](active-directory-protocols-oauth-code.md#Use-the-Authorization-Code-to-Request-an-Access-Token).
