<properties
    pageTitle="Criar um novo grupo na visualização do Active Directory do Azure | Microsoft Azure"
    description="Como criar um grupo no Active Directory do Azure e adicionar usuários (membros) ao grupo"
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


# <a name="create-a-new-group-in-azure-active-directory-preview"></a>Criar um novo grupo na visualização do Active Directory do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-groups-create-azure-portal.md)
- [Azure portal clássico](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)

Este artigo explica como criar e preencher um novo grupo na visualização do Azure Active Directory (AD Azure). [O que é no preview?](active-directory-preview-explainer.md) Use um grupo para realizar tarefas de gerenciamento como a atribuição de licenças ou permissões para um número de usuários ou dispositivos ao mesmo tempo.

## <a name="how-do-i-create-a-group"></a>Como para criar um grupo?

1. Entrar no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2. Selecione **mais serviços**, insira o **usuário e grupos** na caixa de texto e, em seguida, pressione **Enter**.

  ![Gerenciamento de usuários de abertura](./media/active-directory-groups-create-azure-portal/search-user-management.png)

3. Na lâmina **usuários e grupos** , selecione **todos os grupos**.

  ![Abrindo a lâmina de grupos](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)

4. Na lâmina **usuários e grupos - todos os grupos** , selecione o comando **Adicionar** .

  ![Selecionando o comando Adicionar](./media/active-directory-groups-create-azure-portal/add-group-command.png)

5. No **grupo** blade, adicione um nome e uma descrição para o grupo.

6. Para selecionar membros para adicionar ao grupo, selecione **atribuído** na caixa **tipo de associação** e, em seguida, selecione **membros**. Para obter mais informações sobre como gerenciar a associação de um grupo dinamicamente, consulte [usando atributos para criar regras avançadas para associações de grupo](active-directory-groups-dynamic-membership-azure-portal.md).

  ![Selecionando membros para adicionar](./media/active-directory-groups-create-azure-portal/select-members.png)

5. Na lâmina **membros** , selecione um ou mais usuários ou dispositivos para adicionar ao grupo e selecione o botão de **seleção** na parte inferior da lâmina para adicioná-los ao grupo. Caixa de **usuário** filtra a exibição com base em sua entrada para qualquer parte de um nome de usuário ou dispositivo de correspondência. Sem caracteres curinga são aceitos nessa caixa.

6. Quando terminar de adicionar membros ao grupo, selecione **criar** na lâmina **grupo** .    

  ![Criar confirmação de grupo](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)




## <a name="additional-information"></a>Informações adicionais

Estes artigos fornecem informações adicionais sobre Active Directory do Azure.

* [Consulte grupos existentes](active-directory-groups-view-azure-portal.md)
* [Gerenciar as configurações de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerenciar membros de um grupo](active-directory-groups-members-azure-portal.md)
* [Gerenciar membros de um grupo](active-directory-groups-membership-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](active-directory-groups-dynamic-membership-azure-portal.md)
