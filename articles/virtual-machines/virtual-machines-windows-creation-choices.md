<properties
    pageTitle="Maneiras diferentes de criar uma máquina virtual Windows | Microsoft Azure"
    description="Lista as diferentes maneiras de criar uma máquina virtual Windows com o Gerenciador de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure-services"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="different-ways-to-create-a-windows-virtual-machine-with-resource-manager"></a>Diferentes maneiras de criar uma máquina virtual Windows com o Gerenciador de recursos

Azure oferece maneiras diferentes para criar uma máquina virtual porque máquinas virtuais são adequadas para fins e usuários diferentes. Isso significa que você precisa fazer algumas opções sobre a máquina virtual e como criá-lo. Este artigo fornece um resumo dessas opções e links para instruções.

## <a name="azure-portal"></a>Portal do Azure

Usando o portal de Azure é uma maneira simples para experimentar uma máquina virtual, especialmente se você estiver apenas começando com o Azure. 

[Criar uma máquina virtual executando o Windows usando o portal](virtual-machines-windows-hero-tutorial.md)

## <a name="template"></a>Modelo

Máquinas virtuais exigem uma combinação de recursos (como uma disponibilidade conjuntos e contas de armazenamento). Em vez de implantação e gerenciamento de cada recurso separadamente, você pode criar um modelo do Gerenciador de recursos do Azure que implante e provisiona todos os recursos em uma operação única e coordenado.

- [Criar uma máquina virtual Windows com um modelo do Gerenciador de recursos](virtual-machines-windows-ps-template.md)


## <a name="azure-powershell"></a>PowerShell Azure

Se você preferir trabalhando em um shell de comando, você pode usar o PowerShell do Azure.

- [Criar uma máquina de virtual do Windows usando o PowerShell](virtual-machines-windows-ps-create.md)


## <a name="visual-studio"></a>O Visual Studio

Use o Visual Studio para criar, gerenciar e implantar VMs com as ferramentas do Azure para Visual Studio e o SDK do Azure.

[Ferramentas Azure para Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)

