<properties
    pageTitle="Algoritmo de hash de assinatura de alteração de confiança de terceiros respondendo do Office 365 | Microsoft Azure"
    description="Esta página fornece diretrizes para alterar o algoritmo SHA para confiança de federação com o Office 365"
    keywords="SHA1, SHA256, O365, Federação, aadconnect, adfs, o ad fs, alterar sha, confiança de federação, contando confiança de festa"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="samueld"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/01/2016"
    ms.author="anandy"/>

# <a name="change-signature-hash-algorithm-for-office-365-replying-party-trust"></a>Alterar o algoritmo de hash de assinatura do Office 365 respondendo confiança de festa

## <a name="overview"></a>Visão geral

Azure Active Directory serviços de Federação do (AD FS) assina seus tokens ao Microsoft Azure Active Directory para garantir que eles não podem ser violados. Essa assinatura pode ser baseada em SHA1 ou SHA256. Agora, o Active Directory do Azure oferece suporte a tokens conectados com um algoritmo de SHA256 e recomendamos definindo o algoritmo de autenticação de token para SHA256 no nível mais alto de segurança. Este artigo descreve as etapas necessárias para configurar o algoritmo de autenticação de token para o mais seguro SHA256 nível.

## <a name="change-the-token-signing-algorithm"></a>Alterar o algoritmo de autenticação de token

Depois de definir o algoritmo de assinatura com um dos dois processos abaixo, o AD FS assina os tokens para contar confiança de festa com SHA256 do Office 365. Você não precisa fazer alterações de configuração extra e essa alteração não tem impacto sobre a capacidade de acessar o Office 365 ou outros aplicativos do Azure AD.

### <a name="ad-fs-management-console"></a>Console de gerenciamento do AD FS

1. Abra o console de gerenciamento do AD FS no servidor do AD FS principal.
2. Expanda o nó do AD FS e clique em **Confiar festa confia**.
3. A Office 365/Azure terceira festa de confiança de atalho e selecione **Propriedades**.
4. Selecione a guia **Avançado** e selecione o algoritmo de hash seguro SHA256.
5. Clique em **Okey**.

![Algoritmo de assinatura SHA256 - MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Cmdlets do AD FS PowerShell

1. Em qualquer servidor do AD FS, abra o PowerShell em privilégios de administrador.
2. Defina o algoritmo de hash seguro usando o cmdlet **Set-AdfsRelyingPartyTrust** .

 <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Também ler

* [Reparar confiabilidade do Office 365 com o Azure AD Connect](./active-directory-aadconnect-federation-management.md#repairing-the-trust)
