<properties
   pageTitle="Como configurar alertas de segurança | Microsoft Azure"
   description="Aprenda a configurar alertas de segurança para extensão de gerenciamento de identidades privilegiado do Azure."
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
   ms.date="09/02/2016"
   ms.author="kgremban"/>

# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>Como configurar alertas de segurança no gerenciamento de identidades do Azure AD privilegiado

## <a name="security-alerts"></a>Alertas de segurança
Gerenciamento de identidade de privilegiado (PIM) Azure gera alertas quando houver atividade suspeita ou não seguro em seu ambiente. Quando um alerta é disparado, ela aparece no painel Gerenciador. Selecione o alerta para ver um relatório que lista os usuários ou funções que disparou o alerta.

![Alertas de segurança do dashboard Gerenciador - captura de tela][1]



| Alerta | Disparadores | Recomendação |
| ----- | ------- | -------------- |
| **Funções estão sendo atribuídas fora do Gerenciador** | Um administrador permanentemente foi atribuído a uma função, fora da interface do Gerenciador. | Examine a nova atribuição de função. Desde que outros serviços só podem atribuir administradores permanentes, altere-a para uma atribuição qualificada se necessário. |
| **Funções estão sendo ativadas muito com frequência** | Havia muitos reativações a mesma função dentro do tempo permitido nas configurações. | Contate o usuário para ver por que ele ativou a função muitas vezes. Talvez o limite de tempo é muito pequeno para concluir as tarefas, ou talvez eles está usando scripts para ativar automaticamente uma função. |
| **Funções não exigem autenticação multifator para ativação** | Há funções sem MFA habilitado nas configurações. | Exigir MFA para as funções mais altamente privilegiadas, mas recomendamos que você habilite MFA para ativação de todas as funções. |
| **Os administradores não estiverem usando suas funções privilegiadas** | Há administradores qualificados que ainda não tiver ativado suas funções recentemente. | Inicie uma revisão de acesso para determinar os usuários que não precisam mais o acesso. |
| **Existem muitos administradores globais** | Há mais globais administradores do que a recomendada. | Se você tiver um grande número de administradores globais, é provável que os usuários estão obtendo mais permissões que precisam. Mover usuários para funções com menos privilégios ou verifique algumas delas qualificado para a função, em vez de atribuídos permanentemente. |

## <a name="configure-security-alert-settings"></a>Configurar definições de alerta de segurança

Você pode personalizar alguns dos alertas de segurança no Gerenciador para trabalhar com o seu ambiente e metas de segurança. Siga estas etapas para alcançar a lâmina configurações:

1. Entre [portal do Azure](https://portal.azure.com/) e selecione o bloco de **gerenciamento de identidades do Azure AD privilegiado** no painel.
2. Selecione **gerenciado funções privilegiadas** > **configurações** > **configurações de alertas**.

    ![Navegue até configurações de alertas de segurança][2]

### <a name="roles-are-being-activated-too-frequently-alert"></a>Alerta de "Funções estão sendo ativadas muito frequentemente"

Este alerta aciona se um usuário ativa a mesma função privilegiada várias vezes dentro de um período especificado. Você pode configurar o período de tempo e o número de ativações.

- **Período de tempo de renovação de ativação**: especificar em dias, horas, minutos e um segundo o período de tempo que você deseja usar para controlar renovação suspeita.

- **Número de renovação de ativação**: especificar o número de ativações, de 2 a 100, o que você considera pena de alerta, dentro o período de tempo que você escolheu. Você pode alterar essa configuração movendo o controle deslizante ou digitando um número na caixa de texto.


### <a name="there-are-too-many-global-administrators-alert"></a>Alerta de "Há muitos administradores globais"

Gerenciador aciona este alerta se duas diferentes critérios forem atendidos, e você pode configurar ambos. Primeiro, você precisa alcançar um determinado limite de administradores globais. Segundo, um determinado percentual seu total de atribuições de função deve ser administradores globais. Se você atender apenas uma dessas medidas, o alerta não aparecer.  

- **Número mínimo de administradores globais**: especificar o número de administradores globais, de 2 a 100, que você considerar um valor não seguros.

- **Porcentagem de administradores globais**: especificar a porcentagem de administradores que forem administradores globais, de 0% a 100%, que é potencialmente não seguro em seu ambiente.

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>Alerta de "Administradores não estão usando suas funções privilegiadas"

Este alerta aciona se um usuário vai um determinado período de tempo sem ativar uma função.

- **Número de dias**: especificar o número de dias, de 0 a 100, que um usuário pode acessar sem ativar uma função.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png
