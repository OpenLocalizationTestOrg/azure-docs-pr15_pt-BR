<properties
    pageTitle="Perguntas Frequentes do Active Directory do Azure | Microsoft Azure"
    description="Azure Active Directory perguntas Frequentes que fornece respostas para perguntas em conjunto com acessando Azure e o Azure Active Directory, acesso de gerenciamento e a aplicação de senha."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="markusvi"/>

# <a name="azure-active-directory-faq"></a>Perguntas Frequentes do Active Directory do Azure

Active Directory do Azure é uma identidade abrangente como uma solução de serviço (IDaaS) que se expande por todos os aspectos da identidade, gerenciamento de acesso e segurança.


Para obter mais detalhes, consulte [o que é o Active Directory do Azure?](active-directory-whatis.md).



## <a name="accessing-azure-and-azure-active-directory"></a>Acessando o Azure e Azure Active Directory


**P: por que recebo "sem assinaturas encontrada" ao tentar acessar Azure AD no portal do clássico Azure (https://manage.windowsazure.com)?**

**A:** Acessar o portal de clássico Azure requer que cada usuário tenha permissões em uma assinatura do Azure. Se você tiver uma paga do Office 365 ou Azure AD navegar até [http://aka.ms/accessAAD](http://aka.ms/accessAAD) de uma etapa de ativação única, caso contrário, você precisará ativar um completo [Azure avaliação](https://azure.microsoft.com/pricing/free-trial/) ou uma assinatura paga. 

Para obter mais detalhes, consulte:

- [Como assinaturas Azure são associadas com o Active Directory do Azure](active-directory-how-subscriptions-associated-directory.md)

- [Gerenciar o diretório para sua assinatura do Office 365 no Azure](active-directory-manage-o365-subscription.md)

---

**P: qual é a relação entre Azure AD, Office 365 e Azure?**

**A:** Active Directory do Azure oferece capacidades de identidade e acesso comuns a todos os serviços online da Microsoft. Se você estiver usando o Office 365, o Microsoft Azure, o Intune ou outras pessoas, você já está usando um Azure AD para habilitar o logon e acessar o gerenciamento de todos esses serviços. 

Na verdade, todos os usuários que você tenha habilitado para Serviços Online da Microsoft são definidos como contas de usuário em uma ou mais instâncias do Azure AD. Você pode habilitar esses recursos de contas gratuitamente Azure AD como o acesso à nuvem aplicativo.
 
Além disso, o Azure AD serviços pagos (por exemplo: Azure AD básico, Premium, EMS, etc.) complementar outros serviços como o Office 365 e Microsoft Azure com as soluções de segurança e gerenciamento de escala corporativa abrangente Online.


---



## <a name="getting-started-with-hybrid-azure-ad"></a>Introdução ao híbrido Azure AD


**P: como posso me conectar meu diretório local ao Azure AD?**

**A:** Você pode conectar seu diretório local ao Azure AD usando **Azure AD Connect**. 

Para obter mais detalhes, consulte [integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).


---

**P: como configurar SSO entre meu diretório local e Meus aplicativos de nuvem?**

**A:** Você só precisa configurar SSO entre o seu diretório local e o Azure AD. Enquanto você acessa seus aplicativos em nuvem através de Azure AD, o serviço automaticamente unidades seus usuários para autenticar corretamente com suas credenciais de local.

Implementar o SSO do local pode ser obtido facilmente com soluções de federação como ADFS ou configurando a sincronização de hash de senha. Você pode facilmente implantar ambas as opções usando o Assistente de configuração do Azure AD Connect.
  

Para obter mais detalhes, consulte [integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
  

---

**P: o Active Directory do Azure fornecem um portal de autoatendimento para usuários em minha organização?**

**A:** Sim, Active Directory do Azure oferece o [Painel de acesso do Azure AD](http://myapps.microsoft.com) para o acesso do usuário autoatendimento e aplicativo. Se você for um cliente do Office 365, você pode encontrar muitos dos mesmos recursos no portal do Office 365. 

Para obter mais informações, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 



---

**P: o Azure AD me ajudar gerencie a infraestrutura meu local?**

**A:** Sim, ele tem. A edição do Azure AD Premium oferece **Integridade conectar**. Azure AD conectar integridade ajuda você a monitorar e percepção sua infraestrutura de identidade local e os serviços de sincronização.  

Para obter mais detalhes, consulte [Serviços de infraestrutura e sincronização de identidade de monitorar seu local na nuvem](active-directory-aadconnect-health.md).  

---

## <a name="password-management"></a>Gerenciamento de senha

**P: posso usar Azure AD senha regravação sem sincronização de senha? (Também conhecidos como, eu gostaria de usar o Azure AD SSPR com senha regravação mas não quero que meus senhas armazenadas no cloud?)**

**A:** Você não precisa sincronizar suas senhas AD ao Azure AD para habilitar write-back. Em um ambiente federado, Azure AD SSO depende do diretório local para autenticar o usuário. Este cenário não exige a senha de local a ser controlados no Azure AD.

---

**P: quanto tempo leva uma senha para gravado AD local?**

**A:** Senha regravação opera em tempo real. 

Para obter mais detalhes, consulte [Introdução ao gerenciamento de senha](active-directory-passwords-getting-started.md) 


---

**P: é possível usar senha regravação com senhas que são gerenciadas por um administrador?**

**A:** Sim, se você tiver habilitada regravação de senha, as operações de senha executadas por um administrador são gravadas novamente seu ambiente local.  

Para obter mais respostas a senha perguntas relacionadas, consulte [Senha gerenciamento perguntas frequentes](active-directory-passwords-faq.md).

---

## <a name="application-access"></a>Acesso ao aplicativo


**P: onde encontrar uma lista de aplicativos que estão previamente integrada com o Azure AD e seus recursos?**

**A:** Azure AD tem mais 2600 aplicativos previamente integrados da Microsoft, provedores de serviços de aplicativo ou parceiros. Todos os aplicativos integrados previamente suportam SSO. SSO permite que você use suas credenciais organizacionais para acessar seus aplicativos. Alguns dos aplicativos também oferecem suporte de provisionamento automatizado e eliminação de provisionamento

Para obter uma lista completa de aplicativos integrados previamente, consulte o [Marketplace do Active Directory](https://azure.microsoft.com/marketplace/active-directory/).


---

**P: o que acontece se o aplicativo que necessário não está no mercado Azure AD?**

**A:** Com o Azure AD Premium, você pode adicionar e configurar qualquer aplicativo desejado. Dependendo de recursos do seu aplicativo e suas preferências, você pode configurar SSO e provisionamento automatizado.  

Para obter mais detalhes, consulte:

- [Configurando o logon único para aplicativos que não estão na Galeria de aplicativo do Active Directory do Azure](active-directory-saas-custom-apps.md)
- [Usando SCIM para habilitar o fornecimento automático de usuários e grupos do Active Directory do Azure para aplicativos](active-directory-scim-provisioning.md) 


---

**P: como os usuários entrar em aplicativos usando o Active Directory do Azure?**
 
**A:** Active directory do Azure oferece várias maneiras para usuários ver e acessar seus aplicativos, como:

- O painel de acesso do Azure AD

- O inicializador de aplicativos do Office 365

- Logon direto aos aplicativos federados

- Links profunda para federado, baseada em senha, ou aplicativos existentes

Para obter mais informações, consulte [Implantando Azure AD integrada aplicativos para os usuários](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).


---

**P: quais são as diferentes maneiras Azure Active Directory permite autenticação e logon único para aplicativos?**
 
**A:** Active Directory do Azure oferece suporte a muitos protocolos padronizados para autenticação e autorização como SAML 2.0, OpenID conectar, OAuth 2.0 e Web Services Federation. Azure AD também suporta compartimentos de senha e recursos automatizados de entrada para aplicativos que ofereçam suporte somente a autenticação baseada em formulários.  

Para obter mais informações, consulte:

- [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md)

- [Protocolos de autenticação do Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)

- [Como um único logon com o trabalho do Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)


---

**P: posso adicionar aplicativos estou executando o local?**

**A:** Proxy de aplicativo do Azure AD oferece acesso fácil e seguro aos aplicativos da web no local que você escolher. Você pode acessar esses aplicativos da mesma maneira que você está acessando seus aplicativos SaaS no Active Directory do Azure. Não é necessário para uma VPN ou alterando sua infraestrutura de rede.  

Para obter mais detalhes, consulte [como fornecer acesso remoto seguro para aplicativos de local](active-directory-application-proxy-get-started.md).


--- 

**P: como exigem MFA para usuários acessam um aplicativo específico?**

**A:** Com acesso condicional do Azure AD, você pode atribuir uma política de acesso exclusivo para cada aplicativo. Em sua política, você pode exigir MFA tempo todo ou quando os usuários não estiverem conectados à rede local.  

Para obter mais detalhes, consulte [Protegendo o acesso ao Office 365 e outros aplicativos conectados ao Azure Active Directory](active-directory-conditional-access.md).


---

**P: o que é provisionamento automatizado do usuário para os aplicativos de SaaS?**

**A:** Active Directory do Azure permite automatizar a criação, manutenção e remoção de identidades do usuário em vários aplicativos de nuvem populares (SaaS). 

Para obter mais informações, consulte [automatizar provisionamento do usuário e Deprovisioning para aplicativos SaaS com o Active Directory do Azure](active-directory-saas-app-provisioning.md)

---



