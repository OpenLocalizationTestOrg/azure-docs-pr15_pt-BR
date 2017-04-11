<properties
    pageTitle="Notificações de relatórios do Active Directory do Azure"
    description="Como usar o Active Directory do Azure reporting notificações para sinal suspeito ins."
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/07/2016"
    ms.author="dhanyahk"/>

# <a name="azure-active-directory-reporting-notifications"></a>Notificações de relatórios do Active Directory do Azure

## <a name="what-reports-generate-email-notifications"></a>Quais relatórios geram notificações por email

No momento, somente Irregular entrar gatilhos de relatório de atividade notificações de email.

## <a name="what-is-an-irregular-sign-in"></a>O que é um "Irregular entrar"?

Suplementos de entrada irregulares são aquelas que foram identificados por nossa algoritmos, com base em uma condição de "Impossível viagem" combinado com um local de entrada anômalos e o dispositivo de aprendizado de máquina. Isso pode indicar que um hacker ficou tentando entrar usando esta conta.

## <a name="who-receives-the-email-notifications"></a>Quem recebe as notificações de email?

O email é enviado para todos os administradores globais que recebeu uma licença do Active Directory Premium. Para garantir que ele seja entregue, podemos enviá-lo aos administradores de endereço de Email alternativo também. Administradores devem incluir aad-alerts-noreply@mail.windowsazure.com em sua lista de remetentes confiáveis para que eles não perca o email.

## <a name="how-often-are-these-emails-sent"></a>Com que frequência são esses emails enviados?

O email é enviado se 10 novas irregulares entrar atividades ocorrem nos últimos 30 dias ou desde o último email foi enviado, o que for menor.

## <a name="how-do-i-access-the-report-mentioned-in-the-email"></a>Como posso acessar o relatório mencionado no email?

Quando você clica no link, você será redirecionado para a página de relatório dentro do Azure portal clássico. Para acessar o relatório, você precisa estar ambos:

- Um administrador ou co-da sua assinatura do Azure

- Um administrador global no diretório e atribuída a uma licença do Active Directory Premium. Para obter mais informações, consulte [edições do Active Directory do Azure](active-directory-editions.md).

## <a name="can-i-turn-off-these-emails"></a>Para desativar esses e-mails?

Sim, para desativar as notificações relacionadas a anômalos entrada suplementos dentro do Azure portal clássico, clique em **Configurar**e, em seguida, selecione **desabilitado** sob a seção de **notificações** .

## <a name="whats-next"></a>Qual é a próxima
- Curioso para saber quais relatórios de segurança, auditoria e atividade estão disponíveis? Confira a [segurança do Azure AD, auditoria e relatórios de atividade](active-directory-view-access-usage-reports.md)
- [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Adicionar marca para entrar e painel de acesso páginas da empresa](active-directory-add-company-branding.md)
