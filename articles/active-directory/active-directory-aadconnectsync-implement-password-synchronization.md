<properties
    pageTitle="Implementação de sincronização de senha com a sincronização do Azure AD Connect | Microsoft Azure"
    description="Fornece informações sobre como funciona a sincronização de senha e como ativá-lo."
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
    ms.date="08/24/2016"
    ms.author="markusvi;andkjell"/>


# <a name="implementing-password-synchronization-with-azure-ad-connect-sync"></a>Implementação de sincronização de senha com a sincronização do Azure AD Connect
Este tópico fornece as informações necessárias sincronizar suas senhas de usuário de um local do Active Directory (AD) para um baseado em nuvem do Azure Active Directory (AD Azure).

## <a name="what-is-password-synchronization"></a>O que é sincronização de senha
A probabilidade de que você está bloqueado de realizarem seu trabalho devido a uma senha esquecida está relacionado ao número de senhas diferentes, você precisa se lembrar. As senhas mais que você precisa se lembrar, maior probabilidade esqueça um. Perguntas e chamadas sobre redefinições de senha e outros problemas relacionados a senha exigem a maioria dos recursos de suporte técnico.

Sincronização de senha é um recurso para sincronizar senhas de usuário de um Active Directory local para um baseado em nuvem do Azure Active Directory (AD Azure).
Esse recurso permite que você entrar nos serviços do Azure Active Directory (como o Office 365, Microsoft Intune, CRM Online e serviços de domínio do Azure AD) usando a mesma senha que você está usando para entrar no seu Active Directory local.

![O que é Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

Reduzindo o número de senhas que os usuários precisam manter a apenas um, a sincronização de senha ajuda você a:

- Melhorar a produtividade de seus usuários
- Reduzir os custos de suporte técnico  

Além disso, se você selecionar usar a [**federação com o AD FS**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), você pode opcionalmente habilitar a sincronização de senha como um backup em caso de falha de sua infraestrutura do AD FS.

Sincronização de senha é uma extensão para o recurso de sincronização de diretório implementado pela sincronização do Azure AD Connect. Para usar a sincronização de senha em seu ambiente, você precisa:

- Conectar-se de instalar Azure AD  
- Configurar a sincronização de diretório entre seu local AD e o Active Directory do Azure
- Habilitar a sincronização de senha

Para obter mais detalhes, consulte [integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)

> [AZURE.NOTE] Para obter mais detalhes sobre serviços de domínio Active Directory que estão configurados para sincronização de FIPS e a senha, consulte [sincronização de senha e FIPS](#password-synchronization-and-fips).

## <a name="how-password-synchronization-works"></a>Como funciona a sincronização de senha
O serviço de domínio Active Directory armazena senhas no formulário de uma representação de valor de hash da senha do usuário real. Um valor de hash é um resultado de uma função matemática unidirecional (o "*algoritmo de hash*"). Não haverá nenhum método para reverter o resultado de uma função unidirecional para a versão de texto sem formatação de uma senha. Você não pode usar um hash de senha para entrar na sua rede local.

Para sincronizar sua senha, a sincronização do Azure AD Connect extrai seu hash de senha do Active Directory local. Processamento de segurança extra é aplicado ao hash de senha antes que ele é sincronizado com o serviço de autenticação do Active Directory do Azure. As senhas são sincronizadas em uma base por usuário e em ordem cronológica.

O fluxo de dados real do processo de sincronização de senha é semelhante à sincronização de dados do usuário como DisplayName ou endereços de Email. No entanto, as senhas são sincronizadas com mais frequência do que a janela de sincronização de diretório padrão para outros atributos. O processo de sincronização de senha é executado a cada 2 minutos. Você não pode modificar a frequência desse processo. Quando você sincroniza uma senha, ele substitui a senha de nuvem existente.

A primeira vez, você habilitar o recurso de sincronização de senha, ele executa uma sincronização inicial das senhas de todos os usuários no escopo. Você não pode definir explicitamente um subconjunto de senhas de usuário que você deseja sincronizar.

Quando você altera uma senha no local, a senha atualizada está sincronizada, com mais frequência em poucos minutos.
O recurso de sincronização de senha automaticamente tentativas tentativas de sincronização falha. Se ocorrer um erro durante a tentativa de sincronizar uma senha, um erro é registrado no seu visualizador de eventos.

A sincronização de uma senha não tem nenhum impacto sobre o usuário conectado no momento.
Sua sessão de serviço de nuvem atual não é afetado imediatamente por uma alteração de senha sincronizadas que ocorre enquanto você estiver conectado a um serviço de nuvem. No entanto, quando o serviço de nuvem requer que você autenticar novamente, você precisa fornecer sua nova senha.

> [AZURE.NOTE] Só há suporte para a sincronização de senha do usuário de tipo de objeto do Active Directory. Ele não há suporte para o tipo de objeto iNetOrgPerson.

### <a name="how-password-synchronization-works-with-azure-ad-domain-services"></a>Como funciona a sincronização de senha com os serviços de domínio do Azure AD
Você também pode usar o recurso de sincronização de senha para sincronizar suas senhas locais aos [Serviços de domínio do Azure AD](../active-directory-domain-services/active-directory-ds-overview.md). Este cenário permite que os serviços de domínio Azure AD autenticar os usuários na nuvem com todos os métodos disponíveis no seu local AD. A experiência desse cenário é semelhante a usar a ferramenta de migração do Active Directory (ADMT) em um ambiente local.

### <a name="security-considerations"></a>Considerações de segurança
Ao sincronizar senhas, a versão de texto sem formatação da sua senha não é exposta para o recurso de sincronização de senha, ao Azure AD, ou qualquer um dos serviços associados.

Além disso, não há nenhuma exigência no Active Directory local para armazenar a senha em um formato de criptografia reversível. Um resumo do hash de senha do Active Directory é usado para a transmissão entre o local AD e Azure Active Directory. O resumo do hash senha não pode ser usado para acessar recursos no seu ambiente local.

### <a name="password-policy-considerations"></a>Considerações de política de senha
Há dois tipos de políticas de senha afetados habilitando a sincronização de senha:

1. Política de complexidade de senha
2. Política de expiração de senha

**Política de complexidade de senha**  
Quando você habilita a sincronização de senha, as diretivas de complexidade de senha no seu Active Directory local substituam diretivas de complexidade na nuvem para usuários sincronizados. Você pode usar todas as senhas válidas de seu local Active Directory para acessar os serviços do Azure AD.

> [AZURE.NOTE] Senhas de usuários que são criados diretamente na nuvem estão ainda sujeitos políticas de senha, conforme definido na nuvem.

**Política de expiração de senha**  
Se um usuário estiver no escopo de sincronização de senha, a senha da conta de nuvem está definida para "*Nunca expirar*".
Você pode continuar a entrar nos seus serviços de nuvem usando uma senha sincronizada que expirou no seu ambiente local. Sua senha de nuvem é atualizada na próxima vez que você alterar a senha no ambiente local.

### <a name="overwriting-synchronized-passwords"></a>Substituindo sincronizados senhas
Um administrador pode redefinir sua senha usando o Windows PowerShell manualmente.

Nesse caso, a nova senha substitui sua senha sincronizada e todas as políticas de senha definidas na nuvem são aplicadas para a nova senha.

Se você alterar sua senha local novamente, a nova senha é sincronizada na nuvem e substitui a senha atualizada manualmente.

## <a name="enabling-password-synchronization"></a>Ativar sincronização de senha
Sincronização de senha é ativada automaticamente quando você instala o Azure AD Connect usando as **Configurações expressas**. Para obter mais detalhes, consulte [Introdução ao Azure AD Connect usando configurações express](./connect/active-directory-aadconnect-get-started-express.md).

Se você usar configurações personalizadas quando você instala o Azure AD Connect, você habilitar a sincronização de senha na página de entrada do usuário. Para obter mais detalhes, consulte [instalação personalizada do Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md).

![Ativar sincronização de senha](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### <a name="password-synchronization-and-fips"></a>Sincronização de senha e FIPS
Se seu servidor foi bloqueado acordo com Federal Information Processing Standard (FIPS), em seguida, MD5 foi desativado.

**Para habilitar MD5 para sincronização de senha, execute as seguintes etapas:**

1. Vá para **Sync\Bin %programfiles%\Azure AD**.
2. Abra **miiserver.exe.config**.
3. Vá para o nó de **Configuração/tempo de execução** (no final do arquivo).
4. Adicione o seguinte nó:`<enforceFIPSPolicy enabled="false"/>`
5. Salve as alterações.

Para referência, o recorte é como ele deve parecer com:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Para obter informações sobre segurança e FIPS consulte [AAD Sync de senha, criptografia e FIPS de conformidade](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## <a name="troubleshooting-password-synchronization"></a>Solucionando problemas de sincronização de senha
Se as senhas não estiver sincronizando conforme o esperado, ele pode ser para um subconjunto de usuários ou para todos os usuários.

- Se você tiver um problema com objetos individuais, em seguida, consulte [Solucionar problemas de um único objeto que não está sincronizando senhas](#troubleshoot-one-object-that-is-not-synchronizing-passwords).
- Se você tiver um problema onde não há senhas são sincronizadas, consulte [Solucionar problemas onde não há senhas são sincronizadas](#troubleshoot-issues-where-no-passwords-are-synchronized).

### <a name="troubleshoot-one-object-that-is-not-synchronizing-passwords"></a>Solucionar problemas de um único objeto que não está sincronizando senhas
Você pode facilmente solucionar problemas de sincronização de senha revisando o status de um objeto.

Inicie em **computadores e usuários do Active Directory**. Localize o usuário e verifique se o **que usuário deve alterar senha no próximo logon** está desmarcada.
![Senhas produtivas do Active Directory](./media/active-directory-aadconnectsync-implement-password-synchronization/adprodpassword.png)  
Se ele estiver selecionado, em seguida, peça ao usuário para entrar e alterar a senha. Senhas temporárias não são sincronizadas ao Azure AD.

Se ele parecer correto no Active Directory, a próxima etapa é acompanhar o usuário do mecanismo de sincronização. Seguindo o usuário do Active Directory local ao Azure AD, você pode ver se há um erro descritivo no objeto.

1. Inicie o **[Gerenciador de serviço de sincronização](active-directory-aadconnectsync-service-manager-ui.md)**.
2. Clique em **conectores**.
3. Selecione o **Conector do Active Directory** que o usuário está localizado em.
4. Selecione o **espaço de pesquisa de conector**.
5. Localize o usuário que você está procurando.
6. Selecione a guia de **linhagem** e certifique-se de que pelo menos uma regra de sincronização mostra a **Sincronização de senha** como **True**. A configuração padrão, o nome da regra de sincronização é **do AD - AccountEnabled de usuário**.  
    ![Informações de linhagem sobre um usuário](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)  
7. [Siga o usuário](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system) metaverso para espaço do Azure AD conector. O objeto do espaço de conector deve ter uma regra de saída com a **Sincronização de senha** definida como **True**. Na configuração padrão, o nome da regra de sincronização é **Out para AAD - usuário ingressar**.  
    ![Propriedades de espaço de conector de um usuário](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)  
8. Para ver os detalhes de sincronização de senha do objeto para semana passada, clique em **log....**.  
    ![Detalhes do log de objeto](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)  

Coluna status pode ter os seguintes valores:

Status | Descrição
---- | -----
Sucesso | Senha tenha sido sincronizada com êxito.
FilteredByTarget | Senha está definida para o **usuário deve alterar senha no próximo logon**. Senha não foi sincronizada.
NoTargetConnection | Nenhum objeto no metaverso ou no espaço do conector Azure AD.
SourceConnectorNotPresent | Nenhum objeto encontrado no espaço do conector do Active Directory local.
TargetNotExportedToDirectory | O objeto no espaço do conector Azure AD ainda não foi exportado.
MigratedCheckDetailsForMoreInfo | Entrada do log foi criada antes da compilação 1.0.9125.0 e é mostrada em seu estado herdado.

### <a name="troubleshoot-issues-where-no-passwords-are-synchronized"></a>Solucionar problemas de onde não há senhas são sincronizadas
Inicie executando o script na seção [obter o status de configurações de sincronização de senha](#get-the-status-of-password-sync-settings). Ele oferece uma visão geral da configuração de sincronização de senha.  
![Saída de script do PowerShell de configurações de sincronização de senha](./media/active-directory-aadconnectsync-implement-password-synchronization/psverifyconfig.png)  
Se o recurso não está habilitado no Azure AD ou se o status de canal de sincronização não está habilitado, execute o Assistente de instalação de conectar. Selecione as **Opções de sincronização de personalizar** e desmarcar a sincronização de senha. Essa alteração temporariamente desativa o recurso. Em seguida, execute o assistente novamente e habilitar novamente a sincronização de senha. Execute o script novamente para verificar se a configuração está correta.

Não se o script mostra que há nenhuma pulsação, execute o script em [acionar uma sincronização completa de todas as senhas](#trigger-a-full-sync-of-all-passwords). Esse script também pode ser usado para outros cenários em que a configuração está correta, mas as senhas não são sincronizadas.

#### <a name="get-the-status-of-password-sync-settings"></a>Obter o status de configurações de sincronização de senha

```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Acionar uma sincronização completa de todas as senhas
Você pode acionar uma sincronização completa de todas as senhas usando o seguinte script:

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Próximas etapas

* [Azure AD conectar sincronização: Opções de personalização de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
