<properties
   pageTitle="Criar uma Internet opostas balanceador de carga no Gerenciador de recursos utilizando a CLI Azure | Microsoft Azure"
   description="Aprenda a criar uma Internet opostas balanceador de carga no Gerenciador de recursos utilizando a CLI do Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="creating-an-internal-load-balancer-using-the-azure-cli"></a>Criando um balanceador de carga interno utilizando a CLI do Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo discute o modelo de implantação do Gerenciador de recursos. Você também pode [aprender a criar uma Internet opostas balanceador de carga usando a implantação clássica](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-using-the-azure-cli"></a>Implantação da solução usando a CLI do Azure

As etapas a seguir mostram como criar uma Internet opostas balanceador de carga usando o Gerenciador de recursos do Azure com CLI. Com o Gerenciador de recursos do Azure cada recurso é criado e configurado individualmente, em seguida, coloque juntos para criar um recurso.

Você deve criar e configurar os objetos a seguir para implantar um balanceador de carga:

- Configuração de IP front-end - contém os endereços IP públicos tráfego de rede.
- Pool de endereços de back-end - contém interfaces de rede (NICs) para as máquinas virtuais receber o tráfego de rede de Balanceador de carga.
- Regras de balanceamento de carga - contém regras mapeando uma porta pública no balanceador de carga para porta no pool de endereços de back-end.
- Regras NAT de entrada - contém regras mapeando uma porta pública no balanceador de carga para uma porta para uma máquina virtual específica no pool de endereços de back-end.
- Testes - contém testes de integridade usadas para verificar a disponibilidade de instâncias de máquinas virtuais no pool de endereços de back-end.

Para obter mais informações, consulte [Gerenciador de recursos do Azure suporte do balanceador de carga](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Configurar o CLI para usar o Gerenciador de recursos

1. Se você nunca usou CLI do Azure, consulte [instalar e configurar o CLI do Azure](../../articles/xplat-cli-install.md) e siga as instruções para cima até o ponto onde você selecionar sua conta do Azure e assinatura.

2. Execute o comando de **modo config azure** para alternar para modo de Gerenciador de recursos, conforme mostrado abaixo.

        azure config mode arm

    Saída esperada:

        info:    New mode is arm

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Criar uma rede virtual e um endereço IP público para o pool IP front-end

1. Crie uma rede virtual (VNet) denominada *NRPVnet* no local Leste EUA usando um grupo de recursos denominado *NRPRG*.

        azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16

    Crie uma sub-rede denominada *NRPVnetSubnet* com um bloco CIDR de 10.0.0.0/24 em *NRPVnet*.

        azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24

2. Crie um endereço IP público chamado *NRPPublicIP* para ser usado em um pool IP front-end com DNS nome *loadbalancernrp.eastus.cloudapp.azure.com*. O comando a seguir usa o tipo de alocação estática e tempo limite ocioso de 4 minutos.

        azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4

    >[AZURE.IMPORTANT]O balanceador de carga usará o rótulo de domínio do público IP como seu FQDN. Esse uma alteração de implantação clássica, que usa a nuvem serviço como o balanceador de carga totalmente o nome de domínio qualificado (FQDN).
    >Neste exemplo, o FQDN é *loadbalancernrp.eastus.cloudapp.azure.com*.

## <a name="create-a-load-balancer"></a>Criar um balanceador de carga

O comando a seguir cria um balanceador de carga denominado *NRPlb* no grupo de recursos *NRPRG* nos *EUA Leste* Azure local.

    azure network lb create NRPRG NRPlb eastus

## <a name="create-a-front-end-ip-pool-and-a-backend-address-pool"></a>Criar um pool de front-end de IP e um pool de endereços de back-end

Este exemplo demonstra como criar o pool de IP front-end que recebe o tráfego de rede de entrada no balanceador de carga e o pool IP de back-end onde o pool de front-end envia o tráfego de rede de balanceamento de carga.

1. Crie um pool IP front-end associando o IP público criado na etapa anterior e balanceador de carga.

        azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip

2. Configure um pool de endereços de back-end usado para receber o tráfego de entrada do pool de front-end IP.

        azure network lb address-pool create NRPRG NRPlb NRPbackendpool

## <a name="create-lb-rules-nat-rules-and-probe"></a>Criar regras LB, regras NAT e teste

Este exemplo cria os itens a seguir.

- uma regra NAT para traduzir todo o tráfego de entrada na porta 21 à porta 22<sup>1</sup>
- uma regra NAT para traduzir todo o tráfego de entrada na porta 23 à porta 22
- uma regra de Balanceador de carga para saldo todo o tráfego de entrada na porta 80 à porta 80 nos endereços do pool de back-end.
- uma regra de teste para verificar o status de integridade em uma página denominada *HealthProbe.aspx*.

<sup>1</sup> regras NAT são associadas a uma instância de máquina virtual específica atrás balanceador de carga. O tráfego de rede que chegam na porta 21 é enviado para uma máquina virtual específica na porta 22 associado a essa regra NAT. Você deve especificar um protocolo (UDP ou TCP) para uma regra NAT. Ambos os protocolos não podem ser atribuídos para a mesma porta.

1. Crie as regras NAT.

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh1 --protocol tcp --frontend-port 21 --backend-port 22
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh2 --protocol tcp --frontend-port 23 --backend-port 22

2. Crie uma regra de Balanceador de carga.

        azure network lb rule create nrprg nrplb lbrule -p tcp -f 80 -b 80 -t NRPfrontendpool -o NRPbackendpool

3. Crie um teste de integridade.

        azure network lb probe create --resource-group nrprg --lb-name nrplb --name healthprobe --protocol "http" --port 80 --path healthprobe.aspx --interval 15 --count 4

4. Verifique suas configurações.

        azure network lb show nrprg nrplb

    Saída esperada:

        info:    Executing command network lb show
        + Looking up the load balancer "nrplb"
        + Looking up the public ip "NRPPublicIP"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
        data:    Name                            : nrplb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : eastus
        data:    Provisioning State              : Succeeded
        data:    Frontend IP configurations:
        data:      Name                          : NRPfrontendpool
        data:      Provisioning state            : Succeeded
        data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
        data:      Public IP allocation method   : Static
        data:      Public IP address             : 40.114.13.145
        data:
        data:    Backend address pools:
        data:      Name                          : NRPbackendpool
        data:      Provisioning state            : Succeeded
        data:
        data:    Load balancing rules:
        data:      Name                          : HTTP
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 80
        data:      Backend port                  : 80
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:
        data:    Inbound NAT rules:
        data:      Name                          : ssh1
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 21
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:      Name                          : ssh2
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 23
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:    Probes:
        data:      Name                          : healthprobe
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Http
        data:      Port                          : 80
        data:      Interval in seconds           : 15
        data:      Number of probes              : 4
        data:
        info:    network lb show command OK

## <a name="create-nics"></a>Criar NICs

Você precisa criar NICs (ou modificar as existentes) e associá-los NAT regras, regras de Balanceador de carga e testes.

1. Criar uma NIC denominada *lb-nic1-ser*e associá-la a regra NAT *rdp1* e o pool de endereços de back-end *NRPbackendpool* .

        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

    Saída esperada:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. Criar uma NIC denominada *lb-nic2-ser*e associá-la a regra NAT *rdp2* e o pool de endereços de back-end *NRPbackendpool* .

        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

3. Criar uma máquina virtual (VM) denominada *web1*e associá-la a NIC denominada *lb-nic1-ser*. Uma conta de armazenamento chamada *web1nrp* foi criada antes de executar o comando a seguir.

        azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] VMs em um balanceador de carga precisam estar no mesmo conjunto de disponibilidade. Use `azure availset create` para criar uma disponibilidade conjunto.

    A saída deve ser semelhante ao seguinte:

        info:    Executing command vm create
        + Looking up the VM "web1"
        Enter username: azureuser
        Enter password for azureuser: *********
        Confirm password: *********
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account web1nrp
        + Looking up the availability set "nrp-avset"
        info:    Found an Availability set "nrp-avset"
        + Looking up the NIC "lb-nic1-be"
        info:    Found an existing NIC "lb-nic1-be"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
        info:    This is a NIC without publicIP configured
        + Creating VM "web1"
        info:    vm create command OK

    >[AZURE.NOTE] A mensagem informativa **é uma NIC sem publicIP configurado** é esperada desde a NIC criada para se conectar à Internet usando o endereço IP público do balanceador de carga de Balanceador de carga.

    Desde que a *ser lb-nic1* NIC está associado a regra NAT *rdp1* , você pode se conectar a *web1* usando o RDP através de porta 3441 no balanceador de carga.

4. Criar uma máquina virtual (VM) denominada *web2*e associá-la a NIC denominada *lb-nic2-ser*. Uma conta de armazenamento chamada *web1nrp* foi criada antes de executar o comando a seguir.

        azure vm create --resource-group nrprg --name web2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## <a name="update-an-existing-load-balancer"></a>Atualizar um balanceador de carga existente

Você pode adicionar regras referenciando um balanceador de carga existente. No próximo exemplo, uma nova regra de Balanceador de carga é adicionada a um balanceador de carga **NRPlb** existente

    azure network lb rule create --resource-group nrprg --lb-name nrplb --name lbrule2 --protocol tcp --frontend-port 8080 --backend-port 8051 --frontend-ip-name frontendnrppool --backend-address-pool-name NRPbackendpool

## <a name="delete-a-load-balancer"></a>Excluir um balanceador de carga

Use o comando a seguir para remover um balanceador de carga:

    azure network lb delete --resource-group nrprg --name nrplb

## <a name="next-steps"></a>Próximas etapas

[Começar a configurar um balanceador de carga interno](load-balancer-get-started-ilb-arm-cli.md)

[Configurar um modo de distribuição de Balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite TCP ociosas para seu balanceador de carga](load-balancer-tcp-idle-timeout.md)
