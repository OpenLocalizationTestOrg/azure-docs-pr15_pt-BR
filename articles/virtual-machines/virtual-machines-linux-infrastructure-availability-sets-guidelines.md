<properties
    pageTitle="Disponibilidade definir diretrizes | Microsoft Azure"
    description="Saiba mais sobre as diretrizes de design e implementação chaves para implantar conjuntos de disponibilidade em serviços de infraestrutura Azure."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="availability-sets-guidelines"></a>Diretrizes de conjuntos de disponibilidade

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

Este artigo aborda Noções básicas sobre as etapas de planejamento necessárias para conjuntos de disponibilidade garantir que seu permanece aplicativos acessíveis durante planejados ou eventos.

## <a name="implementation-guidelines-for-availability-sets"></a>Diretrizes de implementação para conjuntos de disponibilidade

Decisões:

- Quantos conjuntos de disponibilidade você precisa para as várias funções e níveis em sua infraestrutura de aplicativo?

Tarefas:

- Defina o número de VMs em cada camada de aplicativo que você precisa.
- Determine se você precisa ajustar o número de falhas ou atualizar domínios a ser usado para seu aplicativo.
- Definir os conjuntos de disponibilidade necessários usando seu convenção de nomenclatura e quais residem VMs neles. Uma máquina virtual só pode residem em disponibilidade de um conjunto. 

## <a name="availability-sets"></a>Conjuntos de disponibilidade

No Azure, máquinas virtuais (VMs) podem ser colocadas em para um agrupamento lógico denominado um conjunto de disponibilidade. Quando você cria VMs dentro de um conjunto de disponibilidade, plataforma Windows Azure distribui o posicionamento dessas VMs entre a infraestrutura subjacente. Deve haver um evento de manutenção planejada a plataforma Windows Azure ou um hardware subjacente / falha de infraestrutura, o uso de conjuntos de disponibilidade garante que pelo menos uma VM permanecerá em execução.

Como prática recomendada, aplicativos não devem residem em uma única VM. Um conjunto de disponibilidade que contém uma única VM não obter qualquer proteção de eventos planejadas ou no plataforma Windows Azure. O [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines) requer dois ou mais VMs dentro de uma disponibilidade configurado para permitir a distribuição de VMs entre a infraestrutura subjacente.

A infraestrutura subjacente no Azure é dividida em atualizar domínios e falhas. Esses domínios são definidos por quais hosts compartilhar um ciclo de atualização de comuns ou compartilhar infraestrutura de física semelhante como power e uma rede. Azure distribui automaticamente suas VMs dentro de uma disponibilidade definido em domínios para manter a disponibilidade e tolerância. Dependendo do tamanho do seu aplicativo e o número de VMs dentro de um conjunto de disponibilidade, você pode ajustar o número de domínios que você deseja usar. Você pode ler mais sobre como [Gerenciar disponibilidade e uso de domínios de atualização e falhas](virtual-machines-linux-manage-availability.md).

Ao criar sua infraestrutura de aplicativo, você também deve planejar out as camadas do aplicativo para usar. Define VMs de grupo que têm a mesma finalidade na disponibilidade, como uma disponibilidade definida para suas VMs front-end executando nginx ou Apache. Crie uma disponibilidade separada definir para suas VMs de back-end executando MongoDB ou MySQL. O objetivo é garantir que cada componente do seu aplicativo está protegido por um conjunto de disponibilidade e pelo menos uma vez instância sempre permanece em execução.

Balanceadores de carga podem ser utilizados na frente de cada camada de aplicativo para trabalhar junto com um conjunto de disponibilidade e certifique-se de que o tráfego sempre poderão ser roteado para uma instância em execução. Sem um balanceador de carga, suas VMs podem continuar sendo executado durante eventos de manutenção planejadas e, mas os usuários finais não poderá resolvê-los, se a máquina virtual primária não estiver disponível.


## <a name="next-steps"></a>Próximas etapas
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 