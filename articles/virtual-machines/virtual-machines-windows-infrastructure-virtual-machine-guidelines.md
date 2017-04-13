<properties
    pageTitle="Diretrizes de máquinas virtuais do Windows | Microsoft Azure"
    description="Saiba mais sobre as diretrizes de design e implementação chaves para implantar o máquinas virtuais do windows Azure"
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="virtual-machines-guidelines"></a>Diretrizes de máquinas virtuais

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

Este artigo aborda Noções básicas sobre as etapas de planejamento necessárias para criar e gerenciar máquinas virtuais (VMs) em seu ambiente Azure.

## <a name="implementation-guidelines-for-vms"></a>Diretrizes de implementação para VMs
Decisões:

- Quantos VMs é necessária para vários níveis de aplicativos e componentes de sua infraestrutura?
- Quais recursos de CPU e memória cada máquina virtual precisa e quais são os requisitos de armazenamento?

Tarefas:

- Defina as cargas de trabalho de seu aplicativo e os recursos que exigem as VMs.
- Alinhe as solicitações de recursos para cada máquina virtual com o tipo de armazenamento e tamanho de máquina virtual apropriado.
- Defina os grupos de recursos para os diferentes níveis e componentes de sua infraestrutura.
- Defina a convenção de nomenclatura de máquina virtual.
- Crie suas VMs usando o PowerShell do Azure, web portal, ou com os modelos do Gerenciador de recursos.

## <a name="virtual-machines"></a>Máquinas virtuais

Um dos componentes principais no seu ambiente Azure é provavelmente VMs. Isso é onde você executa seus aplicativos bancos de dados, serviços de autenticação, etc.

É importante entender os [tamanhos diferentes de máquina virtual](virtual-machines-windows-sizes.md) para dimensionar corretamente seu ambiente de uma perspectiva de custo e desempenho. Se suas VMs não tiver cores de CPU ou memória suficiente, o desempenho do seu aplicativo é afetado, independentemente de como ele é projetado e desenvolvido. Examine as cargas de trabalho sugeridas para cada série de máquina virtual como ponto de partida como decidir qual máquina virtual para usar para cada componente em sua infraestrutura de tamanho. Você pode [alterar o tamanho de uma máquina virtual](https://azure.microsoft.com/blog/resize-virtual-machines/) após a implantação.

Armazenamento desempenha um papel fundamental no desempenho de máquina virtual. Você pode usar o armazenamento de Premium ou armazenamento padrão, que usa discos girando regulares, para cargas de trabalho de i/o altas e desempenho de pico, que usa SSD discos. Como com o tamanho de máquina virtual, lá estão custo considerações para selecionar a mídia de armazenamento. Você pode ler o [artigo de diretrizes de infraestrutura de armazenamento](virtual-machines-windows-infrastructure-storage-solutions-guidelines.md) para compreender como projetar armazenamento apropriado para um desempenho ideal das suas VMs.


## <a name="resource-groups"></a>Grupos de recursos
Componentes como VMs logicamente são agrupados para facilitar o gerenciamento e a manutenção usando [Grupos de recursos do Azure](../azure-resource-manager/resource-group-overview.md). Usando grupos de recursos, você pode criar, gerenciar e monitorar todos os recursos que compõem um determinado aplicativo. Você também pode implementar [controles de acesso baseado em função](../active-directory/role-based-access-control-what-is.md) para conceder acesso a outras pessoas dentro de sua equipe somente os recursos que necessitam. Levar tempo para planejar seus grupos de recursos e atribuições de função. Há diferentes abordagens realmente projetar e implementar grupos de recursos, então certifique-se de ler o [artigo de diretrizes de grupos de recursos](virtual-machines-windows-infrastructure-resource-groups-guidelines.md) para entender melhor como construir suas VMs.


## <a name="templates"></a>Modelos 
Você pode criar modelos, definidos pela declarativos arquivos JSON para criar suas VMs. Modelos normalmente também criar o armazenamento necessário rede, interfaces de rede, endereçamento IP, etc. junto com as VMs próprios. Use modelos para criar ambientes consistentes, pode ser reproduzidos para desenvolvimento e teste fins facilmente replicar ambientes de produção e vice-versa. Você pode ler mais sobre como [criar e usar modelos](../azure-resource-manager/resource-group-overview.md#template-deployment) para entender como você pode usá-los para criar e implantar suas VMs.


## <a name="next-steps"></a>Próximas etapas
[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 