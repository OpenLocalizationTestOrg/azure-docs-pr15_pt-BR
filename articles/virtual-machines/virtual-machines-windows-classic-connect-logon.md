<properties
    pageTitle="Faça logon em uma VM clássico do Windows Azure | Microsoft Azure"
    description="Use o portal clássico do Windows Azure para fazer logon uma máquina virtual do Windows criada com o modelo de implantação clássico."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="cynthn"/>


# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-classic-portal"></a>Faça logon uma máquina virtual do Windows usando o portal de clássico do Windows Azure

No portal de clássico do Windows Azure, você pode usar o botão **Conectar** para iniciar uma sessão de área de trabalho remota e faça logon em uma VM do Windows.

Você deseja se conectar a uma VM Linux? Consulte [como fazer logon em uma máquina virtual executando Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Saiba como [executar essas etapas usando o novo portal Azure](virtual-machines-windows-connect-logon.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 

## <a name="video-walkthrough"></a>Vídeo passo a passo

Aqui está um passo a passo vídeo das etapas neste tutorial. Ele também aborda os pontos de extremidade e públicas e particulares portas usadas para conectar-se a uma VM do Windows no Windows Azure.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]


## <a name="connect-to-the-virtual-machine"></a>Conecte-se para a máquina virtual

1. Entrar no portal de clássico do Windows Azure.

2. Clique em **máquinas virtuais**e, em seguida, selecione a máquina virtual.

3. Na barra de comandos na parte inferior da página, clique em **Conectar**.

    ![Faça logon na máquina virtual](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)
    
> [AZURE.TIP] Se o botão **Conectar** não estiver disponível, consulte as dicas de solução de problemas no final deste artigo.

## <a name="log-on-to-the-virtual-machine"></a>Faça logon na máquina virtual

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Próximas etapas

-   Se o botão **Conectar** está inativo ou se você tiver outros problemas com a conexão de área de trabalho remota, tente redefinir a configuração. No painel máquina virtual, em **Relance rápida**, clique em **Redefinir a configuração remota**.
-   Há problemas com a sua senha, tente redefini-lo. No painel máquina virtual, em **Um relance rápida**, clique em **Redefinir senha**.

Se essas dicas não funcionam ou não são o que você precisa, consulte [Solucionar problemas de área de trabalho remota conexões para uma baseada no Windows Azure Máquina Virtual](virtual-machines-windows-troubleshoot-rdp-connection.md). Este artigo o orienta através de diagnosticar e resolver problemas comuns.


