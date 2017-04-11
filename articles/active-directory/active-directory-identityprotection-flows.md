<properties
    pageTitle="Entrar experiências com proteção de identidade do Azure AD | Microsoft Azure"
    description="Fornece uma visão geral da experiência do usuário quando proteção de identidade tem diminuído ou remediadas um usuário ou quando a autenticação multifator é exigida por uma política."
    services="active-directory"
    keywords="proteção de identidade do active directory do Azure, descoberta de aplicativo de nuvem, gerenciando aplicativos, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
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
    ms.date="08/16/2016"
    ms.author="markvi"/>

# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Entrar experiências com proteção de identidade do Azure AD

Com o Azure Active Directory identidade proteção, você pode:

- exigir que os usuários registrar para autenticação multifator

- manipular arriscada entrada e usuários comprometidos

A resposta do sistema para esses problemas tem um impacto na experiência do usuário com entrar porque apenas diretamente entrar no fornecendo um nome de usuário e uma senha não será possível mais. Etapas adicionais são necessárias para obter um usuário com segurança a negócios.

Este tópico fornece uma visão geral de experiência de entrada do usuário para todos os casos que podem ocorrer.

**Autenticação multifator**

- Registro de autenticação multifator



**Entrar em risco**

- Recuperação de entrar arriscada

- Entrar arriscada bloqueado

- Registro de autenticação multifator durante um entrar arriscada
 

**Usuário em risco**

- Recuperação de conta comprometida

- Conta comprometida bloqueada




## <a name="multi-factor-authentication-registration"></a>Registro de autenticação multifator

A melhor experiência de usuário para ambos, o fluxo de recuperação de conta comprometida e arriscado fluxo entrar, é quando o usuário pode recuperar auto. Se os usuários registrados para a autenticação multifator, elas já tem um número de telefone associado à sua conta que pode ser usada para passar desafios de segurança. Sem envolvimento de mesa ou administrador de Ajuda é necessário para recuperar de compromisso de conta. Portanto, altamente recomenda-se para registrar seus usuários registrados para autenticação multifator. 

Os administradores podem:

- Defina uma política que exige que os usuários configurar suas contas para verificação de segurança adicional. 
- Permitir ignorando registro de autenticação multifator por até 30 dias, caso eles queiram conceder aos usuários um período de cortesia antes de registrar.

**O registro de autenticação multifator tem três etapas:**

1. Na primeira etapa, o usuário obtém uma notificação sobre o requisito para definir a conta para autenticação multifator. 

    ![Correção] (./media/active-directory-identityprotection-flows/140.png "Correção")


2. Para configurar a autenticação multifator, você precisa informar o sistema de como você deseja ser contatado.

    ![Correção] (./media/active-directory-identityprotection-flows/141.png "Correção")
 
3. O sistema envia um desafio para você e você precisa responder.

    ![Correção] (./media/active-directory-identityprotection-flows/142.png "Correção")

 



## <a name="risky-sign-in-recovery"></a>Recuperação de entrar arriscada

Quando um administrador configurou uma política para entrar riscos, os usuários afetados notificados ao tentarem entrar. 

**O fluxo de entrada arriscado tem duas etapas:** 

1. O usuário é informado de que algo incomum foi detectado sobre seu entrar, como entrar em um novo local, o dispositivo ou o aplicativo. 

    ![Correção] (./media/active-directory-identityprotection-flows/120.png "Correção")

2. O usuário é necessário para provar sua identidade, resolvendo um desafio de segurança. Se o usuário está registrado para autenticação multifator precisam viagem um código de segurança para seu número de telefone. Como isso é apenas uma entrada arriscada e não uma conta comprometida, o usuário não é preciso alterar a senha nesse fluxo. 

    ![Correção] (./media/active-directory-identityprotection-flows/121.png "Correção")



 
## <a name="risky-sign-in-blocked"></a>Entrar arriscada bloqueado
Os administradores também podem optar por definir uma diretiva de risco entrar para bloquear usuários após entrar dependendo do nível de risco. Para obter desbloqueado, os usuários finais deve contatar um administrador ou o suporte técnico ou eles podem tentar entrar a partir de um local familiar ou dispositivo. Auto recuperando autenticação multifator de solução não é uma opção nesse caso.

![Correção] (./media/active-directory-identityprotection-flows/200.png "Correção")




## <a name="compromised-account-recovery"></a>Recuperação de conta comprometida

Quando uma política de segurança de risco do usuário tiver sido configurada, os usuários que atendem o usuário nível especificado na política de risco (e, portanto, são considerados comprometida) devem passar pelo fluxo de recuperação de compromisso de usuário antes que eles possam entrar. 

**O fluxo de recuperação de compromisso de usuário tem três etapas:**

1. O usuário é informado de que sua segurança de conta está em risco por causa de atividades suspeitas ou perdido credenciais.

    ![Correção] (./media/active-directory-identityprotection-flows/101.png "Correção")

2.  O usuário é necessário para provar sua identidade, resolvendo um desafio de segurança. Se o usuário está registrado para autenticação multifator eles podem recuperar auto seja comprometido. Eles precisam viagem um código de segurança para seu número de telefone. 

    ![Correção] (./media/active-directory-identityprotection-flows/110.png "Correção")


3.  Finalmente, o usuário será forçado a alterar a senha, desde que alguém tiveram acesso à sua conta. Capturas de tela desta experiência estão abaixo.
 
    ![Correção] (./media/active-directory-identityprotection-flows/111.png "Correção")



## <a name="compromised-account-blocked"></a>Conta comprometida bloqueada 

Para obter um usuário que foi bloqueado por uma política de segurança do usuário risco desbloqueada, o usuário deve contatar um administrador ou suporte técnico. Auto recuperando autenticação multifator de solução não é uma opção nesse caso.


![Correção] (./media/active-directory-identityprotection-flows/104.png "Correção")



 
## <a name="reset-password"></a>Redefinir senha

Se usuários comprometidos estão bloqueados da entrar, um administrador pode gerar uma senha temporária para eles. Os usuários terão que alterar sua senha durante um entrar próximo.

![Correção] (./media/active-directory-identityprotection-flows/160.png "Correção")


 




 

## <a name="see-also"></a>Consulte também

- [Proteção de identidade do Active Directory do Azure](active-directory-identityprotection.md) 