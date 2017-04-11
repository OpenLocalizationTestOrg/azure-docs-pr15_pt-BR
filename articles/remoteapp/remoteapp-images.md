<properties
    pageTitle="Quais são as imagens de modelo do Azure RemoteApp? | Microsoft Azure"
    description="Saiba mais sobre as imagens de modelos incluídas com o Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="what-is-in-the-azure-remoteapp-template-images"></a>Quais são as imagens de modelo do Azure RemoteApp?

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Sua assinatura do Azure RemoteApp inclui três imagens de modelo:


- Windows Server 2012
- Microsoft Office 365 ProPlus (assinatura do Office 365 obrigatória)
- Microsoft Office 2013 Professional Plus (somente para avaliação)

> [AZURE.IMPORTANT]Sua assinatura do Azure RemoteApp concede que acesso ao software nas imagens, com exceção do Office 365 ProPlus, que exige uma assinatura separada e Office 2013, que não podem ser usados em produção. Isso significa que você pode compartilhar programas ou aplicativos nas imagens de modelo com seus usuários. Por exemplo, se você criar um conjunto que usa a imagem do Windows Server 2012 R2, você pode publicar proteção de ponto de extremidade do Centro de sistema para usuários acessem através de RemoteApp.
>
> Confira os [detalhes do licenciamento RemoteApp](remoteapp-licensing.md) para obter mais informações. E informações de licença [Usando o Office com o Azure RemoteApp](remoteapp-o365.md) para o Office.

Leia mais para obter detalhes sobre o que cada imagem contém.

## <a name="windows-server-2012-r2--the-vanilla-image"></a>Windows Server 2012 R2 ("a baunilha imagem")
Esta imagem baseia-se no sistema operacional do Microsoft Windows Server 2012 R2 Datacenter e tem as seguintes funções e recursos instalados para atender a requisitos de imagens de modelos do Azure RemoteApp:


- .NET framework 4,5, 3.5.1, 3.5
- Experiência Desktop
- Serviços de tinta e manuscrito
- Media Foundation
- Host de sessão de área de trabalho remota
- O Windows PowerShell 4.0
- O Windows PowerShell ISE
- Suporte a WoW64

Esta imagem também tem os seguintes aplicativos instalados:

- Adobe Flash Player
- O Microsoft Silverlight
- Proteção de ponto de extremidade do Microsoft System Center 2012
- Microsoft Windows Media Player


## <a name="microsoft-office-365-proplus-subscription-required"></a>Microsoft Office 365 ProPlus (assinatura obrigatória)
O Office 365 é o aplicativo mais solicitado, criamos uma imagem "personalizada" para trabalhar com.

Esta imagem é uma extensão da imagem baunilha e tem os seguintes componentes do Microsoft Office 365 ProPlus instalado além os componentes descritos na imagem do Windows Server 2012 R2:


- Acesso
- Excel
- Lync
- OneNote
- SkyDrive Pro (Observe que o agente de sincronização não é suportado para uso com o Azure RemoteApp)
- Outlook
- PowerPoint
- Word
- Revisores de texto do Microsoft Office

A imagem também inclui o Visio Pro e o Project Pro.

E os seguintes aplicativos, assim:

- SQL Native client
- Driver ODBC
- Cliente de mineração de dados do SQL Server
- MasterDataServices cliente
- O Microsoft Publisher
- PowerQuery
- Power Map


Todas as funcionalidades de aplicativos do Office 365 ProPlus estão disponível somente para usuários que tenham um plano do Office 365 ProPlus. Para obter mais detalhes sobre o Office 365 planos de assinatura consulte [planos de serviço do Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx). Ainda tem dúvidas? Verifique as informações do [Office 365 + RemoteApp](remoteapp-o365.md) . Verifique também o artigo novo, [como usar sua assinatura do Office 365 com o Azure RemoteApp](remoteapp-officesubscription.md).

Observe que você precisará licenciar o Office 365 ProPlus, Visio Pro e Project Pro separadamente - cada tiverem licença própria.

## <a name="microsoft-office-2013-professional-plus-trial-only"></a>Microsoft Office 2013 Professional Plus (somente para avaliação)
Durante o período de avaliação gratuito, você pode testar o serviço com a imagem do Office 2013.

Esta imagem é uma extensão da imagem baunilha e tem os seguintes componentes do Microsoft Office 2013 Professional Plus instalado além os componentes descritos na imagem do Windows Server 2012 R2:


- Acesso
- Excel
- Lync
- OneNote
- SkyDrive Pro (Observe que o agente de sincronização não é suportado para uso com o Azure RemoteApp)
- Outlook
- PowerPoint
- Projeto
- Visio
- Word
- Revisores de texto do Microsoft Office

> [AZURE.IMPORTANT]**Informações legais:** Esta imagem não inclui uma licença do Microsoft Office e *não podem ser usados para produção*. O Office 2013 Professional Plus imagem destina-se ao uso de avaliação apenas. Se você quiser usar os aplicativos do Office no Azure RemoteApp para produção, você precisa usar a imagem do Office 365 ProPlus. Para obter mais detalhes sobre o licenciamento do Office, consulte [usando o Office 365 com o Azure RemoteApp](remoteapp-o365.md)
