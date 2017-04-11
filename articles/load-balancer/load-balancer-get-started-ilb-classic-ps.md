<properties
   pageTitle="Criar um balanceador de carga interno usando o PowerShell no modelo clássico de implantação | Microsoft Azure"
   description="Aprenda a criar um balanceador de carga interno usando o PowerShell no modelo clássico de implantação"
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

# <a name="get-started-creating-an-internal-load-balancer-classic-using-powershell"></a>Começar a criar um balanceador de carga interno (clássico) usando o PowerShell

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Saiba como [executar essas etapas usando o modelo do Gerenciador de recursos](load-balancer-get-started-ilb-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


## <a name="create-an-internal-load-balancer-set-for-virtual-machines"></a>Criar um balanceador de carga interno definido para máquinas virtuais

Para criar um conjunto de Balanceador de carga interno e os servidores que enviarão o tráfego para ele, você deve fazer o seguinte:

1. Crie uma instância de interno balanceamento de carga que será o ponto de extremidade de tráfego de entrada seja equilibrada os servidores de um conjunto de balanceamento de carga.

1. Adicione pontos de extremidade correspondente às máquinas virtuais que estará recebendo o tráfego de entrada.

1. Configure os servidores que estará enviando o tráfego para ter a carga equilibrada para enviar o tráfego para o endereço IP (VIP) virtual da instância interno balanceamento de carga.


### <a name="step-1-create-an-internal-load-balancing-instance"></a>Etapa 1: Criar uma instância de balanceamento de carga interno

Para um serviço de nuvem existente ou um serviço de nuvem implantado em uma rede virtual regional, você pode criar uma instância de balanceamento de carga interno com os seguintes comandos do Windows PowerShell:

    $svc="<Cloud Service Name>"
    $ilb="<Name of your ILB instance>"
    $subnet="<Name of the subnet within your virtual network>"
    $IP="<The IPv4 address to use on the subnet-optional>"

    Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP


Observe que esse uso do cmdlet [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) Windows PowerShell usa o conjunto de parâmetro DefaultProbe. Para obter mais informações sobre conjuntos de parâmetro adicional, consulte [Adicionar AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx).

### <a name="step-2-add-endpoints-to-the-internal-load-balancing-instance"></a>Etapa 2: Adicionar pontos de extremidade para a instância de balanceamento de carga interno

Aqui está um exemplo:

    $svc="mytestcloud"
    $vmname="DB1"
    $epname="TCP-1433-1433"
    $lbsetname="lbset"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    $ilb="ilbset"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


### <a name="step-3-configure-your-servers-to-send-their-traffic-to-the-new-internal-load-balancing-endpoint"></a>Etapa 3: Configurar os servidores para enviar o tráfego para o novo ponto de extremidade de balanceamento de carga interno

Você precisa configurar os servidores cujo tráfego vai será carga equilibrada para usar o novo endereço IP (VIP) da instância interno balanceamento de carga. Este é o endereço no qual a instância de balanceamento de carga interna é listening. Na maioria dos casos, você precisa apenas adicionar ou modificar um registro DNS para o VIP da instância balanceamento interno.

Se você especificou o endereço IP durante a criação da instância balanceamento interno, você já tem o VIP. Caso contrário, você pode ver o VIP entre os seguintes comandos:

    $svc="<Cloud Service Name>"
    Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer



Para usar esses comandos, preencher os valores e remover o < e >. Aqui está um exemplo:

    $svc="mytestcloud"
    Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer


Na exibição do comando Get-AzureInternalLoadBalancer, anote o endereço IP e faça as alterações necessárias para seus servidores ou registros DNS para garantir que o tráfego é enviado para o VIP.

>[AZURE.NOTE]A plataforma do Microsoft Azure usa um endereço IPv4 estático, publicamente roteável para uma variedade de cenários administrativos. O endereço IP é 168.63.129.16. Este endereço IP não deve ser bloqueado por qualquer firewalls, porque ele pode causar um comportamento inesperado.
>Relacionadas com Azure interno balanceamento de carga, esse endereço IP é usado pelo monitoramento testes de Balanceador de carga para determinar o estado de integridade para máquinas virtuais em um conjunto de balanceamento de carga. Se um grupo de segurança de rede é usado para restringir o tráfego para Azure máquinas virtuais em um conjunto de balanceamento de carga internamente ou é aplicado a uma sub-rede da rede Virtual, certifique-se de que uma regra de segurança de rede é adicionada para permitir o tráfego de 168.63.129.16.


## <a name="example-of-internal-load-balancing"></a>Exemplo de balanceamento de carga interna

Para percorrer o processo de fim a fim de criar um conjunto de balanceamento de carga para duas configurações de exemplo, consulte as seções a seguir.

### <a name="an-internet-facing-multi-tier-application"></a>Um Internet opostas, o aplicativo de várias camadas

Você deseja fornecer um serviço de banco de dados de balanceamento de carga de um conjunto de servidores web de Internet. Os dois conjuntos de servidores são hospedados em um serviço de nuvem Azure único. Tráfego de servidor da Web para a porta TCP 1433 deve ser distribuído entre duas máquinas virtuais na camada de banco de dados. Figura 1 mostra a configuração.

![Conjunto de balanceamento de carga interno para a camada de banco de dados](./media/load-balancer-internal-getstarted/IC736321.png)


A configuração consiste no seguinte:

- O serviço de nuvem existente hospedando máquinas virtuais é denominado mytestcloud.

- Os dois servidores de banco de dados existentes são nomeados DB1, DB2.

- Servidores Web na camada da web se conectar aos servidores do banco de dados na camada de banco de dados usando o endereço IP privado. Outra opção é usar o seu próprio DNS para a rede virtual e registrar manualmente um registro para o conjunto de Balanceador de carga interno.

Os seguintes comandos configurar uma nova instância de balanceamento de carga interno chamada **ILBset** e adicionar pontos de extremidade às máquinas virtuais correspondente para os servidores de banco de dados de dois:

    $svc="mytestcloud"
    $ilb="ilbset"
    Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
    $prot="tcp"
    $locport=1433
    $pubport=1433
    $epname="TCP-1433-1433"
    $lbsetname="lbset"
    $vmname="DB1"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

    $epname="TCP-1433-1433-2"
    $vmname="DB2"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


## <a name="remove-an-internal-load-balancing-configuration"></a>Remova uma configuração de balanceamento de carga interno

Para remover uma máquina virtual como um ponto de extremidade de uma instância de Balanceador de carga interno, use os seguintes comandos:

    $svc="<Cloud service name>"
    $vmname="<Name of the VM>"
    $epname="<Name of the endpoint>"
    Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

Para usar esses comandos, preencher os valores, removendo o < e >.

Aqui está um exemplo:

    $svc="mytestcloud"
    $vmname="DB1"
    $epname="TCP-1433-1433"
    Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

Para remover uma instância de Balanceador de carga interna de um serviço na nuvem, use os seguintes comandos:

    $svc="<Cloud service name>"
    Remove-AzureInternalLoadBalancer -ServiceName $svc

Para usar esses comandos, preencha o valor e remova o < e >.

Aqui está um exemplo:

    $svc="mytestcloud"
    Remove-AzureInternalLoadBalancer -ServiceName $svc



## <a name="additional-information-about-internal-load-balancer-cmdlets"></a>Informações adicionais sobre os cmdlets de Balanceador de carga interno


Para obter informações adicionais sobre os cmdlets de balanceamento de carga interno, execute os seguintes comandos em um prompt do Windows PowerShell:

- Obtenha a Ajuda de nova AzureInternalLoadBalancerConfig-completo

- Get-help AzureInternalLoadBalancer adicionar-completo

- Get-help Get-AzureInternalLoadbalancer-completo

- Obtenha a Ajuda de remover-AzureInternalLoadBalancer-completo

## <a name="next-steps"></a>Próximas etapas

[Configurar um modo de distribuição de Balanceador de carga usando afinidade IP de origem](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite TCP ociosas para seu balanceador de carga](load-balancer-tcp-idle-timeout.md)