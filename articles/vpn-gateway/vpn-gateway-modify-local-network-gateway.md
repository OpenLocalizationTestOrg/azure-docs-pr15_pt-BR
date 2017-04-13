<properties
   pageTitle="Modificar prefixos de endereço IP de gateway de rede local e gateway IP | Microsoft Azure"
   description="Este artigo conduz você por alterando prefixos de endereço IP do seu gateway de rede local"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="cherylmc"/>

# <a name="modify-local-network-gateway-settings-using-powershell"></a>Modificar as configurações de gateway de rede local usando o PowerShell

Às vezes, altere as configurações do seu gateway de rede local AddressPrefix ou GatewayIPAddress. As instruções a seguir o ajudarão a modificar as configurações de gateway de rede local. Você também pode modificar essas configurações no portal do Azure.

## <a name="before-you-begin"></a>Antes de começar
    
Você precisará instalar a versão mais recente do cmdlets do PowerShell do Gerenciador de recursos do Azure. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.

## <a name="to-modify-ip-address-prefixes"></a>Para modificar prefixos de endereço IP

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address"></a>Para modificar o endereço IP do gateway

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Próximas etapas

Você pode verificar sua conexão de gateway. Consulte [Verificar uma conexão de gateway](vpn-gateway-verify-connection-resource-manager.md).

