<properties
    pageTitle="Adicionar usuários de outros diretórios ou parceiro empresas na visualização do Active Directory do Azure | Microsoft Azure"
    description="Explica como adicionar usuários ou alterar informações do usuário no Active Directory do Azure, incluindo os usuários externos e convidados."
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

# <a name="add-users-from-other-directories-or-partner-companies-in-azure-active-directory-preview"></a>Adicionar usuários de outros diretórios ou empresas de parceiro na visualização do Active Directory do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-users-create-external-azure-portal.md)
- [Azure portal clássico](active-directory-create-users-external.md)

Este artigo explica como adicionar usuários de outros diretórios na visualização do Azure Active Directory (AD Azure) ou de empresas de parceiro. [O que é no preview?](active-directory-preview-explainer.md) Para obter informações sobre como adicionar novos usuários em sua organização e adicionar usuários que têm contas da Microsoft, consulte [Adicionar novos usuários ao Azure Active Directory](active-directory-users-create-azure-portal.md). Usuários adicionados não tem permissões de administrador por padrão, mas você pode atribuir funções-los a qualquer momento.

## <a name="add-a-user"></a>Adicionar um usuário

1.  Entrar no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2.  Selecione **mais serviços**, insira os **usuários e grupos** na caixa de texto e, em seguida, pressione **Enter**.

    ![Gerenciamento de usuários de abertura](./media/active-directory-users-create-external-azure-portal/create-users-user-management.png)

3.  Na lâmina **usuários e grupos** , selecione **usuários**e selecione **Adicionar**.

    ![Selecionando o comando Adicionar](./media/active-directory-users-create-external-azure-portal/create-users-add-command.png)

4. Na lâmina **usuário** , forneça um nome de exibição no **nome** e o nome do usuário entrar em **nome de usuário**.

5. Copie ou anote caso contrário, a senha do usuário gerado para que você possa fornecê-lo ao usuário depois esse processo for concluído.

6. Opcionalmente, selecione **perfil** para adicionar os usuários primeiro nome e o sobrenome, um cargo e um nome de departamento.
    
    ![Abrindo o perfil de usuário](./media/active-directory-users-create-external-azure-portal/create-users-user-profile.png)

    - Selecione **grupos** para adicionar o usuário a um ou mais grupos.

        ![Adicionar um usuário a grupos](./media/active-directory-users-create-external-azure-portal/create-users-user-groups.png)

    - Selecione a **função organizacional** para atribuir o usuário a uma função na lista de **funções** . Para obter mais informações sobre funções de usuário e administrador, consulte [Atribuindo funções de administrador no Azure AD](active-directory-assign-admin-roles.md).

        ![Atribuir um usuário a uma função](./media/active-directory-users-create-external-azure-portal/create-users-assign-role.png)

7. Selecione **criar**.

8. Distribua com segurança a senha gerada para o novo usuário para que o usuário pode entrar.

> [AZURE.IMPORTANT] Se sua organização usar mais de um domínio, você deve saber sobre os seguintes problemas ao adicionar uma conta de usuário:
>
> - Para adicionar contas de usuário mesmo nome de usuário principal (UPN) em domínios, **primeiro** adicione, por exemplo, geoffgrisso@contoso.onmicrosoft.com, **seguido por** geoffgrisso@contoso.com.
> - **Não** adicionar geoffgrisso@contoso.com antes de adicionar geoffgrisso@contoso.onmicrosoft.com. Nesta ordem é importante e pode ser complicada para desfazer.

Se você alterar informações para um usuário cuja identidade é sincronizada com o serviço do Active Directory local, você não pode alterar as informações do usuário no portal de clássico do Azure. Para alterar as informações do usuário, use suas ferramentas de gerenciamento do Active Directory local.


## <a name="whats-next"></a>Qual é a próxima

- [Adicionar um usuário](active-directory-users-create-azure-portal.md)
- [Redefinir a senha de um usuário no novo portal do Azure](active-directory-users-reset-password-azure-portal.md)
- [Atribuir um usuário a uma função no seu Azure AD](active-directory-users-assign-role-azure-portal.md)
- [Alterar as informações de trabalho de um usuário](active-directory-users-work-info-azure-portal.md)
- [Gerenciar perfis de usuário](active-directory-users-profile-azure-portal.md)
- [Excluir um usuário no seu Azure AD](active-directory-users-delete-user-azure-portal.md)
