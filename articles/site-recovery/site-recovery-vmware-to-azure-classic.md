<properties
    pageTitle="Duplicar máquinas virtuais VMware e servidores físicos para Azure com recuperação de Site do Azure | Microsoft Azure"
    description="Este artigo descreve como implantar o Azure recuperação do Site para coordenar replicação, failover e recuperação de máquinas virtuais VMware a locais e Windows/Linux servidores físicos no Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery"></a>Duplicar máquinas virtuais VMware e servidores físicos para Azure com recuperação de Site do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmware-to-azure.md)
- [Portal clássico](site-recovery-vmware-to-azure-classic.md)
- [Portal clássico (herdado)](site-recovery-vmware-to-azure-classic-legacy.md)


O serviço de recuperação de Site do Azure contribui para sua estratégia de recuperação (BCDR) de desastre e continuidade de negócios por coordenação replicação, failover e recuperação de máquinas virtuais e servidores físicos. Máquinas podem ser replicadas para Azure ou uma central de dados local secundário. Para obter uma visão geral rápida leia [o que é recuperação de Site do Azure?](site-recovery-overview.md).

## <a name="overview"></a>Visão geral

Este artigo descreve como:

- **VMware replicar máquinas virtuais do Azure**— implantar recuperação de Site para coordenar replicação, failover e recuperação de máquinas virtuais VMware a local para armazenamento do Azure.
- **Duplicar servidores físicos para Azure**— implantar a recuperação do Site do Azure para coordenar replicação, failover e recuperação de local físicos servidores Windows e Linux no Azure.

>[AZURE.NOTE] Este artigo descreve como duplicar para Azure. Se você quiser replicar VMs VMware ou servidores físicos Windows/Linux em um data center secundário, siga as instruções [neste](site-recovery-vmware-to-vmware.md)artigo.

Poste quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="enhanced-deployment"></a>Implantação aprimorados

Este artigo inclui contém instruções para uma implantação aprimorada no portal do Azure clássico. Recomendamos que você use esta versão de todas as implantações de novo. Se você já tiver implantado usando a versão anterior de herdado recomendamos que você migrar para a nova versão. Leia [mais](site-recovery-vmware-to-azure-classic-legacy.md##migrate-to-the-enhanced-deployment) sobre a migração.

A implantação avançada é uma atualização importante. Aqui está um resumo das melhorias fizemos:

- **Nenhuma infraestrutura VMs no Azure**: replica dados diretamente a uma conta de armazenamento do Azure. Além para replicação e failover não é necessário configurar qualquer infraestrutura VMs (servidor de configuração, servidor de destino mestre) conforme necessário, podemos na implantação herdada.  
- **Instalação unificada**: uma única instalação fornece configuração simples e escalabilidade para componentes de local.
- **Proteger implantação**: todo o tráfego está criptografado e comunicações de gerenciamento de replicação são enviadas pela HTTPS 443.
- **Pontos de recuperação**: pontos de suporte a falha e recuperação consistente com aplicativos para ambientes Windows e Linux e máquina virtual e multi-máquina virtual configurações consistentes de único de suporte.
- **Testar failover**: suporte para teste interrupções failover Azure, sem afetar a produção ou pausando replicação.
- **Failover não planejado**: suporte para failover planejado Azure com uma opção avançada para desligar VMs automaticamente antes do failover.
- **Failback**: failback integrado que replica apenas as alterações delta volta para o site local.
- **vSphere 6.0**: suporte limitado para implantações de VMware Vsphere 6.0.


## <a name="how-does-site-recovery-help-protect-virtual-machines-and-physical-servers"></a>Como o Site recuperação ajudar a proteger máquinas virtuais e servidores físicos?


- Administradores de VMware podem configurar proteção externa no Azure das cargas de trabalho de negócios e aplicativos executados em máquinas virtuais VMware. Gerentes de servidor podem replicar física servidores locais do Windows e Linux no Azure.
- O console de recuperação de Site do Azure fornece um local único para configuração simples e o gerenciamento de replicação, failover e processos de recuperação.
- Se você replica máquinas virtuais VMware que são gerenciadas por um servidor vCenter, recuperação do Site pode descobrir essas VMs automaticamente. Se os computadores estiverem em um host ESXi recuperação de Site descobre VMs do host.
- Execute failovers fácil sua infraestrutura local do Azure e failback (restauração) do Azure para servidores de VM VMware no site local.
- Configure planos de recuperação que agrupar cargas de trabalho de aplicativos que são hierárquico em várias máquinas. Você pode falhar sobre esses planos, e a recuperação de Site oferece consistência de multi-máquina virtual para que os computadores que executam as mesmas cargas de trabalho podem ser recuperados juntos em um ponto de dados consistentes.


## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

### <a name="windows64-bit-only"></a>Windows (somente 64 bits)
- Windows Server 2008 R2 SP1 +
- Windows Server 2012
- Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux (somente 64 bits)
- Red Hat Enterprise Linux 6.7, 7.1, 7.2
- CentOS 6.5, 6.6, 6.7, 7.0, 7.1, 7.2
- Oracle Enterprise Linux 6.4, 6.5 executando o núcleo compatível Red Hat ou o Unbreakable Enterprise núcleo versão 3 (UEK3)
- SUSE Linux Enterprise Server 11 SP3

## <a name="scenario-architecture"></a>Arquitetura de cenário

Componentes de cenário:

- **Um servidor de gerenciamento de local**: O servidor de gerenciamento executa componentes de recuperação do Site:
    - **Servidor de configuração**: coordenadas comunicação e gerencia processos de replicação e a recuperação de dados.
    - **Servidor processo**: atua como um gateway de replicação. Ele recebe dados de máquinas de origem protegido, otimizá-lo com o cache, compactação e criptografia e envia dados de replicação para armazenamento do Azure. Também trata instalação de envio do serviço de mobilidade máquinas protegida e executa a descoberta automática de VMs VMware.
    - **Servidor de destino mestre**: lida com dados de replicação durante o failback do Azure.
    Você também pode implantar um servidor de gerenciamento que atua como um servidor de processo apenas, para dimensionar a sua implantação.
- **Serviço de mobilidade o**: este componente está implantado em cada máquina (VM VMware ou servidor físico) que você deseja duplicar para Azure. Ele captura gravações de dados na máquina e encaminha para o servidor de processo.
- **Azure**: você não precisa criar qualquer VMs do Azure para lidar com replicação e failover. O serviço de recuperação de Site lida com gerenciamento de dados e replica os dados diretamente para o armazenamento do Azure. Replicado Azure VMs são giradas automaticamente para cima somente quando houver falha no Azure. No entanto, se você quiser falhar volta do Azure para o site local você precisará configurar uma máquina virtual do Azure para agir como um servidor de processo.


O gráfico mostra como esses componentes interagem.

![arquitetura](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**Figura 1: VMware/física para Azure** (criada por Henry Robalino)


## <a name="capacity-planning"></a>Planejamento da capacidade

Quando você estiver planejando capacidade, veja o que você precisa pensar sobre:

- **Ambiente de origem**— planejamento da capacidade ou os requisitos de máquina de origem e infraestrutura de VMware.
- **O servidor de gerenciamento**— planejamento para os servidores de gerenciamento de locais que executar componentes de recuperação do Site.
- **Largura de banda de rede de origem para destino**-planejamento de largura de banda de rede necessária para replicação entre origem e Azure

### <a name="source-environment-considerations"></a>Considerações sobre ambiente de origem

- **Taxa de alteração de máxima diariamente**— uma máquina protegida só pode usar um servidor de processo e um servidor de um único processo pode lidar com até 2 TB de alteração de dados por dia. Portanto, a 2 TB é que o máximo de dados diário alterar taxa que tem suporte para uma máquina protegida.
- **Produtividade máximo**— uma máquina replicada pode pertencer a uma conta de armazenamento no Azure. Uma conta de armazenamento padrão pode lidar com um máximo de 20.000 solicitações por segundo e recomendamos que você mantenha o número de IOPS em um computador de origem a 20.000. Por exemplo se você tiver uma máquina de origem com 5 discos e cada disco gera 120 IOPS (tamanho de 8K) na origem será dentro do Azure limite IOPS de disco de 500 por. O número de contas de armazenamento obrigatórias = fonte total IOPs/20000.


### <a name="management-server-considerations"></a>Considerações de gerenciamento de servidor

O servidor de gerenciamento executa componentes de recuperação do Site que manipular otimização de dados, replicação e gerenciamento. Ele deve ser capaz de lidar com a capacidade de taxa de alteração diária em todas as cargas de trabalho em execução em máquinas protegidas e tem largura de banda suficiente para replicar continuamente os dados para armazenamento do Azure. Especificamente:

- O servidor processo recebe dados de replicação de máquinas protegidas e otimiza-lo com o cache, compactação e criptografia antes de enviar para o Azure. O servidor de gerenciamento deve ter recursos suficientes para realizar essas tarefas.
- O servidor de processo usa cache baseado em disco. Recomendamos que um disco de cache separado de 600 GB ou mais para lidar com as alterações de dados armazenadas em caso de interrupção de afunilamento de rede. Durante a implantação, você pode configurar o cache em qualquer unidade que tenha pelo menos 5 GB de armazenamento disponível, mas 600 GB é a recomendação mínima.
- Como uma prática recomendada é recomendável que o servidor de gerenciamento estar localizado na mesma rede e segmento LAN como as máquinas que você deseja proteger. Ele pode estar localizado em uma rede diferente, mas máquinas que você deseja proteger devem ter visibilidade de rede L3-lo.

Recomendações de tamanho do servidor de gerenciamento são resumidas na tabela a seguir.

**Servidor de gerenciamento de CPU** | **Memória** | **Tamanho do cache de disco** | **Taxa de alteração de dados** | **Máquinas protegidas**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 cores @ 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | Implante um servidor de gerenciamento com estas configurações para replicar máquinas menor que 100.
12 vCPUs (2 sockets * 6 cores @ 2,5 GHz) | 18 GB | 600 GB | 500 GB a 1 TB | Implante um servidor de gerenciamento com estas configurações para replicar entre máquinas de 100-150.
16 vCPUs (2 sockets * 8 cores @ 2,5 GHz) | 32 GB | 1 TB | 1 TB a 2 TB | Implante um servidor de gerenciamento com estas configurações para replicar entre máquinas de 150-200.
Implantar outro servidor de processo | | | > 2 TB | Implante servidores de processo adicionais se você estiver replicar máquinas mais de 200, ou se alteram os dados diários taxa exceder 2 TB.

Onde:

- Cada máquina de origem é configurada com 3 discos de 100 GB.
- Usamos armazenamento benchmarking de 8 unidades SAS de 10 K RPM com RAID 10 para as medidas de disco do cache.

### <a name="network-bandwidth-from-source-to-target"></a>Largura de banda de fonte de destino
Verifique se que você calcular a largura de banda que seria necessária para replicação de delta usando a [ferramenta de Planejador de capacidade](site-recovery-capacity-planner.md) e replicação inicial

#### <a name="throttling-bandwidth-used-for-replication"></a>A otimização de largura de banda usada para replicação

Tráfego de VMware replicado para Azure passa por um servidor de processo específico. Você pode controlar a largura de banda que está disponível para replicação de recuperação do Site em que o servidor da seguinte maneira:

1. Abrir o snap-in de MMC de Backup do Microsoft Azure no servidor de gerenciamento principal ou em um servidor de gerenciamento executando adicionais provisionado servidores processo. Por padrão, um atalho para o Microsoft Azure Backup é criado na área de trabalho, ou você pode encontrá-lo em: Agent\bin\wabadmin de serviços de recuperação do C:\Program Files\Microsoft Azure.
2. O snap-in, clique em **Alterar propriedades**.

    ![Reduzir largura de banda](./media/site-recovery-vmware-to-azure-classic/throttle1.png)

3. Na guia **Throttling** Especifica a largura de banda que pode ser usada para replicação de recuperação do Site e o agendamento aplicável.

    ![Reduzir largura de banda](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

Opcionalmente, você também pode definir a otimização usando o PowerShell. Aqui está um exemplo:

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

#### <a name="maximizing-bandwidth-usage"></a>Maximizar o uso de largura de banda
Para aumentar a largura de banda utilizada para replicação pela recuperação de Site do Azure você precisa alterar uma chave do registro.

A seguinte chave controla o número de segmentos por replicação de disco que são usados ao replicar

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 Em uma rede "overprovisioned", essa chave do registro precisa ser alterado dos valores do padrão. Oferecemos suporte um máximo de 32.  


[Saiba mais](site-recovery-capacity-planner.md) sobre o planejamento de capacidade detalhada.

### <a name="additional-process-servers"></a>Servidores adicionais de processo

Se você precisa proteger mais de 200 máquinas ou taxa de alteração diária for maior que 2 TB que você pode adicionar servidores adicionais para lidar com a carga. Para dimensionar a você pode:

- Aumente o número de servidores de gerenciamento. Por exemplo, você pode proteger até 400 máquinas com dois servidores de gerenciamento.
- Adicionar servidores processo adicionais e usá-los para lidar com o tráfego em vez de (ou além) o servidor de gerenciamento.

Esta tabela descreve um cenário em que:

- Você pode configurar o servidor de gerenciamento original usá-lo como um servidor de configuração.
- Configurar um servidor de processo adicional.
- Configurar máquinas virtuais protegidas para usar o servidor de processo adicional.
- Cada máquina de origem protegido está configurada com três discos de 100 GB.

**Servidor de gerenciamento de original**<br/><br/>(servidor de configuração) | **Servidor processo adicionais**| **Tamanho do cache de disco** | **Taxa de alteração de dados** | **Máquinas protegidas**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 cores @ 2,5 GHz), 16 GB de memória | 4 vCPUs (2 sockets * 2 cores @ 2,5 GHz), 8 GB de memória | 300 GB | 250 GB ou menos | Você pode replicar máquinas 85 ou menos.
8 vCPUs (2 sockets * 4 cores @ 2,5 GHz), 16 GB de memória | 8 vCPUs (2 sockets * 4 cores @ 2,5 GHz), 12 GB de memória | 600 GB | 250 GB a 1 TB | Você pode replicar entre máquinas 85-150.
12 vCPUs (2 sockets * 6 cores @ 2,5 GHz), 18 GB de memória | 12 vCPUs (2 sockets * 6 cores @ 2,5 GHz) 24 GB de memória | 1 TB | 1 TB a 2 TB | Você pode replicar entre máquinas de 150-225.


A maneira na qual você dimensiona seus servidores será dependem de sua preferência para uma escala para cima ou dimensionar modelo.  Você crescerão Implantando alguns gerenciamento de ponta e servidores processo ou dimensionar implantando servidores mais com menos recursos. Por exemplo: se você precisar proteger 220 máquinas você poderia siga um destes procedimentos:

- Configure o servidor de gerenciamento original com 12vCPU, 18 GB de memória, um servidor de processo adicional com 12vCPU, 24 GB de memória e máquinas protegidas para usar somente o servidor de processo adicional.
- Ou, Alternativamente você pode configurar dois servidores de gerenciamento (2 x 8vCPU, 16 GB de RAM) e dois servidores de processo adicional (1 x 8vCPU) e 4vCPU x1 lidar com 135 + 85 máquinas (220) e configurar máquinas protegidas para usar apenas os servidores de processo adicional.


[Siga estas instruções](#deploy-additional-process-servers) para configurar um servidor de processo adicional.

## <a name="before-you-start-deployment"></a>Antes de começar a implantação

As tabelas resumem os pré-requisitos para implantar esse cenário.


### <a name="azure-prerequisites"></a>Pré-requisitos Azure

**Pré-requisito** | **Detalhes**
--- | ---
**Conta do Azure**| Você precisará de uma conta [Do Microsoft Azure](https://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação do Site.
**Armazenamento do Azure** | Você precisará de uma conta de armazenamento do Azure para armazenar dados duplicados. Dados replicados são armazenados no armazenamento do Azure e Azure VMs são giradas para cima quando houver falha. <br/><br/>Você precisa de uma [conta de armazenamento redundantes de localização geográfica padrão](../storage/storage-redundancy.md#geo-redundant-storage). A conta deve estar na mesma região como o serviço de recuperação de Site e ser associadas a mesma assinatura. Observe que a duplicação para contas de armazenamento premium atualmente não são compatíveis e não devem ser usados.<br/><br/>Não há suporte para a movimentação de contas de armazenamento criados usando o [novo portal Azure](../storage/storage-create-storage-account.md) em grupos de recursos. [Leia sobre](../storage/storage-introduction.md) Armazenamento do Azure.<br/><br/>
**Rede do Azure** | Você precisará de uma rede virtual Azure que Azure VMs se conectará quando houver falha. A rede virtual Azure deve estar na mesma região como o Cofre de recuperação do Site.<br/><br/>Observe que falha após o failover Azure será preciso uma VPN conexão (ou rota expressa do Azure) configurar de rede do Azure para o site local.


### <a name="on-premises-prerequisites"></a>Pré-requisitos de local

**Pré-requisito** | **Detalhes**
--- | ---
**Servidor de gerenciamento** | Você precisa de um servidor de Windows 2012 R2 do local em execução em um servidor físico ou máquina virtual. Todos os componentes de recuperação de Site local estão instalados neste servidor de gerenciamento<br/><br/> Recomendamos que você implanta o servidor como uma VM VMware altamente disponível. Failback para o site local do Azure é sempre a VMs VMware, independentemente se você falhar VMs ou servidores físicos. Se você não configurar o servidor de gerenciamento como uma VM VMware você precisará configurar um servidor destino mestre separado como uma VM VMware para receber o tráfego de failback.<br/><br/>O servidor não deve ser um controlador de domínio.<br/><br/>O servidor deve ter um endereço IP estático.<br/><br/>O nome de host do servidor deve ser 15 caracteres ou menos.<br/><br/>Localidade do sistema operacional deve ser somente em inglês.<br/><br/>O servidor de gerenciamento requer acesso à internet.<br/><br/>Você precisa de acesso de saída do servidor da seguinte maneira: acesso temporário em HTTP 80 durante a instalação dos componentes de recuperação do Site (para baixar MySQL); Acesso de saída em andamento na HTTPS 443 para gerenciamento de replicação; Acesso de saída em andamento na HTTPS 9443 tráfego de replicação (essa porta pode ser modificada)<br/><br/> Certificar-se de que essas URLs estão acessíveis do management server: <br/>- \*. hypervrecoverymanager.windowsazure.com<br/>- \*. accesscontrol.windows.net<br/>- \*. backup.windowsazure.com<br/>- \*. blob.core.windows.net<br/>- \*. store.core.windows.net<br/>-https://www.msftncsi.com/ncsi.txt<br/>- [https://dev.MySQL.com/Get/archives/MySQL-5.5/MySQL-5.5.37-Win32.msi]( https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi")<br/><br/>Se você tiver regras de firewall baseado em endereço IP no servidor, verifique se as regras permitiu que a comunicação com o Azure. Você precisará permitir que os [Intervalos de IP de data center do Azure](https://www.microsoft.com/download/details.aspx?id=41653) e a porta HTTPS (443). Você também precisará lista branca intervalos de endereços IP para a região Azure da sua assinatura e Oeste EUA. A URL [https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi") é para baixá-lo MySQL.
**VMware vCenter/ESXi host**: | Você precisa de um ou mais vMware vSphere ESX/ESXi hipervisores Gerenciando suas máquinas virtuais VMware, executando ESX/ESXi versão 6.0, 5.5 ou 5.1 com as atualizações mais recentes.<br/><br/> Recomendamos que você implantar um servidor de vCenter VMware para gerenciar seus hosts ESXi. Ele deve estar executando vCenter versão 6.0 ou 5.5 com as últimas atualizações.<br/><br/>Observe que a recuperação de Site não dá suporte a nova vCenter e vSphere 6.0 recursos como cross vCenter vMotion, volumes virtuais e armazenamento DRS. Suporte de recuperação de site está limitado aos recursos que também estavam disponíveis na versão 5.5.
**Máquinas protegido**: | **AZURE**<br/><br/>Máquinas que você deseja proteger devem estar em conformidade com [Azure pré-requisitos](site-recovery-best-practices.md#azure-virtual-machine-requirements) para a criação de VMs do Azure.<br><br/>Se você deseja se conectar VMs Azure após failover, em seguida, você precisará habilitar conexões de área de trabalho remota no firewall local.<br/><br/>Capacidade de disco individual em máquinas protegidas não deve ser mais de 1023 GB. Uma máquina virtual pode ter até 64 discos (portanto, até 64 TB). Se você tiver discos maiores que 1 TB convém o uso de replicação de banco de dados como o SQL Server sempre ligado ou protetor de dados do Oracle.<br/><br/>Mínimo 2 GB de espaço disponível na unidade de instalação para a instalação do componente.<br/><br/>Compartilhado convidado disco clusters não são suportados. Se você tiver uma implantação agrupada considere o uso de replicação de banco de dados como o SQL Server sempre ligado ou protetor de dados do Oracle.<br/><br/>Unified Extensible Firmware Interface (UEFI) / Extensible Firmware Interface(EFI) inicialização não é compatíveis.<br/><br/>Nomes de máquina devem conter entre 1 e 63 caracteres (letras, números e hifens). O nome deve começar com uma letra ou número e termina com uma letra ou número. Depois que uma máquina estiver protegida, você pode modificar o nome do Azure.<br/><br/>**VMs VMware**<br/><br>Você precisará instalar VMware vSphere PowerCLI 6.0. no servidor de gerenciamento (servidor de configuração).<br/><br/>VMs VMware que você deseja proteger deve ter ferramentas de VMware instalado e em execução.<br/><br/>Se a fonte máquina virtual tiver agrupamento NIC-é convertida em uma única NIC após failover no Azure.<br/><br/>Se VMs protegidas tem um disco iSCSI recuperação de Site converte o disco de iSCSI de máquina virtual protegido em um arquivo VHD quando a máquina virtual Falha ao longo do Azure. Se o destino iSCSI pode ser acessado pela máquina virtual do Azure ele será conectar ao destino iSCSI e consulte essencialmente dois discos – o disco VHD na máquina virtual do Azure e o disco de iSCSI de origem. Nesse caso, você precisará desconectar o destino iSCSI que aparece na Falha ao longo de máquina virtual do Azure.<br/><br/>[Saiba mais](#vmware-permissions-for-vcenter-access) sobre as permissões de usuário VMware que são necessárias para a recuperação do Site.<br/><br/> **MÁQUINAS do WINDOWS SERVER (no VM VMware ou servidor físico)**<br/><br/>O servidor deve estar executando um sistema operacional de 64 bits suportado: Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 com no mínimo SP1.<br/><br/>O sistema operacional deve ser instalado na unidade C:\ e o disco de sistema operacional deve ser um disco básico do Windows (sistema operacional não deve ser instalado em um disco dinâmico do Windows).<br/><br/>Para Windows Server 2008 R2 máquinas que você precisará ter o .NET Framework 3.5.1 instalados.<br/><br/>Você precisará fornecer uma conta de administrador (deve ser um administrador local na máquina Windows) para a instalação de envio o serviço de mobilidade em servidores Windows. Se a conta fornecida é uma conta de fora do domínio que você precisará desativar o controle de acesso de usuário remoto no computador local. [Saiba mais](#install-the-mobility-service-with-push-installation).<br/><br/>Recuperação de site oferece suporte a VMs com disco RDM.  Durante o failback recuperação do Site será reutilizar o disco RDM se o disco de máquina virtual e RDM de fonte original estiver disponível. Se eles não estão disponíveis, durante o failback recuperação de Site criará um novo arquivo VMDK para cada disco.<br/><br/>**MÁQUINAS LINUX**<br/><br/>Você precisará de um sistema operacional de 64 bits com suporte: Red Hat Enterprise Linux 6.7; CentOS 6.5, 6.6,6.7; Oracle Enterprise Linux 6.4, 6.5 executando o núcleo compatível Red Hat ou o Unbreakable Enterprise núcleo versão 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>arquivos de /etc/hosts em máquinas protegidas devem conter entradas que mapeiam o nome de host local para endereços IP associados a todos os adaptadores de rede. <br/><br/>Se você quiser se conectar a uma máquina virtual Azure executando Linux após failover usando um Secure Shell cliente (ssh), verifique se o serviço de Secure Shell na máquina protegida está definido para iniciar automaticamente na inicialização do sistema e que as regras de firewall permitem que um ssh conexão a ela.<br/><br/>Proteção só pode ser habilitada para máquinas Linux com o armazenamento a seguir: (EXT3, ETX4, ReiserFS, XFS); de sistema de arquivos Vários caminhos software dispositivo mapeador (vários caminhos)); Gerenciador de volume: (LVM2). Servidores físicos com o armazenamento de controlador HP CCISS não são suportados. O sistema de arquivos ReiserFS é suportado apenas em SUSE Linux Enterprise Server 11 SP3.<br/><br/>Recuperação de site oferece suporte a VMs com disco RDM.  Durante failback para Linux, recuperação de Site não reutilizar o disco RDM. Em vez disso, ele cria um novo arquivo VMDK para cada disco RDM correspondente.

Somente para Linux VM - Certifique-se de que você defina a configuração de disk.enableUUID=true nos parâmetros de configuração da máquina virtual do VMware. Se esta linha não existir, adicione-o. Isso é necessário para fornecer um UUID consistente para o VMDK para que ele montagens corretamente. Observe também que, sem essa configuração, failback causará um download completo, mesmo que a máquina virtual está disponível em prem. Adicionar essa configuração garantirá que somente as alterações delta sejam transferidas durante o failback.

## <a name="step-1-create-a-vault"></a>Etapa 1: Criar um cofre

1. Entre [Portal de gerenciamento](https://manage.windowsazure.com/).
2. Expanda **Serviços de dados** > **Serviços de recuperação** e clique em **Cofre de recuperação do Site**.
3. Clique em **Criar novo** > **criação rápida**.
4. Em **nome**, digite um nome amigável para identificar o cofre.
5. Na **região**, selecione a região geográfica para o cofre. Para verificar regiões com suporte consulte disponibilidade geográficos em [Detalhes de preços de recuperação do Azure Site](https://azure.microsoft.com/pricing/details/site-recovery/)
6. Clique em **criar cofre**.
    ![Novo cofre](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

Verifique a barra de status para confirmar que o cofre foi criado com êxito. O cofre será listado como **ativo** na página de **Serviços de recuperação** de principal.

## <a name="step-2-set-up-an-azure-network"></a>Etapa 2: Configurar uma rede do Azure

Configure uma rede Azure para que o Azure VMs serão conectadas a uma rede após failover e para que failback para o site local possa trabalhar como esperado.

1. No portal do Azure > **Criar rede virtual** especifique o nome de rede. IP endereço intervalo e sub-rede nome.
2. Você precisa adicionar VPN/rota expressa à rede se você precisa realizar failback. VPN/rota expressa podem ser adicionada à rede mesmo após failover.

[Leia mais](../virtual-network/virtual-networks-overview.md) sobre redes Azure.

> [AZURE.NOTE] [Migração de redes](../resource-group-move-resources.md) em grupos de recursos dentro da mesma assinatura ou assinaturas não há suporte para redes usadas para implantar recuperação do Site.

## <a name="step-3-install-the-vmware-components"></a>Etapa 3: Instalar os componentes de VMware

Se você quiser replicar VMware virtual máquinas instalar os seguintes componentes de VMware no servidor de gerenciamento:

1. [Baixar](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) e instalar VMware vSphere PowerCLI 6.0.
2. Reinicie o servidor.


## <a name="step-4-download-a-vault-registration-key"></a>Etapa 4: Baixar uma chave de registro do cofre

1. Do gerenciamento servidor abrir o console de recuperação de Site no Azure. Na página **Serviços de recuperação** clique no cofre para abrir a página de início rápido. Início rápido também pode ser aberto a qualquer momento usando o ícone.

    ![Ícone de início rápido](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)

2. Na página de **Início rápido** , clique em **Recursos de destino de preparar** > **baixar uma chave do registro**. O arquivo de registro é gerado automaticamente. Ele é válido para 5 dias depois que ela é gerada.


## <a name="step-5-install-the-management-server"></a>Etapa 5: Instalar o servidor de gerenciamento
> [AZURE.TIP] Certificar-se de que essas URLs estão acessíveis do management server:
>
- *. hypervrecoverymanager.windowsazure.com
- *. accesscontrol.windows.net
- *. backup.windowsazure.com
- *. blob.core.windows.net
- *. store.core.windows.net
- https://dev.MySQL.com/Get/archives/MySQL-5.5/MySQL-5.5.37-Win32.msi
- https://www.msftncsi.com/ncsi.txt




[AZURE.VIDEO enhanced-vmware-to-azure-setup-registration]

1. Na página de **Início rápido** Baixe o arquivo de instalação unificada no servidor.

2. Execute o arquivo de instalação para iniciar a instalação no Assistente de configuração de Unificação de recuperação de Site.

3.  Antes de **começar** , selecione **instalar o servidor de configuração e processo**.

    ![Antes de começar](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)
4. Na **Licença de Software de terceiros** , clique em **aceito** para baixar e instalar MySQL. 

    ![Terceiro = software de terceiros](./media/site-recovery-vmware-to-azure-classic/combined-wiz105.PNG)

5. No **registro** procure e selecione a chave do registro baixado do cofre.

    ![Registro](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)

6. Em **Configurações de Internet** Especifica como o provedor em execução no servidor de configuração se conectará a recuperação de Site do Azure pela internet.

    - Se você quiser conectar-se com o proxy que está configurado na máquina selecione **conectar com configurações de proxy existentes**.
    - Se quiser que o provedor para conectar-se diretamente selecione **conectar diretamente sem um proxy**.
    - Se o proxy existente requer autenticação, ou que você deseja usar um proxy personalizado para a conexão do provedor, selecione **conectar-se com configurações personalizadas de proxy**.
        - Se você usar um proxy personalizado, que você precisará especificar o endereço, porta e credenciais
        - Se você estiver usando um proxy, você deve ter permissão já URLs a seguir:
            - *. hypervrecoverymanager.windowsazure.com;    
            - *. accesscontrol.windows.net; 
            - *. backup.windowsazure.com; 
            - *. blob.core.windows.net; 
            - *. store.core.windows.net
            

    ![Firewall](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

7. **Verificação de pré-requisitos** configuração executa uma verificação para certificar-se de que a instalação pode executar. 

    
    ![Pré-requisitos](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

     Se for exibido um aviso sobre a **sincronização de tempo globais verificar** verificar que a hora no relógio do sistema (configurações de**Data e hora** ) é a mesma que o fuso horário.

    ![TimeSyncIssue](./media/site-recovery-vmware-to-azure-classic/time-sync-issue.png)

8. Na **Configuração de MySQL** crie credenciais para logon a instância do servidor MySQL que será instalada.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)

9. Em **Detalhes do ambiente** selecionar se você vai replicar VMs VMware. Se você estiver, a instalação verifica se PowerCLI 6.0 está instalado.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)

10. Em selecionar **Instalar o local** onde você deseja instalar os binários e armazene o cache. Você pode selecionar uma unidade que tenha pelo menos 5 GB de armazenamento disponível, mas recomendamos uma unidade de cache com pelo menos 600 GB de espaço livre.

    ![Local de instalação](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)

11. Em **Seleção de rede** , especifique o ouvinte (adaptador de rede e porta SSL) em que o servidor de configuração será enviar e receber dados de replicação. Você pode modificar o padrão porta (9443). Além dessa porta, a porta 443 será usada por um servidor web que organiza operações de replicação. 443 não devem ser usados para receber o tráfego de replicação.


    ![Seleção de rede](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)



12.  Em **Resumo** , revise as informações e clique em **instalar**. Quando a instalação terminar uma senha é gerada. Você precisará delas quando você habilita replicação então copiá-lo e mantê-lo em um local seguro.

    ![Resumo](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)



13.  Em **Resumo** , revise as informações.

    ![Resumo](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)

>[AZURE.WARNING]Proxy do agente de serviço Microsoft Azure recuperação precisa ser configurado.
>Quando a instalação estiver concluída inicie um aplicativo chamado "Shell de serviços de recuperação do Microsoft Azure" no menu Iniciar do Windows. Na janela de comando que será aberta, execute o seguinte conjunto de comandos para configurar as configurações do servidor proxy.
>
    $pwd = ConvertTo-SecureString -String ProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine



### <a name="run-setup-from-the-command-line"></a>Executar a configuração da linha de comando

Você também pode executar o Assistente de unificado da linha de comando, da seguinte maneira:

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Onde:

- / ServerMode: obrigatório. Especifica se a instalação deve instalar os servidores de configuração e processos ou somente o servidor de processo (usado para instalar servidores de processo adicional). Valores de entrada: CS, PS.
- Unidade_de_instalação: obrigatório. Especifica a pasta onde os componentes são instalados.
- / MySQLCredFilePath. Obrigatório. Especifica o caminho para um arquivo onde as credenciais do servidor MySQL estão história. Obtenha o modelo para criar o arquivo.
- / VaultCredFilePath. Obrigatório. Local do arquivo de credenciais do cofre
- / EnvType. Obrigatório. Tipo de instalação. Valores: VMware, NonVMware
- / PSIP e /CSIP. Obrigatório. Endereço IP do servidor de processo e servidor de configuração.
- / PassphraseFilePath. Obrigatório. Local do arquivo de senha.
- / ByPassProxy. Opcional. Especifica que o servidor de gerenciamento se conecta ao Azure sem um proxy.
- / ProxySettingsFilePath. Opcional. Especifica as configurações para um proxy personalizado (proxy padrão no servidor que requer autenticação) ou proxy personalizado




## <a name="step-6-set-up-credentials-for-the-vcenter-server"></a>Etapa 6: Configurar credenciais para o servidor vCenter

> [AZURE.VIDEO enhanced-vmware-to-azure-discovery]

O servidor de processo, possa detectar automaticamente VMs VMware que são gerenciadas por um servidor vCenter. Para descoberta automática recuperação do Site precisa ter uma conta e as credenciais que podem acessar o servidor vCenter. Isso não é relevante se você estiver replicar apenas servidores físicos.

Faça isso da seguinte maneira:

1. No vCenter server crie uma função (**Azure_Site_Recovery**) no nível do vCenter com as [permissões necessárias](#vmware-permissions-for-vcenter-access).
2. Atribua a função de **Azure_Site_Recovery** para um usuário vCenter.

    >[AZURE.NOTE] Uma conta de usuário do vCenter que tenha a função de somente leitura pode executar failover sem desligar máquinas de origem protegido. Se você quiser desligar nessas máquinas você precisará a função Azure_Site_Recovery. Observe que se você está apenas migrando VMs VMware no Azure e não será necessário failback, em seguida, a função de somente leitura é suficiente.

3. Para adicionar a conta abra **cspsconfigtool**. Ele está disponível como um atalho na área de trabalho e localizado na pasta de \home\svsystems\bin [local de instalação].
2. na guia **Gerenciar contas** , clique em **Adicionar conta**.

    ![Adicionar conta](./media/site-recovery-vmware-to-azure-classic/credentials1.png)

3. Em **Account Details** adicione credenciais que podem ser usadas para acessar o servidor vCenter. Observe que pode levar mais de 15 minutos para que o nome da conta apareça no portal. Para atualizar imediatamente, clique em Atualizar na guia **Servidores de configuração** .

    ![Detalhes](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## <a name="step-7-add-vcenter-servers-and-esxi-hosts"></a>Etapa 7: Adicionar servidores do vCenter e ESXi hosts

Se você estiver replicar VMs VMware é necessário adicionar um servidor de vCenter (ou host ESXi).

1. Nos **servidores** > **Servidores de configuração** , selecione o servidor de configuração > **Adicionar vCenter servidor**.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)

2. Adicione o servidor vCenter ou ESXi detalhes do host, o nome da conta que você especificou para acessar o servidor vCenter na etapa anterior e o servidor de processo que será usado para descobrir VMs VMware que são gerenciados pelo servidor vCenter. Observe que o servidor de vCenter ou ESXi host deve estar localizado na mesma rede que o servidor em que o servidor processo está instalado.

    >[AZURE.NOTE] Se você estiver adicionando vCenter servidor ou host de ESXi com uma conta que não tem privilégios de administrador no servidor vCenter ou host, verifique se o vCenter ou contas ESXi tem esses privilégios habilitados: data center, armazenamento de dados, pasta, Jost, rede, recursos, Virtual da máquina, vSphere alternar distribuído. Além disso, o servidor de vCenter precisa do privilégio de modos de exibição de armazenamento.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)

3. Após a conclusão da descoberta servidor vCenter será listado na guia **Servidores de configuração** .

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)


## <a name="step-8-create-a-protection-group"></a>Etapa 8: Criar um grupo de proteção

> [AZURE.VIDEO enhanced-vmware-to-azure-protection]


Os grupos de proteção são agrupamentos lógicos de máquinas virtuais ou servidores físicos que você deseja proteger usando as mesmas configurações de proteção. Você aplicar as configurações de proteção a um grupo de proteção e as configurações são aplicadas a todas as máquinas máquinas virtuais/físico que você adicionar ao grupo.

1. Abrir **Itens protegidos** > **Grupo de proteção** e clique em para adicionar um grupo de proteção.

    ![Criar grupo de proteção](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)

2. Na página **Especificar configurações de grupo de proteção** , especifique um nome para o grupo e em **de** , selecione o servidor de configuração no qual você deseja criar o grupo. **Destino** é Azure.

    ![Configurações de grupo de proteção](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)

3. Na página **Especificar configurações de replicação** de definir as configurações de replicação que serão usadas para todas as máquinas no grupo.

    ![Replicação de grupo de proteção](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

    - **Consistência de máquina virtual multi**: se você ativar isso cria pontos de recuperação consistentes com o aplicativo compartilhado nos computadores do grupo de proteção. Esta configuração é mais relevante quando todas as máquinas no grupo de proteção estão executando a mesma carga de trabalho. Todas as máquinas serão recuperadas ao mesmo ponto de dados. Isso está disponível se você estiver replicação VMs VMware ou servidores físicos Windows/Linux.
    - **Limite de RPO**: define o RPO. Alertas serão geradas quando a replicação de proteção de dados contínua excede o valor de limite RPO configurado.
    - **Retenção de ponto de recuperação**: Especifica a janela de retenção. Máquinas protegidas podem ser recuperadas a qualquer ponto dentro esta janela.
    - **Frequência de instantâneo consistentes com o aplicativo**: Especifica a frequência contendo instantâneos consistentes com o aplicativo de pontos de recuperação serão criados.

Quando você clica na marca de seleção será criado um grupo de proteção com o nome que você especificou. In addition um segundo grupo de proteção é criado com o nome < proteção-grupo-nome-Failback). Este grupo de proteção é usado se você não volta para o site local após failover no Azure. Você pode monitorar os grupos de proteção conforme eles são criados na página **Itens protegidos** .

## <a name="step-9-install-the-mobility-service"></a>Etapa 9: Instalar o serviço de mobilidade

Ativar a proteção para máquinas virtuais e servidores físicos a primeira etapa é instalar o serviço de mobilidade. Você pode fazer isso de duas maneiras:

- Notificações por push e instalá-lo em cada máquina do servidor processo automaticamente. Observe que quando você adiciona máquinas a um grupo de proteção e elas já estiverem executar uma versão apropriada da instalação de envio do serviço de mobilidade não ocorre.
- Instale automaticamente o serviço usando seu método de envio de empresa como WSUS ou Gerenciador de configuração do System Center. Verifique se que você configurou o servidor de gerenciamento antes de fazer isso.
- Instale manualmente em cada máquina que você deseja proteger. fazer Verifique se você configurou o servidor de gerenciamento antes de fazer isso.


### <a name="install-the-mobility-service-with-push-installation"></a>Instalar o serviço de mobilidade com instalação por envio

Quando você adiciona máquinas a um grupo de proteção no serviço de mobilidade é automaticamente enviado e instalado em cada máquina pelo servidor de processo.


#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Preparar para envio automático em máquinas Windows

Veja aqui como preparar máquinas do Windows para que o serviço de mobilidade pode ser instalado automaticamente pelo servidor de processo.

1.  Crie uma conta que pode ser usada pelo servidor de processo para acessar o computador. A conta deve ter privilégios de administrador (local ou domínio). Observe que essas credenciais são usadas apenas para instalação de envio do serviço de mobilidade.

    >[AZURE.NOTE] Se você não estiver usando uma conta de domínio, você precisará desativar o controle de acesso de usuário remoto na máquina local. Para fazer isso, no registro em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, adicione a entrada DWORD LocalAccountTokenFilterPolicy com um valor de 1 em. Para adicionar o registro a entrada de uma CLI abrir comando ou usando o PowerShell inserir **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  O Firewall do Windows da máquina que você deseja proteger, selecione **Permitir um aplicativo ou recurso pelo Firewall** e habilitar o **compartilhamento de impressora e arquivo** e **Instrumentação de gerenciamento do Windows**. Para máquinas que fazem parte de um domínio, você pode configurar a política de firewall com um GPO.

    ![Configurações de firewall](./media/site-recovery-vmware-to-azure-classic/mobility1.png)

2. Adicione a conta que você criou:

    - Abra **cspsconfigtool**. Ele está disponível como um atalho na área de trabalho e localizado na pasta de \home\svsystems\bin [local de instalação].
    - Na guia **Gerenciar contas** , clique em **Adicionar conta**.
    - Adicione a conta que você criou. Depois de adicionar a conta, você precisará fornecer as credenciais quando você adiciona uma máquina a um grupo de proteção.


#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Preparar para envio automático em servidores Linux

1.  Certifique-se de que o computador Linux que você deseja proteger é suportado conforme descrito em [pré-requisitos de local](#on-premises-prerequisites). Verifique se há conectividade de rede entre o computador que você deseja proteger e o servidor de gerenciamento que executa o servidor de processo.

2.  Crie uma conta que pode ser usada pelo servidor de processo para acessar o computador. A conta deve ser um usuário raiz no servidor Linux de origem. Observe que essas credenciais são usadas apenas para instalação de envio do serviço de mobilidade.

    - Abra **cspsconfigtool**. Ele está disponível como um atalho na área de trabalho e localizado na pasta de \home\svsystems\bin [local de instalação].
    - Na guia **Gerenciar contas** , clique em **Adicionar conta**.
    - Adicione a conta que você criou. Depois de adicionar a conta, você precisará fornecer as credenciais quando você adiciona uma máquina a um grupo de proteção.

3.  Verifique se o arquivo /etc/hosts no servidor Linux de origem contém as entradas que mapeiam o nome do host local para endereços IP associados a todos os adaptadores de rede.
4.  Instalar a última openssh, openssh-servidor, pacotes openssl na máquina que deseja proteger.
5.  Certifique-se de que SSH está ativado e em execução na porta 22.
6.  Habilite a autenticação de subsistema e a senha SFTP no arquivo sshd_config da seguinte maneira:

    - Faça login como raiz.
    - No arquivo de /etc/ssh/sshd_config, localize a linha que começa com PasswordAuthentication.
    - Tire comentários a linha e altere o valor de **não** para **Sim**.
    - Localize a linha que começa com **subsistema** e Tire comentários a linha.

        ![Linux](./media/site-recovery-vmware-to-azure-classic/mobility2.png)


### <a name="install-the-mobility-service-manually"></a>Instalar o serviço de mobilidade manualmente

Os instaladores estão disponíveis em C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository.

Sistema operacional de origem | Arquivo de instalação do serviço de mobilidade
--- | ---
Windows Server (somente 64 bits) | Microsoft-ASR_UA_9. *.0.0_Windows_* release.exe
CentOS 6.4, 6.5, 6.6 (somente 64 bits) | Microsoft-ASR_UA_9. *.0.0_RHEL6-64_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (somente 64 bits) | Microsoft-ASR_UA_9. *.0.0_SLES11-SP3-64_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (somente 64 bits) | Microsoft-ASR_UA_9. *.0.0_OL6-64_*release.tar.gz


#### <a name="install-manually-on-a-windows-server"></a>Instalar manualmente em um servidor Windows


1. Baixe e execute o instalador relevante.
2. Antes de **começar **, selecione **serviço de mobilidade**.

    ![Serviço de mobilidade](./media/site-recovery-vmware-to-azure-classic/mobility3.png)

3. Em **Detalhes de configuração do servidor** , especifique o endereço IP do servidor de gerenciamento e a senha que foi gerada quando você tiver instalado os componentes do servidor de gerenciamento. Você pode recuperar a senha executando: ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** no servidor de gerenciamento.

    ![Serviço de mobilidade](./media/site-recovery-vmware-to-azure-classic/mobility6.png)

4. No **Local de instalação** deixe o local padrão e clique em **Avançar** para iniciar a instalação.
5. Em **Andamento de instalação** monitorar instalação e reinicie o computador se for solicitado.

Você também pode instalar da linha de comando:

UnifiedAgent.exe [/ função < agente/MasterTarget >] [/ InstallLocation <Installation Directory>] [/ CSIP <IP address of CS to be registered with>] [/ PassphraseFilePath <Passphrase file path>] [/ LogFilePath <Log File Path>]

Onde:

- / Função: obrigatório. Especifica se o serviço de mobilidade deve ser instalado.
- / InstallLocation: obrigatório. Especifica onde instalar o serviço.
- / PassphraseFilePath: obrigatório. Especifica a senha do servidor de configuração.
- / LogFilePath: obrigatório. Especifica o local de arquivos de configuração de log

#### <a name="uninstall-mobility-service-manually"></a>Desinstalar manualmente o serviço de mobilidade

Serviço de mobilidade pode ser desinstalado usando o programa de remover adicionar no painel de controle ou linha de comando.

O comando para desinstalar o serviço de mobilidade usando linha de comando é

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="modify-the-ip-address-of-the-management-server"></a>Modificar o endereço IP do servidor de gerenciamento

Após executar o assistente, você pode modificar o endereço IP do servidor de gerenciamento da seguinte maneira:

1. Abra o hostconfig.exe arquivo (localizada na área de trabalho).
2. Na guia **Global** , você pode alterar o endereço IP do servidor de gerenciamento.

    >[AZURE.NOTE] Você só deve alterar o endereço IP do servidor de gerenciamento. O número da porta para comunicações de servidor de gerenciamento deve ser 443 e usar HTTPS deve ser habilitado para a esquerda. A senha não deve ser modificada.

    ![Endereço IP do servidor de gerenciamento](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### <a name="install-manually-on-a-linux-server"></a>Instale manualmente em um servidor Linux:

1. Copie o arquivo tar apropriado com base na tabela acima na máquina de Linux que você deseja proteger.
2. Abra um programa shell e extrair o arquivo tar compactado para um caminho local executando:`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Crie um arquivo de passphrase.txt no diretório local para o qual foi extraído o conteúdo do arquivo tar. Para fazer isso copiar a senha de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase no servidor de gerenciamento e salvá-lo no passphrase.txt executando *`echo <passphrase> >passphrase.txt`* no shell.
4. Instalar o serviço de mobilidade por meio de *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Especificar o endereço IP interno do servidor de gerenciamento e verifique se a porta 443 está selecionada.

**Você também pode instalar da linha de comando**:

1. Copiar a senha de C:\Program Files (x86) \InMage Systems\private\connection no servidor de gerenciamento e salve-o como "passphrase.txt" no servidor de gerenciamento. Em seguida, execute esses comandos. Em nosso exemplo o endereço IP do servidor de gerenciamento é 104.40.75.37 e a porta HTTPS deve ser 443:

Para instalar em um servidor de produção:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Para instalar no servidor de destino mestre:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## <a name="step-10-enable-protection-for-a-machine"></a>Etapa 10: Habilitar proteção para um computador

Para ativar a proteção que você adiciona máquinas virtuais e servidores físicos a um grupo de proteção. Antes de começar, observe o seguinte se você estiver protegendo máquinas virtuais VMware:

- VMs VMware forem descobertas a cada 15 minutos e poderia levar mais de 15 minutos para que apareçam no portal de recuperação de Site após a detecção.
- Alterações de ambiente na máquina virtual (como a instalação de ferramentas de VMware) também podem levar mais de 15 minutos para ser atualizado no recuperação do Site.
- Você pode verificar a última vez descoberta VMs VMware no **Último contato no** campo para o host do servidor/ESXi vCenter na guia **Servidores de configuração** .
- Se você tiver um grupo de proteção já criou e adicione um host de servidor ou ESXi vCenter depois disso, ele pode levar mais de 15 minutos para o portal de recuperação de Site do Azure atualizar e máquinas virtuais seja listado na caixa de diálogo **Adicionar computadores a um grupo de proteção** .
- Se você gostaria de continuar adicionando máquinas ao grupo de proteção sem aguardar a descoberta agendada imediatamente, realce o servidor de configuração (não clique nele) e clique no botão **Atualizar** .

Além disso, observe que:

- Recomendamos que você projetar seus grupos de proteção espelho suas cargas de trabalho. Por exemplo, adicione máquinas executando um aplicativo específico ao mesmo grupo.
- Quando você adiciona máquinas a um grupo de proteção, o servidor processo envia automaticamente e instala o serviço de mobilidade, se ela já não estiver instalada. Observe que você precisará ter o mecanismo de envio prepare conforme descrito na etapa anterior.


Adicione máquinas a um grupo de proteção:

1. Clique em **itens protegidos** > **Grupo proteção** > **máquinas** > Adicionar máquinas. \As uma prática recomendada
2. Em **Selecionar máquinas virtuais** se você estiver protegendo máquinas virtuais VMware, selecione um servidor de vCenter que está gerenciando suas máquinas virtuais (ou o host de EXSi em que eles estão sendo executado) e selecione as máquinas.

    ![Habilitar proteção](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)

3.  Em **Selecionar máquinas virtuais** se você estiver protegendo servidores físicos, no Assistente para **Adicionar máquinas físicas** forneça o endereço IP e nome amigável. Selecione a família de sistemas operacionais.

    ![Habilitar proteção](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)

4. Em **Especificar recursos de destino** , selecione a conta de armazenamento você está usando para replicação e selecione se as configurações devem ser usadas para todas as cargas de trabalho. Observe que as contas de armazenamento premium atualmente não são suportadas.

    >[AZURE.NOTE] 1. não há suporte para a movimentação de contas de armazenamento criados usando o [novo portal Azure](../storage/storage-create-storage-account.md) em grupos de recursos.                           2.[migração de contas de armazenamento](../resource-group-move-resources.md) em grupos de recursos dentro da mesma assinatura ou assinaturas não há suporte para contas de armazenamento usadas para implantar recuperação do Site.

    ![Habilitar proteção](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)

5. Em **Especificar contas** , selecione a conta [configurado](#install-the-mobility-service-with-push-installation) para usar para instalação automática do serviço de mobilidade.

    ![Habilitar proteção](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)

6. Clique na marca de seleção para concluir a adição de máquinas ao grupo de proteção e iniciar replicação inicial para cada máquina.

    >[AZURE.NOTE] Se a instalação por envio foi preparada o serviço de mobilidade é instalada automaticamente em computadores que não têm quando são adicionados ao grupo de proteção. Depois que o serviço é instalação um trabalho de proteção inicia e falha. Após a falha, você precisará reiniciar manualmente cada máquina que teve o serviço de mobilidade instalado. Após a reinicialização do trabalho de proteção começa novamente e replicação inicial ocorre.

Você pode monitorar o status na página de **trabalhos** .

![Habilitar proteção](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

Além disso, o status de proteção possa ser monitorado **Protegido itens**do > <protection group name> > **máquinas virtuais**. Depois de replicação inicial seja concluída e os dados são sincronizados, o status de máquina muda para** protegido**.

![Habilitar proteção](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)


## <a name="step-11-set-protected-machine-properties"></a>Etapa 11: Conjunto protegido propriedades de máquina

1. Depois que uma máquina tiver um status de **protegido** , você pode configurar suas propriedades de failover. Na proteção detalhes do grupo Selecione o computador em Abra a guia de **Configurar** .
2. Recuperação de site automaticamente sugere propriedades para a máquina virtual do Azure e detecta que o local as configurações de rede.

    ![Definir propriedades de máquina virtual](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)

3. Você pode modificar essas configurações:

    -  **Nome da máquina virtual do Azure**: Este é o nome que será fornecido na máquina no Azure após failover. O nome deve estar em conformidade com requisitos Azure.

    -  **Tamanho de máquina virtual do Azure**: O número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino. [Leia mais](../virtual-machines/virtual-machines-linux-sizes.md/#size-tables) sobre adaptadores e tamanhos. Observe que:

        - Quando você modifica o tamanho de uma máquina virtual e salva as configurações, o número de adaptador de rede serão alteradas quando você abre a guia de **Configurar** próxima vez. O número de adaptadores de rede de máquinas virtuais de destino é mínimo do número de adaptadores de rede na máquina virtual de origem e o número máximo de adaptadores de rede compatíveis com o tamanho da máquina virtual escolhido.
            - Se o número de adaptadores de rede no computador de origem for menor que ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores como fonte.
            - Se o número de adaptadores da máquina virtual de origem exceder o número permitido para o tamanho de destino e em seguida, o tamanho máximo de destino será usado.
            - Por exemplo, se uma máquina de origem tem dois adaptadores de rede e o tamanho da máquina de destino dá suporte a quatro, máquina de destino terá dois adaptadores. Se o computador de origem tem dois adaptadores, mas o tamanho de destino com suporte apenas dá suporte para uma máquina de destino terá apenas um adaptador.
        - Se a máquina virtual tem vários adaptadores de rede, todos os adaptadores devem conectado à mesma rede Azure.
    - **Rede Azure**: você deve especificar uma rede Azure que Azure VMs serão conectadas ao após failover. Se você não especificar um VMs Azure não estiver conectadas a uma rede. Além disso, você precisará especificar uma rede Azure se você quiser failback do Azure para o site local. Failback requer uma conexão VPN entre uma rede Azure e uma rede local.
    - **Endereço IP do Azure/sub-rede**: para cada adaptador de rede que você seleciona a sub-rede à qual a máquina virtual do Azure deve conectar. Observe que:
        - Se o adaptador de rede da máquina de origem é configurado para usar um endereço IP estático que você pode especificar um endereço IP estático para a máquina virtual do Azure. Se você não fornecer um endereço IP estático, em seguida, qualquer endereço IP disponível será alocado. Se o endereço IP de destino for especificado, mas ele já está em uso por outro máquina virtual no Azure failover falhará. Se o adaptador de rede da máquina de origem estiver configurado para usar DHCP você terá DHCP como a configuração do Azure.

## <a name="step-12-create-a-recovery-plan-and-run-a-failover"></a>Etapa 12: Criar um plano de recuperação e executar um failover

> [AZURE.VIDEO enhanced-vmware-to-azure-failover]

Você pode executar um failover para um único computador ou falhar ao longo de várias máquinas virtuais que executar a mesma tarefa ou executar a mesma carga de trabalho. Falha em vários computadores ao mesmo tempo que você adicioná-las a um plano de recuperação.

### <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

1. Na página de **Planos de recuperação** clique **Adicionar plano de recuperação** e adicione um plano de recuperação. Especifique os detalhes do plano e selecione **Azure** como o destino.

    ![Configurar o plano de recuperação](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)

2. Em **Selecionar Máquina Virtual** selecione um grupo de proteção e selecione máquinas no grupo para adicionar ao plano de recuperação.

    ![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

Você pode personalizar o plano para criar grupos e sequência a ordem na qual máquinas no plano de recuperação são falhou. Você também pode adicionar scripts e prompts para ações manuais. Scripts podem ser criadas manualmente ou usando [Runbooks de automação do Azure](site-recovery-runbook-automation.md). [Saiba mais](site-recovery-create-recovery-plans.md) sobre como personalizar os planos de recuperação.

## <a name="run-a-failover"></a>Executar um failover

Antes de executar um failover, observe que:


- Certifique-se de que o servidor de gerenciamento está em execução e disponível - caso contrário failover falhará.
- Se você executar uma anotação de failover não planejado que:

    - Se possível deve desligar máquinas primárias antes de executar um failover não planejado. Isso garante que você não tiver a fonte e a réplica máquinas executando ao mesmo tempo. Se você estiver replicar VMs VMware, em seguida, quando você executa um failover não planejado você pode especificar que a recuperação de Site deve fazer melhor esforço para desligar as máquinas de origem. Dependendo do estado do site principal isso talvez ou pode não funcionar. Se você estiver replicar servidores físicos que recuperação de Site não oferecer essa opção.
    - Quando você executa um failover não planejado parar replicação de dados de máquinas primárias para que qualquer delta de dados não possam ser transferido após o início de um failover não planejado.

- Se você deseja se conectar à máquina virtual réplica no Azure após failover, habilite Conexão de área de trabalho remota no computador de origem antes de executar o failover e permitir conexão RDP através do firewall. Você também precisará permitir RDP no ponto de extremidade público da máquina virtual Azure após failover. Siga estas [práticas recomendadas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) para garantir que o RDP funciona após um failover.

>[AZURE.NOTE] Para obter o melhor desempenho quando você fizer um failover no Azure, certifique-se de que você tenha instalado o agente do Azure na máquina protegida. Isso ajuda na inicialização com mais rapidez e também ajuda no diagnóstico em caso de problemas. Agente de Linux pode ser encontradas [aqui](https://github.com/Azure/WALinuxAgent) - e Windows agente pode ser encontrada [aqui](http://go.microsoft.com/fwlink/?LinkID=394789)

### <a name="run-a-test-failover"></a>Executar um failover de teste

Executar um failover de teste para simular seus processos de recuperação e failover em uma rede isolada que não afeta o seu ambiente de produção e replicação regular continua normalmente. Teste failover inicia na origem e você pode executá-lo de algumas maneiras:

- **Não especificar uma rede Azure**: se você executar um failover de teste sem uma rede o teste simplesmente verificará que máquinas virtuais iniciar e são exibidos corretamente no Azure. Máquinas virtuais não estiver conectadas a uma rede do Azure após failover.
- **Especificar uma rede Azure**: este tipo de falha verifica que o ambiente inteiro de replicação vem até conforme o esperado e que Azure máquinas virtuais estão conectadas à rede especificada.


1. Na página de **Planos de recuperação** selecione o plano e clique em **Testar Failover**.

    ![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)

2. Em **Confirmar testar Failover** , selecione **Nenhum** para indicar que você não quiser usar uma rede Azure para o failover de teste, ou selecione a rede à qual o teste VMs será conectado após failover. Clique na marca de seleção para iniciar o failover.

    ![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)

3. Monitorar o progresso de failover na guia **trabalhos** .

    ![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)

4. Após concluir o failover também deve ser capaz de ver a réplica Azure máquina aparecem no portal do Azure > **máquinas virtuais**. Se você quiser iniciar uma conexão de RDP para a máquina virtual do Azure, você precisará abrir porta 3389 no ponto de extremidade de máquina virtual.

5. Quando tiver terminado, quando failover atinge Conclua testes fase, clique em testar concluído para concluir. Em anotações gravar e salve observações associadas com o failover de teste.

6. Clique em **o failover de teste é concluído** para limpar automaticamente o ambiente de teste. Depois que isso é feito o failover de teste mostrará um status de **concluída** . Quaisquer elementos ou VMs criadas automaticamente durante o failover de teste são excluídas. Observe que, se um failover de teste continua mais de duas semanas é concluído pela equipe.



### <a name="run-an-unplanned-failover"></a>Executar um failover não planejado

Failover não planejado é iniciada a partir do Azure e pode ser executada mesmo se o local principal não está disponível.


1. Na página de **Planos de recuperação** selecione o plano e clique em **Failover** > **Failover não planejado**.

    ![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)

2. Se você estiver replicar máquinas virtuais VMware, você pode selecionar experimentar e desligar VMs local. Este é o melhor esforço e failover continuará se o esforço é bem sucedida ou não. Se não for bem sucedida detalhes do erro serão exibidos na guia **trabalhos **> **Trabalhos de Failover não planejados**.

    ![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

    >[AZURE.NOTE] Esta opção não estará disponível se você estiver replicação de servidores físicos. Você precisará experimentar e desligar aqueles manualmente se possível.

3. Em **Confirmar Failover** , verifique se a direção de failover (ao Azure) e selecione o ponto de recuperação que você deseja usar para o failover. Se você ativou multi-máquina virtual quando você tiver configurado Propriedades de replicação, você pode recuperar para o ponto de recuperação de aplicativo ou falha consistente mais recente. Você também pode selecionar o **ponto de recuperação personalizada** recuperar para um ponto anterior no tempo. Clique na marca de seleção para iniciar o failover.

    ![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)

3. Aguarde o conclusão do trabalho failover não planejado. Você pode monitorar o andamento de failover na guia **trabalhos** . Observe que mesmo se ocorrerem erros durante failover não planejado o plano de recuperação é executado até que ela seja concluída. Você também deve ser capaz de ver a réplica Azure máquina aparecem em máquinas virtuais no portal do Azure.

### <a name="connect-to-replicated-azure-virtual-machines-after-failover"></a>Conectar a replicados Azure máquinas virtuais após failover

Para conectar-se a replicados máquinas virtuais no Azure após failover aqui o que é preciso:

1. Uma conexão de área de trabalho remota deve estar ativada na máquina principal.
2. O Firewall do Windows na máquina principal para permitir que o RDP.
3. Após o failover, você precisará adicionar RDP para o ponto de extremidade público para Azure máquina virtual.

[Leia mais](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) sobre como configurar isto.


## <a name="deploy-additional-process-servers"></a>Implantar servidores de processo adicionais

Se você tiver dimensionar out sua implantação além 200 máquinas de origem ou a taxa de rotatividade diária total excede 2 TB, você precisará de servidores de processo adicional para lidar com o volume de tráfego. Para configurar um servidor de processo adicional Verifique os requisitos em [servidores de processo adicional](#additional-process-servers) e siga as instruções para configurar o servidor de processo. Após a configuração do servidor, você pode configurar máquinas de origem para usá-lo.

### <a name="set-up-an-additional-process-server"></a>Configurar um servidor de processo adicionais

Configurar um servidor de processo adicional da seguinte maneira:

- Execute o assistente unificado para configurar um servidor de gerenciamento processo apenas como servidor.
- Se você quiser gerenciar a replicação de dados usando somente o novo servidor de processo você precisará migrar seu máquinas protegidas para fazer isso.

### <a name="install-the-process-server"></a>Instalar o servidor de processo

1. Na página de início rápido, baixe o arquivo de instalação unificada para a instalação do componente de recuperação do Site. Execute a instalação.
2. Antes de **começar** , selecione **Adicionar servidores de processo adicional para dimensionar a implantação**.

    ![Adicionar servidor de processo](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)

3. Conclua o Assistente da mesma maneira que você fez quando você [Configurar](#step-5-install-the-management-server) o primeiro servidor de gerenciamento.

4. Em **Detalhes de configuração do servidor** , especifique o endereço IP do servidor de gerenciamento original em que você instalou o servidor de configuração e a senha. No servidor de gerenciamento original executar ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** para obter a senha.

    ![Adicionar servidor de processo](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrar máquinas para usar o novo servidor de processo

1. Abra **Os servidores de configuração** > **Server** > nome do servidor de gerenciamento de original > **Detalhes do servidor**.

    ![Servidor do processo de atualização](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)

2. Na lista de **Servidores processo** clique em **Alterar processo servidor** ao lado do servidor que você deseja modificar.

    ![Servidor do processo de atualização](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)

3. No **Servidor do processo de alteração** > **Servidor do processo de destino** selecione o novo servidor de gerenciamento e selecione as máquinas virtuais que irá manipular o novo servidor de processo. Clique no ícone de informações para obter informações sobre o servidor. O espaço médio necessária para replicar cada máquina virtual selecionada para o novo servidor de processo é exibido para ajudar você a tomar decisões de carregar. Clique na marca de seleção para começar a replicar para o novo servidor de processo.

    ![Servidor do processo de atualização](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)




## <a name="vmware-permissions-for-vcenter-access"></a>Permissões de VMware para obter acesso vCenter

O servidor de processo, possa detectar automaticamente VMs em um servidor de vCenter. Para executar a descoberta automática, você precisará definir uma função (Azure_Site_Recovery) no nível do vCenter para permitir a recuperação de Site acessar o servidor vCenter. Observe que, se você só precisa migrar máquinas VMware para Azure e não precisar failback do Azure, você pode definir uma função de somente leitura que é suficiente. Configurar as permissões conforme descrito em [etapa 6: configurar credenciais para o servidor vCenter](#step-6-set-up-credentials-for-the-vcenter-server) as permissões de função são resumidas na tabela a seguir.

**Função** | **Detalhes** | **Permissões**
--- | --- | ---
Função Azure_Site_Recovery | Descoberta de VM VMware |Atribua esses privilégios do servidor de v Center:<br/><br/>Armazenamento de dados -> alocar espaço, armazenamento de dados de procurar, arquivo de nível baixo operações., remover arquivo, arquivos de máquina virtual de atualização<br/><br/>Rede -> atribuir de rede<br/><br/>Recurso -> Atribuir máquina virtual pool de recursos, migrar desligado máquina virtual, migrar da plataforma na máquina virtual<br/><br/>Tarefas -> Criar tarefa, tarefa de atualização<br/><br/>Máquina virtual -> Configuração<br/><br/>Máquina virtual -> interagir -> pergunta de resposta, dispositivo conexão., mídia configurar CD, mídia de disquete configurar, desligar Power na, instale o VMware tools<br/><br/>Estoque -> máquina virtual -> criar, registro, cancela o registro<br/><br/>Máquina virtual -> provisionamento -> Permitir download de máquina virtual, permitir que os arquivos de máquina virtual carregar<br/><br/>Máquina virtual -> instantâneos -> Remover instantâneos
função de usuário do vCenter | Descoberta de VM VMware/Failover sem desligamento da fonte máquina virtual | Atribua esses privilégios do servidor de v Center:<br/><br/>Objeto de Data Center –> propagar ao objeto filho, função = somente leitura <br/><br/>O usuário está atribuído em nível de data center e, portanto, tem acesso a todos os objetos no data center.  Se você quiser restringir o acesso, atribua a função de **nenhum acesso** com o objeto de **Propagar para filho** aos objetos filho (ESX hosts, armazenamentos de dados, VMs e redes).
função de usuário do vCenter | Failover e failback | Atribua esses privilégios do servidor de v Center:<br/><br/>Objeto de data center – propagar ao objeto filho, função = Azure_Site_Recovery<br/><br/>O usuário está atribuído em nível de data center e, portanto, tem acesso a todos os objetos no data center.  Se você quiser restringir o acesso, atribua a função de **nenhum acesso **com a **Propagar ao objeto filho** ao objeto filho (ESX hosts, armazenamentos de dados, VMs e redes).  



## <a name="third-party-software-notices-and-information"></a>Avisos de softwares de terceiros e informações

Não traduzir ou localizar

O software e firmware em execução no Microsoft produto ou serviço baseado em ou incorpora material dos projetos listados abaixo (coletivamente, "código de terceiros").  A Microsoft está o autor não original do código de terceiros.  O aviso de direitos autorais original e licença, em que a Microsoft recebido esse código de terceiros, são definidas estabelecidos abaixo.

As informações na seção A se refere componentes de código de terceiros nos projetos listados abaixo. Essas licenças e as informações são fornecidas somente para fins informativos.  Este código de terceiros está sendo relicensed a você pela Microsoft em termos de licenciamento de software da Microsoft para o Microsoft produto ou serviço.  

As informações na seção B for referente a componentes de código de terceiros que estão sendo disponibilizadas a você pela Microsoft sob os termos de licenciamento originais.

O arquivo completo pode ser encontrado no [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). A Microsoft se reserva todos os direitos não expressamente concedidos neste documento, seja por implicação, embargo ou de outra forma.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre failback](site-recovery-failback-azure-to-vmware-classic.md) para trazer seu falha sobre máquinas em execução no Azure volta para seu ambiente local.
