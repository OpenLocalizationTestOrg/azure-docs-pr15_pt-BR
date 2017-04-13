<properties
   pageTitle="Como marcar uma máquina virtual Linux | Microsoft Azure"
   description="Saiba mais sobre uma máquina virtual do Linux criada no Azure usando o modelo de implantação do Gerenciador de recursos de marcação."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Como marcar uma máquina virtual Linux no Azure

Este artigo descreve maneiras diferentes para marcar uma máquina virtual Linux no Azure por meio do modelo de implantação do Gerenciador de recursos. Marcas são pares de chave/valor definido pelo usuário que podem ser colocados diretamente em um recurso ou um grupo de recursos. Azure atualmente suporta até 15 marcas por recurso e de grupo de recursos. Marcas podem ser colocadas em um recurso no momento da criação ou adicionadas a um recurso existente. Observe que marcas têm suporte para recursos criados por meio do modelo de implantação do Gerenciador de recursos somente.

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Marcação com CLI Azure

Para começar, [instalar e configurar o CLI do Azure](../xplat-cli-azure-resource-manager.md) e verifique se você está no modo do Gerenciador de recursos (`azure config mode arm`).

Você pode exibir todas as propriedades para uma determinada máquina Virtual, incluindo as marcas, usando este comando:

        azure vm show -g MyResourceGroup -n MyTestVM

Para adicionar uma nova marca de máquina virtual através da CLI do Azure, você pode usar o `azure vm set` comando junto com o parâmetro de marca **-t**:

        azure vm set -g MyResourceGroup -n MyTestVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Para remover todas as marcas, você pode usar o parâmetro **– T** na `azure vm set` comando.

        azure vm set – g MyResourceGroup –n MyTestVM -T


Agora que estamos aplicou marcas para nossos recursos CLI do Azure e o Portal, vamos dar uma olhada os detalhes de uso para ver as marcas no portal de cobrança.

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a marcação de seus recursos Azure, consulte [Visão geral do Gerenciador de recursos do Azure][] e [Usando marcas para organizar seus recursos do Azure][].
* Para ver como marcas podem ajudá-lo a gerenciar o uso dos recursos do Azure, consulte [Noções básicas sobre sua fatura do Azure][] e [obtenha ideias para seu consumo de recursos do Microsoft Azure][].





[Azure CLI environment]: ./xplat-cli-azure-resource-manager.md
[Visão geral do Gerenciador de recursos do Azure]: ../azure-resource-manager/resource-group-overview.md
[Usando marcas para organizar seus recursos do Azure]: ../resource-group-using-tags.md
[Noções básicas sobre sua fatura Azure]: ../billing/billing-understand-your-bill.md
[Obter ideias para seu consumo de recursos do Microsoft Azure]: ../billing-usage-rate-card-overview.md
