<properties
   pageTitle="Máquina virtual reiniciar ou problemas de redimensionamento | Microsoft Azure"
   description="Solucionar problemas de implantação do Gerenciador de recursos com reinicialização ou redimensionando an existing Virtual Machine do Windows no Azure"
   services="virtual-machines-windows, azure-resource-manager"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-windows"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-windows"
   ms.devlang="na"
   ms.workload="required"
   ms.date="09/09/2016"
   ms.author="delhan"/>

# <a name="troubleshoot-resource-manager-deployment-issues-with-restarting-or-resizing-an-existing-windows-virtual-machine-in-azure"></a>Solucionar problemas de implantação do Gerenciador de recursos com reinicialização ou redimensionando an existing Virtual Machine do Windows no Azure

Quando você tenta iniciar um parou Azure Máquina Virtual (VM) ou redimensionar uma VM existente do Azure, o erro comum que encontrar é uma falha de alocação. Esse erro ocorre quando o cluster ou a região não tem recursos disponíveis ou não oferece suporte para o tamanho de máquina virtual solicitado.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Logs de auditoria coletar

Para iniciar a solução de problemas, colete os logs de auditoria para identificar o erro associado com o problema. Os links a seguir contêm informações detalhadas sobre o processo:

[Solução de problemas de implantações de grupo de recursos com o Portal do Azure](../resource-manager-troubleshoot-deployments-portal.md)

[Operações de auditoria com o Gerenciador de recursos](../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: Erro ao iniciar uma máquina virtual parou

Você tenta iniciar uma máquina virtual parou mas obter uma falha de alocação.

### <a name="cause"></a>Causa

A solicitação para iniciar a máquina virtual parou deve ser tentado em cluster original que hospeda o serviço de nuvem. No entanto, o cluster não tem espaço livre disponível para atender à solicitação.

### <a name="resolution"></a>Resolução

*   Parar todas as VMs no conjunto de disponibilidade e reinicie cada máquina virtual.

  1. Clique em **grupos de recursos** > _seu grupo de recursos_ > **recursos** > _definir sua disponibilidade_ > **máquinas virtuais** > _sua máquina virtual_ > **Parar**.

  2. Depois de parar de todas as VMs, selecione cada uma das VMs parou e clique em Iniciar.

*   Repita a solicitação de reiniciar mais tarde.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: Erro ao redimensionar uma máquina virtual existente

Você tenta redimensionar uma máquina virtual existente e receber uma falha de alocação.

### <a name="cause"></a>Causa

A solicitação para redimensionar a máquina virtual deve ser tentado em cluster original que hospeda o serviço de nuvem. No entanto, o cluster não suporta o tamanho de máquina virtual solicitado.

### <a name="resolution"></a>Resolução

* Repita a solicitação usando um tamanho menor de máquina virtual.

* Se o tamanho da máquina virtual solicitada não pode ser alterado:

  1. Pare todas as VMs no conjunto de disponibilidade.

    * Clique em **grupos de recursos** > _seu grupo de recursos_ > **recursos** > _definir sua disponibilidade_ > **máquinas virtuais** > _sua máquina virtual_ > **Parar**.

  2. Depois de parar de todas as VMs, redimensione a máquina virtual desejada para um tamanho maior.
  3. Selecione a máquina virtual redimensionada e clique em **Iniciar**, inicie cada uma das VMs parou.

## <a name="next-steps"></a>Próximas etapas

Se você encontrar problemas quando você cria uma nova VM do Windows no Azure, consulte [Solucionar problemas de implantação com a criação de uma nova máquina de virtual do Windows no Azure](../virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md).
