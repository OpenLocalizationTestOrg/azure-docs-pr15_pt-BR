<properties
   pageTitle="Atualize seu conjunto de RemoteApp Azure | Microsoft Azure"
   description="Saiba como atualizar seu conjunto de RemoteApp do Azure"
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="update-a-collection-in-azure-remoteapp"></a>Atualizar um conjunto no RemoteApp do Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Ficará vez, inevitavelmente, quando você precisa atualizar o aplicativos ou a imagem em seu conjunto de RemoteApp do Azure. Se você estiver usando uma das imagens incluídas com sua assinatura do Azure RemoteApp, conjunto de uma nuvem ou híbrido, todos e quaisquer atualizações são tratadas pelo RemoteApp Azure em si, para que você possa ter fácil.

No entanto, se você estiver usando uma imagem personalizada (que você criou do zero ou que você criou modificando uma das nossas imagens), for responsável pelo mantendo a imagem e aplicativos. Se você precisa atualizar sua imagem ou qualquer um dos aplicativos dentro dela, você precisa criar uma nova versão atualizada da imagem e, em seguida, substitua a imagem existente no seu conjunto com a nova imagem atualizada.

Portanto, como você faz sobre como atualizar seu conjunto? É bem simples:

1. Atualize a imagem que você usou no seu conjunto. Aplicar quaisquer patches ou atualizações necessárias e, em seguida, salve-o com um novo nome.
2. [Carregar](remoteapp-uploadimage.md) ou [Importar](remoteapp-image-on-azurevm.md) que imagem para RemoteApp.
3. Agora, na página conjunto, clique em **Atualizar**.
4. Escolha a nova imagem na lista de **Imagem de modelo** .
4. Aqui está a parte complicada - você precisa decidir como lidar com todos os usuários que atualmente usa um aplicativo na coleção. Você tem as seguintes opções:
    - **Conceder aos usuários 60 minutos após a atualização**. Assim que a atualização estiver concluída, o Azure RemoteApp exibirá uma mensagem para os usuários ativos informando-os para salvar seu trabalho e sair e entrar novamente. Após 60 minutos, quaisquer usuários ativos que não fizeram desativar serão automaticamente desconectados. Os usuários podem imediatamente logon novamente.
    - **Assinar usuários imediatamente**. Assim que a atualização estiver concluída, fazer logoff todos os usuários automaticamente sem qualquer aviso. Se você escolher essa opção, os usuários podem perder dados. No entanto, eles podem se reconectar ao aplicativo imediatamente.

1. Clique na marca de seleção para iniciar a atualização.
