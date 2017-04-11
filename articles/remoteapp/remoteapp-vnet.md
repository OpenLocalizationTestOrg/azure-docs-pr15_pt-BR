
<properties
    pageTitle="Validar a VNET do Azure para usar com o Azure RemoteApp | Microsoft Azure"
    description="Aprenda a certificar-se de que seu VNET Azure está pronto para uso com o Azure RemoteApp"
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



# <a name="validate-the-azure-vnet-to-use-with-azure-remoteapp"></a>Validar a VNET do Azure para usar com o Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Antes de usar um VNET Azure com o Azure RemoteApp, talvez você queira validar o VNET. Isso ajuda a evitar problemas de conectividade.

Para validar seu VNET Azure, faça o seguinte:

1. Crie uma máquina virtual Azure dentro da sub-rede VNET o Azure que você deseja usar com o Azure RemoteApp.

2. Conectar a essa máquina virtual usando a opção de **conectar-se** no portal de gerenciamento.
3. Ingresse na máquina virtual para o mesmo domínio que você deseja usar com o Azure RemoteApp. Se você estiver criando um conjunto de híbrido que se conecta à sua rede local, ingresse na máquina virtual para seu domínio local.

Se esta for bem-sucedido, o VNET Azure está pronto para usar com o RemoteApp.

Para obter mais informações sobre o fluxo de trabalho do conjunto de ponta a ponta híbrida, consulte os seguintes artigos:

- [Como planejar sua rede virtual RemoteApp do Azure](remoteapp-planvnet.md)
- [Criar um conjunto de híbrido](remoteapp-create-hybrid-deployment.md)
- [Implantar o conjunto de RemoteApp Azure à sua rede Virtual do Azure (com suporte para rota expressa)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)
