<properties
    pageTitle="Notificações de provisionamento de contas | Microsoft Azure"
    description="Saiba como garantir que você será notificado sobre problemas relacionados ao provisionamento do usuário que exigem sua atenção, permitindo que as notificações de provisionamento de contas."
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
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="account-provisioning-notifications"></a>Notificações de provisionamento de conta

Com provisionamento do usuário, você pode automatizar o processo de gerenciamento de usuários em aplicativos de SaaS de terceiros. <br>
Embora seja um processo automatizado, sua interação com este processo de vez em quando é necessária. <br>
Isto é, por exemplo o caso, quando a senha da conta que você configurou para trocar dados com um terceiro terceiros SaaS aplicativo expirou. 

Habilitando notificações de provisionamento de contas, você pode garantir que você será notificado sobre problemas relacionados ao provisionamento do usuário que exigem sua atenção.

Ativar ou desativar as notificações de provisionamento como parte de sua configuração para terceiros SaaS aplicativo de provisionamento do usuário.

![Provisionamento do usuário][1] 



Para ativar as notificações de provisionamento de contas, selecione a caixa de seleção relacionada na página de diálogo de **confirmação** e digite o alias de email do destinatário.

![Notificações de provisionamento de conta][2]
 


Você pode inserir uma lista de distribuição como destinatário; No entanto, é importante observar que o email de notificação contém links para os relatórios que só são acessíveis pelos administradores do Azure AD.

Se você tiver habilitadas de notificações de provisionamento de contas, você receberá emails sobre problemas críticos relacionados ao provisionamento do usuário. No entanto, para evitar uma sobrecarga de email, você só receberão uma notificação email por dia para cada aplicativo SaaS para que o email de notificação está habilitado.


##<a name="related-articles"></a>Artigos relacionados

- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
- [Automatizar usuário provisionamento/desprovisionamento aos aplicativos SaaS](active-directory-saas-app-provisioning.md)
- [Personalizando os mapeamentos de atributo de provisionamento do usuário](active-directory-saas-customizing-attribute-mappings.md)
- [Escrevendo expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Escopo filtros para provisionamento do usuário](active-directory-saas-scoping-filters.md)
- [Usando SCIM para habilitar o fornecimento automático de usuários e grupos do Active Directory do Azure para aplicativos](active-directory-scim-provisioning.md)
- [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)



<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png