<properties
    pageTitle="Gerenciar senhas no Active Directory do Azure | Microsoft Azure"
    description="Como gerenciar senhas no Active Directory do Azure."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="curtand"/>

# <a name="manage-passwords-in-azure-active-directory"></a>Gerenciar senhas no Active Directory do Azure

Se você for um administrador, você pode redefinir a senha de um usuário do Azure Active Directory (AD Azure) no portal de clássico do Azure. Clique no nome do seu diretório e na página usuários, clique no nome do usuário e na parte inferior do portal, clique em **Redefinir senha**.

O restante deste tópico aborda o conjunto completo de recursos de gerenciamento de senha Azure AD com suporte, incluindo:

- Alteração de **senha de autoatendimento** permite que os usuários finais ou administradores para alterar suas senhas expiradas ou não expirado sem chamar um administrador ou técnicos de suporte.
- Redefinição de **senha de autoatendimento** permite que os usuários finais ou que os administradores redefinam suas senhas automaticamente sem chamar um administrador ou técnicos de suporte. Redefinição de senha de autoatendimento exige Azure AD Premium ou básico. Para obter mais informações, consulte [edições do Active Directory do Azure](active-directory-editions.md).
- **Redefinição de senha iniciadas pelo administrador** permite que um administrador redefinir a senha de um usuário final ou outro administrador de dentro do portal clássico do Azure.
- **Relatórios de atividades de gerenciamento de senha** dar administradores visões atividade de registro e redefinição de senha ocorrendo em sua organização.
- **Senha write-back** permite o gerenciamento de senhas de locais da nuvem para que todos os cenários acima podem ser executados por, ou em nome de, federado ou senha usuários sincronizados. Senha write-back requer Azure AD Premium. Para obter mais informações, consulte [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md).

> [AZURE.NOTE]
> Azure AD Premium está disponível para clientes chinês usando a instância de todo o mundo do Azure AD. Azure AD Premium não é suportado atualmente no serviço Microsoft Azure operado pela 21Vianet na China. Para obter mais informações, entre em contato no [Fórum do Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

Use os links a seguir para ir para a documentação que mais seu interesse:

- [Visão geral: gerenciamento de senha no Azure AD](active-directory-passwords-how-it-works.md)
- [Senha de autoatendimento Redefinir no Azure AD: como habilitar, configurar e testar a redefinição de senha de autoatendimento](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords)
- [Senha de autoatendimento Redefinir no Azure AD: como personalizar senha Redefinir para atender às suas necessidades](active-directory-passwords-customize.md)
- [Senha de autoatendimento Redefinir no Azure AD: práticas recomendadas de implantação e gerenciamento](active-directory-passwords-best-practices.md)
- [Relatórios de gerenciamento de senha no Azure AD: como exibir atividade de gerenciamento de senha em seu locatário](active-directory-passwords-get-insights.md)
- [Senha write-back: como configurar Azure AD para gerenciar senhas locais](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
- [Solucionando problemas de gerenciamento de senha do Azure AD](active-directory-passwords-troubleshoot.md)
- [Perguntas Frequentes sobre o gerenciamento de senha do Azure AD](active-directory-passwords-faq.md)


## <a name="whats-next"></a>Qual é a próxima

- [Administrando Azure AD](active-directory-administer.md)
- [Criar ou editar usuários no Azure AD](active-directory-create-users.md)
- [Gerenciar grupos no Azure AD](active-directory-manage-groups.md)
