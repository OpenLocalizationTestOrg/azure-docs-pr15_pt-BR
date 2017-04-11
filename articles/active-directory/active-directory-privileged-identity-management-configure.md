<properties
    pageTitle="Gerenciamento de identidades do Azure AD privilegiado | Microsoft Azure"
    description="Um tópico que explica o que é gerenciamento de identidades do Azure AD privilegiados e como usar o Gerenciador para melhorar a segurança de nuvem."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="kgremban"/>

# <a name="azure-ad-privileged-identity-management"></a>Gerenciamento de identidades do Azure AD privilegiado

Com o gerenciamento de identidades privilegiado do Azure Active Directory (AD), você pode gerenciar, controle e monitorar o acesso dentro de sua organização. Isso inclui acesso aos recursos no Azure AD e outros serviços online da Microsoft como o Office 365 ou Microsoft Intune.  

> [AZURE.NOTE] Gerenciamento de identidades privilegiado está disponível somente com a edição Premium P2 do Active Directory do Azure. Para obter mais informações, consulte [edições do Active Directory do Azure](active-directory-editions.md).

Organizações desejam minimizar o número de pessoas que têm acesso a informações seguras ou recursos, pois que reduz a chance de um usuário mal-intencionado obter acesso. No entanto, os usuários ainda precisam realizar operações privilegiadas nos aplicativos do Azure, Office 365 ou SaaS. Organizações dar acesso de usuários privilegiados no Azure AD sem monitoração que esses usuários estão fazendo com seus privilégios de administrador. Gerenciamento de identidades do Azure AD privilegiado ajuda a resolver esse risco.  

Gerenciamento de identidades do Azure AD privilegiado ajuda você a:  

- Ver quais usuários são administradores Azure AD
- Habilitar sob demanda, "no momento" acesso administrativo ao Microsoft Online Services como o Intune e o Office 365
- Obtenha relatórios sobre alterações e histórico de acesso de administrador em atribuições de administrador
- Receber alertas sobre o acesso a uma função privilegiado

Gerenciamento de identidades Azure AD privilegiados podem gerenciar os internos funções organizacionais Azure AD, incluindo:  

- Administrador global
- Administrador de cobrança
- Administrador do serviço  
- Usuário administrador
- Administrador de senha

## <a name="just-in-time-administrator-access"></a>Apenas em acesso de administrador de tempo

Historicamente, você poderia atribuir um usuário a uma função de administrador por meio do Azure portal clássico ou o Windows PowerShell. Como resultado, esse usuário torna-se um **administrador permanente**, sempre ativo na função atribuída. Gerenciamento de identidades do Azure AD privilegiado apresenta o conceito de **Administração qualificada**. Administradores qualificados devem ser usuários que precisam de acesso privilegiado agora e em seguida, mas não todos os dias. A função está inativa até que o usuário precise acessar, então eles concluir um processo de ativação e se tornar administrador ativo para um período predeterminado de tempo.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Habilitar o gerenciamento de identidades privilegiados para seu diretório

Você pode começar a usar o Azure AD privilegiados gerenciamento de identidades no [portal do Azure](https://portal.azure.com/).

>[AZURE.NOTE] Você deve ser um administrador global com uma conta organizacional (por exemplo, @yourdomain.com), não uma conta da Microsoft (por exemplo, @outlook.com), para habilitar o gerenciamento de identidades do Azure AD privilegiados para um diretório.

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador global do seu diretório.
2. Se sua organização tiver mais de uma pasta, selecione seu nome de usuário no canto superior direito do portal do Azure. Selecione a pasta onde você usará o gerenciamento de identidades do Azure AD privilegiados.
3. Selecione **mais serviços** e use a caixa de texto de filtro para procurar **Azure AD privilegiados gerenciamento de identidades**.
4. Marque **Fixar no painel de controle** e clique em **criar**. O aplicativo de gerenciamento de identidades privilegiado é aberta.

Se você for a primeira pessoa a usar o gerenciamento de identidades do Azure AD privilegiado no seu diretório, em seguida, o [Assistente de segurança](active-directory-privileged-identity-management-security-wizard.md) orienta a experiência de atribuição inicial. Depois que você se torna automaticamente o primeiro **administrador de segurança** e o **administrador da função privilegiado** do diretório.

Somente um administrador de função privilegiado pode gerenciar o acesso de outros administradores. Você pode [dar a outros usuários a capacidade de gerenciar em Gerenciador](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="privileged-identity-management-dashboard"></a>Painel de gerenciamento de identidades privilegiado

Gerenciador de identidades do Azure AD privilegiado fornece um painel que fornece informações importantes, como:

- Alertas que destacam oportunidades para melhorar a segurança
- O número de usuários atribuídos a cada função privilegiada  
- O número de administradores qualificados e permanentes
- Revisões de acesso contínuo

![Painel de Gerenciador - captura de tela][2]

## <a name="privileged-role-management"></a>Gerenciamento de função privilegiado

Com o gerenciamento de identidades do Azure AD privilegiados, você pode gerenciar os administradores, adicionando ou removendo permanentes ou qualificados administradores cada função.

![Administradores de adicionar/remover Gerenciador - captura de tela][3]

## <a name="configure-the-role-activation-settings"></a>Definir as configurações de ativação de função

Usando as [configurações de função](active-directory-privileged-identity-management-how-to-change-default-settings.md) , você pode configurar as propriedades de ativação de função qualificado incluindo:

- A duração do período de ativação de função
- A notificação de ativação de função
- As informações de que um usuário precisa fornecer durante o processo de ativação de função  

![Captura de tela do Gerenciador - ativação de administrador - configurações][4]

Observe que a imagem, os botões para **Autenticação multifator** estão desabilitados. Para determinados, altamente privilegiado funções, podemos exigir MFA para proteção avançada.

## <a name="role-activation"></a>Ativação de função  

Para [Ativar uma função](active-directory-privileged-identity-management-how-to-activate-role.md), o administrador qualificado solicitações um tempo-limite "ativação" para a função. A ativação pode ser solicitada usando a opção **ativar minha função** no gerenciamento de identidades do Azure AD privilegiados.

Um administrador que deseja ativar uma função precisa inicializar o gerenciamento de identidades do Azure AD privilegiado no portal do Azure.

Ativação de função é personalizável. Nas configurações do Gerenciador, você pode determinar o comprimento da ativação e o que o administrador precisa fornecer para ativar a função de informações.

![Ativação de função de solicitação de administrador do Gerenciador - captura de tela][5]

## <a name="review-role-activity"></a>Examinar a atividade de função

Há duas maneiras de controlar como seus funcionários e administradores estão usando funções privilegiadas. A primeira opção está usando o [histórico de auditoria](active-directory-privileged-identity-management-how-to-use-audit-log.md). O histórico de auditoria registra controlar alterações em atribuições de função privilegiados e histórico de ativação de função.

![Histórico de ativação do Gerenciador - captura de tela][6]

A segunda opção é configurar regulares [revisões de acesso](active-directory-privileged-identity-management-how-to-start-security-review.md). Essas análises de acesso podem ser realizados por e atribuídas revisor (como um gerente de equipe) ou os funcionários podem examinar a mesmo. Esta é a melhor maneira de monitorar quem ainda requer acesso e quem já não faz.


## <a name="next-steps"></a>Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png
