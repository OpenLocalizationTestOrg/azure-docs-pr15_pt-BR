<properties
    pageTitle="Matriz de suporte de recuperação de Site Azure | Microsoft Azure"
    description="Resume os sistemas operacionais compatíveis e componentes para recuperação de Site do Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="azure-site-recovery-support-matrix"></a>Matriz de suporte do Azure recuperação do Site

Este artigo resume componentes para implantações de recuperação de Site do Azure e sistemas operacionais compatíveis. Há uma lista de componentes com suporte e os pré-requisitos para cada cenário de implantação em cada o artigo correspondente na implantação e este documento resume-los.

## <a name="supported-operating-systems-for-virtualization-servers"></a>Sistemas operacionais compatíveis para servidores de virtualização


**Fonte** | **Destino** | **Sistema operacional de host**
---|---|---|--- 
**Hosts Hyper-V (sem VMM)** | Azure | Windows Server 2012 R2 com atualizações mais recentes
**Hosts Hyper-V (com VMM)** | Azure | Windows Server 2012 R2 com atualizações mais recentes
**Hosts Hyper-V (com VMM)** | Site VMM secundário | Pelo menos o Windows Server 2012 com atualizações mais recentes
**VMware hosts/vCenter** | Azure | vCenter 5.5 ou 6.0 (suporte para 5,5 recursos somente) <br/><br/> vSphere 6.0, 5.5 ou 5.1 com atualizações mais recentes
**VMware hosts/vCenter** | Site de VMware secundário | vCenter 5.5 ou 6.0 (suporte para 5,5 recursos somente) <br/><br/> vSphere 6.0, 5.5 ou 5.1 com atualizações mais recentes

## <a name="supported-requirements-for-replicated-machines"></a>Requisitos de compatíveis para máquinas replicados

**Fonte** | **O que é replicado** | **Destino** | **Sistema operacional de host**
---|---|---|--- 
**VMs Hyper-V** | Qualquer carga de trabalho | Azure | Qualquer sistema operacional de convidado [suportados pelo Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> VMs devem atender aos [requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMs Hyper-V (com VMM)** | Qualquer carga de trabalho | Azure | Qualquer sistema operacional de convidado [suportados pelo Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> VMs devem atender aos [requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMs Hyper-V (com VMM)** | Qualquer carga de trabalho | Site VMM secundário | Qualquer sistema operacional de convidado [suportado pelo Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)
**VMs VMware** | Qualquer carga de trabalho em execução na máquina virtual do Windows | Azure | 64 bits Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 com no mínimo SP1<br/><br/> VMs devem atender aos [requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMs VMware** | Qualquer carga de trabalho em execução no Linux VM | Azure | Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> CentOS 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 executando o núcleo compatível Red Hat ou o Unbreakable Enterprise núcleo versão 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Armazenamento necessário: arquivo system (EXT3, ETX4, ReiserFS, XFS); Vários caminhos software dispositivo mapeador (vários caminhos)); Gerenciador de volume: (LVM2). Servidores físicos com o armazenamento de controlador HP CCISS não são suportados. O sistema de arquivos ReiserFS é suportado apenas em SUSE Linux Enterprise Server 11 SP3.<br/><br/> VMs devem atender aos [requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMs VMware** | Qualquer carga de trabalho em execução na máquina virtual do Windows | Site de VMware secundário | 64 bits Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 com no mínimo SP1
**VMs VMware** | Qualquer carga de trabalho em execução no Linux VM | Site de VMware secundário | Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> CentOS 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 executando o núcleo compatível Red Hat ou o Unbreakable Enterprise núcleo versão 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Armazenamento necessário: arquivo system (EXT3, ETX4, ReiserFS, XFS); Vários caminhos software dispositivo mapeador (vários caminhos)); Gerenciador de volume: (LVM2). Servidores físicos com o armazenamento de controlador HP CCISS não são suportados. O sistema de arquivos ReiserFS é suportado apenas em SUSE Linux Enterprise Server 11 SP3.
**Servidores físicos** | Qualquer carga de trabalho em execução no Windows | Azure | 64 bits Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 com no mínimo SP1
**Servidores físicos** | Qualquer carga de trabalho em execução no Linux | Azure | Red Hat Enterprise Linux 6.7,7.1,7.2 <br/><br/> CentOS 6.5, 6.6,6.7,7.0,7.1,7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 executando o núcleo compatível Red Hat ou o Unbreakable Enterprise núcleo versão 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Armazenamento necessário: arquivo system (EXT3, ETX4, ReiserFS, XFS); Vários caminhos software dispositivo mapeador (vários caminhos)); Gerenciador de volume: (LVM2). Servidores físicos com o armazenamento de controlador HP CCISS não são suportados. O sistema de arquivos ReiserFS é suportado apenas em SUSE Linux Enterprise Server 11 SP3.
**Servidores físicos** | Qualquer carga de trabalho em execução no Windows | Site secundário | 64 bits Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 com no mínimo SP1
**Servidores físicos** | Qualquer carga de trabalho em execução no Linux | Site secundário | Red Hat Enterprise Linux 6.7,7.1,7.2 <br/><br/> CentOS 6.5, 6.6,6.7,7.0,7.1,7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 executando o núcleo compatível Red Hat ou o Unbreakable Enterprise núcleo versão 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Armazenamento necessário: arquivo system (EXT3, ETX4, ReiserFS, XFS); Vários caminhos software dispositivo mapeador (vários caminhos)); Gerenciador de volume: (LVM2). Servidores físicos com o armazenamento de controlador HP CCISS não são suportados. O sistema de arquivos ReiserFS é suportado apenas em SUSE Linux Enterprise Server 11 SP3.


## <a name="provider-versions"></a>Versões de provedor

**Nome** | **Descrição** | **Versão mais recente** | **Suporte** | **Detalhes**
---|---|---|---| ---
**Provedor de recuperação de Site Azure** | Coordenadas comunicações entre servidores de local e o site do Azure/secundário <br/><br/> Instalado em servidores VMM locais ou servidores Hyper-V se há servidor VMM | 5.1.1700 (disponível a partir do portal) | [Recursos e correções mais recentes](https://support.microsoft.com/kb/3155002)
**Recuperação de Site Azure Unified configuração (VMware no Azure)** | Coordenadas comunicações entre servidores de VMware locais e do Azure <br/><br/> Instalado em servidores de VMware de local | 9.3.4246.1 (disponível a partir do portal) | [Recursos e correções mais recentes](https://support.microsoft.com/kb/3155002)
**Serviço de mobilidade** | Replicação de coordenadas entre servidores de servidores/física VMware local e o site do Azure/secundário | DISP (disponível a partir do portal) | Instalado em cada VM VMware ou servidor físico que você deseja duplicar. **Agente de serviços de recuperação de Azure da Microsoft (MARTE)** | Replicação de coordenadas entre VMs Hyper-V e do Azure<br/><br/> Instalado em servidores de Hyper-V local (com ou sem um servidor VMM) | 2.0.8689.0 (disponível a partir do portal) | Este agente é usado pelo Backup do Azure e recuperação de Site de Azure). [Mais] (https://support.microsoft.com/en-us/kb/2997692)

## <a name="next-steps"></a>Próximas etapas

[Preparar para implantação](site-recovery-best-practices.md)

