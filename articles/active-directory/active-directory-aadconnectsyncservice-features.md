<properties
    pageTitle="Recursos do serviço de sincronização do Azure AD Connect e configuração | Microsoft Azure"
    description="Descreve os recursos de lado de serviço do serviço de sincronização do Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="andkjell;markvi"/>

# <a name="azure-ad-connect-sync-service-features"></a>Recursos do serviço de sincronização do Azure AD Connect

O recurso de sincronização do Azure AD Connect tem dois componentes:

- O componente local denominado **sincronização do Azure AD Connect**, também chamado de **mecanismo de sincronização**.
- O serviço que reside no Azure AD também conhecido como o **serviço de sincronização do Azure AD Connect**

Este tópico explica como funcionam os seguintes recursos do **serviço de sincronização do Azure AD Connect** e como você pode configurá-los usando o Windows PowerShell.

Essas configurações são configuradas por do [Active Directory módulo Azure para Windows PowerShell](http://aka.ms/aadposh). Baixe e instale-o separadamente do Azure AD Connect. Os cmdlets documentados neste tópico foram introduzidos no [2016 março liberar (build 9031.1)](http://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Se você não tiver os cmdlets documentados neste tópico ou eles não produzem o mesmo resultado, verifique se que você executar a versão mais recente.

Para ver a configuração no seu diretório Azure AD, execute `Get-MsolDirSyncFeatures`.  
![Resultado de Get-MsolDirSyncFeatures](./media/active-directory-aadconnectsyncservice-features/getmsoldirsyncfeatures.png)

Muitas dessas configurações só podem ser alteradas pelos Azure AD Connect.

As configurações a seguir podem ser configuradas por `Set-MsolDirSyncFeature`:

DirSyncFeature | Comentário
--- | ---
[DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) | Permite que um atributo em quarentena quando é uma duplicata de outro objeto em vez de falhando todo o objeto durante a exportação.
[EnableSoftMatchOnUpn](#userprincipalname-soft-match) | Permite que objetos para a junção userPrincipalName além de endereço primário SMTP.
[SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) | Permite que o mecanismo de sincronização para atualizar o atributo userPrincipalName gerenciados/licenciado usuários (não federado).

Depois que você ativou um recurso, ele não pode ser desativado novamente.

>[AZURE.NOTE] De 24 de agosto de 2016 o recurso *resiliência de atributo duplicata* está habilitado por padrão para novato Azure diretórios do AD. Este recurso também será implantado e habilitado em diretórios criados antes desta data. Você receberá uma notificação por email quando seu diretório está prestes a acessar esse recurso ativado.

As configurações a seguir são configuradas por Azure AD Connect e não pode ser modificadas por `Set-MsolDirSyncFeature`:

DirSyncFeature | Comentário
--- | ---
DeviceWriteback | [Azure AD Connect: Write-back de dispositivo de habilitando](active-directory-aadconnect-feature-device-writeback.md)
DirectoryExtensions | [Sincronização do Azure AD Connect: extensões de diretório](active-directory-aadconnectsync-feature-directory-extensions.md)
PasswordSync | [Implementação de sincronização de senha com a sincronização do Azure AD Connect](active-directory-aadconnectsync-implement-password-synchronization.md)
UnifiedGroupWriteback | [Visualização: Write-back de grupo](active-directory-aadconnect-feature-preview.md#group-writeback)
UserWriteback | Não suportadas atualmente.

## <a name="duplicate-attribute-resiliency"></a>Resiliência de atributo duplicado
Em vez de com falha para provisionar objetos com UPNs duplicados / proxyAddresses, o atributo duplicado é "em quarentena" e um valor temporário será atribuído. Quando o conflito for resolvido, o UPN temporário é alterado automaticamente para o valor correto. Esse comportamento pode ser habilitado para UPN e proxyAddress separadamente. Para obter mais detalhes, consulte [sincronização de identidade e resiliência de atributo duplicado](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>Coincidir suave UserPrincipalName
Quando este recurso está habilitado, suaves-correspondência está habilitado para UPN além o [endereço primário SMTP](https://support.microsoft.com/kb/2641663), que sempre está habilitado. Suaves-coincidir é usado para fazer a correspondência de usuários de nuvem existentes no Azure AD com usuários locais.

Se você precisar coincidir contas do AD com contas existentes criadas na nuvem local não estiver usando o Exchange Online e esse recurso é útil. Neste cenário, você geralmente não tem um motivo para definir o atributo de SMTP na nuvem.

Este recurso está em por padrão para recém-criadas diretórios do Azure AD. Você pode ver se esse recurso é habilitado para você executando:  
```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Se esse recurso não está habilitado para o diretório do Azure AD, em seguida, você pode ativá-lo executando:  
```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Sincronizar atualizações de userPrincipalName
Historicamente, as atualizações para o atributo UserPrincipalName usando o serviço de sincronização do local tiver sido bloqueado, a menos que as seguintes condições forem verdadeiras:

- O usuário é gerenciado (não federado).
- O usuário não recebeu uma licença.

Para obter mais detalhes, consulte [nomes de usuário no Office 365, o Azure ou o Intune não corresponderem o UPN local ou a ID de logon alternativo](https://support.microsoft.com/kb/2523192).

Esse recurso permite que o mecanismo de sincronização atualizar o userPrincipalName quando ele é alterado no local e usar sincronização de senha. Se você usar a federação, este recurso não é suportado.

Este recurso está em por padrão para recém-criadas diretórios do Azure AD. Você pode ver se esse recurso é habilitado para você executando:  
```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Se esse recurso não está habilitado para o diretório do Azure AD, em seguida, você pode ativá-lo executando:  
```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Após ativar esse recurso, os valores de userPrincipalName existentes permanecerá como-é. Na próxima alteração do userPrincipalName atributo local, a sincronização de delta normal em usuários atualizará o UPN.  

## <a name="see-also"></a>Consulte também

- [Sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md)
- [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
