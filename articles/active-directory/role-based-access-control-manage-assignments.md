<properties
    pageTitle="Exibir atribuições de acesso do recurso Azure | Microsoft Azure"
    description="Exibir e gerenciar todas as atribuições de controle de acesso baseado em função para qualquer usuário ou grupo no portal do Azure"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="jeffsta"/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="view-access-assignments-for-users-and-groups-in-the-azure-portal---public-preview"></a>Exibir as atribuições de acesso para usuários e grupos no portal do Azure - demonstração pública

> [AZURE.SELECTOR]
- [Gerenciar o acesso por usuário ou grupo](role-based-access-control-manage-assignments.md)
- [Gerenciar o acesso por recurso](role-based-access-control-configure.md)

Com baseado em função acesso controle (RBAC) na visualização do Azure Active Directory, você pode gerenciar o acesso aos seus recursos Azure. [O que é no preview?](active-directory-preview-explainer.md)

Acesso atribuído com RBAC é refinado porque você pode restringir as permissões de duas maneiras:

- **Escopo:** Atribuições de função RBAC têm como escopo uma assinatura específica, grupo de recursos ou recurso. Um usuário concedido acesso a um único recurso não pode acessar outros recursos na mesma assinatura.
- **Função:** Dentro do escopo da atribuição, o access é restringido ainda mais atribuindo uma função. Funções podem ser alto nível, como o proprietário ou específico, como o leitor de máquina virtual.

Funções só podem ser atribuídas de dentro da assinatura, grupo de recursos ou recurso que é o escopo da atribuição. Mas você pode exibir todas as atribuições de acesso para um determinado usuário ou grupo em um único local.

Obter mais informações sobre como [Usar atribuições de função para gerenciar o acesso aos recursos da sua assinatura Azure](role-based-access-control-configure.md).

##  <a name="view-access-assignments"></a>Exibir atribuições de acesso

Para procurar as atribuições de acesso para um único usuário ou grupo, inicie no Active Directory do Azure no [portal do Azure](http://portal.azure.com).

1. Selecione o **Active Directory do Azure**. Se essa opção não estiver visível na sua lista de navegação, selecione **Mais serviços** e role para baixo para localizar **Azure Active Directory**.
2. Selecione **usuários e grupos**e, em seguida, em **todos os usuários** ou **todos os grupos**. Neste exemplo, vamos focalizar usuários individuais.
    ![Gerenciar usuários e grupos do Active Directory do Azure - captura de tela](./media/role-based-access-control-manage-assignments/rbac_users_groups.png)
3. Procure o usuário por nome ou o nome de usuário.
4. Selecione **recursos Azure** na lâmina usuário. Todas as atribuições de acesso para esse usuário aparecem.

### <a name="read-permissions-to-view-assignments"></a>Permissões de leitura para exibir as atribuições

Esta página mostra apenas as atribuições de acesso que você tem permissão de leitura. Por exemplo, você tem acesso de leitura a assinatura A e ir para a lâmina de recursos Azure para verificar as atribuições de um usuário. Você pode ver suas atribuições de acesso para A assinatura, mas não verá que ela também possui atribuições do access na assinatura B.

## <a name="delete-access-assignments"></a>Excluir atribuições de acesso

Deste lâmina, você pode excluir as atribuições de acesso que foram atribuídas diretamente para um usuário ou grupo. Se a atribuição do access foi herdada de um grupo pai, você precisa ir para o recurso ou a assinatura e gerenciar a atribuição nesse local.

1. Na lista de todas as atribuições de acesso para um usuário ou grupo, selecione aquele que você deseja excluir.
2. Selecione **Remover** e **Sim** para confirmar.
    ![Remover a atribuição de acesso - captura de tela](./media/role-based-access-control-manage-assignments/delete_assignment.png)

## <a name="related-topics"></a>Tópicos relacionados

- Introdução ao controle de acesso baseado em função ao [Usar atribuições de função para gerenciar o acesso aos recursos da sua assinatura do Azure](role-based-access-control-configure.md)
- Ver as [funções internas RBAC](role-based-access-built-in-roles.md)
