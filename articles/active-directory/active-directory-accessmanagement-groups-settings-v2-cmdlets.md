<properties
    pageTitle="Azure cmdlets de visualização do Active Directory do PowerShell para gerenciamento de grupo no Azure AD | Microsoft Azure"
    description="Esta página fornece exemplos do PowerShell para ajudá-lo a gerenciar seus grupos do Active Directory do Azure"
    keywords="PowerShell do Azure AD, Active Directory do Azure, gerenciamento de grupos, grupo"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="curtand"/>

# <a name="azure-active-directory-preview-cmdlets-for-group-management"></a>Azure cmdlets de visualização do Active Directory para gerenciamento de grupo

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-groups-create-azure-portal.md)
- [Azure portal clássico](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)

O seguinte documento fornecerá com exemplos de como usar o PowerShell para gerenciar seus grupos do Azure Active Directory (AD Azure).  Ele também fornece informações sobre como configurar com o módulo do PowerShell do Azure AD preview. Primeiro, você deve [baixar o módulo do PowerShell do Azure AD](http://go.microsoft.com/fwlink/p/?LinkId=828627).

## <a name="installing-the-azure-ad-powershell-module"></a>Instalar o módulo do PowerShell do Azure AD

Para instalar o módulo de visualização do AzureAD PowerShell, use os seguintes comandos:

    PS C:\Windows\system32> install-module azureadpreview

Para verificar se o módulo de visualização foi instalado, use o seguinte comando:

    PS C:\Windows\system32> get-module azureadpreview

    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     1.1.146.0  azureadpreview                      {Add-AzureADAdministrati...}

Agora você pode começar a usar os cmdlets no módulo. Para obter uma descrição completa dos cmdlets no módulo AzureAD Preview, consulte a [documentação de referência online](https://msdn.microsoft.com/library/azure/mt757216.aspx).

## <a name="connecting-to-the-directory"></a>Conectar-se ao diretório
Antes de iniciar Gerenciando grupos usando cmdlets do PowerShell do Azure AD preview, você deve se conectar a sua sessão do PowerShell à pasta que você deseja gerenciar. Para fazer isso, use o seguinte comando:

    PS C:\Windows\system32> Connect-AzureAD -Force

O cmdlet solicitará as credenciais que você deseja usar para acessar seu diretório. Neste exemplo, estamos usando karen@drumkit.onmicrosoft.com para acessar o diretório de demonstração. O cmdlet retornará uma confirmação para mostrar que a sessão foi conectada com êxito ao seu diretório:

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

Agora você pode começar a usar os cmdlets de visualização de AzureAD para gerenciar grupos no diretório.

## <a name="retrieving-groups"></a>Recuperando grupos
Para recuperar grupos existentes do seu diretório, você pode usar o cmdlet Get-AzureADGroups. Para recuperar todos os grupos no diretório, use o cmdlet sem parâmetros:

    PS C:\Windows\system32> get-azureadgroup

O cmdlet vai retornar todos os grupos no diretório conectado.

Você pode usar o parâmetro - ID do objeto para recuperar um grupo específico para a qual você especificar ID do objeto do grupo:

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

O cmdlet agora retornará o grupo cuja ID do objeto corresponde o valor do parâmetro que você inseriu:

    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Você pode procurar por um grupo específico usando o filtro parâmetro-. Esse parâmetro usa uma cláusula de filtro ODATA e retorna todos os grupos que correspondem ao filtro, como no exemplo seguinte:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Observe que os cmdlets do AzureAD PowerShell implementar o padrão de consulta OData, mais informações podem ser encontradas [aqui](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="creating-groups"></a>Criação de grupos
Para criar um novo grupo no seu diretório, use o cmdlet New-AzureADGroup. Esse cmdlet cria um novo grupo de segurança chamado "Marketing":

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="updating-groups"></a>Atualizando grupos
Para atualizar um grupo existente, use o cmdlet Set-AzureADGroup. Neste exemplo, podemos está alterando a propriedade DisplayName do grupo "Administradores Intune." Primeiro, vamos está encontrando o grupo usando o cmdlet Get-AzureADGroup e filtrar usando o atributo DisplayName:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Em seguida, podemos está alterando a propriedade de descrição para o novo valor "Intune dispositivo administradores":

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

Agora, se nós encontrar o grupo novamente, vemos que a propriedade Description é atualizada para refletir o novo valor:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

## <a name="deleting-groups"></a>Excluindo grupos
Para excluir grupos de seu diretório, use o cmdlet remover AzureADGroup da seguinte maneira:

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="managing-members-of-groups"></a>Gerenciamento de membros de grupos
Se você precisar adicionar novos membros a um grupo, use o cmdlet Add-AzureADGroupMember. Este comando adiciona um membro ao grupo Administradores de Intune que usamos no exemplo anterior:

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

O parâmetro - ID do objeto é a ID do objeto do grupo ao qual podemos deseja adicionar um membro e o RefObjectId - é a ID do objeto do usuário que queremos adicionar como um membro ao grupo.

Para acessar os membros existentes de um grupo, use o cmdlet Get-AzureADGroupMember, como neste exemplo:

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                        72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                        8120cc36-64b4-4080-a9e8-23aa98e8b34f User

Para remover o membro que adicionamos anteriormente ao grupo, use o cmdlet Remove-AzureADGroupMember, conforme é mostrado aqui:

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Para verificar a membership(s) de grupo de um usuário, use o cmdlet Select-AzureADGroupIdsUserIsMemberOf. Esse cmdlet leva como seus parâmetros a ID do objeto do usuário para o qual deseja verificar as associações de grupo e uma lista de grupos para os quais verificar as associações. A lista de grupos deve ser fornecido no formulário de uma variável de complexo do tipo "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck", então vamos primeiro deve criar uma variável com esse tipo:

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

Em seguida, fornecemos valores para as IDs de grupo verificar no atributo "IDs de grupo" dessa variável complexo:

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

Agora, se quisermos verificar as associações de grupo de um usuário com a ID do objeto 72cd4bbd-2594-40a2-935c-016f3cfeeeea contra grupos no $g, devemos usar:

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                               Value
    -------------                                                                                               -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


O valor retornado é uma lista de grupos dos quais esse usuário é membro. Você também pode aplicar esse método para verificar a associação de contatos, grupos ou objetos de serviço para uma determinada lista de grupos, usando selecione AzureADGroupIdsContactIsMemberOf, selecione AzureADGroupIdsGroupIsMemberOf ou selecione-AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="managing-owners-of-groups"></a>Gerenciar proprietários de grupos
Para adicionar os proprietários de um grupo, use o cmdlet Add-AzureADGroupOwner:

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

O parâmetro - ID do objeto é a ID do objeto do grupo ao qual desejamos adicionar um proprietário e o RefObjectId - é a ID do objeto do usuário que queremos adicionar como um proprietário do grupo.

Para recuperar os proprietários de um grupo, use o Get-AzureADGroupOwner:

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

O cmdlet retornará a lista de proprietários para o grupo especificado:

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                        e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Se você quiser remover um proprietário de um grupo, use AzureADGroupOwner remover:

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar mais documentação do Azure Active Directory PowerShell em [Cmdlets do Azure Active Directory](http://go.microsoft.com/fwlink/p/?LinkId=808260).

* [Gerenciando o acesso aos recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)

* [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
