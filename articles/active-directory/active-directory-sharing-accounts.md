<properties
    pageTitle="Compartilhando contas usando o Azure AD |  Microsoft Azure"
    description="Descreve como o Active Directory do Azure permite que as organizações compartilhar com segurança contas para locais de aplicativos e serviços de nuvem do consumidor."
    services="active-directory"
    documentationCenter=""
    authors="msStevenPo"
    manager="femila"
    editor=""/>

 <tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"  
    ms.author="stevenpo"/>

# <a name="sharing-accounts-with-azure-ad"></a>Compartilhando contas com o Azure AD

## <a name="overview"></a>Visão geral
Às vezes, as organizações precisam usar um único nome de usuário e senha para várias pessoas. Normalmente, isso acontece em dois casos:

- Ao acessar aplicativos que exigem um login exclusivo e uma senha para cada usuário, se aplicativos locais ou consumidor serviços em nuvem (por exemplo, contas de mídia social corporativa).
- Ao criar ambientes de vários usuários. Você pode ter uma única local conta possui privilégios elevados e pode ser usada para principais atividades de configuração, administração e recuperação (por exemplo, a conta local "administrador global" para o Office 365) ou a conta raiz no Salesforce.

Normalmente, essas contas seriam compartilhadas por distribuir as credenciais (nome de usuário/senha) para os indivíduos certos ou armazená-los em um local compartilhado onde vários agentes confiáveis possam acessá-los.

O modelo de compartilhamento tradicional tem várias desvantagens:

- Permitindo o acesso a novos aplicativos requer que você distribuir credenciais para todos que precisa de acesso.
- Cada aplicativo compartilhado pode exigir seu próprio conjunto exclusivo de credenciais compartilhadas, exigir que os usuários a lembrar vários conjuntos de credenciais. Quando os usuários têm de lembrar muitas credenciais, o risco aumenta que eles serão reclassificar práticas arriscada. (por exemplo, gravando senhas).
- Você não pode saber quem tem acesso a um aplicativo.
- Você não pode saber quem tem *acessado* um aplicativo.
- Quando você precisa remover acesso a um aplicativo, você precisa atualizar as credenciais e distribuí-los novamente para todos os participantes que precise acessar esse aplicativo.

## <a name="azure-active-directory-account-sharing"></a>Compartilhamento de contas do Active Directory Azure

Azure AD fornece uma nova abordagem usando contas compartilhadas que elimina essas desvantagens.

O administrador do Azure AD configura os aplicativos que um usuário pode acessar usando o painel de acesso e escolhendo o tipo de melhor de logon único são adequados para esse aplicativo. Um desses tipos, *baseada em senha single sign - na*, permite Azure AD atuar como um tipo de "agente" durante o processo de logon para esse aplicativo.

Os usuários fazem logon em uma vez com sua conta organizacional. Esta é a mesma conta que usa regularmente para acessar sua área de trabalho ou um email. Eles podem descobrir e acessar somente os aplicativos que eles estão atribuídos. Com contas compartilhadas, esta lista de aplicativos pode incluir qualquer número de credenciais compartilhadas. O usuário final não precisa se lembrar ou anote diversas contas que eles podem estar usando.

Contas compartilhadas não apenas aumentam supervisão e melhorar a usabilidade, eles também aprimoram sua segurança. Usuários com permissões para usar as credenciais não vir a senha compartilhada, mas preferir obter permissões para usar a senha como parte de um fluxo de autenticação orquestrada. Além disso, com alguns aplicativos de SSO de senha, você tem a opção tenha Azure AD periodicamente sobreposição (Atualizar) a senha usando senhas grandes e complexas, aumentar a segurança de conta. O administrador pode facilmente conceder ou revogar o acesso a um aplicativo e também saber quem tem acesso à conta e quem ele acessadas no passado.

Azure AD suporta contas compartilhadas para qualquer pacote de mobilidade de Enterprise (EMS), Premium ou básicos usuários licenciados, em todos os tipos de senha única entrar em aplicativos. Você pode compartilhar contas para qualquer um dos milhares de aplicativos integrados previamente na Galeria de aplicativo e pode adicionar seu próprio aplicativo de autenticação de senha com [aplicativos SSO personalizados](active-directory-sso-integrate-saas-apps.md).

Os recursos do Azure AD que habilitar o compartilhamento de conta incluem:

- [Logon único senha](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
- Agente de logon único senha
- [Atribuição de grupo](active-directory-accessmanagement-self-service-group-management.md)
- Aplicativos de senha personalizada
- [Painel/relatórios de uso do aplicativo](active-directory-passwords-get-insights.md)
- Portais de acesso do usuário final
- [Proxy de aplicativo](active-directory-application-proxy-get-started.md)
- [Marketplace do Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Compartilhamento de uma conta
Para usar o Azure AD para compartilhar uma conta que você precisa:

- Adicionar um aplicativo [Galeria de aplicativo](https://azure.microsoft.com/marketplace/active-directory/) ou um [aplicativo personalizado](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)
- Configurar o aplicativo para senha Single Sign-On (SSO)
- Use o [grupo baseado atribuição](active-directory-accessmanagement-group-saasapps.md) e selecione a opção para inserir uma credencial compartilhada
- Opcional: em alguns aplicativos, como o Facebook, Twitter ou LinkedIn, você pode habilitar a opção de [senha automatizado do Azure AD sobreposição](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)

Você também pode tornar sua conta compartilhada mais seguras com autenticação multifator (MFA) (Saiba mais sobre [proteção de aplicativos com o Azure AD](../multi-factor-authentication/multi-factor-authentication-get-started.md)) e você pode delegar a capacidade de gerenciar quem tem acesso ao aplicativo usando o [Azure AD autoatendimento](active-directory-accessmanagement-self-service-group-management.md) gerenciamento de grupo.

## <a name="related-articles"></a>Artigos relacionados

- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
- [Protegendo aplicativos com acesso condicional](active-directory-conditional-access.md)
- [Gerenciamento de grupo de autoatendimento/SSAA](active-directory-accessmanagement-self-service-group-management.md)
