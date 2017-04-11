 <properties
   pageTitle="Referência de Token do Azure AD | Microsoft Azure"
   description="Um guia para entender e avaliar as declarações em tokens SAML 2.0 e JSON Web Tokens (JWT) emitidos por Azure Active Directory (AAD)"
   documentationCenter="na"
   authors="bryanla"
   services="active-directory"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/06/2016"
   ms.author="mbaldwin"/>

# <a name="azure-ad-token-reference"></a>Referência de token Azure AD

Azure Active Directory (AD Azure) emite vários tipos de tokens de segurança no processamento de cada fluxo de autenticação. Este documento descreve o formato, características de segurança e conteúdo de cada tipo de token.

## <a name="types-of-tokens"></a>Tipos de tokens

Azure AD suporta o [protocolo de autorização de OAuth 2.0](active-directory-protocols-oauth-code.md), que usa access_tokens e refresh_tokens.  Ele também dá suporte à autenticação de entrada e via [OpenID conectar](active-directory-protocols-openid-connect-code.md), que apresenta um terceiro tipo de token, o id_token.  Cada um desses tokens é representada como "token de portador".

Um token de portador é um token de segurança leve que concede acesso à "portador" para um recurso protegido. Nesse sentido, "portador" é qualquer pessoa que pode apresentar o token. Embora a autenticação com o Azure AD é necessária para receberem um token de portador, etapas devem ser seguidas para proteger o token, para evitar interceptação por uma pessoa não intencionais. Porque tokens de portador não tem um mecanismo interno para impedir que autorizadas usá-los, eles devem ser transportados em um canal seguro como segurança da camada de transporte (HTTPS). Se um token de portador é transmitido em Limpar, um homem-ataque de interceptação pode ser usado para adquirir o token e acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam ao armazenar ou cache tokens de portador para uso posterior. Sempre certifique-se de que seu aplicativo transmite e armazena tokens de portador de maneira segura. Para mais considerações de segurança em tokens de portador, consulte [RFC 6750 seção 5](http://tools.ietf.org/html/rfc6750).

Muitos dos tokens emitidos por Azure AD são implementados como JSON Web Tokens ou JWTs.  Um JWT é um meio compacto e URL segura de transferir informações entre duas partes.  As informações contidas no JWTs são conhecidos como "declarações" ou declarações de informações sobre o portador e o assunto do token.  As declarações no JWTs são objetos JSON codificados e serializado para transmissão.  Desde que a JWTs emitidos por Azure AD estiver conectados, mas não criptografados, você pode inspecionar facilmente o conteúdo de um JWT para fins de depuração.  Existem várias ferramentas disponíveis para fazê-lo como [jwt.calebb.net](http://jwt.calebb.net). Para obter mais informações sobre JWTs, você pode se referir a [especificação de JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens

Id_tokens são um formulário de entrada do token de segurança que seu aplicativo recebe ao executar a autenticação usando a [Conexão de OpenID](active-directory-protocols-openid-connect-code.md).  Eles são representados como [JWTs](#types-of-tokens)e contêm as declarações que você pode usar para assinar o usuário em seu aplicativo.  Você pode usar as declarações em um id_token como ajustá-la - comumente elas são usadas para exibir informações de conta ou tomar decisões de controle de acesso em um aplicativo.

Id_tokens está conectado, mas não criptografado neste momento.  Quando seu aplicativo recebe um id_token, ele deve [Validar a assinatura](#validating-tokens) para comprovar autenticidade do token e validar algumas declarações no token para comprovar sua validade.  As declarações validadas por um aplicativo variam dependendo de requisitos de cenário, mas há algumas [validações de declaração comuns](#validating-tokens) que seu aplicativo deve executar em cada cenário.

Consulte a seção a seguir para obter informações sobre declarações de id_tokens, bem como um id_token de amostra.  Observe que as declarações no id_tokens não são retornadas em uma ordem específica.  Além disso, novas declarações poderão ser introduzidas no id_tokens em qualquer ponto no tempo - seu aplicativo não quebre conforme novas declarações são introduzidas.  A lista a seguir inclui as declarações que seu aplicativo confiável pode interpretar no momento da redação deste artigo.  Se necessário, ainda mais detalhadas podem ser encontradas na [especificação de OpenID conectar](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Id_token de amostra

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [AZURE.TIP] Prática, tente inspecionar as declarações no id_token a amostra colando-o em [calebb.net](http://jwt.calebb.net).

#### <a name="claims-in-idtokens"></a>Declarações id_tokens

| Declaração JWT | Nome | Descrição |
|-----------|------|-------------|
| `appid`| ID do aplicativo | Identifica o aplicativo que está usando o token para acessar um recurso. O aplicativo pode agir como si mesmo ou em nome de um usuário. A ID de aplicativo geralmente representa um objeto de aplicativo, mas ele também pode representar um objeto de serviço no Azure AD. <br><br> **Valor de JWT de exemplo**: <br> `"appid":"15CB020F-3984-482A-864D-1D92265E8268"` |
| `aud`| Público-alvo | O destinatário pretendido do token. O aplicativo que recebe o token deve verificar se o valor de audiência está correto e rejeitar qualquer tokens destinados para um público diferente. <br><br> **Valor de SAML de exemplo**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Valor de JWT de exemplo**: <br> `"aud":"https://contoso.com"` |
| `appidacr`| Referência de classe de contexto de autenticação de aplicativo | Indica como o cliente foi autenticado. Para um cliente público, o valor é 0. Se o código do cliente e segredo cliente forem usados, o valor será 1. <br><br> **Valor de JWT de exemplo**: <br> `"appidacr": "0"`|
| `acr`| Referência de classe de contexto de autenticação | Indica como o assunto foi autenticado, em vez de cliente na declaração de referência de classe de contexto de autenticação do aplicativo. Um valor de "0" indica que a autenticação de usuário final não atendeu os requisitos da ISO/IEC 29115. <br><br> **Valor de JWT de exemplo**: <br> `"acr": "0"`|
| | Autenticação instantânea | Registra a data e hora quando ocorreu a autenticação. <br><br> **Valor de SAML de exemplo**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` |
| `amr`| Método de autenticação | Identifica como o assunto do token foi autenticado. <br><br> **Valor de SAML de exemplo**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Valor de JWT de exemplo**:`“amr”: ["pwd"]` |
| `given_name`| Nome | Fornece a primeira ou "recebe" nome do usuário, conforme definido no objeto do usuário Azure AD. <br><br> **Valor de SAML de exemplo**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Valor de JWT de exemplo**: <br> `"given_name": "Frank"` |
| `groups`| Grupos | Fornece as IDs de objeto que representam associações de grupo do assunto. Esses valores são exclusivo (consulte a ID de objeto) e podem ser usados com segurança para gerenciar o acesso, como reforçar a autorização para acessar um recurso. Os grupos incluídos a declaração de grupos são configurados em uma base por aplicativo, através da propriedade "groupMembershipClaims" do manifesto do aplicativo. Um valor nulo excluirá todos os grupos, um valor de "SecurityGroup" incluirá somente associações de grupo de segurança do Active Directory e um valor de "Todos" vai incluem grupos de segurança e listas de distribuição do Office 365. <br><br> **Valor de SAML de exemplo**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Valor de JWT de exemplo**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
| `idp` | Provedor de identidade | O provedor de identidade que autenticado o assunto do token de registros. Esse valor é idêntico ao valor da declaração emissor, a menos que a conta de usuário está em um locatário diferente que o emissor. <br><br> **Valor de SAML de exemplo**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Valor de JWT de exemplo**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` | IssuedAt | Armazena a hora em que o token foi emitido. Ele é usado com frequência para medir a atualização do token. <br><br> **Valor de SAML de exemplo**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Valor de JWT de exemplo**: <br> `"iat": 1390234181` |
| `iss` | Emissor | Identifica o serviço de token de segurança (STS) que constrói e retorna o token. Em tokens que retorna do Azure AD, o emissor é sts.windows.net. O GUID no valor de declaração do emissor é a ID de locatário do diretório do Azure AD. A ID de locatário é um identificador imutável e confiável do diretório. <br><br> **Valor de SAML de exemplo**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Valor de JWT de exemplo**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` | Sobrenome | Fornece o último nome, sobrenome ou sobrenome do usuário, conforme definido no objeto do usuário Azure AD. <br><br> **Valor de SAML de exemplo**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Valor de JWT de exemplo**: <br> `"family_name": "Miller"` |
| `unique_name`| Nome | Fornece um valor legível humano que identifica o assunto do token. Esse valor não garantimos que ser exclusivo dentro de um locatário e destina-se a ser usado somente para fins de exibição. <br><br> **Valor de SAML de exemplo**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Valor de JWT de exemplo**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` | ID do objeto | Contém um identificador exclusivo de um objeto no Azure AD. Esse valor é imutável e não pode ser reatribuída ou reutilizada. Use a identificação do objeto para identificar um objeto em consultas ao Azure AD. <br><br> **Valor de SAML de exemplo**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Valor de JWT de exemplo**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` | Funções | Representa todas as funções de aplicativo que o assunto foi concedido direta e indiretamente por meio de membros do grupo e pode ser usado para impor o controle de acesso baseado em função. Funções do aplicativo são definidas em uma base por aplicativo, por meio do `appRoles` propriedade de manifesto do aplicativo. O `value` propriedade de cada função de aplicativo é o valor que aparece na declaração de funções. <br><br> **Valor de SAML de exemplo**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Valor de JWT de exemplo**: <br> `“roles”: ["Admin", … ]` |
| `scp` | Escopo | Indica as permissões de representação concedidas para o aplicativo cliente. A permissão padrão é `user_impersonation`. O proprietário do recurso protegido pode registrar valores adicionais no Azure AD. <br><br> **Valor de JWT de exemplo**: <br> `"scp": "user_impersonation"`|
| `sub` |Assunto| Identifica o capital sobre quais o token declarações informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuída ou reutilizados, portanto ele pode ser usado para executar verificações de autorização com segurança. Como o assunto é sempre presente em tokens os problemas do Azure AD, é recomendável usar esse valor em um sistema de autorização de finalidade geral. <br> `SubjectConfirmation`não é uma declaração. Ele descreve como o assunto do token será verificado. `Bearer`indica que o assunto é confirmado por sua posse do token. <br><br> **Valor de SAML de exemplo**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Valor de JWT de exemplo**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"`|
| `tid` | ID do locatário | Um identificador de imutável, não reutilizável que identifica o locatário de diretório que emitiu o token. Você pode usar esse valor para acessar os recursos de diretório locatário específico em um aplicativo de vários locatário. Por exemplo, você pode usar esse valor para identificar o locatário em uma chamada para a API do gráfico. <br><br> **Valor de SAML de exemplo**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Valor de JWT de exemplo**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"`|
| `nbf`, `exp`|Vida útil do token | Define o intervalo de tempo em que um token é válido. O serviço valida o token deve verificar que a data atual é na vida útil do token, mais que ele deve rejeitar o token. O serviço pode permitir até cinco minutos fora do intervalo de vida útil do token para levar em conta as diferenças na hora do relógio ("hora distorção") entre Azure AD e o serviço. <br><br> **Valor de SAML de exemplo**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Valor de JWT de exemplo**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn`| Nome UPN | Armazena o nome de usuário de capital do usuário.<br><br> **Valor de JWT de exemplo**: <br> `"upn": frankm@contoso.com`|
| `ver`| Versão | Armazena o número de versão do símbolo. <br><br> **Valor de JWT de exemplo**: <br> `"ver": "1.0"`|

## <a name="access-tokens"></a>Tokens de acesso

Tokens de acesso são apenas consumo pelo Microsoft Services neste momento.  Seus aplicativos não deve realizar nenhuma validação ou inspeção de tokens de acesso para qualquer um dos cenários com suporte no momento.  Você pode tratar tokens de acesso como completamente opaco - elas são apenas cadeias de caracteres que seu aplicativo pode passar para a Microsoft em solicitações HTTP.

Quando você solicita um token de acesso, o Azure AD também retorna alguns metadados sobre o token de acesso para consumo do seu aplicativo.  Essas informações incluem o tempo de expiração de token de acesso e os escopos para o qual é válido.  Isso permite que seu aplicativo realizar armazenamento em cache inteligente de tokens de acesso sem precisar analisar abrir o token de acesso em si.

## <a name="refresh-tokens"></a>Atualizar tokens

Atualizar tokens são tokens de segurança, que seu aplicativo pode usar para adquirir novos tokens de acesso em um fluxo de OAuth 2.0.  Ele permite que seu aplicativo obter acesso a longo prazo aos recursos em nome de um usuário sem a necessidade de interação pelo usuário.

Tokens de atualização são vários recursos, significando que eles podem ser recebidos durante uma solicitação de token para um recurso, mas trocados por tokens de acesso para um recurso completamente diferente. Para especificar vários recursos, defina o `resource` parâmetro na solicitação para o recurso de destino.

Atualizar tokens são completamente opacos para o seu aplicativo. Eles são vida longa, mas seu aplicativo não deve ser escrito esperar que um token de atualização irá durar por qualquer período de tempo.  Tokens de atualização podem ser inválidas em qualquer momento para uma variedade de motivos.  A única maneira de seu aplicativo saber se um token de atualização é válido é tentar resgatá-lo fazendo uma solicitação de token para ponto de extremidade de token do Azure AD.

Quando você resgatar um token de atualização para um novo token de acesso, você receberá um novo token de atualização no token de resposta.  Você deve salvar o token de atualização emitidos recentemente, substituindo usado na solicitação.  Isso garante que seus tokens de atualização permaneçam válidos para contanto que possível.

## <a name="validating-tokens"></a>Validando tokens

Neste momento, a validação somente token que seu aplicativo cliente deve precisam para realizar está validando id_tokens.  Para validar um id_token, seu aplicativo deve validar assinatura do id_token e as declarações na id_token.

Fornecemos bibliotecas e exemplos de código que mostram como tratar facilmente validação token, se você quiser entender o processo subjacente.  Também existem várias bibliotecas de terceiros Abrir fonte disponível para validação JWT, pelo menos uma opção para quase todas as plataformas e idioma. Para saber mais sobre bibliotecas de autenticação do Azure AD e exemplos de código, consulte [bibliotecas de autenticação do Azure AD](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>Validando a assinatura

Um JWT contém três segmentos, que são separados pela `.` caracteres.  O primeiro segmento é conhecido como o **cabeçalho**, o segundo como o **corpo**e o terceiro como a **assinatura**.  O segmento de assinatura pode ser usado para validar a autenticidade do id_token para que ele pode ser confiável para seu aplicativo.

Id_Tokens são assinados usando algoritmos de criptografia assimétricos padrão do setor, como RSA 256. O cabeçalho do id_token contém informações sobre o método de criptografia e chave usada para assinar o token:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

O `alg` declaração indica o algoritmo que foi usado para assinar o token, enquanto o `x5t` declaração indica a chave pública específica que foi usada para assinar o token.

Em qualquer dado momento, o Azure AD pode assinar um id_token usando qualquer uma de um determinado conjunto de pares de chaves pública-particular. Azure AD gira o conjunto possível de chaves periodicamente, para que seu aplicativo deve ser escrito para lidar com essas alterações chaves automaticamente.  Uma frequência razoável para verificar se há atualizações para as chaves públicas usadas pelo Azure AD é a cada 24 horas.

Você pode adquirir os dados da chave autenticação necessários para validar a assinatura usando o documento de metadados OpenID conectar localizado em:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [AZURE.TIP] Experimente esta URL em um navegador!

Este documento de metadados é um objeto JSON que contém várias partes úteis de informações, como o local dos vários pontos de extremidade necessários para executar a autenticação OpenID se conectar.  

Ele também inclui uma `jwks_uri`, que oferece o local do conjunto de chaves públicas usados para assinar tokens.  O documento JSON localizado na `jwks_uri` contém todas as informações de chave públicas em uso nesse momento específico.  Seu aplicativo pode usar o `kid` reivindicar no cabeçalho JWT para selecionar quais chave pública neste documento foi usado para assinar um token específico.  Ele pode realizar validação de assinatura usando a chave pública correta e o algoritmo indicado.

Executar a validação de assinatura está fora do escopo deste documento, há muitas bibliotecas de fonte aberta disponíveis para ajudá-lo a fazer isso se for necessário.

#### <a name="validating-the-claims"></a>Validando as declarações

Quando seu aplicativo recebe um id_token durante a entrada do usuário, ele também deve executar algumas verificações contra as declarações a id_token.  Esses incluem, mas não estão limitados a:

  - A declaração de **audiência** - verificar que o id_token foi destinado a ser dada ao seu aplicativo.
  - As declarações **Não antes** e a **Hora de expiração** - para verificar se o id_token não expirou.
  - A declaração de **emissor** - para verificar se o token foi emitido realmente para o seu aplicativo por Azure AD.
  - O **valor de uso único** - a reduzir um ataque de repetição token.
  - e muito mais...

Para obter uma lista completa de validações de declaração que seu aplicativo deve executar, consulte a [especificação de OpenID conectar](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Detalhes dos valores esperados para essas declarações são incluídos na seção [id_token seção](#id-tokens) anterior.

## <a name="sample-tokens"></a>Tokens de amostra

Esta seção exibe exemplos de tokens SAML e JWT que retorna do Azure AD. Esses exemplos permitem que você veja as declarações no contexto.
Token SAML

Este é um exemplo de um típico token SAML.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>Símbolo JWT - representação de usuário

Este é um exemplo de um típico token de web do JSON (JWT), usado em um fluxo de concessão de código de autorização.
Além de declarações, o token inclui um número de versão em **ver** e **appidacr**, a referência de classe de contexto de autenticação, que indica como o cliente foi autenticado. Para um cliente público, o valor é 0. Se uma ID do cliente ou segredo cliente foi usado, o valor é 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>Conteúdo relacionado
- Consulte as [operações de política](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) do Azure AD Graph e a [entidade de política](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity), para saber mais sobre o gerenciamento de política de vida útil do token por meio da API do Azure AD Graph.
- Para obter mais informações e exemplos sobre como gerenciar políticas via cmdlets do PowerShell, incluindo exemplos, consulte [configuráveis tempos de vida de token no Azure AD](active-directory-configurable-token-lifetimes.md). 
