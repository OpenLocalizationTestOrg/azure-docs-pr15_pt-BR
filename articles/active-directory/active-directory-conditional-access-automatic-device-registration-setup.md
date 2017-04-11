<properties
    pageTitle="Configurar o registro automático de dispositivos de domínio do Windows com o Active Directory do Azure | Microsoft Azure"
    description="Configure o domínio dispositivos com o Windows para registrar automaticamente e silenciosamente com o Active Directory do Azure."
    services="active-directory"
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
    ms.date="10/24/2016"
    ms.author="markvi"/>



# <a name="set-up-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Configurar o registro automático do Windows domínio dispositivos com o Active Directory do Azure

Para usar [o acesso de condicional baseado em dispositivo Azure Active Directory](active-directory-conditional-access.md), computadores com Windows domínio devem ser registrados com o Azure Active Directory (AD Azure). Neste artigo, você pode saber o que você precisa fazer para configurar o registro de dispositivos de domínio do Windows com o Azure AD em sua organização.

Usando o access condicional no Azure AD oferece estas vantagens:

-   Experiência aprimorada logon único (SSO) nos aplicativos do Azure AD por meio de contas corporativas ou de estudante
-   Enterprise roaming das configurações em dispositivos
-   Acesso a Windows Store para empresas
-   Autenticação mais forte e conveniente entrar no Windows Hello

> [AZURE.NOTE] A atualização de novembro do Windows 10 oferece algumas das experiências usuário aprimorada no Azure AD, mas a atualização do Windows 10 aniversário totalmente suporta acesso condicional baseado no dispositivo. Para obter mais informações sobre o acesso condicional, consulte [acesso condicional baseada em dispositivo Azure Active Directory](active-directory-conditional-access.md). Para obter mais informações sobre dispositivos Windows 10 no local de trabalho e como um usuário registra um dispositivo Windows 10 com o Azure AD, consulte [Windows 10 para a empresa: usar dispositivos para trabalho](active-directory-azureadjoin-windows10-devices-overview.md).

Você pode registrar algumas versões anteriores do Windows, incluindo estas versões:

-   Windows 8.1
-   Windows 7

Se você estiver usando um computador Windows Server como uma área de trabalho, você pode registrar essas plataformas:

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2


## <a name="prerequisites"></a>Pré-requisitos

O principal requisito de registro automático dos dispositivos domínio usando Azure AD é ter uma versão atualizada do Azure Active Directory se conectar (Azure AD Connect).

Dependendo de como você implantado Azure AD Connect e se você usou uma instalação expressa ou personalizada ou uma atualização in-loco, os seguintes pré-requisitos podem ter sido configurados automaticamente:

-   **Ponto de conexão de serviço no Active Directory local**. Para descoberta de informações de locatário do Azure AD por computadores que registre-se no Azure AD.

-   **Regras de transformação de emissão dos serviços de Federação do active Directory (AD FS)**. Para autenticação de computador em registro (aplicável às configurações federadas).

Se alguns dispositivos em suas organizações não são Windows 10 domínio, certifique-se de que fazer as seguintes tarefas:

- Definir uma política no Azure AD para que os usuários podem registrar dispositivos
- Configurar autenticação de Windows integrada (IWA) como uma alternativa válida para autenticação multifator do AD FS



## <a name="set-a-service-connection-point-for-discovery-of-the-azure-ad-tenant"></a>Definir um ponto de conexão de serviço para descoberta do locatário Azure AD

Um objeto de ponto de conexão do serviço deve existir na configuração partição de contexto da floresta do domínio onde os computadores fazem parte de nomenclatura. O ponto de conexão de serviço contém informações de descoberta sobre o locatário do Azure AD onde computadores registrar. Em uma configuração do Active Directory de vários floresta, o ponto de conexão do serviço deve existir em todas as florestas que possuem computadores de domínio.

Defina o ponto de conexão de serviço nesses locais do Active Directory:

    CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Configuration Naming Context]

> [AZURE.NOTE] Para uma floresta com o Active Directory domain nome *example.com*, a configuração do contexto de nomenclatura é CN = configuração, DC = exemplo, DC = com.

Você pode verificar os valores de objeto e descoberta de locatário do Azure AD usando o seguinte script do Windows PowerShell. (Substitua o contexto de nomenclatura de configuração no exemplo pelo seu contexto de nomenclatura de configuração).

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com";

    $scp.Keywords;

O `$scp.Keywords` saída mostra as informações de locatário do Azure AD:

    azureADName:microsoft.com

    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Se o ponto de conexão do serviço não existir, crie-lo executando o seguinte script do PowerShell no servidor do Azure AD Connect:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


> [AZURE.NOTE]Quando você executa `$aadAdminCred = Get-Credential`, use o formato *user@example.com* para o nome de usuário na caixa de diálogo **Get-Credential** .  
> Quando você executa o cmdlet ADSyncDomainJoinedComputerSync inicializar, substitua [*nome da conta de conector*] com a conta de domínio que é usada na conta de conector do Active Directory.  
> O cmdlet usa o módulo do Active Directory do PowerShell, que depende dos serviços Web do Active Directory em um controlador de domínio. Os serviços de Web do Active Directory é suportado no controladores de domínio no Windows Server 2008 R2 e versões posteriores. Controladores de domínio no Windows Server 2008 ou versões anteriores, usar a API de serviços por meio do PowerShell para criar o ponto de conexão de serviço e atribuir os valores de palavras-chave.

## <a name="create-ad-fs-rules-for-instant-device-registration-in-federated-organizations"></a>Criar regras de AD FS para o registro de dispositivos instantânea em organizações federadas

Em um Azure federado configuração do AD, dispositivos dependem do AD FS (ou no servidor de federação local) para autenticar Azure AD. Em seguida, eles registram contra Azure Active Directory dispositivo registro serviço (Azure AD dispositivo registro).

> [AZURE.NOTE] Em uma configuração não federado, hash de senha do usuário está sincronizadas com o Azure AD e computadores de domínio do Windows 10 e Windows Server 2016 autenticam no serviço de registro do dispositivo do Azure AD. Um usuário autentica usando uma credencial que o usuário grava em suas contas de computador local, e que será enviada ao Azure AD via Azure AD Connect. Para não - Windows 10 e Windows Server 2016 computadores em uma configuração não federado, você tem opções de configuração de uma autoridade de certificação baseada em dispositivo em sua organização. Para obter mais informações, consulte os pacotes de baixar o Windows Installer de seção para computadores não - Windows 10 neste artigo.

Para computadores Windows 10 e Windows Server 2016, Azure AD Connect associa o objeto de dispositivo no Azure AD o objeto de conta de computador local. As seguintes declarações devem existir durante a autenticação do serviço de registro do dispositivo do Azure AD para concluir o registro e crie o objeto de dispositivo:

- http://schemas.microsoft.com/WS/2012/01/AccountType contém o valor de DJ, que identifica o autenticador principal como um computador de domínio.
- http://schemas.microsoft.com/Identity/Claims/onpremobjectguid contém o valor do atributo **objectGUID** da conta do computador local.
- http://schemas.microsoft.com/WS/2008/06/Identity/Claims/primarysid contém o identificador do computador segurança principal (SID), que corresponde ao valor do atributo **objectSid** da conta do computador local.
- http://schemas.microsoft.com/WS/2008/06/Identity/Claims/issuerid contém o valor que usa o Azure AD para confiar no token emitido do AD FS ou do local serviço STS (Security Token). Isso é importante em uma configuração de vários floresta do Active Directory. Nessa configuração, computadores podem ingressar em uma floresta diferente da que se conecta ao Azure AD do AD FS ou STS local. Para o AD FS, use o valor http://<;*nome de domínio*>/adfs/serviços/confiabilidade /, onde <*nome de domínio*> é o nome de domínio validadas no Azure AD.

Para criar essas regras manualmente, no AD FS, use o seguinte script do PowerShell em uma sessão que está conectada ao servidor. Substitua a primeira linha pelo seu nome de domínio de empresa validadas no Azure AD.

> [AZURE.NOTE] Se você não usa o AD FS para o seu servidor de federação local, siga as instruções do seu fornecedor para criar as regras que emitir essas declarações.

    $validatedDomain = "example.com"      # Replace example.com with your organization's validated domain name in Azure AD

    $rule1 = '@RuleName = "Issue object GUID"

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

        => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain-joined computers"

      c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

      => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

> [AZURE.NOTE] Use somente as primeiras três regras se seu ambiente local é uma única floresta. Se seus computadores estão em uma floresta diferente a uma sincronização com o Azure AD ou se você usar nomes alternativos na configuração de sincronização, você também deve incluir as regras restantes.

    $rule3 = '@RuleName = "Pass through primary SID"

      c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&

      c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

      => issue(claim = c2);'

    $rule4 = '@RuleName = "Issue AccountType with the value User when it’s not a computer account"

      NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"])

      => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");'

    $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID"

      c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] &&

      c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"]

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));'

    $rule6 = '@RuleName = "Update issuer for DJ computer auth"

      c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$validatedDomain+'/adfs/services/trust/");'

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5 + $rule6

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

> [AZURE.NOTE] Computadores de domínio do Windows 10 e Windows Server 2016 autenticam usando IWA com um ponto de extremidade WS-Trust ativo está hospedado pelo AD FS. Certifique-se de que o ponto de extremidade está ativo. Se você estiver usando o Proxy de aplicativo da Web, certifique-se de que esse ponto de extremidade é publicado através do proxy. O ponto de extremidade é adfs/serviços/confiabilidade/13/windowstransport. Para verificar se ele está ativo, no console de gerenciamento do AD FS, vá para **serviço** > **pontos de extremidade**. Se você não usa o AD FS para o seu servidor de federação local, siga as instruções do seu fornecedor para certificar-se de que o ponto de extremidade correspondente está ativo.



## <a name="set-up-ad-fs-for-authentication-of-device-registration"></a>Configurar o AD FS para autenticação do registro do dispositivo

Certifique-se de que IWA é definido como uma alternativa válida para a autenticação multifator para o registro de dispositivos do AD FS. Para fazer isso, você precisa ter um emissão transformar regra que passa o método de autenticação.

1. No console de gerenciamento do AD FS, vá para **o AD FS** > **Relações de confiança** > **Confiar festa confia**.

2. Clique no objeto de confiança de festa confiante da plataforma de identidade do Microsoft Office 365 e selecione **Editar regras de declaração**.

3.  Na guia **Regras de transformação de emissão** , selecione **Adicionar regra**.

4.  Na lista modelo de **regra de declaração** , selecione **Enviar declarações usando uma regra personalizada**.

5.  Selecione **Avançar**.

6.  Na caixa **nome da regra de declaração** , insira a **Regra de declaração de método de autenticação**.

7.  Na caixa **regra de declaração** , digite esse comando:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
    => issue(claim = c);`.

8. No seu servidor de federação, digite este comando do PowerShell:

    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

<*RPObjectName*> é o nome de objeto do participante confiante para seu objeto de confiança de terceiros terceira do Azure AD. Esse objeto geralmente é nomeado *Plataforma de identidade do Microsoft Office 365*.



## <a name="deployment-and-rollout"></a>Implantação e implementação

Quando computadores de domínio atende aos pré-requisitos, eles estão prontos para registrar Azure AD.

Os Windows Update de aniversário de 10 e Windows Server 2016 domínio computadores automaticamente registrar Azure AD na próxima vez que o dispositivo reinicia ou quando um usuário entra no Windows. Registrar novos computadores que fazem parte de domínio com o Azure AD quando o dispositivo for reiniciado após a operação de junção de domínio.

> [AZURE.NOTE] Computadores de domínio do Windows 10 automaticamente registrar Azure AD somente se o objeto de política de grupo de distribuição é definido.

Você pode usar um objeto de política de grupo para controlar a distribuição do registro automático do Windows 10 e computadores de domínio do Windows Server 2016. Para implementar o registro automático de computadores de domínio não - Windows 10, você pode implantar um pacote do Windows Installer para computadores que você selecionar.

> [AZURE.NOTE] A política de grupo para controle de distribuição também aciona o registro de computadores de domínio do Windows 8.1. Você pode usar a política para registrar computadores de domínio do Windows 8.1. Ou, se você tiver uma mistura de versões do Windows, incluindo versões do Windows 7 ou Windows Server, você pode registrar todas as suas não - Windows 10 e Windows Server 2016 computadores usando um pacote do Windows Installer.

### <a name="create-a-group-policy-object-to-control-the-rollout-of-automatic-registration"></a>Criar um objeto de política de grupo para controlar a distribuição do registro automático

Para controlar a distribuição do registro automático do domínio computadores com o Azure AD, você pode implantar a política de grupo **Registrar domínio computadores como dispositivos** para os computadores que você deseja registrar. Por exemplo, você pode implantar a política para uma unidade organizacional ou para um grupo de segurança.

Para definir a política, siga estas etapas:

1. Abra o Gerenciador de servidor e, em seguida, vá para **Ferramentas** > **Gerenciamento de política de grupo**.

2. Vá para o nó do domínio que corresponde ao domínio onde você deseja ativar o registro automático de computadores Windows 10 ou no Windows Server 2016.

3. **Objetos de política de grupo**de atalho e, em seguida, selecione **novo**.

4. Insira um nome para seu objeto de política de grupo. Por exemplo, *O registro automático ao Azure AD*. Selecione **Okey**.

4. Clique com botão direito seu novo objeto de política de grupo e selecione **Editar**.

5. Vá para **configuração do computador** > **políticas** > **modelos administrativos** > **componentes do Windows** > **registro do dispositivo**. **Registrar domínio ingressou computadores como dispositivos**de atalho e, em seguida, selecione **Editar**.

    > [AZURE.NOTE] Este modelo de política de grupo foi renomeado de versões anteriores do console de gerenciamento de política de grupo. Se você estiver usando uma versão anterior do console, vá para **Configuração de computador** > **políticas** > **Modelos administrativos** > **Componentes do Windows** > **Junção do local de trabalho** > **computadores com o local de trabalho junção cliente automaticamente**.

6. Selecione **ativado**e, em seguida, selecione **Aplicar**.

7. Selecione **Okey**.

8. Vincule o objeto de política de grupo para um local de sua escolha. Por exemplo, você poderá vinculá-lo para uma unidade organizacional específica. Você também pode vinculá-lo a um grupo de segurança específico de computadores que registrar automaticamente com o Azure AD. Para definir essa política para todos os domínio Windows 10 e Windows Server 2016 computadores em sua organização, vincule o objeto de política de grupo para o domínio.

### <a name="download-windows-installer-packages-for-non-windows-10-computers"></a>Baixar pacotes do Windows Installer para computadores não - Windows 10  

Para registrar domínio computadores que executam o Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2, você pode baixar e instalar esses arquivos de pacote (. msi) do Windows Installer:

- [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
- [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

Implante o pacote usando um sistema de distribuição de software como o Gerenciador de configuração do System Center. O pacote suporta as opções de instalação silencioso padrão com o parâmetro *silencioso* . Gerenciador de configuração do System Center 2016 oferece benefícios adicionais de versões anteriores, como a capacidade de controlar registros concluídos. Para obter mais informações, consulte [sistema central 2016](https://www.microsoft.com/en-us/cloud-platform/system-center).

O instalador cria uma tarefa agendada no sistema que é executado no contexto do usuário. A tarefa é disparada quando o usuário entra no Windows. A tarefa silenciosamente registra o dispositivo com o Azure AD com as credenciais de usuário após a autenticação por meio de IWA. Para ver a tarefa agendada, vá para a **Microsoft** > **Participar do local de trabalho**e então vá para a biblioteca do Agendador de tarefas.



## <a name="next-steps"></a>Próximas etapas

- [Acesso condicional do Azure Active Directory](active-directory-conditional-access.md)
