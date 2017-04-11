<properties
    pageTitle="Azure Active Directory identidade proteção de - como desbloquear usuários | Microsoft Azure"
    description="Saiba como desbloquear usuários que foram bloqueados por uma política de proteção de identidade do Azure Active Directory."
    services="active-directory"
    keywords="proteção de identidade do active directory do Azure, desbloquear usuário"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection---how-to-unblock-users"></a>Azure Active Directory identidade proteção de - como desbloquear usuários

Com o Azure Active Directory identidade proteção, você pode configurar políticas para bloquear usuários se as condições configuradas forem atendidas. Normalmente, um usuário bloqueados contatos suporte técnico se tornar desbloqueado. Tópicos explica as etapas que você pode executar para desbloquear um usuário bloqueado.


## <a name="determine-the-reason-for-blocking"></a>Determinar o motivo do bloqueio

A primeira etapa para desbloquear um usuário, você precisa determinar o tipo de política que bloqueou o usuário porque as próximas etapas dependem-lo. Com o Azure Active Directory identidade proteção, um usuário pode ser bloqueado ou por uma política de risco entrar ou uma política de risco do usuário. 

Você pode obter o tipo de política que bloqueou um usuário do título na caixa de diálogo que foi apresentado ao usuário durante uma tentativa de entrada:

|Política | Caixa de diálogo do usuário|
|--- | --- |
|Entrar risco | ![Entrar bloqueado](./media/active-directory-identityprotection-unblock-howto/02.png) |
|Risco de usuário | ![Conta bloqueada](./media/active-directory-identityprotection-unblock-howto/104.png) |


Um usuário que está bloqueado pelo:

- Uma política de entrada risco também é conhecido como entrar suspeito
- Uma política de risco do usuário também é conhecido como uma conta do risco

 
## <a name="unblocking-suspicious-sign-ins"></a>Desbloqueie suplementos sinal suspeitos

Para desbloquear uma entrar suspeito, você tem as seguintes opções:

1. **Entrar de um local familiar ou dispositivo** - um motivo comum para inscrições suspeitos bloqueados são tentativas de entrada de locais familiarizados ou dispositivos. Os usuários podem determinar rapidamente se essa é a razão bloqueio tentando entrar de um local familiar ou dispositivo.


3. **Excluir diretiva** - se você acha que a configuração atual da sua política de entrada está causando problemas para usuários específicos, você pode excluir os usuários dele. Consulte a [política de risco entrar](active-directory-identityprotection.md#sign-in-risk-policy) para obter mais detalhes.
 
4. **Desativar diretiva** - se você acha que a configuração de política está causando problemas de todos os usuários, você pode desabilitar a política. Consulte a [política de risco entrar](active-directory-identityprotection.md#sign-in-risk-policy) para obter mais detalhes.


## <a name="unblocking-accounts-at-risk"></a>Como desbloquear contas em risco

Para desbloquear uma conta em risco, você tem as seguintes opções:

1. **Redefinir senha** - você pode redefinir a senha do usuário. Consulte [Redefinir a senha de segurança manual](active-directory-identityprotection.md#manual-secure-password-reset) para obter mais detalhes.

2. **Descartar todos os eventos de risco** - a política de risco do usuário bloqueia um usuário se você atingiu o nível de risco do usuário configurado para bloquear o acesso. Você pode reduzir um usuário do nível de risco manualmente fechando relatadas eventos de risco. Para obter mais detalhes, consulte [Fechar eventos de risco manualmente](active-directory-identityprotection.md#closing-risk-events-manually).

3. **Excluir diretiva** - se você acha que a configuração atual da sua política de entrada está causando problemas para usuários específicos, você pode excluir os usuários dele. Consulte a [política de risco do usuário](active-directory-identityprotection.md#user-risk-policy) para obter mais detalhes.
 
4. **Desativar diretiva** - se você acha que a configuração de política está causando problemas de todos os usuários, você pode desabilitar a política. Consulte a [política de risco do usuário](active-directory-identityprotection.md#user-risk-policy) para obter mais detalhes.




## <a name="next-steps"></a>Próximas etapas

 Deseja saber mais sobre proteção contra Azure AD identidade? Fazer check-out [Do Azure Active Directory proteção de identidade](active-directory-identityprotection.md).
 

