<properties
    pageTitle="Sincronização do Azure AD Connect: atributos sincronizados ao Azure Active Directory | Microsoft Azure"
    description="Lista os atributos que são sincronizados com o Active Directory do Azure."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="markvi;andkjell"/>


# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Sincronização do Azure AD Connect: atributos sincronizados com o Active Directory do Azure
Este tópico lista os atributos que são sincronizados pela sincronização do Azure AD Connect.  
Os atributos são agrupados pelo Azure relacionado AD app.

## <a name="attributes-to-synchronize"></a>Atributos para sincronizar
Uma pergunta comum é *o que é a lista de atributos mínimas para sincronizar*. O padrão e a abordagem recomendada é para manter os atributos padrão para que um total GAL (lista de endereços Global) pode ser construído na nuvem e para obter todos os recursos em cargas de trabalho do Office 365. Em alguns casos, há alguns atributos que sua organização não deseja sincronizados na nuvem desde que contêm esses atributos confidencial ou dados de (informações de identificação pessoal) IIP, como neste exemplo:  
![atributos inválidos](./media/active-directory-aadconnectsync-attributes-synchronized/badextensionattribute.png)

Nesse caso, comece com a lista de atributos neste tópico e identificar os atributos que contém dados de IIP ou confidenciais e não podem ser sincronizados. Em seguida, desmarque os atributos durante a instalação usando o [aplicativo do Azure AD e filtragem de atributo](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering).

>[AZURE.WARNING] Ao desmarcar atributos, você deve ter cuidado e desmarque apenas esses atributos absolutamente não é possíveis sincronizar. Cancelando outros atributos pode ter um impacto negativo em recursos.

## <a name="office-365-proplus"></a>O Office 365 ProPlus

| Nome do atributo| Usuário| Comentário |
| --- | :-: | --- |
| accountEnabled| X| Define se uma conta estiver habilitada.|
| CN| X|  |
| displayName| X|  |
| objectSID| X| propriedade mecânica. Identificador de usuário do AD usada para manter a sincronização entre Azure AD e AD.|
| pwdLastSet| X| propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pelo sincronização de senha e federação.|
| sourceAnchor| X| propriedade mecânica. Identificador imutável para manter a relação entre ADICIONA e Azure AD.|
| usageLocation| X| propriedade mecânica. País/região do usuário. Usado para atribuição de licença.|
| userPrincipalName| X| UPN é a ID de logon do usuário. Geralmente, o valor igual a [email].|

## <a name="exchange-online"></a>Exchange Online

| Nome do atributo| Usuário| Contato| Grupo| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define se uma conta estiver habilitada.|
| Assistente| X| X|  |  |
| authOrig| X| X| X|  |
| c| X| X|  |  |
| CN| X|  | X|  |
| Co| X| X|  |  |
| empresa| X| X|  |  |
| Código| X| X|  |  |
| departamento| X| X|  |  |
| Descrição| X| X| X|  |
| displayName| X| X| X|  |
| dLMemRejectPerms| X| X| X|  |
| dLMemSubmitPerms| X| X| X|  |
| extensionAttribute1| X| X| X|  |
| extensionAttribute10| X| X| X|  |
| extensionAttribute11| X| X| X|  |
| extensionAttribute12| X| X| X|  |
| extensionAttribute13| X| X| X|  |
| extensionAttribute14| X| X| X|  |
| extensionAttribute15| X| X| X|  |
| extensionAttribute2| X| X| X|  |
| extensionAttribute3| X| X| X|  |
| extensionAttribute4| X| X| X|  |
| extensionAttribute5| X| X| X|  |
| extensionAttribute6| X| X| X|  |
| extensionAttribute7| X| X| X|  |
| extensionAttribute8| X| X| X|  |
| extensionAttribute9| X| X| X|  |
| facsimileTelephoneNumber| X| X|  |  |
| givenName| X| X|  |  |
| TelefoneResidencial| X| X|  |  |
| informações| X| X| X| Atualmente, esse atributo não é consumido para grupos.|
| Iniciais| X| X|  |  |
| l| X| X|  |  |
| legacyExchangeDN| X| X| X|  |
| mailNickname| X| X| X|  |
| mangedBy|  |  | X|  |
| Gerenciador de| X| X|  |  |
| membro|  |  | X|  |
| móvel| X| X|  |  |
| msDS-HABSeniorityIndex| X| X| X|  |
| msDS-PhoneticDisplayName| X| X| X|  |
| msExchArchiveGUID| X|  |  |  |
| msExchArchiveName| X|  |  |  |
| msExchAssistantName| X| X|  |  |
| msExchAuditAdmin| X|  |  |  |
| msExchAuditDelegate| X|  |  |  |
| msExchAuditDelegateAdmin| X|  |  |  |
| msExchAuditOwner| X|  |  |  |
| msExchBlockedSendersHash| X| X|  |  |
| msExchBypassAudit| X|  |  |  |
| msExchCoManagedByLink|  |  | X|  |
| msExchDelegateListLink| X|  |  |  |
| msExchELCExpirySuspensionEnd| X|  |  |  |
| msExchELCExpirySuspensionStart| X|  |  |  |
| msExchELCMailboxFlags| X|  |  |  |
| msExchEnableModeration| X|  | X|  |
| msExchExtensionCustomAttribute1| X| X| X| Atualmente, esse atributo não é consumido pelo Exchange Online. |
| msExchExtensionCustomAttribute2| X| X| X| Atualmente, esse atributo não é consumido pelo Exchange Online. |
| msExchExtensionCustomAttribute3| X| X| X| Atualmente, esse atributo não é consumido pelo Exchange Online. |
| msExchExtensionCustomAttribute4| X| X| X| Atualmente, esse atributo não é consumido pelo Exchange Online. |
| msExchExtensionCustomAttribute5| X| X| X| Atualmente, esse atributo não é consumido pelo Exchange Online. |
| msExchHideFromAddressLists| X| X| X|  |
| msExchImmutableID| X|  |  |  |
| msExchLitigationHoldDate| X| X| X|  |
| msExchLitigationHoldOwner| X| X| X|  |
| msExchMailboxAuditEnable| X|  |  |  |
| msExchMailboxAuditLogAgeLimit| X|  |  |  |
| msExchMailboxGuid| X|  |  |  |
| msExchModeratedByLink| X| X| X|  |
| msExchModerationFlags| X| X| X|  |
| msExchRecipientDisplayType| X| X| X|  |
| msExchRecipientTypeDetails| X| X| X|  |
| msExchRemoteRecipientType| X|  |  |  |
| msExchRequireAuthToSendTo| X| X| X|  |
| msExchResourceCapacity| X|  |  |  |
| msExchResourceDisplay| X|  |  |  |
| msExchResourceMetaData| X|  |  |  |
| msExchResourceSearchProperties| X|  |  |  |
| msExchRetentionComment| X| X| X|  |
| msExchRetentionURL| X| X| X|  |
| msExchSafeRecipientsHash| X| X|  |  |
| msExchSafeSendersHash| X| X|  |  |
| msExchSenderHintTranslations| X| X| X|  |
| msExchTeamMailboxExpiration| X|  |  |  |
| msExchTeamMailboxOwners| X|  |  |  |
| msExchTeamMailboxSharePointUrl| X|  |  |  |
| msExchUserHoldPolicies| X|  |  |  |
| msOrg-IsOrganizational|  |  | X|  |
| objectSID| X|  | X| propriedade mecânica. Identificador de usuário do AD usada para manter a sincronização entre Azure AD e AD.|
| oOFReplyToOriginator|  |  | X|  |
| otherFacsimileTelephone| X| X|  |  |
| otherHomePhone| X| X|  |  |
| otherTelephone| X| X|  |  |
| pager| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| CEP| X| X|  |  |
| proxyAddresses| X| X| X|  |
| publicDelegates| X| X| X|  |
| pwdLastSet| X|  |  | propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pelo sincronização de senha e federação.|
| parâmetros reportToOriginator|  |  | X|  |
| reportToOwner|  |  | X|  |
| securityEnabled|  |  | X| Derivado groupType|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutável para manter a relação entre ADICIONA e Azure AD.|
| ST| X| X|  |  |
| streetAddress| X| X|  |  |
| targetAddress| X| X|  |  |
| telephoneAssistant| X| X|  |  |
| telephoneNumber| X| X|  |  |
| thumbnailPhoto| X| X|  |  |
| título| X| X|  |  |
| unauthOrig| X| X| X|  |
| usageLocation| X|  |  | propriedade mecânica. País/região do usuário. Usado para atribuição de licença.|
| userCertificate| X| X|  |  |
| userPrincipalName| X|  |  | UPN é a ID de logon do usuário. Geralmente, o valor igual a [email].|
| userSMIMECertificates| X| X|  |  |
| wWWHomePage| X| X|  |  |

## <a name="sharepoint-online"></a>SharePoint Online

| Nome do atributo| Usuário| Contato| Grupo| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define se uma conta estiver habilitada.|
| authOrig| X| X| X|  |
| c| X| X|  |  |
| CN| X|  | X|  |
| Co| X| X|  |  |
| empresa| X| X|  |  |
| Código| X| X|  |  |
| departamento| X| X|  |  |
| Descrição| X| X| X|  |
| displayName| X| X| X|  |
| dLMemRejectPerms| X| X| X|  |
| dLMemSubmitPerms| X| X| X|  |
| extensionAttribute1| X| X| X|  |
| extensionAttribute10| X| X| X|  |
| extensionAttribute11| X| X| X|  |
| extensionAttribute12| X| X| X|  |
| extensionAttribute13| X| X| X|  |
| extensionAttribute14| X| X| X|  |
| extensionAttribute15| X| X| X|  |
| extensionAttribute2| X| X| X|  |
| extensionAttribute3| X| X| X|  |
| extensionAttribute4| X| X| X|  |
| extensionAttribute5| X| X| X|  |
| extensionAttribute6| X| X| X|  |
| extensionAttribute7| X| X| X|  |
| extensionAttribute8| X| X| X|  |
| extensionAttribute9| X| X| X|  |
| facsimileTelephoneNumber| X| X|  |  |
| givenName| X| X|  |  |
| hideDLMembership|  |  | X|  |
| TelefoneResidencial| X| X|  |  |
| informações| X| X| X|  |
| iniciais| X| X|  |  |
| ipPhone| X| X|  |  |
| l| X| X|  |  |
| mail| X| X| X|  |
| mailNickname| X| X| X|  |
| managedBy|  |  | X|  |
| Gerenciador de| X| X|  |  |
| membro|  |  | X|  |
| middleName| X| X|  |  |
| móvel| X| X|  |  |
| msExchTeamMailboxExpiration| X|  |  |  |
| msExchTeamMailboxOwners| X|  |  |  |
| msExchTeamMailboxSharePointLinkedBy| X|  |  |  |
| msExchTeamMailboxSharePointUrl| X|  |  |  |
| objectSID| X|  | X| propriedade mecânica. Identificador de usuário do AD usada para manter a sincronização entre Azure AD e AD.|
| oOFReplyToOriginator|  |  | X|  |
| otherFacsimileTelephone| X| X|  |  |
| otherHomePhone| X| X|  |  |
| otherIpPhone| X| X|  |  |
| otherMobile| X| X|  |  |
| otherPager| X| X|  |  |
| otherTelephone| X| X|  |  |
| pager| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| CEP| X| X|  |  |
| postOfficeBox| X| X|  |  |
| preferredLanguage| X|  |  |  |
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pelo sincronização de senha e federação.|
| parâmetros reportToOriginator|  |  | X|  |
| reportToOwner|  |  | X|  |
| securityEnabled|  |  | X| Derivado groupType|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutável para manter a relação entre ADICIONA e Azure AD.|
| ST| X| X|  |  |
| streetAddress| X| X|  |  |
| targetAddress| X| X|  |  |
| telephoneAssistant| X| X|  |  |
| telephoneNumber| X| X|  |  |
| thumbnailPhoto| X| X|  |  |
| título| X| X|  |  |
| unauthOrig| X| X| X|  |
| URL| X| X|  |  |
| usageLocation| X|  |  | propriedade mecânica. País/região do usuário. Usado para atribuição de licença.|
| userPrincipalName| X|  |  | UPN é a ID de logon do usuário. Geralmente, o valor igual a [email].|
| wWWHomePage| X| X|  |  |

## <a name="lync-online"></a>Lync Online

| Nome do atributo| Usuário| Contato| Grupo| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define se uma conta estiver habilitada.|
| c| X| X|  |  |
| CN| X|  | X|  |
| Co| X| X|  |  |
| empresa| X| X|  |  |
| departamento| X| X|  |  |
| Descrição| X| X| X|  |
| displayName| X| X| X|  |
| facsimileTelephoneNumber| X| X| X|  |
| givenName| X| X|  |  |
| TelefoneResidencial| X| X|  |  |
| ipPhone| X| X|  |  |
| l| X| X|  |  |
| mail| X| X| X|  |
| mailNickname| X| X| X|  |
| managedBy|  |  | X|  |
| Gerenciador de| X| X|  |  |
| membro|  |  | X|  |
| móvel| X| X|  |  |
| msExchHideFromAddressLists| X| X| X|  |
| msRTCSIP-ApplicationOptions| X|  |  |  |
| msRTCSIP-DeploymentLocator| X| X|  |  |
| msRTCSIP-Line| X| X|  |  |
| msRTCSIP-OptionFlags| X| X|  |  |
| msRTCSIP-OwnerUrn| X|  |  |  |
| msRTCSIP-PrimaryUserAddress| X| X|  |  |
| msRTCSIP-UserEnabled| X| X|  |  |
| objectSID| X|  | X| propriedade mecânica. Identificador de usuário do AD usada para manter a sincronização entre Azure AD e AD.|
| otherTelephone| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| CEP| X| X|  |  |
| preferredLanguage| X|  |  |  |
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pelo sincronização de senha e federação.|
| securityEnabled|  |  | X| Derivado groupType|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutável para manter a relação entre ADICIONA e Azure AD.|
| ST| X| X|  |  |
| streetAddress| X| X|  |  |
| telephoneNumber| X| X|  |  |
| thumbnailPhoto| X| X|  |  |
| título| X| X|  |  |
| usageLocation| X|  |  | propriedade mecânica. País/região do usuário. Usado para atribuição de licença.|
| userPrincipalName| X|  |  | UPN é a ID de logon do usuário. Geralmente, o valor igual a [email].|
| wWWHomePage| X| X|  |  |

## <a name="azure-rms"></a>Azure RMS

| Nome do atributo| Usuário| Contato| Grupo| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define se uma conta estiver habilitada.|
| CN| X|  | X| Nome ou alias comuns. Geralmente, o prefixo de valor [email].|
| displayName| X| X| X| Uma cadeia de caracteres que representa o nome que geralmente é mostrado como o nome amigável (nome sobrenome).|
| mail| X| X| X| endereço de email completo.|
| membro|  |  | X|  |
| objectSID| X|  | X| propriedade mecânica. Identificador de usuário do AD usada para manter a sincronização entre Azure AD e AD.|
| proxyAddresses| X| X| X| propriedade mecânica. Usado pelo Azure AD. Contém todos os endereços de email secundário para o usuário.|
| pwdLastSet| X|  |  | propriedade mecânica. Usado para saber quando invalidar tokens já emitidos.|
| securityEnabled|  |  | X| Derivado de groupType.|
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutável para manter a relação entre ADICIONA e Azure AD.|
| usageLocation| X|  |  | propriedade mecânica. País/região do usuário. Usado para atribuição de licença.|
| userPrincipalName| X|  |  | Este UPN é a ID de logon do usuário. Geralmente, o valor igual a [email].|

## <a name="intune"></a>Intune

| Nome do atributo| Usuário| Contato| Grupo| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define se uma conta estiver habilitada.|
| c| X| X|  |  |
| CN| X|  | X|  |
| Descrição| X| X| X|  |
| displayName| X| X| X|  |
| mail| X| X| X|  |
| mailNickname| X| X| X|  |
| membro|  |  | X|  |
| objectSID| X|  | X| propriedade mecânica. Identificador de usuário do AD usada para manter a sincronização entre Azure AD e AD.|
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pelo sincronização de senha e federação.|
| securityEnabled|  |  | X| Derivado groupType|
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutável para manter a relação entre ADICIONA e Azure AD.|
| usageLocation| X|  |  | propriedade mecânica. País/região do usuário. Usado para atribuição de licença.|
| userPrincipalName| X|  |  | UPN é a ID de logon do usuário. Geralmente, o valor igual a [email].|

## <a name="dynamics-crm"></a>Dynamics CRM

| Nome do atributo| Usuário| Contato| Grupo| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define se uma conta estiver habilitada.|
| c| X| X|  |  |
| CN| X|  | X|  |
| Co| X| X|  |  |
| empresa| X| X|  |  |
| Código| X| X|  |  |
| Descrição| X| X| X|  |
| displayName| X| X| X|  |
| facsimileTelephoneNumber| X| X|  |  |
| givenName| X| X|  |  |
| l| X| X|  |  |
| managedBy|  |  | X|  |
| Gerenciador de| X| X|  |  |
| membro|  |  | X|  |
| móvel| X| X|  |  |
| objectSID| X|  | X| propriedade mecânica. Identificador de usuário do AD usada para manter a sincronização entre Azure AD e AD.|
| physicalDeliveryOfficeName| X| X|  |  |
| CEP| X| X|  |  |
| preferredLanguage| X|  |  |  |
| pwdLastSet| X|  |  | propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pelo sincronização de senha e federação.|
| securityEnabled|  |  | X| Derivado groupType|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutável para manter a relação entre ADICIONA e Azure AD.|
| ST| X| X|  |  |
| streetAddress| X| X|  |  |
| telephoneNumber| X| X|  |  |
| título| X| X|  |  |
| usageLocation| X|  |  | propriedade mecânica. País/região do usuário. Usado para atribuição de licença.|
| userPrincipalName| X|  |  | UPN é a ID de logon do usuário. Geralmente, o valor igual a [email].|

## <a name="3rd-party-applications"></a>3º aplicativos de terceiros
Este grupo é um conjunto de atributos usados como os atributos mínimos necessários para um aplicativo ou a carga de trabalho genérica. Ele pode ser usado para uma carga de trabalho não listado em outra seção ou para um aplicativo não sejam da Microsoft. Explicitamente, ele é usado para o seguinte:

- Yammer (somente usuário é consumido)
- [Cenários de colaboração híbrido Business-to-Business (B2B) entre-org oferecidos por recursos como o SharePoint](http://go.microsoft.com/fwlink/?LinkId=747036)

Este grupo é um conjunto de atributos que podem ser usados se o diretório do Azure AD não é usado para Office 365, Dynamics ou Intune de suporte. Ela tem um conjunto pequeno de principais atributos.

| Nome do atributo| Usuário| Contato| Grupo| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define se uma conta estiver habilitada.|
| CN| X|  | X|  |
| displayName| X| X| X|  |
| givenName| X| X|  |  |
| mail| X|  | X|  |
| managedBy|  |  | X|  |
| mailNickName| X| X| X|  |
| membro|  |  | X|  |
| objectSID| X|  |  | propriedade mecânica. Identificador de usuário do AD usada para manter a sincronização entre Azure AD e AD.|
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pelo sincronização de senha e federação.|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutável para manter a relação entre ADICIONA e Azure AD.|
| usageLocation| X|  |  | propriedade mecânica. País/região do usuário. Usado para atribuição de licença.|
| userPrincipalName| X|  |  | UPN é a ID de logon do usuário. Geralmente, o valor igual a [email].|

## <a name="windows-10"></a>Windows 10
Um computer(device) de domínio do Windows 10 sincroniza alguns atributos ao Azure AD. Para obter mais informações sobre os cenários, consulte [conectar dispositivos de domínio para o Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md). Esses atributos sempre sincronizar e Windows 10 não aparecerá como um aplicativo que você pode desmarcar. Um computador com Windows 10 domínio é identificado por tendo o userCertificate atributo preenchido.

| Nome do atributo| Dispositivo| Comentário |
| --- | :-: | --- |
| accountEnabled| X| |
| deviceTrustType| X| Valor de codificados para computadores de domínio. |
| displayName | X| |
| MS-DS-CreatorSID | X| Também chamado de registeredOwnerReference.|
| objectGUID | X| Também chamado deviceID.|
| objectSID | X| Também chamado de onPremisesSecurityIdentifier.|
| sistema operacional | X| Também chamado de deviceOSType.|
| operatingSystemVersion | X| Também chamado de deviceOSVersion.|
| userCertificate | X| |

Esses atributos de **usuário** são além de outros aplicativos que você selecionou.  

| Nome do atributo| Usuário| Comentário |
| --- | :-: | --- |
| domainFQDN| X| Também chamado NomeDeDomínioDNS. Por exemplo, contoso.com.|
| domainNetBios| X| Também chamado Nome_netbios. Por exemplo, CONTOSO.|

## <a name="exchange-hybrid-writeback"></a>Write-back híbrida do Exchange
Esses atributos são gravados volta do Azure AD do Active Directory local quando você seleciona Habilitar **híbrida do Exchange**. Dependendo da sua versão do Exchange, menos atributos podem ser sincronizados.

| Nome do atributo| Usuário| Contato| Grupo| Comentário |
| --- | :-: | :-: | :-: | --- |
| msDS-ExternalDirectoryObjectID| X|  |  | Derivado de cloudAnchor no Azure AD. Esse atributo é novo no Exchange 2016.|
| msExchArchiveStatus| X|  |  | Arquivo morto online: Permite aos clientes arquivar mensagens.|
| msExchBlockedSendersHash| X|  |  | Filtragem: Grava de volta filtragem local e dados de remetente confiáveis e bloqueados online de clientes.|
| msExchSafeRecipientsHash| X|  |  | Filtragem: Grava de volta filtragem local e dados de remetente confiáveis e bloqueados online de clientes.|
| msExchSafeSendersHash| X|  |  | Filtragem: Grava de volta filtragem local e dados de remetente confiáveis e bloqueados online de clientes.|
| msExchUCVoiceMailSettings| X|  |  | Habilitar a Unificação de mensagens (UM) - on-line caixa postal: usadas pelo Microsoft Lync Server integração para indicar ao Lync Server no local que o usuário tenha caixa postal nos serviços onlinehttps.|
| msExchUserHoldPolicies| X|  |  | Litígio: Permite que os serviços de nuvem para determinar quais usuários estão sob litígio.|
| proxyAddresses| X| X| X| Somente o x500 endereço do Exchange Online é inserido.|

## <a name="device-writeback"></a>Write-back do dispositivo
Objetos de dispositivo são criados no Active Directory. Esses objetos podem ser dispositivos associados ao Azure AD ou domínio Windows 10 computadores.

| Nome do atributo| Dispositivo| Comentário |
| --- | :-: | --- |
| altSecurityIdentities | X| |
| displayName | X| |
| DN | X| |
| msDS-CloudAnchor | X| |
| msDS-DeviceID | X| |
| msDS-DeviceObjectVersion | X| |
| msDS-DeviceOSType | X| |
| msDS-DeviceOSVersion | X| |
| msDS-DevicePhysicalIDs | X| |
| msDS-KeyCredentialLink | X| Somente com o esquema do AD do Windows Server 2016 |
| msDS-IsCompliant | X| |
| msDS-IsEnabled | X| |
| msDS-IsManaged | X| |
| msDS-RegisteredOwner | X| |


## <a name="notes"></a>Anotações

- Ao usar uma ID alternativa, o local atributo userPrincipalName é sincronizado com o Azure AD atributo onPremisesUserPrincipalName. O atributo de ID alternativa, para email de exemplo, é sincronizado com o Azure AD atributo userPrincipalName.
- Nas listas acima, o tipo de objeto **usuário** também se aplica o tipo de objeto **iNetOrgPerson**.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
