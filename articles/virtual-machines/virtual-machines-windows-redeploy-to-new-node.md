<properties 
    pageTitle="Reimplantar máquinas virtuais do Windows | Microsoft Azure" 
    description="Descreve como reimplantar máquinas virtuais do Windows para atenuar problemas de conexão de RDP." 
    services="virtual-machines-windows" 
    documentationCenter="virtual-machines" 
    authors="iainfoulds" 
    manager="timlt"
    tags="azure-resource-manager,top-support-issue" 
/>
    

<tags 
    ms.service="virtual-machines-windows" 
    ms.devlang="na" 
    ms.topic="support-article" 
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure" 
    ms.date="09/19/2016" 
    ms.author="iainfou" 
/>


# <a name="redeploy-virtual-machine-to-new-azure-node"></a>Reimplantar máquina virtual para o novo nó Azure

Se você tiver sido enfrentando dificuldades para solução de problemas da área de trabalho remota (RDP) conexão ou aplicativo acesso ao baseado no Windows Azure virtuais de máquina, reimplantar a máquina virtual pode ajudar. Quando você reimplanta uma máquina virtual, ele move a máquina virtual para um novo nó dentro de infraestrutura do Azure e, em seguida, liga-lo novamente, reter todas as suas opções de configuração e recursos associados. Este artigo mostra como reimplantar uma máquina virtual usando o PowerShell do Azure ou o portal do Azure.

> [AZURE.NOTE] Depois de você reimplanta uma máquina virtual, o disco temporário for perdido e endereços IP dinâmicos associados a interface de rede virtual são atualizados. 

## <a name="using-azure-powershell"></a>Usando o PowerShell Azure

Verifique se você tem o PowerShell mais recente do Azure 1. x instalado no computador. Para obter mais informações, consulte [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

Use este comando do PowerShell do Azure reimplantar sua máquina virtual:

    Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## <a name="next-steps"></a>Próximas etapas
Se você estiver tendo problemas de conexão com sua máquina virtual, você pode encontrar ajuda específica sobre [conexões RDP de solução de problemas](virtual-machines-windows-troubleshoot-rdp-connection.md) ou [RDP detalhada etapas de solução de problemas](virtual-machines-windows-detailed-troubleshoot-rdp.md). Se você não consegue acessar um aplicativo executado em sua máquina virtual, você também pode ler [Solucionando problemas de aplicativo](virtual-machines-windows-troubleshoot-app-connection.md).