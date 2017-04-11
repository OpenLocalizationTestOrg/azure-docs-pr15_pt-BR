<properties
    pageTitle="Cenários de uso e considerações de implantação para participar do Azure AD | Microsoft Azure"
    description="Explica como os administradores podem configurar participar do Azure AD para seus usuários finais (funcionários, alunos, outros usuários). Ele também aborda os cenários reais diferentes para usar o Azure AD ingressar."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

< marcas ms.service= "active directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/27/2016"

    ms.author="femila"/>

# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Cenários de uso e considerações de implantação para participar do Azure AD

## <a name="usage-scenarios-for-azure-ad-join"></a>Cenários de uso para participar do Azure AD
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>Cenário 1: Empresas amplamente na nuvem

Azure Active Directory ingressar (Azure AD ingressar) pode se beneficiar você se atualmente operar e gerenciar identidades para sua empresa na nuvem ou migrar para a nuvem em breve. Você pode usar uma conta que você criou no Azure AD para entrar no Windows 10. Pelo [processo de primeira experiência de execução (FRX)](active-directory-azureadjoin-user-frx.md)ou unindo Azure AD no [menu Configurações](active-directory-azureadjoin-user-upgrade.md), seus usuários podem ingressar suas máquinas ao Azure AD.  Os usuários também podem aproveitar o único acesso sign-on (SSO) para os recursos de nuvem como o Office 365, em seus navegadores ou em aplicativos do Office.

### <a name="scenario-2-educational-institutions"></a>Cenário 2: Instituições de ensino

Instituições de ensino geralmente têm dois tipos de usuário: professores e alunos. Membros do Corpo Docente são considerados membros de longo prazo da organização. Criar contas locais para eles é desejável. Mas os alunos são shorter-term membros da organização e suas contas podem ser gerenciadas no Azure AD. Isso significa que a escala de diretório pode ser enviada para a nuvem em vez de sendo armazenados no local. Isso também significa que os alunos serão capazes de entrar no Windows com suas contas do Azure AD e obtenha acesso aos recursos do Office 365 em aplicativos do Office.

### <a name="scenario-3-retail-businesses"></a>Cenário 3: Empresas de varejo

Empresas de varejo têm trabalhadores sazonais e funcionários a longo prazo. Você normalmente criar contas locais e usar o domínio máquinas para funcionários de tempo integral de longo prazo. Mas trabalhadores sazonais são shorter-term membros da organização e é desejável para gerenciar suas contas onde licenças de usuário podem ser movidas com mais facilidade ao redor. Quando você cria suas contas de usuário na nuvem com o Office 365 licenças, esses usuários obtém os benefícios de entrar no Windows e Office aplicativos com uma conta do Azure AD, enquanto você mantém mais flexibilidade com suas licenças depois que eles saírem.

### <a name="scenario-4-additional-scenarios"></a>Cenário 4: Cenários adicionais

Juntamente com os benefícios discutidos anteriormente, você se beneficiar tendo seus usuários ingressar em seus dispositivos ao Azure AD devido a uma experiência simplificada de ingresso, gerenciamento de dispositivo eficiente, inscrição de gerenciamento de dispositivo móvel automáticas e logon único para o Azure AD e recursos locais.  


## <a name="deployment-considerations-for-azure-ad-join"></a>Considerações de implantação para participar do Azure AD

### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>Permitir que seus usuários ingressar em um dispositivo de empresa diretamente para o Azure AD


Empresas podem fornecer contas somente na nuvem para organizações e empresas de parceiro. Esses parceiros podem acessar facilmente aplicativos de empresa e recursos com logon único. Este cenário é aplicável aos usuários que acessam recursos principalmente na nuvem, como os aplicativos do Office 365 ou SaaS que dependem do Azure AD para autenticação.

### <a name="prerequisites"></a>Pré-requisitos
**No nível empresarial (administrador)**

*   Assinatura do Azure com o Active Directory do Azure  

**No nível de usuário**

*   Windows 10 (edições Professional e Enterprise)

### <a name="administrator-tasks"></a>Tarefas do administrador
* [Configurar o registro de dispositivo](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Tarefas do usuário
* [Configurar um novo dispositivo Windows 10 com o Azure AD durante a instalação](active-directory-azureadjoin-user-frx.md)
* [Configurar um dispositivo Windows 10 com o Azure AD no menu Configurações](active-directory-azureadjoin-user-upgrade.md)
* [Ingressar em um dispositivo pessoal do Windows 10 para sua organização](active-directory-azureadjoin-personal-device.md)



## <a name="enable-byod-in-your-organization-for-windows-10"></a>Habilitar BYOD em sua organização para Windows 10
Você pode configurar os funcionários e seus usuários usem seus dispositivos Windows pessoais (BYOD) para acessar os aplicativos da empresa e recursos. Seus usuários podem adicionar suas contas do Azure AD (contas corporativas ou de estudante) para um dispositivo pessoal do Windows para acessar recursos de forma segura e compatível.

### <a name="prerequisites"></a>Pré-requisitos
**No nível empresarial (administrador)**

*   Assinatura do Azure AD

**No nível de usuário**

*   Windows 10 (edições Professional e Enterprise)


### <a name="administrator-tasks"></a>Tarefas do administrador

* [Configurar o registro de dispositivo](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Tarefas do usuário
* [Ingressar em um dispositivo pessoal do Windows 10 para sua organização](active-directory-azureadjoin-personal-device.md)


## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio de participar do Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticação de identidades sem senhas por meio do Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de uso para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos de domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o Azure AD ingressar](active-directory-azureadjoin-setup.md)
