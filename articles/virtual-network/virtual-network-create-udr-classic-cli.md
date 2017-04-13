<properties 
   pageTitle="Controlar o roteamento e usar dispositivos virtuais usando a CLI Azure no modelo clássico de implantação | Microsoft Azure"
   description="Saiba como controlar o roteamento em VNets utilizando a CLI Azure no modelo clássico de implantação"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

#<a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Controlar o roteamento e usar dispositivos virtuais (clássico) usando a CLI do Azure

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo discute o modelo de implantação clássico. Você também pode [controlar roteamento e usar dispositivos virtuais no modelo de implantação do Gerenciador de recursos](virtual-network-create-udr-arm-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Os exemplo Azure comandos abaixo esperam um ambiente simples já criado com base no cenário acima. Se você quiser executar os comandos conforme eles são exibidos neste documento, crie o ambiente mostrado em [criar um VNet (clássico) usando a CLI do Azure](virtual-networks-create-vnet-classic-cli.md).

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Criar o UDR para a sub-rede de front-end
Para criar a tabela de rota e a rota necessária para a sub-rede de front-end baseada no cenário acima, siga as etapas abaixo.

1. Executar o **`azure config mode`** para alternar para modo clássico.

        azure config mode asm

    Saída:

        info:    New mode is asm

3. Executar o **`azure network route-table create`** comando para criar uma tabela de rota para a sub-rede de front-end.

        azure network route-table create -n UDR-FrontEnd -l uswest

    Saída:

        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK

    Parâmetros:
    - **-l (ou - local)**. Azure região onde o novo NSG será criado. Para o nosso cenário, *westus*.
    - **-n (ou - nome)**. Nome para o novo NSG. Para o nosso cenário, *NSG-FrontEnd*.

4. Executar o **`azure network route-table route set`** comando para criar uma rota na tabela de rota criada acima para enviar todo o tráfego destinado à sub-rede back-end (192.168.2.0/24) para o **FW1** máquina virtual (192.168.0.4).

        azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4

    Saída:

        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK

    Parâmetros:
    - **-r (ou - nome da tabela de rota)**. Nome da tabela de rota onde a rota será adicionada. Para o nosso cenário, *UDR-FrontEnd*.
    - **-um (ou - prefixo de endereço)**. Prefixo de endereço para a sub-rede onde os pacotes estão destinados a. Para o nosso cenário, *192.168.2.0/24*.
    - **-t (ou --tipo de salto Avançar)**. Tipo de objeto tráfego será enviado para. Valores possíveis são *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*ou *Nenhum*.
    - **-p (ou --salto Avançar-endereço ip**). Endereço IP para o próximo salto. Para o nosso cenário, *192.168.0.4*.

5. Executar o **`azure network vnet subnet route-table add`** comando associar a tabela de rota criada acima com a sub-rede **FrontEnd** .

        azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd

    Saída:

        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK 

    Parâmetros:
    - **-t (ou – vnet-nome)**. Nome da VNet onde se encontra a sub-rede. Para o nosso cenário, *TestVNet*.
    - **- n (ou --nome de sub-rede**. Nome da tabela a rota será adicionada à sub-rede. Para o nosso cenário, *FrontEnd*.
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Criar o UDR para a sub-rede de back-end
Para criar a tabela de rota e a rota necessária para a sub-rede de back-end baseada no cenário acima, siga as etapas abaixo.

3. Executar o **`azure network route-table create`** comando para criar uma tabela de rota para a sub-rede de back-end.

        azure network route-table create -n UDR-BackEnd -l uswest

4. Executar o **`azure network route-table route set`** comando para criar uma rota na tabela de rota criada acima para enviar todo o tráfego destinado à sub-rede front-end (192.168.1.0/24) para o **FW1** máquina virtual (192.168.0.4).

        azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4

5. Executar o **`azure network vnet subnet route-table add`** comando associar a tabela de rota criada acima com a sub-rede de **back-end** .

        azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd

