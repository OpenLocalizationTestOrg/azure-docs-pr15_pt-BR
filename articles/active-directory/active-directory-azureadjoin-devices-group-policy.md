<properties
    pageTitle="Conectar dispositivos de domínio ao Azure AD para Windows 10 experiências | Microsoft Azure"
    description="Explica como os administradores podem configurar política de grupo para habilitar dispositivos sejam domínio à rede corporativa."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Conectar dispositivos de domínio ao Azure AD para Windows 10 experiências

Associação de domínio é que as organizações de maneira tradicional conectados dispositivos para trabalho últimos 15 anos e muito mais. Ele permitia aos usuários entrar para seus dispositivos usando seu trabalho do Windows Server Active Directory (Active Directory) ou da escola contas e permitidos TI gerencie totalmente esses dispositivos. Organizações geralmente contam com métodos de imagens para dispositivos de provisionar aos usuários e usam geralmente System Center Configuration Manager (SCCM) ou política de grupo para gerenciá-los.

Associação de domínio no Windows 10 fornecerá os seguintes benefícios após conectar dispositivos ao Azure Active Directory (AD Azure):

- Logon único (SSO) para recursos do Azure AD de praticamente qualquer lugar
- Acesso à empresa da Windows Store usando contas corporativas ou de Estudante (nenhuma conta da Microsoft obrigatória)
- Compatível com Enterprise roaming das configurações do usuário nos dispositivos usando contas corporativas ou de Estudante (nenhuma conta da Microsoft obrigatória)
- Autenticação forte e conveniente entrar para contas de trabalho ou escola com o Microsoft Passport e Windows Hello
- Capacidade de restringir o acesso somente a dispositivos que cumprem as configurações de política de grupo do dispositivo organizacional

## <a name="prerequisites"></a>Pré-requisitos

Junção de domínio continua a ser úteis. No entanto, para obter os benefícios do Azure AD do SSO, roaming configurações com o trabalho ou escola contas e acesso da Windows Store com contas de trabalho ou da escola, será necessário o seguinte:

- Assinatura do Azure AD
- Azure AD Connect para estender o diretório local ao Azure AD
- Política que tenha definido para conectar dispositivos de domínio para o Azure AD
- Windows 10 compilação (build 10551 ou mais recente) para dispositivos

Para habilitar o Microsoft Passport para trabalho e Windows Hello, você precisará também o seguinte:

- PKI public key infrastructure () para emissão de certificados de usuário.
- System Center Configuration Manager versão 1509 para Technical Preview. Para obter mais informações, consulte [Visualização técnica do Gerenciador de configuração de centro do Microsoft sistema](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) e [Blog da equipe do System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx). Isso é necessário para implantar certificados de usuário baseados em chaves do Microsoft Passport.

Como uma alternativa para o requisito de implantação de PKI, você pode fazer o seguinte:

- Ter alguns controladores de domínio com serviços de domínio do Active Directory do Windows Server 2016.

Para habilitar o acesso condicional, você pode criar configurações de política de grupo que permitem o acesso ao domínio dispositivos com nenhum implantações adicionais. Para gerenciar o controle de acesso baseado em conformidade do dispositivo, você precisará do seguinte:

- Gerenciador de configuração do System Center versão 1509 para visualização técnica para cenários de Passport

## <a name="deployment-instructions"></a>Instruções de implantação



### <a name="step-1-deploy-azure-active-directory-connect"></a>Etapa 1: Implantar o Active Directory do Azure conectar

Azure AD Connect permitirá que você provisionar computadores locais como objetos de dispositivo na nuvem. Para implantar o Azure AD Connect, consulte "Instalar Azure AD Connect" no artigo [integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md#install-azure-ad-connect).

 - Se você seguiu uma [instalação personalizada do Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md) (não a instalação Express), siga o procedimento **criar uma conexão de serviço ponto no Active Directory local**, mais adiante nesta etapa.
 - Se você tiver uma configuração federada com Azure AD antes de instalar o Azure AD Connect (por exemplo, se você tiver implantado os serviços de Federação do Active Directory (AD FS) antes), siga o procedimento **regras de declaração de configurar o AD FS** , mais adiante nesta etapa.

#### <a name="create-a-service-connection-point-in-on-premises-active-directory"></a>Criar um ponto de conexão de serviço no Active Directory local

Dispositivos de domínio usará o ponto de conexão de serviço para descobrir informações de locatário do Azure AD no momento do registro automático com o serviço de registro do dispositivo Azure.

No servidor do Azure AD Connect, execute os seguintes comandos do PowerShell:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


Ao executar o cmdlet $aadAdminCred = Get-Credential, use o formato *user@example.com* para o nome de usuário da credencial que é inserida quando o pop-up Get-Credential é exibida.

Ao executar o cmdlet inicializar ADSyncDomainJoinedComputerSync..., substitua [*nome da conta de conector*] com a conta de domínio que será usada como a conta de conector do Active Directory.

#### <a name="configure-ad-fs-claim-rules"></a>Configurar regras de declaração do AD FS
Configurar as regras de declaração do AD FS permite registro instantâneo de um computador com o serviço de registro do dispositivo Azure permitindo computadores para autenticar usando Kerberos/NTLM por meio do AD FS. Sem essa etapa, computadores obterá ao Azure AD de maneira atrasada (sujeitos a tempos de sincronização do Azure AD Connect).

>[AZURE.NOTE]
Se você não tiver o AD FS como o federação server local, siga as instruções do seu fornecedor para criar as regras de declaração.

No servidor do AD FS (ou em uma sessão conectado ao servidor do AD FS), execute os seguintes comandos do PowerShell:

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed
     |   for DomainJoin++. The rules include:
     |   -ObjectGuid
     |   -AccountType
     |   -ObjectSid
     +---------------------------------------------------------------------#>

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $rule1 = '@RuleName = "Issue object GUID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain joined computers"
          c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

    $rule3 = '@RuleName = "Pass through primary SID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(claim = c2);'

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

>[AZURE.NOTE]
Computadores Windows 10 irá autenticar usando a autenticação integrada do Windows para um ponto de extremidade WS-Trust active hospedado pelo AD FS. Certifique-se de que esse ponto de extremidade está habilitado. Se você estiver usando o Proxy de autenticação da Web, também garantir que esse ponto de extremidade é publicado por meio do proxy. Você pode fazer isso marcando o adfs/serviços/confiabilidade/13/windowstransport. Ele deve mostrar como habilitados no console de gerenciamento do AD FS em **serviço** > **pontos de extremidade**.


### <a name="step-2-configure-automatic-device-registration-via-group-policy-in-active-directory"></a>Etapa 2: Configurar o registro de dispositivos automáticas via diretiva de grupo no Active Directory

Você pode usar a política de grupo no Active Directory para configurar seus dispositivos de domínio do Windows 10 para registrar automaticamente com o Azure AD.

> [AZURE.NOTE]
> Para mais recentes instruções sobre como configurar o registro de dispositivo automática, veja [como configurar o registro automático de domínio do Windows unidas dispositivos com o Active Directory do Azure](active-directory-conditional-access-automatic-device-registration-setup.md).
>
> Este modelo de política de grupo foi renomeado no Windows 10. Se você estiver executando a ferramenta de política de grupo de um computador com Windows 10, a política será exibido como: <br>
> **Registrar computadores associados a domínio como dispositivos**<br>
> A política é no seguinte local:<br>
> ***Registro de componentes/dispositivos de configuração/políticas/administrativo modelos/Windows do computador***


## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio de participar do Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre cenários de uso para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos de domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o Azure AD ingressar](active-directory-azureadjoin-setup.md)
