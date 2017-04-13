<properties
   pageTitle="Verifique se uma conexão de gateway | Microsoft Azure"
   description="Este artigo mostra como verificar uma conexão de gateway no modelo de implantação do Gerenciador de recursos"
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
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="verify-a-gateway-connection"></a>Verifique se uma conexão de gateway

Você pode verificar sua conexão de gateway de algumas maneiras diferentes. Este artigo mostrará como verificar o status de uma conexão de gateway do Gerenciador de recursos usando o portal do Azure e usando o PowerShell.


## <a name="verify-using-powershell"></a>Verifique usando o PowerShell

Você precisará instalar a versão mais recente do cmdlets do PowerShell do Gerenciador de recursos do Azure. Para obter informações sobre como instalar cmdlets do PowerShell, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Para obter mais informações sobre como usar o Gerenciador de recursos de cmdlets, consulte [Usando o Windows PowerShell com o Gerenciador de recursos](../powershell-azure-resource-manager.md).

### <a name="step-1-log-in-to-your-azure-account"></a>Etapa 1: Faça logon em sua conta do Azure

1. Abra seu console do PowerShell com privilégios elevados e conectar à sua conta.

        Login-AzureRmAccount

2. Verifique as assinaturas para a conta.

        Get-AzureRmSubscription 

3. Especifica a assinatura que você deseja usar.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="step-2-verify-your-connection"></a>Etapa 2: Confirmar sua conexão


[AZURE.INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)] 


## <a name="verify-using-the-azure-portal"></a>Verifique usando o portal do Azure

[AZURE.INCLUDE [verify connection portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)] 


## <a name="next-steps"></a>Próximas etapas

- Você pode adicionar máquinas virtuais às suas redes virtuais. Consulte [criar uma máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter as etapas.

