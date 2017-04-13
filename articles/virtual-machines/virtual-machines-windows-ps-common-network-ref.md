<properties
    pageTitle="Comandos de rede comum PowerShell para VMs | Microsoft Azure"
    description="Comandos comuns do PowerShell para você começou a criação de uma rede virtual e seus recursos associados para VMs."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="common-network-azure-powershell-commands-for-vms"></a>Comandos de rede Azure PowerShell comuns para VMs

Se você quiser criar uma máquina virtual, você precisa criar uma [rede virtual](../virtual-network/virtual-networks-overview.md) ou saber sobre uma rede virtual existente no qual a máquina virtual pode ser adicionada. Normalmente, quando você cria uma máquina virtual, você também precisa considerar a criação dos recursos descritos neste artigo.

Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter informações sobre como instalar a versão mais recente do PowerShell do Azure, selecionando sua assinatura e entrar sua conta.

## <a name="create-network-resources"></a>Criar recursos de rede

Tarefa | Comando 
-------------- | -------------------------
Criar configurações de sub-rede | $subnet1 = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -nome "subnet_name" - AddressPrefix XX. X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig-nome "subnet_name" - AddressPrefix XX. X.X.X/XX<BR><BR>Uma rede típica pode ter uma sub-rede para um [internet opostas balanceador de carga](../load-balancer/load-balancer-internet-overview.md) e uma sub-rede separada para um [balanceador de carga interno](../load-balancer/load-balancer-internal-overview.md). |
Criar uma rede virtual | $vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -- ResourceGroupName de "virtual_network_name" nome "resource_group_name"-local "nome_do_local" - AddressPrefix XX. X.X.X/XX-sub-rede $subnet1, $subnet2
Teste para um nome de domínio exclusivo | [Teste-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) - DomainQualifiedName "nome_do_domínio"-local "nome_do_local"<BR><BR>Você pode especificar um nome de domínio DNS para um [recurso IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md), que cria um mapeamento para domainname.location.cloudapp.azure.com para o endereço IP público nos servidores DNS gerenciado Azure. O nome pode conter apenas letras, números e hifens. O primeiro e último caracteres deve ser uma letra ou número e o nome de domínio devem ser exclusivos dentro de seu local Azure. Se **True** será retornado, seu nome proposto é globalmente exclusivo.
Criar um endereço IP público | $pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -nome_de_domínio"nome"ip_address_name"- ResourceGroupName"resource_group_name"- DomainNameLabel"-local "nome_do_local" - AllocationMethod dinâmico<BR><BR>O endereço IP público utiliza o nome de domínio que você testou anteriormente e é usado pela configuração frontend do balanceador de carga.
Criar uma configuração de IP frontend | $frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -nome "frontend_ip_name" - PublicIpAddress $pip<BR><BR>A configuração de frontend inclui o endereço IP público que você criou anteriormente tráfego de rede.
Criar um pool de endereços de back-end | $beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -nome "backend_pool_name"<BR><BR>Fornece endereços internos para o back-end de Balanceador de carga que são acessados por meio de uma interface de rede.
Criar um teste | $healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -nome "probe_name" - RequestPath 'HealthProbe.aspx'-protocolo http-porta 80 - IntervalInSeconds 15 - ProbeCount 2<BR><BR>Contém testes de integridade usadas para verificar a disponibilidade de instâncias de máquinas virtuais no pool de endereços de back-end.
Criar uma regra de balanceamento de carga | $lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -nome HTTP - FrontendIpConfiguration $frontendIP - BackendAddressPool $beAddressPool-teste $healthProbe-protocolo Tcp - FrontendPort 80 - BackendPort 80<BR><BR>Contém regras que atribuir uma porta pública no balanceador de carga para uma porta no pool de endereços de back-end.
Criar uma regra de NAT de entrada | $inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -nome "rule_name" - FrontendIpConfiguration $frontendIP-protocolo TCP - FrontendPort 3441 - BackendPort 3389<BR><BR>Contém regras mapeando uma porta pública no balanceador de carga para uma porta para uma máquina virtual específica no pool de endereços de back-end.
Criar um balanceador de carga | $loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) - ResourceGroupName "resource_group_name"-nome "load_balancer_name"-local "nome_do_local" - FrontendIpConfiguration $frontendIP - InboundNatRule $inboundNATRule - LoadBalancingRule $lbRule - BackendAddressPool $beAddressPool-teste $healthProbe
Criar uma interface de rede | $nic1 = [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) - ResourceGroupName "resource_group_name"-nome "network_interface_name"-local "nome_do_local" - PrivateIpAddress XX. X.X.X-sub-rede subnet2 - LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] - LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Crie uma interface de rede usando o endereço IP público e sub-rede de rede virtual que você criou anteriormente.
    
## <a name="get-information-about-network-resources"></a>Obter informações sobre recursos de rede

Tarefa | Comando 
-------------- | -------------------------
Redes virtuais de lista | [Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Lista todas as redes virtuais no grupo de recursos.
Obter informações sobre uma rede virtual | Get-AzureRmVirtualNetwork-- ResourceGroupName de "virtual_network_name" nome "resource_group_name"
Lista sub-redes em uma rede virtual | Get-AzureRmVirtualNetwork-nome - ResourceGroupName "virtual_network_name" "resource_group_name" & #124; Selecione sub-redes
Obter informações sobre uma sub-rede | [Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -nome "subnet_name" - VirtualNetwork $vnet<BR><BR>Obtém informações sobre a sub-rede na rede virtual especificada. O valor de $vnet representa o objeto retornado por Get-AzureRmVirtualNetwork.
Lista endereços IP | [Get-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619342.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Lista os endereços IP públicos no grupo de recursos.
Balanceadores de carga de lista | [Get-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603668.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Lista todos os balanceadores de carga no grupo de recursos.
Interfaces de rede de lista | [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Lista todas as interfaces de rede no grupo de recursos.
Obter informações sobre uma interface de rede | Get-AzureRmNetworkInterface-- ResourceGroupName de "network_interface_name" nome "resource_group_name"<BR><BR>Obtém informações sobre uma interface de rede específica.
Obter a configuração de IP de uma interface de rede | [Get-AzureRmNetworkInterfaceIPConfig](https://msdn.microsoft.com/library/mt732618.aspx) -nome "ipconfiguration_name" - NetworkInterface $nic<BR><BR>Obtém informações sobre a configuração de IP da interface de rede especificado. O valor de $nic representa o objeto retornado por Get-AzureRmNetworkInterface.

## <a name="manage-network-resources"></a>Gerenciar recursos de rede

Tarefa | Comando 
-------------- | -------------------------
Adicionar uma sub-rede a uma rede virtual | [Adicionar-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603722.aspx) - AddressPrefix XX. X.X.X/XX-nome "subnet_name" - VirtualNetwork $vnet<BR><BR>Adiciona uma sub-rede a uma rede virtual existente. O valor de $vnet representa o objeto retornado por Get-AzureRmVirtualNetwork.
Excluir uma rede virtual | [Remover AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt619338.aspx) -- ResourceGroupName de "virtual_network_name" nome "resource_group_name"<BR><BR>Remove a rede virtual especificada do grupo de recursos.
Excluir uma interface de rede | [Remover AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt603836.aspx) -- ResourceGroupName de "network_interface_name" nome "resource_group_name"<BR><BR>Remove a interface de rede especificado do grupo de recursos.
Excluir um balanceador de carga | [Remover AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) -- ResourceGroupName de "load_balancer_name" nome "resource_group_name"<BR><BR>Remove o balanceador de carga especificada do grupo de recursos.
Excluir um endereço IP público | [Remover AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619352.aspx)-- ResourceGroupName de "ip_address_name" nome "resource_group_name"<BR><BR>Remove o endereço IP público especificado do grupo de recursos.

## <a name="next-steps"></a>Próximas etapas

- Usar a interface de rede que você acabou de criar quando você [criar uma máquina virtual](virtual-machines-windows-ps-create.md).
- Saiba mais sobre como você pode [criar uma máquina virtual com várias interfaces de rede](../virtual-network/virtual-networks-multiple-nics.md).
