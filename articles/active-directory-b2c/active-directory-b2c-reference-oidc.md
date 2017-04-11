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

# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure B2C diretório ativo: Web entrar com OpenID conectar

Conectar OpenID é um protocolo de autenticação, criado na parte superior OAuth 2.0, que pode ser usado para entrar com segurança usuários em aplicativos da web.  Usando a implementação do Azure Active Directory (AD Azure) B2C de OpenID conectar, você pode terceirização de inscrição, entrar, e outro gerenciamento de identidades experiências em seus aplicativos web ao Azure AD. Este guia mostrará como fazer isso de maneira independente de linguagem. Ele descreverá como enviar e receber mensagens HTTP sem usar qualquer um dos nossas bibliotecas de código-fonte aberto.

[Conectar OpenID](http://openid.net/specs/openid-connect-core-1_0.html) estende o protocolo de *autorização* OAuth 2.0 para uso como um protocolo de *autenticação* . Isso permite executar logon único usando OAuth. Ele apresenta o conceito de um `id_token`, que é um token de segurança que permite que o cliente verificar a identidade do usuário e obter informações de perfil básico sobre o usuário.

Pois ele estende OAuth 2.0, ele também permite que aplicativos com segurança adquirir **access_tokens**. Você pode usar access_tokens para acessar os recursos que são protegidos por um [servidor de autorização](active-directory-b2c-reference-protocols.md#the-basics). É recomendável OpenID conectar se você estiver criando um aplicativo web que está hospedado em um servidor e acessado através de um navegador. Se você quiser adicionar gerenciamento de identidades para os aplicativos móveis ou da área de trabalho usando o Azure AD B2C, você deve usar [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) em vez de OpenID conectar.

Azure AD B2C estende o protocolo OpenID conectar padrão para fazer mais do que simples autenticação e autorização. Ele apresenta o [**parâmetro de política**](active-directory-b2c-reference-policies.md), que permite que você use OpenID conectar adicionar experiências do usuário para o seu aplicativo – como inscrição, entrar e gerenciamento de perfil. Aqui mostraremos como usar OpenID conectar e políticas para implementar cada um dessas experiências em seus aplicativos web. Também mostraremos como obter access_tokens para acessar web APIs.

As solicitações HTTP de exemplo abaixo usará nosso diretório de B2C de amostra, **fabrikamb2c.onmicrosoft.com**, bem como nosso exemplo aplicativo **https://aadb2cplayground.azurewebsites.net** e políticas. Você está livre para experimentar as solicitações por conta própria usando esses valores, ou você pode substituí-los com seu próprio.
Saiba como [obter o seu próprio aplicativo, políticas e B2C locatário](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Enviar solicitações de autenticação
Quando seu aplicativo web precisa autenticar o usuário e execute uma política, ele pode direcionar o usuário para o `/authorize` ponto de extremidade. Este é a parte interativa de fluxo, onde o usuário será realmente agir, dependendo da política.

Nesta solicitação, o cliente indica as permissões que ele precisa adquirir do usuário na `scope` parâmetro e a política de executar na `p` parâmetro. Três exemplos são apresentados abaixo (com quebras de linha para fins de legibilidade), cada um usando uma regra diferente. Para obter uma aparência para funcionamento de cada solicitação, tente colando a solicitação em um navegador e executá-lo.

#### <a name="use-a-sign-in-policy"></a>Usar uma política de entrada

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Usar uma política de inscrição

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Usar uma política de Editar perfil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parâmetro | Necessário? | Descrição |
| ----------------------- | ------------------------------- | ----------------------- |
| client_id | Necessário | A ID de aplicativo do [portal do Azure](https://portal.azure.com/) atribuída ao seu aplicativo. |
| response_type | Necessário | O tipo de resposta, que deve incluir `id_token` para OpenID Connect. Se o seu aplicativo web também precisará tokens para chamar um web API, você pode usar `code+id_token`, como fizemos aqui. |
| redirect_uri | Recomendados | O redirect_uri do aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas por seu aplicativo. Ele deve corresponder exatamente à dentre as redirect_uris que você registrou no portal do, exceto que ele deve ser codificada de URL. |
| escopo | Necessário | Uma lista separada de escopos. Um valor único escopo indica ao Azure AD ambas das permissões que está sendo solicitado. O `openid` escopo indica uma permissão entrar o usuário e obter dados sobre o usuário na forma de **id_tokens** (mais sobre isso posteriormente neste artigo). O `offline_access` escopo é opcional para aplicativos web. Indica que o seu aplicativo precisará um **refresh_token** para vida longa acesso aos recursos. |
| response_mode | Recomendados | O método que deve ser usado para enviar a authorization_code resultante volta para seu aplicativo. Pode ser um dos 'consulta', 'form_post' ou 'fragmento'.  'form_post' é recomendado para melhor segurança. |
| estado | Recomendados | Um valor incluído na solicitação que também será retornada na resposta token. Pode ser uma cadeia de caracteres de qualquer conteúdo que desejar. Um valor exclusivo gerado aleatoriamente normalmente é usado para prevenir ataques de falsificação de solicitação intersite. O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes de que ocorreu a solicitação de autenticação, como a página estivessem em. |
| valor de uso único | Necessário | Um valor incluído na solicitação (gerada pelo aplicativo) que será incluída na id_token resultante como uma declaração. O aplicativo pode verificar esse valor para atenuar ataques de repetição de token. Normalmente, o valor é uma cadeia de caracteres aleatório exclusiva que pode ser usada para identificar a origem da solicitação. |
| p | Necessário | A política que será executada. É o nome de uma política que é criada no seu locatário B2C. O valor de nome de política deve começar com "b2c\_1\_". Saiba mais sobre políticas na [estrutura de políticas extensível](active-directory-b2c-reference-policies.md). |
| prompt | Opcional | O tipo de interação do usuário é necessária. O único valor válido neste momento é 'login', que força o usuário inserir suas credenciais na solicitação. Logon único não terá efeito. |

Neste ponto, o usuário será solicitado a concluir o fluxo de trabalho da política. Isso pode envolver o usuário inserindo seu nome de usuário e senha, entrando com uma identidade social, se inscrevendo no diretório ou qualquer outro número de etapas, dependendo de como a política é definida.

Depois que o usuário conclui a política, Azure AD retornará uma resposta para o seu aplicativo no indicado `redirect_uri`, usando o método especificado no `response_mode` parâmetro. A resposta será exatamente o mesmo para cada um dos casos acima, independentemente da política que foi executada.

Uma resposta bem-sucedida usando `response_mode=fragment` teria o seguinte:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| id_token | O id_token que o aplicativo solicitado. Você pode usar o id_token para verificar a identidade do usuário e iniciar uma sessão com o usuário. Mais detalhes sobre id_tokens e seu conteúdo são incluídos na [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| código | O authorization_code que o aplicativo solicitado, se você usou o `response_type=code+id_token`. O aplicativo pode usar o código de autorização para solicitar um access_token para um recurso de destino. Authorization_codes são vida muito curta. Normalmente, elas expiram após cerca de 10 minutos. |
| estado | Se um parâmetro de estado for incluído na solicitação, o mesmo valor deve aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e resposta são idênticos. |

Respostas de erro também podem ser enviadas para o `redirect_uri` para que o aplicativo pode manipulá-los adequadamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| Erro | Uma sequência de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| error_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa de um erro de autenticação. |
| estado | Consulte a descrição completa na tabela anterior. Se um parâmetro de estado for incluído na solicitação, o mesmo valor deve aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e resposta são idênticos. |


## <a name="validate-the-idtoken"></a>Validar o id_token
Apenas receber um id_token não é suficiente para autenticar o usuário, você deve validar assinatura do id_token e verifique se as declarações no token de acordo com as necessidades do seu aplicativo. Azure AD B2C usa [JSON Web Tokens (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos.

Há muitas bibliotecas de código-fonte aberto que estão disponíveis para validar JWTs, dependendo do seu idioma preferencial. É recomendável explorando essas opções ao invés de implementar sua própria lógica de validação. Veja as informações serão úteis para descobrir como usar essas bibliotecas corretamente.

Azure AD B2C tem um ponto extremo se conectar OpenID metadados, que permite que um aplicativo buscar informações sobre Azure AD B2C em tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e token de chaves de assinatura. Não há um documento de metadados JSON para cada diretiva no seu locatário B2C. Por exemplo, o documento de metadados para o `b2c_1_sign_in` política no `fabrikamb2c.onmicrosoft.com` está localizado em:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Uma das propriedades do documento configuração é o `jwks_uri`, cujo valor para a mesma diretiva seria:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Em ordem para determinar qual diretiva era usada em um id_token de assinatura (e onde buscar os metadados do), você tem duas opções. Primeiro, o nome da política está incluído na `acr` reivindicar na id_token. Para obter informações sobre como analisar as declarações de um id_token, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). A outra opção é codificar a política no valor da `state` parâmetro quando emitiu a solicitação e, em seguida, decodificar para determinar qual diretiva foi usada. Qualquer um dos métodos é perfeitamente válido.

Depois que você tiver adquirido o documento de metadados do ponto de extremidade OpenID conectar metadados, você pode usar as chaves públicas do RSA 256 (que estão localizadas neste ponto de extremidade) para validar a assinatura da id_token. Pode haver várias teclas listadas neste ponto de extremidade em qualquer ponto no tempo, cada uma identificada por uma `kid`. O cabeçalho do id_token também contém um `kid` reivindicar, que indica que essas chaves foi usado para assinar o id_token. Consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md) para obter mais informações, incluindo seções sobre [Validando tokens](active-directory-b2c-reference-tokens.md#validating-tokens) e [informações importantes sobre a assinatura de chave sobreposição](active-directory-b2c-reference-tokens.md#validating-tokens).
<!--TODO: Improve the information on this-->

Depois que você já validados a assinatura do id_token, há várias declarações que você precisa verificar, por exemplo:

- Você deve validar a `nonce` reivindicar impedir ataques de repetição de token. Seu valor deve ser o que você especificou na solicitação de entrada.
- Você deve validar a `aud` reivindicar garantir que o id_token foi emitido para seu aplicativo. Seu valor deve ser o ID de aplicativo do aplicativo.
- Você deve validar a `iat` e `exp` declarações para garantir que o id_token não expirou.

Também existem vários validações mais que você deve executar, descritas em detalhes a [Especificação de núcleo de conectar OpenID](http://openid.net/specs/openid-connect-core-1_0.html).  Você também pode querer validar declarações adicionais, dependendo do cenário. Algumas validações comuns incluem:

- Garantir que a organização do usuário/inscreveu para o aplicativo.
- Garantir que o usuário tenha autorização/privilégios apropriados.
- Garantir que tenha ocorrido um determinado nível de autenticação, como autenticação multifator do Azure.

Para obter mais informações sobre as declarações em um id_token, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md).

Após completamente validar o id_token, você pode iniciar uma sessão com o usuário e use as declarações a id_token para obter informações sobre o usuário em seu aplicativo. Essas informações podem ser usadas para exibição, registros, autorização e assim por diante.

## <a name="get-a-token"></a>Obter um símbolo
Se tudo o que o seu aplicativo web precisa fazer é executar políticas, você pode ignorar as próximas seções. Estas seções só são aplicáveis para aplicativos que precisam fazer autenticado chamadas para um web API e também são protegidos por Azure AD B2C da web.

Você pode recuperar o authorization_code que você adquiriu (usando `response_type=code+id_token`) para obter um símbolo para o recurso desejado enviando um `POST` solicitar a `/token` ponto de extremidade. Atualmente, o único recurso que você pode solicitar um símbolo para é sua web de back-end do aplicativo API. A convenção para solicitar um token para si mesmo é usar o código do cliente do seu aplicativo como o escopo:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Parâmetro | Necessário? | Descrição |
| ----------------------- | ------------------------------- | --------------------- |
| p | Necessário | A política que foi usada para adquirir o código de autorização. Você não pode usar uma política diferente nesta solicitação. Observe que você adicionar esse parâmetro para a **cadeia de caracteres de consulta**, não no corpo da POSTAGEM. |
| client_id | Necessário | A ID de aplicativo do [portal do Azure](https://portal.azure.com/) atribuída ao seu aplicativo. |
| grant_type | Necessário | O tipo de concessão, que deve ser `authorization_code` para o fluxo de código de autorização. |
| escopo | Recomendados | Uma lista separada de escopos. Um valor único escopo indica ao Azure AD ambas das permissões que está sendo solicitado. O `openid` escopo indica uma permissão entrar o usuário e obter dados sobre o usuário na forma de **id_tokens**. Ele pode ser usado para obter tokens à sua web de back-end do aplicativo API, que é representado pela mesma ID de aplicativo como o cliente. O `offline_access` escopo indica que o seu aplicativo precisará um **refresh_token** para vida longa acesso aos recursos. |
| código | Necessário | O authorization_code que você adquiriu no primeiro trecho do fluxo. |
| redirect_uri | Necessário | O redirect_uri do aplicativo em que você recebeu o authorization_code. |
| client_secret | Necessário | O segredo de aplicativo que você gerou no [portal do Azure](https://portal.azure.com/). Esse segredo de aplicativo é um produto de segurança importantes. Você deve armazenar com segurança no seu servidor. Você também deve ter cuidado para girar esse segredo de cliente periodicamente. |

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
| access_token | O token de JWT assinado solicitada. |
| escopo | Os escopos que o token é válido, que podem ser usados para armazenamento em cache tokens para uso posterior. |
| expires_in | O período de tempo que o access_token é válido (em segundos). |
| refresh_token | Um refresh_token OAuth 2.0. O aplicativo pode usar esse token adquirir tokens adicionais após o atual expire.  Refresh_tokens são vivia longo e pode ser usado para manter o acesso aos recursos por longos períodos de tempo. Para obter mais detalhes, consulte a [referência de token B2C](active-directory-b2c-reference-tokens.md). Observe que você deve ter usado o escopo `offline_access` em autorização e solicitações de token para receberem uma refresh_token. |

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

## <a name="use-the-token"></a>Use o símbolo
Agora que você já adquiriu com êxito um `access_token`, você pode usar o token em solicitações para seu back-end web APIs incluindo-o na `Authorization` cabeçalho:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Atualizar o token
Os id_tokens são vida curta. Atualize-los depois que elas expirarem para continuar a ser capaz de acessar os recursos. Você pode fazer isso enviando outra `POST` solicitar a `/token` ponto de extremidade. Desta vez, forneça o `refresh_token` , em vez da `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parâmetro | Necessário | Descrição |
| ----------------------- | ------------------------------- | -------- |
| p | Necessário | A política que foi usada para adquirir a refresh_token original. Você não pode usar uma política diferente nesta solicitação. Observe que você adicionar esse parâmetro para a **cadeia de caracteres de consulta**, não no corpo da POSTAGEM. |
| client_id | Necessário | A ID de aplicativo do [portal do Azure](https://portal.azure.com/) atribuída ao seu aplicativo. |
| grant_type | Necessário | O tipo de concessão, que deve ser `refresh_token` para esse trecho do fluxo de código de autorização. |
| escopo | Recomendados | Uma lista separada de escopos. Um valor único escopo indica ao Azure AD ambas das permissões que está sendo solicitado. O `openid` escopo indica uma permissão entrar o usuário e obter dados sobre o usuário na forma de **id_tokens**. Ele pode ser usado para obter tokens à sua web de back-end do aplicativo API, que é representado pela mesma ID de aplicativo como o cliente. O `offline_access` escopo indica que o seu aplicativo precisará um **refresh_token** para vida longa acesso aos recursos. |
| redirect_uri | Recomendados | O redirect_uri do aplicativo em que você recebeu o authorization_code. |
| refresh_token | Necessário | O refresh_token original que você adquiriu no segundo trecho do fluxo. Observe que você deve ter usado o escopo `offline_access` em autorização e solicitações de token para receberem uma refresh_token. |
| client_secret | Necessário | O segredo de aplicativo que você gerou no [portal do Azure](https://portal.azure.com/). Esse segredo de aplicativo é um produto de segurança importantes. Você deve armazenar com segurança no seu servidor. Você também deve ter cuidado para girar esse segredo de cliente periodicamente. |

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
| access_token | O token de JWT assinado solicitada. |
| escopo | Os escopos que o token é válido, que podem ser usados para armazenamento em cache tokens para uso posterior. |
| expires_in | O período de tempo que o access_token é válido (em segundos). |
| refresh_token | Um refresh_token OAuth 2.0. O aplicativo pode usar esse token adquirir tokens adicionais após o atual expire.  Refresh_tokens são vivia longo e pode ser usado para manter o acesso aos recursos por longos períodos de tempo. Para obter mais detalhes, consulte a [referência de token B2C](active-directory-b2c-reference-tokens.md). |

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


## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de saída

Quando você deseja assinar o usuário sair do aplicativo, não é suficiente limpar seu aplicativo cookies ou final caso contrário, a sessão com o usuário. Você também deve redirecionar o usuário ao Azure AD para sair. Se você não conseguir fazer isso, o usuário poderá autenticar novamente para o seu aplicativo sem inserir suas credenciais novamente. Isso ocorre porque eles terão uma única logon sessão válida com o Azure AD.

Você pode simplesmente redirecionar o usuário para o `end_session_endpoint` que está listado no mesmo OpenID conectar metadados documento descrito na seção anterior "Validar o id_token":

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parâmetro | Necessário? | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| p | Necessário | A política que você deseja usar para assinar o usuário sair do aplicativo. |
| post_logout_redirect_uri | Recomendados | A URL que o usuário deve ser redirecionado após bem-sucedida saída. Se não for incluída, o usuário aparecerá uma mensagem genérica por Azure AD B2C.  |

> [AZURE.NOTE]
    Enquanto direciona o usuário para o `end_session_endpoint` limpará alguns do estado do usuário única logon com o Azure AD B2C, ele não se conectará o usuário sair da sessão do usuário identidade social provedor (IDP). Se o usuário seleciona IDP mesmo durante uma entrar subsequente, eles serão ser autenticado novamente, sem inserir suas credenciais. Se um usuário quiser sair do aplicativo B2C, isso não significa necessariamente desejam sair de sua conta do Facebook inteiramente. No entanto, no caso de contas locais, a sessão do usuário será finalizada corretamente.

## <a name="use-your-own-b2c-tenant"></a>Usar seu próprio locatário B2C

Se você quiser tentar essas solicitações para si mesmo, você deve primeiro realizar estas três etapas e, em seguida, substitua os valores de exemplo acima seu próprio:

- [Crie um locatário B2C](active-directory-b2c-get-started.md)e use o nome do seu locatário nas solicitações.
- [Criar um aplicativo](active-directory-b2c-app-registration.md) para obter uma ID de aplicativo e um redirect_uri. Convém incluir uma **web app/web api** em seu aplicativo e, opcionalmente, crie um **segredo do aplicativo**.
- [Criar suas regras](active-directory-b2c-reference-policies.md) para obter seus nomes de política.
