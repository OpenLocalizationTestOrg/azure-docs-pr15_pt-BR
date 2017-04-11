<Properties
    pageTitle="Acesso condicional do Azure Active Directory | Microsoft Azure"  
    description="Use o controle de acesso condicional do Azure Active Directory para verificar se há condições específicas ao autenticar acesso aos aplicativos."  
    services="active-directory"
    keywords="acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos de empresa, políticas de acesso condicional"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/21/2016"
    ms.author="markvi"/>


# <a name="conditional-access-in-azure-active-directory"></a>Acesso condicional no Active Directory do Azure   

Os recursos de controle no access condicional do Azure Active Directory (AD Azure) oferecem maneiras simples para ajudar a recursos seguros na nuvem e locais. Políticas de acesso condicional como autenticação multifator pode ajudar a proteger contra o risco de roubado e capturados credenciais. Outras diretivas de acesso condicional podem ajudar a manter os dados da sua organização seguros. Por exemplo, além de exigir credenciais, você pode ter uma política que somente dispositivos que estão inscritos em um sistema de gerenciamento de dispositivo móvel, como Microsoft Intune podem acessar serviços de confidenciais de sua organização.


## <a name="prerequisites"></a>Pré-requisitos

Acesso condicional do Azure AD é um recurso do [Azure Active Directory Premium](http://www.microsoft.com/identity). Cada usuário que acessa um aplicativo que possui acesso condicional políticas aplicadas deve ter uma licença do Azure AD Premium. Você pode saber mais sobre os requisitos de licença no [relatório de usuário não está licenciado](https://aka.ms/utc5ix).


## <a name="how-is-conditional-access-control-enforced"></a>Como o controle de acesso condicional é aplicado?  

Com o controle de acesso condicional no lugar, Azure AD procura condições específicas definidas para um usuário para acessar um aplicativo. Depois de acesso requisitos forem atendidos, o usuário é autenticado e pode acessar o aplicativo.  

![Visão geral de acesso condicional](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## <a name="conditions"></a>Condições

Estas são as condições que você pode incluir em uma política de acesso condicional:

- **Associações de grupo**. Controle o acesso do usuário com base em participação em um grupo.

- **Local**. Usar o local do usuário para a autenticação multifator disparador e use os controles de bloco quando um usuário não está em uma rede confiável.

- **Plataforma do dispositivo**. Use a plataforma do dispositivo, como o iOS, Android, Windows Mobile ou Windows, como uma condição para aplicação da política.

- **Dispositivo habilitado**. Estado do dispositivo, se habilitada ou desabilitada, é validado durante a avaliação da política de dispositivo. Se você desabilitar um dispositivo perdido ou roubado no diretório, ele não está mais pode satisfazer requisitos de política.

- **Risco entrar e usuário**. Você pode usar a [proteção de identidade do Azure AD](active-directory-identityprotection.md) para políticas de acesso condicional do risco. Políticas de acesso condicional do risco ajudam a oferecer sua proteção de avanço da organização com base em eventos de risco e atividades incomuns entrar.


## <a name="controls"></a>Controles

Estes são os controles que você pode usar para aplicar uma política de acesso condicional:

- **Autenticação multifator**. Você pode exigir autenticação forte por meio de autenticação multifator. Você pode usar a autenticação multifator com a autenticação multifator Azure ou usando um provedor de autenticação multifator local, combinado com os serviços de Federação do Active Directory (AD FS). Usando a autenticação multifator ajuda a proteger recursos sejam acessados por um usuário não autorizado que pode ter obtido acesso para as credenciais de um usuário válido.

- **Bloquear**. Você pode aplicar condições como o local do usuário para bloquear o acesso de usuário. Por exemplo, você pode bloquear o acesso quando um usuário não está em uma rede confiável.

- **Dispositivos incompatíveis**. Você pode definir políticas de acesso condicional no nível do dispositivo. Você pode configurar uma política para que somente os computadores que são domínio, ou dispositivos móveis que são registrados em um aplicativo de gerenciamento de dispositivo móvel, podem acessar os recursos da sua organização. Por exemplo, você pode usar o Intune verificar conformidade do dispositivo e reportá-lo ao Azure AD para a imposição de quando o usuário tenta acessar um aplicativo. Para obter orientações detalhadas sobre como usar o Intune proteger aplicativos e dados, consulte [proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune). Você também pode usar o Intune para aplicar a proteção de dados para dispositivos perdidos ou roubados. Para obter mais informações, consulte a [Ajuda a proteger seus dados com apagamento completo ou seletivo usando o Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune).

## <a name="applications"></a>Aplicativos

Você pode aplicar uma política de acesso condicional no nível do aplicativo. Definir níveis de acesso para aplicativos e serviços na nuvem ou local. A política é aplicada diretamente para o site ou serviço. A política é aplicada para acesso para o navegador e para aplicativos que acessar o serviço.


## <a name="device-based-conditional-access"></a>Acesso condicional baseado no dispositivo

Você pode restringir o acesso aos aplicativos de dispositivos que são registradas com o Azure AD e que atendem a condições específicas. Acesso condicional baseado no dispositivo protege os recursos de uma organização de usuários que tentarem acessar os recursos de:

- Dispositivos desconhecidos ou não gerenciados.
- Dispositivos que não atendem as políticas de segurança de sua organização configurado.

Você pode definir políticas com base nesses requisitos:

- **Dispositivos de domínio**. Defina uma política para restringir o acesso a dispositivos que fazem parte de um domínio do Active Directory local e que também são registradas com o Azure AD. Essa política se aplica ao Windows desktops, notebooks e tablets de empresa.
Para obter mais informações sobre como configurar o registro automático do domínio dispositivos com o Azure AD, consulte [Configurar o registro automático do Windows domínio dispositivos com o Active Directory do Azure](active-directory-conditional-access-automatic-device-registration-setup.md).

- **Dispositivos incompatíveis**. Defina uma política para restringir o acesso a dispositivos que são marcados **compatíveis** no diretório de sistema de gerenciamento. Essa política garante que somente os dispositivos que atendam às políticas de segurança como impondo criptografia de arquivo em um dispositivo têm permissão de acesso. Você pode usar esta diretiva para restringir o acesso de dispositivos a seguir:

    - **Dispositivos de domínio do Windows**. Gerenciadas pelo System Center Configuration Manager (na ramificação atual) implantados em uma configuração híbrida.
    - **Windows 10 Mobile corporativa ou dispositivos pessoais**. Gerenciado por Intune ou por um sistema de gerenciamento de dispositivos móveis com suporte de terceiros.
    - **dispositivos iOS e Android**. Gerenciadas pelo Intune.


Os usuários que acessam aplicativos protegidos por um dispositivo com base em, diretiva de autoridade de certificação deve acessar o aplicativo em um dispositivo que atende aos requisitos desta política. Acesso negado se uma tentativa em um dispositivo que não atende aos requisitos de política.

Para obter informações sobre como configurar uma diretiva de autoridade de certificação baseada no dispositivo, no Azure AD, consulte [definir a política de acesso condicional baseado no dispositivo para aplicativos conectados Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

## <a name="resources"></a>Recursos

Consulte os seguintes categorias de recursos e artigos para aprender mais sobre a configuração de acesso condicional para sua organização.


### <a name="multi-factor-authentication-and-location-policies"></a>Políticas de autenticação e local multifatores

- [Introdução ao access condicional aos aplicativos Azure AD conectados, com base em grupo, local e políticas de autenticação multifator](active-directory-conditional-access-azuread-connected-apps.md)
- [Aplicativos com suporte](active-directory-conditional-access-supported-apps.md)


### <a name="device-based-conditional-access"></a>Acesso condicional baseado no dispositivo

- [Definir a política de acesso condicional baseado no dispositivo para controlar o acesso aos aplicativos conectados Active Directory do Azure](active-directory-conditional-access-policy-connected-applications.md)
- [Configurar o registro automático do Windows domínio dispositivos com o Active Directory do Azure](active-directory-conditional-access-automatic-device-registration-setup.md)
- [Solução de problemas para problemas de acesso do Active Directory do Azure](active-directory-conditional-access-device-remediation.md)
- [Ajudar a proteger dados em dispositivos perdidos ou roubados usando Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### <a name="protect-resources-based-on-sign-in-risk"></a>Proteger os recursos com base em riscos entrar

-   [Proteção de identidade do Azure AD](active-directory-identityprotection.md)

### <a name="next-steps"></a>Próximas etapas

- [Acesso condicional perguntas frequentes](active-directory-conditional-faqs.md)
- [Referência técnica](active-directory-conditional-access-technical-reference.md)
