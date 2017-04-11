<properties
    pageTitle="Atribuir um usuário a funções de administrador na visualização do Active Directory do Azure | Microsoft Azure"
    description="Explica como alterar informações administrativas do usuário no Active Directory do Azure"
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
    ms.date="09/12/2016"
    ms.author="curtand"/>

# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory-preview"></a>Atribuir um usuário a funções de administrador na visualização do Active Directory do Azure

Este artigo explica como atribuir uma função administrativa a um usuário na visualização do Azure Active Directory (AD Azure). [O que é no preview?](active-directory-preview-explainer.md) Para obter informações sobre como adicionar novos usuários em sua organização, consulte [Adicionar novos usuários ao Azure Active Directory](active-directory-users-create-azure-portal.md). Usuários adicionados não tem permissões de administrador por padrão, mas você pode atribuir funções-los a qualquer momento.

## <a name="assign-a-role-to-a-user"></a>Atribuir uma função a um usuário

1.  Entrar no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2.  Selecione **mais serviços**, insira os **usuários e grupos** na caixa de texto e, em seguida, pressione **Enter**.

    ![Gerenciamento de usuários de abertura](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)

3.  Na lâmina **usuários e grupos** , selecione **todos os usuários**.

    ![Abrindo a todos os lâmina de usuários](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)

4. Na lâmina **usuários e grupos - todos os usuários** , selecione um usuário na lista.

5. Na lâmina para o usuário selecionado, selecione a **função Directory**e atribua o usuário a uma função na lista de **funções de diretório** . Para obter mais informações sobre funções de usuário e administrador, consulte [Atribuindo funções de administrador no Azure AD](active-directory-assign-admin-roles.md).

      ![Atribuir um usuário a uma função](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)

6. Selecione **Salvar**.


## <a name="whats-next"></a>Qual é a próxima

- [Adicionar um usuário](active-directory-users-create-azure-portal.md)
- [Redefinir a senha de um usuário no novo portal do Azure](active-directory-users-reset-password-azure-portal.md)
- [Alterar as informações de trabalho de um usuário](active-directory-users-work-info-azure-portal.md)
- [Gerenciar perfis de usuário](active-directory-users-profile-azure-portal.md)
- [Excluir um usuário no seu Azure AD](active-directory-users-delete-user-azure-portal.md)
