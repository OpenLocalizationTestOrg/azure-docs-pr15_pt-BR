<properties
    pageTitle="Azure AD v 2.0 escopos, permissões e consentimento | Microsoft Azure"
    description="Uma descrição de autorização no ponto de extremidade de v 2.0 do Azure AD, incluindo escopos, permissões e consentimento."
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
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="scopes-permissions--consent-in-the-v20-endpoint"></a>Escopos, permissões e consentimento no ponto de extremidade v 2.0

Aplicativos que se integram ao Azure AD siga um modelo de autorização específico que permite aos usuários controlar como um aplicativo pode acessar seus dados.  A implementação de v 2.0 desse modelo de autorização foi atualizada, alterando como um aplicativo deve interagir com o Azure AD.  Este tópico aborda os conceitos básicos deste modelo de autorização, incluindo escopos, permissões e consentimento.

> [AZURE.NOTE]
    Nem todos os cenários do Active Directory do Azure e recursos são compatíveis com o ponto de extremidade de v 2.0.  Para determinar se você deve usar o ponto de extremidade de v 2.0, leia sobre as [limitações da versão 2.0](active-directory-v2-limitations.md).

## <a name="scopes--permissions"></a>Escopos & permissões

Azure AD implementa o protocolo de autorização [OAuth 2.0](active-directory-v2-protocols.md) , que é um método para permitir que um aplicativo de terceiros 3º acessar os recursos da web hospedado em nome de um usuário.  Qualquer recurso hospedado na web que se integra ao Azure AD terá um identificador de recurso, ou **URI de ID do aplicativo**.  Por exemplo, alguns dos recursos de hospedado na web da Microsoft incluem:

- O Office 365 Unificação de mensagens API:`https://outlook.office.com`
- A API do Azure AD gráfico:`https://graph.windows.net`
- O Microsoft Graph:`https://graph.microsoft.com`

O mesmo é verdadeiro para quaisquer recursos de festa 3º integrado com o Azure AD.  Qualquer um desses recursos também pode definir um conjunto de permissões que podem ser usados para dividir a funcionalidade do recurso em partes menores.  Como exemplo, o Microsoft Graph tem algumas permissões definidas:

- Ler calendário de um usuário
- Gravar um calendário de usuário
- Enviar email como um usuário
- [+ mais](https://graph.microsoft.io)

Ao definir essas permissões, o recurso pode ter um controle refinado sobre seus dados e como ele é exposto para o mundo exterior.  Um aplicativo de terceiros 3º pode solicitar essas permissões de um usuário final - e o usuário final deverá aprovar as permissões para que o aplicativo pode atuar em seu nome.  Por reunir a funcionalidade do recurso em conjuntos de permissão menores, 3º aplicativos de terceiros podem ser criados para solicitar apenas as permissões específicas que precisam para realizar seu imposto.  Ele também permite que usuários finais souber exatamente como um aplicativo usará seus dados, para que elas fiquem mais certeza de que o aplicativo não está funcionando com intenções maliciosas.

No Azure AD e OAuth, essas permissões são conhecidas como **escopos**.  Você também pode vê-los denomina **oAuth2Permissions**.  Um escopo é representado no Azure AD como um valor de cadeia de caracteres.  Continuar com o exemplo do Microsoft Graph, o valor de escopo para cada permissão é:

- Leia o calendário de um usuário:`Calendar.Read`
- Gravar um calendário de usuário:`Mail.ReadWrite`
- Envie email como um usuário:`Mail.Send`

Um aplicativo pode solicitar essas permissões especificando os escopos em solicitações para o ponto de extremidade de v 2.0, conforme descrito abaixo.

## <a name="openid-connect-scopes"></a>Escopos de OpenId conectar

A implementação v 2.0 OpenID conectar tem algumas escopos bem definidos que não se aplicam a qualquer determinado recurso - `openid`, `email`, `profile`, e `offline_access`.

#### <a name="openid"></a>OpenId

Se um aplicativo executa entrar usando a [Conexão de OpenID](active-directory-v2-protocols.md#openid-connect-sign-in-flow), ele deve solicitar a `openid` escopo.  O `openid` escopo aparecerá na tela de consentimento do trabalho conta como a permissão "Entrar" e a tela de consentimento de conta Microsoft pessoal como a permissão "Exibir seu perfil e conectar-se a aplicativos e serviços usando sua conta da Microsoft".  Esta permissão possibilita a um aplicativo receber um identificador exclusivo para o usuário na forma do `sub` reivindicar.  Ele também permite o acesso de aplicativo para o ponto de extremidade de informações do usuário.  O `openid` escopo também pode ser usado o ponto de extremidade de token de v 2.0 adquirir id_tokens, que pode ser usado para proteger chamadas HTTP entre diferentes componentes de um aplicativo.

#### <a name="email"></a>Email

O `email` escopo pode ser incluído junto com o `openid` escopo e quaisquer outras pessoas.  Ele permite o acesso de aplicativo ao endereço de email principal do usuário na forma do `email` reivindicar.  O `email` declaração só será incluída em tokens se um endereço de email estiver associado com a conta de usuário, que não é sempre o caso.  Se usando o `email` escopo, seu aplicativo deve estar preparado para manipular o caso no qual o `email` declarar não existir no token.

#### <a name="profile"></a>Perfil

O `profile` escopo pode ser incluído junto com o `openid` escopo e quaisquer outras pessoas.  Ele permite o acesso de aplicativo a uma grande variedade de informações sobre o usuário.  Isso inclui, mas não está limitado a, nome fornecido do usuário, sobrenome, nome de usuário preferencial, ID do objeto e assim por diante.  Para obter uma lista completa de perfil declarações disponíveis no id_tokens para um determinado usuário, consulte a [referência de token de v 2.0](active-directory-v2-tokens.md).

#### <a name="offlineaccess"></a>Offline_access

O [ `offline_access` escopo](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) permite que o seu aplicativo para acessar recursos em nome de usuário por um longo período de tempo.  Na tela de consentimento da conta de trabalho, este escopo aparecerá como a permissão "Acesso a seus dados a qualquer momento".  Pessoais Microsoft consentimento tela conta, ele aparecerá como a permissão "Acesso a suas informações a qualquer momento".  Quando um usuário aprova o `offline_access` escopo, seu aplicativo será habilitado para receber tokens de atualização do ponto de extremidade token v 2.0.  Atualizar tokens são de vida longa e permitir que o seu aplicativo adquirir novas tokens de acesso, como os mais antigos expirarem.

Se seu aplicativo não solicitar a `offline_access` escopo, não receberá refresh_tokens.  Isso significa que quando você resgatar um authorization_code no [fluxo de código de autorização OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), você receberá apenas novamente um access_token do `/token` ponto de extremidade.  Esse access_token permanecerá válida por um curto período de tempo (geralmente uma hora), mas expirarão eventualmente.  No momento, seu aplicativo precisará redirecionar o usuário de volta para o `/authorize` ponto de extremidade para recuperar uma nova authorization_code.  Durante esse redirecionamento, o usuário pode ou não precise inserir suas credenciais novamente ou novamente consentimento para permissões, dependendo do tipo de aplicativo.

Para obter mais informações sobre como acessar e usar tokens de atualização, consulte a [referência de protocolo v 2.0](active-directory-v2-protocols.md).


## <a name="requesting-individual-user-consent"></a>Solicitar consentimento do usuário individual

Em uma solicitação de autorização [OpenID conectar ou OAuth 2.0](active-directory-v2-protocols.md) , um aplicativo pode solicitar as permissões necessárias usando a `scope` parâmetro de consulta.  Por exemplo, quando um usuário entra em um aplicativo, o aplicativo poderia enviar uma solicitação semelhante ao seguinte (com quebras de linha para fins de legibilidade):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

O `scope` parâmetro é uma lista separada de escopos que o aplicativo está solicitando.  Cada escopo individual é indicado anexando o valor de escopo ao URI (identificador App ID) do recurso.  A solicitação acima indica que o aplicativo precisa de permissão para o calendário de usuário de ler e enviar emails como o usuário.

Depois que o usuário insere suas credenciais, o ponto de extremidade de v 2.0 verificará um registro correspondente de **consentimento do usuário**.  Se o usuário não aceitou a qualquer uma das permissões solicitadas no passado, o ponto de extremidade de v 2.0 perguntará o usuário para conceder as permissões solicitadas.  

![Captura de tela do trabalho conta consentimento](../media/active-directory-v2-flows/work_account_consent.png)

Quando o usuário aprova a permissão, o consentimento será registrado para que o usuário não tem a consentimento novamente em suplementos de entrada subsequentes.

## <a name="requesting-consent-for-an-entire-tenant"></a>Solicitar consentimento para um locatário inteiro

Muitas vezes, quando uma organização compra uma assinatura a um aplicativo ou a licença, desejarem totalmente provisioná-lo para seus funcionários.  Como parte desse processo, um administrador da empresa pode conceder consentimento para o aplicativo agir em nome de qualquer funcionário.  Concedendo consentimento para um locatário inteiro, os funcionários dessa organização não terão a tela de consentimento para esse aplicativo.

Para solicitar consentimento para todos os usuários em um locatário, seu aplicativo pode usar o **ponto de extremidade de consentimento de administração**, descrito a seguir.

## <a name="admin-restricted-scopes"></a>Escopos restringido pelo administrador

Determinadas permissões de alto privilégio no ecossistema do Microsoft podem ser marcadas como **restringido pelo administrador**.  Exemplos de tais escopos incluem:

- Lendo dados de diretório de um organizaion:`Directory.Read`
- Dados de gravação ao diretório da organização:`Directory.ReadWrite`
- Grupos de segurança de leitura no diretório da organização:`Groups.Read.All`

Embora um usuário do consumidor pode conceder a um aplicativo acesso a esses dados, usuários organizacionais são restrito concedendo acesso para o mesmo conjunto de dados confidenciais da empresa.  Se seu aplicativo solicita acesso a uma dessas permissões de um usuário organizacional, o usuário receberá uma mensagem de erro indicando que eles estão autorizados a consentimento às permissões do seu aplicativo.

Se seu aplicativo requer acesso a esses escopos restringido pelo administrador para as organizações, solicite-los diretamente de um administrador da empresa também usando o **ponto de extremidade de consentimento de administração**, descrito a seguir.

Quando um administrador concede essas permissões via o ponto de extremidade de consentimento de administração, consentimento será concedido para todos os usuários no locatário, conforme descrito acima.

## <a name="using-the-admin-consent-endpoint"></a>Usando o ponto de extremidade de consentimento do administrador

Seguindo estas etapas, seu aplicativo poderá coletar permissões para todos os usuários em um determinado locatário, incluindo escopos restringido pelo administrador.  Para ver um exemplo de código que implementa desribed as etapas abaixo, consulte a [amostra de restrito escopos de administrador](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicitar as permissões no portal de registro do aplicativo

- Navegue até seu aplicativo em [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou [criar um aplicativo](active-directory-v2-app-registration.md) se você ainda não fez isso.
- Localize a seção **Permissões do Microsoft Graph** e adicione as permissões exigidas pelo seu aplicativo.
- Certifique-se de **Salvar** o registro de aplicativo

#### <a name="recommended-sign-the-user-into-your-app"></a>Recomendado: entrar do usuário no seu aplicativo

Normalmente quando criando um aplicativo que usa o administrador consentimento ponto de extremidade, o aplicativo precisa ter uma página/exibição que permite que o administrador aprovar permissões do aplicativo.  Esta página pode ser parte do fluxo de inscrição do aplicativo, parte das configurações do aplicativo ou um fluxo de "conectar" dedicado.  Em muitos casos, faz sentido para o aplicativo mostrar isso "conectar" Exibir somente depois que um usuário tem conectados com um trabalho ou escola conta da Microsoft.

Assinatura do usuário no aplicativo permite que você identifique a organização à qual o administrador pertence antes solicitando aprovem as permissões necessárias.  Enquanto não terminantemente necessário, ele pode ajudá-lo a criar uma experiência mais intuitiva para seus usuários organizacionais.  Para assinar o usuário na, siga nossos [tutoriais de protocolo v 2.0](active-directory-v2-protocols.md).

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
| locatário | Necessário | O locatário de diretório que você deseja solicitar permissão de.  Pode ser fornecido no formato de nome amigável ou guid. |
| client_id | Necessário | A Id de aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuída a seu aplicativo. |
| redirect_uri | Necessário | O redirect_uri onde você deseja que a resposta a ser enviada para o aplicativo tratar.  Ele deve corresponder exatamente um da redirect_uris registrado no portal. |
| estado | recomendados | Um valor incluído na solicitação que também será retornada na resposta token.  Pode ser uma cadeia de caracteres de qualquer conteúdo que desejar.  O estado é usado para codificar informações sobre o estado do usuário no aplicativo antes de que ocorreu a solicitação de autenticação, como a página ou o modo de exibição estivessem em. |

Neste ponto, o Azure AD aplicará somente um administrador locatário pode entrar para concluir a solicitação.  O administrador será solicitado a aprovar todas as permissões solicitado para o aplicativo no portal do registro. 

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

Depois que você recebeu resposta bem-sucedida do ponto de extremidade de consentimento admin, o seu aplicativo obteve as permissões que ele solicitado.  Agora, você pode mover para solicitar um símbolo para o recurso desejado, conforme descrito abaixo.

## <a name="using-permissions"></a>Usando permissões

Depois que o usuário dê o seu consentimento para permissões para seu aplicativo, seu aplicativo pode adquirir tokens de acesso que representam permissão de seu aplicativo para acessar um recurso em alguma capacidade.  Um token de acesso determinado só pode ser usado para uma única resorce, mas codificado dentro dele serão todas as permissões que seu aplicativo recebeu para o recurso.  Para adquirir um token de acesso, o seu aplicativo pode fazer uma solicitação para o ponto de extremidade de token de v 2.0:

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

O token de acesso resultante pode ser usado em solicitações HTTP ao recurso - -confiavelmente indicará ao recurso que seu aplicativo tem a permissão adequada para executar uma determinada tarefa.  

Para obter mais detalhes sobre o protocolo OAuth 2.0 e como adquirir tokens de acesso, consulte a [referência de protocolo de ponto de extremidade de v 2.0](active-directory-v2-protocols.md).
