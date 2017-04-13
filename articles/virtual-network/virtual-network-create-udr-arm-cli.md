<properties 
   pageTitle="Controlar o roteamento e usar dispositivos virtuais no Gerenciador de recursos utilizando a CLI Azure | Microsoft Azure"
   description="Saiba como controlar o roteamento e usar dispositivos virtuais usando a CLI do Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

#<a name="create-user-defined-routes-udr-in-the-azure-cli"></a>Criar rotas (UDR) definidas pelo usuário no Azure CLI

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo discute o modelo de implantação do Gerenciador de recursos. Você também pode [criar UDRs no modelo clássico de implantação](virtual-network-create-udr-classic-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Os exemplo Azure comandos abaixo esperam um ambiente simples já criado com base no cenário acima. Se você quiser executar os comandos conforme eles são exibidos neste documento, primeiro criar o ambiente de teste implantando [Este modelo](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), clique em **implantar no Azure**, substitua os valores de parâmetro padrão, se necessário e siga as instruções no portal.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Criar o UDR para a sub-rede de front-end
Para criar a tabela de rota e a rota necessária para a sub-rede de front-end baseada no cenário acima, siga as etapas abaixo.

3. Executar o **`azure network route-table create`** comando para criar uma tabela de rota para a sub-rede de front-end.

        azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest

    Saída:

        info:    Executing command network route-table create
        info:    Looking up route table "UDR-FrontEnd"
        info:    Creating route table "UDR-FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    Name                            : UDR-FrontEnd
        data:    Type                            : Microsoft.Network/routeTables
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        info:    network route-table create command OK

    Parâmetros:
    - **-g (ou - grupo de recursos)**. Nome do grupo de recursos onde o UDR será criado. Para o nosso cenário, *TestRG*.
    - **-l (ou - local)**. Azure região onde o novo UDR será criado. Para o nosso cenário, *westus*.
    - **-n (ou - nome)**. Nome para o novo UDR. Para o nosso cenário, *UDR-FrontEnd*.

4. Executar o **`azure network route-table route create`** comando para criar uma rota na tabela de rota criada acima para enviar todo o tráfego destinado à sub-rede back-end (192.168.2.0/24) para o **FW1** máquina virtual (192.168.0.4).

        azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4

    Saída:

        info:    Executing command network route-table route create
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd/routes/RouteToBackEnd
        data:    Name                            : RouteToBackEnd
        data:    Provisioning state              : Succeeded
        data:    Next hop type                   : VirtualAppliance
        data:    Next hop IP address             : 192.168.0.4
        data:    Address prefix                  : 192.168.2.0/24
        info:    network route-table route create command OK

    Parâmetros:
    - **-r (ou - nome da tabela de rota)**. Nome da tabela de rota onde a rota será adicionada. Para o nosso cenário, *UDR-FrontEnd*.
    - **-um (ou - prefixo de endereço)**. Prefixo de endereço para a sub-rede onde os pacotes estão destinados a. Para o nosso cenário, *192.168.2.0/24*.
    - **-y (ou --tipo de salto Avançar)**. Tipo de objeto tráfego será enviado para. Valores possíveis são *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*ou *Nenhum*.
    - **-p (ou --salto Avançar-endereço ip**). Endereço IP para o próximo salto. Para o nosso cenário, *192.168.0.4*.

5. Executar o **`azure network vnet subnet set`** comando associar a tabela de rota criada acima com a sub-rede **FrontEnd** .

        azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd

    Saída:

        info:    Executing command network vnet subnet set
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        info:    Setting subnet "FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    Route Table                     : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    IP configurations:
        data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
        igurations/ipconfig1
        data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
        igurations/ipconfig1
        data:    
        info:    network vnet subnet set command OK

    Parâmetros:
    - **-e (ou – vnet-nome)**. Nome da VNet onde se encontra a sub-rede. Para o nosso cenário, *TestVNet*.
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Criar o UDR para a sub-rede de back-end
Para criar a tabela de rota e a rota necessária para a sub-rede de back-end baseada no cenário acima, siga as etapas abaixo.

1. Executar o **`azure network route-table create`** comando para criar uma tabela de rota para a sub-rede de back-end.

        azure network route-table create -g TestRG -n UDR-BackEnd -l westus

4. Executar o **`azure network route-table route create`** comando para criar uma rota na tabela de rota criada acima para enviar todo o tráfego destinado à sub-rede front-end (192.168.1.0/24) para o **FW1** máquina virtual (192.168.0.4).

        azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4

5. Executar o **`azure network vnet subnet set`** comando associar a tabela de rota criada acima com a sub-rede de **back-end** .

        azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd

## <a name="enable-ip-forwarding-on-fw1"></a>Habilitar o encaminhamento de IP na FW1
Para habilitar o encaminhamento na NIC usado pelo **FW1**IP, siga as etapas abaixo.

1. Executar o **`azure network nic show`** comando e observou o valor de **encaminhamento de IP habilitar**. Ele deve ser definido como *false*.

        azure network nic show -g TestRG -n NICFW1

    Saída:
        
        info:    Executing command network nic show
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : false
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic show command OK

2. Executar o **`azure network nic set`** comando para ativar encaminhamento IP.

        azure network nic set -g TestRG -n NICFW1 -f true

    Saída:

        info:    Executing command network nic set
        info:    Looking up the network interface "NICFW1"
        info:    Updating network interface "NICFW1"
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : true
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic set command OK

    Parâmetros:

    - **-f (ou - ativar encaminhamento ip)**. *True* ou *false*.
