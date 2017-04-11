
<properties
    pageTitle="Informações para um VNET no Azure RemoteApp de dimensionamento | Microsoft Azure"
    description="Saiba mais sobre os requisitos de endereço IP para o Azure RemoteApp executando com uma VNET"
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



# <a name="sizing-information-for-a-vnet-in-azure-remoteapp"></a>Informações de dimensionamento para um VNET no RemoteApp do Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Quando você usa o Azure RemoteApp com uma rede virtual (VNET), o RemoteApp utiliza endereços IP dentro da sub-rede. Com base na escala de seu serviço de RemoteApp, é necessário garantir que sua sub-rede tenha endereços IP suficientes para máquinas virtuais de RemoteApp. Enquanto neste guia dimensionamento não é perfeito fornecidas como RemoteApp dinamicamente gira e gira pressionada máquinas virtuais dentro de uma coleção, ele ajudará você estimar o intervalo de sub-rede. Isso é especialmente importante como, depois de um serviço de RemoteApp é colocado em uma VNET, você não pode aumentar o tamanho de sub-rede sem remover RemoteApp.

Para cada conjunto de RemoteApp que você deseja executar em capacidade máxima, você deve ter 100 endereços IP disponíveis. Por exemplo, se você tiver uma coleção de RemoteApp no plano do padrão e você deseja ter o máximo de 500 usuários, você deve ter 100 endereços IP para esse conjunto. Da mesma forma, você precisa 100 endereços IP para um conjunto de RemoteApp no plano do básico com 800 usuários. Se você planeja ter menos usuários (menor do que o máximo), você pode reduzir os endereços IP necessários por conjunto. O requisito de tamanho mínimo sub-rede é 30 endereços IP (/ 27).

Confira as informações a seguir para garantir que seu VNET está configurado e funcionando propertly:

- [Migrar de um VNET pessoal para um VNET do Azure](remoteapp-migratevnet.md)
- [Validar a VNET do Azure para usar com o Azure RemoteApp](remoteapp-vnet.md)
