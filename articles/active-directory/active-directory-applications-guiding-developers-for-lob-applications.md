<properties
    pageTitle="Azure AD e aplicativos: orientando desenvolvedores | Microsoft Azure"
    description="Escrito para profissionais de TI, este artigo fornece diretrizes para integrar aplicativos do Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="azure-ad-and-applications-develop-line-of-business-apps"></a>Azure AD e aplicativos: desenvolver aplicativos de linha de negócios

Este guia fornece uma visão geral de desenvolvimento de linha de negócios (LoB) aplicativos do Azure Active Directory (AD). O público-alvo é administradores globais do Active Directory/Office 365.

## <a name="overview"></a>Visão geral

Criação de aplicativos integrados com o Azure AD oferece aos usuários em sua organização logon único com o Office 365. Tendo o aplicativo no Azure AD lhe dá que controle sobre a política de autenticação para o aplicativo. Para saber mais sobre condicional acesso e como proteger aplicativos com autenticação multifator (MFA) consulte [Configurando regras de acesso](active-directory-conditional-access-azuread-connected-apps.md).

Registre seu aplicativo para usar o Active Directory do Azure. Registrar o aplicativo significa que os desenvolvedores podem usar Azure AD para autenticar usuários e solicitar acesso aos recursos de usuário como email, calendário e documentos.

Qualquer membro do seu diretório (não convidados) pode registrar um aplicativo, também conhecido como *criar um objeto de aplicativo*.

Registrar um aplicativo permite que qualquer usuário faça o seguinte:

- Obter uma identidade do seu aplicativo que reconhece Azure AD
- Obter um ou mais segredos/teclas que o aplicativo pode usar para se autenticar AD
- Marca o aplicativo no portal do Azure com um nome personalizado, logotipo, etc.
- Aplique os recursos de autorização do Azure AD para seus aplicativos, incluindo:
  - Controle de acesso baseado em função (RBAC)
  - Active Directory do Azure como servidor de autorização oAuth (segura uma API exposta pelo aplicativo)

- Declarar permissões necessárias necessários para o aplicativo função conforme o esperado, incluindo:-permissões do aplicativo (somente administradores globais). Por exemplo: membro da função de outra associação de aplicativo ou função do Azure AD relativo um Azure recurso, grupo de recursos, ou assinatura - permissões delegadas (qualquer usuário). Por exemplo: Azure AD, entrar e no perfil de leitura


> [AZURE.NOTE]Por padrão, qualquer membro pode registrar um aplicativo. Para saber como restringir permissões para registrar aplicativos a membros específicos, consulte [como os aplicativos são adicionados ao Azure AD](active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

Veja aqui o que você, o administrador global, precisa fazer para ajudar os desenvolvedores a tornar seu aplicativo pronto para produção:

- Configurar regras de acesso (política de acesso/MFA)
- Configurar o aplicativo para exigir a atribuição de usuário e atribuir aos usuários
- Suprimir a experiência de consentimento do usuário padrão

## <a name="configure-access-rules"></a>Configurar regras de acesso

Configure regras de acesso de por aplicativo seus aplicativos SaaS. Por exemplo, você pode exigir MFA ou permitir somente o acesso aos usuários em redes confiáveis. Os detalhes para isso estão disponíveis no documento [Configurando regras de acesso](active-directory-conditional-access-azuread-connected-apps.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Configurar o aplicativo para exigir a atribuição de usuário e atribuir aos usuários

Por padrão, os usuários podem acessar aplicativos sem sendo atribuído. No entanto, se o aplicativo expõe funções ou se quiser que o aplicativo seja exibido no painel de acesso do usuário, você deve exigir a atribuição de usuário.

[Exigindo a atribuição de usuário](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Se você for um assinante do Azure AD Premium ou o pacote de mobilidade da empresa (EMS), é altamente recomendável usar grupos. Atribuindo grupos para o aplicativo permite delegar o gerenciamento de acesso contínuo ao proprietário do grupo. Você pode criar o grupo ou peça parte responsável em sua organização para criar o grupo usando o recurso de gerenciamento de grupo.

[Atribuir usuários a um aplicativo](active-directory-applications-guiding-developers-assigning-users.md)  
[Atribuindo grupos para um aplicativo](active-directory-applications-guiding-developers-assigning-groups.md)

## <a name="suppress-user-consent"></a>Suprimir consentimento do usuário

Por padrão, cada usuário passa por uma experiência de consentimento para entrar. A experiência de consentimento, solicitando que os usuários concedam permissões para um aplicativo, pode ser desconcertante para usuários que não estão familiarizados com essas decisões.

Para aplicativos que você confia, você pode simplificar a experiência do usuário por concordando para o aplicativo em nome de sua organização.

Para obter mais informações sobre o consentimento do usuário e a experiência de consentimento no Azure, consulte [Aplicativos de integração com o Active Directory do Azure](active-directory-integrating-applications.md).

##<a name="related-articles"></a>Artigos relacionados

- [Habilitar o acesso remoto seguro para aplicativos de local com o Proxy de aplicativo do Azure AD](active-directory-application-proxy-get-started.md)
- [Visualização de acesso condicional Azure para aplicativos SaaS](active-directory-conditional-access-azuread-connected-apps.md)
- [Gerenciando o acesso aos aplicativos com o Azure AD](active-directory-managing-access-to-apps.md)
- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
