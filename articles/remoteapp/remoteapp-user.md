<properties
    pageTitle="Adicionar um usuário à sua coleção de RemoteApp Azure | Microsoft Azure"
    description="Saiba como adicionar usuários à sua coleção de RemoteApp do Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="how-to-add-a-user-to-your-azure-remoteapp-collection"></a>Como adicionar um usuário à sua coleção de RemoteApp do Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Antes de seus usuários podem ver e use os aplicativos do Azure RemoteApp, você precisa conceder-lhes acesso à sua coleção. Essa é a parte fácil: na guia **Acesso do usuário** , insira as informações da conta do usuário e clique na marca de seleção.

Quais informações de conta você precisa? Isso depende do tipo de conjunto que você criou (nuvem ou híbrido) e se você estiver usando o Office 365 ProPlus nessa coleção.

## <a name="supported-user-identities"></a>Identidades dos usuários com suporte

Os tipos de coleção diferentes (nuvem versus híbrido) suportam o uso de identidades de usuário diferentes para obter acesso a aplicativos.  

Para uma coleção de híbrido de RemoteApp, você precisa configurar o uma infraestrutura de domínio Active Directory local e um locatário do Azure Active Directory com integração de diretório (e, opcionalmente, logon único). Além disso, você precisa criar alguns objetos do Active Directory no diretório local.  

Para uma coleção de nuvem de RemoteApp, qualquer usuário que tenha o Active Directory do Azure identidades de suporte pode obter acesso de usuário para o RemoteApp para incluir Accounts da Microsoft.  Consulte a tabela a seguir.

Usuários do Office 365 são usuários do Active Directory do Azure. Se eles tiverem híbrido do Active Directory do Azure, diretório sincronizados contas, eles podem ser concedidos acesso de usuário em uma implantação híbrida de RemoteApp.   

Você pode usar esta tabela como uma referência rápida para o qual identidade é compatível com sua coleção e quais são os requisitos do Active Directory.

|Contas de usuário |Nuvem   |Híbrido|
|--------------|--------|------|
|Conta da Microsoft|     Sim|    Não|
|Azure Active Directory (AD Azure)| | |
|Somente nuvem Azure AD    |Sim    |Não |
|ADsync com a sincronização de senha  |Sim    |Sim    |
|ADsync sem sincronização de senha|  Sim |Não |
|ADsync com o AD FS  |Sim    |Sim    |
|[3º terceiros Azure suportados provedores de identidade](https://msdn.microsoft.com/library/azure/jj679342.aspx)  (exemplo Ping) |Sim    |Sim|
|Autenticação multifator    |Sim    |Sim    |

Consulte [mais informações](remoteapp-ad.md) sobre como configurar o Active Directory para RemoteApp.


> [AZURE.NOTE] Os usuários do Active Directory do Azure devem ser de locatário que está associada a sua assinatura. (Você pode exibir e modificar sua assinatura na guia **configurações** no portal. Consulte [alterar o locatário do Azure Active Directory usados pelo RemoteApp](remoteapp-changetenant.md) para obter mais informações.)

## <a name="office-365-proplus-user-account-information"></a>Informações de conta do Office 365 ProPlus usuário
Se você estiver usando a Office 365 ProPlus imagem de modelo em seu conjunto *ou* se você tiver criado uma imagem personalizada que usa o Office 365, você só tem permissão para adicionar usuários do Active Directory do Azure que têm assinaturas do Office 365 para o domínio padrão da sua assinatura. Consulte [usando o Office 365 com o Azure RemoteApp](remoteapp-o365.md) para obter mais informações.
