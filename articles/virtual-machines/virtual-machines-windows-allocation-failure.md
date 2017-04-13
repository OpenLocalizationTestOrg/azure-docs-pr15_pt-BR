<properties
    pageTitle="Solução de problemas de falhas de alocação de máquina virtual do Windows | Microsoft Azure"
    description="Solucionar problemas de falhas de alocação quando você cria, reinicie ou redimensionar uma máquina de virtual do Windows no Azure"
    services="virtual-machines-windows, azure-resource-manager"
    documentationCenter=""
    authors="JiangChen79"
    manager="felixwu"
    editor=""
    tags="top-support-issue,azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/02/2016"
    ms.author="cjiang"/>

# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-windows-vms-in-azure"></a>Solucionar problemas de falhas de alocação quando você cria, reinicie ou redimensionar VMs do Windows no Azure

Quando você cria uma máquina virtual, reinicie parou VMs (liberadas) ou redimensionar uma máquina virtual, o Microsoft Azure aloca recursos de computação à sua assinatura. Ocasionalmente, você poderá receber erros ao executar essas operações – mesmo antes de você atingir os limites de assinatura Azure. Este artigo explica as causas de algumas das falhas de alocação comuns e sugere possíveis correções. As informações também podem ser útil quando você planejar a implantação dos seus serviços. Você também pode [Solucionar problemas de falhas de alocação quando você cria, reinicia ou redimensionar VMs Linux no Azure](virtual-machines-linux-allocation-failure.md).

[AZURE.INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]
