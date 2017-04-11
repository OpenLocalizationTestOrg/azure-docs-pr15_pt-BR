<properties
   pageTitle="Guia de introdução de integração do Azure Active Directory com aplicativos obtendo |  Microsoft Azure"
   description="Este artigo é um guia de Introdução ao obter para integrar o Azure Active Directory (AD) com aplicativos de locais e aplicativos de nuvem."
   services="active-directory"
   documentationCenter=""
   authors="ihenkel"
   manager="femila"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/09/2016"
      ms.author="inhenk"/>

# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Integração do Azure Active Directory com aplicativos obtendo o guia de Introdução
## <a name="overview"></a>Visão geral
Este tópico se destina a fornecer um roteiro para integrar aplicativos com o Azure Active Directory (AD). Cada uma das seções a seguir contêm um breve resumo de um tópico mais detalhado para que você possa identificar quais partes deste guia de Introdução ao obter forem relevantes para você.  Siga os links para aprofundar sobre cada assunto.

## <a name="before-you-begin-take-inventory"></a>Antes de começar, faça o inventário
Antes de você saltar para integrar aplicativos com o Azure AD, é importante saber onde você está e onde você deseja ir.  As questões a seguir destinam-se para ajudá-lo a pensar sobre o projeto de integração de aplicativo do Azure AD.

### <a name="application-inventory"></a>Inventário de aplicativos
- Onde estão todos os aplicativos? Quem é responsável-las?
- Que tipo de autenticação exigem seus aplicativos?
- Quem precisa de acesso a quais aplicativos?
- Você deseja implantar um novo aplicativo?
  - Você será criado internamente e implantá-lo em uma instância de computação Azure?
  - Você usará que está disponível na Galeria de aplicativo do Azure?

### <a name="user-and-group-inventory"></a>Inventário de usuário e grupo
- Onde residem suas contas de usuário?
 - Active Directory no local
 - Azure AD
 - Dentro de um banco de dados de aplicativo separado que você possui
 - Em aplicativos unsanctioned
 - Todos os itens acima
- Quais permissões e atribuições de função usuários individuais atualmente tem? Você precisa examinar seu acesso ou tem certeza de que suas atribuições de acesso e a função de usuário sejam adequadas agora?
- Grupos já estejam estabelecidos no seu local Active Directory?
 - Como os grupos são organizados?
 - Quem são os membros do grupo?
 - Quais atribuições de permissões/função os grupos possuem atualmente?
- Você precisará limpar bancos de dados do usuário/grupo antes de integrar?  (Isso é uma pergunta muito importante. Lixo in, lixo check-out).

### <a name="access-management-inventory"></a>Inventário de gerenciamento de acesso
- Como você gerencia atualmente acesso do usuário aos aplicativos? O que precisa alterar?  Você considerou outras maneiras de gerenciar o acesso, como com [RBAC](role-based-access-control-configure.md) por exemplo?
- Quem precisa de acesso a quê?

Talvez você não tiver as respostas de todas essas perguntas com antecedência, mas isso é okey.  Este guia pode ajudá-lo a atender algumas dessas perguntas e fazer algumas decisões.

## <a name="prerequisites"></a>Pré-requisitos
- Uma assinatura do Azure e um diretório do Active Directory do Azure.  Se você ainda não tiver uma assinatura do Azure, você pode experimentar Azure gratuitamente por 30 dias. [Experimente!](https://azure.microsoft.com/trial/get-started-active-directory/)

## <a name="application-integration-with-azure-ad"></a>Integração de aplicativo com o Azure AD
### <a name="finding-unsanctioned-cloud-applications-with-cloud-app-discovery"></a>Localizando unsanctioned aplicativos em nuvem com detecção de aplicativo de nuvem
Conforme mencionado acima, pode haver aplicativos que ainda não foram gerenciados por sua organização até agora.  Como parte do processo de estoque, é possível localizar os aplicativos de nuvem unsanctioned. Consulte [Localizando aplicativos em nuvem unsanctioned com detecção de aplicativo de nuvem](active-directory-cloudappdiscovery-whatis.md).

### <a name="authentication-types"></a>Tipos de autenticação
Cada um dos seus aplicativos pode ter requisitos de autenticação diferentes. Com o Azure AD, certificados de assinatura podem ser usado com aplicativos que usam SAML 2.0, Web Services Federation, ou OpenID conectar protocolos bem como senha de logon único. Para obter mais informações sobre o aplicativo tipos de autenticação para uso com o Azure AD consulte [Gerenciando certificados para federado Single Sign-On no Active Directory do Azure](active-directory-sso-certs.md) e [senha baseia o logon único](active-directory-appssoaccess-whatis.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Habilitando o SSO com o Proxy de aplicativo do Azure AD
Com o Proxy de aplicativo do Microsoft Azure AD, você pode fornecer acesso aos aplicativos localizadas dentro de sua rede privada segura, de qualquer lugar e em qualquer dispositivo. Após ter instalado um conector de proxy do aplicativo em seu ambiente, ele pode ser facilmente configurado com o Azure AD.

### <a name="integrating-applications-with-azure-ad"></a>Integração de aplicativos com o Azure AD
Os artigos a seguir discutem as maneiras diferentes aplicativos integram com o Azure AD e fornecem algumas diretrizes.

- [Determinar quais Active Directory para usar](active-directory-administer.md)
- [Usando aplicativos na Galeria de aplicativo do Azure](active-directory-appssoaccess-whatis.md)
- [Integração de lista de tutoriais de aplicativos SaaS](active-directory-saas-tutorial-list.md)

## <a name="managing-access-to-applications"></a>Gerenciando o acesso aos aplicativos
Os artigos a seguir descrevem métodos que você pode gerenciar o acesso aos aplicativos depois que eles foram integrados com o Azure AD usando Azure AD conectores e Azure AD.

- [Gerenciando o acesso aos aplicativos usando o Azure AD](active-directory-managing-access-to-apps.md)
- [Automatizar com conectores de Azure AD](active-directory-saas-app-provisioning.md)
- [Atribuir usuários a um aplicativo](active-directory-applications-guiding-developers-assigning-users.md)
- [Atribuindo grupos para um aplicativo](active-directory-applications-guiding-developers-assigning-groups.md)
- [Compartilhamento de contas](active-directory-sharing-accounts.md)

## <a name="integrating-custom-applications"></a>Integração de aplicativos personalizados
Se você estiver escrevendo um novo aplicativo e deseja ajudar os desenvolvedores na aproveitando a potência Azure AD, consulte [Guiding desenvolvedores](active-directory-applications-guiding-developers-for-lob-applications.md).

Se você quiser adicionar seu aplicativo personalizado para a Galeria de aplicativo do Azure, consulte ["Trazer seu próprio aplicativo" com a configuração de autoatendimento do Azure AD SAML](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

## <a name="see-also"></a>Consulte também

- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
