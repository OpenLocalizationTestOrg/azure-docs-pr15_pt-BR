<properties
    pageTitle="Use o controle de acesso baseado em função no portal do Azure | Microsoft Azure"
    description="Introdução no gerenciamento de acesso ao controle de acesso baseado em função no Portal do Azure. Use atribuições de função para atribuir permissões a seus recursos."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="use-role-assignments-to-manage-access-to-your-azure-subscription-resources"></a>Use atribuições de função para gerenciar o acesso aos recursos da sua assinatura do Azure

> [AZURE.SELECTOR]
- [Gerenciar o acesso por usuário ou grupo](role-based-access-control-manage-assignments.md)
- [Gerenciar o acesso por recurso](role-based-access-control-configure.md)

Azure controle de acesso baseado em função (RBAC) permite o gerenciamento de acesso refinados para Azure. Usando RBAC, você pode conceder somente a quantidade de acesso que os usuários precisam para realizar seus trabalhos. Este artigo ajuda você a começar a trabalhar com RBAC no portal do Azure. Se desejar mais detalhes sobre como o RBAC ajuda você a gerenciar o acesso, consulte [o que é o controle de acesso baseado em função](role-based-access-control-what-is.md).

## <a name="view-access"></a>Acesso de modo de exibição
Você pode ver quem tem acesso a um recurso, grupo de recursos ou assinatura da sua lâmina principal no [portal do Azure](https://portal.azure.com). Por exemplo, queremos ver quem tem acesso a um dos nossos grupos de recursos:

1. Selecione **grupos de recursos** na barra de navegação à esquerda.  
    ![Grupos de recursos - ícone](./media/role-based-access-control-configure/resourcegroups_icon.png)
2. Selecione o nome do grupo de recursos da lâmina **grupos de recursos** .
3. Selecione o **controle de acesso (IAM)** no menu à esquerda.  
4. A lâmina de controle de acesso lista todos os usuários, grupos e aplicativos que possui acesso ao grupo de recursos.  

    ![Blade de usuários - vs herdadas atribuídas captura de tela do access](./media/role-based-access-control-configure/view-access.png)

Observe que alguns usuários foram **atribuídas** acessar enquanto outros **herdadas** -lo. Acesso seja atribuído especificamente para o grupo de recursos ou herdado de uma atribuição à assinatura pai.

> [AZURE.NOTE] Administradores de assinatura clássico e administradores de co são considerados proprietários da assinatura no novo modelo de RBAC.


## <a name="add-access"></a>Adicionar acesso
Conceder acesso a partir de dentro do recurso, grupo de recursos ou assinatura que é o escopo da atribuição de função.

1. Selecione **Adicionar** a lâmina de controle de acesso.  
2. Selecione a função que você deseja atribuir da lâmina **Selecione uma função** .
3. Selecione o usuário, o grupo ou o aplicativo no diretório que você deseja conceder acesso a. Você pode pesquisar o diretório com os identificadores de objeto, endereços de email e nomes para exibição.  

    ![Adicionar blade de usuários - pesquisar captura de tela](./media/role-based-access-control-configure/grant-access2.png)

4. Selecione **Okey** para criar a atribuição. Pop-up do **usuário adicionando** rastreia o progresso.  
    ![Adicionando barra de progresso do usuário - captura de tela](./media/role-based-access-control-configure/addinguser_popup.png)

Depois de adicionar com êxito uma atribuição de função, ele aparecerá na lâmina **usuários** .

## <a name="remove-access"></a>Remover acesso

1. Selecione a atribuição de função na lâmina de controle de acesso.
2. Selecione **Remover** a lâmina de detalhes da atribuição.  
3. Selecione **Sim** para confirmar a remoção.  
    ![Blade de usuários - remover de captura de tela de função](./media/role-based-access-control-configure/remove-access1.png)

Atribuições herdadas não podem ser removidas. Na imagem abaixo, observe que o botão Remover estiver desabilitado. Em vez disso, examine os detalhes **Atribuídos em** . Vá para o recurso listado lá para remover a atribuição de função.

![Blade de usuários - desativa o acesso herdadas remove captura de tela do botão](./media/role-based-access-control-configure/remove-access2.png)

## <a name="other-tools-to-manage-access"></a>Outras ferramentas para gerenciar o acesso
Você pode atribuir funções e gerenciar o acesso com comandos do Azure RBAC nas ferramentas diferente de portal do Azure.  Siga os links para saber mais sobre os pré-requisitos e começar a usar os comandos de RBAC do Azure.

- [PowerShell Azure](role-based-access-control-manage-access-powershell.md)
- [Interface de linha de comando Azure](role-based-access-control-manage-access-azure-cli.md)
- [API REST](role-based-access-control-manage-access-rest.md)

## <a name="next-steps"></a>Próximas etapas
- [Criar um relatório de histórico de alteração do access](role-based-access-control-access-change-history-report.md)
- Ver as [funções internas RBAC](role-based-access-built-in-roles.md)
- Definir suas próprias [funções personalizadas no Azure RBAC](role-based-access-control-custom-roles.md)
