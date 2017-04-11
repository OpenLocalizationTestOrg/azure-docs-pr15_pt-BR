<properties
    pageTitle="Notificações do Azure Active Directory de proteção de identidade | Microsoft Azure"
    description="Saiba como as notificações de suportam de atividades de investigação."
    services="active-directory"
    keywords="proteção de identidade do active directory do Azure, descoberta de aplicativo de nuvem, gerenciando aplicativos, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection-notifications"></a>Notificações do Azure Active Directory de proteção de identidade 


Proteção de identidade AD Azure envia dois tipos de emails de notificação automatizada para ajudá-lo a gerenciar o risco de usuário e eventos de risco:

- Usuário comprometido email de alerta

- E-mail de resumo semanal

## <a name="user-compromised-alert-email"></a>Usuário comprometido email de alerta

Um alerta de email comprometido de usuário é gerado quando a proteção de identidade do Azure AD identifica uma conta como comprometida. O email inclui um link para os usuários sinalizados para relatório de risco no painel de proteção de identidade. Recomendamos que você investiga imediatamente notificações de comprometida.


## <a name="weekly-digest-email"></a>E-mail de resumo semanal

O email de resumo semanal contém um resumo dos novos eventos de risco.<br>
Ele inclui:

- Usuários em risco
- Atividades suspeitas
- Vulnerabilidades detectadas
- Links para os relatórios relacionados em proteção de identidade


<br>
![Correção](./media/active-directory-identityprotection-notifications/400.png "Remediation")
<br> 

Você pode alternar enviando um email de resumo semanal.
<br><br>
![Riscos de usuário](./media/active-directory-identityprotection-notifications/62.png "User risks")
<br>
 

**Para abrir a caixa de diálogo de configuração relacionados**:

1. Na lâmina **Azure AD identidade proteção** , clique em **configurações**.
<br><br>
![Política de risco do usuário](./media/active-directory-identityprotection-notifications/401.png "User risk policy")
<br>

2. Na seção **Geral** , clique em **notificações**.
<br><br>
![Política de risco do usuário](./media/active-directory-identityprotection-notifications/405.png "User risk policy")
<br>




## <a name="see-also"></a>Consulte também

- [Proteção de identidade do Active Directory do Azure](active-directory-identityprotection.md) 

