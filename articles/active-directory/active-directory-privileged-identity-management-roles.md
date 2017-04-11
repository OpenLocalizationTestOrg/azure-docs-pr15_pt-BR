<properties
   pageTitle="Funções no Gerenciador | Microsoft Azure"
   description="Saiba quais funções são usadas para identidades privilegiadas com a extensão de gerenciamento de identidades privilegiado do Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# <a name="roles-in-azure-ad-privileged-identity-management"></a>Funções no gerenciamento de identidades do Azure AD privilegiado

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Você pode atribuir usuários de sua organização diferentes funções administrativas no Azure AD. Essas atribuições de função controlam quais tarefas, como adicionar ou remover usuários ou alterar configurações de serviço, os usuários poderão executar no Azure AD, Office 365 e outros serviços Online da Microsoft e aplicativos conectados.  

Um administrador global pode atualizar quais usuários estão **permanentemente** atribuídas às funções no Azure AD, usando cmdlets do PowerShell como `Add-MsolRoleMember` e `Remove-MsolRoleMember`, ou por meio do portal clássico como descrito em [atribuir funções de administrador no Active Directory do Azure](active-directory-assign-admin-roles.md).

Gerenciamento de identidades do Azure AD privilegiado (PIM) gerencia políticas para acesso privilegiado para usuários do Azure AD. Gerenciador atribui usuários para uma ou mais funções no Azure AD e você pode atribuir alguém para ser permanentemente na função, ou qualificado para a função. Quando um usuário permanentemente é atribuído a uma função ou ativa uma atribuição de função qualificada, depois que eles possam gerenciar o Active Directory do Azure, Office 365 e outros aplicativos com as permissões atribuídas às suas funções.

Não há nenhuma diferença na acessar atribuído a alguém com uma permanente versus uma atribuição de função qualificado. A única diferença é que algumas pessoas não é necessário que o access o tempo todo. Eles são feitos qualificados para a função e podem ativá-la e desativar sempre que precisam.

## <a name="roles-managed-in-pim"></a>Gerenciado no Gerenciador de funções

Gerenciamento de identidades privilegiados permite que você atribuir usuários às funções de administrador comuns, incluindo:


- **Administrador global** (também conhecido como administrador da empresa) tem acesso a todos os recursos administrativos. Você pode ter mais de um administrador global em sua organização. A pessoa que se inscreve para comprar o Office 365 automaticamente se torna administrador global
- **Administrador da função privilegiados** gerencia Azure AD Gerenciador e atualiza atribuições de função para outros usuários.  
- **Administrador de cobrança** faz compras, gerencia assinaturas, gerencia tíquetes de suporte e monitora a integridade do serviço.
- **Administrador de senha** redefine senhas, gerencia solicitações de serviço e monitora a integridade do serviço. Administradores de senha limitam-se a redefinição de senhas de usuários.
- **Administrador do serviço** gerencia solicitações de serviço e monitora integridade do serviço.

  > [AZURE.NOTE] Se você estiver usando o Office 365, antes de atribuir a função de administrador de serviço para um usuário, primeiro atribua o usuário permissões administrativas para um serviço, como o Exchange Online.

- **Administrador de gerenciamento de usuário** redefine senhas, monitora a integridade do serviço e gerencia contas de usuário, grupos de usuários e solicitações de serviço. O administrador de gerenciamento de usuário não pode excluir um administrador global, criar outras funções de administrador ou redefinir senhas de cobrança, globais e de serviço.
- **Administrador do Exchange** tem acesso administrativo ao Exchange Online por meio do Centro de administração do Exchange (EAC) e pode executar quase todas as tarefas que no Exchange Online.
- **Administrador do SharePoint** tem acesso administrativo ao SharePoint Online por meio do Centro de administração do SharePoint Online e pode executar quase todas as tarefas no SharePoint Online.
- **Skype para o administrador de negócios** tem acesso administrativo ao Skype for Business por meio do Skype para o Centro de administração de negócios e pode executar quase todas as tarefas no Skype for Business Online.

Leia estes artigos para obter mais detalhes sobre como [atribuir funções de administrador no Azure AD](active-directory-assign-admin-roles.md) e [Atribuindo funções de administrador no Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


Do Gerenciador, você pode [atribuir essas funções para um usuário](active-directory-privileged-identity-management-how-to-add-role-to-user.md) para que o usuário pode [Ativar a função quando necessário](active-directory-privileged-identity-management-how-to-activate-role.md).

Se desejar dar a outro usuário acesso para gerenciar no Gerenciador em si, as funções que Gerenciador requer que o usuário tenha são descritas mais detalhadamente em [como dar acesso ao Gerenciador](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Não gerenciadas no Gerenciador de funções

Funções no Exchange Online ou no SharePoint Online, exceto aqueles mencionados acima, não são representadas no Azure AD e então não estão visíveis no Gerenciador. Para obter mais informações sobre como alterar as atribuições de função refinadas desses serviços do Office 365, consulte [permissões no Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Assinaturas do Azure e grupos de recursos também não são representados no Azure AD. Gerenciar assinaturas do Azure, veja [como adicionar ou alterar funções de administrador do Azure](../billing-add-change-azure-subscription-administrator.md) e para obter mais informações sobre Azure RBAC consulte [Controle de acesso de Azure Role-Based](role-based-access-control-configure.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Funções de usuário e entrar
Para alguns aplicativos e serviços da Microsoft, a atribuição de um usuário a uma função pode não ser suficiente para habilitar o usuário ser um administrador.

Acesso ao portal de clássico do Azure requer que o usuário ser um administrador do serviço ou administrador colegas em uma assinatura do Azure, mesmo se o usuário não precisa gerenciar as assinaturas Azure.  Por exemplo, para gerenciar configurações de Azure AD no portal do clássico, um usuário deve ser um administrador global no Azure AD e um administrador de colegas de assinatura em uma assinatura do Azure.  Para saber como adicionar usuários ao Azure assinaturas, veja [como adicionar ou alterar funções de administrador Azure](../billing-add-change-azure-subscription-administrator.md).

Acesso aos Serviços Online da Microsoft pode exigir que o usuário também ser atribuídas uma licença antes que possam abrir do portal do serviço ou executar tarefas administrativas.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Atribuir uma licença a um usuário no Azure AD

1. Entre portal do [Azure clássico] (http://manage.windowsazure.com) com uma conta de administrador global ou uma conta de administrador de colegas.
2. Selecione **Todos os itens** no menu principal.
3. Selecione a pasta que você deseja trabalhar e que tenha licenças associadas a ele.
4. Selecione **licenças**. A lista de licenças disponíveis será exibida.
5. Selecione o plano de licenças que contém as licenças que você deseja distribuir.
6. Selecione **atribuir aos usuários**.
7. Selecione o usuário que você deseja atribuir uma licença.
8. Clique no botão **atribuir** .  O usuário agora pode entrar no Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
