<properties 
   pageTitle="Como migrar de grupos de afinidade com uma rede Virtual regionais (VNet)"
   description="Saiba como migrar de grupos de afinidade para vnets regionais"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-migrate-from-affinity-groups-to-a-regional-virtual-network-vnet"></a>Como migrar de grupos de afinidade com uma rede Virtual regionais (VNet)

Você pode usar um grupo de afinidade para garantir que os recursos criados dentro do mesmo grupo de afinidade física são hospedados por servidores que estejam próximos uns dos outros, permitindo que esses recursos para se comunicar mais rápido. No passado, grupos de afinidade foram um requisito para a criação de redes virtuais (VNets). Nesse momento, o serviço de Gerenciador de rede que gerenciados VNets poderia só funcionam dentro de um conjunto de servidores físicos ou unidade de escala. Melhorias na arquiteturais tem aumentado o escopo de gerenciamento de rede para uma região.

Como resultado esses aperfeiçoamentos arquitetônicos, grupos de afinidade não é mais recomendados ou obrigatório para redes virtuais. O uso de grupos de afinidade para VNets está sendo substituído por regiões. VNets que estão associadas a regiões são chamadas VNets regionais.

Além disso, é recomendável que você não usa grupos de afinidade em geral. Além do requisito de VNet, grupos de afinidade também foram importantes usar para garantir que recursos, como computação e armazenamento, foram colocados próximas umas das outras. No entanto, com a arquitetura de rede Azure atuais, esses requisitos de posicionamento não são mais necessários. Consulte [grupos de afinidade e VMs](#Affinity-groups-and-VMs) para algumas das restantes casos específicos em que talvez você queira usar um grupo de afinidade.

## <a name="creating-and-migrating-to-regional-vnets"></a>Criando e migrando para VNets regionais

Ao prosseguir, ao criar novas VNets, use a *região*. Você verá isto como uma opção no Portal de gerenciamento. Observe que no arquivo de configuração de rede, isso mostra como *local*.

>[AZURE.IMPORTANT] Embora seja ainda tecnicamente possível criar uma rede virtual que está associada um grupo de afinidade, não há nenhum motivo convincente para fazê-lo. Vários novos recursos, como grupos de segurança de rede, só estão disponíveis ao usar um VNet regional e não estão disponíveis para redes virtuais que estão associadas a grupos de afinidade.

### <a name="about-vnets-currently-associated-with-affinity-groups"></a>Sobre VNets atualmente associados a grupos de afinidade

VNets que estão atualmente associadas a grupos de afinidade estão habilitados para a migração para VNets regionais. Para migrar para um VNet regional, siga estas etapas:

1. Exporte o arquivo de configuração de rede. Você pode usar o PowerShell ou o Portal de gerenciamento. Para obter instruções sobre como usar o Portal de gerenciamento, consulte [configurar sua VNet usando um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md).

1. Edite o arquivo de configuração de rede, substituindo os valores antigos com os novos valores. 

    > [AZURE.NOTE] O **local** é a região que você especificou para o grupo de afinidade que está associado a sua VNet. Por exemplo, se sua VNet estiver associado um grupo de afinidade que está localizado no Oeste EUA, quando você migrar, seu local deve apontar para Oeste EUA. 
    
    Edite as seguintes linhas no seu arquivo de configuração de rede, substituindo os valores com seu próprio: 

    **Valor antigo:** \<VirtualNetworkSitename = AffinityGroup "VNetUSWest" = "VNetDemoAG"\> 

    **Novo valor:** \<VirtualNetworkSitename = local "VNetUSWest" = "Oeste EUA"\>

1. Salve suas alterações e [Importar](virtual-networks-using-network-configuration-file.md) a configuração de rede do Azure.

>[AZURE.NOTE] Essa migração não causa qualquer tempo de inatividade para seus serviços.

## <a name="affinity-groups-and-vms"></a>Grupos de afinidade e VMs

Conforme mencionado anteriormente, grupos de afinidade não está mais geralmente são recomendados para VMs. Você deve usar um grupo de afinidade somente quando um conjunto de VMs deve ter a latência de rede mais baixa absoluta entre VMs. Colocando VMs em um grupo de afinidade, VMs serão todos colocadas na mesma computação cluster ou escala de unidade.

É importante observar que usar um grupo de afinidade pode ter dois, possivelmente negativo consequências:

- O conjunto de tamanhos de máquina virtual serão limitado ao conjunto de tamanhos de máquina virtual oferecidas pela unidade de escala de computação.

- Não há maior probabilidade de impossibilidade de alocar uma nova VM. Isso acontece quando a unidade de escala específicas para o grupo de afinidade estiver fora do capacidade.

### <a name="what-to-do-if-you-have-a-vm-in-an-affinity-group"></a>O que fazer se você tiver uma máquina virtual em um grupo de afinidade

VMs que estão atualmente em um grupo de afinidade não precisam ser removido do grupo de afinidade.

Quando uma máquina virtual é implantada, ele é implantado em uma unidade de escala único. Grupos de afinidade podem restringir o conjunto de tamanhos de máquina virtual disponíveis para uma nova implantação de máquina virtual, mas qualquer máquina virtual existente que é implantado já está restrito ao conjunto de tamanhos de máquina virtual disponíveis na unidade de escala no qual a máquina virtual é implantada. Por isso, a remoção de uma máquina virtual do grupo de afinidade não terá efeito.
 
