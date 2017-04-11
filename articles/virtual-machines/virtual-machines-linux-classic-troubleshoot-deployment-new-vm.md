<properties
   pageTitle="Solucionar problemas de implantação de Linux VM-clássico | Microsoft Azure"
   description="Solucionar problemas de implantação clássico quando você cria uma nova máquina virtual Linux no Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="JiangChen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/06/2016"
  ms.author="cjiang"/>

# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Solucionar problemas de implantação clássico com a criação de uma nova máquina virtual Linux no Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-selectors-include.md)]

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Logs de auditoria coletar

Para iniciar a solução de problemas, colete os logs de auditoria para identificar o erro associado com o problema.

No portal do Azure, clique em **Procurar** > **máquinas virtuais** > *máquina virtual Windows* > **configurações** > **logs de auditoria**.

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Se o sistema operacional é Linux generalizado, e ele é carregado e/ou capturado com a configuração de generalizado, não haverá erros. Da mesma forma, se o sistema operacional é Linux especializado e ele é carregado e/ou capturado com a configuração de especializado, não haverá erros.

**Carregar erros:**

**N<sup>1</sup>:** Se o sistema operacional é Linux generalizado e carregá-lo como um especializado, você receberá um erro de tempo limite de provisionamento porque a máquina virtual está preso no estágio de provisionamento.

**N<sup>2</sup>:** Se o sistema operacional é Linux especializado, e ele é carregado como generalizado, você receberá um erro de falha de provisionamento porque a máquina virtual novo está em execução com o nome do computador original, nome de usuário e senha.

**Resolução:**

Para resolver os dois esses erros, carregue o VHD original, disponível no local, com a mesma configuração que para o sistema operacional (generalizado/especializado). Para carregar generalizado, se lembrar de executar - desprovisionamento primeiro. Para obter mais informações, consulte [criar e carregar um disco rígido Virtual que contém o sistema operacional Linux](virtual-machines-linux-classic-create-upload-vhd.md) .

**Capture erros:**

**N<sup>3</sup>:** Se o sistema operacional é Linux generalizado, e ele é capturado como especializada, você receberá um erro de tempo limite de provisionamento porque a máquina virtual original não é útil conforme ela é marcada como generalizado.

**N<sup>4</sup>:** Se o sistema operacional é Linux especializado, e ele é capturado como generalizado, você receberá um erro de falha de provisionamento porque a máquina virtual novo está em execução com o nome do computador original, nome de usuário e senha. Além disso, o original máquina virtual não é útil porque ele está marcado como especializados.

**Resolução:**

Para resolver os dois esses erros, exclua a imagem atual do portal e [recuperar dos VHDs atuais](virtual-machines-linux-classic-capture-image.md) com a mesma configuração que para o sistema operacional (generalizado/especializado).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problema: Personalizado / Galeria / imagem marketplace; Falha de alocação
Este erro surge em situações quando a nova solicitação de máquina virtual é enviada para um cluster que não tem espaço livre disponível para acomodar a solicitação, ou não oferece suporte para o tamanho de máquina virtual que está sendo solicitado. Não é possível misturar diferentes séries de VMs no mesmo serviço de nuvem. Portanto, se você deseja criar uma nova VM da qual seu serviço de nuvem pode oferecer suporte a um tamanho diferente, a solicitação de computação falhará.

Dependendo das restrições de serviço de nuvem que você usar para criar a máquina virtual novo, talvez você encontre um erro causado por uma das duas situações.

**Causar 1:** O serviço de nuvem é fixado um cluster específico ou ele está vinculado a um grupo de afinidade e fixado, portanto, um cluster específico por design. Portanto novo recurso de computação solicitações nesse grupo de afinidade são tentou no mesmo cluster que hospedam os recursos existentes. No entanto, mesmo cluster talvez não suportam o tamanho de máquina virtual solicitado tanto tem espaço suficiente, resultando em um erro de alocação. Isso é verdadeiro se os novos recursos são criados por meio de um novo serviço de nuvem ou um serviço de nuvem existente.

**Resolução 1:**

- Criar um novo serviço de nuvem e associá-la a uma região ou em uma rede virtual baseada em região.
- Crie uma nova VM no novo serviço de nuvem.
  Se você receber um erro ao tentar criar um novo serviço de nuvem, tente novamente mais tarde ou alterar a região para o serviço de nuvem.

> [AZURE.IMPORTANT] Se você estava tentando criar uma nova VM em um serviço de nuvem existente, mas não foi possível e tinha que criar um novo serviço de nuvem para sua máquina virtual novo, você pode optar por consolidar todas as suas VMs no serviço de nuvem do mesmo. Para fazer isso, exclua as VMs no serviço de nuvem existente e recuperá-los dos seus discos do novo serviço de nuvem. No entanto, é importante lembrar que o novo serviço de nuvem terá um novo nome e VIP, portanto, será necessário atualizar esses para todas as dependências que atualmente usam essas informações para o serviço de nuvem existente.

**Causa 2:** O serviço de nuvem está associado uma rede virtual que esteja vinculada a um grupo de afinidade, para que ele é fixado um cluster específico por design. Todos os novos recursos de computação solicitações nesse grupo de afinidade, portanto, são tentou no mesmo cluster que hospedam os recursos existentes. No entanto, mesmo cluster talvez não suportam o tamanho de máquina virtual solicitado tanto tem espaço suficiente, resultando em um erro de alocação. Isso é verdadeiro se os novos recursos são criados por meio de um novo serviço de nuvem ou um serviço de nuvem existente.

**Resolução 2:**

- Crie uma nova rede virtual regional.
- Crie a máquina virtual novo na nova rede virtual.
- [Conectar sua rede virtual existente](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) para a nova rede virtual. Consulte mais informações sobre [redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/). Como alternativa, você pode [migrar sua rede virtual afinidade baseado em grupo com uma rede virtual regional](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)e crie a máquina virtual novo.

## <a name="next-steps"></a>Próximas etapas
Se você encontrar problemas quando você inicia uma VM Linux parou ou redimensionar uma VM Linux existente no Azure, consulte [Solucionar problemas de implantação clássico com reinicialização ou redimensionando um computador de Virtual Linux existente no Azure](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md).
