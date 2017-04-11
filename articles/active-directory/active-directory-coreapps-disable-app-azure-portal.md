<properties
    pageTitle="Desabilitar suplementos entrada de usuário para um aplicativo do enterprise na visualização do Active Directory do Azure | Microsoft Azure"
    description="Como desabilitar um aplicativo empresarial para que nenhum usuário pode entrar para ele no Active Directory do Azure"
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
    ms.date="10/17/2016"
    ms.author="curtand"/>


# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory-preview"></a>Desabilitar suplementos entrada de usuário para um aplicativo do enterprise na visualização do Active Directory do Azure

É fácil desabilitar um aplicativo empresarial para que nenhum usuário pode entrar para ele na visualização do Azure Active Directory (AD Azure). [O que é no preview?](active-directory-preview-explainer.md) Você deve ter as permissões apropriadas para gerenciar o aplicativo de empresa. Na visualização atual, você deve ser administrador global do diretório.

## <a name="how-do-i-disable-user-sign-ins"></a>Como posso desabilitar suplementos entrada de usuário?

1. Entrar no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2. Selecione **mais serviços**, insira o **Azure Active Directory** na caixa de texto e, em seguida, pressione **Enter**.

3. Sobre o * *Active Directory do Azure - *directoryname* ** blade (ou seja, a Azure AD lâmina para o diretório que você está gerenciando), selecione **Enterprise aplicativos * *.

    ![Aplicativos empresariais de abertura](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)

4. Na lâmina **aplicativos corporativos** , selecione **todos os aplicativos**. Você verá uma lista dos aplicativos que você pode gerenciar.

5. Na lâmina **aplicativos corporativos - todos os aplicativos** , selecione um aplicativo.

6. Na lâmina ***appname*** (ou seja, a lâmina com o nome do aplicativo selecionado no título), selecione **Propriedades**.

    ![Selecionando o comando de aplicativos tudo](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)

7. Sobre a ***appname*** **-Propriedades** lâmina, selecione **não** para **habilitados para usuários entrar?**.

8. Selecione o comando **Salvar** .

## <a name="next-steps"></a>Próximas etapas

- [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
- [Atribuir um usuário ou grupo para um aplicativo empresarial](active-directory-coreapps-assign-user-azure-portal.md)
- [Remover uma atribuição de usuário ou grupo de um aplicativo empresarial](active-directory-coreapps-remove-assignment-azure-portal.md)
- [Alterar o nome ou o logotipo de um aplicativo empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)
