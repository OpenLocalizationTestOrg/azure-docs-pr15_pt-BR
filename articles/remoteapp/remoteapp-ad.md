
<properties 
    pageTitle="Azure AD + requisitos do Active Directory do Azure RemoteApp | Microsoft Azure" 
    description="Saiba como configurar o Active Directory para trabalhar com o Azure RemoteApp." 
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



# <a name="azure-ad--active-directory-requirements-for-azure-remoteapp"></a>Azure AD + requisitos do Active Directory do Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.


Para o seu conjunto de híbrido do Azure RemoteApp ou para um conjunto de nuvem que você deseja federada usando AD Connect, você precisa fazer o seguinte.

### <a name="connect-azure-ad-and-active-directory"></a>Conectar Azure AD e o Active Directory

Se você quiser conectar seu locatário do Azure AD e seus ambientes do Active Directory local, use AD Connect. Levará você apenas [4 cliques](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) para conectar os dois diretórios.

Observação - sincronização de diretórios é necessária para conjuntos de híbrido.

### <a name="make-sure-your-domaincom-match"></a>Verifique se seu "@domain.com" corresponder
Antes de começar, certifique-se de que o nome UPN da floresta local corresponde o sufixo do seu domínio do Azure AD. 

Depois de configurar o sufixo UPN do domínio no Azure AD, todos os usuários efetuando login Azure RemoteApp fará logon como “user@ <the suffix you set up>". Certifique-se de que os usuários também podem fazer logon com a mesma user@suffix no domínio local. Em alguns casos você pode configurar um nome de domínio no Azure AD ao especificar um sufixo de domínio diferente para o usuário prem. Nesse caso, os usuários não será capazes de se conectar a qualquer domínio computadores ou recursos por meio do Azure RemoteApp.

Por exemplo, se você configurar seu sufixo de domínio UPN na AAD como contoso.com, mas alguns usuários em locais/AD são configurados para fazer login @contoso.uk, e em seguida, esses usuários não poderão fazer logon corretamente na coleção ARA. Usuários UPN no AAD e AD devem ser o mesmo para o logon seja possível"

### <a name="create-objects-for-azure-remoteapp"></a>Criar objetos para RemoteApp do Azure
Você também precisa criar os seguintes locais objetos do Active Directory:

- Uma conta de serviço para fornecer acesso aos recursos de domínio para programas RemoteApp unindo RDSH pontos de extremidade para o domínio local.
- Uma unidade organizacional (OU) para conter objetos de máquina RemoteApp. Uso da unidade Organizacional é recomendável (mas não obrigatório) para isolar as contas e políticas que você usará com RemoteApp.

Você precisa de ambos desses objetos quando você cria sua coleção de RemoteApp, então certifique-se de fazer essas etapas primeiro.