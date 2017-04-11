<properties
    pageTitle="Duplicar máquinas virtuais VMware e servidores físicos para Azure com recuperação de Site do Azure no portal do Azure | Microsoft Azure"
    description="Descreve como implantar o Azure recuperação do Site para coordenar replicação, failover e recuperação de máquinas virtuais VMware a locais e Windows/Linux servidores físicos para Azure usando o portal do Azure"
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
    ms.date="08/12/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-machines-to-azure-with-azure-site-recovery-using-the-azure-portal"></a>Replicar máquinas virtuais VMware e máquinas físicas no Azure com recuperação de Site do Azure usando o portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmware-to-azure.md)
- [Azure clássico](site-recovery-vmware-to-azure-classic.md)
- [Azure clássico (herdado)](site-recovery-vmware-to-azure-classic-legacy.md)

Bem-vindo ao Site Azure recuperação! Utilize este artigo se você quiser replicar máquinas virtuais VMware a locais ou servidores físicos Windows/Linux no Azure usando a recuperação de Site Azure no portal do Azure.

> [AZURE.NOTE] Azure tem dois diferentes [modelos de implantação](../resource-manager-deployment-model.md) para criar e trabalhar com recursos: Azure Resource Manager (ARM) e clássico. Azure também tem dois portais – o portal de clássico Azure que suporta o modelo clássico de implantação e o portal do Azure com suporte para ambos os modelos de implantação.

Recuperação de site no portal do Azure fornece um número de novos recursos:

- Os serviços de recuperação de Site do Azure e Backup de Azure são combinados em um único compartimento de serviços de recuperação para que você possa configurar e gerenciar continuidade de negócios e recuperação de dados (BCDR) de um único local. No painel de controle unificada, você pode monitorar e gerenciar operações em seus sites locais e na nuvem pública do Azure.
- Usuários com assinaturas do Azure provisionados com o programa de provedor de solução da nuvem (CSP) agora podem gerenciar as operações de recuperação de Site no portal do Azure.
- Recuperação de site no portal do Azure pode replicar máquinas para contas de armazenamento do ARM. Falha a recuperação de Site cria baseado em ARM VMs no Azure.
- Recuperação de site continua a oferecer suporte à replicação a contas de armazenamento clássico. Falha a recuperação de Site cria VMs usando o modelo clássico.

Depois de ler este artigo postar os comentários na parte inferior nos comentários Disqus. Perguntas técnicas no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Visão geral

As organizações precisam uma estratégia BCDR que determina como dados, cargas de trabalho e aplicativos permanecer em execução e disponível durante o tempo de inatividade planejado e e recuperar a condições de trabalho normal assim que possível. Sua estratégia BCDR deve manter dados corporativos seguros e recuperáveis e certifique-se de que cargas de trabalho permanecem disponíveis continuamente quando desastre.

Recuperação de site é um serviço Azure que contribui para sua estratégia BCDR por coordenação replicação de servidores físicos locais e máquinas virtuais para a nuvem (Azure) ou um data center secundário. Quando ocorrem interrupções em seu local principal, você Falha ao longo para o local secundário para manter aplicativos e cargas de trabalho disponíveis. Você não volta para seu local principal quando ele retorna as operações normais. Saiba mais em [o que é recuperação de Site do Azure?](site-recovery-overview.md)

Este artigo fornece todas as informações que você precisa replicar local VMs VMware e Windows/Linux servidores físicos no Azure. Ele inclui uma visão geral arquitetônica, informações e etapas de implantação para a configuração do Azure, os servidores locais, as configurações de replicação e planejamento da capacidade de planejamento. Depois que você configurou a infraestrutura você pode habilitar replicação em máquinas que você deseja proteger e verificar que o failover funciona.

## <a name="business-advantages"></a>Vantagens de negócios

- Recuperação de site oferece proteção externa para cargas de trabalho de negócios e aplicativos executados em VMs VMware e servidores físicos.
- O portal de serviços de recuperação fornece um único local para configurar, gerenciar e monitorar replicação, failover e recuperação.
- Recuperação do site possa detectar automaticamente VMs VMware adicionado ao vSphere hosts.
- Você pode facilmente executar failovers sua infraestrutura local do Azure e failback (restauração) do Azure para os servidores de VM VMware em seu site local.
- Você pode habilitar multi-máquina virtual e criar grupos de replicação para que cargas de trabalho de aplicativo hierárquico entre vários replicar máquinas ao mesmo tempo. Todas as máquinas em um grupo de replicação tem pontos de recuperação consistentes com falha e consistentes com o aplicativo quando elas falham sobre. Para failover, você pode coletar várias máquinas em planos de recuperação para que cargas de trabalho de aplicativo hierárquico falharem juntos.

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

Estes são os componentes do cenário:

- **Servidor de configuração**: um computador local que coordenadas comunicação e gerencia os processos de replicação e a recuperação de dados. Neste computador, você executará um arquivo de configuração única para instalar o servidor de configuração e esses componentes adicionais:
    - **Servidor processo**: atua como um gateway de replicação. Ele recebe dados de replicação de máquinas de origem protegido, otimizá-lo com o cache, compactação e criptografia e envia para o armazenamento do Azure. Ele também lida com a instalação de envio do serviço de mobilidade máquinas protegida e executa a descoberta automática de VMs VMware. O servidor de processo padrão é instalado no servidor de configuração. Você pode implantar servidores de processo autônomo adicionais para dimensionar a sua implantação.
    - **Servidor de destino mestre**: lida com dados de replicação durante o failback do Azure.

- **Serviço de mobilidade**: este componente está implantado em cada máquina (VM VMware ou servidor físico) que você deseja duplicar para Azure. Ele captura gravações de dados na máquina e encaminha para o servidor de processo.
- **Azure**: você não precisa criar qualquer VMs do Azure para lidar com replicação e failover no Azure.  Você precisa de uma assinatura do Azure, uma conta de armazenamento do Azure para armazenar dados replicados e uma rede virtual Azure para que o Azure VMs estão conectadas a uma rede após failover. A conta de armazenamento e a rede devem estar na mesma região como o Cofre de serviços de recuperação.
- **Failback**: quando você estiver pronto para falhar volta do Azure ao seu site local após um failover, você precisará criar uma VM Azure como um servidor de processo temporário. Você pode excluí-la após a conclusão da failback. Para failback, você também precisará uma conexão VPN (ou rota expressa do Azure) entre seu site local e a rede de Azure no qual suas VMs Azure estão localizados. Se o tráfego de failback for pesado também precisará configurar um mestre dedicado servidor destino máquina local. Tráfego mais claro de servidor de destino mestre padrão em execução no servidor de configuração pode ser usado.


O gráfico mostra como esses componentes interagem.

![arquitetura](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**Figura 1: VMware/física para Azure**

## <a name="azure-prerequisites"></a>Pré-requisitos Azure

Veja aqui o que você precisará no Azure implantar esse cenário.

**Pré-requisito** | **Detalhes**
--- | ---
**Conta do Azure**| Você precisará de uma conta [Do Microsoft Azure](http://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação do Site.
**Armazenamento do Azure** | Dados replicados são armazenados no armazenamento do Azure e Azure VMs são criadas quando houver falha. <br/><br/>Para armazenar dados você precisará de uma conta de armazenamento padrão ou premium na mesma região como o Cofre de serviços de recuperação.<br/><br/>Você pode usar uma conta de armazenamento LRS ou GRS. Recomendamos GRS para que dados e apresentam resiliência ocorre uma interrupção regional ou se a região primária não poderão ser recuperada. [Saiba mais](../storage/storage-redundancy.md).<br/><br/> [Armazenamento de Premium](../storage/storage-premium-storage.md) normalmente é usado para máquinas virtuais que precisa de um consistentemente alto desempenho de IO e baixa latência para cargas de trabalho intenso de IO de host.<br/><br/> Se você quiser usar uma conta de premium para armazenar dados duplicados, também será necessário uma conta de armazenamento padrão para armazenar logs de replicação que capturar alterações contínuas em dados locais.<br/><br/> Observe que as contas de armazenamento criadas no portal do Azure não podem ser movidas entre grupos de recursos. Também proteção para contas de armazenamento premium na Índia Central e do Sul Índia não é suportada atualmente.<br/><br/> [Leia sobre](../storage/storage-introduction.md) Armazenamento do Azure.
**Rede do Azure** | Você precisará de uma rede virtual Azure que Azure VMs se conectará quando houver falha. A rede virtual Azure deve estar na mesma região como o Cofre de serviços de recuperação.
**Failback do Azure** | Você precisará de um temporário servidor de processo configurado como uma máquina virtual do Azure. Você pode criar isso quando você estiver pronto para falhar novamente e excluí-la após falhas volta conclusão.<br/><br/> Para falhar novamente você precisará uma conexão de VPN (ou rota expressa do Azure) da rede Azure para o site local.

## <a name="configuration-server--scale-out-process-prerequisites"></a>Servidor de configuração / dimensionar pré-requisitos de processo

Você vai configurar uma máquina local como o servidor de configuração / escala-out servidor processo.

**Pré-requisito** | **Detalhes**
--- | ---
**Servidor de configuração**| Você precisa de um local físico ou máquina virtual que executa o Windows Server 2012 R2. Todos os componentes de recuperação de Site local são instalados no computador.<br/><br/>Para replicação de VM VMware, recomendamos que você implanta o servidor como uma VM VMware altamente disponível. Se você estiver replicar máquinas físicas máquina pode ser um servidor físico.<br/><br/> Failback para o site local do Azure é sempre VMs VMware independentemente se você falhou VMs ou servidores físicos. Se você não implantar o servidor de configuração como uma VM VMware você precisará configurar um servidor destino mestre separado como uma VM VMware para receber o tráfego de failback.<br/><br/>Se o servidor é uma VM VMware, o tipo de adaptador de rede deve ser VMXNET3. Se você usa um tipo diferente de adaptador de rede, você precisará instalar uma [atualização do VMware](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1) no servidor vSphere 5.5.<br/><br/>O servidor deve ter um endereço IP estático.<br/><br/>O servidor não deve ser um controlador de domínio.<br/><br/>O nome de host do servidor deve ser 15 caracteres ou menos.<br/><br/>O sistema operacional deve ser somente em inglês.<br/><br/> Você precisará instalar VMware vSphere PowerCLI 6.0. no servidor de configuração.<br/><br/>O servidor de configuração precisa acesso à internet. Acesso de saída é necessário da seguinte maneira:<br/><br/>Acesso temporário em HTTP 80 durante a instalação dos componentes de recuperação do Site (para baixar MySQL)<br/><br/>Acesso de saída em andamento na HTTPS 443 para gerenciamento de replicação<br/><br/>Acesso de saída em andamento na HTTPS 9443 tráfego de replicação (essa porta pode ser modificada)<br/><br/>O servidor também precisará acesso aos seguintes URLs para que ele possa se conectar ao Azure: *. hypervrecoverymanager.windowsazure.com; *. AccessControl.Windows.NET; *. backup.windowsazure.com; *. blob.Core.Windows.NET; *. store.core.windows.net<br/><br/>Se você tiver regras de firewall baseado em endereço IP no servidor, verifique se as regras permitiu que a comunicação com o Azure. Você precisará permitir que os [Intervalos de IP de data center do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e o protocolo HTTPS (443).<br/><br/>Permitir que os intervalos de endereços IP para a região Azure da sua assinatura e Oeste EUA.<br/><br/>Permitir que esta URL para o download de MySQL:.http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi


## <a name="vmware-vcentervsphere-host-prerequisites"></a>Pré-requisitos do VMware vCenter/vSphere host

**Pré-requisito** | **Detalhes**
--- | ---
**vSphere**| Você precisa de um ou mais hipervisores de vSphere VMware.<br/><br/>Hipervisores devem estar executando a versão de vSphere 6.0, 5.5 ou 5.1 com as últimas atualizações.<br/><br/>Recomendamos que sua vSphere hosts e servidores de vCenter estão localizados na mesma rede que o servidor de processo (Isso será a rede na qual o servidor de configuração está localizado, a menos que você configurou um servidor de processo dedicado).
**vCenter** | Recomendamos que você implantar um servidor de vCenter VMware para gerenciar seus hosts vSphere. Ele deve estar executando vCenter versão 6.0 ou 5.5 com as últimas atualizações.<br/><br/>Observe que a recuperação de Site não dá suporte a nova vCenter e vSphere 6.0 recursos como cross vCenter vMotion, volumes virtuais e armazenamento DRS. Suporte de recuperação de site está limitado aos recursos que também estavam disponíveis na versão 5.5.


## <a name="protected-machine-prerequisites"></a>Pré-requisitos de máquina protegida

**Pré-requisito** | **Detalhes**
--- | ---
**Local (VMs VMware)** | VMs VMware que você deseja proteger deve ter ferramentas de VMware instalado e em execução.<br/><br/> Máquinas que você deseja proteger devem estar em conformidade com [Azure pré-requisitos](site-recovery-best-practices.md#azure-virtual-machine-requirements) para a criação de VMs do Azure.<br/><br/>Capacidade de disco individual em máquinas protegidas não deve ser mais de 1023 GB. Uma máquina virtual pode ter até 64 discos (portanto, até 64 TB). <br/><br/>Mínimo 2 GB de espaço disponível na unidade de instalação para a instalação do componente.<br/><br/>Não há suporte para a proteção de VMs com discos criptografados.<br/><br/>Compartilhado convidado disco clusters não são suportados.<br/><br/>**20004 porta** deve ser aberta no firewall local da máquina virtual protegido, se você quiser ativar a **consistência do aplicativo**.<br/><br/>Máquinas que possuem Unified Extensible Firmware Interface (UEFI) / Extensible Firmware Interface(EFI) inicialização não é compatível.<br/><br/>Nomes de máquina devem conter entre 1 e 63 caracteres (letras, números e hifens). O nome deve começar com uma letra ou número e termina com uma letra ou número. Depois de habilitado replicação para um computador, você pode modificar o nome do Azure.<br/><br/>Se a fonte máquina virtual tiver agrupamento NIC-é convertida em uma única NIC após failover no Azure.<br/><br/>Se protegido máquinas virtuais tiver um disco iSCSI recuperação de Site converte o disco de iSCSI de máquina virtual protegido em um arquivo VHD quando a máquina virtual Falha ao longo do Azure. Se o destino iSCSI pode ser acessado pela máquina virtual do Azure ele irá conectá-la e ver essencialmente dois discos – o disco VHD na máquina virtual do Azure e o disco de iSCSI de origem. Nesse caso, você precisará desconectar o destino iSCSI que aparece na máquina virtual do Azure.
**Máquinas Windows (física ou VMware)** | O computador deve estar executando um sistema operacional de 64 bits suportado: Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 com no mínimo SP1.<br/><br/> O sistema operacional deve ser instalado na unidade C:\. O disco de sistema operacional deve ser um disco básico do Windows e não dinâmico. O disco de dados pode ser dinâmico.<br/><br/>Recuperação de site oferece suporte a VMs com um disco RDM. Durante failback, recuperação do Site será reutilizar o disco RDM se o disco de máquina virtual e RDM de fonte original estiver disponível. Se eles não estão disponíveis, durante o failback recuperação de Site criará um novo arquivo VMDK para cada disco.
**Máquinas Linux** (phyical ou VMware)|  Você precisará de um sistema operacional de 64 bits suportado: Red Hat Enterprise Linux 6.7,7.1,7.2; CentOS 6.5, 6.6,6.7,7.0,7.1,7.2; Oracle Enterprise Linux 6.4, 6.5 executando o núcleo compatível Red Hat ou o Unbreakable Enterprise núcleo versão 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>arquivos de /etc/hosts em máquinas protegidas devem conter entradas que mapeiam o nome de host local para endereços IP associados a todos os adaptadores de rede.<br/><br/>Se você quiser se conectar a uma máquina virtual Azure executando Linux após failover usando um Secure Shell cliente (ssh), verifique se o serviço de Secure Shell na máquina protegida está definido para iniciar automaticamente na inicialização do sistema e que as regras de firewall permitem que um ssh conexão a ela.<br/><br/>O nome do host, pontos de montagem, nomes de dispositivo e caminhos de sistema do Linux e nomes de arquivo (ex/etc /; /usr) devem estar apenas em inglês.<br/><br/>Proteção só pode ser habilitada para máquinas Linux com o armazenamento a seguir: (EXT3, ETX4, ReiserFS, XFS); de sistema de arquivos Vários caminhos software dispositivo mapeador (vários caminhos)); Gerenciador de volume: (LVM2). Servidores físicos com o armazenamento de controlador HP CCISS não são suportados. O sistema de arquivos ReiserFS é suportado apenas em SUSE Linux Enterprise Server 11 SP3.<br/><br/>Recuperação de site oferece suporte a VMs com um disco RDM.  Durante failback para Linux, recuperação de Site não reutilizar o disco RDM. Em vez disso, ele cria um novo arquivo VMDK para cada disco RDM correspondente.<br/><br/>Certifique-se de que você defina a configuração de disk.enableUUID=true nos parâmetros de configuração da máquina virtual do VMware. Crie a entrada se ele não existir. Ela tem necessária para fornecer um UUID consistente para o VMDK para que ele montagens corretamente. Adicionar esta configuração também garante que as alterações delta somente são transferidas para o local durante failback e não uma replicação completa.
**Serviço de mobilidade** |  **Windows**: você vai para enviar automaticamente o serviço de mobilidade em VMs executando o Windows, você precisará fornecer uma conta de administrador (administrador local na máquina Windows) para que o servidor processo pode fazer uma instalação por envio.<br/><br/>**Linux**: para enviar automaticamente o serviço de mobilidade para VMs executando Linux, você precisará criar uma conta que pode ser usada pelo servidor de processo para fazer uma instalação de envio.<br/><br/> Por padrão, todos os discos em um computador são duplicados. Para [Excluir um disco da replicação](#exclude-disks-from-replication), o serviço de mobilidade deve ser instalado manualmente na máquina antes de habilitar replicação.<br/>

## <a name="prepare-for-deployment"></a>Preparar para implantação

Para se preparar para a implantação, você precisará:

1. [Configurar uma rede Azure](#set-up-an-azure-network) na qual Azure VMs ficarão localizadas quando eles estão girados backup após failover. Além disso, para failback você precisará configurar uma conexão VPN (ou rota expressa do Azure) da rede Azure ao seu site local.
2. [Configurar uma conta de armazenamento do Azure](#set-up-an-azure-storage-account) para dados replicados.
3. [Preparar uma conta](#prepare-an-account-for-automatic-discovery) no servidor vCenter ou vSphere hospeda para que a recuperação do Site pode detectar automaticamente VMs VMware que são adicionados.
4. [Preparar o servidor de configuração](#prepare-the-configuration-server) para garantir que ela pode acessar URLs necessárias e instale vSphere PowerCLI 6.0.


### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

- A rede deve estar na mesma região Azure que no qual você vai implantar o Cofre de serviços de recuperação.
- Dependendo do modelo de recurso que você deseja usar para Falha ao longo do Azure VMs, você configurará a rede Azure no [modo de ARM](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [modo clássico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Para falhar volta do Azure ao seu site de VMware local é necessário uma conexão de VPN (ou uma conexão de rota expressa do Azure) da rede Azure no qual as VMs Azure replicado estão localizadas, à rede local em que o servidor de configuração está localizado.
- [Saiba mais sobre](../vpn-gateway/vpn-gateway-site-to-site-create.md) a implantação compatível modelos para conexões VPN de site para site e como [Configurar uma conexão](../vpn-gateway/vpn-gateway-site-to-site-create.md#create-your-virtual-network).
- Como alternativa, você pode configurar [Rota expressa do Azure](../expressroute/expressroute-introduction.md). [Saiba mais](../expressroute/expressroute-howto-vnet-portal-classic.md) sobre como configurar uma rede com rota expressa do Azure.

> [AZURE.NOTE] [Migração de redes](../resource-group-move-resources.md) em grupos de recursos dentro da mesma assinatura ou assinaturas não há suporte para redes usadas para implantar recuperação do Site.

### <a name="set-up-an-azure-storage-account"></a>Configurar uma conta de armazenamento do Azure

- Você precisará de um padrão ou uma conta de armazenamento do Azure premium para armazenar dados duplicados no Azure. A conta deve ser na mesma região como o Cofre de serviços de recuperação. Dependendo do modelo de recurso que você deseja usar para Falha ao longo do Azure VMs, você configurará uma conta no [modo ARM](../storage/storage-create-storage-account.md) ou [modo clássico](../storage/storage-create-storage-account-classic-portal.md).
- Se você estiver usando uma conta de premium para dados duplicados, que você precisa criar uma conta padrão adicional para armazenar logs de replicação que capturar alterações contínuas em dados locais.  

> [AZURE.NOTE] Não há suporte para a [migração de contas de armazenamento](../resource-group-move-resources.md) em grupos de recursos dentro da mesma assinatura ou assinaturas para contas de armazenamento usadas para implantar recuperação do Site.

### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a descoberta automática

O servidor do processo de recuperação do Site possa detectar automaticamente VMs VMware vSphere hosts ou em um servidor de vCenter que gerencia hosts. Para executar automáticas descoberta recuperação de Site credenciais que pode acessar o servidor de VMware. Isso não é relevante se você estiver replicar somente máquinas físicas.

1. Para usar uma conta dedicada para descoberta automática cria uma função (por exemplo, Azure_Site_Recovery) no nível do vCenter com as [permissões necessárias](#vmware-account-permissions).
2. Criar um novo usuário no servidor do host ou vCenter vSphere e atribuir a função ao usuário. Posteriormente você vai informar recuperação do Site sobre essas credenciais para que ele possa executar a descoberta automática.

    >[AZURE.NOTE] Uma conta de usuário do vCenter com uma função de somente leitura pode executar failover, mas não é possível desligar máquinas de origem protegido. Se você quiser desligar nessas máquinas você precisará a função [Azure_Site_Recovery](#vmware-account-permissions) . Se você está apenas migrando VMs VMware no Azure e não será necessário failback a função de somente leitura é suficiente.

### <a name="prepare-the-configuration-server"></a>Preparar o servidor de configuração

1.  Certifique-se de que o computador que você estiver usando para o servidor de configuração é compatível com os [pré-requisitos](#configuration-server-prerequisites). Em particular, certifique-se de que o computador está conectado à internet com estas configurações:

    - Permitir acesso destas URLs: *. hypervrecoverymanager.windowsazure.com; *. AccessControl.Windows.NET; *. backup.windowsazure.com; *. blob.Core.Windows.NET; *. store.core.windows.net
    - Permita acesso ao [http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi](http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi) baixar MySQL.
    - Permita a comunicação de firewall para Azure com os [intervalos de IP do data center Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e o protocolo HTTPS (443).

2.  Baixe e instale o [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) no servidor de configuração. (Outras versões do PowerCLI não são suportados, incluindo versões de R da versão 6.0.)


## <a name="create-a-recovery-services-vault"></a>Criar um cofre de serviços de recuperação

1. Entrar no [portal do Azure](https://portal.azure.com).
2. Clique em **novo** > **gerenciamento** > **Backup e recuperação de Site (OMS)**. Alternativamente você pode clicar em **Procurar** > **Cofre de serviços de recuperação** > **Adicionar**.

    ![Novo cofre](./media/site-recovery-vmware-to-azure/new-vault3.png)

3. Em **nome** , especifique um nome amigável para identificar o cofre. Se você tiver mais de uma assinatura, selecione um deles.
4. [Criar um novo grupo de recursos](../resource-group-template-deploy-portal.md) ou selecionar um existente. Especifique uma região do Azure. Máquinas serão replicadas para essa região. Observe que o armazenamento do Azure e redes usadas para recuperação do Site precisarão ser na mesma região. Para verificar regiões com suporte consulte disponibilidade geográficos em [Detalhes de preços de recuperação do Azure Site](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Se quiser acessar rapidamente o cofre no painel clique em **Fixar no painel de controle** e clique em **criar**.

    ![Novo cofre](./media/site-recovery-vmware-to-azure/new-vault-settings.png)

O novo cofre aparecerá no **painel** > **todos os recursos**e em principal blade **compartimentos de serviços de recuperação** .

## <a name="getting-started"></a>Guia de Introdução

Recuperação de site fornece uma experiência de Introdução projetado para você começar e executando assim que possível. Verifica os pré-requisitos e orienta você pelas etapas que é necessário para obter recuperação de Site implantado.

Você deverá selecionar o tipo de máquinas que você deseja duplicar e onde você deseja duplicar para. Configurar a infraestrutura, incluindo os servidores locais, configurações do Azure, políticas de replicação e planejamento da capacidade. Após sua infraestrutura de você habilitar replicação para VMs e servidores físicos. Em seguida, você pode executar failovers para máquinas específicas ou criar planos de recuperação falha em vários computadores.

Começar Introdução escolhendo como você deseja implantar recuperação do Site. As alterações do fluxo Introdução ligeiramente dependendo das suas necessidades de replicação.


## <a name="step-1-choose-your-protection-goals"></a>Etapa 1: Escolher suas metas de proteção

Selecione o que você deseja duplicar e onde você deseja duplicar para.

1. Na lâmina **compartimentos de serviços de recuperação** selecione seu cofre e clique em **configurações**.
2. Nas **configurações** > **Introdução** clique em **Recuperação de Site** > **etapa 1: preparar infraestrutura** > **meta de proteção**.

    ![Escolha metas](./media/site-recovery-vmware-to-azure/choose-goals.png)

3. Em **meta de proteção** selecione **Azure para**e selecione **Sim, com VMware vSphere Hypervisor**. Clique em **Okey**.

    ![Escolha metas](./media/site-recovery-vmware-to-azure/choose-goals2.png)


## <a name="step-2-set-up-the-source-environment"></a>Etapa 2: Configurar o ambiente de origem

Configurar o servidor de configuração e registre-o no cofre de serviços de recuperação. Se você estiver replicar VMs VMware especificar a conta de VMware que você estiver usando para descoberta automática.

1. Clique em **etapa 1: preparar infraestrutura** > **fonte**. Na **fonte de preparação** se você não tiver um servidor de configuração clique **+ servidor de configuração** de adicioná-lo.

    ![Configurar fonte](./media/site-recovery-vmware-to-azure/set-source1.png)

2. Na verificação **Adicionar servidor** blade nesse **Servidor de configuração** aparece em **servidor digite**.
3. Antes de configurar o servidor de configuração, verifique se [os pré-requisitos](#configuration-server-prerequisites). Em verificação específica que máquina pode acessar as URLs necessárias.
4.  Baixe o arquivo de instalação de configuração de Unificação de recuperação de Site.
5.  Baixe a chave de registro do cofre. Você precisará isso quando você executa a configuração de Unificação. A chave é válida por 5 dias após você gerá-lo.

    ![Configurar fonte](./media/site-recovery-vmware-to-azure/set-source2.png)

6.  No computador que você está usando como o servidor de configuração, executado a configuração de Unificação de mensagens para instalar o servidor de configuração, o servidor processo e o servidor de destino mestre.


### <a name="run-site-recovery-unified-setup"></a>Configuração de Unificação de recuperação de execução de Site

1.  Execute o arquivo de instalação de configuração de Unificação.
2.  Antes de **começar** , selecione **instalar o servidor de configuração e processo**.

    ![Antes de começar](./media/site-recovery-vmware-to-azure/combined-wiz1.png)

3. Na **Licença de Software de terceiros** , clique em **aceito** para baixar e instalar MySQL.

    ![Terceiro = software de terceiros](./media/site-recovery-vmware-to-azure/combined-wiz105.PNG)

4. No **registro** procure e selecione a chave do registro baixado do cofre.

    ![Registro](./media/site-recovery-vmware-to-azure/combined-wiz3.png)

5. Em **Configurações de Internet** Especifica como o provedor em execução no servidor de configuração se conectará a recuperação de Site do Azure pela internet.

    - Se você quiser conectar-se com o proxy que está configurado na máquina selecione **conectar com configurações de proxy existentes**.
    - Se quiser que o provedor para conectar-se diretamente selecione **conectar diretamente sem um proxy**.
    - Se o proxy existente requer autenticação, ou que você deseja usar um proxy personalizado para a conexão do provedor, selecione **conectar-se com configurações personalizadas de proxy**.
        - Se você usar um proxy personalizado, que você precisará especificar o endereço, porta e credenciais
        - Se você estiver usando um proxy, que você deve ter permissão já as URLs descritas em [pré-requisitos](#configuration-server-prerequisites).

    ![Firewall](./media/site-recovery-vmware-to-azure/combined-wiz4.png)

6. **Verificação de pré-requisitos** configuração executa uma verificação para certificar-se de que a instalação pode executar. Se for exibido um aviso sobre a **sincronização de tempo globais verificar** verificar que a hora no relógio do sistema (configurações de**Data e hora** ) é a mesma que o fuso horário.

    ![Pré-requisitos](./media/site-recovery-vmware-to-azure/combined-wiz5.png)

7. Na **Configuração de MySQL** crie credenciais para logon a instância do servidor MySQL que será instalada.

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz6.png)

8. Em **Detalhes do ambiente** selecionar se você vai replicar VMs VMware. Se você estiver, a instalação verifica se PowerCLI 6.0 está instalado.

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz7.png)

9. Em selecionar **Instalar o local** onde você deseja instalar os binários e armazene o cache. Você pode selecionar uma unidade que tenha pelo menos 5 GB de armazenamento disponível, mas recomendamos uma unidade de cache com pelo menos 600 GB de espaço livre.

    ![Local de instalação](./media/site-recovery-vmware-to-azure/combined-wiz8.png)

10. Em **Seleção de rede** , especifique o ouvinte (adaptador de rede e porta SSL) em que o servidor de configuração será enviar e receber dados de replicação. Você pode modificar o padrão porta (9443). Além dessa porta, a porta 443 será usada por um servidor web que organiza operações de replicação. 443 não devem ser usados para receber o tráfego de replicação.


    ![Seleção de rede](./media/site-recovery-vmware-to-azure/combined-wiz9.png)



11.  Em **Resumo** , revise as informações e clique em **instalar**. Quando a instalação terminar uma senha é gerada. Você precisará delas quando você habilita replicação então copiá-lo e mantê-lo em um local seguro.

    ![Resumo](./media/site-recovery-vmware-to-azure/combined-wiz10.png)

12.  Após termina de registro do servidor é exibido nas **configurações** > **servidores** blade no cofre.



#### <a name="run-setup-from-the-command-line"></a>Executar a configuração da linha de comando

Você pode configurar o servidor de configuração da linha de comando:

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Parâmetros:

- / ServerMode: obrigatório. Especifica se a configuração e o processo de servidores devem ser instalados ou somente o servidor de processo. Valores de entrada: CS, PS.
- InstallLocation: obrigatório. A pasta na qual os componentes são instalados.
- / MySQLCredsFilePath. Obrigatório. O caminho do arquivo no qual as credenciais do servidor MySQL estão armazenadas. O arquivo deve ser neste formato:
    - [MySQLCredentials]
    - MySQLRootPassword = "<Password>"
    - MySQLUserPassword = "<Password>"
- / VaultCredsFilePath. Obrigatório. O local do arquivo de credenciais do cofre
- / EnvType. Obrigatório. O tipo de instalação. Valores: VMware, NonVMware
- / PSIP e /CSIP. Obrigatório. O endereço IP do servidor de processo e servidor de configuração.
- / PassphraseFilePath. Obrigatório. O local do arquivo de senha.
- / BypassProxy. Opcional. Especifica que o servidor de configuração se conecta ao Azure sem um proxy.
- / ProxySettingsFilePath. Opcional. Configurações de proxy (o proxy padrão requer autenticação, ou um proxy personalizado). O arquivo deve ser neste formato:
    - [ProxySettings]
    - ProxyAuthentication = "Sim/não"
    - Proxy IP = "endereço IP >"
    - ProxyPort = "<Port>"
    - ProxyUserName = "<User Name>"
    - ProxyPassword = "<Password>"
- DataTransferSecurePort. Opcional. Número da porta a ser usada para dados de replicação.
- SkipSpaceCheck. Opcional. Ignorar a verificação de espaço para cache.
- AcceptThirdpartyEULA. Obrigatório. Sinalizador indica a aceitação de terceiros EULA.
- ShowThirdpartyEULA. Obrigatório. Exibe EULA de terceiros. Se fornecidos como entrada todos os outros parâmetros serão ignorados.

### <a name="add-the-vmware-account-used-for-automatic-discovery"></a>Adicionar a conta de VMware usada para descoberta automática

 Quando você preparado para implantação, você deve ter [criado uma conta de VMware](#prepare-an-account-for-automatic-discovery) que recuperação do Site pode usar para descoberta automática. Adicione esta conta da seguinte maneira:

1. Abra **CSPSConfigtool.exe**. Ele está disponível como um atalho na área de trabalho e localizado na pasta de \home\svsystems\bin [local de instalação].
2. Clique em **Gerenciar contas** > **Adicionar conta**.

    ![Adicionar conta](./media/site-recovery-vmware-to-azure/credentials1.png)

3. Em **Detalhes da conta** , adicione a conta que será usada para a descoberta automática. Observe que pode levar 15 minutos ou mais para o nome da conta apareça no portal. Para atualizar imediatamente, clique em **Servidores de configuração** > nome do servidor > **Atualizar servidor**.

    ![Detalhes](./media/site-recovery-vmware-to-azure/credentials2.png)

### <a name="connect-to-vsphere-hosts-and-vcenter-servers"></a>Conectar a vSphere hosts e servidores de vCenter

Se você estiver replicar VMs VMware conectar ao vSphere hosts e servidores vCenter.

1. Verifique se o servidor de configuração possui acesso de rede para os hosts vSphere e servidores de vCenter.
2. Clique em **Preparar infraestrutura** > **fonte**. Na **fonte de preparar** selecione o servidor de configuração e clique em **+ vCenter** para adicionar um servidor de host ou vCenter vSphere.
3. Em **Adicionar vCenter** especifique um nome amigável para o servidor de host ou vCenter vSphere e especifique o endereço IP ou o FQDN do servidor. Deixe a porta como 443, a menos que seus servidores VMware são configurados para escutar solicitações em uma porta diferente. Selecione a conta que será usada para conectar ao servidor VMware. Clique em **Okey**.

    ![VMware](./media/site-recovery-vmware-to-azure/vmware-server.png)

    >[AZURE.NOTE] Se você estiver adicionando vCenter servidor ou host de vSphere com uma conta que não tem privilégios de administrador no servidor vCenter ou host, verifique se a conta tem estes privilégios habilitados: data center, armazenamento de dados, pasta, Host, rede, recursos, Virtual da máquina, vSphere alternar distribuído. Além disso, o servidor de vCenter precisa do privilégio de modos de exibição de armazenamento.

Recuperação de site conecta-se aos servidores VMware usando as configurações que você especificou e descobre VMs.

## <a name="step-3-set-up-the-target-environment"></a>Etapa 3: Configurar o ambiente de destino

Verifique se que você tem uma conta de armazenamento para replicação e uma rede Azure ao qual o Azure VMs conectará após failover.

1.  Clique em **Preparar infraestrutura** > **destino** e selecione a assinatura do Azure que você deseja usar.
2.  Especifique o modelo de implantação que você deseja usar para VMs após failover.
3.  Recuperação de site verifica se você tem uma ou mais contas de armazenamento do Azure compatível e redes.

    ![Destino](./media/site-recovery-vmware-to-azure/gs-target.png)

4.  Se você ainda não tiver criado uma conta de armazenamento e você deseja criá-la usando BRAÇO clique **+ conta de armazenamento** para fazer esse embutida.  Na lâmina **Criar conta de armazenamento** , especifique um nome de conta, tipo, assinatura e local. A conta deve ser na mesma região como o Cofre de serviços de recuperação.

    ![Armazenamento](./media/site-recovery-vmware-to-azure/gs-createstorage.png)

    Observe que:

    - Se você quiser criar uma conta de armazenamento usando o modelo clássico você vai fazer isso no portal do Azure. [Saiba Mais](../storage/storage-create-storage-account-classic-portal.md)
    - Se você estiver usando uma conta de armazenamento premium para dados duplicados, para que você precisará configurar uma conta padrão de armazenamento adicional replicação de armazenamento registra que alterações contínuas captura dados locais.

    > [AZURE.NOTE] Proteção para contas de armazenamento premium na Índia Central e do Sul Índia não é suportada atualmente.

4.  Selecione uma rede Azure. Se você ainda não tiver criado uma rede e você desejar fazer isso usando BRAÇO clique **+ rede** para fazer esse embutida. Na lâmina **Criar rede virtual** , especifique um nome de rede, intervalo de endereços, detalhes de sub-rede, assinatura e local. A rede deve estar no mesmo local que o Cofre de serviços de recuperação.

    ![Rede](./media/site-recovery-vmware-to-azure/gs-createnetwork.png)

    Se você quiser criar uma rede usando o modelo clássico você vai fazer isso no portal do Azure. [Saiba mais](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

## <a name="step-4-set-up-replication-settings"></a>Etapa 4: Configurar as configurações de replicação

1. Para criar uma nova replicação política clique **Preparar infraestrutura** > **Configurações de replicação** > **+ criar e associar**.
2. Em **criar e associar diretiva** especifique um nome de política.
3. No **limite de RPO**: especifique o limite RPO. Alertas serão geradas quando replicação contínua excede esse limite.
5. Em **retenção de ponto de recuperação**, especifique em horas quanto tempo a janela de retenção será para cada ponto de recuperação. Máquinas protegidas podem ser recuperadas a qualquer ponto dentro de uma janela. Para retenção de 24 horas é suportado para máquinas replicadas para armazenamento de premium.
6. No **aplicativo consistente frequência de instantâneo**, especifique com que frequência (em minutos) que contém instantâneos consistentes com o aplicativo de pontos de recuperação serão criados.
7. Quando você cria uma política de replicação, por padrão uma política correspondente é automaticamente criada para failback. Por exemplo, se a política de replicação for **política de representante** a política de failback será o **representante de política de failback**. Essa política não é usada até você iniciar um failback.  
8. Clique em **Okey** para criar a política.

    ![Política de replicação](./media/site-recovery-vmware-to-azure/gs-replication2.png)

9. Quando você cria uma nova política automaticamente associado com o servidor de configuração. Clique em **Okey**.

    ![Política de replicação](./media/site-recovery-vmware-to-azure/gs-replication3.png)


## <a name="step-5-capacity-planning"></a>Etapa 5: Planejamento da capacidade

Agora que você tem seu basic infraestrutura configurar você pode pensar sobre o planejamento de capacidade e descobrir se você precisa de recursos adicionais.

Recuperação de site fornece um planejador de capacidade para ajudá-lo a alocar os recursos certos para seu ambiente de origem, os componentes de recuperação do site, rede e armazenamento. Você pode executar o planejador no modo rápido para estimativas com base em um número médio de VMs, discos e armazenamento ou no modo detalhado, na qual você vai entrada figuras no nível de carga de trabalho. Antes de começar você precisará:

- Reunir informações sobre o ambiente de replicação, inclusive VMs, discos por VMs e armazenamento por disco.
- Estime a taxa de alteração (rotatividade) diária que terá para dados replicados. Você pode usar a [capacidade de vSphere aparelho de planejamento](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) para ajudá-lo a fazer isso.

1.  Clique em **Baixar** para baixar a ferramenta e execute-o. [Leia o artigo](site-recovery-capacity-planner.md) que acompanha a ferramenta.
2.  Quando você terminar selecione **Sim** **você concluiu planejamento da capacidade?**

    ![Planejamento da capacidade](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)

A tabela a seguir captura um número de pontos para ajudá-lo com a capacidade de planejamento para esse cenário.


**Componente** | **Detalhes**
--- | --- | ---
**Replicação** | **Taxa de alteração de máxima diariamente**— uma máquina protegida só pode usar um servidor de processo e um servidor de um único processo pode manipular diária até 2 TB de taxa de alterações. Portanto, a 2 TB é que o máximo de dados diário alterar taxa que tem suporte para uma máquina protegida.<br/><br/> **Produtividade máximo**— uma máquina replicada pode pertencer a uma conta de armazenamento no Azure. Uma conta de armazenamento padrão pode lidar com um máximo de 20.000 solicitações por segundo e recomendamos que você mantenha o número de IOPS em um computador de origem a 20.000. Por exemplo se você tiver uma máquina de origem com 5 discos e cada disco gera 120 IOPS (tamanho de 8K) na origem será dentro do Azure limite IOPS de disco de 500 por. O número de contas de armazenamento obrigatórias = fonte total IOPs/20000.
**Servidor de configuração** | O servidor de configuração deve ser capaz de lidar com a capacidade de taxa de alteração diária em todas as cargas de trabalho em execução em máquinas protegidas e precisa de largura de banda suficiente para replicar continuamente os dados para armazenamento do Azure.<br/><br/> Como uma prática recomendada é recomendável que o servidor de configuração estar localizado na mesma rede e segmento LAN como as máquinas que você deseja proteger. Ele pode estar localizado em uma rede diferente, mas máquinas que você deseja proteger devem ter visibilidade de rede L3-lo.<br/><br/> Recomendações de tamanho para o servidor de configuração são resumidas na tabela a seguir.
**Servidor de processo** | O primeiro servidor de processo é instalado por padrão no servidor de configuração. Você pode implantar servidores de processo adicionais para dimensionar seu ambiente. Observe que:<br/><br/> O servidor processo recebe dados de replicação de máquinas protegidas e otimiza-lo com o cache, compactação e criptografia antes de enviar para o Azure. Máquina do servidor de processo deve ter recursos suficientes para realizar essas tarefas.<br/><br/> O servidor de processo usa cache baseado em disco. Recomendamos que um disco de cache separado de 600 GB ou mais para lidar com as alterações de dados armazenadas em caso de interrupção de afunilamento de rede.

### <a name="size-recommendations-for-the-configuration-server"></a>Recomendações de tamanho para o servidor de configuração

**CPU** | **Memória** | **Tamanho do cache de disco** | **Taxa de alteração de dados** | **Máquinas protegidas**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 cores @ 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | Duplicar menos de 100 máquinas.
12 vCPUs (2 sockets * 6 cores @ 2,5 GHz) | 18 GB | 600 GB | 500 GB a 1 TB | Replica entre máquinas de 100-150.
16 vCPUs (2 sockets * 8 cores @ 2,5 GHz) | 32 GB | 1 TB | 1 TB a 2 TB | Replica entre máquinas de 150-200.
Implantar outro servidor de processo | | | > 2 TB | Implante servidores de processo adicionais se você estiver replicar máquinas mais de 200, ou se alteram os dados diários taxa exceder 2 TB.

Onde:

- Cada máquina de origem é configurada com 3 discos de 100 GB.
- Usamos armazenamento benchmarking de 8 unidades SAS de 10 K RPM com RAID 10 para as medidas de disco do cache.

### <a name="size-recommendations-for-the-process-server"></a>Recomendações de tamanho do servidor de processo

Se você precisar proteger mais de 200 máquinas ou taxa de alteração diária for maior que 2 TB, você pode adicionar servidores de processo adicional para lidar com a carga de replicação. Para dimensionar a você pode:

- Aumente o número de servidores de configuração. Por exemplo, você pode proteger até 400 máquinas com dois servidores de configuração.
- Adicionar servidores processo adicionais e usá-los para lidar com o tráfego em vez de (ou além) o servidor de configuração.

Esta tabela descreve um cenário em que:

- Você não estiver planejando usar o servidor de configuração como um processo.
- Você configurou um servidor de processo adicional.
- Você já configurar máquinas virtuais protegidas para usar o servidor de processo adicional.
- Cada máquina de origem protegido está configurada com três discos de 100 GB.

**Servidor de configuração** | **Servidor processo adicionais**| **Tamanho do cache de disco** | **Taxa de alteração de dados** | **Máquinas protegidas**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 cores @ 2,5 GHz), 16 GB de memória | 4 vCPUs (2 sockets * 2 cores @ 2,5 GHz), 8 GB de memória | 300 GB | 250 GB ou menos | Duplicar máquinas 85 ou menos.
8 vCPUs (2 sockets * 4 cores @ 2,5 GHz), 16 GB de memória | 8 vCPUs (2 sockets * 4 cores @ 2,5 GHz), 12 GB de memória | 600 GB | 250 GB a 1 TB | Replica entre máquinas 85-150.
12 vCPUs (2 sockets * 6 cores @ 2,5 GHz), 18 GB de memória | 12 vCPUs (2 sockets * 6 cores @ 2,5 GHz) 24 GB de memória | 1 TB | 1 TB a 2 TB | Replica entre máquinas de 150-225.


A maneira na qual você dimensiona seus servidores será dependem de sua preferência para uma escala para cima ou dimensionar modelo.  Você crescerão Implantando alguns configuração de ponta e servidores de processo ou dimensionar implantando servidores mais com menos recursos. Por exemplo: se você precisar proteger 220 máquinas você poderia siga um destes procedimentos:

- Configurar o servidor de configuração com 12vCPU, 18 GB de memória, um servidor de processo adicional com 12vCPU, 24 GB de memória e configurar máquinas protegidas para usar somente o servidor de processo adicional.
- Alternativamente você pode configurar dois servidores de configuração (2 x 8vCPU, 16 GB de RAM) e dois servidores de processo adicional (1 x 8vCPU) e 4vCPU x1 lidar com 135 + 85 máquinas (220) e configurar máquinas protegidas para usar apenas os servidores de processo adicional.

[Siga estas instruções](#deploy-additional-process-servers) para configurar um servidor de processo adicional.

### <a name="network-bandwidth-considerations"></a>Considerações de largura de banda de rede

Você pode usar a ferramenta de Planejador de capacidade para calcular a largura de banda que necessária para replicação (replicação inicial e, em seguida, delta). Para controlar a quantidade de uso de largura de banda para replicação, você tem algumas opções:

- **Reduzir largura de banda**: o tráfego VMware replica para Azure passa através de um servidor de processo específico. Você pode reduzir a largura de banda nas máquinas executando como servidores de processo.
- **Influenciar a largura de banda**: você pode influenciar a largura de banda usada para replicação usando algumas das chaves do registro:
    - O valor de registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM** Especifica o número de segmentos que são usados para transferir dados (replicação inicial ou delta) de um disco. Um valor mais alto aumenta a largura de banda de rede usada para replicação.
    - O **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DownloadThreadsPerVM** Especifica o número de segmentos usado para transferir dados durante o failback.

#### <a name="throttle-bandwidth"></a>Reduzir largura de banda

1. Abra o snap-in do Microsoft Azure Backup MMC na máquina atuando como o servidor de processo. Por padrão, um atalho para o Microsoft Azure Backup está disponível na área de trabalho ou em C:\Program Files\Microsoft Azure recuperação serviços Agent\bin\wabadmin.
2. O snap-in, clique em **Alterar propriedades**.

    ![Reduzir largura de banda](./media/site-recovery-vmware-to-azure/throttle1.png)

3. Na guia **Throttling** selecione **Habilitar otimização para operações de backup do uso de largura de banda de internet**e definir os limites de trabalho e não-trabalho de horas. Intervalos válidos são de 512 Kbps a 102 Mbps por segundo.

    ![Reduzir largura de banda](./media/site-recovery-vmware-to-azure/throttle2.png)

Você também pode usar o cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para definir a otimização. Aqui está um exemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting-NoThrottle** indica que nenhuma limitação é necessária.


#### <a name="influence-network-bandwidth"></a>Largura de banda de rede de influência

1. Navegue até **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**o registro.
    - Para influenciar o tráfego de largura de banda em um disco replicação, modifique o valor do **UploadThreadsPerVM**ou crie a chave se ele não existir.
    - Para influenciar a largura de banda para o tráfego de failback do Azure, modifique o valor **DownloadThreadsPerVM**.
2. O valor padrão é 4. Em uma rede "overprovisioned", essas chaves do registro devem ser alteradas dos valores padrão. O máximo é 32. Monitorar o tráfego para otimizar o valor.

## <a name="step-6-replicate-applications"></a>Etapa 6: Replicar aplicativos

Certifique-se de que máquinas que você deseja duplicar estão preparadas para instalação do serviço de mobilidade e habilitar replicação.

### <a name="install-the-mobility-service"></a>Instalar o serviço de mobilidade

Ativar a proteção para máquinas virtuais e servidores físicos a primeira etapa é instalar o serviço de mobilidade. Você pode fazer isso em algumas maneiras:

- **Envio de servidor de processo**: quando você habilita a replicação em um computador, notificações por push e instalar o componente de serviço de mobilidade do servidor processo. Observe que a instalação por envio não ocorre se máquinas já estiver executando uma versão de cima-todate do componente.
- **Envio de Enterprise**: instalar automaticamente o componente usando o processo de envio de empresa como WSUS ou Gerenciador de configuração do System Center ou [automação do Azure e configuração de estado desejado](./site-recovery-automate-mobility-service-install.md). Configure o servidor de configuração antes de fazer isso.
- **Instalação manual**: instalar o componente manualmente em cada máquina que você deseja duplicar. Configure o servidor de configuração antes de fazer isso.


#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Preparar para envio automático em máquinas Windows

Veja aqui como preparar máquinas do Windows para que o serviço de mobilidade pode ser instalado automaticamente pelo servidor de processo.

1.  Crie uma conta que pode ser usada pelo servidor de processo para acessar o computador. A conta deve ter privilégios de administrador (local ou domínio), e ele é usado somente para a instalação de envio.

    >[AZURE.NOTE] Se você não estiver usando uma conta de domínio, você precisará desativar o controle de acesso de usuário remoto na máquina local. Para fazer isso, no registro em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, adicione a entrada DWORD LocalAccountTokenFilterPolicy com um valor de 1. Para adicionar a entrada do registro de um tipo CLI **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  O Firewall do Windows da máquina que você deseja proteger, selecione **Permitir um aplicativo ou recurso pelo Firewall**. Habilite o **compartilhamento de arquivo e impressora** e **Instrumentação de gerenciamento do Windows**. Para máquinas que fazem parte de um domínio que você pode definir as configurações de firewall com um GPO.

    ![Configurações de firewall](./media/site-recovery-vmware-to-azure/mobility1.png)

2. Adicione a conta que você criou:

    - Abra **cspsconfigtool**. Ele está disponível como um atalho na área de trabalho e localizado na pasta de \home\svsystems\bin [local de instalação].
    - Na guia **Gerenciar contas** , clique em **Adicionar conta**.
    - Adicione a conta que você criou. Depois de adicionar a conta, você precisará fornecer as credenciais quando você habilita a replicação para um computador.


#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Preparar para envio automático em servidores Linux

1.  Certifique-se de que o computador Linux que você deseja proteger é suportado conforme descrito na [máquina protegida pré-requisitos](#protected-machine-prerequisites). Certifique-se de que não há conectividade entre o computador Linux e o servidor do processo de rede.

2.  Crie uma conta que pode ser usada pelo servidor de processo para acessar o computador. A conta deve ser um usuário raiz no servidor Linux de origem e ele é usado somente para a instalação de envio.

    - Abra **cspsconfigtool**. Ele está disponível como um atalho na área de trabalho e localizado na pasta de \home\svsystems\bin [local de instalação].
    - Na guia **Gerenciar contas** , clique em **Adicionar conta**.
    - Adicione a conta que você criou. Depois de adicionar a conta, você precisará fornecer as credenciais quando você habilita a replicação para um computador.

3.  Verifique se o arquivo /etc/hosts no servidor Linux de origem contém as entradas que mapeiam o nome do host local para endereços IP associados a todos os adaptadores de rede.
4.  Instale o openssh mais recente, openssh-servidor, openssl pacotes na máquina que você deseja duplicar.
5.  Certifique-se de que SSH está ativado e em execução na porta 22.
6.  Habilite a autenticação de subsistema e a senha SFTP no arquivo sshd_config da seguinte maneira:

    - Faça login como raiz.
    - No arquivo de /etc/ssh/sshd_config, localize a linha que começa com **PasswordAuthentication**.
    - Tire comentários a linha e altere o valor de **não** para **Sim**.
    - Localize a linha que começa com **subsistema** e Tire comentários a linha.

        ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)


### <a name="install-the-mobility-service-manually"></a>Instalar o serviço de mobilidade manualmente

Os instaladores estão disponíveis no servidor de configuração **C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

Sistema operacional de origem | Arquivo de instalação do serviço de mobilidade
--- | ---
Windows Server (somente 64 bits) | Microsoft-ASR_UA_9. *.0.0_Windows_* release.exe
CentOS 6.4, 6.5, 6.6 (somente 64 bits) | Microsoft-ASR_UA_9. *.0.0_RHEL6-64_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (somente 64 bits) | Microsoft-ASR_UA_9. *.0.0_SLES11-SP3-64_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (somente 64 bits) | Microsoft-ASR_UA_9. *.0.0_OL6-64_*release.tar.gz


#### <a name="install-mobility-service-on-a-windows-server"></a>Instalar o serviço de mobilidade em um servidor Windows


1. Baixe e execute o instalador relevante.
2. Antes de **começar** , selecione **serviço de mobilidade**.

    ![Serviço de mobilidade](./media/site-recovery-vmware-to-azure/mobility3.png)

3. Na **Detalhes de configuração do servidor** , especifique o endereço IP do servidor configuração e a senha que foi gerada quando você executou a instalação unificada. Você pode recuperar a senha executando: ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – v** no servidor de configuração.

    ![Serviço de mobilidade](./media/site-recovery-vmware-to-azure/mobility6.png)

4. No **Local de instalação** , deixe a configuração padrão e clique em **Avançar** para iniciar a instalação.
5. Em **Andamento de instalação** monitorar instalação e reinicie o computador se for solicitado. Após instalar o serviço pode levar cerca de 15 minutos para status para atualizar no portal.

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>Instalar o serviço de mobilidade em um Windows server usando o prompt de comando

1. Copie o instalador para uma pasta local (digamos C:\Temp) no servidor que você deseja proteger. O instalador pode ser encontrado no servidor de configuração sob o **\home\svsystems\pushinstallsvc\repository [local de instalação]**. O pacote para sistemas operacionais do Windows terá um nome semelhante ao Microsoft-ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe
2. **Renomear** este arquivo para MobilitySvcInstaller.exe
3. Execute o seguinte comando para extrair o instalador MSI </br>

        C:\> cd C:\Temp
        C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted
        C:\Temp> cd Extracted
        C:\Temp\Extracted> UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>

#####<a name="full-command-line-syntax"></a>Sintaxe completa linha de comando

    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**Parâmetros**

- **/Role:** Obrigatório. Especifica se o serviço de mobilidade deve ser instalado. Agente de valores de entrada | MasterTarget
- **/InstallLocation:** Obrigatório. Especifica onde instalar o serviço.
- **/PassphraseFilePath:** Obrigatório. A senha do servidor de configuração.
- **/LogFilePath:** Obrigatório. Local onde os arquivos de log de instalação devem ser criados.



#### <a name="uninstall-mobility-service-manually"></a>Desinstalar manualmente o serviço de mobilidade

Serviço de mobilidade pode ser desinstalado usando o programa de remover adicionar no painel de controle ou linha de comando.

O comando para desinstalar o serviço de mobilidade usando linha de comando é

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}


#### <a name="install-mobility-service-on-a-linux-server-using-command-line"></a>Instalar o serviço de mobilidade em um servidor Linux usando linha de comando

1. Copie o arquivo tar apropriado com base na tabela acima na máquina de Linux que você deseja duplicar.
2. Abra um programa shell e extrair o arquivo tar compactado para um caminho local executando:`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Crie um arquivo de passphrase.txt no diretório local para o qual foi extraído o conteúdo do arquivo tar. Para fazer isso copiar a senha de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase no servidor de configuração e salvá-lo no passphrase.txt executando *`echo <passphrase> >passphrase.txt`* no shell.
4. Instalar o serviço de mobilidade executando *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Especificar o endereço IP interno do servidor configuração e verifique se a porta 443 está selecionada. Após instalar o serviço pode levar cerca de 15 minutos para status para atualizar no portal.

**Você também pode instalar da linha de comando**:

1. Copiar a senha de C:\Program Files (x86) \InMage Systems\private\connection no servidor de configuração e salve-o como "passphrase.txt" no servidor de configuração. Em seguida, execute esses comandos. Em nosso exemplo o endereço IP do servidor de configuração é 104.40.75.37 e a porta HTTPS deve ser 443:

Para instalar em um servidor de produção:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Para instalar no servidor de destino mestre:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


### <a name="enable-replication"></a>Habilitar a replicação

#### <a name="before-you-start"></a>Antes de começar

Se você estiver replicar VMware virtual máquinas Observe o seguinte:

- VMs VMware forem descobertas a cada 15 minutos e ele pode levar 15 minutos ou mais para que apareçam no portal após a detecção. Da mesma forma descoberta pode levar 15 minutos ou mais, ao adicionar um novo host de servidor ou vSphere vCenter.
- Alterações de ambiente na máquina virtual (como a instalação de ferramentas de VMware), também podem levar 15 minutos ou mais para ser atualizado no portal.
- Você pode verificar a última vez descoberta VMs VMware no **Último contato no** campo para o host do servidor/vSphere vCenter na lâmina **Servidores de configuração** .
- Para adicionar máquinas para replicação sem aguardar a descoberta agendada, realce o servidor de configuração (não clique nele) e clique no botão **Atualizar** .
- Quando você habilita a replicação, se o computador está preparado automaticamente o servidor processo instala o serviço de mobilidade nele.

#### <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

Quando você habilita a replicação, por padrão, todos os discos em um computador são duplicados. Você pode excluir discos da replicação. Por exemplo, talvez você não queira replicar discos com dados temporários ou dados que tem atualizados sempre que um computador ou aplicativo for reiniciado (por exemplo Pagefile ou tempdb do SQL Server). Se você deseja excluir discos Observe que:

- Você só pode excluir discos que já tem o serviço de mobilidade instalado. Você precisará [instalar manualmente o serviço de mobilidade](#install-the-mobility-service-manually) porque o serviço de mobilidade só é instalado usando o mecanismo de envio após replicação está habilitada.
- Apenas discos básicos podem ser excluídos da replicação. Você não pode excluir o sistema operacional ou discos dinâmicos.
- Depois de replicação está habilitada você não pode adicionar ou remover discos para replicação. Se você quiser adicionar ou excluir um disco você precisará desativar a proteção para o computador e, em seguida, reativá-lo.
- Se você excluir um disco necessários para um aplicativo operar, após o failover Azure você precisará criá-lo manualmente no Azure para que o aplicativo replicado possa executar. Alternativamente você pode integrar automação Azure em um plano de recuperação para criar o disco durante o failover da máquina.
- Discos que você criar manualmente no Azure resultado serão a falha novamente. Por exemplo se você falha em três discos e cria dois diretamente no Azure, todos os cinco ser falha novamente. Você não pode excluir discos criados manualmente de failback.

**Agora, habilitar replicação da seguinte maneira**:

1. Clique em **etapa 2: replicar o aplicativo** > **fonte**. Depois de habilitado replicação pela primeira vez que você vai clique **+ replicar** no cofre para permitir a replicação para máquinas adicionais.
2. Na **origem** lâmina > **fonte** selecionar o servidor de configuração.
3. Em **tipo de máquina** selecione **máquinas virtuais** ou **Máquinas físicas**.
4. No **vCenter/vSphere Hypervisor** selecione servidor vCenter que gerencia o host vSphere ou selecione o host. Essa configuração não é relevante se você estiver replicar máquinas físicas.
5. Selecione o servidor de processo. Se você ainda não tiver criado quaisquer servidores de processo adicional este será o nome do servidor de configuração. Clique em **Okey**.

    ![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. Em **destino** selecione a assinatura do cofre e no **modelo de implantação de postagem failover** , selecione o modelo (gerenciamento de recurso ou clássico) que você deseja usar no Azure após failover.
7. Selecione a conta de armazenamento do Azure que você usará para replicar dados. Observe que:

    - Você pode selecionar uma conta de armazenamento padrão ou premium. Se você selecionar uma conta de premium, que você precisará especificar uma conta padrão de armazenamento adicional para logs de replicação contínua. Contas devem estar na mesma região como o Cofre de serviços de recuperação.
    - Se você quiser usar uma conta de armazenamento diferentes daqueles você ter que você pode [criar uma](#set-up-an-azure-storage-account). Para criar um armazenamento conta usando o modelo ARM, clique em **Criar novo**. Se você quiser criar uma conta de armazenamento usando o modelo clássico você fará essa [no portal do Azure](../storage/storage-create-storage-account-classic-portal.md).

8. Selecione a rede do Azure e sub-rede ao qual Azure VMs conectará quando eles estão girados backup após failover. A rede deve estar na mesma região como o Cofre de serviços de recuperação. Selecione **Configurar agora para máquinas selecionadas** para aplicar a configuração de rede a todas as máquinas que você selecionar para proteção. Selecione **Configurar depois** para selecionar a rede Azure por máquina. Se você não tiver uma rede, você precisará [criar uma](#set-up-an-azure-network). Para criar uma rede usando o modelo ARM, clique em **Criar novo**. Se você quiser criar uma rede usando o modelo clássico você fará essa [no portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selecione uma sub-rede se aplicável. Clique em **Okey**.

    ![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-replication3.png)

9. Em **máquinas virtuais** > **Selecione máquinas virtuais** clique e selecione cada máquina que você deseja duplicar. Você só poderá selecionar máquinas qual replicação pode ser habilitada. Clique em **Okey**.

    ![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-replication5.png)

10. Nas **Propriedades** > **Configurar propriedades**, selecione a conta que será usada automaticamente pelo servidor processo para instala o serviço de mobilidade na máquina. Por padrão, todos os discos são duplicados. Clique em **Todos os discos** e desmarque qualquer disco que não deseja duplicar. Clique em **Okey**. Você pode definir propriedades adicionais posteriormente.

    ![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-replication6.png)

11. Nas **configurações de replicação** > **Configurar definições de replicação** Verifique se a política de replicação correta está selecionada. Você pode modificar as configurações de política de replicação nas **configurações** > **políticas de replicação** > nome da política > **Editar configurações**. Alterações que você aplicar a uma política serão aplicadas máquinas replica e novo.

12. Ative a **consistência de Multi-máquina virtual** se você quiser coletar máquinas em um grupo de replicação e especifique um nome para o grupo. Clique em **Okey**. Observe que:

    - Máquinas em replicação agrupar replicar e compartilharam pontos de recuperação consistentes com falha e consistentes com o aplicativo quando elas falham sobre.
    - Recomendamos que você reunir VMs e servidores físicos espelho suas cargas de trabalho. Habilitando consistência multi-máquina virtual pode afetar o desempenho de carga de trabalho e deve ser usada somente se máquinas estiverem executando a mesma carga de trabalho e você precisa consistência.

    ![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-replication7.png)

13. Clique em **habilitar replicação**. Você pode controlar o andamento do trabalho **Habilitar proteção** nas **configurações** > **trabalhos** > **Trabalhos de recuperação de Site**. Após o trabalho de **Proteção de finalizar** é executado máquina está pronto para failover.

> [AZURE.NOTE] Se o computador está preparado para instalação por envio que o componente de serviço de mobilidade será instalado quando proteção está habilitada. Após o componente está instalado na máquina que inicia um trabalho de proteção e falhar. Após a falha é necessário reiniciar manualmente cada máquina. Após a reinicialização do trabalho de proteção começa novamente e replicação inicial ocorre.

### <a name="view-and-manage-vm-properties"></a>Exibir e gerenciar propriedades de máquina virtual

Recomendamos que você verifique se as propriedades da máquina de origem. Lembre-se de que o nome de máquina virtual do Azure deve estar em conformidade com os [requisitos do Azure máquina virtual](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Clique em **configurações** > **replicado itens** > e selecione o computador. A lâmina **Essentials** mostra informações sobre configurações de máquinas e status.

2. Nas **Propriedades** que você pode exibir informações de replicação e failover para a máquina virtual.

    ![Habilitar a replicação](./media/site-recovery-vmware-to-azure/test-failover2.png)

3. Em **rede e computação** > a**Calcular propriedades** que você pode especificar o tamanho de nome e destino de máquina virtual do Azure. Modifique o nome em conformidade com requisitos Azure se for necessário.
Você também pode exibir e adicionar informações sobre a rede de destino, a sub-rede e o endereço IP que será atribuído para a máquina virtual do Azure. Observe o seguinte:

    - Você pode definir o endereço IP de destino. Se você não fornecer um endereço, a falha máquina usará DHCP. Se você definir um endereço que não está disponível em failover, o failover não funcionará. O mesmo endereço IP de destino pode ser usado para testar failover se o endereço está disponível na rede de failover de teste.
    - O número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino, da seguinte maneira:

        - Se o número de adaptadores de rede no computador de origem for menor que ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores como fonte.
        - Se o número de adaptadores da máquina virtual de origem exceder o número permitido para o tamanho de destino e em seguida, o tamanho máximo de destino será usado.
        - Por exemplo, se uma máquina de origem tem dois adaptadores de rede e o tamanho da máquina de destino dá suporte a quatro, máquina de destino terá dois adaptadores. Se o computador de origem tem dois adaptadores, mas o tamanho de destino com suporte apenas dá suporte para uma máquina de destino terá apenas um adaptador.     
    - Se a máquina virtual tem vários adaptadores de rede, que ele serão conectado à mesma rede.

    ![Habilitar a replicação](./media/site-recovery-vmware-to-azure/test-failover4.png)

4. Em **discos** que você pode ver o sistema operacional e discos de dados sobre a máquina virtual que será duplicada.


## <a name="step-7-test-the-deployment"></a>Etapa 7: Testar a implantação

Para testar a implantação, você pode executar um failover de teste para uma única máquina virtual ou um plano de recuperação que contém uma ou mais máquinas virtuais.


### <a name="prepare-for-failover"></a>Preparar para failover

- Para executar um failover de teste, que recomendamos que você crie uma nova rede Azure que tem isolados da sua rede de produção Azure (Isso é comportamento padrão quando você cria uma nova rede no Azure). [Saiba mais](site-recovery-failover.md#run-a-test-failover) sobre como executar failovers de teste.
- Para obter o melhor desempenho quando não conseguir ao longo do Azure, instale o agente do Azure na máquina protegida. Ele torna a inicialização com mais rapidez e ajuda a solucionar problemas. Instale o agente [Linux](https://github.com/Azure/WALinuxAgent) ou [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) .
- Para testar completamente sua implantação, você precisará uma infraestrutura da máquina replicado funcione como esperado. Se você quiser testar o Active Directory e DNS você pode criar uma máquina virtual como controlador de domínio com o DNS e replicar isso no Azure usando a recuperação de Site do Azure. Leia mais em [Considerações de failover de teste para o Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Certifique-se de que o servidor de configuração está sendo executado. Caso contrário, failover falhará.
- Se você tiver excluído discos da replicação talvez seja necessário criar esses discos manualmente no Azure após failover para que o aplicativo é executado conforme o esperado.
- Se você desejar executar um failover não planejado em vez de um failover de teste Observe o seguinte:

    - Se possível deve desligar máquinas primárias antes de executar um failover não planejado. Isso garante que você não tiver a fonte e a réplica máquinas executando ao mesmo tempo. Se você estiver replicar VMs VMware você pode especificar que a recuperação de Site deve fazer um melhor esforço para desligar as máquinas de origem. Dependendo do estado do site principal isso talvez ou pode não funcionar. Se você estiver replicar servidores físicos que recuperação de Site não oferecer essa opção.
    - Quando você executa um failover não planejado parar replicação de dados de máquinas primárias para que qualquer delta de dados não possam ser transferido após o início de um failover não planejado. Além disso se você executar um failover não planejado em um plano de recuperação ele será executado até sua conclusão, mesmo se ocorrer um erro.

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para se conectar ao Azure VMs após failover

Se você deseja se conectar VMs Azure usando o RDP após failover, certifique-se de que você faça o seguinte:

**Na máquina local antes do failover**:

- Para obter acesso pela internet habilitar RDP, certifique-se de que as regras TCP e UDP são adicionadas para o **público**e certifique-se de que o RDP é permitida no **Firewall do Windows** -> **permitidos aplicativos e recursos** para todos os perfis.
- Para obter acesso ao longo de uma conexão de-to-site habilitar RDP na máquina e certifique-se de que o RDP é permitida no **Firewall do Windows** -> **permitidos aplicativos e recursos** para redes de **domínio** e **particular** .
- Instale o [agente de máquina virtual do Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) na máquina local.
- [Instalar manualmente o serviço de mobilidade](#install-the-mobility-service-manually) em máquinas em vez de usar o servidor processo empurrar o serviço automaticamente. Isso ocorre porque a instalação por envio só acontece depois que o computador está habilitado para replicação.
- Certifique-se de que a política de SAN do sistema operacional está definida como OnlineAll. [Saiba Mais]( https://support.microsoft.com/kb/3031135)
- Desative o serviço IPSec antes de executar o failover.

**Sobre o Azure máquina virtual após failover**:

- Adicionar um ponto de extremidade público para o protocolo RDP (porta 3389) e especifique as credenciais de login.
- Certifique-se de que você não tem quaisquer diretivas de domínio que impedem a conexão para uma máquina virtual usando um endereço público.
- Tente se conectar. Se você não consegue se conectar, verifique se a máquina virtual está em execução. Para obter mais dicas de solução de problemas leia este [artigo](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


Se quiser acessar uma VM Azure executando Linux após failover usando um Secure Shell cliente (ssh), faça o seguinte:

**Na máquina local antes do failover**:

- Certifique-se de que o serviço de Secure Shell na máquina virtual do Azure está definido para iniciar automaticamente na inicialização do sistema.
- Verifique se as regras de firewall permitiu que uma conexão SSH-lo.

**Sobre o Azure máquina virtual após failover**:

- As regras de grupo de segurança de rede na Falha ao longo de máquina virtual e a sub-rede Azure ao qual ele está conectado precisam permitir conexões de entrada para a porta SSH.
- Um ponto de extremidade público deve ser criado para permitir conexões de entrada na porta SSH (porta TCP 22 por padrão).
- Se a máquina virtual for acessada ao longo de uma conexão de VPN (via expressa ou to site VPN) o cliente pode ser usado para se conectar diretamente para a máquina virtual via SSH.

**Sobre o Azure Windows/Linux máquina virtual após failover**:

Se você tiver um grupo de segurança de rede associados a máquina Virtual ou a sub-rede ao qual a máquina pertence, verifique se o grupo de segurança de rede tem uma regra de saída para permitir que o HTTP/HTTPS. Também certificar-se de que o DNS da rede à qual máquina virtual está obtendo não ultrapassa está configurado corretamente. Else o failover poderia tempo limite com erro-'Task PreFailoverWorkflow WaitForScriptExecutionTask expirou'. Para compreender isso em detalhes, consulte a seção sobre recuperação no [monitoramento e guia de solução de problemas](site-recovery-monitoring-and-troubleshooting.md#recovery).

## <a name="run-a-test-failover"></a>Executar um failover de teste

1. Falha ao longo de uma única máquina, nas **configurações** > **Itens duplicados**, clique na máquina virtual > ícone **+ Failover de teste** .

    ![Failover de teste](./media/site-recovery-vmware-to-azure/test-failover1.png)

2. Falha ao longo de um plano de recuperação, nas **configurações** > de**Planos de recuperação**o plano de atalho > **Failover de teste**. Para criar uma plano de recuperação [siga estas instruções](site-recovery-create-recovery-plans.md).

3. Em **Teste Failover** selecione a rede Azure ao qual serão conectadas Azure VMs após houver falha.
4. Clique em **Okey** para iniciar o failover. Você pode controlar o andamento clicando na máquina virtual para abrir suas propriedades ou no trabalho **Failover de teste** em nome do cofre > **configurações** > **trabalhos** > **trabalhos de recuperação do Site**.
5. Quando o failover atinge o status de **teste concluída** , faça o seguinte:

    1. Exiba a máquina virtual de réplica no portal do Azure. Verifique se a máquina virtual for iniciado com êxito.
    2. Se estiver configurado para cima até máquinas virtuais de acesso da sua rede local, você pode iniciar uma conexão de área de trabalho remota na máquina virtual.
    3. Clique em **Testar concluída** para concluí-lo.

        ![Failover de teste](./media/site-recovery-vmware-to-azure/test-failover6.png)


    4. Clique em **anotações** para gravar e salvar observações associadas com o failover de teste.
    5. Clique em **o failover de teste é concluído** para limpar automaticamente o ambiente de teste. Depois que isso é feito o failover de teste mostrará um status de **concluída** .
    6.  Neste estágio qualquer elementos ou VMs criadas automaticamente pelo Site recuperação durante o failover de teste são excluídas. Quaisquer elementos adicionais que você criou para failover de teste não são excluídos.

    > [AZURE.NOTE] Se um failover de teste continua mais de duas semanas que ela seja concluída pela equipe.


6. Após concluir o failover também deve ser capaz de ver a réplica Azure máquina aparecem no portal do Azure > **máquinas virtuais**. Você deverá garantir que a máquina virtual é o tamanho apropriado, que tenha se conectado à rede apropriada, e que ele seja executado.
7. Se você [preparado para conexões após failover](#prepare-to-connect-to-azure-vms-after-failover) você deve ser capaz de se conectar para a máquina virtual do Azure.

## <a name="monitor-your-deployment"></a>Monitorar sua implantação

Veja aqui como você pode monitorar as definições de configuração, status e integridade para a sua implantação de recuperação do Site:

1. Clique no nome do cofre para acessar o painel **Essentials** . Neste painel, você pode trabalhos recuperação do Site, status de replicação, planos de recuperação, a integridade do servidor e eventos.  Você pode personalizar Essentials para mostrar os blocos e layouts que são mais úteis para você, incluindo o status dos outros compartimentos de Backup e recuperação do Site.<br>
![Essentials](./media/site-recovery-vmware-to-azure/essentials.png)

2. No bloco **integridade** , você pode monitorar servidores de sites (servidores VMM ou configuração) que estão tendo o problema e os eventos gerados pelo Site recuperação nas últimas 24 horas.
3. Você pode gerenciar e monitorar a replicação na **Itens duplicados**, de **Planos de recuperação**e blocos de **Trabalhos de recuperação de Site** . É possível fazer drill down em trabalhos nas **configurações** -> **trabalhos** -> **Trabalhos de recuperação de Site**.


## <a name="deploy-additional-process-servers"></a>Implantar servidores de processo adicionais

Se você tiver de dimensionar sua implantação além 200 máquinas de origem ou uma taxa de rotatividade diária total de mais de 2 TB, você precisará servidores de processo adicional para lidar com o volume de tráfego.

Verifique as [recomendações de tamanho para servidores processo](#size-recommendations-for-the-process-server) e, em seguida, siga estas instruções para configurar o servidor de processo. Após a configuração do servidor, você vai migrar máquinas de origem para usá-lo.

### <a name="install-an-additional-process-server"></a>Instalar um servidor de processo adicionais

1. Nas **configurações** > **servidores de recuperação de Site** clique no servidor de configuração > **servidor processo**.

    ![Adicionar servidor de processo](./media/site-recovery-vmware-to-azure/migrate-ps1.png)

2. Em **Tipo de servidor** , clique em **servidor de processo (local)**.

    ![Adicionar servidor de processo](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

3. Baixar o arquivo de configuração de Unificação de recuperação de Site e executá-lo para instalar o servidor de processo e registre-o no cofre.
4. Antes de **começar** , selecione **Adicionar servidores de processo adicional para dimensionar a implantação**.
5. Conclua o Assistente da mesma maneira que você fez quando você [Configurar](#step-2-set-up-the-source-environment) o servidor de configuração.

    ![Adicionar servidor de processo](./media/site-recovery-vmware-to-azure/add-ps1.png)

6. Em **Detalhes de configuração do servidor** , especifique o endereço IP do servidor de configuração e a senha. Para obter a senha executar ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** no servidor de configuração.

    ![Adicionar servidor de processo](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrar máquinas para usar o novo servidor de processo

1. Nas **configurações** > **servidores de recuperação de Site** , clique no servidor de configuração e expanda **servidores processo**.

    ![Servidor do processo de atualização](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

2. O servidor de processo em uso no momento de atalho e clique em **Alternar**.

    ![Servidor do processo de atualização](./media/site-recovery-vmware-to-azure/migrate-ps3.png)

3. Em **Selecione o servidor do processo de destino**, selecione o novo servidor de processo que você deseja usar e, em seguida, selecione as máquinas virtuais que o novo servidor de processo tratará. Clique no ícone de informações para obter informações sobre o servidor. Para ajudá-lo a fazer carregar decisões, espaço médio que tem necessário para duplicar cada máquina virtual selecionada para o novo servidor de processo é exibido. Clique na marca de seleção para começar a replicar para o novo servidor de processo.

## <a name="vmware-account-permissions"></a>Permissões de conta do VMware

O servidor de processo, possa detectar automaticamente VMs em um servidor de vCenter. Para executar a descoberta automática que você precisará [definir uma função (Azure_Site_Recovery)](#prepare-an-account-for-automatic-discovery) permitir recuperação de Site acessar o servidor VMware. Observe que, se você só precisa migrar máquinas VMware para Azure e não precisar failback do Azure, você pode definir uma função de somente leitura que é suficiente. As permissões de função necessários estão resumidas na tabela a seguir.

**Função** | **Detalhes** | **Permissões**
--- | --- | ---
Função Azure_Site_Recovery | Descoberta de VM VMware |Atribua esses privilégios do servidor de v Center:<br/><br/>Armazenamento de dados -> alocar espaço, armazenamento de dados de procurar, arquivo de nível baixo operações., remover arquivo, arquivos de máquina virtual de atualização<br/><br/>Rede -> atribuir de rede<br/><br/>Recurso -> Atribuir máquina virtual pool de recursos, migrar desligado máquina virtual, migrar da plataforma na máquina virtual<br/><br/>Tarefas -> Criar tarefa, tarefa de atualização<br/><br/>Máquina virtual -> Configuração<br/><br/>Máquina virtual -> interagir -> pergunta de resposta, dispositivo conexão., mídia configurar CD, mídia de disquete configurar, desligar Power na, instale o VMware tools<br/><br/>Estoque -> máquina virtual -> criar, registro, cancela o registro<br/><br/>Máquina virtual -> provisionamento -> Permitir download de máquina virtual, permitir que os arquivos de máquina virtual carregar<br/><br/>Máquina virtual -> instantâneos -> Remover instantâneos
função de usuário do vCenter | Descoberta de VM VMware/Failover sem desligamento da fonte máquina virtual | Atribua esses privilégios do servidor de v Center:<br/><br/>Objeto de Data Center –> propagar ao objeto filho, função = somente leitura <br/><br/>O usuário está atribuído em nível de data center e, portanto, tem acesso a todos os objetos no data center.  Se você quiser restringir o acesso, atribua a função de **nenhum acesso** com o objeto de **Propagar para filho** aos objetos filho (vSphere hosts, armazenamentos de dados, VMs e redes).
função de usuário do vCenter | Failover e failback | Atribua esses privilégios do servidor de v Center:<br/><br/>Objeto de data center – propagar ao objeto filho, função = Azure_Site_Recovery<br/><br/>O usuário está atribuído em nível de data center e, portanto, tem acesso a todos os objetos no data center.  Se você quiser restringir o acesso, atribua a função de **nenhum acesso** com a **Propagar ao objeto filho** ao objeto filho (vSphere hosts, armazenamentos de dados, VMs e redes).  
## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](site-recovery-failover.md) sobre os diferentes tipos de falha.
- [Saiba mais sobre failback](site-recovery-failback-azure-to-vmware.md) para trazer seu falha sobre máquinas em execução no Azure volta para seu ambiente local.

## <a name="third-party-software-notices-and-information"></a>Avisos de softwares de terceiros e informações

Não traduzir ou localizar

O software e firmware em execução no Microsoft produto ou serviço baseado em ou incorpora material dos projetos listados abaixo (coletivamente, "código de terceiros").  A Microsoft está o autor não original do código de terceiros.  O aviso de direitos autorais original e licença, em que a Microsoft recebido esse código de terceiros, são definidas estabelecidos abaixo.

As informações na seção A se refere componentes de código de terceiros nos projetos listados abaixo. Essas licenças e as informações são fornecidas somente para fins informativos.  Este código de terceiros está sendo relicensed a você pela Microsoft em termos de licenciamento de software da Microsoft para o Microsoft produto ou serviço.  

As informações na seção B for referente a componentes de código de terceiros que estão sendo disponibilizadas a você pela Microsoft sob os termos de licenciamento originais.

O arquivo completo pode ser encontrado no [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). A Microsoft se reserva todos os direitos não expressamente concedidos neste documento, seja por implicação, embargo ou de outra forma.
