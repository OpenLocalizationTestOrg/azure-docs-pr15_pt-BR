<properties 
    pageTitle="Como gerenciar contas de usuário no gerenciamento de API do Azure | Microsoft Azure" 
    description="Saiba como criar ou convidar usuários no gerenciamento de API do Azure" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Como gerenciar contas de usuário no gerenciamento de API do Azure

Em gerenciamento de API, os desenvolvedores são os usuários das APIs que você expor usando o gerenciamento de API. Este guia mostra como criar e convidar desenvolvedores para usar as APIs e os produtos que você faça disponível para eles com sua instância de gerenciamento de API. Para obter informações sobre como gerenciar contas de usuário programaticamente, consulte a documentação de [entidade de usuário](https://msdn.microsoft.com/library/azure/dn776330.aspx) na referência [API REST de gerenciamento](https://msdn.microsoft.com/library/azure/dn776326.aspx) .

## <a name="create-developer"> </a>Criar um novo desenvolvedor

Para criar um novo desenvolvedor, clique em **Gerenciar** no Portal de clássico do Azure para o serviço de gerenciamento de API. Isso o leva para o portal de fornecedor de gerenciamento de API. Se você ainda não tiver criado uma instância de serviço de gerenciamento de API, consulte [criar uma instância de serviço de gerenciamento de API][] do tutorial de [Introdução ao gerenciamento de API do Azure][] .

![Portal do Publisher][api-management-management-console]

Clique em **usuários** no menu **Gerenciamento de API** à esquerda e clique em **Adicionar usuário**.

![Criar desenvolvedor][api-management-create-developer]

Insira o **Email**, a **senha**e o **nome** para o novo desenvolvedor e clique em **Salvar**.

![Criar desenvolvedor][api-management-add-new-user]

Por padrão, as contas de desenvolvedor recém-criado estão **ativos**e associados ao grupo de **desenvolvedores** .

![Novo desenvolvedor][api-management-new-developer]

Contas de desenvolvedor que estão em um estado **ativo** podem ser usadas para acessar todas as APIs para os quais tenham assinaturas. Para associar o desenvolvedor recém-criado grupos adicionais, consulte [como associar grupos com desenvolvedores][].

## <a name="invite-developer"> </a>Convidar um desenvolvedor

Para convidar um desenvolvedor, clique em **usuários** no menu **Gerenciamento de API** à esquerda e, em seguida, clique em **Convidar usuário**.

![Convidar desenvolvedor][api-management-invite-developer]

Insira o nome e endereço de email do desenvolvedor e clique em **Convidar**.

![Convidar desenvolvedor][api-management-invite-developer-window]

Uma mensagem de confirmação é exibida, mas o desenvolvedor recentemente convidado não aparecer na lista de até após aceitarem o convite. 

![Convidar confirmação][api-management-invite-developer-confirmation]

Quando um desenvolvedor está convidado, um email é enviado para o desenvolvedor. Este e-mail é gerado usando um modelo e é personalizável. Para obter mais informações, consulte [Configurar modelos de email][].

Depois que o convite for aceito, a conta se torna ativa.

## <a name="block-developer"></a> Desativar ou reativar uma conta de desenvolvedor

Por padrão, as contas de desenvolvedor recém-criado ou convidados são **ativa**. Para desativar uma conta de desenvolvedor, clique em **Bloquear**. Para reativar uma conta de desenvolvedor bloqueados, clique em **Ativar**. Uma conta de desenvolvedor bloqueados não pode acessar o portal do desenvolvedor ou chamar qualquer APIs. Para excluir uma conta de usuário, clique em **Excluir**.

![Desenvolvedor do bloco][api-management-new-developer]

## <a name="reset-a-user-password"></a>Redefinir uma senha de usuário

Para redefinir a senha de uma conta de usuário, clique no nome da conta.

![Redefinir senha][api-management-view-developer]

Clique em **Redefinir senha** para enviar um link para o usuário para redefinir sua senha.

![Redefinir senha][api-management-reset-password]

Para trabalhar programaticamente com contas de usuário, consulte a documentação de [entidade de usuário](https://msdn.microsoft.com/library/azure/dn776330.aspx) na referência [API REST de gerenciamento](https://msdn.microsoft.com/library/azure/dn776326.aspx) . Para redefinir a senha de uma conta de usuário para um valor específico, você pode usar a operação de [atualização de um usuário](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) e especificar a senha desejada.

## <a name="pending-verification"></a>Verificação pendente

![Verificação pendente][api-management-pending-verification]

## <a name="next-steps"> </a>Próximos passos

Depois que uma conta de desenvolvedor é criada, você pode associá-lo com funções e inscrever-se aos produtos e APIs. Para obter mais informações, consulte [como criar e usar grupos][].


[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png
[]: ./media/api-management-howto-create-or-invite-developers/.png



[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[Como criar e usar grupos]: api-management-howto-create-groups.md
[Como associar grupos desenvolvedores]: api-management-howto-create-groups.md#associate-group-developer

[Introdução ao gerenciamento de API do Azure]: api-management-get-started.md
[Criar uma instância de serviço de gerenciamento de API]: api-management-get-started.md#create-service-instance
[Configurar modelos de email]: api-management-howto-configure-notifications.md#email-templates