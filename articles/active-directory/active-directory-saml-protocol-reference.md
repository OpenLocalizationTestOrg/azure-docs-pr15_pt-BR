<properties
    pageTitle="Referência de protocolo do Azure AD SAML | Microsoft Azure"
    description="Este artigo fornece uma visão geral dos perfis de logon único e único SAML Sign-Out no Active Directory do Azure."
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
    ms.date="06/23/2016"
    ms.author="priyamo"/>


# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Como o Active Directory do Azure usa o protocolo SAML

Azure Active Directory (AD Azure) usa o SAML 2.0 protocolo permitem que aplicativos fornecer aos usuários uma experiência de logon único. Os perfis de [Logon único](active-directory-single-sign-on-protocol-reference.md) e SAML [único saído](active-directory-single-sign-out-protocol-reference.md) do Azure AD explicam como declarações SAML, protocolos e ligações são usadas no serviço de provedor de identidade.

Protocolo de SAML requer o provedor de identidade (Azure AD) e o provedor de serviço (o aplicativo) para trocar informações sobre si mesmo.

Quando um aplicativo é registrado com o Azure AD, o desenvolvedor do aplicativo registra informações relacionadas a federação com Azure AD. Isso inclui o **Redirecionar URI** e o **URI de metadados** do aplicativo.

Azure AD usa o **URI de metadados** do serviço de nuvem para recuperar a chave de assinatura e o logout URI do serviço de nuvem. Se o aplicativo não oferece suporte a um URI de metadados, o desenvolvedor deve contatar o suporte da Microsoft para fornecer o logout URI e chave de assinatura.

Active Directory do Azure expõe comuns e específicas do locatário (independente de locatário) único logon único saídos pontos de extremidade e. Estas URLs representam locais endereçamento – não estiverem apenas um identificadores – para que você pode ir para o ponto de extremidade para ler os metadados.

 - O ponto de extremidade do locatário específico está localizado em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.  O <TenantDomainName> espaço reservado representa um nome de domínio registrado ou TenantID GUID de um locatário do Azure AD. Por exemplo, os metadados de Federação do locatário contoso.com são em: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

- O ponto de extremidade do locatário independente está localizado em `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Nesse endereço do ponto de extremidade, **comuns** for exibida, em vez de um nome de domínio do locatário ou ID.

Para obter informações sobre os documentos de metadados de federação que publica Azure AD, consulte [Metadados de Federação](active-directory-federation-metadata.md).
