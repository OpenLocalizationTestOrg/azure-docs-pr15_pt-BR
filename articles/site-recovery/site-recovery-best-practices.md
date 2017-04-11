<properties
    pageTitle="Preparar para implantação de recuperação de Site | Microsoft Azure"
    description="Este artigo descreve como se preparar implantar replicação com recuperação de Site do Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="prepare-for-azure-site-recovery-deployment"></a>Preparar para implantação de recuperação de Site do Azure

Leia este artigo para obter uma visão de alto nível os requisitos de implantação de cada cenário de replicação compatíveis com o serviço de recuperação de Site do Azure. Após ler os requisitos gerais para cada cenário, você pode vincular a detalhes específicos de cada implantação implantação.

Depois de ler este artigo postar quaisquer comentários ou perguntas na parte inferior do artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Visão geral

As organizações precisam uma estratégia BCDR que determina como dados, cargas de trabalho e aplicativos permanecer em execução e disponível durante o tempo de inatividade planejado e e recuperar a condições de trabalho normal assim que possível. Sua estratégia BCDR deve manter dados corporativos seguros e recuperáveis e certifique-se de que cargas de trabalho permanecem disponíveis continuamente quando desastre. 

Recuperação de site é um serviço Azure que contribui para sua estratégia BCDR por coordenação replicação de servidores físicos locais e máquinas virtuais para a nuvem (Azure) ou um data center secundário. Quando ocorrem interrupções em seu local principal, você Falha ao longo para o local secundário para manter aplicativos e cargas de trabalho disponíveis. Você não volta para seu local principal quando ele retorna as operações normais. Saiba mais em [o que é recuperação de Site?](site-recovery-overview.md)

## <a name="select-your-deployment-model"></a>Selecione seu modelo de implantação

Azure tem dois diferentes [modelos de implantação](../resource-manager-deployment-model.md) para criar e trabalhar com recursos: o modelo do Gerenciador de recursos do Azure e o modelo de gerenciamento de serviços clássico. Azure também tem dois portais – o [Azure portal clássico](https://manage.windowsazure.com/) que suporta o modelo clássico de implantação e o [portal do Azure](https://ms.portal.azure.com/) com suporte para ambos os modelos de implantação.

Recuperação de site está disponível no portal do clássico e o portal do Azure. No portal do clássico Azure você pode oferecer suporte a recuperação de Site com o modelo de gerenciamento de serviços clássico. No portal do Azure, você pode suportar o modelo clássico ou implantações do Gerenciador de recursos. [Leia mais](site-recovery-overview.md#site-recovery-in-the-azure-portal) sobre a implantação com o portal do Azure.

Quando você estiver escolhendo uma anotação de modelo de implantação que:

- Recomendamos que você use o [portal do Azure](https://portal.azure.com) e o modelo do Gerenciador de recursos sempre que possível.
- Recuperação de site fornece mais simples e mais intuitiva Introdução experiência no portal do Azure.
- Usando o portal do Azure, você pode replicar máquinas clássico e no Gerenciador de recursos de armazenamento no Azure. Além disso, no portal do Azure, você pode usar LRS ou GRS contas de armazenamento.
- O portal do Azure combina os serviços de Backup e recuperação de Site em um único compartimento de serviços de recuperação para que você possa configurar e gerenciar serviços BCDR de um único local.
- Usuários com assinaturas do Azure provisionados com o programa de provedor de solução da nuvem (CSP) agora podem gerenciar as operações de recuperação de Site no portal do Azure.
- Duplicar VMs VMware ou máquinas físicas no Azure no portal do Azure fornece um número de novos recursos, incluindo o suporte para armazenamento de premium e a capacidade para excluindo discos específicos da replicação.


## <a name="select-your-deployment-scenario"></a>Selecione seu cenário de implantação

**Implantação** | **Detalhes** | **Portal do Azure** | **Portal clássico** | **PowerShell**
---|---|---|---|---
**VMs VMware no Azure** | Duplicar VMs VMware para armazenamento do Azure | No Azure portal VMs pode falhar ao longo do clássico ou o armazenamento do Gerenciador de recursos<br/><br/> Implantação no [portal do Azure](site-recovery-vmware-to-azure.md) fornece uma experiência simplificada de implantação e um número de benefícios de recurso. | No portal de clássico do Azure, você pode implantar com o [modelo aprimorado](site-recovery-vmware-to-azure-classic.md) e alternar para o armazenamento do Azure clássico.<br/><br/> Também há um modelo herdado que não deve ser usado para novas implantações. |  PowerShell atualmente não é compatíveis.
**VMs Hyper-V no Azure** | VMs Hyper-V ao armazenamento do Azure. VMs podem estar em hosts Hyper-V gerenciados em nuvens do System Center VMM ou sem VMM. | No Azure portal VMs pode falhar ao longo do clássico ou o armazenamento do Gerenciador de recursos.<br/><br/> O portal do Azure fornece uma experiência simplificada de implantação. [Saiba mais](site-recovery-vmm-to-azure.md) sobre replicar VMs Hyper-V em nuvens do VMM. [Saiba mais](site-recovery-hyper-v-site-to-azure.md) sobre replicar VMs Hyper-V (sem VMM).| No portal do Azure clássico você pode alternar VMs para armazenamento do Azure clássico | Implantação do PowerShell é suportada.
**Servidores físicos no Azure** | Duplicar servidores Windows/Linux físicos para armazenamento do Azure | No Azure servidores do portal podem falhar ao longo do clássico ou o armazenamento do Gerenciador de recursos.<br/><br/> Implantação no [portal do Azure](site-recovery-vmware-to-azure.md) fornece uma experiência simplificada de implantação e um número de benefícios de recurso. | No portal de clássico do Azure, você pode implantar com o [modelo aprimorado](site-recovery-vmware-to-azure-classic.md) e alternar para o armazenamento do Azure clássico.<br/><br/> Também há um modelo herdado que não deve ser usado para novas implantações. | Implantação do PowerShell atualmente não é compatíveis.
**Servidores de VMware VMs/física para sites secundário* | Duplicar VMs VMware ou servidores Windows/Linux físicos para um site secundário. [Saiba mais e baixar](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) o guia do usuário InMage Scout. | Não disponível no portal do Azure | No portal do clássico você vai baixar InMage Scout do cofre recuperação do Site. | Atualmente não há suporte para implantação do PowerShell
**VMs Hyper-V em um local secundário** | Duplicar VMs Hyper-V em nuvens VMM para uma nuvem secundária | No [portal do Azure](site-recovery-vmm-to-vmm.md) você pode replicar VMs Hyper-V em nuvens VMM para um site secundário usando apenas o Hyper-V Replica. SAN atualmente não é compatíveis. | No portal do clássico Azure pode replicar VMs Hyper-V em nuvens VMM para um site secundário usando [Hyper-V Replica](site-recovery-vmm-to-vmm-classic.md) ou [replicação SAN](site-recovery-vmm-san.md) | Há suporte para implantação do PowerShell



## <a name="check-what-you-need-for-deployment"></a>Verificar o que é necessário para implantação

### <a name="replicate-to-azure"></a>Duplicar para Azure

**Requisito** | **Detalhes** 
---|---
**Conta do Azure** | Você precisará de uma conta [Do Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação do Site.
**Armazenamento do Azure** | Dados replicados são armazenados no armazenamento do Azure e Azure VMs são criadas quando houver falha. Para replicar Azure, você precisará de uma [conta de armazenamento do Azure](../storage/storage-introduction.md).<br/><br/>Se você estiver implantando recuperação de Site no portal do clássico, você precisará de uma ou mais [contas de armazenamento GRS padrão](..storage/storage-redundancy.md#geo-redundant-storage).<br/><br/> Se você estiver implantando no portal do Azure você pode usar o armazenamento GRS ou LRS.<br/><br/> Se você estiver replicação VMs VMware ou servidores físicos no armazenamento Azure premium portal é compatível. Observe que se você estiver usando uma conta de armazenamento premium também será preciso uma conta de armazenamento padrão para armazenar logs de replicação que capturar alterações contínuas em dados locais. [Armazenamento de Premium](../storage/storage-premium-storage.md) normalmente é usado para máquinas virtuais que precisa de um consistentemente alto desempenho de IO e baixa latência para cargas de trabalho intenso de IO de host.<br/><br/> Se você quiser usar uma conta de premium para armazenar dados duplicados, também será necessário uma conta de armazenamento padrão para armazenar logs de replicação que capturar alterações contínuas em dados locais.
**Rede do Azure** | Para replicar Azure, você precisará de uma rede Azure que Azure VMs se conectará quando eles são criados após failover.<br/><br/> Se você estiver implantando no portal do clássico você usará uma rede clássica. Se você estiver implantando no portal do Azure, você pode usar um clássico ou na rede do Gerenciador de recursos.<br/><br/> A rede deve ser na mesma região como o cofre.
**Mapeamento de rede (VMM no Azure)** | Se você estiver replicar do VMM no Azure, [mapeamento de rede](site-recovery-network-mapping.md) garante que Azure VMs conectadas a redes corretos após failover.<br/><br/> Para configurar o mapeamento de rede, você precisará configurar redes de máquina virtual no portal do VMM.
**Local** | **VMs VMware**: você precisará de um computador local executando componentes de recuperação do Site, VMware vSphere hosts/vCenter server e VMs que você deseja duplicar. [Leia mais](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).<br/><br/> **Servidores físicos**: se você estiver replicação de servidores físicos será necessário um máquinas de local executando componentes de recuperação de Site e servidores físicos que deseja duplicar. [Leia mais](site-recovery-vmware-to-azure.md#configuration-server-prerequisites). Se você quiser [falhar novamente](site-recovery-failback-azure-to-vmware.md) após failover no Azure será necessário uma infraestrutura de VMware fazer isso.<br/><br/> **VMs Hyper-V**: se você quiser replicar VMs Hyper-V em nuvens VMM você precisará de um servidor VMM e hosts Hyper-V em quais VMs que você deseja proteger estão localizados. [Leia mais](site-recovery-vmm-to-azure.md#on-premises-prerequisites).<br/><br/> Se você quiser replicar VMs Hyper-V sem o VMM você precisará hosts Hyper-V quais VMs estão localizadas. [Leia mais](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites).
**Máquinas protegidas** | Protegido máquinas que serão replicados para Azure devem cumprir [pré-requisitos Azure](#azure-virtual-machine-requirements) descrito a seguir.


### <a name="replicate-to-a-secondary-site"></a>Duplicar para um site secundário

**Requisito** | **Detalhes** 
---|---
**Conta do Azure** | Você precisará de uma conta [Do Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação do Site.
**Local** | **VMs VMware**: no local principal você precisará de um servidor de processo para cache, compactação e criptografia de dados de replicação antes de enviá-lo para o site secundário. No site secundário, você vai instalar um servidor de configuração para gerenciar e monitorar a implantação e um servidor de destino mestre. Recomendamos também um servidor de vContinuum para facilitar o gerenciamento. Além disso, você precisará de um ou mais hosts de vSphere VMware e, opcionalmente, um servidor vCenter. [Leia mais](site-recovery-vmware-to-vmware.md)<br/><br/> **VMs Hyper-V em nuvens VMM**: você precisará configurar os servidores VMM e hosts Hyper-V contendo VMs que você deseja duplicar. [Leia mais](site-recovery-vmm-to-vmm.md#on-premises-prerequisites).
**Mapeamento de rede (VMM no VMM)** | Se você estiver replicação de VMM para VMM, [mapeamento de rede](site-recovery-network-mapping.md) garante que réplica VMs conectadas a redes apropriados após failover e ideal são colocadas em hosts Hyper-V. Para configurar o mapeamento de rede, você precisará configurar redes de máquina virtual nos servidores VMM.
**Mapeamento de armazenamento** | Se você estiver replicação de VMM para VMM você pode opcionalmente configurar [mapeamento de armazenamento](site-recovery-storage-mapping.md) para especificar o destino de armazenamento para VMs replicadas. Mapeamento de armazenamento pode ser configurado para replicação Hyper-V Replica e SAN.<br/><br/> Mapeamento de armazenamento atualmente não tem suporte no portal do Azure.


## <a name="verify-url-access"></a>Verificar o acesso de URL

Certificar-se de que essas URLs estão acessíveis de servidores.

**URL** | **VMM VMM** | **VMM Azure** | **Hyper-V no Azure** | **VMware no Azure**
---|---|---|---|---
*. accesscontrol.windows.net | Permitir | Permitir | Permitir | Permitir
*. backup.windowsazure.com | Não é necessária | Permitir | Permitir | Permitir
*. hypervrecoverymanager.windowsazure.com | Permitir | Permitir | Permitir | Permitir
*. store.core.windows.net | Permitir | Permitir | Permitir | Permitir
*. blob.core.windows.net | Não é necessária | Permitir | Permitir | Permitir
https://www.msftncsi.com/ncsi.txt | Permitir | Permitir | Permitir | Permitir
https://dev.MySQL.com/Get/archives/MySQL-5.5/MySQL-5.5.37-Win32.msi | Não é necessária | Não é necessária | Não é necessária | Permitir

## <a name="azure-virtual-machine-requirements"></a>Requisitos do Azure máquina virtual

Você pode implantar recuperação de Site para replicar máquinas virtuais e servidores físicos executando qualquer sistema operacional compatível com o Azure. Isso inclui a maioria das versões do Windows e Linux. Você precisará garantir que máquinas virtuais que você deseja proteger em conformidade com requisitos Azure no local.


**Recurso** | **Requisitos** | **Detalhes**
---|---|---
Host Hyper-V | Deve estar executando o Windows Server 2012 R2 | Verificação de pré-requisitos falhará se o sistema operacional sem suporte
Hipervisor VMware  | Sistema operacional compatível | [Requisitos de seleção](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
Sistema operacional convidado | Hyper-V para replicação Azure: recuperação de Site oferece suporte a todos os sistemas operacionais que são [suportados pelo Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para replicação de servidor físico e VMware: verificar os [pré-requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) do Windows e Linux | Verificação de pré-requisitos falhará se incompatíveis.
Arquitetura do sistema operacional de convidado | 64 bits | Verificação de pré-requisitos falhará se incompatíveis
Tamanho de disco do sistema operacional |  Até 1023 GB | Verificação de pré-requisitos falhará se incompatíveis
Contagem de disco do sistema operacional | 1 | Verificação de pré-requisitos falhará se incompatíveis.
Contagem de disco de dados | 16 ou menos (o valor máximo é uma função do tamanho da máquina virtual está sendo criada. 16 = XL) | Verificação de pré-requisitos falhará se incompatíveis
Tamanho VHD de disco de dados | Até 1023 GB | Verificação de pré-requisitos falhará se incompatíveis
Adaptadores de rede | Vários adaptadores são suportados |
Endereço IP estático | Com suporte | Se a máquina virtual primária está usando um endereço IP estático, que você pode especificar o endereço IP estático para a máquina virtual que será criado no Azure. Observe que o endereço IP estático para uma máquina virtual do linux em execução no Hyper-v não é suportado.
disco iSCSI | Sem suporte | Verificação de pré-requisitos falhará se incompatíveis
VHD compartilhado | Sem suporte | Verificação de pré-requisitos falhará se incompatíveis
Disco FC | Sem suporte | Verificação de pré-requisitos falhará se incompatíveis
Formato de disco rígido| VHD <br/><br/> VHDX | Embora VHDX atualmente não é suportado no Azure, recuperação de Site converte automaticamente VHDX VHD, quando você alternar para o Azure. Quando você não volta para o local máquinas virtuais continuar a usar o formato VHDX.
BitLocker | Sem suporte | BitLocker deve ser desabilitado antes de proteger uma máquina virtual.
Nome de máquina virtual| Entre 1 e 63 caracteres. Restrito a letras, números e hifens. Deve iniciar e termine com uma letra ou número | Atualizar o valor nas propriedades de máquina virtual em recuperação de Site
Tipo de máquina virtual | <p>Geração 1</p> <p>Geração 2 - Windows</p> |  Máquina virtual de geração 2 com o tipo de disco do sistema operacional de disco básico que inclui 1 ou 2 volumes de dados com formato de disco como VHDX que é menor que 300GB é suportado. Máquinas virtuais de Linux geração 2 não são suportadas. [Leia mais informações](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## <a name="optimizing-your-deployment"></a>Otimizando sua implantação

Use as dicas a seguir para ajudá-lo a otimizar e escalonar sua implantação.

- **Tamanho de volume do sistema operacional**: quando você replica uma máquina virtual para o Azure o volume do sistema operacional deve ser menor que 1 TB. Se você tiver mais volumes que isso você pode movê-las manualmente para outro disco antes de começar a implantação.
- **Tamanho do disco de dados**: se você estiver replicar no Azure você pode ter até 32 discos de dados em uma máquina virtual, cada uma com um máximo de 1 TB. Você pode efetivamente replicar e falhar ao longo de uma máquina virtual de ~ 32 TB.
- **Limites de plano de recuperação**: recuperação de Site pode chegar a milhares de máquinas virtuais. Planos de recuperação destinam-se como um modelo para aplicativos que deve falhar sobre juntos para que podemos limitar o número de máquinas em um plano de recuperação para 50.
- **Limites de serviço Azure**: assinatura do Azure cada vem com um conjunto de limites padrão em cores, serviços etc em nuvem. Recomendamos que você execute um failover de teste para validar a disponibilidade dos recursos em sua assinatura. Você pode modificar esses limites via suporte Azure.
- **Planejamento da capacidade**: ler sobre o [planejamento de capacidade](site-recovery-capacity-planner.md) de recuperação do Site.
- **Largura de banda de replicação**: Observe que, se você estiver curto na largura de banda de replicação:
    - **Rota expressa**: recuperação do Site funciona com o otimizador de rota expressa do Azure e WAN como Riverbed. [Leia mais](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sobre rota expressa.
    - **Tráfego de replicação**: usos de recuperação de Site executa uma replicação inicial inteligente usando somente os blocos de dados e não o VHD inteiro. Somente as alterações são replicadas durante a replicação em andamento.
    - **Tráfego de rede**: você pode controlar o tráfego de rede usado para replicação, configurando [QoS do Windows](https://technet.microsoft.com/library/hh967468.aspx) com uma regra com base no endereço IP de destino e porta.  Além disso, se você estiver replicação para recuperação de Site do Azure usando o agente de Backup do Azure você pode configurar a otimização para esse agente. [Leia mais](https://support.microsoft.com/kb/3056159).
- **RTO**: para medir o objetivo de tempo de recuperação (RTO) você pode esperar com recuperação de Site sugerimos que você executa um failover de teste e exibir os trabalhos de recuperação de Site para analisar quanto tempo ele leva para concluir as operações. Se você estiver falhando sobre no Azure, para o melhor RTO recomendamos que você automatizar todas as ações manuais por meio da integração com o Azure planos de recuperação e de automação.
- **RPO**: recuperação de Site compatível com um objetivo de ponto de recuperação próximo síncrono (RPO) quando você replica no Azure. Isso pressupõe largura de banda suficiente entre o seu data center e Azure.


##<a name="service-urls"></a>URLs de serviço
Certificar-se de que esses URLs são acessíveis a partir do servidor


**URLs** | **VMM VMM** | **VMM Azure** | **Site de Hyper-V no Azure** | **VMware no Azure**
---|---|---|---|---
 \*. accesscontrol.windows.net | Acesso obrigatório  | Acesso obrigatório  | Acesso obrigatório  | Acesso obrigatório
 \*. backup.windowsazure.com |  | Acesso obrigatório  | Acesso obrigatório  | Acesso obrigatório
 \*. hypervrecoverymanager.windowsazure.com | Acesso obrigatório  | Acesso obrigatório  | Acesso obrigatório  | Acesso obrigatório
 \*. store.core.windows.net | Acesso obrigatório  | Acesso obrigatório  | Acesso obrigatório  | Acesso obrigatório
 \*. blob.core.windows.net |  | Acesso obrigatório  | Acesso obrigatório  | Acesso obrigatório
 https://www.msftncsi.com/ncsi.txt | Acesso obrigatório  | Acesso obrigatório  | Acesso obrigatório  | Acesso obrigatório
 https://dev.MySQL.com/Get/archives/MySQL-5.5/MySQL-5.5.37-Win32.msi | | | | Acesso obrigatório


## <a name="next-steps"></a>Próximas etapas

Depois de aprendizagem e comparando requisitos de implantação geral, você pode ler os pré-requisitos detalhados e começar a implantar cada cenário.

- [Replicar máquinas virtuais VMware no Azure](site-recovery-vmware-to-azure-classic.md)
- [Duplicar servidores físicos para Azure](site-recovery-vmware-to-azure-classic.md)
- [Duplicar Hyper-V server em nuvens VMM para Azure](site-recovery-vmm-to-azure.md)
- [Replicar máquinas virtuais de Hyper-V (sem VMM) para o Azure](site-recovery-hyper-v-site-to-azure.md)
- [Duplicar VMs Hyper-V para um site secundário](site-recovery-vmm-to-vmm.md)
- [Duplicar VMs Hyper-V para um site secundário com SAN](site-recovery-vmm-san.md)
- [Duplicar VMs Hyper-V com um único servidor VMM](site-recovery-single-vmm.md)
