<properties
    pageTitle="Active Directory do Azure B2C | Microsoft Azure"
    description="Criar aplicativos web usando a implementação do Active Directory do Azure do protocolo de autenticação OpenID se conectar."
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure B2C diretório ativo: Fluxo de código de autorização de OAuth 2.0

A concessão de código de autorização OAuth 2.0 pode ser usada nos aplicativos que são instalados em um dispositivo para obter acesso aos recursos protegidos, como web APIs. Usando a implementação do Azure Active Directory (AD Azure) B2C do OAuth 2.0, você pode adicionar tarefas de gerenciamento de identidade de inscrição, entrar e outros seus aplicativos de desktop e móveis. Este guia é independente de linguagem. Ele descreve como enviar e receber mensagens HTTP sem usar qualquer um dos nossas bibliotecas de código-fonte aberto.

<!-- TODO: Need link to libraries -->

O fluxo de código de autorização OAuth 2.0 é descrito na [seção 4.1 da especificação OAuth 2.0](http://tools.ietf.org/html/rfc6749). Você pode usá-lo para executar a autenticação e a autorização na maioria dos tipos de aplicativo, incluindo [web apps](active-directory-b2c-apps.md#web-apps) e [instalado nativamente aplicativos](active-directory-b2c-apps.md#mobile-and-native-apps). Ele permite que aplicativos com segurança adquirir **access_tokens**, que podem ser usadas para acessar recursos que são protegidos por um [servidor de autorização](active-directory-b2c-reference-protocols.md#the-basics).

Este guia se concentrará em um determinado tipo de OAuth 2.0 autorização código fluxo –**clientes públicos**. Um cliente público é qualquer aplicativo cliente não é confiável para com segurança manter a integridade de uma senha secreta. Isso inclui aplicativos móveis, aplicativos de desktop e praticamente qualquer aplicativo que é executado em um dispositivo e precisa obter access_tokens. Se você quiser adicionar o gerenciamento de identidades a um aplicativo web usando Azure AD B2C, você deve usar [OpenID conectar](active-directory-b2c-reference-oidc.md) em vez de OAuth 2.0.

Azure AD B2C estende o padrão que OAuth 2.0 fluirá para fazer mais do que simples autenticação e autorização. Ele apresenta o [**parâmetro de política**](active-directory-b2c-reference-policies.md), que permite que você use OAuth 2.0 para adicionar experiências de usuário ao seu aplicativo, tais como inscrição, entrar e gerenciamento de perfil. Aqui mostraremos como usar políticas e OAuth 2.0 para implementar cada um dessas experiências em seus aplicativos nativos. Também mostraremos como obter access_tokens para acessar web APIs.

As solicitações HTTP de exemplo abaixo usará nosso diretório de B2C de amostra, **fabrikamb2c.onmicrosoft.com**, bem como nosso aplicativo de exemplo e políticas. Você está vontade para testar as solicitações de check-out por conta própria usando esses valores, ou você pode substituí-los com seu próprio.
Saiba como [obter o seu próprio diretório, aplicativo e políticas B2C](#use-your-own-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. obter um código de autorização
O fluxo de código de autorização começa com o cliente direciona o usuário para o `/authorize` ponto de extremidade. Este é a parte interativa de fluxo, onde o usuário será realmente agir. Nesta solicitação, o cliente indica as permissões que ele precisa adquirir do usuário na `scope` parâmetro e a política de executar na `p` parâmetro. Três exemplos são apresentados abaixo (com quebras de linha para fins de legibilidade), cada um usando uma regra diferente.

#### <a name="use-a-sign-in-policy"></a>Usar uma política de entrada

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Usar uma política de inscrição

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Usar uma política de Editar perfil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parâmetro | Necessário? | Descrição |
| ----------------------- | ------------------------------- | ----------------------- |
| client_id | Necessário | A ID de aplicativo do [portal do Azure](https://portal.azure.com) atribuída ao seu aplicativo. |
| response_type | Necessário | O tipo de resposta, que deve incluir `code` para o fluxo de código de autorização. |
| redirect_uri | Necessário | O redirect_uri do aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas por seu aplicativo. Ele deve corresponder exatamente à dentre as redirect_uris que você registrou no portal do, exceto que ele deve ser codificada de URL. |
| escopo | Necessário | Uma lista separada de escopos. Um valor único escopo indica ao Azure AD ambas das permissões que está sendo solicitado. Usando o ID de cliente como o escopo indica que o seu aplicativo precisa de um **token de acesso** que podem ser usados em relação a seu próprio serviço ou web API, representado pela mesma ID do cliente.  O `offline_access` escopo indica que o seu aplicativo precisará um **refresh_token** para vida longa acesso aos recursos.  Você também pode usar o `openid` escopo para solicitar um **id_token** do Azure AD B2C. |
| response_mode | Recomendados | O método que deve ser usado para enviar a authorization_code resultante volta para seu aplicativo. Pode ser um dos 'consulta', 'form_post' ou 'fragmento'.
| estado | Recomendados | Um valor incluído na solicitação que também será retornada na resposta token. Pode ser uma cadeia de caracteres de qualquer conteúdo que desejar. Um valor exclusivo gerado aleatoriamente normalmente é usado para prevenir ataques de falsificação de solicitação intersite. O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes de que ocorreu a solicitação de autenticação, como a página estivessem em ou a política que está sendo executado. |
| p | Necessário | A política que será executada. É o nome de uma política que é criada no diretório B2C. O valor de nome de política deve começar com "b2c\_1\_". Saiba mais sobre políticas na [estrutura de políticas extensível](active-directory-b2c-reference-policies.md). |
| prompt | Opcional | O tipo de interação do usuário é necessária. O único valor válido neste momento é 'login', que força o usuário inserir suas credenciais na solicitação. Logon único não terá efeito. |

Neste ponto, o usuário será solicitado a concluir o fluxo de trabalho da política. Isso pode envolver o usuário inserindo seu nome de usuário e senha, entrando com uma identidade social, se inscrevendo no diretório ou qualquer outro número de etapas, dependendo de como a política é definida.

Depois que o usuário conclui a política, Azure AD retornará uma resposta para o seu aplicativo no indicado `redirect_uri`, usando o método especificado na `response_mode` parâmetro. A resposta será exatamente o mesmo para cada um dos casos acima, independentemente da política que foi executada.

Uma resposta bem-sucedida que usa `response_mode=query` aparência semelhante a:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| código | O authorization_code que o aplicativo solicitado. O aplicativo pode usar o código de autorização para solicitar um access_token para um recurso de destino. Authorization_codes são vida muito curta. Normalmente, elas expiram após cerca de 10 minutos. |
| estado | Consulte a descrição completa na tabela anterior. Se um parâmetro de estado for incluído na solicitação, o mesmo valor deve aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e resposta são idênticos. |

Respostas de erro também podem ser enviadas para o `redirect_uri` para que o aplicativo pode manipulá-los adequadamente:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| Erro | Uma sequência de código de erro que pode ser usada para classificar os tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| error_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor para identificar a causa de um erro de autenticação. |
| estado | Consulte a descrição completa na primeira tabela nesta seção. Se um parâmetro de estado for incluído na solicitação, o mesmo valor deve aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e resposta são idênticos. |


## <a name="2-get-a-token"></a>2. Obtenha um token
Agora que você já tiver adquirido uma authorization_code, você pode recuperar o `code` para obter um símbolo para o recurso desejado enviando um `POST` solicitar a `/token` ponto de extremidade. No Azure AD B2C, o único recurso que você pode solicitar um símbolo para é sua web de back-end do aplicativo API. A convenção que é usada para solicitar um token para si mesmo é usar o código do cliente do seu aplicativo como o escopo:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parâmetro | Necessário? | Descrição |
| ----------------------- | ------------------------------- | --------------------- |
| p | Necessário | A política que foi usada para adquirir o código de autorização. Você não pode usar uma política diferente nesta solicitação. Observe que você adicionar esse parâmetro para a *cadeia de caracteres de consulta*, não no corpo da POSTAGEM. |
| client_id | Necessário | A ID de aplicativo do [portal do Azure](https://portal.azure.com) atribuída ao seu aplicativo. |
| grant_type | Necessário | O tipo de concessão, que deve ser `authorization_code` para o fluxo de código de autorização. |
| escopo | Recomendados | Uma lista separada de escopos. Um valor único escopo indica ao Azure AD ambas das permissões que está sendo solicitado. Usando o ID de cliente como o escopo indica que o seu aplicativo precisa de um **token de acesso** que podem ser usados em relação a seu próprio serviço ou web API, representado pela mesma ID do cliente.  O `offline_access` escopo indica que o seu aplicativo precisará um **refresh_token** para vida longa acesso aos recursos.  Você também pode usar o `openid` escopo para solicitar um **id_token** do Azure AD B2C. |
| código | Necessário | O authorization_code que você adquiriu no primeiro trecho do fluxo. |
| redirect_uri | Necessário | O redirect_uri do aplicativo em que você recebeu o authorization_code. |

Uma resposta de token bem-sucedida será parecida com:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| not_before | A hora em que o token é considerado válido, em vez de época. |
| token_type | O valor de tipo de token. O único tipo que ofereça suporte Azure AD é portador. |
| access_token | O token de JSON Web Token (JWT) assinado solicitada. |
| escopo | Os escopos que o token é válido, que podem ser usados para armazenamento em cache tokens para uso posterior. |
| expires_in | O período de tempo que o token é válido (em segundos). |
| refresh_token | Um refresh_token OAuth 2.0. O aplicativo pode usar esse token adquirir tokens adicionais após o atual expire. Refresh_tokens são vivia longo e pode ser usado para manter o acesso aos recursos por longos períodos de tempo. Para obter mais detalhes, consulte a [referência de token B2C](active-directory-b2c-reference-tokens.md). |

Respostas de erro serão parecida com:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| Erro | Uma sequência de código de erro que pode ser usada para classificar os tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| error_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor para identificar a causa de um erro de autenticação. |

## <a name="3-use-the-token"></a>3. use o token
Agora que você já adquiriu com êxito um `access_token`, você pode usar o token em solicitações para seu back-end web APIs incluindo-o na `Authorization` cabeçalho:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Atualize o token
Tokens de acesso & identificação são vida curta. Atualize-los depois que elas expirarem para continuar a ser capaz de acessar os recursos. Você pode fazer isso enviando outra `POST` solicitar a `/token` ponto de extremidade. Desta vez fornecer a `refresh_token` , em vez da `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parâmetro | Necessário? | Descrição |
| ----------------------- | ------------------------------- | -------- |
| p | Necessário | A política que foi usada para adquirir a refresh_token original. Você não pode usar uma política diferente nesta solicitação. Observe que você adicionar esse parâmetro para a *cadeia de caracteres de consulta*, não no corpo da POSTAGEM. |
| client_id | Recomendados | A ID de aplicativo do [portal do Azure](https://portal.azure.com) atribuída ao seu aplicativo. |
| grant_type | Necessário | O tipo de concessão, que deve ser `refresh_token` para esse trecho do fluxo de código de autorização. |
| escopo | Recomendados | Uma lista separada de escopos. Um valor único escopo indica ao Azure AD ambas das permissões que está sendo solicitado. Usando o ID de cliente como o escopo indica que o seu aplicativo precisa de um **token de acesso** que podem ser usados em relação a seu próprio serviço ou web API, representado pela mesma ID do cliente.  O `offline_access` escopo indica que o seu aplicativo precisará um **refresh_token** para vida longa acesso aos recursos.  Você também pode usar o `openid` escopo para solicitar um **id_token** do Azure AD B2C. |
| redirect_uri | Opcional | O redirect_uri do aplicativo em que você recebeu o authorization_code. |
| refresh_token | Necessário | O refresh_token original que você adquiriu no segundo trecho do fluxo. |

Uma resposta de token bem-sucedida será parecida com:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| not_before | A hora em que o token é considerado válido, em vez de época. |
| token_type | O valor de tipo de token. O único tipo que ofereça suporte Azure AD é portador. |
| access_token | O token de JSON Web Token (JWT) assinado solicitada. |
| escopo | Os escopos que o token é válido, que podem ser usados para armazenamento em cache tokens para uso posterior. |
| expires_in | O período de tempo que o token é válido (em segundos). |
| refresh_token | Um refresh_token OAuth 2.0. O aplicativo pode usar esse token adquirir tokens adicionais após o atual expire. Refresh_tokens são vivia longo e pode ser usado para manter o acesso aos recursos por longos períodos de tempo. Para obter mais detalhes, consulte a [referência de token B2C](active-directory-b2c-reference-tokens.md). |

Respostas de erro serão parecida com:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| Erro | Uma sequência de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| error_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa de um erro de autenticação. |

## <a name="use-your-own-b2c-directory"></a>Usar seu próprio diretório B2C

Se você quiser tentar essas solicitações para si mesmo, você deve primeiro realizar estas três etapas e, em seguida, substitua os valores de exemplo acima seu próprio:

- [Criar um diretório de B2C](active-directory-b2c-get-started.md) e use o nome do seu diretório nas solicitações.
- [Criar um aplicativo](active-directory-b2c-app-registration.md) para obter uma ID de aplicativo e um redirect_uri. Você desejará incluem um **cliente nativo** em seu aplicativo.
- [Criar suas regras](active-directory-b2c-reference-policies.md) para obter seus nomes de política.
