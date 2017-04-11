<properties
    pageTitle="Gerenciar grupos de seu grupo é um membro na visualização do Active Directory do Azure | Microsoft Azure"
    description="Grupos podem conter outros grupos do Active Directory do Azure. Veja aqui como gerenciar as associações."
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


# <a name="manage-the-groups-your-group-is-a-member-of-in-azure-active-directory-preview"></a>Gerenciar grupos de que seu grupo é um membro na visualização do Active Directory do Azure

Grupos podem conter outros grupos na visualização do Active Directory do Azure. [O que é no preview?](active-directory-preview-explainer.md) Veja aqui como gerenciar as associações.

## <a name="how-do-i-find-the-groups-my-group-is-a-member-of"></a>Como posso localizar os grupos que meu grupo é membro?

1.  Entrar no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2.  Selecione **mais serviços**, insira os **usuários e grupos** na caixa de texto e, em seguida, pressione **Enter**.

  ![Gerenciamento de usuários de abertura](./media/active-directory-groups-membership-azure-portal/search-user-management.png)

3.  Na lâmina **usuários e grupos** , selecione **todos os grupos**.

  ![Abrindo a lâmina de grupos](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)

4. Na lâmina **usuários e grupos - todos os grupos** , selecione um grupo.

5. No *nome do *grupo - *grupo* ** blade, selecione **agrupe associações * *.

  ![Abrindo a lâmina de associações de grupo](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)

6. Para adicionar seu grupo como membro de outro grupo, na lâmina **grupo - associações de grupo** , selecione o comando **Adicionar** .

7. Selecione um grupo da lâmina **Selecionar grupo** e selecione o botão **Selecionar** na parte inferior da lâmina. Você pode adicionar seu grupo a apenas um grupo por vez. Caixa de **usuário** filtra a exibição com base em sua entrada para qualquer parte de um nome de usuário ou dispositivo de correspondência. Sem caracteres curinga são aceitos nessa caixa.

  ![Adicionar uma associação de grupo](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)

8. Para remover seu grupo como membro de outro grupo, na lâmina **grupo - associações de grupo** , selecione um grupo.

9. Na lâmina ***nome do grupo*** , selecione o comando **Remover** e confirmar sua escolha no prompt.

  ![Remova o comando de associação](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)

9. Quando terminar de alterar os membros do grupo para seu grupo, selecione **Salvar**.


## <a name="additional-information"></a>Informações adicionais

Estes artigos fornecem informações adicionais sobre Active Directory do Azure.

* [Consulte grupos existentes](active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionando participantes](active-directory-groups-create-azure-portal.md)
* [Gerenciar as configurações de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerenciar membros de um grupo](active-directory-groups-members-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](active-directory-groups-dynamic-membership-azure-portal.md)
