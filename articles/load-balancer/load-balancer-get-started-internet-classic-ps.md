<properties
   pageTitle="Começar a criar um Internet opostas balanceador de carga no modo clássico usando o PowerShell | Microsoft Azure"
   description="Aprenda a criar uma Internet opostas balanceador de carga no modo clássico usando o PowerShell"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Começar a criar um Internet opostas balanceador de carga (clássico) no PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo discute o modelo de implantação clássico. Você também pode [aprender a criar uma Internet opostas balanceador de carga usando o Gerenciador de recursos do Azure](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]



## <a name="set-up-load-balancer-using-powershell"></a>Configurar o balanceador de carga usando o PowerShell

Para configurar um balanceador de carga usando o powershell, execute as etapas abaixo:

1. Se você nunca usou o PowerShell do Azure, veja [como instalar e configurar o Azure PowerShell](../../articles/powershell-install-configure.md) e siga as instruções totalmente ao final para entrar no Azure e selecione sua assinatura.


2. Depois de criar uma máquina virtual, você pode usar cmdlets do PowerShell para adicionar um balanceador de carga para uma máquina virtual dentro do mesmo serviço de nuvem.

No exemplo a seguir, você adicionará que um balanceador de carga definir chamado "webfarm" para nuvem serviço "mytestcloud" (ou myctestcloud.cloudapp.net), adicionando os pontos de extremidade para o balanceador de carga para máquinas virtuais denominada "web1" e "web2". O balanceador de carga recebe tráfego de rede na porta 80 e balanceamento de carga entre as máquinas virtuais definidas pelo ponto de extremidade local (neste caso a porta 80) usando TCP.


### <a name="step-1"></a>Etapa 1
Criar um ponto de extremidade de balanceamento de carga para a primeira máquina virtual "web1"

    Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

### <a name="step-2"></a>Etapa 2

Criar outro ponto de extremidade para a segunda máquina virtual "web2" usando o mesmo nome de conjunto de Balanceador de carga

    Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Remover uma máquina virtual de um balanceador de carga

Você pode usar AzureEndpoint remover para remover um ponto de extremidade de máquina virtual do balanceador de carga

    Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM

## <a name="next-steps"></a>Próximas etapas

Você também pode [começar a criar um balanceador de carga interno](load-balancer-get-started-ilb-classic-ps.md) e configurar o tipo de [modo de distribuição](load-balancer-distribution-mode.md) para um comportamento de tráfego de rede do especific carga balanceador.

Se seu aplicativo precisar manter conexões ativa para servidores atrás de um balanceador de carga, você pode compreender mais sobre [as configurações de tempo limite ociosas TCP para um balanceador de carga](load-balancer-tcp-idle-timeout.md). Ela ajuda para saber mais sobre o comportamento de conexão ocioso quando você estiver usando o balanceador de carga do Azure.

