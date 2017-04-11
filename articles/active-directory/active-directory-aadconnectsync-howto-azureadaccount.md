<properties
    pageTitle="Sincronização do Azure AD Connect: como gerenciar a conta de serviço do Azure AD | Microsoft Azure"
    description="Este tópico documentos como restaurar a conta de serviço do Azure AD."
    services="active-directory"
    keywords="AADSTS70002, AADSTS50054, como redefinir a senha para a conta de serviço do conector de sincronização do Azure AD Connect"
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
    ms.date="09/01/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Sincronização do Azure AD Connect: como gerenciar a conta de serviço do Azure AD
A conta de serviço usada pelo Azure AD conector deve para ser serviço gratuito. Se você precisar redefinir suas credenciais, este tópico é para você. Por exemplo, se um Administrador Global tem por engano redefinir a senha da conta de serviço usando o PowerShell.

## <a name="reset-the-credentials"></a>Redefinir as credenciais
Se a conta de serviço definida no Azure AD conector não puder contatar Azure AD devido a problemas de autenticação, poderá redefinir a senha.

1. Entrar no servidor de sincronização do Azure AD Connect e iniciar o PowerShell.
2. Executar `Add-ADSyncAADServiceAccount`.  
![Addadsyncaadserviceaccount de cmdlet do PowerShell](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Fornece credenciais de Administrador Global do Azure AD.

Esse cmdlet redefine a senha da conta de serviço e atualizá-lo no Azure AD e do mecanismo de sincronização.

## <a name="known-issues-these-steps-can-solve"></a>Estas etapas podem resolver os problemas conhecidos
Esta seção é uma lista de erros relatados por clientes que foram corrigidos por um redefinir na conta de serviço do Azure AD de credenciais.

-----------
Evento 6900  
O servidor encontrou um erro inesperado ao processar uma notificação de alteração de senha:  
AADSTS70002: Credenciais de validação de erro. AADSTS50054: Senha antiga é usada para autenticação.

----------
Evento 659  
Erro ao recuperar a configuração de sincronização de política de senha. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Credenciais de validação de erro. AADSTS50054: Senha antiga é usada para autenticação.

## <a name="next-steps"></a>Próximas etapas

**Tópicos de visão geral**

- [Sincronização do Azure AD Connect: entender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
- [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
