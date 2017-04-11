<properties
    pageTitle="Active Directory do Azure B2C | Microsoft Azure"
    description="Os tipos de tokens emitidos no Azure Active Directory B2C."
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


# <a name="azure-ad-b2c-token-reference"></a>B2C do Azure AD: Referência de Token

Azure Active Directory (AD Azure) B2C emite vários tipos de tokens de segurança enquanto ele processa cada [fluxo de autenticação](active-directory-b2c-apps.md). Este documento descreve o formato, características de segurança e conteúdo de cada tipo de token.

## <a name="types-of-tokens"></a>Tipos de tokens

Azure AD B2C suporta o [protocolo de autorização de OAuth 2.0](active-directory-b2c-reference-protocols.md), que usa os tokens de acesso e tokens de atualização. Ele também dá suporte à autenticação de entrada e via [OpenID conectar](active-directory-b2c-reference-protocols.md), que apresenta um terceiro tipo de token: o token de ID. Cada um desses tokens é representada por um token de portador.

Um token de portador é um token de segurança leve que concede acesso à "portador" para um recurso protegido. O portador é qualquer pessoa que pode apresentar o token. Azure AD deve primeiro autenticar uma festa antes que ele possa receber um token de portador. Mas se as etapas necessárias não são levadas para proteger o token de transmissão e armazenamento, ele pode ser interceptado e usado por uma pessoa não intencionais. Alguns tokens de segurança tem um mecanismo interno para impedir que autorizadas usá-los, mas tokens de portador não tem esse mecanismo. Eles devem ser transportados em um canal seguro, como segurança da camada de transporte (HTTPS).

Se um token de portador for transmitido fora de um canal seguro, um terceiro mal-intencionado pode usar um ataque no meio para adquirir o token e usá-lo para obter acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam quando tokens de portador são armazenados ou em cache para uso posterior. Sempre certifique-se de que seu aplicativo transmite e armazena tokens portador de maneira segura.

Para considerações de segurança adicionais em tokens portador, consulte [RFC 6750 seção 5](http://tools.ietf.org/html/rfc6750).

Muitos dos tokens Azure AD B2C problemas são implementados como tokens de web JSON (JWTs). Um JWT é um meio compacto e confiável URL de transferência de informações entre duas partes. JWTs contêm informações conhecidas como declarações. Estas são declarações de informações sobre o portador e o assunto do token. As declarações no JWTs são objetos JSON que são codificados e serializados para transmissão. Porque os JWTs emitidos por Azure AD B2C estiver conectados, mas não criptografados, você pode inspecionar facilmente o conteúdo de uma JWT depurá-lo. Diversas ferramentas estão disponíveis que pode fazer isso, incluindo [calebb.net](http://calebb.net). Para saber mais sobre JWTs, consulte [as especificações de JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Tokens de ID

Um token de ID é uma forma de token de segurança que seu aplicativo recebe do Azure AD B2C `authorize` e `token` pontos de extremidade. Tokens de identificação são representados como [JWTs](#types-of-tokens)e contêm declarações que você pode usar para identificar os usuários em seu aplicativo. Quando os tokens de identificação são adquiridos do `authorize` ponto de extremidade, muitas vezes são usados para entrar no usuários para aplicativos da web. Quando os tokens de identificação são adquiridos do `token` ponto de extremidade, eles podem ser enviados em solicitações HTTP durante a comunicação entre dois componentes do mesmo aplicativo ou serviço. Você pode usar as declarações em um token de ID ajustá-la. Eles são usados para exibir informações de conta ou para tomar decisões de controle de acesso em um aplicativo.  

Tokens de identificação estiver conectados, mas elas não são criptografadas neste momento. Quando seu aplicativo ou API recebe um token de ID, ele deve [Validar a assinatura](#token-validation) para provar que o token é autêntico. Seus aplicativos ou API também deve validar algumas declarações no token para provar que ela é válida. Dependendo dos requisitos de cenário, as declarações validadas por um aplicativo podem variar, mas seu aplicativo deve executar algumas [validações de declaração comuns](#token-validation) em cada cenário.

#### <a name="sample-id-token"></a>Token de ID de amostra
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>Tokens de acesso

Um token de acesso também é uma forma de token de segurança que seu aplicativo recebe do Azure AD B2C `authorize` e `token` pontos de extremidade. Tokens de acesso também são representados como [JWTs](#types-of-tokens)e contêm declarações que você pode usar para identificar os usuários de sua APIs & serviços da web.

Tokens de acesso estiver conectados, mas eles não são muito semelhantes às tokens de id e criptografada neste momento.  Tokens de acesso devem ser usados para fornecer acesso a APIs & serviços web e para identificar e autenticar o usuário nesses serviços.  No entanto, eles não fornecem qualquer declaração de autorização desses serviços.  Isto é dizer que o `scp` declarações em tokens de acesso não limitar ou caso contrário representam o acesso que você recebeu o assunto do token.

Quando sua API recebe um token de acesso, ele deve [Validar a assinatura](#token-validation) para provar que o token é autêntico. Sua API também deve validar algumas declarações no token para provar que ela é válida. Dependendo dos requisitos de cenário, as declarações validadas por um aplicativo podem variar, mas seu aplicativo deve executar algumas [validações de declaração comuns](#token-validation) em cada cenário.

### <a name="claims-in-id--access-tokens"></a>Declarações em ID & Tokens de acesso

Quando você usa o Azure AD B2C, você tem controle refinado sobre o conteúdo do seu tokens. Você pode configurar [políticas](active-directory-b2c-reference-policies.md) para enviar determinados conjuntos de dados do usuário em declarações que seu aplicativo requer para suas operações. Essas declarações podem incluir propriedades padrão como o usuário `displayName` e `emailAddress`. Eles também podem incluir [atributos do usuário personalizada](active-directory-b2c-reference-custom-attr.md) que você pode definir no seu diretório B2C. Cada ID & acesso token que recebe conterá um determinado conjunto de declarações de segurança. Seus aplicativos podem usar essas declarações para autenticar com segurança usuários e solicitações.

Observe que as declarações em tokens de ID não são retornadas em uma ordem específica. Além disso, as novas declarações podem ser introduzidas em tokens de ID a qualquer momento. Seu aplicativo não deve quebrar conforme novas declarações são introduzidas. Aqui estão as declarações que você espera existir em tokens de acesso e ID emitidos por Azure AD B2C. Quaisquer declarações adicionais serão determinadas por políticas. Prática, tente inspecionar as declarações no token de ID de amostra colando-o em [calebb.net](http://calebb.net). Detalhes adicionais podem ser encontradas na [especificação de OpenID conectar](http://openid.net/specs/openid-connect-core-1_0.html).

| Nome | Declaração | Valor de exemplo | Descrição |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Público-alvo | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Uma declaração de audiência identifica o destinatário pretendido do token. Para Azure AD B2C, a audiência é ID de aplicativo do seu aplicativo, como atribuído para o seu aplicativo no portal de registro do aplicativo. Seu aplicativo deve validar esse valor e rejeitar o token se ele não corresponder. |
| Emissor | `iss` | `https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Essa declaração identifica o serviço de token de segurança (STS) que constrói e retorna o token. Ele também identifica a pasta de Azure AD em que o usuário foi autenticado. Seu aplicativo deve validar a declaração de emissor para garantir que o token veio do ponto de extremidade v 2.0. |
| Emitido em | `iat` | `1438535543` | Essa declaração é o tempo em que o token foi emitido, representado em vez de época. |
| Tempo de expiração | `exp` | `1438539443` | O tempo de expiração declaração é a hora em que o token se torna inválido, representados em vez de época. Seu aplicativo deve usar esta declaração para verificar a validade da vida útil do token.  |
| Não antes | `nbf` | `1438535543` | Essa declaração é o tempo em que o token se torna válido, representado em vez de época. Isso geralmente é a mesma que o tempo que o token foi emitido. Seu aplicativo deve usar esta declaração para verificar a validade da vida útil do token.  |
| Versão | `ver` | `1.0` | Esta é a versão do token ID, conforme definido pelo Azure AD. |
| Hash de código | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Um hash de código está incluído em um token de ID somente quando o token seja emitido junto com um código de autorização OAuth 2.0. Um hash de código pode ser usado para validar a autenticidade de um código de autorização. Consulte a [especificação de OpenID conectar-se](http://openid.net/specs/openid-connect-core-1_0.html) para obter mais detalhes sobre como executar essa validação. |
| Hash de token de acesso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Um hash de token de acesso está incluído em um token de ID somente quando o token seja emitido junto com um token de acesso OAuth 2.0. Um hash de token de acesso pode ser usado para validar a autenticidade de um token de acesso. Consulte a [especificação de OpenID conectar-se](http://openid.net/specs/openid-connect-core-1_0.html) para obter mais detalhes sobre como executar essa validação. |
| Valor de uso único | `nonce` | `12345` | Um valor de uso único é uma estratégia usada para atenuar ataques de repetição de token. Seu aplicativo pode especificar um valor de uso único em uma solicitação de autorização usando o `nonce` parâmetro de consulta. O valor que você fornecer na solicitação será emitido inalterado no `nonce` reivindicar de apenas um token de ID. Isso permite que seu aplicativo verificar o valor com relação ao valor que ele especificado na solicitação, que associa a um determinado token de ID de sessão do aplicativo. Seu aplicativo deve executar este validação durante o processo de validação token de ID. |
| Assunto | `sub` | `Not supported currently. Use oid claim.` | Esta é uma entidade de segurança sobre o qual o token declarações informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuída ou reutilizadas. Ele pode ser usado para executar verificações de autorização com segurança, como quando o token é usado para acessar um recurso. No entanto, a declaração de assunto ainda não está implementada no Azure AD B2C. Você deve configurar suas políticas para incluir a identificação do objeto `oid` reivindicar e use seu valor para identificar os usuários, em vez de usar a declaração de assunto para autorização. |
| Referência de classe de contexto de autenticação | `acr` | `b2c_1_sign_in` | Este é o nome da política que foi usado para adquirir o token de ID.  |
| Tempo de autenticação | `auth_time` | `1438535543` | Essa declaração é o tempo em que um usuário última inserido de credenciais, representado no tempo de época. |


### <a name="refresh-tokens"></a>Atualizar tokens

Atualizar tokens são tokens de segurança que seu aplicativo pode usar para adquirir novos tokens de ID e acessar tokens em um fluxo de OAuth 2.0. Eles fornecem seu aplicativo longo prazo acesso aos recursos em nome de usuários sem a necessidade de interação com esses usuários.

Para receber uma atualização token em uma resposta de token, a seu aplicativo deve solicitar a `offline_acesss` escopo. Para saber mais sobre o `offline_access` escopo, consulte a [referência de protocolo do Azure AD B2C](active-directory-b2c-reference-protocols.md).

Atualizar tokens são e sempre será, completamente opaco para o seu aplicativo. São emitidos por Azure AD e podem ser inspecionadas e interpretadas apenas por Azure AD. Eles são vida longa, mas seu aplicativo não deve ser escrito com as expectativas que um token de atualização irá durar por um período específico de tempo. Tokens de atualização podem ser inválidas em qualquer momento para uma variedade de motivos. A única maneira de seu aplicativo saber se um token de atualização é válido é tentar resgatá-lo fazendo uma solicitação de token Azure AD.

Quando você resgatar um token de atualização para um novo token (e se seu aplicativo tiver sido concedido a `offline_access` escopo), você receberá um novo token de atualização no token de resposta. Você deve salvar o token de atualização emitidos recentemente. Ele deve substituir o token de atualização que você usava anteriormente na solicitação. Isso ajuda a garantir que seus tokens de atualização permaneçam válidos para contanto que possível.

## <a name="token-validation"></a>Validação de token

Para validar um token, seu aplicativo deve verificar a assinatura e declarações do token.

Muitas bibliotecas de abrir origem estão disponíveis para validar JWTs, dependendo do seu idioma preferencial. Recomendamos que você explorar essas opções em vez de implementa sua própria lógica de validação. As informações neste guia podem ajudar você a aprender a usar essas bibliotecas corretamente.

### <a name="validate-the-signature"></a>Validar a assinatura
Um JWT contém três segmentos, separados pela `.` caracteres. O primeiro segmento é o **cabeçalho**, o segundo é o **corpo**e o terceiro é a **assinatura**. O segmento de assinatura pode ser usado para validar a autenticidade do token de forma que ele pode ser confiável para seu aplicativo.

Azure AD B2C tokens são assinados usando algoritmos de criptografia assimétricos padrão da indústria, como RSA 256. O cabeçalho do token contém informações sobre o método de criptografia e chave usada para assinar o token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

O `alg` declaração indica o algoritmo que foi usado para assinar o token. O `kid` declaração indica a chave pública específica que foi usada para assinar o token.

A qualquer momento, o Azure AD pode assinar um token usando qualquer um de um determinado conjunto de pares de chaves pública-particular. Azure AD gira o conjunto possível de chaves periodicamente, para que seu aplicativo deve ser escrito para lidar com essas alterações chaves automaticamente. Uma frequência razoável para verificar se há atualizações para as chaves públicas usadas pelo Azure AD é a cada 24 horas.

Azure AD B2C tem um ponto de extremidade de metadados OpenID se conectar. Isso permite que aplicativos buscar informações sobre Azure AD B2C em tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e token de chaves de assinatura. Seu diretório B2C contém um documento de metadados JSON para cada política. Por exemplo, o documento de metadados para o `b2c_1_sign_in` política no `fabrikamb2c.onmicrosoft.com` está localizado em:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com`é o diretório B2C usado para autenticar o usuário, e `b2c_1_sign_in` é a política usada para adquirir o token. Para determinar qual política foi usada para assinar um token (e para onde ir para buscar os metadados), você tem duas opções. Primeiro, o nome da política está incluído na `acr` reivindicar no token. Você pode analisar declarações sair do corpo do JWT por base 64 decodificar corpo e desserializar a cadeia de caracteres JSON que resulta. O `acr` declaração será o nome da política que foi usado para emitir o token.  A outra opção é codificar a política no valor da `state` parâmetro quando emitiu a solicitação e, em seguida, decodificar para determinar qual diretiva foi usada. O método é válido.

O documento de metadados é um objeto JSON que contém várias partes útil das informações. Eles incluem o local dos pontos de extremidade necessários para executar a autenticação OpenID se conectar. Eles também incluem `jwks_uri`, que oferece o local do conjunto de chaves públicas que são usados para assinar tokens. Que local é fornecido aqui, mas é melhor buscar o local dinamicamente usando o documento de metadados e analisando `jwks_uri`:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

O documento JSON localizado neste URL contém todas as informações de chave públicas em uso em um momento específico. Seu aplicativo pode usar o `kid` reivindicar no cabeçalho da JWT para selecionar a chave pública no documento JSON que é usado para assinar um token específico. Ele pode executar validação de assinatura usando a chave pública correta e o algoritmo indicado.

Uma descrição de como executar a validação de assinatura está fora do escopo deste documento. Muitas bibliotecas de abrir origem estão disponíveis para ajudá-lo com isso se você precisar dele.

### <a name="validate-the-claims"></a>Valide as declarações
Quando seu aplicativo ou API recebe um token de ID, ele também deve executar várias verificações contra as declarações no token de ID. Esses incluem, mas não estão limitados a:

- A declaração de **audiência** : Isso verifica que o token de ID foi destinado a ser dada ao seu aplicativo.
- As declarações **não antes** e a **hora de expiração** : esses verificar se o token de ID não expirou.
- A declaração de **emissor** : Isso verifica se o token foi emitido para o seu aplicativo por Azure AD.
- O **valor de uso único**: esta é uma estratégia de atenuação de ataque de repetição token.

Para obter uma lista completa de validações de que seu aplicativo deve executar, consulte a [especificação de OpenID conectar](https://openid.net). Detalhes dos valores esperados para essas declarações são incluídos na [seção token](#types-of-tokens)anterior.  

## <a name="token-lifetimes"></a>Vida útil de token

Os seguintes tempos de vida de token são fornecidos para aumentar o seu conhecimento. Elas podem ajudá-lo quando você desenvolver e depurar aplicativos. Observe que seus aplicativos não devem ser escritos esperar qualquer uma destas vidas permaneçam constantes. Eles podem e serão alterado.  Você pode ler mais sobre a personalização das vidas token no Azure AD B2C [aqui](active-directory-b2c-token-session-sso.md).

| Token | Vida útil | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| Tokens de ID | Uma hora | Tokens de identificação são normalmente válidas por uma hora. Seu aplicativo web pode usar este vida útil para manter suas própria sessões com usuários (recomendados). Você também pode escolher um tempo de vida de sessão diferente. Se seu aplicativo precisar Obtenha uma nova identificação token, ele simplesmente precisa fazer uma nova solicitação de entrada Azure AD. Se um usuário tiver uma sessão de navegador válido com o Azure AD, esse usuário pode não ser necessário inserir as credenciais novamente. |
| Atualizar tokens | Até 14 dias | Um token de atualização único é válido para um máximo de 14 dias. No entanto, um token de atualização pode se tornar inválido a qualquer momento por vários motivos. Seu aplicativo deve continuar, tente usar um token de atualização até que a solicitação falhar ou seu aplicativo substitui o token de atualização com um novo.  Um token de atualização também pode se tornar inválido se 90 dias passou desde que o usuário inseriu últimos credenciais. |
| Códigos de autorização | Cinco minutos | Códigos de autorização são intencionalmente curta duração. Eles devem ser trocados imediatamente por tokens de acesso, tokens de ID ou tokens de atualização quando elas são recebidas. |
