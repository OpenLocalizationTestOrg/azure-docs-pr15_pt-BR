<properties
    pageTitle="Publicar um aplicativo no Azure RemoteApp | Microsoft Azure"
    description="Saiba como publicar aplicativos e recursos no Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="how-to-publish-an-app-in-remoteapp"></a>Como publicar um aplicativo no RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Depois de criar seu conjunto de RemoteApp, você precisa publicar os aplicativos ou recursos que você deseja disponibilizar para seus usuários. As imagens de modelo fornecidas com sua assinatura tem só alguns aplicativos publicados por padrão - compartilhar outros aplicativos, você precisa publicá-los.

> [AZURE.NOTE] Você precisa atualizar um aplicativo? Você precisará [atualizar a imagem](remoteapp-update.md) primeiro.

Na guia **publicação** no portal, clique em **Publicar**. Você pode adicionar um aplicativo do menu **Iniciar** da imagem seu modelo ou forneça o caminho para onde o aplicativo está instalado na imagem do modelo. Se você optar por adicionar a partir do menu **Iniciar** , escolha o aplicativo para publicar na lista. Se você optar por fornecer o caminho para o aplicativo, insira um nome para o aplicativo e o caminho para o aplicativo. Usar variáveis no caminho - por exemplo, "% systemdrive %" em vez de "c:\".

> [AZURE.NOTE] Se você quiser adicionar seu aplicativo no menu **Iniciar** , você precisa ter *adicionou esse aplicativo para o * *Iniciar* * menu na sua imagem de modelo.* Caso contrário, RemoteApp só verá que *está* no menu **Iniciar** , e você será confuso. 

>Para garantir que o aplicativo está no menu **Iniciar** , colocar um arquivo de atalho - **. lnk** - dentro da pasta de Menu\Programs %systemdrive%\ProgramData\Microsoft\Windows\Start.

> Se você esqueceu de adicionar o aplicativo ao menu **Iniciar** quando você criou o modelo, escolha Adicionar o caminho para o aplicativo. (Ou recriar sua imagem de modelo, mas isso é trabalho muito mais.)


 