<properties
   pageTitle="Como adicionar ou remover uma função de usuário | Microsoft Azure"
   description="Saiba como adicionar funções a identidades privilegiadas com o aplicativo do Azure Active Directory privilegiados gerenciamento de identidades."
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
   ms.date="10/24/2016"
   ms.author="kgremban"/>

# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure gerenciamento privilegiados de identidade de AD: Como adicionar ou remover uma função de usuário

Com o Azure Active Directory (AD), um administrador global (ou administrador da empresa) pode atualizar quais usuários estão **permanentemente** atribuídas às funções no Azure AD. Isso é feito com cmdlets do PowerShell como `Add-MsolRoleMember` e `Remove-MsolRoleMember`. Ou poderão usar o portal de clássico Azure conforme descrito em [atribuir funções de administrador no Active Directory do Azure](active-directory-assign-admin-roles.md).

O aplicativo de gerenciamento de identidades do Azure AD privilegiados permite que administradores de função privilegiados fazer atribuições de função permanente, também. Além disso, os administradores de função privilegiados podem fazer usuários **qualificada** para funções de administrador. Administrador qualificado pode ativar a função quando eles precisam e então suas permissões expiram depois que terminar.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Gerenciar funções com Gerenciador no portal do Azure

Em sua organização, você pode atribuir usuários a diferentes funções administrativas no Azure AD, aplicativos e serviços do Office 365 e outros Microsoft.  Obter mais detalhes sobre as funções disponíveis podem ser encontradas em [funções no Gerenciador de AD do Azure](active-directory-privileged-identity-management-roles.md).

Para adicionar ou remover um usuário em uma função usando o gerenciamento de identidades privilegiados, abra o painel de Gerenciador. Em seguida, clique no botão de **usuários em funções de administrador** , ou selecione uma função específica (como Administrador Global) da tabela de funções.

> [AZURE.NOTE] Se você ainda não ativou Gerenciador no portal do Azure, vá para [começar a usar o Gerenciador do Azure AD](active-directory-privileged-identity-management-getting-started.md) para obter detalhes.

Se desejar dar a outro usuário acesso ao Gerenciador em si, as funções que Gerenciador requer que o usuário tenha são descritas mais detalhadamente em [como dar acesso ao Gerenciador](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="add-a-user-to-a-role"></a>Adicionar um usuário a uma função

1. No [portal do Azure](https://portal.azure.com/), selecione o bloco de **gerenciamento de identidades do Azure AD privilegiado** no painel.
2. Selecione **Gerenciar funções privilegiadas**.
3. Na tabela a **função de resumo** , selecione a função que você deseja gerenciar.
4. Na lâmina função, selecione **Adicionar**.
5. Clique em **Selecionar usuários** e procure o usuário na lâmina **Selecione usuários** .  
6. Selecione o usuário na lista de resultados da pesquisa e clique em **concluído**.
4. Clique em **Okey** para salvar sua seleção. O usuário que você selecionou aparecerão na lista como qualificado para a função.

> [AZURE.NOTE]
>Novos usuários em uma função só são qualificados para a função por padrão. Se você quiser fazer a função permanente, clique no usuário na lista. As informações do usuário serão exibida em uma nova lâmina. Selecione **tornar perm** no menu de informações do usuário.  
>Se um usuário não pode registrar para autenticação de vários fatores Azure (MFA) ou se estiver usando uma conta da Microsoft (geralmente @outlook.com), você precisa torná-las permanentes em todas as suas funções. Administradores qualificados serão solicitados a registrar para MFA durante a ativação.

Agora que o usuário está qualificado para uma função, avisá-los que eles podem ativá-lo acordo com as instruções em [como ativar ou desativar uma função](active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="remove-a-user-from-a-role"></a>Remover um usuário de uma função

Você pode remover usuários de atribuições de função elegível, mas certificar-se de que sempre há pelo menos um usuário que seja um administrador global permanente.

Siga estas etapas para remover um usuário específico de uma função:

1. Navegue até a função na lista função selecionando uma função no painel Azure AD Gerenciador ou clicando no botão de **usuários em funções de administrador** .
2. Clique no usuário na lista de usuários.
3. Clique em **Remover**. Uma mensagem solicitará que você confirme.
4. Clique em **Sim** para remover a função do usuário.

Se você não tiver certeza de quais usuários ainda precisam de suas atribuições de função, você pode [Iniciar uma revisão de acesso para a função](active-directory-privileged-identity-management-how-to-start-security-review.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
