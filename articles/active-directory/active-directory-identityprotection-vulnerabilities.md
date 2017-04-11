<properties
    pageTitle="Vulnerabilidades detectadas pelo Azure Active Directory proteção de identidade | Microsoft Azure"
    description="Visão geral das vulnerabilidades detectado pelo Azure Active Directory proteção de identidade."
    services="active-directory"
    keywords="proteção de identidade do active directory do Azure, descoberta de aplicativo de nuvem, gerenciando aplicativos, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi"/>

# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Vulnerabilidades detectadas pelo Azure Active Directory proteção de identidade 

Vulnerabilidades são fraquezas em seu ambiente que podem ser exploradas por um invasor. Recomendamos que você atender essas vulnerabilidades para melhorar a postura de segurança da sua organização e impedir que ataques explorando-los.
<br><br>
![vulnerabilidades](./media/active-directory-identityprotection-vulnerabilities/101.png "vulnerabilities")
<br>

As seções a seguir fornecem uma visão geral das vulnerabilidades relatadas pela proteção de identidade.

## <a name="multi-factor-authentication-registration-not-configured"></a>Registro de autenticação multifator não configurado 

Essa vulnerabilidade ajuda você a controlar a implantação do Azure multi-Factor Authentication em sua organização. 

Azure autenticação multifator fornece uma segunda camada de segurança para autenticação do usuário. Ele ajuda a proteger o acesso a dados e aplicativos atendendo demanda do usuário para um processo de entrada simples. Ele oferece autenticação sólida via uma variedade de opções de verificação de fácil — chamada telefônica, uma mensagem de texto ou aplicativo móvel notificação ou verificação de código e 3º festa JURAMENTO tokens.

Recomendamos que você exige autenticação multifator do Azure para inscrições de usuário. Autenticação multifator desempenha um papel fundamental em políticas de acesso condicional baseado em risco disponíveis por meio de proteção de identidade.

Para obter mais detalhes, consulte [o que é a autenticação multifator Azure?](../multi-factor-authentication/multi-factor-authentication.md)


## <a name="unmanaged-cloud-apps"></a>Aplicativos de nuvem não gerenciado

Essa vulnerabilidade ajuda você a identificar aplicativos de nuvem não gerenciado em sua organização.
 
Em empresas modernas, departamentos de TI costumam cientes de todos os aplicativos de nuvem que os usuários em sua organização estão usando para fazer seu trabalho. É fácil ver por que os administradores terão preocupações sobre acesso não autorizado aos dados corporativos, perda de dados possíveis e outros riscos de segurança. 

Recomendamos que sua organização implantar descoberta de aplicativo de nuvem para detectar aplicativos de nuvem não gerenciado e para gerenciar esses aplicativos usando o Active Directory do Azure.

Para obter mais detalhes, consulte [localizar aplicativos em nuvem não gerenciado com detecção de aplicativo de nuvem](active-directory-cloudappdiscovery-whatis.md).



##<a name="security-alerts-from-privileged-identity-management"></a>Alertas de segurança de gerenciamento de identidades privilegiado

Essa vulnerabilidade ajuda você a descobrir e resolver alertas sobre identidades privilegiadas em sua organização.  

Para permitir que os usuários executem operações privilegiadas, as organizações precisam conceder aos usuários temporários ou permanentes acesso privilegiado no Azure AD, recursos Azure ou Office 365 ou outros aplicativos SaaS. Cada um desses usuários privilegiados aumenta a superfície de ataque de sua organização. Essa vulnerabilidade ajuda você a identificar os usuários com acesso privilegiado desnecessário e executar a ação apropriada para reduzir ou eliminar o risco que representam. 

Recomendamos que sua organização usa o gerenciamento de identidades do Azure AD privilegiados para gerenciar, o controle, e monitor privilegiado identidades e o acesso aos recursos do Azure AD bem como outros Microsoft online services como o Office 365 ou Microsoft Intune.

Para obter mais detalhes, consulte [gerenciamento de identidades do Azure AD privilegiados](active-directory-privileged-identity-management-configure.md). 



## <a name="see-also"></a>Consulte também

 - [Proteção de identidade do Active Directory do Azure](active-directory-identityprotection.md)
