<properties
   pageTitle="Começar a criar um Internet opostas balanceador de carga no modelo de implantação clássico utilizando a CLI Azure | Microsoft Azure"
   description="Aprenda a criar uma Internet opostas balanceador de carga no modelo de implantação clássico usando a CLI do Azure"
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
   ms.date="02/09/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-cli"></a>Começar a criar um Internet opostas balanceador de carga (clássico) na CLI do Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo discute o modelo de implantação clássico. Você também pode [aprender a criar uma Internet opostas balanceador de carga usando o Gerenciador de recursos do Azure](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## <a name="step-by-step-creating-an-internet-facing-load-balancer-using-cli"></a>Passo a passo Criando um opostas balanceador de carga usando CLI da Internet

Este guia mostra como criar um balanceador de carga de Internet baseado no cenário acima.

1. Se você nunca usou CLI do Azure, consulte [instalar e configurar o CLI do Azure](../../articles/xplat-cli-install.md) e siga as instruções para cima até o ponto onde você selecionar sua conta do Azure e assinatura.

2. Execute o comando de **modo config azure** para alternar para modo clássico, conforme mostrado abaixo.

        azure config mode asm

    Saída esperada:

        info:    New mode is asm


## <a name="create-endpoint-and-load-balancer-set"></a>Criar ponto de extremidade e conjunto de Balanceador de carga

O cenário supõe máquinas virtuais "web1" e "web2" foram criados.
Este guia irá criar um conjunto de Balanceador de carga usando as portas 80 como porta pública e porta 80 como porta local. Uma porta de teste também é configurada na porta 80 e nomeada o conjunto de Balanceador de carga "lbset".


### <a name="step-1"></a>Etapa 1

Criar o primeiro ponto de extremidade e definiu usando de Balanceador de carga `azure network vm endpoint create` para máquina virtual "web1".

    azure vm endpoint create web1 80 -k 80 -o tcp -t 80 -b lbset

Parâmetros usados:

**k** - porta de máquina virtual local<br>
**-o** - protocolo<BR>
**-t** - porta de teste<BR>
**-b** - nome de Balanceador de carga<BR>

## <a name="step-2"></a>Etapa 2

Adicione uma segunda máquina virtual "web2" ao conjunto de Balanceador de carga.

    azure vm endpoint create web2 80 -k 80 -o tcp -t 80 -b lbset

## <a name="step-3"></a>Etapa 3

Verifique se a configuração de Balanceador de carga usando `azure vm show` .

    azure vm show web1

A saída será:

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Criar um ponto de extremidade da área de trabalho remoto para uma máquina virtual

Você pode criar um ponto de extremidade da área de trabalho remoto para encaminhar o tráfego de rede de uma porta pública para uma porta local para uma máquina virtual específica usando `azure vm endpoint create`.

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>Remover máquina virtual do balanceador de carga

Você precisa excluir o ponto de extremidade associado ao balanceador de carga definido na máquina virtual. Após o ponto de extremidade é removido, a máquina virtual não pertence ao balanceador de carga definir mais.

 Usando o exemplo acima, você pode remover o ponto de extremidade criado para máquina virtual "web1" de Balanceador de carga "lbset" usando o comando `azure vm endpoint delete`.

    azure vm endpoint delete web1 tcp-80-80


>[AZURE.NOTE] Você pode explorar mais opções para gerenciar pontos de extremidade usando o comando`azure vm endpoint --help`


## <a name="next-steps"></a>Próximas etapas

[Começar a configurar um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição de Balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite TCP ociosas para seu balanceador de carga](load-balancer-tcp-idle-timeout.md)

