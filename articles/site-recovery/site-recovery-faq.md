<properties
    pageTitle="Azure recuperação do Site: Perguntas frequentes sobre o | Microsoft Azure"
    description="Este artigo discute perguntas populares sobre recuperação de Site do Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="get-started-article"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/10/2016"
    ms.author="raynew"/>


# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Recuperação de Site Azure: Perguntas frequentes (FAQ)

Este artigo contém perguntas frequentes sobre recuperação de Site do Azure. Se você tiver dúvidas após ler este artigo, publique-os no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Geral

### <a name="what-does-site-recovery-do"></a>O que faz a recuperação de Site?

Recuperação de site contribui para sua estratégia de negócios desastre e continuidade recuperação (BCDR), organizar e automação da replicação de máquinas virtuais de locais e servidores físicos Azure ou um data center secundário. [Saiba mais](site-recovery-overview.md).


### <a name="what-can-site-recovery-protect"></a>O que pode proteger de recuperação de Site?

- **Máquinas virtuais Hyper-V**: recuperação de Site pode proteger qualquer carga de trabalho em execução em uma máquina virtual Hyper-V.
- **Servidores físicos**: recuperação de Site pode proteger físicos servidores que executam o Windows ou Linux.
- **Máquinas virtuais VMware**: recuperação de Site pode proteger qualquer carga de trabalho em execução em uma VM VMware.

### <a name="does-site-recovery-support-the-azure-resource-manager-model"></a>Recuperação de Site oferece suporte para o modelo do Gerenciador de recursos do Azure?

Além de recuperação de Site no portal de clássico do Azure, recuperação de Site está disponível no portal do Azure com suporte para o Gerenciador de recursos. Para a maioria dos cenários de implantação recuperação de Site no Azure portal fornece uma experiência simplificada de implantação e pode replicar VMs e servidores físicos em armazenamento clássico ou armazenamento do Gerenciador de recursos. Aqui estão as implantações compatíveis:

- [Duplicar VMs VMware ou servidores físicos para Azure no portal do Azure](site-recovery-vmware-to-azure.md)
- [Duplicar VMs Hyper-V em nuvens VMM para Azure no portal do Azure](site-recovery-vmm-to-azure.md)
- [Duplicar VMs Hyper-V (sem VMM) para Azure no portal do Azure](site-recovery-hyper-v-site-to-azure.md)
- [Duplicar VMs Hyper-V em nuvens VMM para um site secundário no portal do Azure](site-recovery-vmm-to-vmm.md)


### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>O que eu preciso no Hyper-V coordenar replicação com recuperação de Site?

Para o servidor de host Hyper-V o que você precisa depende o cenário de implantação. Confira os pré-requisitos Hyper-V em:

- [Duplicar VMs Hyper-V (sem VMM) para o Azure](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Duplicar VMs Hyper-V (com VMM) para o Azure](site-recovery-vmm-to-azure.md#before-you-start)
- [Replicação de VMs Hyper-V em um data center secundário](site-recovery-vmm-to-vmm.md#before-you-start)

- Se você estiver replicação para um centro de dados secundário, leia sobre [os sistemas operacionais convidados compatíveis para VMs Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
- Se você estiver replicar no Azure, recuperação de Site oferece suporte a todos os sistemas de operacionais convidado que são [compatíveis com o Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Posso proteger VMs quando Hyper-V está em execução em um sistema operacional cliente?

Não, VMs devem estar localizadas em um servidor de host Hyper-V que está sendo executado em um computador com suporte do Windows server. Se você precisar proteger um computador cliente que você poderia replicá-la como uma máquina física [Azure](site-recovery-vmware-to-azure.md) ou um [Data Center secundário](site-recovery-vmware-to-vmware.md).


### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Quais cargas de trabalho pode proteger com recuperação de Site?

Você pode usar a recuperação de Site para proteger a maioria das cargas de trabalho em execução em um servidor físico ou máquina virtual com suporte. Recuperação de site oferece suporte para replicação de reconhecimento de aplicativos, para que aplicativos possam ser recuperados a um estado inteligente. Ele integra-se com aplicativos Microsoft, como o SharePoint, Exchange, Dynamics, SQL Server e do Active Directory e trabalha em conjunto com principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre a proteção de carga de trabalho.


### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Hosts Hyper-V precisa estar em nuvens do VMM?

Se você deseja replicar para um data center secundário, e em seguida, VMs Hyper-V deve estar ligado Hyper-V hospeda servidores localizados em uma nuvem VMM. Se você quiser replicar para Azure, você pode replicar VMs em servidores de host Hyper-V com ou sem nuvens do VMM. [Leia mais](site-recovery-hyper-v-site-to-azure.md).

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Pode implantar o recuperação de Site com VMM se eu tiver somente um servidor VMM?

Sim. Ou você pode replicar VMs em servidores Hyper-V na nuvem VMM no Azure, ou você pode replicar entre nuvens do VMM no mesmo servidor. Para o local para replicação local, recomendamos que você tenha um servidor VMM em ambos os locais primárias e secundários.  [Leia mais](site-recovery-single-vmm.md)

### <a name="what-physical-servers-can-i-protect"></a>Quais servidores físicos pode proteger?

Você pode replicar físicos servidores que executam o Windows e Linux para Azure ou para um site secundário. [Saiba mais sobre](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) requisitos do sistema operacional.  Os mesmos requisitos se aplicam se você estiver replicação de servidores físicos para Azure, ou para um site secundário.

Observe que servidores físicos serão executado como VMs no Azure se seu servidor local falhar. Atualmente não há suporte para failback para um servidor físico no local, mas você pode falhar para uma máquina virtual em execução no Hyper-V ou VMware.


### <a name="what-vmware-vms-can-i-protect"></a>Quais VMs VMware pode proteger?

Para proteger VMs VMware será necessário um hipervisor vSphere e máquinas virtuais executando o VMware tools. Também é recomendável que você tenha um servidor de vCenter VMware para gerenciar os hipervisores. [Saiba mais](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) sobre requisitos exatos para replicação de servidores VMware e VMs para Azure, ou para um site secundário.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Pode gerenciar recuperação para minha escritórios com recuperação de Site?

Sim. Quando você usa a recuperação de Site para coordenar replicação e failover em seus escritórios de ramificação, você receberá uma coordenação unificada e o modo de exibição de todas as suas cargas de trabalho da office de ramificação em um local central. Executar failovers e facilmente administrar recuperação de todas as ramificações de matriz, sem visitar as ramificações.

## <a name="security"></a>Segurança

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Dados de replicação são enviados para o serviço de recuperação de Site?

Não, a recuperação de Site não interceptar dados duplicados e não tem nenhuma informação sobre o que está em execução no máquinas virtuais ou servidores físicos.
Dados de replicação são trocados entre hosts Hyper-V de locais, hipervisores VMware, ou servidores físicos e armazenamento do Azure ou seu site secundário. Recuperação de site tem nenhuma possibilidade de interceptar dados. Apenas os metadados necessários para coordenar replicação e failover é enviado para o serviço de recuperação de Site.

Recuperação de site é ISO 27001:2013, 27018, HIPAA DPA certified e está em processo de avaliações SOC2 e FedRAMP JAB.


### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Por razões de conformidade, até mesmo nossos metadados local devem permanecer dentro da mesma região geográfica. Recuperação de Site pode ajudar conosco?

Sim. Quando você cria um cofre de recuperação de Site em uma região, podemos garantir que todos os metadados que precisamos habilitar e coordenar replicação e failover permanece dentro dessa região é geográficos limite.

### <a name="does-site-recovery-encrypt-replication"></a>Recuperação de Site criptografar replicação?

Máquinas virtuais e servidores físicos, replicação entre locais sites criptografia-em trânsito é suportado. Para máquinas virtuais e servidores físicos replicar no Azure, criptografia em trânsito e criptografia inativos (no Azure) são aceitos.


## <a name="replication"></a>Replicação


### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Há algum pré-requisito para replicar máquinas virtuais no Azure?

Máquinas virtuais que você deseja duplicar para Azure devem ser compatíveis com os [requisitos de Azure](site-recovery-best-practices.md#virtual-machines).

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Pode replicar máquinas de virtuais de geração 2 Hyper-V no Azure?

Sim. Recuperação de site converte de geração 2 para geração 1 durante tolerância a falhas. Em failback máquina é convertida para a geração 2. [Leia mais](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Se eu replicar no Azure como para pagar pela VMs Azure?

Durante a replicação normal, dados são replicados para armazenamento do Azure geográfica redundante e não é necessário pagar qualquer encargos de máquina virtual do Azure IaaS, fornecendo uma vantagem significativa. Quando você executa um failover no Azure, recuperação de Site criará automaticamente máquinas virtuais de IaaS do Azure e depois que você serão cobrados para os recursos de computação que consumir no Azure.


### <a name="is-there-an-sdk-i-can-use-to-automate-the-asr-workflow"></a>Há um SDK posso usar para automatizar o fluxo de trabalho de recuperação automatizada do sistema?

Sim. Você pode automatizar fluxos de trabalho de recuperação de Site usando a API Rest, PowerShell ou o SDK do Azure. Cenários com suporte no momento de implantação de recuperação do Site usando o PowerShell:

- [Duplicar VMs Hyper-V em VMMs nuvens para Azure PowerShell clássico](site-recovery-deploy-with-powershell.md)
- [Duplicar VMs Hyper-V em VMMs nuvens ao Gerenciador de recursos de PowerShell do Azure](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Duplicar VMs Hyper-V sem o VMM para Azure PowerShell clássico](site-recovery-hyper-v-site-to-azure-classic.md)
- [Duplicar VMs Hyper-V sem o VMM Gerenciador de recursos do Azure PowerShell](site-recovery-deploy-with-powershell-resource-manager.md)


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Se eu replicar no Azure que tipo de conta de armazenamento necessárias?

- **Azure portal clássico**: se você estiver implantando recuperação de Site no portal de clássico do Azure, será necessário uma [conta de armazenamento redundantes de localização geográfica padrão](../storage/storage-redundancy.md#geo-redundant-storage). Armazenamento de Premium atualmente não é compatíveis. A conta deve ser na mesma região como o Cofre de recuperação do Site.

- **Portal do Azure**: se você estiver implantando recuperação de Site no portal do Azure, você precisará de uma conta de armazenamento LRS ou GRS. Recomendamos GRS para que dados e apresentam resiliência ocorre uma interrupção regional ou se a região primária não poderão ser recuperada. A conta deve ser na mesma região como o Cofre de serviços de recuperação. Armazenamento de Premium são aceitos somente se você estiver replicação VMs VMware ou servidores físicos.

### <a name="how-often-can-i-replicate-data"></a>Com que frequência posso replicar dados?

- **Hyper-v:** VMs Hyper-V podem ser replicadas a cada 30 segundos, 5 minutos ou 15 minutos. Se você configurou replicação SAN replicação é síncrona.
- **VMware e servidores físicos:** Uma frequência de replicação não é relevante aqui. Replicação é contínua.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Posso estender replicação de site de recuperação existente para outro site terceira?

Replicação estendida ou encadeada não é compatíveis. Solicite esse recurso no [Fórum de comentários](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).


### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Posso fazer uma replicação offline na primeira vez em que eu replicar para Azure?

Não há suporte para isso. Solicite esse recurso no [Fórum de comentários](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).


### <a name="can-i-exclude-specific-disks-from-replication"></a>Pode excluir discos específicos de replicação?

Isso é suportado quando você estiver [replicar VMs VMware e servidores físicos](site-recovery-vmware-to-azure.md#exclude-disks-from-replication) no Azure, usando o portal do Azure.


### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Posso replicar máquinas virtuais com discos dinâmicos?

Discos dinâmicos são suportados ao replicar máquinas virtuais Hyper-V. Eles também são compatíveis ao replicar VMs VMware e máquinas físicas no Azure. O disco de sistema operacional deve ser um disco básico.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>Pode adicionar uma nova máquina a um grupo de replicação existente?

Adicionar novas máquinas aos grupos de replicação existentes é suportada. Para fazer isso, selecione o grupo de replicação (da lâmina 'Replicado itens') e o menu de contexto de clique direito do mouse/selecionar no grupo de replicação e selecione a opção apropriada.

![Adicionar ao grupo de replicação](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Posso pode reduzir largura de banda alocada para o tráfego de replicação de Hyper-V?

Sim. Você pode ler mais sobre a otimização de largura de banda nos artigos implantação:

- [Capacidade de planejamento para replicar VMs VMware e servidores físicos](site-recovery-vmware-to-azure.md#step-5-capacity-planning)
- [Capacidade de planejamento para replicar VMs Hyper-V em nuvens VMM](site-recovery-vmm-to-azure.md#step-5-capacity-planning)
- [Capacidade de planejamento para replicar VMs Hyper-V sem o VMM](site-recovery-hyper-v-site-to-azure.md#step-5-capacity-planning)

## <a name="failover"></a>Failover


### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Se eu estou falhando sobre no Azure, como acesso as máquinas virtuais Azure após failover?

Você pode acessar as VMs Azure sobre uma conexão de Internet segura, através de uma VPN to-site ou sobre rota expressa do Azure. Você precisará preparar uma série de coisas para se conectar. Leia mais em:

- [Conectar ao Azure VMs depois failover de VMs VMware ou servidores físicos](hsite-recovery-vmware-to-azure.md#step-7-test-the-deployment)
- [Conectar ao Azure VMs após failover de VMs Hyper-V em nuvens VMM](site-recovery-vmm-to-azure.md#step-7-test-your-deployment)
- [Conectar ao Azure VMs após failover de VMs Hyper-V sem o VMM](site-recovery-hyper-v-site-to-azure.md#step-7-test-the-deployment)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Se não ao longo do Azure como Azure assegurar de que meus dados serão flexíveis?

Azure destina-se a resiliência. Recuperação de site já foi projetada para failover um secundário Azure data center, de acordo com o SLA Azure se for necessário. Se isso acontecer, podemos garantir sua metadados e compartimentos permanecem dentro da região geográfica mesma que você escolheu para seu cofre.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Se eu estou replicar entre dois centros de dados, o que acontece se meu data center principal sofrer uma interrupção inesperada?

Você pode disparar um failover não planejado do local secundário. Recuperação de site não precisa de conectividade do local principal para executar o failover.

### <a name="is-failover-automatic"></a>É failover automática?

Failover não é automática. Você inicia failovers com único clique no portal do, ou você pode usar o [PowerShell de recuperação de Site](https://msdn.microsoft.com/library/dn850420.aspx) disparar um failover. Falhando volta é uma ação simple no portal de recuperação do Site.

Para automatizar você poderia usar Orchestrator local ou Operations Manager para detectar uma falha de máquina virtual e depois disparar o failover usando o SDK.

- [Leia mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.
- [Leia mais](site-recovery-failover.md) sobre failover.
- [Leia mais](site-recovery-failback-azure-to-vmware.md) sobre com falha de volta VMs VMware e servidores físicos


## <a name="service-providers"></a>Provedores de serviço


### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Eu sou um provedor de serviços. Recuperação do Site funciona para modelos de infraestrutura dedicada e compartilhada?

Sim, recuperação de Site oferece suporte a ambos os modelos de infraestrutura dedicada e compartilhada.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Para um provedor de serviço, é a identidade do meu locatário compartilhado com o serviço de recuperação de Site?

Não. Locatário identidade permanece anônima. Seu locatários não precisam acessar o portal de recuperação do Site. Somente o administrador do provedor de serviço interage com o portal.


### <a name="will-tenant-application-data-ever-go-to-azure"></a>Dados de aplicativo do locatário nunca irão para Azure?

Ao replicar entre sites de propriedade do provedor de serviço, dados de aplicativo nunca vai para o Azure. Dados são criptografados em trânsito e replicados diretamente entre os sites de provedor de serviço.

Se você estiver replicar no Azure, dados de aplicativo são enviados para o armazenamento do Azure mas não para o serviço de recuperação de Site. Dados criptografados em trânsito e permanecerão criptografados no Azure.


### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Meu locatários receberão uma fatura para todos os serviços Azure?

Não. Relação de cobrança do Azure é diretamente com o provedor de serviços. Provedores de serviço serão responsáveis por gerar faturas específicas para seus locatários.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Se eu estou replicar no Azure, precisamos executar máquinas virtuais no Azure todas as vezes?

Não, os dados são replicados para uma conta de armazenamento do Azure em sua assinatura. Quando você executa um teste failover (DR detalhada) ou um failover real, recuperação de Site cria automaticamente máquinas virtuais em sua assinatura.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Garantir isolamento de nível do locatário quando eu replica no Azure?

Sim.

### <a name="what-platforms-do-you-currently-support"></a>Quais plataformas você atualmente oferece suporte?

Oferecemos suporte a pacote do Azure, sistema de plataforma de nuvem, e System Center base implantações (2012 e superior). [Saiba mais](https://technet.microsoft.com/library/dn850370.aspx) sobre a integração de pacote Azure e recuperação do Site.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Você suporte único pacote do Azure e implantações de servidor única VMM?

Sim, você pode replicar máquinas virtuais Hyper-V no Azure ou replicar entre sites do provedor de serviço.  Observe que se replicar entre sites de provedor de serviço, integração de runbook Azure não está disponível.


## <a name="next-steps"></a>Próximas etapas

- Leia a [Visão geral de recuperação do Site](site-recovery-overview.md)
- Saiba mais sobre [arquitetura de recuperação do Site](site-recovery-components.md)  
