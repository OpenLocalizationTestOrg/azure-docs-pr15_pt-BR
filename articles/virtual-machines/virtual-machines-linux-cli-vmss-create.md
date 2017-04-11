<properties
    pageTitle="O que são escala de máquina virtual define? | Microsoft Azure"
    description="Saiba mais sobre conjuntos de escala de máquina virtual."
    keywords="computador de virtual Linux, escala de máquina virtual define" 
    services="virtual-machines-linux"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/24/2016"
    ms.author="gatneil"/>

# <a name="what-are-virtual-machine-scale-sets"></a>O que são escala de máquina virtual define?

Conjuntos de escala de máquina virtual permitem que você gerencie várias VMs como um conjunto. Em um alto nível, conjuntos de escala têm os seguintes prós e contras:

Profissionais:

1. Alta disponibilidade. Cada conjunto de escala coloca seus VMs em um conjunto de disponibilidade com 5 falhas domínios (FDs) e 5 atualização (UDs) para assegurar a disponibilidade (para obter mais informações sobre FDs e UDs, ver a [disponibilidade de máquina virtual](./virtual-machines-linux-manage-availability.md)). 
2. Fácil integração com balanceador de carga do Azure e Gateway do aplicativo.
3. Fácil integração com escala automática do Azure.
4. Simplifica a implantação, gerenciamento e limpar de VMs.
5. Suporte a tipos comuns do Windows e Linux, bem como imagens personalizadas.

Desvantagens:

1. Não é possível anexar discos de dados para instâncias de máquina virtual em um conjunto de escala. Em vez disso, deve usar o armazenamento de Blob, arquivos do Azure, tabelas do Azure ou outra solução de armazenamento.

## <a name="quick-create-using-azure-cli"></a>Criar rápido usando CLI do Azure

[AZURE.INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter informações gerais, confira a [página inicial principal conjuntos de escala](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Para obter mais documentação, confira a [página de escala principal documentação define](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

Por exemplo, modelos de Gerenciador de recursos usando conjuntos de escala, procurar por "vmss" nos [modelos de início rápido do Azure github repo](https://github.com/Azure/azure-quickstart-templates).

