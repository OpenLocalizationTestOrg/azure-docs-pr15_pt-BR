
<properties
    pageTitle="Lista de portas e URLs à branca para Azure RemoteApp implantado na rede virtual do cliente | Microsoft Azure"
    description="Saiba quais portas e URLs que você precisará configurar para comunicação por meio do Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="elizapo" />



# <a name="list-of-ports-and-urls-to-permit-access-for-azure-remoteapp-deployed-in-customer-virtual-network"></a>Lista de portas e URLs para permitir o acesso do Azure RemoteApp implantado no cliente Rede Virtual 

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Este procedimento se aplica ao Azure RemoteApp uma coleção de nuvem ou híbrido se você estiver implantando-lo em uma rede virtual (VNET). Para obter mais informações sobre redes virtuais, leia [Visão geral de rede Virtual](../virtual-network/virtual-networks-overview.md). Se você tiver criado um grupo de segurança de rede (NSG) como restringir o tráfego para os recursos da sua rede virtual que você escolheu para Azure RemoteApp, verifique se que a seguir estão acessíveis e permitido por meio de diretivas de segurança em uma rede virtual. Para obter mais informações sobre grupos de segurança de rede, leia [o que é um grupo de segurança de rede? (NSG)](../virtual-network/virtual-networks-nsg.md).

##  <a name="azure-remoteapp-subnet-needs-access-to-these-endpoints-and-urls"></a>Azure RemoteApp sub-rede precisa de acesso a esses pontos de extremidade e URLs: 
*   *. servicebus.windows.net
*    *. servicebus.net
*    https://*.RemoteApp.windwsazure.com  
*    https://www.RemoteApp.WindowsAzure.com 
*    https://*RemoteApp.WindowsAzure.com  
*    https://*.Core.Windows.NET  
*    Saída: TCP: 443, TCP: 10101 10175 
*    Opcional – UDP: 10201 10275  
 
## <a name="azure-remoteapp-clients-need-access-to-these-endpoints-and-urls"></a>Os clientes de RemoteApp Azure precisam ter acesso a esses pontos de extremidade e URLs: 

Por clientes posso dizer as áreas de trabalho, dispositivos etc que as pessoas usam para se conectar aos aplicativos implantados no conjunto de RemoteApp do Azure.

-  https://telemetry.RemoteApp.WindowsAzure.com  
-  https://*.RemoteApp.WindowsAzure.com (portas UDP opcionais são para este endereço) 
-  https://login.Windows.NET  
-  https://login.microsoftonline.com  
-  https://www.RemoteApp.WindowsAzure.com 
-  https://*.Core.Windows.NET  
-  Saída: TCP: 443  
-  Opcional - UDP: 3391 