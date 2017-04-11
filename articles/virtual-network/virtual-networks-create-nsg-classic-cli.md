<properties
   pageTitle="Como criar NSGs no modo clássico utilizando a CLI Azure | Microsoft Azure"
   description="Aprenda a criar e implantar NSGs no modo clássico utilizando a CLI do Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
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

# <a name="how-to-create-nsgs-classic-in-the-azure-cli"></a>Como criar NSGs (clássico) na CLI do Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo discute o modelo de implantação clássico. Você também pode [criar NSGs no modelo de implantação do Gerenciador de recursos](virtual-networks-create-nsg-arm-cli.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Os exemplo do Azure comandos abaixo esperam um ambiente simples já criado com base no cenário acima. Se você quiser executar os comandos conforme eles são exibidos neste documento, primeiro crie o ambiente de teste, [Criando um VNet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-create-the-nsg-for-the-front-end-subnet"></a>Como criar o NSG para a sub-rede de front-end
Para criar um NSG chamado nomeado **NSG FrontEnd** baseado no cenário acima, siga as etapas abaixo.

1. Se você nunca usou CLI do Azure, consulte [instalar e configurar o CLI do Azure](../xplat-cli-install.md) e siga as instruções para cima até o ponto onde você selecionar sua conta do Azure e assinatura.

2. Executar o **`azure config mode`** comando para alternar para modo clássico, conforme mostrado abaixo.

        azure config mode asm

    Saída esperada:

        info:    New mode is asm

3. Executar o **`azure network nsg create`** comando para criar um NSG.

        azure network nsg create -l uswest -n NSG-FrontEnd

    Saída esperada:

        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-FrontEnd"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : NSG-FrontEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK

    Parâmetros:

    - **-l (ou - local)**. Azure região onde o novo NSG será criado. Para o nosso cenário, *westus*.
    - **-n (ou - nome)**. Nome para o novo NSG. Para o nosso cenário, *NSG-FrontEnd*.

4. Executar o **`azure network nsg rule create`** comando para criar uma regra que permite o acesso à porta 3389 (RDP) da Internet.

        azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389

    Saída esperada:

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "rdp-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : rdp-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 3389
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK

    Parâmetros:

    - **-um (ou – nsg-nome)**. Nome da NSG no qual a regra será criada. Para o nosso cenário, *NSG-FrontEnd*.
    - **-n (ou - nome)**. Nome para a nova regra. Para o nosso cenário, a *regra de rdp*.
    - **-c (ou - ação)**. Nível de acesso para a regra (negar ou permitir).
    - **-p (ou - protocolo)**. Protocolo (Tcp, Udp, ou *) para a regra.
    - **-r (ou - tipo)**. Direção da conexão (entrada ou saída).
    - **-y (ou - prioridade)**. Prioridade para a regra.
    - **-f (ou - prefixo de endereço de origem)**. Prefixo de endereço de origem em CIDR ou usando marcas de formatação padrão.
    - **-o (ou - intervalo de porta de origem)**. Porta de origem, ou intervalo.
    - **-e (ou - prefixo de endereço de destino)**. Prefixo de endereço de destino em CIDR ou usando marcas de formatação padrão.
    - **-u (ou - intervalo de porta de destino)**. Porta de destino, ou intervalo.

5. Executar o **`azure network nsg rule create`** comando para criar uma regra que permite o acesso à porta 80 (HTTP) da Internet.

        azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80

    Putput esperado:

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 200
        info:    network nsg rule create command OK

6. Executar o **`azure network nsg subnet add`** comando vincular a NSG à sub-rede front-end.

        azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd

    Saída esperada:

        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-FrontEnd"
        info:    network nsg subnet add command OK

## <a name="how-to-create-the-nsg-for-the-back-end-subnet"></a>Como criar o NSG para a sub-rede de back-end
Para criar um NSG chamado nomeado *NSG-back-end* baseado no cenário acima, siga as etapas abaixo.

3. Executar o **`azure network nsg create`** comando para criar um NSG.

        azure network nsg create -l uswest -n NSG-BackEnd

    Saída esperada:

        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-BackEnd"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : NSG-BackEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK

    Parâmetros:

    - **-l (ou - local)**. Azure região onde o novo NSG será criado. Para o nosso cenário, *westus*.
    - **-n (ou - nome)**. Nome para o novo NSG. Para o nosso cenário, *NSG-FrontEnd*.

4. Executar o **`azure network nsg rule create`** comando para criar uma regra que permite o acesso à porta 1433 (SQL) da sub-rede front-end.

        azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433

    Saída esperada:

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "sql-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : sql-rule
        data:    Source address prefix           : 192.168.1.0/24
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 1433
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK


5. Executar o **`azure network nsg rule create`** comando para criar uma regra que nega acesso à Internet.

        azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80

    Putput esperado:

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : *
        data:    Source Port                     : *
        data:    Destination address prefix      : INTERNET
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Outbound
        data:    Action                          : Deny
        data:    Priority                        : 200
        info:    network nsg rule create command OK

6. Executar o **`azure network nsg subnet add`** comando vincular a NSG à sub-rede back-end.

        azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd

    Saída esperada:

        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-BackEndX"
        info:    Looking up the subnet "BackEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-BackEndX"
        info:    network nsg subnet add command OK
