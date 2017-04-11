<properties
    pageTitle="Remover uma atribuição de usuário ou grupo de um aplicativo de empresa na visualização do Active Directory do Azure | Microsoft Azure"
    description="Como remover a atribuição de acesso de um usuário ou grupo de um aplicativo de empresa no Active Directory do Azure"
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
    ms.date="09/30/2016"
    ms.author="curtand"/>


# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory-preview"></a>Remover um usuário ou uma atribuição de grupo de um aplicativo de empresa na visualização do Active Directory do Azure

É fácil remover um usuário ou um grupo de sendo atribuído acesso a um de seus aplicativos corporativos na visualização do Azure Active Directory (AD Azure). [O que é no preview?](active-directory-preview-explainer.md) Você deve ter as permissões apropriadas para gerenciar o aplicativo de empresa. Na visualização atual, você deve ser administrador global do diretório.

## <a name="how-do-i-remove-a-user-or-group-assignment"></a>Como remover um usuário ou uma atribuição de grupo?

1. Entrar no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2. Selecione **mais serviços**, insira o **Azure Active Directory** na caixa de texto e, em seguida, pressione **Enter**.

3. Sobre o * *Active Directory do Azure - *directoryname* ** blade (ou seja, a Azure AD lâmina para o diretório que você está gerenciando), selecione **Enterprise aplicativos * *.

    ![Aplicativos empresariais de abertura](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)

4. Na lâmina **aplicativos corporativos** , selecione **todos os aplicativos**. Você verá uma lista dos aplicativos que você pode gerenciar.

5. Na lâmina **aplicativos corporativos - todos os aplicativos** , selecione um aplicativo.

6. Na lâmina ***appname*** (ou seja, a lâmina com o nome do aplicativo selecionado no título), selecione **usuários e grupos**.

    ![Selecionar usuários ou grupos](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)

7. Sobre a ***appname*** **-usuário & atribuição de grupo** lâmina, selecione uma das mais usuários ou grupos e selecione o comando **Remove** . Confirme sua decisão no prompt.

    ![Selecionando o comando Remover](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="next-steps"></a>Próximas etapas

- [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
- [Atribuir um usuário ou grupo para um aplicativo empresarial](active-directory-coreapps-assign-user-azure-portal.md)
- [Desabilitar suplementos entrada de usuário de um aplicativo empresarial](active-directory-coreapps-disable-app-azure-portal.md)
- [Alterar o nome ou o logotipo de um aplicativo empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)
