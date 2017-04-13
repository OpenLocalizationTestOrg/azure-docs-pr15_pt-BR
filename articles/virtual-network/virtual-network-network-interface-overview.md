<properties 
   pageTitle="Interfaces de rede | Microsoft Azure"
   description="Saiba mais sobre interfaces de rede Azure no Gerenciador de recursos do Azure."
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
   ms.date="09/23/2016"
   ms.author="jdial" />

# <a name="network-interfaces"></a>Interfaces de rede

Uma interface de rede (NIC) é a interconexão entre uma máquina Virtual (máquina virtual) e a rede de software subjacente. Este artigo explica o que uma interface de rede é e como ele é usado no modelo de implantação do Gerenciador de recursos do Azure.

A Microsoft recomenda implantando novos recursos usando o modelo de implantação do Gerenciador de recursos, mas você também pode implantar VMs com conectividade de rede no modelo de implantação [clássico](virtual-network-ip-addresses-overview-classic.md) . Se você estiver familiarizado com o modelo clássico, existem diferenças importantes na rede de máquina virtual no modelo de implantação do Gerenciador de recursos. Saiba mais sobre as diferenças lendo o artigo de [rede de máquina Virtual - clássico](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) .

No Azure, uma interface de rede:

1. É um recurso que pode ser criado, excluída, e tem suas próprias configurações configuráveis.
2. Deve estar conectado a uma sub-rede em uma rede Virtual Azure (VNet) quando ele é criado. Se você não estiver familiarizado com VNets, saiba mais sobre eles lendo o artigo de [Visão geral de rede Virtual](virtual-networks-overview.md) . NIC deve estar conectado a um VNet que existe no mesmo [local](https://azure.microsoft.com/regions) de Azure e [assinatura](../azure-glossary-cloud-terminology.md#subscription) como NIC. Depois de uma NIC for criada, você pode alterar a sub-rede que ele está conectado ao, mas não é possível alterar a VNet que ele está conectado ao.
3. Tem um nome atribuído que não podem ser alteradas depois que a NIC é criada. O nome deve ser exclusivo dentro de um [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups)do Azure, mas não precisa ser exclusivo a assinatura, o Azure local em que ele é criado no ou a VNet que ele está conectado ao. Várias NICs normalmente são criadas dentro de uma assinatura do Azure. É recomendável que você planejar uma convenção de nomenclatura que torna Gerenciando várias placas de rede mais fácil que usar nomes padrão. Consulte o artigo [recomendados convenções de nomenclatura para recursos Azure](../guidance/guidance-naming-conventions.md) para obter sugestões.
4. Pode ser anexado a uma máquina virtual, mas só pode ser anexado a uma única máquina virtual que existe no mesmo local que a NIC.
5. Tem um endereço MAC, que é fornecido com NIC para desde que ele permaneça conectado a uma máquina virtual. O endereço MAC é mantido se a máquina virtual seja reiniciada (de dentro do sistema operacional) ou interrompido (eliminação alocado) e iniciado usando o Portal do Azure, Azure PowerShell ou a Interface de linha do Azure. Se ele tiver desanexado de uma máquina virtual e anexada a uma máquina virtual diferente, a NIC recebe um endereço MAC diferente. Se a NIC for excluída, o endereço MAC é atribuído a outras NICs.
6. Deve ter um primário **particular** *IPv4* estático ou dinâmico endereço IP atribuído a ele.
8. Pode ter um recurso de endereço IP público associados a ela.
9. Suporta acelerada redes com virtualização de i/o único raiz (SR-IOV) para tamanhos específicos de máquina virtual executando versões específicas do sistema operacional Microsoft Windows Server. Para saber mais sobre esse recurso de visualização, leia o artigo [acelerada rede para uma máquina virtual](virtual-network-accelerated-networking-powershell.md) .
10. Receba tráfego não destinado a endereços IP particulares atribuídos a ela se encaminhamento IP estiver habilitado para a NIC. Se uma máquina virtual estiver executando o software de firewall por exemplo, ele roteia pacotes não destinados a seus próprios endereços IP. A máquina virtual ainda deverá executar software capaz de roteamento ou encaminhamento de tráfego, mas para fazer isso, encaminhamento IP deve estar habilitado para uma NIC.
11. Muitas vezes é criada no mesmo grupo de recursos como a máquina virtual ele está anexado ao ou a mesma VNet que ele está conectado, apesar de não é necessário ser.

## <a name="vms-with-multiple-network-interfaces"></a>VMs com várias interfaces de rede

Várias placas de rede podem ser anexadas a uma máquina virtual, mas quando fazê-lo estar ciente do seguinte:  

- O tamanho da máquina virtual deve suportar várias placas de rede. Para saber mais sobre quais tamanhos de máquina virtual oferecem suporte a várias placas de rede, leia os artigos de [tamanhos de máquina virtual do Windows Server](../virtual-machines/virtual-machines-windows-sizes.md) ou [tamanhos de máquina virtual Linux](../virtual-machines/virtual-machines-linux-sizes.md) .   
- A máquina virtual deve ser criada com pelo menos duas NICs. Se a máquina virtual é criada com apenas uma NIC, mesmo se o tamanho da máquina virtual dá suporte a mais de um, você não pode anexar NICs adicionais para a máquina virtual após sua criação. Desde que a máquina virtual foi criada com pelo menos duas NICs, você pode anexar NICs adicionais para a máquina virtual após sua criação, desde que o tamanho de máquina virtual dá suporte a mais de duas NICs.  
- Você pode desconectar NICs secundárias (NIC primária não pode ser desanexado) de uma máquina virtual se a máquina virtual tiver pelo menos três NICs anexadas a ele. Você não é possível desanexar NICs se a máquina virtual tem dois ou menos NICs anexado a ele.  
- A ordem das NICs de dentro da VM será aleatória e também poderia alterar em atualizações de infraestrutura Azure. No entanto, os endereços IP e ethernet correspondente MAC endereços, permanecerá igual. Por exemplo, suponha que o sistema operacional identifica NIC1 Azure como Eth1. Eth1 tem o endereço IP 10.1.0.100 e endereço de MAC 00-0D-3A-B0-39-0D. Depois que uma infraestrutura Azure atualizar e reinicializar, o sistema operacional agora pode identificar NIC1 Azure como Eth2, mas os endereços IP e MAC serão iguais a como estavam quando o sistema operacional identificado NIC1 Azure como Eth1. Quando uma reinicialização for iniciado pelo cliente, a ordem NIC permanecerá igual dentro do sistema operacional.  
- Se a máquina virtual for um membro de um [conjunto de disponibilidade](../azure-glossary-cloud-terminology.md#availability-set), todas as VMs dentro do conjunto de disponibilidade devem ter uma única NIC ou várias placas de rede. Se as VMs tem várias placas de rede, o número cada tiverem não precisa ser o mesmo, desde que cada máquina virtual tiver pelo menos duas NICs.

## <a name="next-steps"></a>Próximas etapas

- Saiba como criar uma máquina virtual com uma única NIC lendo o artigo [criar uma máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .
- Saiba como criar uma máquina virtual com várias placas de rede lendo o artigo de [implantar uma máquina virtual com vários NIC](virtual-network-deploy-multinic-arm-ps.md) .
- Saiba como criar uma NIC com várias configurações de IP lendo o artigo de [endereços IP vários para Azure máquinas virtuais](virtual-network-multiple-ip-addresses-powershell.md) .
