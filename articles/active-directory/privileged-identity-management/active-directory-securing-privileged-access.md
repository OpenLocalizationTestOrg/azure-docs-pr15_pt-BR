<properties
    pageTitle="Protegendo o acesso privilegiado no Azure AD | Microsoft Azure"
    description="Um tópico que explica as abordagens para proteger o acesso privilegiado entre Azure, o Active Directory do Azure e o Microsoft Online Services."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="mwahl"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="kgremban"/>


# <a name="securing-privileged-access-in-azure-ad"></a>Protegendo o acesso privilegiado no Azure AD

Proteger o acesso privilegiado é a primeira etapa crítica para ajudar a proteger ativos de negócios em uma organização moderna. Contas com privilégios são aqueles que administrar e gerenciar sistemas de TI. Ataques virtuais direcionar essas contas para obter acesso a dados e sistemas de uma organização. Para proteger o acesso privilegiado, você deve isolar os sistemas com o risco de ser exposto a um usuário mal-intencionado e contas.

Mais usuários estão iniciando obter acesso privilegiado por meio de serviços de nuvem. Isso pode incluir os administradores globais do Office 365, assinatura Azure administradores e usuários que têm acesso administrativo em VMs ou em aplicativos SaaS.

Recomendamos que você siga este roteiro [Protegendo](https://technet.microsoft.com/library/mt631194.aspx)acesso privilegiado.

Para clientes que usam o Active Directory do Azure para gerenciar o acesso a Azure, Office 365, ou outros aplicativos e serviços da Microsoft, esses princípios se aplicam se contas de usuário são gerenciadas e autenticadas pelo Active Directory ou do Azure Active Directory. As seções a seguir fornecem mais informações sobre os recursos do Azure AD para dar suporte a proteger o acesso privilegiado.

## <a name="multi-factor-authentication"></a>Autenticação multifator

Para aumentar a segurança de autenticação de administrador, você deve exigir autenticação multifator (MFA) antes de conceder privilégios. MFA é um método verificar quem é você que requer o uso de mais do que apenas um nome de usuário e senha. Ele fornece uma segunda camada de segurança para usuário entrada e transações.

Azure autenticação multifator ajuda a proteger o acesso a dados e aplicativos atendendo demanda do usuário para um processo de entrada simples. Ele oferece autenticação sólida via uma variedade de opções de verificação de fácil incluindo chamadas telefônicas, mensagens de texto, notificações do aplicativo móvel, ou código de verificação e 3º tokens de JURAMENTO de terceiros.

Para obter uma visão geral do funcionamento do Azure multi-Factor Authentication consulte vídeo a seguir.

>[AZURE.VIDEO windows-azure-multi-factor-authentication]

Para obter mais detalhes, consulte [MFA para Office 365 e MFA do Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/).

## <a name="time-bound-privileges"></a>Privilégios de tempo limite

Algumas organizações descobrirá que eles tenham muitos usuários em funções altamente privilegiadas. Um usuário talvez tenha sido adicionado à função de uma determinada atividade, como se para inscrever para um serviço, mas não usou esses privilégios frequentemente posteriormente.

Para reduzir o tempo de exposição de privilégios e aumentar sua visibilidade seu uso, limitar os usuários a tomar somente em seus privilégios Just no tempo (JIT), quando eles precisam realizar uma tarefa. Para Active Directory do Azure e serviços Online da Microsoft, você pode usar o [gerenciamento de identidades do Azure AD privilegiado (PIM)](http://aka.ms/AzurePIM).


![Painel de Gerenciador][2]


## <a name="attack-detection"></a>Detecção de ataque

[Proteção de identidade do Azure Active Directory](../active-directory-identityprotection.md) fornece uma visão consolidada em eventos de risco e possíveis vulnerabilidades afetando identidades da sua organização. Proteção de identidade com base em eventos de risco, calcula um nível de risco do usuário para cada usuário, permitindo que você configure baseada em riscos políticas para proteger automaticamente as identidades da sua organização. Essas políticas, juntamente com outros controles de acesso condicional fornecidos pelo Active Directory do Azure e EMS, podem impedir que o usuário ou oferecer sugestões que incluem redefinições de senha e imposição de autenticação multifator automaticamente.

![Proteção de identidade do Azure AD][3]

## <a name="conditional-access"></a>Acesso condicional

Com o controle de acesso condicional, o Azure Active Directory verifica as condições específicas que você escolher ao autenticar um usuário, antes de permitir acesso a um aplicativo. Depois que essas condições forem atendidas, o usuário autenticado e permissão para acessar o aplicativo.


![Configuração de regras de acesso condicional com MFA][4]


## <a name="related-articles"></a>Artigos relacionados

- Habilitar [a autenticação multifator Azure](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
- Habilitar o [gerenciamento de identidades do Azure AD privilegiado](../active-directory-privileged-identity-management-configure.md)
- Habilitar a [proteção identidade do Azure AD](../active-directory-identityprotection.md)
- Habilitar [os controles de acesso condicional](../active-directory-conditional-access.md)


Para obter mais informações sobre a criação de um roteiro de segurança completa, consulte a seção "responsabilidades do cliente e roteiro" do documento [Microsoft Cloud Security for Enterprise Architects](http://aka.ms/securecustomer) . Para obter mais informações sobre envolventes serviços da Microsoft para ajudá-lo com qualquer um destes tópicos, entre em contato com o representante da Microsoft ou visite nossa [página de soluções de Cybersecurity](https://www.microsoft.com/microsoftservices/campaigns/cybersecurity-protection.aspx).

<!--Image references-->
[1]: ../media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ../media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ../media/active-directory-identityprotection/29.png
[4]: ../media/active-directory-conditional-access/conditionalaccess-saas-apps.png
