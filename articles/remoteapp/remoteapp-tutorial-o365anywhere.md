<properties
   pageTitle="Obter a mesma experiência do Office 365 em qualquer dispositivo com o Azure RemoteApp | Microsoft Azure"
   description="Aprenda a compartilhar qualquer aplicativo do Office 365 com seus usuários usando RemoteApp do Azure."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="guscatal;elizapo"/>


# <a name="get-the-same-office-365-experience-on-any-device-with-azure-remoteapp"></a>Obter a mesma experiência do Office 365 em qualquer dispositivo com RemoteApp do Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Este artigo abordará como implantar o Office 365 em qualquer dispositivo em sua empresa. Os usuários possam obter os mesmos recursos e a experiência UI em Android, Apple e Windows.

Faremos isso usando o Azure RemoteApp hospedando o Office 365 em máquinas virtuais capaz de escala no Azure que os usuários podem se conectar a. Este conjunto de máquinas virtuais chamamos uma coleção"nuvem".

## <a name="create-a-cloud-collection"></a>Criar um conjunto de nuvem

Primeiro após ter criado uma conta do Azure, navegue até **RemoteApp** clicando no link no lado esquerdo.
![Mostrando RemoteApp Azure no Portal do Azure](./media/remoteapp-tutorial-o365anywhere/1-menu.png)

Continue clicando em **novo** na parte inferior e "Criando rápido" um conjunto. Forneça um nome, a região, a assinatura, o plano e a imagem "Proffesional do Office 2013" que fornecemos.
![Caixa de diálogo Criar](./media/remoteapp-tutorial-o365anywhere/2-quickcreate.png)

Após concluir o formulário que deve começar o processo de criação do conjunto. Isso pode levar uma hora ou mais.

![Aguardando](./media/remoteapp-tutorial-o365anywhere/3-waiting.png)

Depois que o processo for concluído, você verá algo assim. Se clicamos **publicação** podemos ver que a maioria dos aplicativos do Office foram publicadas para nós já.
![Coleção criada](./media/remoteapp-tutorial-o365anywhere/4-done.png)

![Aplicativos publicados](./media/remoteapp-tutorial-o365anywhere/5-publish.png)

Neste ponto você também pode adicionar mais usuários que têm acesso a esse conjunto, clicando em **Acesso de usuário**.
![Configurar o acesso de usuário](./media/remoteapp-tutorial-o365anywhere/6-user.png)

Agora vamos experimentar conectando ao Office 365!

## <a name="connect-to-office-365"></a>Conectar ao Office 365

Vamos vá direto para [https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/), role para baixo e clique em **Baixar clientes** para instalar o cliente do Azure RemoteApp do dispositivo que você está em. As capturas de tela abaixo são para Windows.

Depois que o aplicativo é iniciado você será solicitado a entrar com sua conta da Microsoft (anteriormente chamada de um "Live ID"), use o mesmo como sua conta do Azure para agora. Quando você está conectado na você deve ver uma notificação sobre convites de novo, clique em lá e você verá uma lista como um abaixo. Aceite o convite que corresponde ao seu email de proprietário de conta do Microsoft Azure.

![Novo convite](./media/remoteapp-tutorial-o365anywhere/7-araclient.png)

Aparência quando há convites de novo.

![Aceitar um aplicativo](./media/remoteapp-tutorial-o365anywhere/8-invitation.png)

Depois que você aceita o convite, você deve ver todos os aplicativos do Office no cliente do Azure RemoteApp.

![Lista de aplicativos](./media/remoteapp-tutorial-o365anywhere/9-work.png)

Quando você clica em qualquer um desses que deve iniciar o aplicativo na máquina virtual Azure e você deve ser pronto! Aproveite!

![Iniciando](./media/remoteapp-tutorial-o365anywhere/10-arastart.png)

![PowerPoint](./media/remoteapp-tutorial-o365anywhere/11-pp.png)
