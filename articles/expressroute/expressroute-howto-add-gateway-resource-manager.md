<properties
   pageTitle="Adicionando um gateway VNet para uma rede virtual para rota expressa usando o Gerenciador de recursos e PowerShell | Microsoft Azure"
   description="Este artigo explica a adição de um gateway Vnet para um VNet Gerenciador de recursos já criadas para rota expressa"
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/10/2016"
   ms.author="charwen"/>

# <a name="configure-a-virtual-network-gateway-for-expressroute-using-resource-manager-and-powershell"></a>Configurar um gateway de rede virtual para rota expressa usando o Gerenciador de recursos e PowerShell


> [AZURE.SELECTOR]
- [PowerShell - Gerenciador de recursos](expressroute-howto-add-gateway-resource-manager.md)
- [PowerShell - clássico](expressroute-howto-add-gateway-classic.md)


Este artigo o orientará pelas etapas para adicionar, redimensionar e remover um gateway de rede virtual (VNet) para um VNet preexistente. As etapas para essa configuração são especificamente para VNets que foram criados usando o **modelo de implantação do Gerenciador de recursos** e que será ser usado em uma configuração de rota expressa. 

**Sobre os modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="before-beginning"></a>Antes de começar

Verifique se você instalou cmdlets do PowerShell do Azure necessário para esta configuração (1.0.2 ou posterior). Se você ainda não instalou os cmdlets, você precisará fazer isso antes de iniciar as etapas de configuração. Para obter mais informações sobre como instalar o PowerShell do Azure, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

    
## <a name="next-steps"></a>Próximas etapas

Após ter criado gateway VNet, você pode vincular sua VNet a um circuito de rota expressa. Consulte [uma rede Virtual a um circuito de rota expressa do Link](expressroute-howto-linkvnet-arm.md).
