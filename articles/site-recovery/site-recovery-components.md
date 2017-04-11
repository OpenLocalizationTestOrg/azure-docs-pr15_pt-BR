<properties
    pageTitle="Como funciona a recuperação de Site? | Microsoft Azure"
    description="Este artigo fornece uma visão geral de arquitetura de recuperação do Site"
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
    ms.topic="get-started-article"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="how-does-azure-site-recovery-work"></a>Como funciona a recuperação de Site do Azure?

Leia este artigo para entender a arquitetura subjacente do serviço de recuperação de Site do Azure e os componentes que o tornam funcionam. 

Poste quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Visão geral

As organizações precisam uma estratégia BCDR que determina como dados, cargas de trabalho e aplicativos permanecer em execução e disponível durante o tempo de inatividade planejado e e recuperar a condições de trabalho normal assim que possível. Sua estratégia BCDR deve manter dados corporativos seguros e recuperáveis e certifique-se de que cargas de trabalho permanecem disponíveis continuamente quando desastre. 

Recuperação de site é um serviço Azure que contribui para sua estratégia BCDR por coordenação replicação de servidores físicos locais e máquinas virtuais para a nuvem (Azure) ou um data center secundário. Quando ocorrem interrupções em seu local principal, você Falha ao longo para o local secundário para manter aplicativos e cargas de trabalho disponíveis. Você não volta para seu local principal quando ele retorna as operações normais. Saiba mais em [o que é recuperação de Site?](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Recuperação de site no portal do Azure

Azure tem dois diferentes [modelos de implantação](../resource-manager-deployment-model.md) para criar e trabalhar com recursos: o modelo do Gerenciador de recursos do Azure e o modelo de gerenciamento de serviços clássico. Azure também tem dois portais – o [Azure portal clássico](https://manage.windowsazure.com/) que suporta o modelo clássico de implantação e o [portal do Azure](https://portal.azure.com) com suporte para ambos os modelos de implantação.

Recuperação de site está disponível no portal do clássico e o portal do Azure. No portal do clássico Azure você pode oferecer suporte a recuperação de Site com o modelo de gerenciamento de serviços clássico. No portal do Azure você pode oferecer suporte a modelo clássico ou implantações de modelo de recursos. [Leia mais](site-recovery-overview.md#site-recovery-in-the-azure-portal) sobre a implantação com o portal do Azure.

As informações neste artigo se aplica ao clássicas e Azure implantações portal. Diferenças são indicadas onde aplicável.

## <a name="deployment-scenarios"></a>Cenários de implantação

Recuperação de site pode ser implantada para coordenar replicação em várias situações:

- **Máquinas virtuais VMware replicar**: você pode replicar máquinas virtuais VMware a locais para Azure ou um data center secundário.
- - **Replicar máquinas físicas**: você pode replicar máquinas físicas executando o Windows ou Linux para Azure ou um data center secundário. O processo para replicar máquinas físicas é quase a mesma que o processo de replicação VMs VMware
- **Duplicar VMs Hyper-V (sem VMM)**: você pode replicar VMs Hyper-V que não são gerenciadas pelo VMM no Azure.
- **Duplicar Hyper-V VMs gerenciado no System Center VMM nuvens**: você pode replicar local Hyper-V virtual máquinas executados em servidores de host do Hyper-V em nuvens VMM Azure ou um data center secundário. Você pode replicar usando padrão Hyper-V Replica ou replicação SAN.
- **Migrar VMs**: você pode usar o Site recuperação para [migrar Azure IaaS VMs](site-recovery-migrate-azure-to-azure.md) entre regiões ou [migrar instâncias AWS Windows](site-recovery-migrate-aws-to-azure.md) Azure IaaS VMs. No momento, somente migração é suportada que significa que você pode falhar sobre essas VMs, mas você não pode falhá-las novamente.

Recuperação de site pode replicar a maioria dos aplicativos em execução nessas VMs e servidores físicos. Você pode obter uma completa resumo dos aplicativos com suporte no [quais cargas de trabalho recuperação do Azure Site pode proteger?](site-recovery-workload.md)


## <a name="replicate-to-azure-vmware-virtual-machines-or-physical-windowslinux-servers"></a>Duplicar para Azure: VMware máquinas virtuais ou servidores Windows/Linux físicos

Há duas maneiras de replicar VMs VMware com recuperação de Site.

- **Usando o portal de Azure**-quando você implanta recuperação de Site no portal do Azure, você pode falhar ao longo de VMs ao armazenamento do Gerenciador de serviço clássico ou ao Gerenciador de recursos. Duplicar VMs VMware no portal do Azure traz um número de vantagens, incluindo a capacidade de replicar clássico ou o Gerenciador de recursos de armazenamento no Azure. [Saiba mais](site-recovery-vmware-to-azure.md).
- **Usando o portal de clássico**-você pode implantar recuperação de Site no portal do clássico usando uma experiência avançada. Isso deve ser usado para todas as novas implantações no portal do clássico. Nesta implantação você somente pode falhar ao longo de VMs para armazenamento clássico no Azure e não para o armazenamento do Gerenciador de recursos. [Saiba mais](site-recovery-vmware-to-azure-classic.md). Também há uma [experiência herdada](site-recovery-vmware-to-azure-classic-legacy.md) de configuração de replicação de VMware no portal do clássico. Isso não deve ser usado para novas implantações.  Se você já tiver implantado usando a experiência herdados [Saiba como migrar](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) para a implantação avançada.



Os requisitos de arquitetura para implantar recuperação de Site para replicar servidores VMware VMs/físico no portal do Azure ou o Azure portal clássico (Avançado) são semelhantes, com algumas diferenças:

- Se você implantar no portal do Azure, você pode replicar ao armazenamento baseado em Gerenciador de recursos e usar redes do Gerenciador de recursos de conexão VMs Azure após failover.
- Quando você implantar no portal do Azure ambas as LRS e armazenamento GRS é suportado. No portal do clássico GRS é necessária.
- O processo de implantação é simplificado e mais amigável no portal do Azure.


Veja aqui o que é preciso:

- **Conta do Microsoft Azure**: você precisará de uma conta do Microsoft Azure.
- **Armazenamento do Azure**: você precisará de uma conta de armazenamento do Azure para armazenar dados duplicados. Você pode usar uma conta clássica ou uma conta de armazenamento do Gerenciador de recursos. A conta pode ser LRS ou GRS quando você implanta no portal do Azure. Dados replicados são armazenados no armazenamento do Azure e Azure VMs são giradas para cima quando houver falha. 
- **Rede Azure**: você precisará de uma rede virtual Azure que Azure VMs se conectará quando eles são criados no failover. No portal do Azure, eles podem ser criadas no modelo de Gerenciador de serviço clássico ou no modelo de Gerenciador de recursos de redes.
- **Servidor de configuração de local**: você precisará de um computador Windows Server 2012 R2 local que executa o servidor de configuração e outros componentes de recuperação do Site. Se você estiver replicar VMs VMware deve ser uma VM VMware altamente disponível. Se você quiser replicar servidores físicos máquina pode ser física. Esses componentes de recuperação do Site serão instalados no computador:
    - **Servidor de configuração**: coordenadas a comunicação entre o seu ambiente local e o Azure e gerencia a replicação de dados e a recuperação.
    - **Servidor processo**: atua como um gateway de replicação. Ele recebe dados de replicação de máquinas de origem protegido, otimizá-lo com o cache, compactação e criptografia e envia os dados para o armazenamento do Azure. Ele também lida com a instalação de envio do serviço de mobilidade máquinas protegida e executa a descoberta automática de VMs VMware. À medida que sua implantação cresce, você pode adicionar servidores adicionais processo dedicado separada para lidar com volumes crescentes de tráfego de replicação.
    - **Servidor de destino mestre**: lida com dados de replicação durante o failback do Azure. 
- **VMs VMware ou servidores físicos para replicar**: cada máquina que você deseja duplicar para Azure será necessário o componente de serviço de mobilidade instalado. Esse serviço captura gravações de dados na máquina e encaminha para o servidor de processo. Este componente pode ser manualmente, ou pode ser enviado e instalado automaticamente pelo servidor processo quando você habilita a replicação para um computador.
- **servidor de hosts/vCenter vSPhere**: você precisará de um ou mais servidores de host de vSphere executando VMs VMware. Recomendamos que você implantar um servidor de vCenter para gerenciar esses hosts.
- **Failback**: Veja aqui o que você precisa:
    - **Failback físico-para-físico não tem suporte**: isso significa que se você falha sobre os servidores físicos no Azure e então deseja falhar novamente, você deve falhar para uma VM VMware. Você não pode falhar para um servidor físico. Você precisará de uma VM Azure falha voltar ao e se você não implantar o servidor de configuração como uma VM VMware você precisará configurar um servidor de destino mestre separado como uma VM VMware. Isso é necessário porque o servidor de destino mestre interage e anexa ao armazenamento de VMware para restaure para uma VM VMware.
    - - **Servidor de processo temporário no Azure**: se você deseja falhar volta do Azure após failover, você precisará configurar uma VM Azure configurado como um servidor de processo, lidar com a replicação do Azure. Você pode excluir esta máquina virtual depois que termina de failback.
    - **Conexão de VPN**: para failback, você precisará de uma conexão de VPN (ou a rota expressa do Azure) Configure da rede Azure para o site local.
    - **Local separado mestre servidor de destino**: O servidor de destino mestre local trata failback. O servidor de destino mestre é instalado por padrão no servidor de gerenciamento, mas se você estiver falhando volta maiores volumes de tráfego que você deve configurar um servidor de destino mestre local separado para essa finalidade.

**Arquitetura geral**

![Aprimorado](./media/site-recovery-components/arch-enhanced.png)

**Componentes de implantação**

![Aprimorado](./media/site-recovery-components/arch-enhanced2.png)

**Failback**

![Failback aprimorada](./media/site-recovery-components/enhanced-failback.png)


- [Saiba mais](site-recovery-vmware-to-azure.md#azure-prerequisites) sobre os requisitos para implantação de portal Azure.
- [Saiba mais](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sobre os requisitos de implantação aprimorados no portal do clássico.
- [Saiba mais](site-recovery-failback-azure-to-vmware.md) sobre failback no portal do Auzre.
- [Saiba mais](site-recovery-failback-azure-to-vmware-clas- [Learn more](site-recovery-failback-azure-to-vmware-classic.md) about failback in the Auzre portal.sic.md) sobre failback no portal do clássico.

## <a name="replicate-to-azure-hyper-v-vms-not-managed-by-vmm"></a>Duplicar para Azure: VMs Hyper-V não gerenciadas pelo VMM

Você pode replicar VMs Hyper-V que não são gerenciados pelo System Center VMM no Azure com recuperação de Site da seguinte maneira:

- **Usando o portal de Azure**-quando você implanta recuperação de Site no portal do Azure, você pode falhar ao longo de VMs ao armazenamento clássico ou ao Gerenciador de recursos. [Saiba mais](site-recovery-hyper-v-site-to-azure.md).
- **Usando o portal de clássico**-você pode implantar recuperação de Site no portal do clássico. Nesta implantação você somente pode falhar ao longo de VMs para armazenamento clássico no Azure e não para o armazenamento do Gerenciador de recursos. [Saiba mais](site-recovery-hyper-v-site-to-azure-classic.md).

A arquitetura de ambas as implantações é semelhante, exceto que:

- Se você implantar no portal do Azure, você pode replicar ao armazenamento do Gerenciador de recursos e usar redes do Gerenciador de recursos de conexão VMs Azure após failover.
- O processo de implantação é simplificado e mais amigável no portal do Azure.

Veja aqui o que é preciso:

- **Conta do Microsoft Azure**: você precisará de uma conta do Microsoft Azure.
- **Armazenamento do Azure**: você precisará de uma conta de armazenamento do Azure para armazenar dados duplicados. No portal do Azure, você pode usar uma conta clássica ou uma conta de armazenamento do Gerenciador de recursos. No portal do clássico, você pode usar apenas uma conta clássica. Dados replicados são armazenados no armazenamento do Azure e Azure VMs são criadas quando houver falha.
- **Rede Azure**: você precisará de uma rede Azure que Azure VMs se conectará quando eles são criados após failover. 
- **Host Hyper-v**: você precisará de um ou mais servidor de host do Windows Server 2012 R2 Hyper-V. Durante a implantação de recuperação do Site, você vai instalar o provedor de recuperação de Site do Azure e o agente de serviços de recuperação do Microsoft Azure do host.
- **VMs Hyper-V**: você precisará de um ou mais VMs no servidor host Hyper-V. Provedor de recuperação de Site Azure e o agente de serviços de recuperação do Azure no host Hyper-V durante a implantação de recuperação do Site. O provedor coordenadas e organiza replicação com o serviço de recuperação de Site pela internet. O agente lida com dados de replicação de dados pela HTTPS 443. Comunicações do provedor de e o agente são seguro e criptografado. Dados duplicados no armazenamento do Azure também são criptografados.

**Arquitetura geral**

![Site de Hyper-V no Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


- [Saiba mais](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) sobre os requisitos para implantação de portal Azure.
- [Saiba mais](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) sobre os requisitos para implantação de portal clássico.



## <a name="replicate-to-azure-hyper-v-vms-managed-by-vmm"></a>Duplicar para Azure: Hyper-V VMs gerenciadas pelo VMM

Você pode replicar VMs Hyper-V em nuvens VMM para Azure com recuperação de Site da seguinte maneira:

- **Usando o portal de Azure**-quando você implanta recuperação de Site no portal do Azure, você pode falhar ao longo de VMs ao armazenamento clássico ou ao Gerenciador de recursos. [Saiba mais](site-recovery-vmm-to-azure.md).
- **Usando o portal de clássico**-você pode implantar recuperação de Site no portal do clássico. Nesta implantação você somente pode falhar ao longo de VMs para armazenamento clássico no Azure e não para o armazenamento do Gerenciador de recursos. [Saiba mais](site-recovery-vmm-to-azure-classic.md).

A arquitetura de ambas as implantações é semelhante, exceto que:

- Se você implantar no portal do Azure, você pode replicar ao armazenamento baseado em Gerenciador de recursos e usar redes do Gerenciador de recursos de conexão VMs Azure após failover.
- O processo de implantação é simplificado e mais amigável no portal do Azure.


Veja aqui o que é preciso:

- **Conta do Microsoft Azure**: você precisará de uma conta do Microsoft Azure.
- **Armazenamento do Azure**: você precisará de uma conta de armazenamento do Azure para armazenar dados duplicados. No portal do Azure, você pode usar uma conta clássica ou uma conta de armazenamento do Gerenciador de recursos. No portal do clássico, você pode usar apenas uma conta clássica. Dados replicados são armazenados no armazenamento do Azure e Azure VMs são criadas quando houver falha.
- **Rede Azure**: você precisará configurar o mapeamento de rede para que o Azure VMs conectadas a redes apropriados quando são criados após failover. 
- **Servidor VMM**: você precisa de um ou mais servidores VMM locais em execução no System Center 2012 R2 e configurado com um ou mais nuvens privadas. Se você estiver implantando no Azure portal será necessário lógico e redes de máquina virtual configurar para que você possa configurar mapeamento de rede. No portal do clássico isso é opcional.  Uma rede de máquina virtual deve ser vinculada a uma rede lógica associado com a nuvem.
- **Host Hyper-v**: você precisará de um ou mais servidores de host do Windows Server 2012 R2 Hyper-V na nuvem VMM.
- **VMs Hyper-V**: você precisará de um ou mais VMs no servidor host Hyper-V.

**Arquitetura geral**

![VMM Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

- [Saiba mais](site-recovery-vmm-to-azure.md#azure-requirements) sobre os requisitos para implantação de portal Azure.
- [Saiba mais](site-recovery-vmm-to-azure-classic.md#before-you-start) sobre os requisitos para implantação de portal clássico.




## <a name="replicate-to-a-secondary-site-vmware-virtual-machines-or-physical-servers"></a>Replicar para um local secundário: VMware máquinas virtuais ou servidores físicos 

Para duplicar VMs VMware ou servidores físicos para um site secundário como download InMage Scout que está incluído na assinatura do Azure recuperação do Site. Ele pode ser baixado do portal do Azure ou de portal clássico do Azure. 

Você configurar os servidores de componentes em cada site (configuração, processo, destino mestre) e instala o agente unificadas em máquinas que você deseja duplicar. Após a replicação inicial o agente em cada máquina envia alterações de replicação delta para o servidor de processo. O servidor processo otimiza os dados e transfere-o para o servidor de destino mestre no local secundário. O servidor de configuração gerencia o processo de replicação.

Veja aqui o que você precisa:

**Conta do Microsoft Azure**: implantar esse cenário usando InMage Scout. Para obtê-lo, você precisará uma assinatura do Azure. Depois de criar um cofre de recuperação do Site que você baixe InMage Scout e instale as atualizações mais recentes para configurar a implantação.
**Servidor de processo (site principal)**: configurar o componente de servidor de processo em seu site principal para lidar com o cache, compactação e otimização de dados. Também trata push instalação do agente de Unificação de mensagens máquinas que você deseja proteger. 
**Servidor de vCenter (site principal) e VMware ESX/ESXi**: se você estiver protegendo VMs VMware será necessário um hipervisor VMware EXS/ESXi e, opcionalmente, um servidor de vCenter VMware para gerenciar hipervisores.
- **Servidores de VMs/física (site principal)**: VMs VMware ou Windows/Linux servidores físicos que deseja proteger será necessitem o agente de Unificação de mensagens instalado. O agente unificada também é instalado nas máquinas atuando como o servidor de destino mestre. O agente atua como um provedor de comunicação entre todos os componentes. 
- - **Servidor de configuração (site secundário)**: O servidor de configuração é o primeiro componente instalar e ele estiver instalado no site secundário para gerenciar, configurar e monitorar sua implantação, usando o site de gerenciamento ou no console de vContinuum. Há apenas um servidor de configuração de único em uma implantação e ele deve ser instalado em um computador executando o Windows Server 2012 R2.
- **servidor de vContinuum (site secundário)**: é instalado no mesmo local (site secundário) como o servidor de configuração. Ele fornece um console para gerenciar e monitorar seu ambiente protegido. Em uma instalação padrão, o servidor de vContinuum é o primeiro servidor de destino mestre e instalou o agente unificado.
- **Servidor de destino (site secundário) mestre**: O servidor de destino mestre mantém dados replicados. Ele recebe dados do servidor de processo, cria uma máquina de réplica no site secundário e mantém os pontos de retenção de dados. O número de servidores de destino mestre que você precisa depende do número de máquinas que você está protegendo. Se você quiser falhar volta para o site principal será necessário um servidor de destino mestre lá demais. 

**Arquitetura geral**

![VMware para VMware](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="replicate-to-a-secondary-site-hyper-v-vms-managed-by-vmm"></a>Replicar para um local secundário: Hyper-V VMs gerenciadas pelo VMM


Você pode replicar VMs Hyper-V que são gerenciados pelo System Center VMM para um data center secundário com recuperação de Site da seguinte maneira:

- **Usando o portal de Azure**-quando você implanta recuperação de Site no portal do Azure. [Saiba mais](site-recovery-hyper-v-site-to-azure.md).
- **Usando o portal de clássico**-você pode implantar recuperação de Site no portal do clássico. [Saiba mais](site-recovery-hyper-v-site-to-azure-classic.md).

A arquitetura de ambas as implantações é semelhante, exceto que:

- Se você implantar no portal do Azure, que você deve configurar mapeamento de rede. Isso é opcional no portal do clássico.
- O processo de implantação é simplificado e mais amigável no portal do Azure.
- - Se você implantar no Azure de portal clássico [mapeamento de armazenamento](site-recovery-storage-mapping.md) está disponível.

Veja aqui o que é preciso:

- **Conta do Microsoft Azure**: você precisará de uma conta do Microsoft Azure.
- **Servidor VMM**: Recomendamos um servidor VMM no local principal e um no site secundário, cada um contendo pelo menos uma nuvem privada do VMM. O servidor deve estar executando pelo menos System Center 2012 SP1 com as últimas atualizações e conectado à internet. Nuvens devem ter o perfil de capacidade de Hyper-V definida. Você vai instalar o provedor de recuperação de Site do Azure do servidor VMM. O provedor coordenadas e organiza replicação com o serviço de recuperação de Site pela internet. Comunicações entre o provedor e Azure são seguro e criptografado.
- **Servidor Hyper-V**: servidores de host Hyper-V devem estar localizados nas nuvens VMM primárias e secundárias. O host de servidores devem estar executando pelo menos o Windows Server 2012 com as últimas atualizações instalado e conectado à internet. Dados são replicados entre os servidores de host Hyper-V primárias e secundários via LAN ou VPN usando autenticação Kerberos ou certificados.  
- **Protegido máquinas**: O servidor de host de origem Hyper-V deve ter pelo menos uma máquina virtual que você deseja proteger.

**Arquitetura geral**

![Local para o local](./media/site-recovery-components/arch-onprem-onprem.png)


- [Saiba mais](site-recovery-vmm-to-vmm.md#azure-prerequisites) sobre os requisitos de implantação no portal do Azure.
- - [Saiba mais](site-recovery-vmm-to-vmm-classic.md#before-you-start) sobre os requisitos de implantação no portal de clássico do Azure.




## <a name="replicate-to-a-secondary-site-with-san-replication-hyper-v-vms-managed-by-vmm"></a>Replicar para um local secundário com replicação de SAN: Hyper-V VMs gerenciadas pelo VMM

Você pode replicar VMs Hyper-V gerenciado no nuvens do VMM em um local secundário usando replicação de SAN usando o portal de clássico Azure. Este cenário atualmente não tem suporte no novo portal do Azure. 

Neste cenário durante a implantação de recuperação do Site, você vai instalar o provedor de recuperação de Site do Azure em servidores VMM. O provedor coordenadas e organiza replicação com o serviço de recuperação de Site pela internet. Dados são replicados entre os conjuntos de armazenamento principal e secundário usando replicação de SAN síncrona.

Veja aqui o que é preciso:

**Conta do Microsoft Azure**: você precisará de uma assinatura do Azure
- **Matriz SAN**: um [conjunto de SAN com suporte](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) gerenciadas pelo servidor VMM primário. SAN compartilha uma infraestrutura de rede com outra matriz SAN no local secundário.
- **Servidor VMM**: Recomendamos um servidor VMM no local principal e um no site secundário, cada um contendo pelo menos uma nuvem privada do VMM. O servidor deve estar executando pelo menos System Center 2012 SP1 com as últimas atualizações e conectado à internet. Nuvens devem ter o perfil de capacidade de Hyper-V definida.
- **Servidor Hyper-V**: servidores de host Hyper-V localizados nas nuvens VMM primárias e secundárias. O host de servidores devem estar executando pelo menos o Windows Server 2012 com as últimas atualizações instalado e conectado à internet.
- **Protegido máquinas**: O servidor de host de origem Hyper-V deve ter pelo menos uma máquina virtual que você deseja proteger.

**Arquitetura de replicação de SAN**

![Replicação de SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

[Saiba mais](site-recovery-vmm-san.md#before-you-start) sobre os requisitos de implantação.
### <a name="on-premises"></a>Local



## <a name="hyper-v-protection-lifecycle"></a>Ciclo de vida de proteção de Hyper-V

Este fluxo de trabalho mostra o processo de proteção, replicação e falhando sobre máquinas virtuais Hyper-V. 

1. **Habilitar proteção**: você configurar o Cofre de recuperação do Site, definir configurações de replicação para uma nuvem VMM ou um site de Hyper-V e ativar a proteção para VMs. Um trabalho chamado **Habilitar proteção** é iniciado e é possível monitorar na guia **trabalhos** . O trabalho verificações de máquina cumpre com os pré-requisitos e, em seguida, chama o método [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) que define a replicação no Azure com as configurações que você configurou. O trabalho **Habilitar proteção** também chama o método de [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) para inicializar uma replicação de máquina virtual total.
2. **Replicação inicial**: um instantâneo de máquina virtual é colocado e discos virtuais são replicados um por vez até que eles serão todos copiados para Azure ou ao data center secundário. O tempo necessário para concluir essa depende do tamanho da máquina virtual, largura de banda de rede e o método de replicação inicial. Se ocorrerem de alterações de disco enquanto replicação inicial está em andamento do controlador de replicação de réplica do Hyper-V rastreia essas alterações como Logs de replicação de Hyper-V (.hrl) que estão localizados na mesma pasta que os discos. Cada disco tem um arquivo de .hrl associado que será enviado para o armazenamento secundário. Observe que os arquivos de log e instantâneo consumam recursos de disco enquanto replicação inicial está em andamento. Quando termina a replicação inicial o instantâneo de máquina virtual é excluído e as alterações de disco delta no log de são sincronizadas e mescladas.
3. **Proteção de finalizar**: após replicação inicial concluir o trabalho de **proteção de finalizar** configura rede e outras configurações de replicação POST para que a máquina virtual estiver protegida. Se você estiver replicar no Azure você talvez seja necessário ajustar as configurações da máquina virtual para que ele está pronto para failover. Neste ponto, você pode executar um failover de teste para verificar se que tudo está funcionando conforme esperado.
4. **Replicação**: após a replicação inicial sincronização delta começa, de acordo com as configurações de replicação. 
    - **Falha de replicação**: se replicação delta falha, uma replicação completa seria cara em termos de largura de banda ou tempo e nova sincronização ocorre. Por exemplo, se os arquivos .hrl alcançar a 50% do tamanho do disco, em seguida, a máquina virtual será marcado para nova sincronização. Nova sincronização minimiza a quantidade de dados enviados Calculando somas de verificação das máquinas virtuais origem e destino e enviando apenas o delta. Depois que termina de nova sincronização será reiniciada replicação delta. Por padrão nova sincronização está agendada para ser executada automaticamente fora do horário de trabalho, mas você pode sincronizar uma máquina virtual manualmente.
    - **Erro de replicação**: se um erro de replicação ocorrer não há uma tentativa de interna. Se é um erro não recuperável como um erro de autenticação ou autorização ou uma máquina réplica está em um estado inválido, então não repetir será tentado. Se ele é um erro recuperável como um erro de rede ou espaço de disco insuficiente/memória, então uma repetição ocorre com intervalos entre as tentativas crescentes (1, 2, 4, 8, 10 e, em seguida, a cada 30 minutos).
4. **Failovers planejada/planejado**: você pode executar failovers planejadas ou conforme necessário. Se você executar um failover planejado e fonte VMs são desligadas para garantir sem perda de dados. Após a criação de réplica VMs eles estão colocados em uma confirmação pendente. Você precisa confirmá-las para concluir o failover, a menos que você estiver duplicar com SAN, caso no qual confirmação é automática. Após o site principal estiver funcionando failback pode ocorrer. Se você tiver replicado no Azure inverso replicação é automática. Caso contrário, você disparar replicação reversa manualmente.
 

![fluxo de trabalho](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Próximas etapas

[Preparar para implantação](site-recovery-best-practices.md)
