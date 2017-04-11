<properties
    pageTitle="Alterar o nome ou o logotipo de um aplicativo empresarial na visualização do Active Directory do Azure | Microsoft Azure"
    description="Como alterar o nome ou o logotipo para um aplicativo de empresa personalizados no Active Directory do Azure"
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

# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory-preview"></a>Alterar o nome ou o logotipo de um aplicativo empresarial na visualização do Active Directory do Azure

É fácil alterar o nome ou o logotipo de um aplicativo de empresa personalizado na visualização do Azure Active Directory (AD Azure). [O que é no preview?](active-directory-preview-explainer.md) Você deve ter as permissões apropriadas para fazer essas alterações. Na visualização atual, você deve ser o criador do aplicativo personalizado.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Como alterar nome ou o logotipo de um aplicativo de enterprise?

1. Entrar no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2. Selecione **mais serviços**, insira o **Azure Active Directory** na caixa de texto e, em seguida, pressione **Enter**.

3. Sobre o * *Active Directory do Azure - *directoryname* ** blade (ou seja, a Azure AD lâmina para o diretório que você está gerenciando), selecione **Enterprise aplicativos * *.

    ![Aplicativos empresariais de abertura](./media/active-directory-coreapps-change-app-logo-azure-portal/open-enterprise-apps.png)

4. Na lâmina **aplicativos corporativos** , selecione **todos os aplicativos**. Você verá uma lista dos aplicativos que você pode gerenciar.

5. Na lâmina **aplicativos corporativos - todos os aplicativos** , selecione um aplicativo.

6. Na lâmina ***appname*** (ou seja, a lâmina com o nome do aplicativo selecionado no título), selecione **Propriedades**.

    ![Selecionando o comando Propriedades](./media/active-directory-coreapps-change-app-logo-azure-portal/select-app.png)

7. Sobre a ***appname*** **-Propriedades** blade, procure um arquivo usar como um novo logotipo ou editar o nome do aplicativo, ou ambos.

    ![Alterando o comando de logotipo ou nameproperties do aplicativo](./media/active-directory-coreapps-change-app-logo-azure-portal/change-logo.png)

8. Selecione o comando **Salvar** .

## <a name="next-steps"></a>Próximas etapas

- [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
- [Atribuir um usuário ou grupo para um aplicativo empresarial](active-directory-coreapps-assign-user-azure-portal.md)
- [Remover uma atribuição de usuário ou grupo de um aplicativo empresarial](active-directory-coreapps-remove-assignment-azure-portal.md)
- [Desabilitar suplementos entrada de usuário de um aplicativo empresarial](active-directory-coreapps-disable-app-azure-portal.md)
