<properties
   pageTitle="Solucionar problemas de implantação de Linux VM-RM | Microsoft Azure"
   description="Solucionar problemas de implantação do Gerenciador de recursos quando você cria uma nova máquina virtual Linux no Azure"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="JiangChen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue, azure-resource-manager"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/09/2016"
  ms.author="cjiang"/>

# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Solucionar problemas de implantação do Gerenciador de recursos com a criação de uma nova máquina virtual Linux no Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Logs de auditoria coletar

Para iniciar a solução de problemas, colete os logs de auditoria para identificar o erro associado com o problema. Os links a seguir contêm informações detalhadas sobre o processo para acompanhar.

[Solucionar problemas de implantações de grupo de recursos com o Portal do Azure](../resource-manager-troubleshoot-deployments-portal.md)

[Operações de auditoria com o Gerenciador de recursos](../resource-group-audit.md)

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Se o sistema operacional é Linux generalizado, e ele é carregado e/ou capturado com a configuração de generalizado, não haverá erros. Da mesma forma, se o sistema operacional é Linux especializado e ele é carregado e/ou capturado com a configuração de especializado, não haverá erros.

**Carregar erros:**

**N<sup>1</sup>:** Se o sistema operacional é Linux generalizado e carregá-lo como um especializado, você receberá um erro de tempo limite de provisionamento porque a máquina virtual está preso no estágio de provisionamento.

**N<sup>2</sup>:** Se o sistema operacional é Linux especializado, e ele é carregado como generalizado, você receberá um erro de falha de provisionamento porque a máquina virtual novo está em execução com o nome do computador original, nome de usuário e senha.

**Resolução:**

Para resolver os dois esses erros, carregue o VHD original, disponível no local, com a mesma configuração que para o sistema operacional (generalizado/especializado). Para carregar generalizado, se lembrar de executar - desprovisionamento primeiro.

**Capture erros:**

**N<sup>3</sup>:** Se o sistema operacional é Linux generalizado, e ele é capturado como especializada, você receberá um erro de tempo limite de provisionamento porque a máquina virtual original não é útil conforme ela é marcada como generalizado.

**N<sup>4</sup>:** Se o sistema operacional é Linux especializado, e ele é capturado como generalizado, você receberá um erro de falha de provisionamento porque a máquina virtual novo está em execução com o nome do computador original, nome de usuário e senha. Além disso, o original máquina virtual não é útil porque ele está marcado como especializados.

**Resolução:**

Para resolver os dois esses erros, exclua a imagem atual do portal e [recuperar dos VHDs atuais](virtual-machines-linux-capture-image.md) com a mesma configuração que para o sistema operacional (generalizado/especializado).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problema: Personalizado / Galeria / imagem marketplace; Falha de alocação
Este erro surge em situações quando a nova solicitação de máquina virtual é fixada um cluster que não oferece suporte para o tamanho de máquina virtual que está sendo solicitado, ou não tem espaço livre disponível para acomodar a solicitação.

**Causar 1:** O cluster não oferece suporte para o tamanho de máquina virtual solicitado.

**Resolução 1:**

- Repita a solicitação usando um tamanho menor de máquina virtual.
- Se o tamanho da máquina virtual solicitada não pode ser alterado:
  - Pare todas as VMs no conjunto de disponibilidade.
  Clique em **grupos de recursos** > *seu grupo de recursos* > **recursos** > *definir sua disponibilidade* > **máquinas virtuais** > *sua máquina virtual* > **Parar**.
  - Depois de parar de todas as VMs, crie a máquina virtual novo no tamanho desejado.
  - Iniciar a máquina virtual novo primeiro, selecione cada uma das VMs parou e clique em **Iniciar**.

**Causa 2:** O cluster não tem recursos gratuitos.

**Resolução 2:**

- Repita a solicitação mais tarde.
- Se a máquina virtual novo pode ser parte de um conjunto diferente de disponibilidade
  - Crie uma nova VM em uma disponibilidade diferentes definir (na mesma região).
  - Adicione a máquina virtual novo à mesma rede virtual.

## <a name="next-steps"></a>Próximas etapas
Se você encontrar problemas quando você inicia uma VM Linux parou ou redimensionar uma VM Linux existente no Azure, consulte [Gerenciador de recursos de solucionar problemas de implantação com reinicialização ou redimensionando um computador de Virtual Linux existente no Azure](virtual-machines-linux-restart-resize-error-troubleshooting.md).
