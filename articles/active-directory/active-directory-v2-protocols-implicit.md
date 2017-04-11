<properties
    pageTitle="Azure AD v 2.0 implícito fluxo | Microsoft Azure"
    description="Criando aplicativos da web usando a implementação de v 2.0 do Azure AD do fluxo de implícito para aplicativos de única página."
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---spas-using-the-implicit-flow"></a>v 2.0 protocolos - SPAs usando o fluxo implícito
Com o ponto de extremidade de v 2.0, você pode assinar os usuários em seus aplicativos de única página com contas pessoais e de trabalho/escola da Microsoft.  Página única e outros aplicativos de JavaScript que executam principalmente em uma face de navegador algumas interessantes desafios quando se trata de autenticação:

- As características de segurança desses aplicativos são significativamente diferentes aplicativos web do servidor tradicional com base.
- Muitos servidores de autorização & provedores de identidade não dão suporte a solicitações CORS.
- Navegador de página inteira redireciona longe do aplicativo ficam particularmente invasivo à experiência do usuário.

Para esses aplicativos (acha: AngularJS, Ember.js, React.js, etc) Azure AD oferece suporte ao fluxo de OAuth 2.0 implícito conceder.  O fluxo implícito é descrito na [Especificação de 2.0 OAuth](http://tools.ietf.org/html/rfc6749#section-4.2).  Seu principal benefício é que ele permite que o aplicativo obter tokens do Azure AD sem executar um servidor back-end troca de credenciais.  Isso permite que o aplicativo entrar no usuário, manter sessão e obter tokens para outra web APIs tudo isso dentro do cliente do código JavaScript.  Há algumas considerações de segurança importante levar em conta ao usar o fluxo implícito - especificamente em torno de [cliente](http://tools.ietf.org/html/rfc6749#section-10.3) e [representação de usuário](http://tools.ietf.org/html/rfc6749#section-10.3).

Se você quiser usar o fluxo implícito e Azure AD para adicionar autenticação a seu aplicativo JavaScript, recomendamos que você use nossa biblioteca de JavaScript Abrir origem, [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js).  Existem alguns tutoriais AngularJS disponíveis [aqui](active-directory-appmodel-v2-overview.md#getting-started) para ajudá-lo a começar.  

No entanto, se você preferir não usar uma biblioteca em seu aplicativo de página única e enviar mensagens de protocolo por conta própria, siga as etapas gerais abaixo.

> [AZURE.NOTE]
    Nem todos os cenários do Active Directory do Azure e recursos são compatíveis com o ponto de extremidade de v 2.0.  Para determinar se você deve usar o ponto de extremidade de v 2.0, leia sobre as [limitações da versão 2.0](active-directory-v2-limitations.md).
    
## <a name="protocol-diagram"></a>Diagrama de protocolo
Todo implícito de entrada do fluxo esta aparência: cada uma das etapas são descritas em detalhes abaixo.

![OpenId conectar raias](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-the-sign-in-request"></a>Enviar a solicitação de entrada

Para entrar inicialmente o usuário em seu aplicativo, você pode enviar uma solicitação de autorização [OpenID conectar](active-directory-v2-protocols-oidc.md) e obter um `id_token` do ponto de extremidade v 2.0:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [AZURE.TIP] Clique no link abaixo para executar esta solicitação! Após entrar, seu navegador deve ser redirecionado para `https://localhost/myapp/` com um `id_token` na barra de endereços.
    <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/Common/oauth2/v2.0/Authorize...</a>

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| locatário | Necessário | O `{tenant}` valor no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo.  Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de locatários.  Para obter mais detalhes, consulte [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id | Necessário | A Id de aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuída a seu aplicativo. |
| response_type | Necessário | Deve incluir `id_token` para conectar OpenID entrar.  Ele também pode incluir o response_type `token`. Usando `token` aqui permitirá que seu aplicativo receber um token de acesso imediatamente do ponto de extremidade autorizar sem precisar fazer uma segunda solicitação para o ponto de extremidade de autorizar.  Se você usar o `token` response_type, o `scope` parâmetro deve conter um escopo que indica qual recurso para emitir o token. |
| redirect_uri | recomendados | O redirect_uri do aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas por seu aplicativo.  Ele deve corresponder exatamente um da redirect_uris que você registrado no portal do, exceto deve ser codificada de url. |
| escopo | Necessário | Uma lista separada de escopos.  Para se conectar OpenID, ele deve incluir o escopo `openid`, que converte para a permissão "Entrar" no consentimento da interface do usuário.  Opcionalmente, você talvez também queira incluir o `email` ou `profile` [escopos](active-directory-v2-scopes.md) para obter acesso aos dados de usuário adicionais.  Você também pode incluir outros escopos nesta solicitação para solicitar consentimento para vários recursos.  |
| response_mode | recomendados | Especifica o método que deve ser usado para enviar o back token resultante para seu aplicativo.  Deve ser `fragment` para o fluxo de implícito.  |
| estado | recomendados | Um valor incluído na solicitação que também será retornada na resposta token.  Pode ser uma cadeia de caracteres de qualquer conteúdo que desejar.  Um valor exclusivo gerado aleatoriamente normalmente é usado para [prevenir ataques de falsificação de solicitação intersite](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes de que ocorreu a solicitação de autenticação, como a página ou o modo de exibição estivessem em. |
| valor de uso único | Necessário | Um valor incluído na solicitação, gerada pelo aplicativo, que será incluído na id_token resultante como uma declaração.  O aplicativo pode verificar esse valor para atenuar ataques de repetição de token.  Normalmente, o valor é uma cadeia de caracteres aleatório exclusiva que pode ser usada para identificar a origem da solicitação.  |
| prompt | opcional | Indica o tipo de interação do usuário é necessária.  Os valores válidos somente neste momento são 'login', 'Nenhum' e 'consentimento'.  `prompt=login`forçar o usuário inserir suas credenciais nessa solicitação, eliminando single sign-no.  `prompt=none`é o oposto - garantirá que o usuário não será apresentado qualquer prompt interativo qualquer.  Se a solicitação não pode ser concluída silenciosamente por meio do logon único no, o ponto de extremidade de v 2.0 retornará um erro.  `prompt=consent`acionará a caixa de diálogo de consentimento OAuth depois que o usuário entra no, solicitando que o usuário para conceder permissões para o aplicativo. |
| login_hint | opcional | Pode ser usado para preencher previamente o campo de endereço de email/nome de usuário da página de entrada do usuário, se você souber o nome de usuário antecedência.  Muitas vezes aplicativos usará esse parâmetro durante nova autenticação, já ter extraído o nome de usuário de uma entrada usando anterior a `preferred_username` reivindicar. |
| domain_hint | opcional | Pode ser uma das `consumers` ou `organizations`.  Se incluída, ele ignorará o processo de descoberta baseada em email esse usuário percorre na página, entrar v 2.0 levam a uma experiência de usuário simplificada um pouco mais.  Muitas vezes aplicativos usará esse parâmetro durante nova autenticação, extraindo a `tid` reivindicar da id_token.  Se o `tid` reivindicar valor é `9188040d-6c67-4c5b-b112-36a304b66dad`, você deve usar `domain_hint=consumers`.  Caso contrário, use `domain_hint=organizations`. |

Neste ponto, o usuário será solicitado a digitar suas credenciais e concluir a autenticação.  O ponto de extremidade de v 2.0 garantirá que o usuário aceitou as permissões indicadas na `scope` parâmetro de consulta.  Se o usuário não aceitou a qualquer uma das permissões, ele perguntará ao usuário concordar com as permissões necessárias.  Detalhes de [permissões, consentimento e locatários vários aplicativos são fornecidos aqui](active-directory-v2-scopes.md).

Depois que o usuário faz a autenticação e concede consentimento, o ponto de extremidade de v 2.0 retornará uma resposta para o seu aplicativo no indicado `redirect_uri`, usando o método especificado na `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida usando `response_mode=fragment` e `response_type=id_token+token` parece com o seguinte, com quebras de linha para legibilidade:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access_token | Se incluído `response_type` inclui `token`. O token de acesso que o aplicativo solicitado, nesse caso para o Microsoft Graph.  O token de acesso não deve ser decodificar ou inspecionado caso contrário, ele pode ser tratado como uma cadeia de caracteres opaca. |
| token_type | Se incluído `response_type` inclui `token`.  Sempre será `Bearer`. |
| expires_in | Se incluído `response_type` inclui `token`.  Indica o número de segundos que o token é válido, para fins de cache. |
| escopo | Se incluído `response_type` inclui `token`.  Indica o escopo (s) para que o access_token será válido. |
| id_token | O id_token que o aplicativo solicitado. Você pode usar o id_token para verificar a identidade do usuário e iniciar uma sessão com o usuário.  Mais detalhes sobre id_tokens e seu conteúdo é incluída na [referência de token de ponto de extremidade de v 2.0](active-directory-v2-tokens.md).  |
| estado | Se um parâmetro de estado for incluído na solicitação, o mesmo valor deve aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e resposta são idênticos. |


#### <a name="error-response"></a>Resposta de erro
Respostas de erro também podem ser enviadas para o `redirect_uri` para que o aplicativo pode manipulá-los adequadamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| Erro | Uma sequência de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| error_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa de um erro de autenticação.  |

## <a name="validate-the-idtoken"></a>Validar o id_token
Basta receber um id_token não é suficiente para autenticar o usuário; Você deve validar assinatura do id_token e verifique se as declarações no token por requisitos do seu aplicativo.  O ponto de extremidade de v 2.0 usa [JSON Web Tokens (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos.

Você pode escolher validar a `id_token` no cliente de código, mas uma prática comum é enviar a `id_token` em um servidor back-end e realize a validação lá.  Depois que você já validados a assinatura do id_token, há algumas declarações, que você será solicitado para confirmar.  Consulte a [referência de token de v 2.0](active-directory-v2-tokens.md) para obter mais informações, incluindo [Validando Tokens](active-directory-v2-tokens.md#validating-tokens) e [Importantes informações sobre assinatura de chave sobreposição](active-directory-v2-tokens.md#validating-tokens).  É recomendável fazer uso de uma biblioteca para análise e validando tokens - há pelo menos uma disponível para a maioria dos idiomas e plataformas.
<!--TODO: Improve the information on this-->

Talvez você também queira validar declarações adicionais dependendo do cenário.  Algumas validações comuns incluem:

- Garantindo que a organização do usuário/inscreveu para o aplicativo.
- Garantindo que o usuário tem autorização/privilégios apropriados
- Garantindo que um determinado nível de autenticação ocorreu, como autenticação multifator.

Para obter mais informações sobre as declarações em um id_token, consulte a [referência de token de ponto de extremidade de v 2.0](active-directory-v2-tokens.md).

Depois que você tiver validada completamente o id_token, você pode iniciar uma sessão com o usuário e use as declarações a id_token para obter informações sobre o usuário em seu aplicativo.  Essas informações podem ser usadas para exibição, registros, autorizações, etc.

## <a name="get-access-tokens"></a>Obter tokens de acesso

Agora que você tiver conectado o usuário ao seu aplicativo de página única, você pode obter tokens de acesso para web chamada APIs protegidas por Azure AD, como o [Microsoft Graph](https://graph.microsoft.io).  Mesmo se você já recebeu um token usando o `token` response_type, você pode usar esse método para adquira tokens para recursos adicionais sem precisar redirecionar o usuário entrar novamente.

No fluxo de OpenID conectar/OAuth normal, você faria isso fazendo uma solicitação para o v 2.0 `/token` ponto de extremidade.  No entanto, o ponto de extremidade de v 2.0 não suporta solicitações CORS, para que fazer chamadas de AJAX para obter e atualizar tokens estiver fora da pergunta.  Em vez disso, você pode usar o fluxo implícito em um iframe oculto para obter novos tokens para outra web APIs: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [AZURE.TIP] Tente copiar e colar a abaixo solicitação em uma guia do navegador! (Não se esqueça de substituir o `domain_hint` e o `login_hint` valores com os valores corretos para o usuário)

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint={{consumers-or-organizations}}&login_hint={{your-username}}
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| locatário | Necessário | O `{tenant}` valor no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo.  Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de locatários.  Para obter mais detalhes, consulte [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id | Necessário | A Id de aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuída a seu aplicativo. |
| response_type | Necessário | Deve incluir `id_token` para conectar OpenID entrar.  Ele também pode incluir outros response_types, tais como `code`. |
| redirect_uri | recomendados | O redirect_uri do aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas por seu aplicativo.  Ele deve corresponder exatamente um da redirect_uris que você registrado no portal do, exceto deve ser codificada de url. |
| escopo | Necessário | Uma lista separada de escopos.  Para obter tokens, inclua todos os [escopos](active-directory-v2-scopes.md) exigem para o recurso de interesse.  |
| response_mode | recomendados | Especifica o método que deve ser usado para enviar o back token resultante para seu aplicativo.  Pode ser uma das `query`, `form_post`, ou `fragment`.  |
| estado | recomendados | Um valor incluído na solicitação que também será retornada na resposta token.  Pode ser uma cadeia de caracteres de qualquer conteúdo que desejar.  Um valor exclusivo gerado aleatoriamente normalmente é usado para prevenir ataques de falsificação de solicitação intersite.  O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes de que ocorreu a solicitação de autenticação, como a página ou o modo de exibição estivessem em. |
| valor de uso único | Necessário | Um valor incluído na solicitação, gerada pelo aplicativo, que será incluído na id_token resultante como uma declaração.  O aplicativo pode verificar esse valor para atenuar ataques de repetição de token.  Normalmente, o valor é uma cadeia de caracteres aleatório exclusiva que pode ser usada para identificar a origem da solicitação.  |
| prompt | Necessário | Para atualizar e Obtendo tokens em um iframe oculto, você deve usar `prompt=none` para garantir que o iframe não travar na página entrar v 2.0 e retorna imediatamente. |
| login_hint | Necessário | Para atualizar e Obtendo tokens em um iframe oculto, você deve incluir o nome de usuário do usuário nessa dica para distinguir entre várias sessões, que o usuário pode ter em um determinado ponto no tempo. Você pode extrair o nome de usuário de uma entrada usando anterior a `preferred_username` reivindicar. |
| domain_hint | Necessário | Pode ser uma das `consumers` ou `organizations`.  Para atualizar e Obtendo tokens em um iframe oculto, você deve incluir o domain_hint na solicitação.  Você deve extrair o `tid` reivindicar do id_token de um anterior entrar para determinar qual valor a ser usado.  Se o `tid` reivindicar valor é `9188040d-6c67-4c5b-b112-36a304b66dad`, você deve usar `domain_hint=consumers`.  Caso contrário, use `domain_hint=organizations`. |

Graças a `prompt=none` parâmetro, essa solicitação será tanto êxito ou falhar imediatamente e retorne ao seu aplicativo.  Uma resposta bem-sucedida será enviada para o seu aplicativo no indicado `redirect_uri`, usando o método especificado na `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta bem-sucedida
Uma resposta bem-sucedida usando `response_mode=fragment` aparência semelhante a:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access_token | O token de que o aplicativo solicitado. |
| token_type | Sempre será `Bearer`. |
| estado | Se um parâmetro de estado for incluído na solicitação, o mesmo valor deve aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e resposta são idênticos. |
| expires_in | Por quanto tempo o token de acesso é válido (em segundos). |
| escopo | Os escopos que o token de acesso é válido para. |

#### <a name="error-response"></a>Resposta de erro
Respostas de erro também podem ser enviadas para o `redirect_uri` para que o aplicativo pode manipulá-los adequadamente.  No caso de `prompt=none`, um erro esperado será:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| Erro | Uma sequência de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| error_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa de um erro de autenticação.  |

Se você receber esse erro na solicitação de iframe, o usuário deve interativamente entrar novamente para recuperar um novo símbolo.  Você pode optar por lidar com esse caso na forma que faça sentido para seu aplicativo.

## <a name="refreshing-tokens"></a>Atualizando tokens

Ambos `id_token`s e `access_token`s expirará após um curto período de tempo, para que seu aplicativo deve estar preparado para atualizar esses tokens periodicamente.  Para atualizar qualquer tipo de token, você pode executar a mesma solicitação de iframe ocultos acima usando o `prompt=none` parâmetro para controlar o comportamento do Azure AD.  Se você quiser receber uma nova `id_token`, certifique-se de usar `response_type=id_token` e `scope=openid`, bem como um `nonce` parâmetro.


## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de sair

O OpenIdConnect `end_session_endpoint` não é suportado atualmente pelo ponto de extremidade de v 2.0. Isso significa que seu aplicativo não envia uma solicitação para o ponto de extremidade de v 2.0 para terminar uma sessão de usuário e limpar os cookies definidos pelo ponto de extremidade de v 2.0.
Para assinar um usuário check-out, seu aplicativo simplesmente pode encerrar sua própria sessão com o usuário e deixar a sessão do usuário com a v 2.0 ponto de extremidade no-intacto.  Na próxima vez que o usuário tentar entrar, eles verá uma página de "Escolha conta", com suas contas no conectado ativamente listadas.
Nessa página, o usuário pode escolher sair qualquer conta, encerrando a sessão com o ponto de extremidade de v 2.0.

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->