<properties
    pageTitle="Referência token do Azure AD v 2.0 | Microsoft Azure"
    description="Os tipos de tokens e declarações emitidas por uma empresa v 2.0"
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

# <a name="v20-token-reference"></a>referência de token de v 2.0

O ponto de extremidade de v 2.0 emite vários tipos de tokens de segurança no processamento de cada [fluxo de autenticação](active-directory-v2-flows.md). Este documento descreve o formato, características de segurança e conteúdo de cada tipo de token.

> [AZURE.NOTE]
    Nem todos os cenários do Active Directory do Azure e recursos são compatíveis com o ponto de extremidade de v 2.0.  Para determinar se você deve usar o ponto de extremidade de v 2.0, leia sobre as [limitações da versão 2.0](active-directory-v2-limitations.md).

## <a name="types-of-tokens"></a>Tipos de tokens

O ponto de extremidade de v 2.0 suporta o [protocolo de autorização de OAuth 2.0](active-directory-v2-protocols.md), que usa access_tokens e refresh_tokens.  Ele também dá suporte à autenticação de entrada e via [OpenID conectar](active-directory-v2-protocols.md#openid-connect-sign-in-flow), que apresenta um terceiro tipo de token, o id_token.  Cada um desses tokens é representada como "token de portador".

Um token de portador é um token de segurança leve que concede acesso à "portador" para um recurso protegido. Nesse sentido, "portador" é qualquer pessoa que pode apresentar o token. Embora uma festa deve primeiro autenticar com o Azure AD para receber o token de portador, se as etapas necessárias não são levadas para proteger o token de transmissão e armazenamento, podem ser interceptado e usado por uma pessoa não intencionais. Enquanto alguns tokens de segurança tem um mecanismo interno para impedir que autorizadas usá-los, tokens de portador não tem esse mecanismo e devem ser transportados em um canal seguro como segurança da camada de transporte (HTTPS). Se um token de portador é transmitido em Limpar, um homem-ataque de interceptação pode ser usado por um terceiro mal-intencionado adquirir o token e usá-lo para um acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam ao armazenar ou cache tokens de portador para uso posterior. Sempre certifique-se de que seu aplicativo transmite e armazena tokens de portador de maneira segura. Para mais considerações de segurança em tokens de portador, consulte [RFC 6750 seção 5](http://tools.ietf.org/html/rfc6750).

Muitos dos tokens emitidos pelo ponto de extremidade v 2.0 são implementados como Json Web Tokens ou JWTs.  Um JWT é um meio compacto e confiável URL de transferência de informações entre duas partes.  As informações contidas no JWTs são conhecidos como "declarações" ou declarações de informações sobre o portador e o assunto do token.  As declarações no JWTs são objetos JSON codificados e serializado para transmissão.  Desde que a JWTs emitidos pelo ponto de extremidade de v 2.0 conectados, mas não criptografados, você pode inspecionar facilmente o conteúdo de um JWT para fins de depuração. Para obter mais informações sobre JWTs, você pode se referir a [especificação de JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens

Id_tokens são um formulário de entrada do token de segurança que seu aplicativo recebe ao executar a autenticação usando a [Conexão de OpenID](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  Eles são representados como [JWTs](#types-of-tokens)e contêm as declarações que você pode usar para assinar o usuário em seu aplicativo.  Você pode usar as declarações em um id_token como ajustá-la - comumente elas são usadas para exibir informações de conta ou tomar decisões de controle de acesso em um aplicativo.  O ponto de extremidade de v 2.0 emite apenas um tipo de id_token, que tem um conjunto consistente de declarações, independentemente do tipo de usuário que tenha entrado em.  Que é dizer que o formato e conteúdo do id_tokens será o mesmo para ambos os usuários de Microsoft Account pessoal e contas de trabalho ou da escola.

Id_tokens está conectado, mas não criptografado neste momento.  Quando seu aplicativo recebe um id_token, ele deve [Validar a assinatura](#validating-tokens) para comprovar autenticidade do token e validar algumas declarações no token para comprovar sua validade.  As declarações validadas por um aplicativo variam dependendo de requisitos de cenário, mas há algumas [validações de declaração comuns](#validating-tokens) que seu aplicativo deve executar em cada cenário.

Detalhes completos sobre as declarações no id_tokens são apresentados abaixo, bem como um id_token de amostra.  Observe que as declarações no id_tokens não são retornadas em uma ordem específica.  Além disso, novas declarações poderão ser introduzidas no id_tokens em qualquer ponto no tempo - seu aplicativo não quebre conforme novas declarações são introduzidas.  A lista a seguir inclui as declarações que seu aplicativo confiável pode interpretar no momento da redação deste artigo.  Se necessário, ainda mais detalhadas podem ser encontradas na [especificação de OpenID conectar](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Id_token de amostra

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [AZURE.TIP] Prática, tente inspecionar as declarações no id_token a amostra colando-o em [calebb.net](https://calebb.net).

#### <a name="claims-in-idtokens"></a>Declarações id_tokens
| Nome | Declaração | Valor de exemplo | Descrição |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Público-alvo | `aud` | `6731de76-14a6-49ae-97bc-6eba6914391e` | Identifica o destinatário pretendido do token.  Em id_tokens, a audiência é Id de aplicativo do seu aplicativo, como atribuído para o seu aplicativo no portal de registro do aplicativo.  Seu aplicativo deve validar esse valor e rejeitar o token se ele não corresponder. |
| Emissor | `iss` | `https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` | Identifica o serviço de token de segurança (STS) que constrói e retorna o token, bem como o locatário do Azure AD em que o usuário foi autenticado.  Seu aplicativo deve validar a declaração de emissor para garantir que o token veio do ponto de extremidade v 2.0.  Ele também deve usar a parte de guid da declaração para restringir o conjunto de locatários que têm permissão para entrar no aplicativo.  O guid que indica o usuário é um usuário do consumidor da Microsoft é conta `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| Emitido em | `iat` | `1452285331` | A hora em que o token foi emitido, representado no tempo de época. |
| Tempo de expiração | `exp` | `1452289231` | A hora em que o token se torna inválido, representado no tempo de época.  Seu aplicativo deve usar esta declaração para verificar a validade da vida útil do token.  |
| Não antes | `nbf` | `1452285331` |  A hora em que o token se torna válido, representado no tempo de época. Geralmente é a mesma que a hora de emissão.  Seu aplicativo deve usar esta declaração para verificar a validade da vida útil do token.  |
| Versão | `ver` | `2.0` | A versão do id_token, conforme definido pelo Azure AD.  O ponto de extremidade de v 2.0, o valor será `2.0`. |
| Id do locatário | `tid` | `b9419818-09af-49c2-b0c3-653adc1f376e` | Um guid que representa o Azure AD locatário que o usuário foi enviada.  Para contas de trabalho e escola, o guid será a identificação de locatário imutável da organização que o usuário pertence.  Para contas pessoais, o valor será `9188040d-6c67-4c5b-b112-36a304b66dad`.  O `profile` escopo é necessária para receberem essa declaração. |
| Hash de código | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | O hash de código será incluído no id_tokens somente quando o id_token é emitida junto com um código de autorização OAuth 2.0.  Ele pode ser usado para validar a autenticidade de um código de autorização.  Consulte a [especificação de OpenID conectar-se](http://openid.net/specs/openid-connect-core-1_0.html) para obter mais detalhes sobre como realizar essa validação. |
| Hash de Token de acesso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | O hash de token de acesso será incluído no id_tokens somente quando o id_token é emitida junto com um token de acesso OAuth 2.0.  Ele pode ser usado para validar a autenticidade de um token de acesso.  Consulte a [especificação de OpenID conectar-se](http://openid.net/specs/openid-connect-core-1_0.html) para obter mais detalhes sobre como realizar essa validação. |
| Valor de uso único | `nonce` | `12345` | O valor de uso único é uma estratégia para atenuar ataques de repetição de token.  Seu aplicativo pode especificar um valor de uso único em uma solicitação de autorização usando o `nonce` parâmetro de consulta.  O valor que você fornecer na solicitação será emitido na id_token `nonce` declaração, inalterada.  Isso permite que seu aplicativo verificar o valor com relação ao valor que ele especificado na solicitação, que associa a sessão do aplicativo com um determinado id_token.  Seu aplicativo deve executar este validação durante o processo de validação id_token. |
| Nome | `name` | `Babe Ruth` | A declaração de nome fornece um valor legível humano que identifica o assunto do token. Este valor não garantimos que ser exclusivo, é mutável e destina-se a ser usado somente para fins de exibição.  O `profile` escopo é necessária para receberem essa declaração. |
| Email | `email` | `thegreatbambino@nyy.onmicrosoft.com` | O endereço de email principal associado à conta de usuário, se houver uma.  Seu valor é mutável e pode mudar para um determinado usuário ao longo do tempo.  O `email` escopo é necessária para receberem essa declaração. |
| Nome de usuário preferencial | `preferred_username` | `thegreatbambino@nyy.onmicrosoft.com` | O nome de usuário principal que é usado para representar o usuário no ponto de extremidade v 2.0.  Ele pode ser um endereço de email, número de telefone ou um nome de usuário genérico sem um formato especificado.  Seu valor é mutável e pode mudar para um determinado usuário ao longo do tempo.  O `profile` escopo é necessária para receberem essa declaração. |
| Assunto | `sub` | `MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | O capital sobre quais o token declarações informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuída ou reutilizados, portanto pode ser usado para executar verificações de autorização com segurança, como quando o token é usado para acessar um recurso. Como o assunto é sempre presente em tokens os problemas do Azure AD, é recomendável usar esse valor em um sistema de autorização de finalidade geral. |
| ID do objeto | `oid` | `a1dbdde8-e4f9-4571-ad93-3059e3750d23` | O objeto Id da conta de trabalho ou da escola no sistema Azure AD.  Esta declaração não será emitida para contas pessoais do Microsoft.  O `profile` escopo é necessária para receberem essa declaração. |


## <a name="access-tokens"></a>Tokens de acesso

Os tokens de acesso emitidos pelo ponto de extremidade de v 2.0 só estão consumo pelo Microsoft Services neste momento.  Seus aplicativos não deve realizar nenhuma validação ou inspeção de tokens de acesso para qualquer um dos cenários com suporte no momento.  Você pode tratar tokens de acesso como completamente opaco - elas são apenas cadeias de caracteres que seu aplicativo pode passar para a Microsoft em solicitações HTTP.

Em breve, o ponto de extremidade de v 2.0 apresentará a capacidade de seu aplicativo receber tokens de acesso de outros clientes.  Nesse momento, essa informação será atualizada com as informações do que seu aplicativo precisa executar validação de token de acesso e outras tarefas semelhantes.

Quando você solicita um token de acesso do ponto de extremidade v 2.0, o ponto de extremidade de v 2.0 também retorna alguns metadados sobre o token de acesso para consumo do seu aplicativo.  Essas informações incluem o tempo de expiração de token de acesso e os escopos para o qual é válido.  Isso permite que seu aplicativo realizar armazenamento em cache inteligente de tokens de acesso sem precisar analisar abrir o token de acesso em si.

## <a name="refresh-tokens"></a>Atualizar tokens

Atualizar tokens são tokens de segurança que seu aplicativo pode usar para adquirir novas acessar tokens em um fluxo de OAuth 2.0.  Ele permite que seu aplicativo obter acesso a longo prazo aos recursos em nome de um usuário sem a necessidade de interação pelo usuário.

Atualizar tokens são vários recursos.  Isto é dizer que um token de atualização recebido durante uma solicitação de token para um recurso pode ser trocados por tokens de acesso para um recurso completamente diferente.

Para receberem uma atualização em uma resposta de token, seu aplicativo deve solicitar & ser concedido a `offline_acesss` escopo.   Para saber mais sobre o `offline_access` escopo, consulte o [artigo consentimento & escopos aqui](active-directory-v2-scopes.md).

Atualizar tokens são e sempre será, completamente opaco para o seu aplicativo.  Eles são emitidos pelo ponto de extremidade de v 2.0 Azure AD e podem apenas ser inspecionados e interpretados pelo ponto de extremidade v 2.0.  Eles são vida longa, mas seu aplicativo não deve ser escrito esperar que um token de atualização irá durar por qualquer período de tempo.  Tokens de atualização podem ser inválidas em qualquer momento para uma variedade de motivos.  A única maneira de seu aplicativo saber se um token de atualização é válido é tentar resgatá-lo fazendo uma solicitação de token para o ponto de extremidade de v 2.0.

Quando você resgatar um token de atualização para um novo token de acesso (e se o seu aplicativo foi concedido a `offline_access` escopo), você receberá um novo token de atualização no token de resposta.  Você deve salvar o token de atualização emitidos recentemente, substituindo usado na solicitação.  Isso garante que seus tokens de atualização permaneçam válidos para contanto que possível.

## <a name="validating-tokens"></a>Validando tokens

Neste momento, a validação de token somente que seus aplicativos devem precisam para realizar está validando id_tokens.  Para validar um id_token, seu aplicativo deve validar assinatura do id_token e as declarações na id_token.

<!-- TODO: Link -->
Fornecemos bibliotecas e exemplos de código que mostram como tratar facilmente validação token - as informações abaixo simplesmente for fornecido para aqueles que desejam entender o processo subjacente.  Também há várias bibliotecas de abrir origem de festa 3º disponível para validação de JWT - há pelo menos uma opção para quase todos os idiomas por aí & plataforma.

#### <a name="validating-the-signature"></a>Validando a assinatura
Um JWT contém três segmentos, que são separados pela `.` caracteres.  O primeiro segmento é conhecido como o **cabeçalho**, o segundo como o **corpo**e o terceiro como a **assinatura**.  O segmento de assinatura pode ser usado para validar a autenticidade do id_token para que ele pode ser confiável para seu aplicativo.

Id_Tokens são assinados usando algoritmos de criptografia assimétricos padrão do setor, como RSA 256. O cabeçalho do id_token contém informações sobre o método de criptografia e chave usada para assinar o token:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

O `alg` declaração indica o algoritmo que foi usado para assinar o token, enquanto o `kid` declaração indica a chave pública específica que foi usada para assinar o token.

Em qualquer dado momento, o ponto de extremidade de v 2.0 pode assinar um id_token usando qualquer uma de um determinado conjunto de pares de chaves pública-particular.  O ponto de extremidade de v 2.0 gira o conjunto possível de chaves periodicamente, para que seu aplicativo deve ser escrito para lidar com essas alterações chaves automaticamente.  Uma frequência razoável para verificar se há atualizações para as chaves públicas usadas pelo ponto de extremidade v 2.0 é cerca de 24 horas.

Você pode adquirir os dados da chave autenticação necessários para validar a assinatura usando o documento de metadados OpenID conectar localizado em:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [AZURE.TIP] Experimente esta URL em um navegador!

Este documento de metadados é um objeto JSON que contém várias partes úteis de informações, como o local dos vários pontos de extremidade necessários para executar a autenticação OpenID se conectar.  

Ele também inclui uma `jwks_uri`, que oferece o local do conjunto de chaves públicas usados para assinar tokens.  O documento JSON localizado na `jwks_uri` contém todas as informações de chave públicas em uso nesse momento específico.  Seu aplicativo pode usar o `kid` reivindicar no cabeçalho JWT para selecionar quais chave pública neste documento foi usado para assinar um token específico.  Ele pode realizar validação de assinatura usando a chave pública correta e o algoritmo indicado.

Executar a validação de assinatura está fora do escopo deste documento, há muitas bibliotecas de fonte aberta disponíveis para ajudá-lo a fazer isso se for necessário.

#### <a name="validating-the-claims"></a>Validando as declarações
Quando seu aplicativo recebe um id_token durante a entrada do usuário, ele também deve executar algumas verificações contra as declarações a id_token.  Esses incluem, mas não estão limitados a:

- A declaração de **audiência** - verificar que o id_token foi destinado a ser dada ao seu aplicativo.
- As declarações **Não antes** e a **Hora de expiração** - para verificar se o id_token não expirou.
- A declaração de **emissor** - para verificar se o token foi emitido realmente para o seu aplicativo pelo ponto de extremidade v 2.0.
- O **valor de uso único** - como um fator atenuante ataque de repetição token.
- e muito mais...

Para obter uma lista completa das validações de declaração que seu aplicativo deve executar, consulte a [especificação de OpenID conectar](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Detalhes dos valores esperados para essas declarações são incluídos acima na [seção id_token](#id_tokens).


## <a name="token-lifetimes"></a>Vida útil de token

Os seguintes tempos de vida de token são fornecidos puramente para sua compreensão, como eles podem ajudar na desenvolvimento e depuração de aplicativos.  Seus aplicativos não devem ser escritos esperar qualquer uma destas vidas permaneçam constantes - podem e será alterado em qualquer ponto no tempo.

| Token | Vida útil | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| Id_Tokens (contas corporativas ou de estudante) | 1 hora | Id_Tokens são normalmente válidas por uma hora.  O aplicativo web pode usar esse mesmo tempo de vida em manter sua própria sessão com o usuário (recomendado) ou escolha um tempo de vida de sessão completamente diferente.  Se seu aplicativo precisa obter um novo id_token, ele simplesmente precisa fazer com que uma nova solicitação entrar para o v 2.0 autorizar o ponto de extremidade.  Se o usuário tiver uma sessão de navegador válido com o ponto de extremidade de v 2.0, elas não podem ser necessárias para inserir suas credenciais novamente. |
| Id_Tokens (contas pessoais) | 24 horas | Id_Tokens para contas pessoais são normalmente válidas por 24 horas.  O aplicativo web pode usar esse mesmo tempo de vida em manter sua própria sessão com o usuário (recomendado) ou escolha um tempo de vida de sessão completamente diferente.  Se seu aplicativo precisa obter um novo id_token, ele simplesmente precisa fazer com que uma nova solicitação entrar para o v 2.0 autorizar o ponto de extremidade.  Se o usuário tiver uma sessão de navegador válido com o ponto de extremidade de v 2.0, elas não podem ser necessárias para inserir suas credenciais novamente. |
| Tokens de acesso (contas corporativas ou de estudante) | 1 hora | Indicado em respostas token como parte dos metadados do token. |
| Tokens de acesso (contas pessoais) | 1 hora | Indicado em respostas token como parte dos metadados do token.  Access_tokens emitidos em nome de contas pessoais podem ser configuradas para um tempo de vida diferentes, mas uma hora é geralmente o caso. |
| Atualizar Tokens (conta corporativa ou de estudante) | Até 14 dias | Um token de atualização único é válido para um máximo de 14 dias.  No entanto, o token de atualização pode se tornar inválido a qualquer momento por vários motivos, para que seu aplicativo deve continuar a experimentar e use um token de atualização até que ele falha ou seu aplicativo substitui por um novo token de atualização.  Um token de atualização também tornarão inválido se ele tiver sido 90 dias desde que o usuário inseriu suas credenciais. |
| Atualizar Tokens (contas pessoais) | Até 1 ano | Um token de atualização único é válido para um máximo de 1 ano.  No entanto, o token de atualização pode se tornar inválido a qualquer momento por vários motivos, para que seu aplicativo deve continuar a experimentar e use um token de atualização até falhar. |
| Códigos de autorização (contas corporativas ou de estudante) | 10 minutos | Códigos de autorização são intencionalmente curta duração e devem ser imediatamente trocados por access_tokens e refresh_tokens quando elas são recebidas. |
| Códigos de autorização (contas pessoais) | 5 minutos | Códigos de autorização são intencionalmente curta duração e devem ser imediatamente trocados por access_tokens e refresh_tokens quando elas são recebidas.  Códigos de autorização emitidos em nome de contas pessoais também são uso único. |
