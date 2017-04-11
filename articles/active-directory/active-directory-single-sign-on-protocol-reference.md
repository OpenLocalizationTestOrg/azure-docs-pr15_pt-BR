<properties
    pageTitle="Azure logon único protocolo SAML | Microsoft Azure"
    description="Este artigo descreve o protocolo de logon único em SAML no Active Directory do Azure"
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

# <a name="single-sign-on-saml-protocol"></a>Protocolo SAML logon único

Este artigo aborda as solicitações de autenticação de SAML 2.0 e respostas que Azure Active Directory (AD Azure) suporte para Single Sign-On.

O diagrama de protocolo abaixo descreve a sequência de logon única. O serviço de nuvem (o provedor de serviço) usa uma associação de redirecionamento HTTP para passar um `AuthnRequest` elemento (solicitação de autenticação) para Azure AD (o provedor de identidade). Azure AD depois usa um HTTP post encadernação postar um `Response` elemento ao serviço de nuvem.

![Fluxo de trabalho de logon único](media/active-directory-single-sign-on-protocol-reference/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

Para solicitar uma autenticação do usuário, envio de serviços de nuvem uma `AuthnRequest` elemento Azure AD. Uma amostra SAML 2.0 `AuthnRequest` poderia ter esta aparência:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```


| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| ID | Necessário | Azure AD usa esse atributo para preencher a `InResponseTo` atributo da resposta retornado. ID não deve começar com um número, portanto, uma estratégia comum é preceder uma cadeia de caracteres como "id" para a representação de cadeia de caracteres de um GUID. Por exemplo, `id6c1c178c166d486687be4aaf5e482730` é uma ID válida. |
| Versão | Necessário | Isso deve ser **2.0**.|
| IssueInstant | Necessário | Isso é uma cadeia de caracteres com um [formato de ida e volta ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx)e o valor de UTC DateTime. Azure AD espera um valor DateTime desse tipo, mas não avaliar ou use o valor. |
| AssertionConsumerServiceUrl | opcional | Se fornecido, este deve corresponder a `RedirectUri` ao serviço de nuvem do Azure AD. |
| ForceAuthn | opcional | Se fornecido, isso deve ser false. Qualquer outro valor causará um erro.|
| IsPassive | opcional | Se fornecido, isso deve ser false. Qualquer outro valor causará um erro. |  

Todos os outros `AuthnRequest` atributos, como consentimento, destino, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex e ProviderName são **ignorados**.

Azure AD também ignora o `Conditions` elemento em `AuthnRequest`.

### <a name="issuer"></a>Emissor

O `Issuer` elemento de um `AuthnRequest` deve corresponder exatamente a uma da **ServicePrincipalNames** no serviço de nuvem no Azure AD. Normalmente, isso é definido para o **URI de ID de aplicativo** especificado durante o registro do aplicativo.

Um exemplo SAML trecho contendo o `Issuer` elemento tem esta aparência:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Este elemento solicita um formato de ID de nome específico na resposta e é opcional em `AuthnRequest` elementos enviados ao Azure AD.

Uma amostra `NameIdPolicy` elemento tem esta aparência:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Se `NameIDPolicy` for fornecido, você pode incluir seu opcional `Format` atributo. O `Format` atributo só pode ter um dos seguintes valores; qualquer outro valor resulta em um erro.

-  `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory emite a declaração NameID como um identificador par.
- `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory emite a declaração NameID no formato de endereço de email.
- `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Esse valor permite Azure Active Directory para selecionar o formato de declaração. Active Directory do Azure emite o NameID como um identificador par.

Não inclua a `SPNameQualifer` atributo. Azure AD ignora o `AllowCreate` atributo.

### <a name="requestauthncontext"></a>RequestAuthnContext

O `RequestedAuthnContext` elemento Especifica os métodos de autenticação desejado. É opcional no `AuthnRequest` elementos enviados ao Azure AD. Azure AD dá suporte a apenas um `AuthnContextClassRef` valor: `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Escopo

O `Scoping` elemento, que inclui uma lista de provedores de identidade, é opcional em `AuthnRequest` elementos enviados ao Azure AD.

Se fornecido, não inclua a `ProxyCount` atributo, `IDPListOption` ou `RequesterID` elemento, como eles não são suportados.

### <a name="signature"></a>Assinatura

Não inclua um `Signature` elemento no `AuthnRequest` elementos, como Azure AD não suporta assinado solicitações de autenticação.

### <a name="subject"></a>Assunto

Azure AD ignora o `Subject` elemento do `AuthnRequest` elementos.

## <a name="response"></a>Resposta

Quando um logon solicitado é concluída com êxito, o Azure AD postagens uma resposta para o serviço de nuvem. Uma resposta de amostra para uma tentativa de logon bem-sucedida tem esta aparência:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Resposta

O `Response` elemento inclui o resultado da solicitação de autorização. Azure AD define o `ID`, `Version` e `IssueInstant` valores na `Response` elemento. Ele também define os seguintes atributos:

- `Destination`: Quando o logon for concluído com êxito, isso é definido para o `RedirectUri` do provedor de serviços (serviço de nuvem).
- `InResponseTo`: Isso é definido para o `ID` atributo do `AuthnRequest` elemento que iniciou a resposta.

### <a name="issuer"></a>Emissor

Azure AD define o `Issuer` elemento `https://login.microsoftonline.com/<TenantIDGUID>/` onde <TenantIDGUID> é a ID de locatário do locatário Azure AD.

Por exemplo, uma resposta de exemplo com elemento emissor poderia esta aparência:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Status

O `Status` transmitidas elemento o sucesso ou falha de logon. Ele inclui a `StatusCode` elemento, que contém um código ou um conjunto de códigos aninhados que representam o status da solicitação. Ele também inclui a `StatusMessage` elemento, que contém mensagens de erro personalizadas que são geradas durante o processo de logon.

<!-- TODO: Add a authentication protocol error reference -->

A seguir é uma resposta SAML até uma tentativa de logon malsucedida.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Declaração

Além do `ID`, `IssueInstant` e `Version`, Azure AD define os seguintes elementos na `Assertion` elemento da resposta.

#### <a name="issuer"></a>Emissor

Isso é definido para `https://sts.windows.net/<TenantIDGUID>/`onde <TenantIDGUID> é a ID de locatário do locatário Azure AD.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Assinatura

Azure AD assina a declaração em resposta a um logon bem-sucedido. O `Signature` elemento contém uma assinatura digital que o serviço de nuvem pode usar para autenticar a fonte para verificar a integridade da declaração.

Para gerar essa assinatura digital, o Azure AD usa a chave de assinatura no `IDPSSODescriptor` elemento do seu documento de metadados.

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Assunto

Especifica o objeto que é o assunto das instruções na declaração. Ela contém um `NameID` elemento, que representa o usuário autenticado. O `NameID` valor é um identificador de destino que é direcionado somente para o provedor de serviços que o público para o token. Ele é persistente - ele pode ser revogado, mas nunca é reatribuída. Também é opaca, em que ele não revela nada sobre o usuário e não pode ser usado como um identificador para consultas de atributo.

O `Method` atributo do `SubjectConfirmation` elemento é sempre definido como `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Condições

Este elemento Especifica as condições que definem o uso aceitável de declarações de SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

O `NotBefore` e `NotOnOrAfter` atributos especificam o intervalo durante o qual a declaração é válida.

- O valor da `NotBefore` atributo é igual a ou ligeiramente (menos de um segundo) mais tarde do que o valor de `IssueInstant` atributo do `Assertion` elemento. Azure AD não conta para qualquer diferença de horário entre si mesmo e o serviço de nuvem (provedor de serviços) e não adicionará qualquer buffer neste momento.
- O valor da `NotOnOrAfter` atributo é 70 minutos depois que o valor da `NotBefore` atributo.

#### <a name="audience"></a>Público-alvo

Esta página contém uma URI que identifica um público-alvo. Azure AD define o valor desse elemento como o valor de `Issuer` elemento do `AuthnRequest` que iniciou o logon. Para avaliar a `Audience` valor, use o valor da `App ID URI` que foi especificada durante o registro do aplicativo.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Como o `Issuer` valor, o `Audience` valor deve corresponder exatamente a um dos nomes principais de serviço que representa o serviço de nuvem no Azure AD. No entanto, se o valor da `Issuer` elemento não é um valor URI, o `Audience` valor na resposta é o `Issuer` valor o prefixo `spn:`.

#### <a name="attributestatement"></a>AttributeStatement

Esta página contém declarações sobre o assunto ou usuário. O trecho a seguir contém uma amostra `AttributeStatement` elemento. Nas reticências indica que o elemento pode incluir vários atributos e seus valores.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```     

- **Declaração de nome** : O valor da `Name` atributo (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) é o UPN do usuário autenticado, como `testuser@managedtenant.com`.
- **Declaração de ObjectIdentifier** : O valor da `ObjectIdentifier` atributo (`http://schemas.microsoft.com/identity/claims/objectidentifier`) é o `ObjectId` do objeto de diretório que representa o usuário autenticado no Azure AD. `ObjectId`é um imutável, globalmente exclusivo e usar novamente o identificador de segurança do usuário autenticado.

#### <a name="authnstatement"></a>AuthnStatement

Este elemento declarações que o assunto de declaração foi autenticado por um meio específico em um determinado momento.

- O `AuthnInstant` atributo especifica a hora em que o usuário autenticado com o Azure AD.
- O `AuthnContext` elemento Especifica o contexto de autenticação usado para autenticar o usuário.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
