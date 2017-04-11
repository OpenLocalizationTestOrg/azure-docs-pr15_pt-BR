<properties
    pageTitle="Serviços de domínio Active Directory do Azure: Guia de solução | Microsoft Azure"
    description="Guia de solução de problemas para os serviços de domínio do Azure AD"
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
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Serviços de domínio do Azure AD - guia de solução de problemas
Este artigo fornece dicas de solução de problemas que você pode encontrar ao configurar ou administração de serviços de domínio do Azure Active Directory (AD).


## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Você não pode habilitar serviços de domínio do Azure AD para seu diretório Azure AD
Esta seção ajuda você a solucionar problemas de erros quando você tentar ativar os serviços de domínio do Azure AD para seu diretório e ele falha ou obtém alternado para 'Desabilitado'.

Escolha as etapas de solução de problemas que correspondem à mensagem de erro que você encontrar.

|**Mensagem de erro**|**Resolução**|
|---|:---|
|*O nome contoso100.com já está em uso na rede. Especifique um nome que não está em uso.*|[Conflito de nome de domínio na rede virtual](active-directory-ds-troubleshooting.md#domain-name-conflict)
|*Serviços de domínio não podem ser ativados no locatário Azure AD. O serviço não tem as permissões adequadas para o aplicativo chamado 'Sync de serviços de domínio do Azure AD'. Exclua o aplicativo chamado 'Sync de serviços de domínio do Azure AD' e tente ativar os serviços de domínio do seu locatário do Azure AD.*|[Serviços de domínio não tem as permissões adequadas para o aplicativo de sincronização de serviços de domínio do Azure AD](active-directory-ds-troubleshooting.md#inadequate-permissions)|
|*Serviços de domínio não podem ser ativados no locatário Azure AD. O aplicativo de serviços de domínio em seu locatário do Azure AD não tem as permissões necessárias para ativar os serviços de domínio. Exclua o aplicativo com o d87dcbc6-a371-462e-88e3-28ad15ec4e64 de identificador de aplicativo e tente ativar os serviços de domínio do seu locatário do Azure AD.*|[O aplicativo de serviços de domínio não está configurado corretamente em seu locatário](active-directory-ds-troubleshooting.md#invalid-configuration)|
|*Serviços de domínio não podem ser ativados no locatário Azure AD. O aplicativo do Microsoft Azure AD está desabilitado em seu locatário do Azure AD. Habilitar o aplicativo com o 00000002-0000-0000-c000-000000000000 de identificador de aplicativo e tente ativar os serviços de domínio do seu locatário do Azure AD.*|[O aplicativo Microsoft Graph está desabilitado em seu locatário do Azure AD](active-directory-ds-troubleshooting.md#microsoft-graph-disabled)|


### <a name="domain-name-conflict"></a>Conflito de nome de domínio
**Mensagem de erro:**

*O nome contoso100.com já está em uso na rede. Especifique um nome que não está em uso.*

**Correção:**

Certifique-se de que você não tem um domínio existente com o mesmo nome de domínio disponível na rede virtual. Por exemplo, suponha que você tenha um domínio chamado 'contoso.com' já disponíveis na rede virtual selecionada. Posteriormente, você tentar ativar um domínio de serviços de domínio do Azure AD gerenciado com o mesmo nome de domínio (ou seja, ' contoso.com') na rede virtual. Você encontrar uma falha ao tentar ativar os serviços de domínio do Azure AD.

Essa falha será devido a conflitos de nome para o nome de domínio da rede virtual. Nessa situação, você deve usar um nome diferente para configurar seu domínio gerenciado de serviços de domínio do Azure AD. Como alternativa, você pode eliminação provisionar o domínio existente e, em seguida, prossiga para ativar os serviços de domínio do Azure AD.


### <a name="inadequate-permissions"></a>Permissões inadequadas
**Mensagem de erro:**

*Serviços de domínio não podem ser ativados no locatário Azure AD. O serviço não tem as permissões adequadas para o aplicativo chamado 'Sync de serviços de domínio do Azure AD'. Exclua o aplicativo chamado 'Sync de serviços de domínio do Azure AD' e tente ativar os serviços de domínio do seu locatário do Azure AD.*

**Correção:**

Verificar se existe um aplicativo com o nome 'Sync de serviços de domínio do Azure AD' no diretório Azure AD. Se este aplicativo existir, excluí-la e, em seguida, habilitar novamente o serviços de domínio do Azure AD.

Execute as seguintes etapas para verificar a presença do aplicativo e excluí-lo, se o aplicativo existe:

  1. Navegue até o **portal clássico Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
  2. Selecione o nó do **Active Directory** no painel esquerdo.
  3. Selecione o locatário do Azure AD (diretório) para o qual você gostaria de ativar os serviços de domínio do Azure AD.
  4. Navegue até a guia de **aplicativos** .
  5. Selecione a opção **minha empresa possui de aplicativos** na lista suspensa.
  6. Verificar um aplicativo chamado **sincronização de serviços de domínio do Azure AD**. Se o aplicativo já existir, vá para excluí-la.
  7. Depois que o aplicativo tenha sido excluído, tente ativar os serviços de domínio do Azure AD novamente.


### <a name="invalid-configuration"></a>Configuração inválida
**Mensagem de erro:**

*Serviços de domínio não podem ser ativados no locatário Azure AD. O aplicativo de serviços de domínio em seu locatário do Azure AD não tem as permissões necessárias para ativar os serviços de domínio. Exclua o aplicativo com o d87dcbc6-a371-462e-88e3-28ad15ec4e64 de identificador de aplicativo e tente ativar os serviços de domínio do seu locatário do Azure AD.*

**Correção:**

Verifique se você tiver um aplicativo com o nome 'AzureActiveDirectoryDomainControllerServices' (com um identificador de aplicativo de d87dcbc6-a371-462e-88e3-28ad15ec4e64) no seu diretório Azure AD. Se este aplicativo existir, você precisa excluí-lo e, em seguida, habilitar novamente o serviços de domínio do Azure AD.

Use o seguinte script do PowerShell para encontrar o aplicativo e exclua-o.

> [AZURE.NOTE] Esse script usa cmdlets **Do PowerShell do Azure AD versão 2** . Para uma lista completa de todos os cmdlets disponíveis e baixar o módulo, leia a [documentação de referência AzureAD PowerShell](https://msdn.microsoft.com/library/azure/mt757189.aspx).

```
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph desabilitado
**Mensagem de erro:**

Serviços de domínio não podem ser ativados no locatário Azure AD. O aplicativo do Microsoft Azure AD está desabilitado em seu locatário do Azure AD. Habilitar o aplicativo com o 00000002-0000-0000-c000-000000000000 de identificador de aplicativo e tente ativar os serviços de domínio do seu locatário do Azure AD.

**Correção:**

Verifique se você tiver desabilitado um aplicativo com o identificador 00000002-0000-0000-c000-000000000000. Este aplicativo é o aplicativo do Microsoft Azure AD e fornece acesso de API do gráfico em seu locatário do Azure AD. Serviços de domínio do AD Azure necessidades este aplicativo a ser habilitado para sincronizar seu locatário do Azure AD para seu domínio gerenciado.

Para resolver esse erro, habilitar este aplicativo e tente ativar os serviços de domínio do seu locatário do Azure AD.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Os usuários não conseguem entrar no domínio gerenciado serviços de domínio do Azure AD
Se um ou mais usuários em seu locatário do Azure AD não consegue entrar no domínio gerenciado recém-criado, execute as seguintes etapas de solução de problemas:

- **Entrar usando o formato UPN:** Tente entrar usando o formato UPN (por exemplo, 'joeuser@contoso.com') em vez de no formato de SAMAccountName ('CONTOSO\joeuser'). SAMAccountName pode ser gerado automaticamente para usuários cujo prefixo UPN é muito longo ou é a mesma que outro usuário no domínio gerenciado. O formato UPN certamente ser exclusivo dentro de um locatário do Azure AD.

> [AZURE.NOTE] É recomendável usar o formato UPN para entrar no domínio gerenciado serviços de domínio do Azure AD.

- Certifique-se de que você tenha [habilitado para sincronização de senha](active-directory-ds-getting-started-password-sync.md) de acordo com as etapas descritas no guia de Introdução.

- **Contas externas:** Certifique-se de que a conta de usuário afetado não é uma conta externa no locatário Azure AD. Exemplos de contas externas contas da Microsoft (por exemplo, 'joe@live.com') ou contas de usuário do externos directory do Azure AD. Como os serviços de domínio do Azure AD não tem as credenciais para essas contas de usuário, esses usuários não podem entrar no domínio gerenciado.

- **Sincronizado contas:** Se as contas de usuário afetadas são sincronizadas em um diretório local, verifique se:
    - Você tiver implantado ou atualizados para a [versão mais recente recomendada do Azure AD Connect](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect).

    - Você configurou Azure AD Connect para [realizar uma sincronização completa](active-directory-ds-getting-started-password-sync.md).

    - Dependendo do tamanho do seu diretório, ele pode demorar um pouco para contas de usuário e credencial hash esteja disponível nos serviços de domínio do Azure AD. Certifique-se de que você aguarda tempo suficiente antes repetindo a autenticação (dependendo do tamanho do seu diretório - algumas horas para um dia ou dois para grandes diretórios).

    - Se o problema persistir depois de verificar as etapas anteriores, tente reiniciar o serviço de sincronização do AD do Microsoft Azure. A partir de sua máquina de sincronização, inicie um prompt de comando e execute os seguintes comandos:
      1. Parar líquido 'Microsoft Azure AD Sync'
      2. Iniciar líquido 'Microsoft Azure AD Sync'

- **Contas somente na nuvem**: se a conta de usuário afetado for uma conta de usuário somente na nuvem, certifique-se de que o usuário tiver alterado sua senha depois habilitado serviços de domínio do Azure AD. Essa etapa faz com que a credencial hash necessário para serviços de domínio do Azure AD será gerado.


## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Usuários removidos do seu locatário do Azure AD não são removidos do seu domínio gerenciado
Azure AD protege você contra exclusão acidental de objetos de usuário. Quando você exclui uma conta de usuário do seu locatário do Azure AD, objeto de usuário correspondente é movido para a Lixeira. Quando essa operação de exclusão é sincronizada com seu domínio gerenciado, ela faz com que a conta de usuário correspondente a ser marcado como desativado. Este recurso ajuda você a recuperar ou exclusão a conta de usuário mais tarde.

Para remover a conta de usuário totalmente de seu domínio gerenciado, exclua o usuário permanentemente do seu locatário do Azure AD. Use o cmdlet do PowerShell Remove-MsolUser com o '-RemoveFromRecycleBin' opção, conforme descrito neste [artigo da MSDN](https://msdn.microsoft.com/library/azure/dn194132.aspx).


## <a name="contact-us"></a>Fale conosco
Entre em contato com a equipe de produto do Azure Active Directory Domain Services para [compartilhar comentários ou para suporte] (active-directory-ds-contato-us.md).
