<properties
    pageTitle="Serviços de domínio do Azure AD: Habilitar a sincronização de senha | Microsoft Azure"
    description="Introdução ao Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="maheshu"/>

# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Habilitar a sincronização de senha com os serviços de domínio do Azure AD
Em tarefas anteriores, você ativou a serviços de domínio do Azure AD para seu locatário do Azure AD. A próxima tarefa é habilitar a sincronização de senhas para os serviços de domínio do Azure AD. Depois que a sincronização de credencial é configurada, os usuários possam entrar no domínio gerenciado usando suas credenciais corporativas.

As etapas envolvidas são diferentes com base em se sua organização tem um Azure AD somente na nuvem locatário ou está configurado para sincronizar com seu diretório local usando o Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Somente na nuvem Azure AD de locatários](active-directory-ds-getting-started-password-sync.md)
- [Sincronizado locatário Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-synced-azure-ad-tenant"></a>Tarefa 5: Habilitar a sincronização de senha para serviços de domínio AAD para um Azure sincronizado locatário do AD
R sincronizado Azure locatário do AD está configurado para sincronizar com o diretório de locais da sua organização usando o Azure AD Connect. Azure AD Connect não sincroniza NTLM e Kerberos credencial hash ao Azure AD por padrão. Para usar os serviços de domínio do Azure AD, você precisa configurar Azure AD Connect para sincronizar hash credenciais necessárias para autenticação NTLM e Kerberos. As etapas a seguir habilitam a sincronização do hash de credencial necessários em seu locatário do Azure AD.


### <a name="install-or-update-azure-ad-connect"></a>Instalar ou atualizar Azure AD Connect
Instale a versão mais recente do Azure AD Connect recomendada em um computador de unidas de domínio. Se você tiver uma instância existente do Azure AD Connect configuração, você precisa atualizá-lo para usar a versão mais recente do Azure AD Connect. Para evitar problemas/erros conhecidos que pode já corrigidos, certifique-se de que você sempre usa a versão mais recente do Azure AD Connect.

**[Conectar-se de download Azure AD](http://www.microsoft.com/download/details.aspx?id=47594)**

Versão recomendada: **1.1.281.0** - publicado em 7 de setembro de 2016.

  > [AZURE.WARNING] Você deve instalar a versão mais recente do Azure AD Connect para habilitar as credenciais de senha herdados (necessárias para autenticação NTLM e Kerberos) para sincronizar seu locatário do Azure AD recomendada. Esta funcionalidade não está disponível nas versões anteriores do Azure AD Connect ou com a ferramenta de DirSync herdada.

Instruções de instalação do Azure AD Connect estão disponíveis no seguinte artigo - [Introdução ao Azure AD Connect](../active-directory/active-directory-aadconnect.md)


### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Habilitar a sincronização de hash de credencial NTLM e Kerberos ao Azure AD
Execute o seguinte script do PowerShell em cada floresta AD, para forçar a sincronização de senha completo e habilitar o hash de credencial de todos os usuários locais sincronizar seu locatário do Azure AD. Esse script habilita o hash de credencial necessário para a autenticação NTLM/Kerberos ser sincronizada com seu locatário do Azure AD.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

Dependendo do tamanho do seu diretório (número de usuários, grupos etc), sincronização de hash de credencial para o Azure AD leva tempo. As senhas poderá ser usadas no domínio gerenciado dos serviços de domínio do Azure AD logo após o hash de credencial terão sido sincronizadas ao Azure AD.


<br>

## <a name="related-content"></a>Conteúdo relacionado

- [Habilitar a sincronização de senha para serviços de domínio AAD para um Azure somente na nuvem diretório AD](active-directory-ds-getting-started-password-sync.md)

- [Administrar um domínio gerenciado de serviços de domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Ingressar em uma máquina virtual Windows a um domínio gerenciado de serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Ingressar em uma máquina virtual de Red Hat Enterprise Linux em um domínio gerenciado de serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
