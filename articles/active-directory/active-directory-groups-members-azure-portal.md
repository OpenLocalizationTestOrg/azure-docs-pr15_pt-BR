<properties
    pageTitle="Gerenciar os participantes de um grupo na visualização do Active Directory do Azure | Microsoft Azure"
    description="Como os usuários e dispositivos que são membros de um grupo no Active Directory do Azure"
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


# <a name="manage-the-members-for-a-group-in-azure-active-directory-preview"></a>Gerenciar os participantes de um grupo na visualização do Active Directory do Azure

Este artigo explica como gerenciar os membros de um grupo na visualização do Azure Active Directory (AD Azure). [O que é no preview?](active-directory-preview-explainer.md)

## <a name="how-do-i-find-the-members-and-manage-them"></a>Como localizar os membros e gerenciá-los?

1.  Entrar no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2.  Selecione **mais serviços**, insira os **usuários e grupos** na caixa de texto e, em seguida, pressione **Enter**.

  ![Gerenciamento de usuários de abertura](./media/active-directory-groups-members-azure-portal/search-user-management.png)

3.  Na lâmina **usuários e grupos** , selecione **todos os grupos**.

  ![Abrindo a lâmina de grupos](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)

4. Na lâmina **usuários e grupos - todos os grupos** , selecione um grupo.

5. No *grupo *grupo - ** ** blade, selecione **membros * *.

  ![Abrindo a lâmina de membros](./media/active-directory-groups-members-azure-portal/view-group-members.png)

6. Para adicionar membros ao grupo, no **grupo - membros** blade, selecione **Adicionar membros**.

  ![Adicione um comando de membros](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)

7. Na lâmina **membros** , selecione um ou mais usuários ou dispositivos para adicionar ao grupo e selecione o botão de **seleção** na parte inferior da lâmina para adicioná-los ao grupo. Caixa de **usuário** filtra a exibição com base em sua entrada para qualquer parte de um nome de usuário ou dispositivo de correspondência. Sem caracteres curinga são aceitos nessa caixa.

8. Para remover membros do grupo, no **grupo - membros** blade, selecione um membro.

9. Na lâmina ***nome do membro*** , selecione o comando **Remover** e confirmar sua escolha no prompt.

  ![Remova o comando de membros](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)

9. Quando terminar de alterar os membros do grupo, selecione **Salvar**.


## <a name="additional-information"></a>Informações adicionais

Estes artigos fornecem informações adicionais sobre Active Directory do Azure.

* [Consulte grupos existentes](active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionando participantes](active-directory-groups-create-azure-portal.md)
* [Gerenciar as configurações de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerenciar membros de um grupo](active-directory-groups-membership-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](active-directory-groups-dynamic-membership-azure-portal.md)
