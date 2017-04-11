<properties
    pageTitle="Atribuir um usuário ou grupo para um aplicativo empresarial na visualização do Active Directory do Azure | Microsoft Azure"
    description="Como selecionar um aplicativo empresarial para atribuir um usuário ou grupo para ele no Active Directory do Azure"
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
    ms.date="10/03/2016"
    ms.author="curtand"/>

# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory-preview"></a>Atribuir um usuário ou grupo para um aplicativo empresarial na visualização do Active Directory do Azure

É fácil atribuir um usuário ou grupo para os aplicativos corporativos na visualização do Azure Active Directory (AD Azure). [O que é no preview?](active-directory-preview-explainer.md) Você deve ter as permissões apropriadas para gerenciar o aplicativo de empresa. Na visualização atual, você deve ser administrador global do diretório.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app"></a>Como é possível atribuir o acesso do usuário a um aplicativo do enterprise?

1. Entrar no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2. Selecione **mais serviços**, insira o Azure Active Directory na caixa de texto e, em seguida, pressione **Enter**.

3. Sobre o * *Active Directory do Azure - *directoryname* ** blade (ou seja, a Azure AD lâmina para o diretório que você está gerenciando), selecione **Enterprise aplicativos * *.

    ![Aplicativos empresariais de abertura](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)

4. Na lâmina **aplicativos corporativos** , selecione **todos os aplicativos**. Você verá uma lista dos aplicativos que você pode gerenciar.

5. Na lâmina **aplicativos corporativos - todos os aplicativos** , selecione um aplicativo.

6. Na lâmina ***appname*** (ou seja, a lâmina com o nome do aplicativo selecionado no título), selecione **usuários e grupos**.

    ![Selecionando o comando de aplicativos tudo](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)

7. Sobre a ***appname*** **-usuário & atribuição de grupo** lâmina, selecione o comando **Adicionar** .

8. Na lâmina **Adicionar atribuição** , selecione **usuários e grupos**.

    ![Atribuir um usuário ou grupo para o aplicativo](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)

9. Na lâmina **usuários e grupos** , selecione um ou mais usuários ou grupos na lista e selecione o botão de **seleção** na parte inferior da lâmina.

10. Na lâmina **Adicionar atribuição** , selecione a **função**. Em seguida, na lâmina **Selecione função** , selecione uma função para aplicar a usuários ou grupos selecionados e selecione o botão de **Okey** na parte inferior da lâmina.

11. Na lâmina **Adicionar atribuição** , selecione o botão **atribuir** na parte inferior da lâmina. Os usuários atribuídos ou grupos terão as permissões definidas pela função selecionada para este aplicativo enterprise.

## <a name="next-steps"></a>Próximas etapas

- [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
- [Remover uma atribuição de usuário ou grupo de um aplicativo empresarial](active-directory-coreapps-remove-assignment-azure-portal.md)
- [Desabilitar suplementos entrada de usuário de um aplicativo empresarial](active-directory-coreapps-disable-app-azure-portal.md)
- [Alterar o nome ou o logotipo de um aplicativo empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)
