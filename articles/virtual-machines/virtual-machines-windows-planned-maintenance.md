<properties
    pageTitle="Manutenção planejada para Windows VMs | Microsoft Azure"
    description="Entender quais Azure manutenção planejada é e como ele afeta sua máquinas virtuais do Windows em execução no Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="drewm"
    manager="timlt"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/26/2016"
    ms.author="drewm"/>

# <a name="planned-maintenance-for-virtual-machines-in-azure"></a>Manutenção planejada para máquinas virtuais no Azure


Compreenda qual a manutenção planejada Azure e como ele pode afetar a disponibilidade de suas máquinas virtuais do Windows. Este artigo também está disponível para [máquinas virtuais Linux](virtual-machines-linux-planned-maintenance.md). 

Este artigo fornece um plano de fundo como para o processo de manutenção planejada Azure. Se você estiver querendo solucione por que sua máquina virtual reiniciado, você pode [ler o blog post detalha exibindo máquina virtual reinicializar logs](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="why-azure-performs-planned-maintenance"></a>Por que o Azure realiza manutenção planejada

Microsoft Azure periodicamente executa atualizações em todo o mundo para melhorar a confiabilidade, o desempenho e a segurança da infraestrutura host que serve como base para máquinas virtuais. Muitas dessas atualizações são executadas sem nenhum impacto ao seu máquinas virtuais ou serviços de nuvem, incluindo atualizações preservação de memória.

No entanto, algumas atualizações exigem uma reinicialização para suas máquinas virtuais para aplicar as atualizações necessárias na infraestrutura. As máquinas virtuais são encerradas enquanto podemos patch a infraestrutura e, em seguida, as máquinas virtuais são reiniciadas.

Observe que há dois tipos de manutenção que podem afetar a disponibilidade de suas máquinas virtuais: planejadas e. Esta página descreve como o Microsoft Azure executa manutenção planejada. Para obter mais informações sobre a manutenção planejada, consulte [compreender planejado versus manutenção planejada](virtual-machines-windows-manage-availability.md).

[AZURE.INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]
