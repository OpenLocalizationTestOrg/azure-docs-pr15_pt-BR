<properties
    pageTitle="Acesso condicional do Active Directory do Azure perguntas Frequentes | Microsoft Azure"
    description="Perguntas frequentes sobre acesso condicional "
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-conditional-access-faq"></a>Acesso condicional do Active Directory do Azure perguntas Frequentes

## <a name="which-applications-work-with-conditional-access-policies"></a>Quais aplicativos funcionam com políticas de acesso condicional?

**A:** Consulte o tópico, [condicionais aplicativos do access-quais são suportados](active-directory-conditional-access-supported-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Políticas de acesso condicional são impostas para colaboração B2B e usuários convidados?

**A:** Políticas são impostas para usuários de colaboração B2B. No entanto, em alguns casos, um usuário talvez não consiga satisfazer o requisito de política se, por exemplo, uma organização não dá suporte a autenticação multifator. 

A política não será aplicada no momento para usuários de convidados do SharePoint. A relação de convidado é mantida dentro do SharePoint. Usuários convidados contas não estão sujeitos a acesso políticas no servidor de autenticação. Acesso de convidado pode ser gerenciado no SharePoint.

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Se uma política do SharePoint Online também aplica SkyDrive Pro?

**A:** Sim.
 
## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Por que não é possível definir uma política em aplicativos cliente, como o Word ou o Outlook?

**A:** Uma política de acesso condicional define requisitos para acessar um serviço e é imposta quando autenticação acontece nesse serviço. A política não é definida diretamente em um aplicativo cliente; em vez disso, ela é aplicada quando ele discar para um serviço. Por exemplo, uma política definida no SharePoint se aplica aos clientes chamando do SharePoint e uma conjunto de políticas no Exchange aplica-se ao Outlook.


## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Uma política de acesso condicional se aplica contas de serviço?

**A:** Políticas de acesso condicional se aplicam a todas as contas de usuário. Isso inclui contas de usuário usadas como contas de serviço. Em muitos casos, uma conta de serviço que executa o autônoma não é capaz de atender a uma política. Isto é, por exemplo o caso, quando MFA é necessária. Nesses casos, contas de serviços podem ser excluídas de uma política, usando configurações de gerenciamento de política de acesso condicional. Saiba mais sobre como aplicar uma política para usuários aqui.
