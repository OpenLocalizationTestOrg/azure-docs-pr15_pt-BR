<properties
    pageTitle="Integrar o logon único Azure Active Directory com aplicativos de SaaS |  Microsoft Azure"
    description="Habilite a autenticação de logon única e provisionamento de gerenciamento de acesso centralizado de aplicativos SaaS no Azure Active Directory do usuário. Uma visão geral de como integrar o Active Directory do Azure para aplicativos SaaS."
    services="active-directory"
      keywords="integrar o Azure AD com aplicativos de SaaS"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/30/2016"
    ms.author="curtand"/>

# <a name="integrate-azure-active-directory-single-sign-on-with-saas-apps"></a>Integrar o logon único Azure Active Directory com aplicativos de SaaS  

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-enterprise-apps-manage-sso.md)
- [Azure portal clássico](active-directory-sso-integrate-saas-apps.md)

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

Para começar a configurar o logon único de um aplicativo que você está trazer para sua organização, você usará um diretório existente do Azure Active Directory (AD Azure). Você pode usar um diretório de Azure AD que obtém através do Microsoft Azure, Office 365 ou do Windows Intune. Se você tiver dois ou mais destes procedimentos, consulte [administrar seu diretório Azure AD](active-directory-administer.md) para determinar qual deles usar.

## <a name="authentication"></a>Autenticação

Para aplicativos que oferece suporte a SAML 2.0, Web Services Federation, ou protocolos OpenID conectar, Azure Active Directory utiliza certificados para estabelecer relações de confiança de assinatura. Para obter mais informações sobre isso, consulte [Gerenciando certificados para logon único federado](active-directory-sso-certs.md).

Para aplicativos que suportam somente HTML baseada em formulários entrar, o Active Directory do Azure usa 'compartimentos de senha' estabelecer relações de confiança. Isso permite que os usuários de sua organização automaticamente conectado a um aplicativo de SaaS por Azure AD usando as informações de conta de usuário do aplicativo de SaaS. Azure AD coleta e armazena as informações de conta de usuário e a senha relacionada de forma segura. Para obter mais informações, consulte [baseada em senha logon único](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

## <a name="authorization"></a>Autorização

Uma conta provisionada permite que um usuário seja autorizado a usar um aplicativo após terem autenticado por meio do logon único. Provisionamento do usuário pode ser feito manualmente ou em alguns casos, você pode adicionar e remover informações do usuário do aplicativo SaaS com base nas alterações feitas no Active Directory do Azure. Para obter mais informações sobre como usar os conectores existentes do Azure AD para provisionamento automatizado, consulte [automatizada usuário fornecimento e cancelamento para aplicativos SaaS](active-directory-saas-app-provisioning.md).

Caso contrário, você pode manualmente adicionar informações do usuário a um aplicativo ou use outras soluções de provisionamento que estão disponíveis no mercado.

## <a name="access"></a>Acesso

Azure AD fornece várias maneiras personalizáveis para implantar aplicativos para os usuários finais em sua organização. Você não é bloqueados em qualquer implantação específica ou a solução de acesso. Você pode usar [a solução que melhor atenda às suas necessidades](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

## <a name="additional-considerations-for-applications-already-in-use"></a>Considerações adicionais para aplicativos já em uso

Configurar o logon único em para um aplicativo que usa de sua organização já é um processo diferente do processo de criação de novas contas para um novo aplicativo. Existem algumas etapas preliminares incluindo: o mapeamento de identidades do usuário no aplicativo para identidades do Azure AD e Noções básicas sobre como os usuários terão logon em um aplicativo depois que ele é integrado.

> [AZURE.NOTE] Para configurar o SSO para um aplicativo existente, você precisa ter direitos de administrador global no Azure AD e o aplicativo de SaaS.

### <a name="mapping-user-accounts"></a>Contas de usuário de mapeamento

A identidade do usuário normalmente tem um identificador exclusivo que pode ser um endereço de email ou nome de usuário principal (UPN). Você precisará link (mapa) identidade de aplicativo de cada usuário para seus respectivos identidade do Azure AD. Há algumas maneiras de fazer isso, dependendo de como a exigência de autenticação do seu aplicativo.

Para obter mais informações sobre o mapeamento de identidades do aplicativo com identidades do Azure AD, consulte [Personalizando declarações emitidas no token SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) e [os mapeamentos de atributo personalizando para provisionamento](active-directory-saas-customizing-attribute-mappings.md).

### <a name="understanding-the-users-log-in-experience"></a>Noções básicas sobre a experiência de login do usuário

Quando você integra SSO para um aplicativo que já está em uso, é importante perceber que a experiência do usuário será afetada. Todos os aplicativos, os usuários iniciará usando suas credenciais do Azure AD para entrar. Também é possível que eles devem usar um portal diferente para acessar os aplicativos.

SSO para alguns aplicativos pode ser feito na entrada do aplicativo na interface, mas para outros aplicativos, o usuário terá de percorrer um portal central como ([Meus aplicativos](http://myapps.microsoft.com) ou [Office365](http://portal.office.com/myapps)) para entrar. Saiba mais sobre os diferentes tipos de SSO e suas experiências de usuário no [que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

Outro recurso valioso é *Suppressing usuário consentimento* no artigo [Guiding desenvolvedores](active-directory-applications-guiding-developers-for-lob-applications.md) .

## <a name="next-steps"></a>Próximas etapas


Para aplicativos SaaS que encontrar na Galeria de aplicativo, Azure AD fornece um número de [tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md).

Se o aplicativo não estiver na Galeria de aplicativos, você pode [adicioná-la à Galeria de aplicativo do Azure AD como um aplicativo personalizado](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

Há muito mais detalhes em todos esses problemas na biblioteca Azure.com, começando com [o que é o acesso de aplicativo e logon único com o Active Directory do Azure.](active-directory-appssoaccess-whatis.md).

## <a name="see-also"></a>Consulte também

- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
