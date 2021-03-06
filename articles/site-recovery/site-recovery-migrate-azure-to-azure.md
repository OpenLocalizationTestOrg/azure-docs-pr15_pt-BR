<properties
    pageTitle="Migrar máquinas virtuais de Azure IaaS de uma região Azure para outro com recuperação de Site | Microsoft Azure"
    description="Use a recuperação de Site do Azure para migrar máquinas virtuais de Azure IaaS de uma região Azure para outro."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/21/2016"
    ms.author="raynew"/>

#  <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migrar máquinas virtuais de Azure IaaS entre regiões Azure com recuperação de Site do Azure

## <a name="overview"></a>Visão geral

Bem-vindo ao Site Azure recuperação! Utilize este artigo se você quiser migrar VMs do Azure entre regiões Azure. Antes de começar, observe que:

- Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: Gerenciador de recursos do Azure e clássico. Azure também tem dois portais – o portal de clássico Azure que suporta o modelo clássico de implantação e o portal do Azure com suporte para ambos os modelos de implantação. As etapas básicas para a migração são os mesmos se você estiver configurando a recuperação de Site no Gerenciador de recursos ou em clássico. No entanto, as instruções de interface do usuário e capturas de tela neste artigo são relevantes para o portal do Azure.
- **No momento você pode apenas migrar de uma região para outro. Você pode falhar sobre VMs de uma região Azure para outro, mas você não pode falhá-los novamente novamente.**
- As instruções de migração neste artigo são baseadas nas instruções para replicar uma máquina física no Azure. Ele inclui links para as etapas em [replicar VMs de VMware ou servidores físicos no Azure](site-recovery-vmware-to-azure.md), que descreve como replicar um servidor físico no portal do Azure.
- Se você estiver configurando a recuperação de Site no portal do clássico, siga as instruções detalhadas [neste](site-recovery-vmware-to-azure-classic.md)artigo. **Você não deve usar** as instruções neste [artigo herdado](site-recovery-vmware-to-azure-classic-legacy.md).

Poste quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prerequisites"></a>Pré-requisitos

Veja aqui o que é necessário para essa implantação:

- **Servidor de configuração**: uma VM local executando o Windows Server 2012 R2 que atua como o servidor de configuração. Você instala os outros componentes de recuperação do Site (incluindo o servidor de processo e destino mestre) nesta máquina virtual muito. Leia mais em [arquitetura de cenário](site-recovery-vmware-to-azure.md#scenario-architecture) e [os pré-requisitos do servidor de configuração](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).
- **Máquinas virtuais de IaaS**: VMs o que você deseja migrar. Você pode migrar essas VMs tratando-los como máquinas físicas.

## <a name="deployment-steps"></a>Etapas de implantação

Esta seção descreve as etapas de implantação no novo portal do Azure. Se você precisar estas etapas de implantação para recuperação de Site no portal do clássico, consulte [Este artigo](site-recovery-vmware-to-azure-classic.md).

1. [Criar um cofre](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Implantar um servidor de configuração](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Depois que você já implantou o servidor de configuração, verifique que ele possa se comunicar com as VMs que você deseja migrar.
4. [Definir configurações de replicação](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Criar uma política de replicação e atribuir para o servidor de configuração.
5. [Instalar o serviço de mobilidade](site-recovery-vmware-to-azure.md#step-6-replication-application). Cada máquina virtual que você deseja proteger necessidades o serviço de mobilidade instalado. Este serviço envia dados para o servidor de processo. O serviço de mobilidade pode ser instalado manualmente ou enviado e instalado automaticamente pelo servidor processo quando proteção para a máquina virtual está habilitada. Regras de firewall nas VMs que você deseja migrar devem ser configuradas para permitir a instalação de envio desse serviço.
6. [Habilitar a replicação](site-recovery-vmware-to-azure.md#enable-replication). Habilite a replicação para as VMs que você deseja migrar. Você pode descobrir máquinas virtuais IaaS que você deseja migrar para o Azure usando o endereço IP privado das máquinas virtuais. Encontre esse endereço no painel máquina virtual do Azure. Quando você habilita replicação, defina o tipo de máquina de VMs como máquinas físicas.
7. [Executar um failover não planejado](site-recovery-failover.md#run-an-unplanned-failover). Depois de replicação inicial for concluída, você pode executar um failover não planejado uma região Azure para outro. Opcionalmente, você pode criar um plano de recuperação e executar um failover não planejado, para migrar várias máquinas virtuais entre regiões. [Saiba mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre outros cenários de replicação em [o que é recuperação de Site do Azure?](site-recovery-overview.md)
