<properties
    pageTitle="Recurso agrupa diretrizes | Microsoft Azure"
    description="Saiba mais sobre as diretrizes de design e implementação chaves para a implantação de grupos de recursos em serviços de infraestrutura Azure."
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

# <a name="azure-resource-group-guidelines"></a>Diretrizes de grupo de recursos do Azure

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

Este artigo aborda Noções básicas sobre como criar seu ambiente logicamente e agrupar todos os componentes em grupos de recursos.


## <a name="implementation-guidelines-for-resource-groups"></a>Diretrizes de implementação de grupos de recursos

Decisões:

- Você vai criar grupos de recursos pelos componentes de infraestrutura de núcleo ou pela implantação do aplicativo completo?
- Você precisa restringir o acesso a grupos de recursos usando controles de acesso baseado em função?

Tarefas:

- Defina o que infraestrutura componentes principais e dedicada grupos de recursos que você precisa.
- Examine como implementar o recurso Gerenciador de modelos para implantações consistentes e podem ser reproduzidos.
- Defina quais funções de acesso de usuário necessárias para controlar o acesso a grupos de recursos.
- Crie o conjunto de grupos de recursos usando a convenção de nomenclatura. Você pode usar o Azure CLI ou portal.


## <a name="resource-groups"></a>Grupos de recursos

No Azure, você logicamente agrupar recursos relacionados como contas de armazenamento, redes virtuais e VMs (máquinas virtuais) para implantar, gerenciar e mantê-las como uma única entidade. Essa abordagem torna mais fácil para implantar aplicativos mantendo todos os recursos relacionados juntos de uma perspectiva de gerenciamento, ou para conceder acesso a esse grupo de recursos a outras pessoas. Para obter uma compreensão mais abrangente de grupos de recursos, você pode ler a [Visão geral do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

Um recurso-chave para grupos de recursos é a capacidade de criar seu ambiente usando um arquivo JSON que declara o armazenamento, rede, e recursos de computação. Você também pode definir quaisquer configurações para aplicar ou scripts personalizados relacionados. Usando esses modelos JSON, você pode criar implantações consistentes e pode ser reproduzidas para seus aplicativos. Essa abordagem permite que você criar um ambiente de desenvolvimento e, em seguida, use o mesmo modelo para criar uma implantação de produção, ou vice-versa. Para melhor compreensão sobre como usar modelos, leia [o passo a passo de modelo](../resource-manager-template-walkthrough.md) que orienta você pelas etapas de criação de um modelo de JSON.

Há duas abordagens diferentes que você pode realizar durante a criação de seu ambiente com grupos de recursos:

- Grupos de recursos para cada implantação de aplicativo que combina a contas de armazenamento, redes virtuais e sub-redes, VMs, carregar balanceadores, etc.
- Grupos de recursos centralizado que contêm suas contas de rede e sub-redes ou armazenamento de virtual do core. Seus aplicativos são, em seguida, em seus próprios grupos de recursos que contenham apenas VMs, balanceadores de carga, interfaces de rede, etc.

Conforme você escala, criar grupos de recursos centralizado para sua rede e sub-redes virtual torna mais fácil criar entre locais conexões para opções de conectividade híbrido de rede. A abordagem alternativa é para cada aplicativo tem sua própria rede virtual que requer configuração e manutenção. [Controles de acesso baseado em função](../active-directory/role-based-access-control-what-is.md) oferece uma maneira granular para controlar o acesso a grupos de recursos. Para aplicativos de produção, você pode controlar os usuários que podem acessar esses recursos ou para os recursos de infraestrutura de núcleo, você pode limitar apenas os engenheiros de infraestrutura para trabalhar com eles. Os proprietários de aplicativo tem acesso somente a componentes do aplicativo dentro de seu grupo de recursos e não o núcleo Azure infraestrutura do seu ambiente. Ao projetar seu ambiente, considere os usuários que precisam de acesso aos recursos e os grupos de recursos de design adequadamente. 


## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 