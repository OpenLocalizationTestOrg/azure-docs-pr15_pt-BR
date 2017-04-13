<properties 
   pageTitle="Controlar o roteamento e usar dispositivos virtuais usando o PowerShell no modelo clássico de implantação | Microsoft Azure"
   description="Saiba como controlar o roteamento em VNets usando o PowerShell no modelo clássico de implantação"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Controlar o roteamento e usar dispositivos virtuais (clássico) usando o PowerShell

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo discute o modelo de implantação clássico.

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

O exemplo Azure PowerShell comandos abaixo esperam um ambiente simples que já criado baseado no cenário acima. Se você quiser executar os comandos conforme eles são exibidos neste documento, crie o ambiente mostrado em [criar um VNet (clássico) usando o PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Criar o UDR para a sub-rede de front-end
Para criar a tabela de rota e a rota necessária para a sub-rede de front-end baseada no cenário acima, siga as etapas abaixo.

3. Executar o **`New-AzureRouteTable`** cmdlet para criar uma tabela de rota para a sub-rede de front-end.

        New-AzureRouteTable -Name UDR-FrontEnd `
            -Location uswest `
            -Label "Route table for front end subnet"

    Saída:

        Name         Location   Label                          
        ----         --------   -----                          
        UDR-FrontEnd West US    Route table for front end subnet

4. Executar o **`Set-AzureRoute`** cmdlet para criar uma rota na tabela de rota criado acima para enviar todo o tráfego destinado à sub-rede back-end (192.168.2.0/24) para o **FW1** máquina virtual (192.168.0.4).
    
        Get-AzureRouteTable UDR-FrontEnd `
            |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4

    Saída:

        Name     : UDR-FrontEnd
        Location : West US
        Label    : Route table for frontend subnet
        Routes   : 
                   Name                 Address Prefix    Next hop type        Next hop IP address
                   ----                 --------------    -------------        -------------------
                   RouteToBackEnd       192.168.2.0/24    VirtualAppliance     192.168.0.4  

5. Executar o **`Set-AzureSubnetRouteTable`** cmdlet para associar a tabela de rota criado acima com a sub-rede **FrontEnd** .

        Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
            -SubnetName FrontEnd `
            -RouteTableName UDR-FrontEnd
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Criar o UDR para a sub-rede de back-end
Para criar a tabela de rota e a rota necessária para a sub-rede de back-end baseada no cenário acima, siga as etapas abaixo.

3. Executar o **`New-AzureRouteTable`** cmdlet para criar uma tabela de rota para a sub-rede de back-end.

        New-AzureRouteTable -Name UDR-BackEnd `
            -Location uswest `
            -Label "Route table for back end subnet"

4. Executar o **`Set-AzureRoute`** cmdlet para criar uma rota na tabela de rota criado acima para enviar todo o tráfego destinado à sub-rede front-end (192.168.1.0/24) para o **FW1** máquina virtual (192.168.0.4).

        Get-AzureRouteTable UDR-BackEnd `
            |Set-AzureRoute -RouteName RouteToFrontEnd -AddressPrefix 192.168.1.0/24 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4

5. Executar o **`Set-AzureSubnetRouteTable`** cmdlet para associar a tabela de rota criado acima com a sub-rede de **back-end** .

        Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
            -SubnetName BackEnd `
            -RouteTableName UDR-BackEnd

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Habilitar o encaminhamento de IP na máquina virtual FW1
Para habilitar o encaminhamento na VM FW1 IP, siga as etapas abaixo.

1. Executar o **`Get-AzureIPForwarding`** cmdlet para Veri o status de encaminhamento IP

        Get-AzureVM -Name FW1 -ServiceName TestRGFW `
            | Get-AzureIPForwarding

    Saída:

        Disabled

2. Executar o **`Set-AzureIPForwarding`** comando para habilitar o encaminhamento de IP para o *FW1* máquina virtual.

        Get-AzureVM -Name FW1 -ServiceName TestRGFW `
            | Set-AzureIPForwarding -Enable
