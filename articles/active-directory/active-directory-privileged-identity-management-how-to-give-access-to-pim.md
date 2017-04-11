<properties
   pageTitle="Como dar acesso ao Gerenciador | Microsoft Azure"
   description="Saiba como adicionar funções aos usuários com a extensão do Azure Active Directory privilegiados gerenciamento de identidades, para que eles possam gerenciar Gerenciador."
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
   ms.date="09/22/2016"
   ms.author="kgremban"/>

# <a name="how-to-give-access-to-manage-azure-ad-privileged-identity-management"></a>Como dar acesso para gerenciar o gerenciamento de identidades do Azure AD privilegiado

O administrador global que habilita o gerenciamento de identidades do Azure AD privilegiado (PIM) para uma organização automaticamente Obtenha atribuições de função e acesso ao Gerenciador. Ninguém obtém acesso de gravação por padrão, no entanto, incluindo outros administradores globais. Outros administradores globais, administradores de segurança e leitores de segurança têm acesso somente leitura ao Gerenciador do Azure AD. Para conceder acesso ao Gerenciador, o primeiro usuário pode atribuir outras pessoas para a função de **administrador da função privilegiados** . Esta atribuição deve ser feita a partir do Gerenciador em si e não pode ser alterada por meio do PowerShell ou outros portais.

> [AZURE.NOTE] Gerenciar Azure AD Gerenciador requer MFA do Azure. Já que não é possível registrar contas da Microsoft para MFA do Azure, um usuário que assina com uma conta da Microsoft não consegue acessar Gerenciador do Azure AD.

Verifique se há sempre pelo menos dois usuários em uma função de administrador de função privilegiados, caso um usuário está bloqueado ou sua conta é excluída.

## <a name="give-another-user-access-to-manage-pim"></a>Dar a outro usuário acesso para gerenciar Gerenciador

1. Entrar no [portal do Azure](https://portal.azure.com/) e selecione o aplicativo de **gerenciamento de identidades do Azure AD privilegiado** no painel.
2. Selecione **Gerenciar funções privilegiadas** > **administrador da função privilegiados** > **Adicionar**.

    ![Adicionar administradores de função privilegiados - captura de tela][1]

4. Etapa 1 na lâmina adicionar usuários gerenciados, já está concluída. Selecione a etapa 2, **Selecione usuários** e pesquisa para o usuário que você deseja adicionar.

    ![Selecione usuários - captura de tela][2]

6. Selecione o usuário nos resultados da pesquisa e clique em **concluído**.
7. Clique em **Okey** para salvar sua seleção. O usuário que você selecionou aparecerá na lista de administradores de função privilegiados.

    - Sempre que você atribui uma nova função a alguém, eles são configurados automaticamente como qualificadas para ativar a função. Se você quiser torná-las permanentes na função, clique no usuário na lista. Selecione **tornar perm** no menu de informações do usuário.

8. Envie um link de usuário para [Introdução ao gerenciamento de identidades do Azure AD privilegiados](active-directory-privileged-identity-management-getting-started.md).


## <a name="remove-another-users-access-rights-for-managing-pim"></a>Remover direitos de acesso de outro usuário para o gerenciamento de Gerenciador

Antes de remover alguém da função de administrador do função privilegiados, verifique sempre ainda haverá dois usuários atribuídos a ela.

1. No painel Gerenciador, clique na função de **administrador da função privilegiados**.  Na lista de usuários atualmente nessa função será exibida.
2. Clique no usuário na lista de usuários.
3. Clique em **Remover**.  Você recebe uma mensagem de confirmação.
4. Clique em **Sim** para remover o usuário da função.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
