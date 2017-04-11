<properties
   pageTitle="Configurar um gateway VNet para rota expressa usando o PowerShell | Microsoft Azure"
   description="Configurar um gateway VNet para uma implantação clássico VNet usando o PowerShell para uma configuração de rota expressa de modelo."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/03/2016"
   ms.author="charwen"/>

# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-classic-deployment-model-and-powershell"></a>Configurar um gateway de rede virtual para rota expressa usando o modelo clássico de implantação e PowerShell

> [AZURE.SELECTOR]
- [PowerShell - Gerenciador de recursos](expressroute-howto-add-gateway-resource-manager.md)
- [PowerShell - clássico](expressroute-howto-add-gateway-classic.md)

Este artigo o orientará pelas etapas para adicionar, redimensionar e remover um gateway de rede virtual (VNet) para um VNet preexistente. As etapas para essa configuração são especificamente para VNets que foram criados usando o **modelo clássico de implantação** e que será ser usado em uma configuração de rota expressa. 

**Sobre os modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="before-beginning"></a>Antes de começar

Verifique se você instalou cmdlets do PowerShell do Azure necessário para esta configuração (1.0.2 ou posterior). Se você ainda não instalou os cmdlets, você precisará fazer isso antes de iniciar as etapas de configuração. Para obter mais informações sobre como instalar o PowerShell do Azure, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

    
## <a name="next-steps"></a>Próximas etapas

Após ter criado gateway VNet, você pode vincular sua VNet a um circuito de rota expressa. Consulte [uma rede Virtual a um circuito de rota expressa do Link](expressroute-howto-linkvnet-classic.md).
