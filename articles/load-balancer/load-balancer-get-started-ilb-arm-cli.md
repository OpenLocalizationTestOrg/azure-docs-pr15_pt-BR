<properties
   pageTitle="Criar um balanceador de carga interno usando a CLI Azure no Gerenciador de recursos | Microsoft Azure"
   description="Aprenda a criar um balanceador de carga interno usando a CLI Azure no Gerenciador de recursos"
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

# <a name="create-an-internal-load-balancer-by-using-the-azure-cli"></a>Criar um balanceador de carga interno usando a CLI do Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implantação clássico](load-balancer-get-started-ilb-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-solution-by-using-the-azure-cli"></a>Implantar a solução usando a CLI do Azure

As etapas a seguir mostram como criar um balanceador de carga voltado para a Internet usando o Gerenciador de recursos do Azure com CLI. Com o Gerenciador de recursos do Azure, cada recurso é criado configurado individualmente e, em seguida, coloque juntos para criar um recurso.

Você precisa criar e configurar os objetos a seguir para implantar um balanceador de carga:

- **Configuração de IP de front-end**: contém os endereços IP públicos tráfego de rede
- **Pool de endereços de back-end**: contém interfaces de rede (NICs) que permitem que as máquinas virtuais receber o tráfego de rede de Balanceador de carga
- **Regras de balanceamento de carga**: contém regras que mapeiam uma porta pública no balanceador de carga à porta no pool de endereços de back-end
- **Regras de entrada NAT**: contém regras que mapeiam uma porta pública no balanceador de carga a uma porta para uma máquina virtual específica no pool de endereços de back-end
- **Testes**: contém testes de integridade que são usados para verificar a disponibilidade de instâncias de máquinas virtuais no pool de endereços de back-end

Para obter mais informações, consulte [Gerenciador de recursos do Azure suporte do balanceador de carga](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Configurar o CLI para usar o Gerenciador de recursos

1. Se você nunca usou CLI do Azure, consulte [instalar e configurar o CLI Azure](../../articles/xplat-cli-install.md). Siga as instruções para cima até o ponto onde você selecionar sua conta do Azure e assinatura.

2. Execute o comando **modo config azure** para alternar para modo de Gerenciador de recursos, da seguinte maneira:

        azure config mode arm

    Saída esperada:

        info:    New mode is arm

## <a name="create-an-internal-load-balancer-step-by-step"></a>Criar um balanceador de carga interno passo a passo

1. Entrar no Azure.

        azure login

    Quando solicitado, insira suas credenciais Azure.

2. Altere as ferramentas de comando para o modo do Gerenciador de recursos do Azure.

        azure config mode arm

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Todos os recursos no Gerenciador de recursos do Azure estão associados um grupo de recursos. Se você ainda não tiver feito isso ainda, crie um grupo de recursos.

    azure group create <resource group name> <location>

## <a name="create-an-internal-load-balancer-set"></a>Criar um conjunto de Balanceador de carga interno

1. Criar um balanceador de carga interno

    No cenário a seguir, um grupo de recursos chamado nrprg é criado na região Leste EUA.

        azure network lb create --name nrprg --location eastus

    >[AZURE.NOTE] Todos os recursos para um balanceadores de carga interno, como redes virtuais e sub-redes de uma rede virtual, devem estar no mesmo grupo de recursos e na mesma região.

2. Crie um endereço IP front-end para o balanceador de carga interno.

    O endereço IP que você usar deve ser dentro do intervalo de sub-rede de sua rede virtual.

        azure network lb frontend-ip create --resource-group nrprg --lb-name ilbset --name feilb --private-ip-address 10.0.0.7 --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet

3. Crie o pool de endereço de back-end.

        azure network lb address-pool create --resource-group nrprg --lb-name ilbset --name beilb

    Após definir um endereço IP front-end e um pool de endereços de back-end, você pode criar regras de Balanceador de carga, regras de entrada NAT, e testes de saúde personalizadas.

4. Crie uma regra de Balanceador de carga de Balanceador de carga interno.

    Quando você seguir as etapas anteriores, o comando cria uma regra de Balanceador de carga para ouvir a porta 1433 no pool de front-end e também enviar tráfego de balanceamento de carga de rede para o pool de endereços de back-end, usando a porta 1433.

        azure network lb rule create --resource-group nrprg --lb-name ilbset --name ilbrule --protocol tcp --frontend-port 1433 --backend-port 1433 --frontend-ip-name feilb --backend-address-pool-name beilb

5. Crie regras de NAT de entrada.

    Regras de NAT de entrada são usadas para criar pontos de extremidade em um balanceador de carga que ir para uma instância de máquina virtual específica. As etapas anteriores criado duas regras NAT para área de trabalho remota.

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule1 --protocol TCP --frontend-port 5432 --backend-port 3389

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule2 --protocol TCP --frontend-port 5433 --backend-port 3389

6. Crie testes de integridade para o balanceador de carga.

    Um teste de integridade verifica todas as instâncias de máquina virtual para certificar-se de que eles podem enviar tráfego de rede. A instância de máquina virtual com verificações de teste com falha é removida de Balanceador de carga até ele fica online novamente e uma seleção de teste determina o que é eficaz.

        azure network lb probe create --resource-group nrprg --lb-name ilbset --name ilbprobe --protocol tcp --interval 300 --count 4

    >[AZURE.NOTE]A plataforma do Microsoft Azure usa um endereço IPv4 estático, publicamente roteável para uma variedade de cenários administrativos. O endereço IP é 168.63.129.16. Este endereço IP não deve ser bloqueado por qualquer firewalls, porque isso pode causar um comportamento inesperado.
    >Relacionadas com balanceamento de carga interna Azure, esse endereço IP é usado pelo monitoramento testes de Balanceador de carga para determinar o estado de integridade para máquinas virtuais em um conjunto de balanceamento de carga. Se um grupo de segurança de rede é usado para restringir o tráfego para Azure máquinas virtuais em um conjunto de balanceamento de carga internamente ou é aplicado a uma sub-rede de rede virtual, certifique-se de que uma regra de segurança de rede é adicionada para permitir o tráfego de 168.63.129.16.

## <a name="create-nics"></a>Criar NICs

Você precisa criar NICs (ou modificar as existentes) e associá-los NAT regras, regras de Balanceador de carga e testes.

1. Criar uma NIC denominada *lb-nic1-ser*e, em seguida, associá-la a regra NAT de *rdp1* e o pool de endereços de back-end *beilb* .

        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" --location eastus

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

2. Criar uma NIC denominada *lb-nic2-ser*e, em seguida, associá-la a regra NAT de *rdp2* e o pool de endereços de back-end *beilb* .

        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" --location eastus

3. Criar uma máquina virtual nomeada *DB1*e associá-lo com a NIC denominada *lb-nic1-ser*. Uma conta de armazenamento chamada *web1nrp* é criada antes de executa o comando a seguir:

        azure vm create --resource--resource-grouproup nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] VMs em um balanceador de carga precisam estar no mesmo conjunto de disponibilidade. Use `azure availset create` para criar uma disponibilidade conjunto.

4. Criar uma máquina virtual (VM) denominada *DB2*e associá-lo com a NIC denominada *lb-nic2-ser*. Uma conta de armazenamento chamada *web1nrp* foi criada antes de executar o comando a seguir.

        azure vm create --resource--resource-grouproup nrprg --name DB2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## <a name="delete-a-load-balancer"></a>Excluir um balanceador de carga

Para remover um balanceador de carga, use o seguinte comando:

    azure network lb delete --resource-group nrprg --name ilbset

## <a name="next-steps"></a>Próximas etapas

[Configurar um modo de distribuição de Balanceador de carga usando afinidade IP de origem](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite TCP ociosas para seu balanceador de carga](load-balancer-tcp-idle-timeout.md)
