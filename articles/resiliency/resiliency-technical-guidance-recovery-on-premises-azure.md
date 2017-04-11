<properties
   pageTitle="Orientações técnicas: recuperação do local para o Azure | Microsoft Azure"
   description="Artigo sobre a compreensão e a recuperação de projetar sistemas de infraestrutura de locais no Azure"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-on-premises-to-azure"></a>Orientações técnicas resiliência Azure: recuperação do local para o Azure

Azure fornece um conjunto abrangente de serviços para habilitar a extensão de um data center de locais no Azure para fins de recuperação de desastres e disponibilidade altas:

* __Rede__: com uma rede virtual privada, você com segurança estender sua rede local para a nuvem.
* __Calcular__: clientes que usam o Hyper-V local podem "tire e shift" máquinas virtuais (VMs) existentes no Azure.
* __Armazenamento__: StorSimple estende seu sistema de arquivos ao armazenamento do Azure. O serviço do Azure Backup fornece backup de arquivos e bancos de dados SQL ao armazenamento do Azure.
* __Replicação de banco de dados__: com SQL Server 2014 (ou posterior) grupos de disponibilidade, você pode implementar alta disponibilidade e recuperação de desastres para os seus dados locais.

##<a name="networking"></a>Rede

Você pode usar a rede Virtual do Azure para criar uma seção logicamente isolada no Azure e com segurança conectá-lo ao seu centro de dados locais ou uma única máquina cliente usando uma conexão de IPsec. Com uma rede Virtual, você pode aproveitar a infraestrutura de scalable, sob demanda no Azure fornecendo conectividade com dados e aplicativos no local, incluindo sistemas em execução no Windows Server, mainframes e UNIX. Consulte a [documentação de rede do Azure](../virtual-network/virtual-networks-overview.md) para obter mais informações.

##<a name="compute"></a>Calcular

Se você estiver usando o Hyper-V no local, você pode "tire e shift" existente máquinas virtuais do Azure e formatos executando o Windows Server 2012 (ou posterior), sem fazer alterações para a máquina virtual ou conversão de máquina virtual de provedores de serviço. Para obter mais informações, consulte [sobre discos e VHDs para Azure máquinas virtuais](../virtual-machines/virtual-machines-linux-about-disks-vhds.md).

##<a name="azure-site-recovery"></a>Recuperação de Site Azure

Se você quiser recuperação de dados como um serviço (DRaaS), o Azure fornece [Recuperação de Site do Azure](https://azure.microsoft.com/services/site-recovery/). Recuperação de Site Azure oferece proteção abrangente para VMware, os servidores Hyper-V e físicas. Com o Azure recuperação do Site, você pode usar outro servidor de local ou Azure como seu site de recuperação. Para obter mais informações sobre a recuperação de Site do Azure, consulte a [documentação de recuperação de Site do Azure](https://azure.microsoft.com/documentation/services/site-recovery/).

##<a name="storage"></a>Armazenamento

Há várias opções para usar o Azure como um site de backup de dados locais.

###<a name="storsimple"></a>StorSimple

StorSimple com segurança e transparente integra armazenamento em nuvem para aplicativos de local. Ele também oferece um único dispositivo que oferece local hierárquico de alto desempenho e armazenamento em nuvem, arquivamento ao vivo, proteção de dados baseado em nuvem e recuperação de dados. Para obter mais informações, consulte a [página de produto do StorSimple](https://azure.microsoft.com/services/storsimple/).

###<a name="azure-backup"></a>Backup Azure

Backup Azure permite backups de nuvem usando as ferramentas conhecidas do backup no Windows Server 2012 (ou posterior), Windows Server 2012 Essentials (ou posterior) e Gerenciador de proteção de dados do System Center 2012 (ou posterior). Essas ferramentas fornecem um fluxo de trabalho para gerenciamento de backup que seja independente dos locais de armazenamento dos backups, se um disco local ou o armazenamento do Azure. Depois que o backup dos dados para a nuvem, usuários autorizados podem facilmente recuperar backups para qualquer servidor.

Com backups incrementais, somente as alterações aos arquivos são transferidas para a nuvem. Isso ajuda a usar o espaço de armazenamento, reduzir o consumo de largura de banda e suportar a recuperação no momento de várias versões dos dados com eficiência. Você também pode optar por usar recursos adicionais, como as políticas de retenção de dados, compactação de dados e a otimização de transferência de dados. Usando o Azure como o local de backup tem a vantagem de óbvia que os backups são automaticamente "externo". Isso elimina os requisitos adicionais para proteger a mídia de backup no local.

Para obter mais informações, consulte [o que é o Backup do Azure?](../backup/backup-introduction-to-azure-backup.md) e [Configurar o Backup do Azure para dados DPM](https://technet.microsoft.com/library/jj728752.aspx).

##<a name="database"></a>Banco de dados

Você pode ter uma solução de recuperação para seus bancos de dados do SQL Server em um ambiente híbrido TI usando grupos de disponibilidade sempre ativado, espelhamento de banco de dados, envio de log e backup e restauração com armazenamento de Blob do Azure. Todas essas soluções usam o SQL Server em máquinas virtuais do Azure em execução.

Grupos de disponibilidade sempre ativado pode ser usados em um ambiente de TI híbrida onde réplicas de banco de dados existem ambos os locais e na nuvem. Isso é mostrado no diagrama a seguir.

![Grupos de disponibilidade do SQL Server AlwaysOn em uma arquitetura de nuvem híbrida](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-3.png)

Espelhamento de banco de dados também pode abranger servidores locais e na nuvem em uma instalação baseada em certificado. O diagrama a seguir ilustra este conceito.

![Espelhamento de banco de dados do SQL Server em uma arquitetura de nuvem híbrida](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-4.png)

O envio de log pode ser usado para sincronizar um banco de dados local com um banco de dados do SQL Server em uma máquina virtual Azure.

![Envio em uma arquitetura de nuvem híbrida de log do SQL Server](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-5.png)

Por fim, você pode fazer backup um banco de dados local diretamente ao armazenamento de Blob do Azure.

![Fazer backup do SQL Server para armazenamento de Blob do Azure em uma arquitetura de nuvem híbrida](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-6.png)

Para obter mais informações, consulte [alta disponibilidade e recuperação de desastres para SQL Server no Azure máquinas virtuais](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md) e [Backup e restauração para SQL Server no Azure máquinas virtuais](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).

##<a name="checklists-for-on-premises-recovery-in-microsoft-azure"></a>Listas de verificação de recuperação de locais no Microsoft Azure

###<a name="networking"></a>Rede

  1. Examine a seção de rede deste documento.
  2. Use uma rede Virtual para conectar com segurança locais na nuvem.

###<a name="compute"></a>Calcular

  1. Examine a seção de computação deste documento.
  2. Reposicionar VMs entre Hyper-V e Azure.

###<a name="storage"></a>Armazenamento

  1. Examine a seção de armazenamento deste documento.
  2. Tire proveito dos serviços de StorSimple para usar o armazenamento em nuvem.
  3. Use o serviço de Backup do Azure.

###<a name="database"></a>Banco de dados

  1. Examine a seção de banco de dados deste documento.
  2. Considere o uso do SQL Server em VMs Azure como o backup.
  3. Configure grupos de disponibilidade sempre ativado.
  4. Configure o espelhamento de banco de dados baseado em certificado.
  5. Use o envio de log.
  6. Fazer backup de bancos de dados local para armazenamento de Blob do Azure.

##<a name="next-steps"></a>Próximas etapas

Este artigo é parte de uma série voltada para [orientações técnicas resiliência Azure](./resiliency-technical-guidance.md). Próximo artigo desta série é a [recuperação de corrupção de dados ou exclusões acidentais](./resiliency-technical-guidance-recovery-data-corruption.md).