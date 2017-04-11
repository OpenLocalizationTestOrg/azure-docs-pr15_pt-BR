
<properties 
    pageTitle="Como usar o Azure RemoteApp com contas de usuário do Office 365 | Microsoft Azure"
    description="Saiba como usar o Azure RemoteApp com minhas contas de usuário do Office 365"
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-to-use-azure-remoteapp-with-office-365-user-accounts"></a>Como usar o Azure RemoteApp com contas de usuário do Office 365

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Se você tiver uma assinatura do Office 365, você terá um Azure Active Directory que armazena os nomes de usuário e senhas usadas para acessar os serviços do Office 365. Por exemplo, quando os usuários ativarem o Office 365 ProPlus eles autenticam Azure AD para verificar se há licenças. A maioria dos clientes gostaria de usar o mesmo diretório com o Azure RemoteApp.

Se você estiver implantando o Azure RemoteApp provavelmente você está usando uma assinatura do Azure que está associada a um anúncio Azure diferentes. Para poder usar seu diretório do Office 365, você precisará mover a assinatura do Azure nessa pasta.

Para obter informações sobre como implantar aplicativos clientes do Office 365, veja [como usar sua assinatura do Office 365 com o Azure RemoteApp](remoteapp-officesubscription.md).
 
## <a name="phase-1-register-your-free-office-365-azure-active-directory-subscription"></a>Fase 1: Registrar sua assinatura gratuita do Office 365 Azure Active Directory
Se você estiver usando o portal de clássico Azure, use as etapas em [registrar sua assinatura gratuita do Azure Active Directory](https://technet.microsoft.com/library/dn832618.aspx) para obter acesso administrativo ao seu Azure AD através do Portal de gerenciamento do Azure. Como resultado desse processo, você deve ser capaz de fazer logon no portal do Azure e ver seu diretório lá – neste momento você não verá muito mais desde que a assinatura completa Azure que estiver usando com o Azure RemoteApp está em um diretório diferente.

Lembre-se o nome e senha da conta de administrador que você criou nesta etapa – eles serão necessários na fase 2.

Se você estiver usando o portal do Azure, confira [como registrar e ativar um gratuita do Azure Active Directory usando o portal do Office 365](http://azureblogger.com/2016/01/how-to-register-and-activate-a-free-azure-active-directory-using-office-365-portal/).

## <a name="phase-2-change-the-azure-ad-associated-with-your-azure-subscription"></a>Fase 2: Altere o Azure AD associado a sua assinatura do Azure.
Vamos alterar sua assinatura do Azure seu diretório atual no diretório do Office 365, que podemos trabalhou na fase 1.

Siga as instruções descritas em [mudar o locatário do Azure Active Directory do Azure RemoteApp](remoteapp-changetenant.md). Atenção especial para as seguintes etapas:

- Etapa #1: Se você implantou Azure RemoteApp (ARA) nesta inscrição, verifique se que você remove todas as contas de usuário do Azure AD de qualquer conjuntos de ARA primeiro, antes de tentar mais nada. Como alternativa, você pode considerar excluindo qualquer coleções existentes.
- Etapa #2: Este é uma etapa fundamental. Você precisa usar uma conta da Microsoft (por exemplo, @outlook.com) como um administrador de serviço com assinaturas; isso ocorre porque não temos quaisquer contas de usuário do anúncio Azure existente anexados à assinatura – se fazemos, podemos não poderá movê-lo para um anúncio Azure diferentes.
- Etapa 4 #: Ao adicionar uma pasta existente, o sistema solicitará que você entrar com a conta de administrador para esse diretório. Certifique-se de usar a conta de administrador da fase 1.
- Etapa 5 #: Altere o diretório pai da assinatura para seu diretório do Office 365. O resultado final deve ser que em Configurações -> assinaturas sua assinatura listas no diretório do Office 365. 
![Alterar o diretório pai da assinatura](./media/remoteapp-o365user/settings.png)
 

Neste ponto sua assinatura do Azure RemoteApp está associada com o Office 365 Azure AD; Você pode usar as contas de usuário existentes do Office 365 com o Azure RemoteApp!




