<properties
   pageTitle="Máquina virtual reiniciar ou problemas de redimensionamento | Microsoft Azure"
   description="Solucionar problemas de implantação clássico com reinicialização ou redimensionando an existing Virtual Machine do Windows no Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-windows"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="required"
   ms.date="09/20/2016"
   ms.devlang="na"
   ms.author="delhan"/>

# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-windows-virtual-machine-in-azure"></a>Solucionar problemas de implantação clássico com reinicialização ou redimensionando an existing Virtual Machine do Windows no Azure

> [AZURE.SELECTOR]
- [Clássico](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
- [Gerenciador de recursos](../../virtual-machines-windows-restart-resize-error-troubleshooting.md)

Quando você tenta iniciar um parou Azure Máquina Virtual (VM) ou redimensionar uma VM existente do Azure, o erro comum que encontrar é uma falha de alocação. Esse erro ocorre quando o cluster ou a região não tem recursos disponíveis ou não oferece suporte para o tamanho de máquina virtual solicitado.
> [AZURE.IMPORTANT] Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de recursos e clássico](../../../resource-manager-deployment-model.md).  Este artigo aborda usando o modelo clássico de implantação. Recomendamos que mais novas implantações de usam o modelo do Gerenciador de recursos.

[AZURE.INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Logs de auditoria coletar

Para iniciar a solução de problemas, colete os logs de auditoria para identificar o erro associado com o problema.

No portal do Azure, clique em **Procurar** > **máquinas virtuais** > _máquina virtual Windows_ > **configurações** > **logs de auditoria**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: Erro ao iniciar uma máquina virtual parou

Você tenta iniciar uma máquina virtual parou mas obter uma falha de alocação.

### <a name="cause"></a>Causa

A solicitação para iniciar a máquina virtual parou deve ser tentado em cluster original que hospeda o serviço de nuvem. No entanto, o cluster não tem espaço livre disponível para atender à solicitação.

### <a name="resolution"></a>Resolução

* Criar um novo serviço de nuvem e associá-la em uma região ou uma rede virtual baseada em região, mas não é um grupo de afinidade.

* Exclua a máquina virtual parou.

* Recrie a máquina virtual no serviço de nuvem novo usando os discos.

* Inicie a máquina virtual criada novamente.

Se você receber um erro ao tentar criar um novo serviço de nuvem, tente novamente mais tarde ou alterar a região para o serviço de nuvem.

> [AZURE.IMPORTANT] O novo serviço de nuvem terá um novo nome e VIP, portanto, será necessário alterar essas informações para todas as dependências que usam essas informações para o serviço de nuvem existente.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: Erro ao redimensionar uma máquina virtual existente

Você tenta redimensionar uma máquina virtual existente e receber uma falha de alocação.

### <a name="cause"></a>Causa

A solicitação para redimensionar a máquina virtual deve ser tentado em cluster original que hospeda o serviço de nuvem. No entanto, o cluster não suporta o tamanho de máquina virtual solicitado.

### <a name="resolution"></a>Resolução

Reduzir o tamanho de máquina virtual solicitado e repita a solicitação de redimensionamento.

* Clique em **Procurar tudo** > **máquinas virtuais (clássico)** > _sua máquina virtual_ > **configurações** > **tamanho**. Para obter etapas detalhadas, consulte [redimensionar a máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).

Se não for possível reduzir o tamanho de máquina virtual, siga estas etapas:

  * Criar um novo serviço de nuvem, garantindo que ele não é vinculada a um grupo de afinidade e não associado a uma rede virtual que esteja vinculada a um grupo de afinidade.

  * Crie uma máquina virtual novo, tamanho maior nele.

Você pode consolidar todas as suas VMs no serviço de nuvem do mesmo. Se seu serviço de nuvem existente estiver associado uma rede virtual baseada em região, você pode conectar o novo serviço de nuvem à rede virtual existente.

Se o serviço de nuvem existente não está associado uma rede virtual baseada em região, em seguida, você precisa excluir as VMs no serviço de nuvem existente e recrie no novo serviço de nuvem do seus discos. No entanto, é importante lembrar que o novo serviço de nuvem terá um novo nome e VIP, portanto, será necessário atualizar esses para todas as dependências que atualmente usam essas informações para o serviço de nuvem existente.

## <a name="next-steps"></a>Próximas etapas

Se você encontrar problemas quando você cria uma nova VM do Windows no Azure, consulte [Solucionar problemas de implantação com a criação de uma nova máquina de virtual do Windows no Azure](../../virtual-machines-windows-troubleshoot-deployment-new-vm.md).
