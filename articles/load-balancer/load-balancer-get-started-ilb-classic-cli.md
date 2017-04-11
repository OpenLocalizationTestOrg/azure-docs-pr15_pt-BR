<properties
   pageTitle="Criar um balanceador de carga interno utilizando a CLI Azure no modelo clássico de implantação | Microsoft Azure"
   description="Aprenda a criar um balanceador de carga interno utilizando a CLI Azure no modelo clássico de implantação"
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

# <a name="get-started-creating-an-internal-load-balancer-classic-using-the-azure-cli"></a>Começar a criar um balanceador de carga interno (clássico) usando a CLI do Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Saiba como [executar essas etapas usando o modelo do Gerenciador de recursos](load-balancer-get-started-ilb-arm-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Para criar um balanceador de carga interno defina para máquinas virtuais

Para criar um conjunto de Balanceador de carga interno e os servidores que enviarão o tráfego para ele, você deve fazer o seguinte:

1. Crie uma instância de interno balanceamento de carga que será o ponto de extremidade de tráfego de entrada seja equilibrada os servidores de um conjunto de balanceamento de carga.

1. Adicione pontos de extremidade correspondente às máquinas virtuais que estará recebendo o tráfego de entrada.

1. Configure os servidores que estará enviando o tráfego para ter a carga equilibrada para enviar o tráfego para o endereço IP (VIP) virtual da instância interno balanceamento de carga.

## <a name="step-by-step-creating-an-internal-load-balancer-using-cli"></a>Passo a passo a criação de um balanceador de carga interno usando CLI

Este guia mostra como criar um balanceador de carga interno baseado no cenário acima.

1. Se você nunca usou CLI do Azure, consulte [instalar e configurar o CLI do Azure](../../articles/xplat-cli-install.md) e siga as instruções para cima até o ponto onde você selecionar sua conta do Azure e assinatura.

2. Execute o comando de **modo config azure** para alternar para modo clássico, conforme mostrado abaixo.

        azure config mode asm

    Saída esperada:

        info:    New mode is asm


## <a name="create-endpoint-and-load-balancer-set"></a>Criar ponto de extremidade e conjunto de Balanceador de carga

O cenário supõe máquinas virtuais "DB1" e "DB2" em um serviço de nuvem chamado "mytestcloud". As duas máquinas virtuais estão usando uma rede virtual chamada Meu "testvnet" com sub-rede "sub-rede-1".

Este guia criará um conjunto de Balanceador de carga interno usando a porta 1433 como porta particular e 1433 como porta local.

Este é um cenário comum onde tem máquinas virtuais SQL no back-end usando um balanceador de carga interno para garantir que os servidores de banco de dados não ficará expostos diretamente usando um endereço IP público.


### <a name="step-1"></a>Etapa 1

Criar um balanceador de carga interno definido usando `azure network service internal-load-balancer add`.

     azure service internal-load-balancer add -r mytestcloud -n ilbset -t subnet-1 -a 192.168.2.7

Parâmetros usados:

**-r** - nome do serviço de nuvem<BR>
**-n** - nome de Balanceador de carga interno<BR>
**-t** - nome de sub-rede (mesma sub-rede por máquinas virtuais que você adicionará ao balanceador de carga interna)<BR>
**-a** - (opcional) adicionar um endereço IP privado estático<BR>

Fazer check-out `azure service internal-load-balancer --help` para obter mais informações.

Você pode verificar as propriedades de Balanceador de carga interno usando o comando `azure service internal-load-balancer list` *nome de serviço de nuvem*.

Veja a seguir um exemplo da saída:

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


## <a name="step-2"></a>Etapa 2

Você configurar o conjunto de Balanceador de carga interno quando você adiciona o primeiro ponto de extremidade. Você associará o ponto de extremidade, a máquina virtual e a porta de teste para o conjunto de Balanceador de carga interno nesta etapa.

    azure vm endpoint create db1 1433 -k 1433 tcp -t 1433 -r tcp -e 300 -f 600 -i ilbset

Parâmetros usados:

**k** - porta de máquina virtual local<BR>
**-t** - porta de teste<BR>
**-r** - protocolo de teste<BR>
**-e** - intervalo de sondagem em segundos<BR>
**-f** - intervalo de tempo limite em segundos <BR>
**-i** - nome de Balanceador de carga interno <BR>


## <a name="step-3"></a>Etapa 3

Verifique se a configuração de Balanceador de carga usando `azure vm show` *nome de máquina virtual*

    azure vm show DB1

A saída será:

        azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK


## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Criar um ponto de extremidade da área de trabalho remoto para uma máquina virtual

Você pode criar um ponto de extremidade da área de trabalho remoto para encaminhar o tráfego de rede de uma porta pública para uma porta local para uma máquina virtual específica usando `azure vm endpoint create`.

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>Remover máquina virtual do balanceador de carga

Você pode remover uma máquina virtual de um balanceador de carga interno definir excluindo o ponto de extremidade associado. Após o ponto de extremidade é removido, a máquina virtual não pertencer ao balanceador de carga definir mais.

 Usando o exemplo acima, você pode remover o ponto de extremidade criado para máquina virtual "DB1" de Balanceador de carga interno "ilbset" usando o comando `azure vm endpoint delete`.

    azure vm endpoint delete DB1 tcp-1433-1433


Fazer check-out `azure vm endpoint --help` para obter mais informações.


## <a name="next-steps"></a>Próximas etapas

[Configurar um modo de distribuição de Balanceador de carga usando afinidade IP de origem](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite TCP ociosas para seu balanceador de carga](load-balancer-tcp-idle-timeout.md)