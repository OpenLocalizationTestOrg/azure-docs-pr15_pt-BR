<properties
    pageTitle="Metadados de Federação do AD Azure | Microsoft Azure"
    description="Este artigo descreve o documento de metadados de Federação do Active Directory do Azure publica para serviços que aceitam tokens do Active Directory do Azure."
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


# <a name="federation-metadata"></a>Metadados de Federação

Azure Active Directory (AD Azure) publica um documento de metadados de federação para serviços que configurados para aceitar os tokens de segurança que emite Azure AD. O formato de documento de metadados de Federação é descrito na [Web Services Federation Language (Web Services Federation) versão 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), que estende [metadados para o v 2.0 OASIS segurança declaração SAML Markup Language ()](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Específicas do locatário e pontos de extremidade de metadados de locatário independente

Azure AD publica pontos de extremidade de locatário específicas e locatário independente.

Pontos de extremidade de locatário específicas destinam-se um determinado locatário. Os metadados de Federação específicas do locatário incluem informações sobre o locatário, incluindo informações específicas do locatário de emissor e o ponto de extremidade. Aplicativos que restringem o acesso a um único locatário usar pontos de extremidade de locatário específicas.

Pontos de extremidade de locatário independente fornecem informações comuns a todos os Azure AD locatários. Essa informação aplica-se ao locatários hospedados em *login.microsoftonline.com* e é compartilhada entre locatários. Pontos de extremidade de locatário independente são recomendados para aplicativos de vários locatários, desde que eles não estão associados a qualquer locatário específico.

## <a name="federation-metadata-endpoints"></a>Pontos de extremidade de metadados de Federação

Azure AD publica os metadados de Federação em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

Para **pontos de extremidade de locatário específico**, o `TenantDomainName` pode ser um dos seguintes tipos:

- Um nome de domínio registrado de um anúncio Azure locatário, tais como: `contoso.onmicrosoft.com`.
- O imutável locatário ID do domínio, como `72f988bf-86f1-41af-91ab-2d7cd011db45`.

Para pontos de **extremidade de locatário independente**, o `TenantDomainName` é `common`. Este documento lista apenas os elementos de metadados de federação que são comuns a todos os locatários Azure AD hospedadas em login.microsoftonline.com.

Por exemplo, um ponto de extremidade do locatário específicas pode ser `https:// login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. O ponto de extremidade do locatário independente é [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Você pode exibir o documento de metadados de Federação digitando esta URL em um navegador.

## <a name="contents-of-federation-metadata"></a>Conteúdo da federação metadados

A seção a seguir fornece informações necessárias para os serviços que consumir os tokens emitidos por Azure AD.

### <a name="entity-id"></a>ID da entidade

O `EntityDescriptor` elemento contém um `EntityID` atributo. O valor da `EntityID` atributo representa o emissor, ou seja, o serviço de token segurança (STS) que emitiu o token. É importante validar o emissor quando você recebe um token.

Os metadados a seguir mostra um exemplo específicas do locatário `EntityDescriptor` elemento com um `EntityID` elemento.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Você pode substituir a ID de locatários no ponto de extremidade do locatário independente com sua ID de locatário para criar uma locatário específicas `EntityID` valor. O valor resultante será o mesmo que o emissor do token. A estratégia permite que um aplicativo de vários locatário validar o emissor para um determinado inquilino.

Os metadados a seguir mostra um exemplo locatário independente `EntityID` elemento. Observe que o `{tenant}` é um literal, não um espaço reservado.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certificados de assinatura de token

Quando um serviço recebe um token emitido por um locatário do Azure AD, a assinatura do token deve ser validada com uma chave de assinatura é publicada no documento de metadados de Federação. Os metadados de Federação incluem a parte pública dos certificados que os locatários usam para autenticação de token. Os bytes bruto certificado aparecem no `KeyDescriptor` elemento. O certificado de autenticação de token é válido para assinatura somente quando o valor da `use` atributo é `signing`.

Um documento de metadados de Federação publicado por Azure AD pode ter várias chaves de assinatura, como quando Azure AD está se preparando para atualizar o certificado de assinatura. Quando um documento de metadados de Federação inclui mais de um certificado, um serviço que está validando os tokens deve oferecer suporte a todos os certificados no documento.

Os metadados a seguir mostra um exemplo `KeyDescriptor` elemento com uma chave de assinatura.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

O `KeyDescriptor` elemento aparece em dois lugares do documento de metadados de Federação; na seção WS federação-específicas e a seção SAML específicas. Os certificados publicados em ambas as seções serão iguais.

Na seção WS federação-específicas, um leitor de metadados de Web Services Federation seria ler os certificados de um `RoleDescriptor` elemento com o `SecurityTokenServiceType` tipo.

Os metadados a seguir mostra um exemplo `RoleDescriptor` elemento.

```
<RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">
```

Na seção SAML específicas, um leitor de metadados de Web Services Federation seria ler os certificados de um `IDPSSODescriptor` elemento.

Os metadados a seguir mostra um exemplo `IDPSSODescriptor` elemento.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Não existem diferenças no formato de certificados específicas do locatário e locatário independente.

### <a name="ws-federation-endpoint-url"></a>URL do ponto de extremidade de Web Services Federation

Os metadados de Federação incluem a URL que é Azure AD usa para entrar único e único saído no protocolo Web Services Federation. Neste ponto de extremidade aparece no `PassiveRequestorEndpoint` elemento.

Os metadados a seguir mostra um exemplo `PassiveRequestorEndpoint` elemento para um ponto de extremidade do locatário específicas.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Para o ponto de extremidade do locatário independente, a URL de Web Services Federation aparece no ponto de extremidade de Web Services Federation, conforme mostrado no exemplo a seguir.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>URL do ponto de extremidade de protocolo SAML

Os metadados de Federação incluem a URL que Azure AD usa para entrar único e único saído no protocolo SAML 2.0. Esses pontos de extremidade aparecem no `IDPSSODescriptor` elemento.

As URLs de entrada e saídas aparecem no `SingleSignOnService` e `SingleLogoutService` elementos.

Os metadados a seguir mostra um exemplo `PassiveResistorEndpoint` para um ponto de extremidade do locatário específicas.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Da mesma forma os pontos de extremidade para os pontos de extremidade de protocolo SAML 2.0 comuns são publicados nos metadados de Federação do locatário independente, conforme mostrado no exemplo a seguir.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
