<properties
   pageTitle="Usar cmdlets do PowerShell com o Azure RemoteApp | Microsoft Azure"
   description="Saiba como usar cmdlets do Windows PowerShell no Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>



# <a name="use-windows-powershell-cmdlets-with-azure-remoteapp"></a>Usar cmdlets do Windows PowerShell com o Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

 Você pode usar os cmdlets do PowerShell do Azure RemoteApp para administrar e manter seus conjuntos. Use as seguintes informações para começar.

## <a name="get-the-cmdlets"></a>Obter os cmdlets 
-------------
Primeiro, baixe os cmdlets do Powershell do Azure [aqui](http://go.microsoft.com/?linkid=9811175), o RemoteApp cmdlets incluídos nele. 

Confira a [Ajuda do Azure RemoteApp cmdlet](https://msdn.microsoft.com/library/mt428031.aspx).

## <a name="configure-azure-cmdlets-to-use-your-subscription"></a>Configurar cmdlets do Azure para usar sua assinatura
------------------
Siga [Este guia](../powershell-install-configure.md) para que você pode usar os cmdlets em relação a sua assinatura do Azure.

Você pode usar essas etapas para começar rapidamente:

1.  Baixe e instale os [cmdlets do PowerShell do Azure](http://go.microsoft.com/?linkid=9811175).
2.  Abra o PowerShell do Microsoft Azure.
3.  Execute **Add-AzureAccount** para autenticar à sua assinatura do Azure. Quando solicitado, insira o mesmo nome de usuário e senha que você usa para entrar no portal do Azure.  
4.  Execute **Get-AzureSubscription** para listar as assinaturas associadas a uma conta de usuário. 
5.  Execute **Select AzureSubscription** e especifique o nome da assinatura ou a ID usar no console do PowerShell.

Parabéns, seu console do Azure PowerShell está configurado e pronto para usar. Lembre-se de que você precisará repeate as etapas de 2 a 5 toda vez que iniciar o console do PowerShell do Azure.  

## <a name="create-a-cloud-collection"></a>Criar um conjunto de nuvem
--------------------
É simple, execute o seguinte comando:

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

O comando acima publica automaticamente os aplicativos do Microsoft Office 365 (Excel, OneNote, Outlook, PowerPoint, Visio e Word).

Criação de coleção pode levar 30 minutos ou mais para ser concluída. Portanto, esse comando retorna uma ID de controle que você pode usar da seguinte maneira:


    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

Após a coleção, você pode adicionar usuários à coleção com o seguinte comando:

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

E pronto! Esse usuário deve ser capaz de se conectar ao aplicativo usando o cliente do Azure RemoteApp encontrado [aqui](https://www.remoteapp.windowsazure.com/).

## <a name="available-cmdlets"></a>Cmdlets disponíveis
Há muitos outros comandos que temos, documentação-los será lançado em breve:

Cmdlets de conjunto de RemoteApp básicos: 

- Novo AzureRemoteAppCollection
- Get-AzureRemoteAppCollection
- Set-AzureRemoteAppCollection
- Atualização AzureRemoteAppCollection
- Remover AzureRemoteAppCollection
- AzureRemoteAppUser adicionar
- Get-AzureRemoteAppUser
- Remover AzureRemoteAppUser
- Get-AzureRemoteAppSession
- Desconectar-AzureRemoteAppSession
- AzureRemoteAppSessionLogoff invocar
- Enviar-AzureRemoteAppSessionMessage
- Get-AzureRemoteAppProgram
- Get-AzureRemoteAppStartMenuProgram
- AzureRemoteAppProgram publicar
- Cancelar a publicação-AzureRemoteAppProgram
- Get-AzureRemoteAppCollectionUsageDetails
- Get-AzureRemoteAppCollectionUsageSummary
- Get-AzureRemoteAppPlan

Cmdlets de rede virtual RemoteApp:

- Novo AzureRemoteAppVNet
- Get-AzureRemoteAppVNet
- Set-AzureRemoteAppVNet
- Remover AzureRemoteAppVNet
- Get-AzureRemoteAppVpnDevice
- Get - AzureRemoteAppVpnDeviceConfigScript
- Redefinir AzureRemoteAppVpnSharedKey

Cmdlets de imagem de modelo de RemoteApp:

- Novo AzureRemoteAppTemplateImage
- Get-AzureRemoteAppTemplateImage
- Renomear-AzureRemoteAppTemplateImage
- Remover AzureRemoteAppTemplateImage

Outros cmdlets RemoteApp:

- Get-AzureRemoteAppLocation
- Get-AzureRemoteAppWorkspace
- Set-AzureRemoteAppWorkspace
- Get-AzureRemoteAppOperationResult
 
