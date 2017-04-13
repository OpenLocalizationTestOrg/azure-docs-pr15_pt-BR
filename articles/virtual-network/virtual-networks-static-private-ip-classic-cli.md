<properties 
   pageTitle="Como definir um IP particular estático no modo clássico ausing CLI | Microsoft Azure"
   description="Compreendendo IPs estático particular (queda) e como gerenciá-los no modo clássico utilizando a CLI"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-classic-in-azure-cli"></a>Como definir um endereço IP privado estático (clássico) em CLI do Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo discute o modelo de implantação clássico. Você também pode [Gerenciar um endereço IP privado estático no modelo de implantação do Gerenciador de recursos](virtual-networks-static-private-ip-arm-cli.md).

Os exemplo Azure comandos abaixo esperam um ambiente simples que já criado. Se você quiser executar os comandos conforme eles são exibidos neste documento, primeiro crie o ambiente de teste descrito em [criar um vnet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Como especificar um endereço IP privado estático ao criar uma máquina virtual
Para criar uma nova máquina virtual chamada *DNS01* em um novo serviço de nuvem denominado *TestService* baseado no cenário acima, siga estas etapas:

1. Se você nunca usou CLI do Azure, consulte [instalar e configurar o CLI do Azure](../xplat-cli-install.md) e siga as instruções para cima até o ponto onde você selecionar sua conta do Azure e assinatura.
1. Execute o comando **criar de serviço do azure** para criar o serviço de nuvem.

        azure service create TestService --location uscentral

    Saída esperada:

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
    
2. Execute o comando **azure criar máquina virtual** para criar a máquina virtual. Observe o valor para um endereço IP privado estático. A lista mostrada após a saída explica os parâmetros usados.

        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd

    Saída esperada:

        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK

    - **-l (ou - local)**. Azure região onde a máquina virtual será criada. Para o nosso cenário, *centralus*.
    - **-n (ou - máquina virtual-nome)**. Nome da máquina virtual a ser criado.
    - **-w (ou --nome de rede virtual)**. Nome da VNet onde a máquina virtual será criada. 
    - **-S (ou - ip estático)**. Endereço IP privado estático para a máquina virtual.
    - **TestService**. Nome do serviço de nuvem onde a máquina virtual será criada.
    - **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**. Imagem usada para criar a máquina virtual.
    - **adminuser**. Administrador local para a máquina virtual Windows.
    - **AdminP@ssw0rd**. Senha de administrador local para a máquina virtual Windows.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como recuperar informações de endereço IP particulares estático para uma máquina virtual
Para exibir as informações de endereço IP particulares estático para a máquina virtual criada com o script acima, execute o seguinte comando CLI do Azure e observe o valor para *StaticIP de rede*:

    azure vm static-ip show DNS01

Saída esperada:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP privado estático de uma máquina virtual
Para remover o endereço IP privado estático adicionados para a máquina virtual no script acima, execute o seguinte comando CLI Azure:
    
    azure vm static-ip remove DNS01

Saída esperada:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Como adicionar um IP privado estático para uma máquina virtual existente
Para adicionar um estático particular endereço IP para a máquina virtual criada usando o script acima, runt ele comandos a seguir:

    azure vm static-ip set DNS01 192.168.1.101

Saída esperada:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre endereços [IP públicos reservado](virtual-networks-reserved-public-ip.md) .
- Saiba mais sobre endereços de [nível de instância pública IP (ILPIP)](virtual-networks-instance-level-public-ip.md) .
- Consulte as [APIs REST de IP reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx).
