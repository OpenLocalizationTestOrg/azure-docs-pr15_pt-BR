<properties
    pageTitle="Adicionar novos usuários a visualização do Active Directory do Azure | Microsoft Azure"
    description="Explica como adicionar novos usuários ou alterar informações do usuário no Active Directory do Azure."
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


# <a name="add-new-users-to-azure-active-directory-preview"></a>Adicionar novos usuários a visualização do Active Directory do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-users-create-azure-portal.md)
- [Azure portal clássico](active-directory-create-users.md)

Este artigo explica como adicionar novos usuários em sua organização na visualização do Azure Active Direstory (Azure AD). [O que é no preview?](active-directory-preview-explainer.md)

1.  Entrar no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2.  Selecione **mais serviços**, insira os **usuários e grupos** na caixa de texto e, em seguida, pressione **Enter**.

    ![Gerenciamento de usuários de abertura](./media/active-directory-users-create-azure-portal/create-users-user-management.png)

3.  Na lâmina **usuários e grupos** , selecione **todos os usuários**e selecione **Adicionar**.

    ![Selecionando o comando Adicionar](./media/active-directory-users-create-azure-portal/create-users-add-command.png)

4.  Insira os detalhes do usuário, como **nome** e o **nome de usuário**. A parte do nome de domínio do nome de usuário deve ser o nome de domínio de nome de domínio "foo.onmicrosoft.com" inicial padrão ou um nome de domínio verificado não federado, como "contoso.com".

5. Copie ou anote caso contrário, a senha de usuário gerada para que você possa fornecê-lo ao usuário depois esse processo for concluído.

6. Opcionalmente, você pode abrir e preencha as informações na lâmina de **perfil** , a lâmina de **grupos** ou a lâmina de **função de diretório** para o usuário. Para obter mais informações sobre funções de usuário e administrador, consulte [Atribuindo funções de administrador no Azure AD](active-directory-assign-admin-roles.md).

7.  Na lâmina **usuário** , selecione **criar**.

8. Distribua com segurança a senha gerada para o novo usuário para que o usuário pode entrar.

## <a name="whats-next"></a>Qual é a próxima

- [Adicionar um usuário externo](active-directory-users-create-external-azure-portal.md)
- [Redefinir a senha de um usuário no novo portal do Azure](active-directory-users-reset-password-azure-portal.md)
- [Alterar as informações de trabalho de um usuário](active-directory-users-work-info-azure-portal.md)
- [Gerenciar perfis de usuário](active-directory-users-profile-azure-portal.md)
- [Excluir um usuário no seu Azure AD](active-directory-users-delete-user-azure-portal.md)
- [Atribuir um usuário a uma função no seu Azure AD](active-directory-users-assign-role-azure-portal.md)
