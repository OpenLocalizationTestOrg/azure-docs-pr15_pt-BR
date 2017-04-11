<properties
    pageTitle="Duplicar máquinas virtuais VMware e servidores físicos para Azure com recuperação de Site do Azure (herdado) | Microsoft Azure" 
    description="Descreve como replicar VMs local e Windows/Linux servidores físicos para Azure usando o Azure recuperação de Site em uma implantação herdada no portal do clássico." 
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

# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery-using-the-classic-portal-legacy"></a>Duplicar máquinas virtuais VMware e servidores físicos para Azure com recuperação de Site do Azure usando o portal de clássico (herdado)

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmware-to-azure.md)
- [Portal clássico](site-recovery-vmware-to-azure-classic.md)
- [Portal clássico (herdado)](site-recovery-vmware-to-azure-classic-legacy.md)


Bem-vindo ao Site Azure recuperação! Este artigo descreve uma implantação herdada para replicação de máquinas virtuais VMware a locais ou servidores físicos Windows/Linux no Azure usando a recuperação de Site Azure no portal do clássico.

## <a name="overview"></a>Visão geral

As organizações precisam uma estratégia BCDR que determina como dados, cargas de trabalho e aplicativos permanecer em execução e disponível durante o tempo de inatividade planejado e e recuperar a condições de trabalho normal assim que possível. Sua estratégia BCDR deve manter dados corporativos seguros e recuperáveis e certifique-se de que cargas de trabalho permanecem disponíveis continuamente quando desastre.

Recuperação de site é um serviço Azure que contribui para sua estratégia BCDR por coordenação replicação de servidores físicos locais e máquinas virtuais para a nuvem (Azure) ou um data center secundário. Quando ocorrem interrupções em seu local principal, você Falha ao longo para o local secundário para manter aplicativos e cargas de trabalho disponíveis. Você não volta para seu local principal quando ele retorna as operações normais. Saiba mais em [o que é recuperação de Site do Azure?](site-recovery-overview.md)


>[AZURE.WARNING] Este artigo contém **instruções herdadas**. Não usá-la para novas implantações. Em vez disso, [siga estas instruções](site-recovery-vmware-to-azure.md) para implantar recuperação do Site do portal do Azure ou [use essas instruções](site-recovery-vmware-to-azure-classic.md) para configurar a implantação aprimorada no portal do clássico. Se você já tiver implantado usando o método descrito neste artigo, recomendamos que você migrar para a implantação aprimorada no portal do clássico.


## <a name="migrate-to-the-enhanced-deployment"></a>Migrar para a implantação aprimorada

Esta seção só é relevante se você já tiver implantado recuperação de Site seguindo as instruções neste artigo.

Para migrar sua implantação existente, que você precisará:

1. Implante novos componentes de recuperação de Site em seu site local.
2. Configure credenciais para descoberta automática de VMs VMware no novo servidor de configuração.
3. Descubra os servidores VMware com o novo servidor de configuração.
3. Crie um novo grupo de proteção com o novo servidor de configuração.


Antes de começar:

- Recomendamos que você configure uma janela de manutenção para a migração.
- A opção de **Migrar máquinas** está disponível somente se você tiver grupos de proteção existentes criadas durante uma implantação herdada.
- Após concluir as etapas de migração pode levar 15 minutos ou mais para atualizar as credenciais e para descobrir e atualizar máquinas virtuais para que você pode adicioná-los a um grupo de proteção. Você pode atualizar manualmente em vez de espera. 

Migre da seguinte maneira:

1. Leia sobre [implantação aprimorados no portal do clássico](site-recovery-vmware-to-azure-classic.md#enhanced-deployment). Examine a avançada [arquitetura](site-recovery-vmware-to-azure-classic.md#scenario-architecture)e [os pré-requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment).
2. Desinstale o serviço de mobilidade de máquinas que você está atualmente replicar. Uma nova versão do serviço será instalada nas máquinas quando você adicioná-los para o novo grupo de proteção.
3. Baixe uma [chave de registro do cofre](site-recovery-vmware-to-azure-classic.md#step-4-download-a-vault-registration-key) e [execute o Assistente de configuração unificada](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server) para instalar o servidor de configuração, servidor processo e componentes de servidor de destino mestre. Leia mais sobre o [planejamento de capacidade](site-recovery-vmware-to-azure-classic.md#capacity-planning).
4. [Configurar credenciais](site-recovery-vmware-to-azure-classic.md#step-6-set-up-credentials-for-the-vcenter-server) que a recuperação Site pode usar para acessar o servidor VMware para detectar automaticamente a VMs VMware. Saiba mais sobre as [permissões necessárias](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
5. Adicione [os servidores de vCenter ou vSphere hosts](site-recovery-vmware-to-azure-classic.md#step-7-add-vcenter-servers-and-esxi-hosts). Pode levar 15 minutos para obter mais servidores para que apareçam no portal de recuperação do Site.
6. Crie um [novo grupo de proteção](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Pode levar até 15 minutos para o portal atualizar para que as máquinas virtuais forem descobertas e apareçam. Se você não quiser aguardar você pode realçar o nome do servidor de gerenciamento (não clique nele) > **Atualizar**.
7. Sob a proteção de novo grupo, clique em **Migrar máquinas**.

    ![Adicionar conta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration1.png)

8. Em **Selecionar máquinas** , selecione o grupo de proteção que você deseja migrar e as máquinas que você deseja migrar.

    ![Adicionar conta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration2.png)

9. Em **Definir configurações de destino** Especifique se deseja usar as mesmas configurações para todas as máquinas e selecione a conta de armazenamento do Azure e servidor de processo. Se você não tiver um servidor de processo separado esta será ao endereço IP do servidor a configuração.


    ![Adicionar conta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration3.png)

    > [AZURE.NOTE] Não há suporte para a [migração de contas de armazenamento](../resource-group-move-resources.md) em grupos de recursos dentro da mesma assinatura ou assinaturas para contas de armazenamento usadas para implantar recuperação do Site.

10. Em **Especificar contas**, selecione a conta que você criou para o servidor de processo acessar a máquina para a nova versão do serviço mobilidade por push.

    ![Adicionar conta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration4.png)

11. Recuperação de site irá migrar seus dados replicados para a conta de armazenamento do Azure que você forneceu. Opcionalmente, você pode reutilizar a conta de armazenamento usado na implantação herdada.
12. Após concluir o trabalho máquinas virtuais sincronizará automaticamente. Após a conclusão de sincronização, você pode excluir as máquinas virtuais do grupo de proteção herdados.
13. Depois de todas as máquinas tenham migrados, você pode excluir o grupo de proteção herdados.
14. Lembre-se especificar as propriedades de failover para máquinas e as configurações de rede Azure após a conclusão da sincronização.
15. Se você tiver planos de recuperação existente, você pode migrá-las para a implantação aprimorada com a opção de **Plano de recuperação de migrar** . Você só deve fazer isso após todas as máquinas protegidas foram migradas. 

    ![Adicionar conta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration5.png)

>[AZURE.NOTE] Após terminar de migração continuar com o [artigo aprimorado](site-recovery-vmware-to-azure-classic.md). O restante deste artigo herdados não será mais relevante e você não precisa acompanhar mais as etapas descritas no it * *.




## <a name="what-do-i-need"></a>O que eu preciso?

Este diagrama mostra os componentes de implantação.

![Novo cofre](./media/site-recovery-vmware-to-azure-classic-legacy/architecture.png)

Veja aqui o que é preciso:

**Componente** | **Implantação** | **Detalhes**
--- | --- | ---
**Servidor de configuração** | Uma Azure A3 máquina virtual padrão na mesma assinatura como recuperação do Site. | O servidor de configuração coordenadas comunicação entre máquinas protegidas, o servidor processo e servidores de destino mestre no Azure. Ele configura replicação e a recuperação de coordenadas no Azure quando houver falha.
**Servidor de destino mestre** | Uma máquina virtual Azure — um servidor Windows com base em uma imagem da Galeria de Windows Server 2012 R2 (para proteger máquinas Windows) ou como um servidor Linux com base em uma imagem da Galeria OpenLogic CentOS 6.6 (para proteger máquinas Linux).<br/><br/> Dimensionamento três opções estão disponíveis – A4 padrão, D14 padrão e DS4 padrão.<br/><br/> O servidor está conectado à mesma rede Azure como o servidor de configuração.<br/><br/> Você tiver configurado o portal de recuperação do Site | Ele recebe e retém dados replicados de suas máquinas protegidas usar VHDs anexados criados no armazenamento de blob em sua conta de armazenamento do Azure.<br/><br/> Selecione DS4 padrão especificamente para configurar proteção para cargas de trabalho exigindo consistente alto desempenho e baixa latência usando a conta de armazenamento Premium.
**Servidor de processo** | Um servidor local virtual ou física executando o Windows Server 2012 R2<br/><br/> Recomendamos que ele é colocado na mesma rede e segmento LAN como máquinas que você deseja proteger, mas ela possa ser executada em uma rede diferente enquanto máquinas protegidas tiverem L3 visibilidade da rede-lo.<br/><br/> Você configurá-lo e registre-o para o servidor de configuração no portal de recuperação do Site. | Máquinas protegidas enviem dados de replicação para o servidor de processo local. Ela tem uma replicação de cache para cache baseado em disco dados que ele recebe. Ele executa várias ações nos dados.<br/><br/> Ele otimiza dados cache, compactando e criptografando-o antes de enviá-lo para o servidor de destino mestre.<br/><br/> Trata instalação de envio do serviço de mobilidade.<br/><br/> Ele executa a descoberta automática de máquinas virtuais VMware.
**Máquinas de local** | Locais máquinas virtuais VMware ou físicos servidores que executam o Windows ou Linux. | Configurar definições de replicação que se aplicam a um ou mais computadores. Você pode falhar ao longo de uma máquina individual ou mais comum, várias máquinas que você reunir em um plano de recuperação. 
**Serviço de mobilidade** | Instalado em cada máquina virtual ou o servidor físico que você deseja proteger<br/><br/> Podem ser instalados manualmente ou enviados e instalado automaticamente pelo servidor processo quando você habilita a replicação para um computador. | O serviço de mobilidade envia dados ao servidor do processo durante a replicação inicial (nova sincronização). Depois que o computador estiver em um estado protegido (depois de concluir a nova sincronização) o serviço de mobilidade captura gravações disco na memória e envia para o servidor de processo. Applicationconsistency para servidores Windows é alcançado usando VSS.
**Azure Cofre de recuperação do Site** | Crie um cofre de recuperação de Site com uma assinatura do Azure e registrar servidores no cofre. | O cofre coordenadas e organiza replicação de dados, failover e recuperação entre o seu site local e o Azure.
**Mecanismo de replicação** | **Sobre a Internet**— comunica e replica dados de servidores local protegida no Azure usando seguro SSL/TLS canal pela internet. Esta é a opção padrão.<br/><br/> **VPN/rota expressa**— comunica e replica dados entre servidores locais e Azure ao longo de uma conexão VPN. Você precisará configurar uma VPN to-site ou uma conexão de rota expressa entre o seu site local e a rede do Azure.<br/><br/> Você selecionará como você deseja duplicar durante a implantação de recuperação do Site. Você não pode alterar o mecanismo quando ela está configurada sem afetar replicação de máquinas existentes. | Nenhuma opção requer que você abra qualquer portas de rede de entrada em máquinas protegidas. Todas as comunicações de rede é iniciada a partir do site local. 

## <a name="capacity-planning"></a>Planejamento da capacidade

As principais áreas que você precisará considerar:

- **Ambiente de origem**— VMware a infraestrutura, configurações de máquina de origem e requisitos.
- **Servidores de componentes**— o servidor processo, o servidor de configuração e o servidor de destino mestre 

### <a name="considerations-for-the-source-environment"></a>Considerações para o ambiente de origem

- **Tamanho máximo do disco**— o tamanho máximo atual do disco que pode ser anexado a uma máquina virtual é 1 TB. Portanto, o tamanho máximo de um disco de origem que pode ser replicado também está limitado aos 1 TB.
- **Tamanho máximo por fonte**— o tamanho máximo de uma máquina única fonte é 31 TB (com 31 discos) e com uma instância de D14 provisionada para o servidor de destino mestre. 
- **Número de fontes por servidor de destino mestre**— várias máquinas de fonte podem ser protegidas com um servidor de destino de mestre único. Entretanto, uma máquina única fonte não pode ser protegida em vários servidores de destino mestre, porque como discos replicam, um VHD que reflete o tamanho do disco é criado no armazenamento de blob do Microsoft Azure e anexada como um disco de dados para o servidor mestre de destino.  
- **Taxa por fonte de alteração de máxima diariamente**— há três fatores que precisam ser considerados ao levar em consideração a taxa de alteração recomendada por origem. Para as considerações de destino com base em duas IOPS são necessárias no disco de destino para cada operação na origem. Isso ocorre porque a leitura de dados antigos e uma gravação de novos dados acontecerá no disco de destino. 
    - **Diária alterar taxa suportada pelo servidor processo**— uma máquina de origem não pode abranger vários servidores de processo. Um servidor de um único processo pode oferecer suporte a até 1 TB de taxa de alteração diária. Portanto, 1 TB é que o máximo de dados diário alterar taxa tem suportada para uma máquina de origem. 
    - **Produtividade máximo compatíveis com o disco de destino**— rotatividade máximo por disco de origem não pode ser mais de 144 GB/dia (com tamanho de gravação de 8 K). Consulte a tabela na seção de destino mestre para a produtividade e IOPs do destino de vários tamanhos de gravação. Esse número deve ser dividido por dois porque cada fonte IOP gera 2 IOPS no disco de destino. Leia sobre [Azure destinos de desempenho e escalabilidade](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) ao configurar o destino para contas de armazenamento premium.
    - **Produtividade máximo compatível com a conta de armazenamento**— uma fonte não pode abranger várias contas de armazenamento. Considerando que uma conta de armazenamento leva um máximo de 20.000 solicitações por segundo e que cada fonte IOP gera 2 IOPS no servidor de destino mestre, recomendamos que você mantenha o número de IOPS entre a fonte para 10.000. Leia sobre [Azure destinos de desempenho e escalabilidade](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) ao configurar a fonte para contas de armazenamento premium.

### <a name="considerations-for-component-servers"></a>Considerações para servidores de componentes

Tabela 1 resume os tamanhos de máquina virtual para a configuração e os servidores de destino mestre.

**Componente** | **Instâncias de Azure implantadas** | **Cores** | **Memória** | **Discos max** | **Tamanho do disco**
--- | --- | --- | --- | --- | ---
Servidor de configuração | A3 padrão | 4 | 7 GB | 8 | GB 1023
Servidor de destino mestre | A4 padrão | 8 | 14 GB | 16 | GB 1023
 | D14 padrão | 16 | GB 112 | 32 | GB 1023
 | DS4 padrão | 8 | 28 GB | 16 | GB 1023

**Tabela 1**

#### <a name="process-server-considerations"></a>Considerações de processo de servidor

Geralmente dimensionamento de servidor de processo depende da taxa de alteração diária em todas as cargas de trabalho protegidas.


- Você precisa de computação suficiente para realizar tarefas como embutida compactação e criptografia.
- O servidor de processo usa cache baseado em disco. Verifique se o espaço recomendado do cache e taxa de transferência de disco está disponível para facilitar as alterações de dados armazenadas em caso de interrupção de afunilamento de rede. 
- Garantir a largura de banda suficiente para que o servidor processo pode carregar os dados para o servidor de destino mestre para fornecer proteção contínua de dados. 

Tabela 2 fornece um resumo das diretrizes de servidor de processo.

**Taxa de alteração de dados** | **CPU** | **Memória** | **Tamanho do cache de disco**| **Taxa de transferência do cache em disco** | **Entrada/saída de largura de banda**
--- | --- | --- | --- | --- | ---
< 300 GB | 4 vCPUs (2 sockets * 2 cores @ 2,5 GHz) | 4 GB | 600 GB | 7 a 10 MB por segundo | 30 Mbps/21 Mbps
300 para 600 GB | 8 vCPUs (2 sockets * 4 cores @ 2,5 GHz) | 6 GB | 600 GB | 11 a 15 MB por segundo | 60 Mbps/42 Mbps
600 GB a 1 TB | 12 vCPUs (2 sockets * 6 cores @ 2,5 GHz) | 8 GB | 600 GB | 16 a 20 MB por segundo | 100 Mbps Mbps/70
> 1 TB | Implantar outro servidor de processo | | | | 

**Tabela 2**

Onde: 

- Ingresso é largura de banda do download (intranet entre o servidor de origem e de processo).
- Saída é a largura de banda de carregamento (internet entre o processo e servidor de destino mestre). Números de egresso presumem compactação média de servidor de processo de 30%.
- Cache de disco um disco separado do sistema operacional de 128 GB mínimo é recomendado para todos os servidores de processo.
- Para taxa de transferência de disco de cache o armazenamento seguinte foi usado para analisar o desempenho: 8 unidades SAS de 10 K RPM com a configuração de RAID 10.

#### <a name="configuration-server-considerations"></a>Considerações de servidor de configuração

Cada servidor de configuração pode oferecer suporte a até 100 máquinas de origem com volumes de 3-4. Se sua implantação é maior, recomendamos que você implantar outro servidor de configuração. Consulte a tabela 1 para as propriedades de máquina virtual padrão do servidor de configuração. 

#### <a name="master-target-server-and-storage-account-considerations"></a>Considerações de conta de servidor e armazenamento de destino mestre

O armazenamento para cada servidor de destino mestre inclui um disco de sistema operacional, um volume de retenção e discos de dados. A unidade de retenção mantém o diário das alterações de disco para a duração da janela de retenção definida no portal de recuperação do Site.  Consulte a tabela 1 para as propriedades de máquina virtual do servidor mestre de destino. Tabela 3 mostra como as unidades dos A4 são usadas.

**Instância** | **Disco do sistema operacional** | **Retenção** | **Discos de dados**
--- | --- | --- | ---
 | | **Retenção** | **Discos de dados**
A4 padrão | 1 disco (1 * 1023 GB) | 1 disco (1 * 1023 GB) | 15 discos (15 * 1023 GB)
D14 padrão |  1 disco (1 * 1023 GB) | 1 disco (1 * 1023 GB) | 31 discos (15 * 1023 GB)
DS4 padrão |  1 disco (1 * 1023 GB) | 1 disco (1 * 1023 GB) | 15 discos (15 * 1023 GB)

**Tabela 3**

Capacidade de planejamento para o servidor mestre de destino depende:

- Limitações e desempenho de armazenamento do azure
    - O número máximo de altamente utilizados discos de uma VM padrão de camada, aproximadamente 40 (20.000/500 IOPS por disco) em uma conta de armazenamento único. Leia sobre [os destinos de escalabilidade para sccounts de armazenamento padrão](../storage/storage-scalability-targets.md#scalability-targets-for-standard-storage-accounts) e para [premium armazenamento sccounts](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts).
-   Taxa de alteração diária 
-   Armazenamento de volume de retenção.

Observe que:

- Uma fonte não pode abranger várias contas de armazenamento. Isso se aplica para o disco de dados que vão para as contas de armazenamento selecionadas quando você configurar a proteção. O sistema operacional e os discos de retenção geralmente ir para a conta de armazenamento implantado automaticamente.
- O volume de armazenamento de retenção necessário depende da taxa de alteração diária e o número de dias de retenção. O armazenamento de retenção necessário por servidor de destino mestre = Rotatividade total de fonte por dia * número de dias de retenção. 
- Cada servidor de destino mestre tem apenas um volume de retenção. O volume de retenção é compartilhado entre os discos anexados ao servidor de destino mestre. Por exemplo:
    - Se houver uma máquina de origem com 5 discos e cada disco gera 120 IOPS (tamanho de 8K) na origem, isso se traduz em 240 IOPS por disco (2 operações no disco de destino por origem AE). 240 IOPS está dentro do Azure limite IOPS de disco de 500 por.
    - No volume de retenção, isso se torna 120 * 5 = 600 IOPS e isso podem se tornar um gargalo. Neste cenário, uma boa estratégia seria adicionar mais discos para o volume de retenção e abrangência-entre, como uma configuração de distribuição RAID. Isso melhora o desempenho porque o IOPS é distribuído entre várias unidades. O número de unidades a ser adicionado ao volume de retenção ficará assim:
        - Total de IOPS do ambiente de origem / 500
        - Rotatividade total por dia do ambiente de origem (descompactado) / 287 GB. 287 GB é a taxa de transferência máxima suportada por um disco de destino por dia. Esta métrica variará com base no tamanho da gravação com um fator de 8K, porque nesse caso 8K é nova pressupõe que o tamanho de gravação. Por exemplo, se o tamanho de gravação é 4K produtividade será 287/2. E se o tamanho de gravação for 16K produtividade será 287 * 2.
- O número de contas de armazenamento obrigatórias = fonte total IOPs/10000.


## <a name="before-you-start"></a>Antes de começar

**Componente** | **Requisitos** | **Detalhes**
--- | --- | --- 
**Conta do Azure** | Você precisará de uma conta [Do Microsoft Azure](https://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Armazenamento do Azure** | Você precisará de uma conta de armazenamento do Azure para armazenar dados duplicados<br/><br/> A conta deve ser uma [Conta de armazenamento de localização geográfica redundantes padrão](../storage/storage-redundancy.md#geo-redundant-storage) ou [Conta de armazenamento Premium](../storage/storage-premium-storage.md).<br/><br/> Ele deve na mesma região como o serviço de recuperação de Site do Azure e ser associadas a mesma assinatura. Não há suporte para a movimentação de contas de armazenamento criados usando o [novo portal Azure](../storage/storage-create-storage-account.md) em grupos de recursos.<br/><br/> Para saber mais, leia [Introdução ao armazenamento do Microsoft Azure](../storage/storage-introduction.md)
**Rede virtual Azure** | Você precisará de uma rede virtual Azure no qual o servidor de configuração e destino mestre serão implantadas. Ele deve ser na mesma assinatura e região como o Cofre de recuperação de Site do Azure. Se você quiser replicar dados em uma conexão VPN ou rota expressa a rede virtual Azure deve estar conectada à sua rede local por meio de uma conexão de rota expressa ou uma VPN to-Site.
**Recursos do Azure** | Verifique se que você tem recursos suficientes Azure para implantar todos os componentes. Leia mais em [Limites de assinatura do Azure](../azure-subscription-service-limits.md).
**Azure máquinas virtuais** | Máquinas virtuais que deseja proteger deve estar em conformidade com [pré-requisitos Azure](site-recovery-best-practices.md).<br/><br/> **Contagem de disco**— um máximo de 31 discos pode ser suportado em um único servidor protegido<br/><br/> **Tamanhos de disco**— a capacidade de disco Individual não deve ser mais de 1023 GB<br/><br/> **Cluster**— os servidores de cluster não são suportados<br/><br/> **Inicialização**— Unified Extensible Firmware Interface (UEFI) / Extensible Firmware Interface(EFI) inicialização não é compatíveis<br/><br/> **Volumes**— Bitlocker volumes criptografados não são suportados<br/><br/> **Nomes de servidor**— nomes devem conter entre 1 e 63 caracteres (letras, números e hifens). O nome deve começar com uma letra ou número e termina com uma letra ou número. Depois que uma máquina estiver protegida, você pode modificar o nome do Azure.
**Servidor de configuração** | Máquina de virtual A3 padrão com base em uma imagem da Galeria de recuperação de Site Azure Windows Server 2012 R2 será criada em sua assinatura para o servidor de configuração. Ele é criado como a primeira instância em um novo serviço de nuvem. Se você selecionar Internet pública como o tipo de conectividade para o servidor de configuração do serviço de nuvem será criado com um endereço IP público reservado.<br/><br/> O caminho de instalação deve estar no somente caracteres em inglês.
**Servidor de destino mestre** | Azure máquina virtual padrão A4, D14 ou DS4.<br/><br/> O caminho de instalação deve estar no somente caracteres em inglês. Por exemplo, o caminho deve ser **/usr/local/ASR** para um servidor de destino mestre executando Linux.
**Servidor de processo** | Você pode implantar o servidor de processo em física ou máquina virtual que executa o Windows Server 2012 R2 com as últimas atualizações. Instalar em c: /.<br/><br/> Recomendamos que você coloca o servidor na mesma rede e sub-rede como as máquinas que você deseja proteger.<br/><br/> Instale o VMware vSphere CLI 5.5.0 no servidor de processo. O componente do VMware vSphere CLI é necessário no servidor processo para descobrir máquinas virtuais gerenciadas por um servidor vCenter ou máquinas virtuais em execução em um host ESXi.<br/><br/> O caminho de instalação deve estar no somente caracteres em inglês.<br/><br/> Não há suporte para o sistema de arquivos de referências.
**VMware** | Um servidor de vCenter VMware Gerenciando sua hipervisores do VMware vSphere. Ele deve estar executando vCenter versão 5.1 ou 5.5 com as últimas atualizações.<br/><br/> Um ou mais hipervisores vSphere contendo máquinas virtuais VMware que você deseja proteger. O hipervisor deve estar executando ESX/ESXi versão 5.1 ou 5.5 com as últimas atualizações.<br/><br/> Máquinas virtuais VMware deve ter ferramentas de VMware instalado e em execução. 
**Máquinas do Windows** | Servidores físicos protegidos ou máquinas virtuais VMware a executando o Windows tem um número de requisitos.<br/><br/> Suporte de um sistema operacional de 64 bits: **Windows Server 2012 R2**, **Windows Server 2012**, ou **Windows Server 2008 R2 com no mínimo SP1**.<br/><br/> O nome do host, os pontos de montagem, os nomes de dispositivo, os caminho de sistema do Windows (ex: C:\Windows) deve estar somente em inglês.<br/><br/> O sistema operacional deve ser instalado na unidade de disco C:\.<br/><br/> Apenas discos básicos são suportados. Discos dinâmicos não têm suporte.<br/><br/> Regras de firewall em máquinas protegidas devem permitir que ele atingir os servidores de destino de configuração e mestre em Azure.p ><p>Você precisará fornecer uma conta de administrador (deve ser um administrador local na máquina Windows) para envio instalar o serviço de mobilidade em servidores Windows. Se a conta fornecida é uma conta de fora do domínio que você precisará desativar o controle de acesso de usuário remoto no computador local. Para fazer isso adicione a entrada de registro LocalAccountTokenFilterPolicy DWORD com um valor de 1 em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Para adicionar a entrada do registro de um cmd abrir CLI ou powershell e digite **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. [Saiba mais](https://msdn.microsoft.com/library/aa826699.aspx) sobre o controle de acesso.<br/><br/> Após o failover, certifique-se de que a área de trabalho remota está habilitada para a máquina local se desejar conectar-se a máquinas virtuais do Windows no Azure com área de trabalho remota. Se você não estiver se conectando através de VPN, regras de firewall devem permitir conexões de área de trabalho remota pela internet.
**Máquinas Linux** | Um sistema operacional de 64 bits com suporte: **Centos 6.4, 6.5, 6.6**; **Oracle Enterprise Linux 6.4, 6.5 executando o Red Hat compatível núcleo ou o Unbreakable Enterprise núcleo versão 3 (UEK3)**, **SUSE Linux Enterprise Server 11 SP3**.<br/><br/> Regras de firewall em máquinas protegidas devem permiti-los alcançar a configuração e os servidores de destino mestre no Azure.<br/><br/> arquivos de /etc/hosts em máquinas protegidas devem conter entradas que mapeiam o nome de host local para endereços IP associados a todas as NICs <br/><br/> Se você quiser se conectar a uma máquina virtual Azure executando Linux após failover usando um Secure Shell cliente (ssh), verifique se o serviço de Secure Shell na máquina protegida está definido para iniciar automaticamente na inicialização do sistema e que as regras de firewall permitem que um ssh conexão a ela.<br/><br/> O nome do host, pontos de montagem, nomes de dispositivo e caminhos de sistema do Linux e nomes de arquivo (ex/etc /; /usr) devem estar apenas em inglês.<br/><br/> Proteção pode ser habilitada para máquinas locais com o armazenamento a seguir:-<br>Sistema de arquivos: EXT3, ETX4, ReiserFS, XFS<br>Vários caminhos dispositivo software mapeador (vários caminhos)<br>Gerenciador de volume: LVM2<br>Servidores físicos com o armazenamento de controlador HP CCISS não são suportados.
**Terceiro** | Alguns componentes de implantação neste cenário dependem do software de terceiros para funcionar corretamente. Para obter uma lista completa, consulte [informações e avisos de softwares de terceiros](#third-party)


### <a name="network-connectivity"></a>Conectividade de rede

Você tem duas opções ao configurar a conectividade de rede entre seu site local e a rede virtual Azure no qual os componentes de infraestrutura (servidor de configuração, servidores de destino mestre) são implantados. Você precisará decidir qual opção de conectividade de rede para usar antes de implantar o servidor de configuração. Você precisará escolher esta configuração no momento da implantação. Ele não pode ser alterado posteriormente.

**Internet:** Comunicação e replicação de dados entre os servidores de local (servidor de processo, máquinas protegidas) e os servidores de componentes de infraestrutura Azure (servidor de configuração, servidor de destino mestre) acontece sobre uma conexão SSL/TLS seguro do local para os pontos de extremidade públicos nos servidores de destino configuração e mestre. (A única exceção é a conexão entre o servidor de processo e o servidor de destino mestre na porta TCP 9080 que é não criptografado. Somente controle relacionadas ao protocolo replicação de configuração de replicação são trocadas informações sobre esta conexão.)

![Internet de diagrama de implantação](./media/site-recovery-vmware-to-azure-classic-legacy/internet-deployment.png)

**VPN**: comunicação e replicação de dados entre os servidores de local (servidor de processo, máquinas protegidas) e os servidores de componentes de infraestrutura Azure (servidor de configuração, servidor de destino mestre) acontece sobre uma conexão VPN entre sua rede local e a rede virtual Azure no qual o servidor de configuração e os servidores de destino mestre são implantados. Certifique-se de que sua rede local está conectado à rede virtual Azure por uma conexão de rota expressa ou uma conexão de VPN-to-site.

![Diagrama de implantação VPN](./media/site-recovery-vmware-to-azure-classic-legacy/vpn-deployment.png)


## <a name="step-1-create-a-vault"></a>Etapa 1: Criar um cofre

1. Entre [Portal de gerenciamento](https://portal.azure.com).


2. Expanda **Serviços de dados** > **Serviços de recuperação** e clique em **Cofre de recuperação do Site**.


3. Clique em **Criar novo** > **criação rápida**.

4. Em **nome**, digite um nome amigável para identificar o cofre.

5. Na **região**, selecione a região geográfica para o cofre. Para verificar regiões com suporte consulte disponibilidade geográficos em [Detalhes de preços de recuperação do Azure Site](https://azure.microsoft.com/pricing/details/site-recovery/)

6. Clique em **criar cofre**.

    ![Novo cofre](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-create-vault.png)

Verifique a barra de status para confirmar que o cofre foi criado com êxito. O cofre será listado como **ativo** na página de **Serviços de recuperação** de principal.

## <a name="step-2-deploy-a-configuration-server"></a>Etapa 2: Implantar um servidor de configuração

### <a name="configure-server-settings"></a>Configurar definições de servidor

1. Na página **Serviços de recuperação** , clique no cofre para abrir a página de início rápido. Início rápido também pode ser aberto a qualquer momento usando o ícone.

    ![Ícone de início rápido](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-icon.png)

2. Na lista suspensa, selecione **entre um site local com os servidores de VMware/física e Azure**.
3. Em **Preparar Target(Azure) recursos** , clique em **Implantar o servidor de configuração**.

    ![Implantar o servidor de configuração](./media/site-recovery-vmware-to-azure-classic-legacy/deploy-cs2.png)

4. Na **Nova detalhes de servidor de configuração** especifique:

    - Um nome para o servidor de configuração e credenciais para se conectar a ele.
    - No tipo de conectividade de rede lista suspensa Selecione **Internet pública** ou **VPN**. Observe que você não pode modificar essa configuração depois que ela é aplicada.
    - Selecione a rede Azure em que o servidor deve ser localizado. Se você estiver usando tornar VPN sabe Azure rede está conectada à sua rede local conforme esperado. 
    - Especifique o endereço IP interno e sub-rede que será atribuída ao servidor. Observe que os quatro primeiros endereços IP em qualquer sub-rede são reservados para uso interno do Azure. Use qualquer outro endereço IP disponível.
    
    ![Implantar o servidor de configuração](./media/site-recovery-vmware-to-azure-classic-legacy/cs-details.png)

5. Quando você clica em **Okey** uma máquina de virtual A3 padrão com base em uma imagem da Galeria de recuperação de Site Azure Windows Server 2012 R2 será criada em sua assinatura para o servidor de configuração. Ele é criado como a primeira instância em um novo serviço de nuvem. Se você tiver selecionado para se conectar pela internet o serviço de nuvem é criado com um endereço IP público reservado. Você pode monitorar o andamento na guia **trabalhos** .

    ![Monitorar o progresso](./media/site-recovery-vmware-to-azure-classic-legacy/monitor-cs.png)

6.  Se você estiver se conectando através da internet, após o servidor de configuração nota implantada o endereço IP público atribuída a ela na página **máquinas virtuais** no portal do Azure. Então sobre os **pontos de extremidade** de anotação de guia a porta HTTPS pública mapeado para particular porta 443. Você precisará dessas informações mais tarde, quando você registrar o destino mestre e os servidores de processo com o servidor de configuração. O servidor de configuração seja implantado com esses pontos de extremidade:

    - HTTPS: Uma porta pública é usada para coordenar a comunicação entre servidores de componentes e Azure pela internet. Particular porta 443 é usada para coordenar a comunicação entre servidores de componentes e Azure através de VPN.
    - Personalizado: Uma porta pública é usada para failback ferramenta comunicação pela internet. Porta privada 9443 é usada para comunicação de ferramenta de failback através de VPN.
    - PowerShell: Porta privada 5986
    - Área de trabalho remota: particular porta 3389
    
    ![Pontos de extremidade de máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/vm-endpoints.png)

    >[AZURE.WARNING] Não exclua ou altere o número da porta público ou privado de pontos de extremidade quaisquer criada durante a implantação do servidor de configuração.

O servidor de configuração está implantado em um serviço de nuvem Azure criados automaticamente com um endereço IP reservado. O endereço reservado é necessária para garantir que o endereço IP de serviço de nuvem do configuração servidor permanece o mesmo em reinicialização das máquinas virtuais (incluindo o servidor de configuração), o serviço de nuvem. O endereço IP público reservado precisará ser manualmente não reservadas quando o servidor de configuração é desativado ou ele vai permanecer reservado. Não há um limite padrão de 20 público os endereços IP reservados por assinatura. [Saiba mais](../virtual-network/virtual-networks-reserved-private-ip.md) sobre os endereços IP reservados. 

### <a name="register-the-configuration-server-in-the-vault"></a>Registrar o servidor de configuração no cofre

1. Na página de **Início rápido** , clique em **Recursos de destino de preparar** > **baixar uma chave do registro**. O arquivo de chave é gerado automaticamente. Ele é válido para 5 dias depois que ela é gerada. Copiá-lo para o servidor de configuração.
2. Em **máquinas virtuais** selecione o servidor de configuração da lista de máquinas virtuais. Abra a guia **painel** e clique em **Conectar**. **Abrir** o arquivo baixado do RDP para fazer logon no servidor de configuração usando a área de trabalho remota. Se você estiver usando VPN, use o endereço IP interno (o endereço especificado quando você implantou o servidor de configuração) para uma conexão de área de trabalho remota do site local. Assistente de configuração de servidor de configuração de Azure Site recuperação é executado automaticamente quando você fizer logon pela primeira vez.

    ![Registro](./media/site-recovery-vmware-to-azure-classic-legacy/splash.png)

3. Na **Instalação do Software de terceiros** , clique em **aceito** para baixar e instalar MySQL.

    ![Instalar MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-eula.png)

4. Em **Detalhes do servidor MySQL** crie credenciais para fazer logon a instância do servidor MySQL.

    ![Credenciais de MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-password.png)

5. Em **Configurações de Internet** Especifica como o servidor de configuração irão se conectar à internet. Observe que:

    - Se você quiser usar um proxy personalizado, que você deve configurá-lo antes de instalar o provedor.
    - Quando você clicar em **Avançar** um teste será executado para verificar a conexão de proxy.
    - Se você usar um proxy personalizado, ou seu proxy padrão requer autenticação, que você precisará inserir os detalhes de proxy, incluindo o endereço, porta e credenciais.
    - As seguintes URLs devem ser acessados através do proxy:
        - *. hypervrecoverymanager.windowsazure.com
        - *. accesscontrol.windows.net
        - *. backup.windowsazure.com
        - *. blob.core.windows.net
        - *. store.core.windows.net
    - Se você tiver com base em endereço IP regras de firewall garantem que as regras são definidas para permitir a comunicação do servidor de configuração para os endereços IP descrito em [Intervalos de IP de data center do Azure](https://msdn.microsoft.com/library/azure/dn175718.aspx) e HTTPS protocolo (443). Você teria intervalos de IP de lista branca do Azure região que você planeja usar e do Oeste EUA.

    ![Registro de proxy](./media/site-recovery-vmware-to-azure-classic-legacy/register-proxy.png)

6. Em **Configurações de localização de mensagem de erro de provedor** Especifica em qual idioma você deseja que mensagens de erro apareça.

    ![Registro de mensagem de erro](./media/site-recovery-vmware-to-azure-classic-legacy/register-locale.png)

7. No **Registro de recuperação de Site do Azure** procurar e selecione o arquivo de chave que foi copiado para o servidor.

    ![Registro de arquivo de chave](./media/site-recovery-vmware-to-azure-classic-legacy/register-vault.png)

8. Na página de conclusão do assistente, selecione estas opções:

    - Selecione **Iniciar diálogo de gerenciamento de conta** para especificar que a caixa de diálogo Gerenciar contas deve abrir após concluir o assistente.
    - Selecione **criar um ícone da área de trabalho para Cspsconfigtool** para adicionar um atalho da área de trabalho no servidor de configuração para que você possa abrir a caixa de diálogo **Gerenciar contas** a qualquer momento sem precisar executar novamente o assistente.

    ![Registro concluído](./media/site-recovery-vmware-to-azure-classic-legacy/register-final.png)

9. Clique em **Concluir** para concluir o assistente. Uma senha é gerada. Copie-o em um local seguro. Você precisará delas para autenticar e registrar o processo e os servidores de destino mestre com o servidor de configuração. Ele também é usado para garantir a integridade do canal em comunicações de servidor de configuração. Você pode gerar novamente a senha, mas, em seguida, será preciso registrar novamente o destino mestre e processar servidores usando a nova senha.

    ![Senha](./media/site-recovery-vmware-to-azure-classic-legacy/passphrase.png)

Após o registro do servidor de configuração será listado na página **Configuração Servers** no cofre.

### <a name="set-up-and-manage-accounts"></a>Configurar e gerenciar contas

Durante a implantação recuperação do Site solicita credenciais para as seguintes ações:

- Uma conta de VMware para thatSite recuperação automaticamente possa descoberta VMs em servidores de vCenter ou vSphere hosts. 
- Quando você adiciona máquinas para proteção, para que a recuperação do Site pode instalar o serviço de mobilidade neles.

Após ter registrado o servidor de configuração, você pode abrir a caixa de diálogo **Gerenciar contas** para adicionar e gerenciar contas que devem ser usadas para essas ações. Há algumas maneiras de fazer isso:

- Abra o atalho que você optado pelo criado para a caixa de diálogo na última página de instalação para o servidor de configuração (cspsconfigtool).
- Abrir a caixa de diálogo no término da instalação do servidor de configuração.

1. Em **Gerenciar contas** , clique em **Adicionar conta**. Você também pode modificar e excluir contas existentes.

    ![Gerenciar contas](./media/site-recovery-vmware-to-azure-classic-legacy/manage-account.png)

2. Em **Detalhes da conta** , especifique um nome de conta para usar no Azure e credenciais (nome de usuário do domínio). 

    ![Gerenciar contas](./media/site-recovery-vmware-to-azure-classic-legacy/account-details.png)

### <a name="connect-to-the-configuration-server"></a>Conectar ao servidor de configuração 

Há duas maneiras de se conectar ao servidor de configuração:

- Em uma VPN site-to-site ou conexão de rota expressa
- Pela internet 

Observe que:

- Uma conexão de internet usa os pontos de extremidade da máquina virtual em conjunto com o endereço IP virtual público do servidor.
- Uma conexão VPN usa o endereço IP interno do servidor junto com as portas particular do ponto de extremidade.
- É uma decisão única para decidir se conectem (dados de controle e replicação) dos seus servidores de local para os servidores de componente diversos (configuração server, servidor de destino mestre) em execução no Azure por uma conexão de VPN ou pela internet. Você não pode alterar essa configuração posteriormente. Se você não fizer você precisará reimplantar o cenário e reprotect suas máquinas.  


## <a name="step-3-deploy-the-master-target-server"></a>Etapa 3: Implantar o servidor de destino mestre

1. Clique em **Preparar recursos de Target(Azure)** > **servidor de destino mestre de implantar**.
2. Especifique os detalhes do servidor de destino mestre e credenciais. O servidor será implantado na mesma rede Azure como o servidor de configuração. Quando você clica em para concluir uma máquina virtual Azure será criada com uma imagem da Galeria Windows ou Linux.

    ![Configurações de servidor de destino](./media/site-recovery-vmware-to-azure-classic-legacy/target-details.png)

Observe que os quatro primeiros endereços IP em qualquer sub-rede são reservados para uso interno do Azure. Especifica qualquer outro endereço IP disponível.

>[AZURE.NOTE] Selecione DS4 padrão quando configurar proteção para cargas de trabalho que exigem consistente alto desempenho de e/s e baixa latência para hospedar cargas de trabalho intenso e/s usando a [Conta de armazenamento Premium](../storage/storage-premium-storage.md).


3. Um Windows mestre máquina virtual é criado com esses pontos de extremidade do servidor de destino. Observe que os pontos de extremidade públicos são criados somente se seu conectando através da internet.

    - Personalizado: Porta de pública usada pelo servidor de processo para enviar dados de replicação pela internet. Porta privada 9443 é usada pelo servidor de processo para enviar dados de replicação ao servidor de destino mestre pela VPN.
    - Custom1: Porta do pública usada pelo servidor de processo para enviar metadados pela internet. Porta privada 9080 é usada pelo servidor de processo para enviar metadados ao servidor de destino mestre pela VPN.
    - PowerShell: Porta privada 5986
    - Área de trabalho remota: particular porta 3389

4. Um servidor de destino mestre Linux máquina virtual é criado com esses pontos de extremidade. Observe que os pontos de extremidade públicos são criados somente se você estiver se conectando pela internet.

    - Personalizado: Porta de pública usada pelo servidor de processo para enviar dados de replicação pela internet. Porta privada 9443 é usada pelo servidor de processo para enviar dados de replicação ao servidor de destino mestre pela VPN.
    - Custom1: Porta pública é usada pelo servidor de processo para enviar metadados pela internet. Porta privada 9080 é usada pelo servidor de processo para enviar metadados ao servidor de destino mestre pela VPN
    - SSH: Porta privada 22

    >[AZURE.WARNING] Não exclua ou altere o número da porta público ou privado de qualquer um dos pontos de extremidade criados durante a implantação de servidor de destino mestre.

5. Em **máquinas virtuais** aguardar a máquina virtual para começar.

    - Se for uma anotação de servidor Windows para baixo os detalhes da área de trabalho remotas.
    - Se for um servidor Linux e estiver se conectando através de VPN Observe o endereço IP interno da máquina virtual. Se você estiver se conectando pela internet Observe o endereço IP público.

6.  Fazer logon no servidor para concluir a instalação e registre-o com o servidor de configuração. 
7.  Se você estiver executando o Windows:

    1. Inicie uma conexão de área de trabalho remota na máquina virtual. Na primeira vez que você faça logon em um script será executado em uma janela do PowerShell. Não feche-o. Ao terminar a ferramenta de configuração do agente de Host é aberto automaticamente para registrar o servidor.
    2. Na **Configuração do agente de Host** especifique o endereço IP interno do servidor de configuração e porta 443. Você pode usar o endereço interno e privada 443, mesmo se você não estiver se conectando através de VPN porque a máquina virtual está anexada a mesma rede Azure que o servidor de configuração. Deixe **Usar HTTPS** habilitado. Insira a senha para o servidor de configuração que você anotou anteriormente. Clique em **Okey** para registrar o servidor. Você pode ignorar as opções de NAT. Eles não são usados.
    3. Se sua necessidade de unidade de retenção estimado for mais de 1 TB, você pode configurar o volume de retenção (r) usando um disco virtual e [espaços de armazenamento](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)
    
    ![Servidor de destino mestra do Windows](./media/site-recovery-vmware-to-azure-classic-legacy/target-register.png)

8. Se você estiver executando Linux:
    1. Verifique se que você instalou as últimas Linux integração serviços (lista com) instalado antes de instalar o servidor de destino mestre. Você pode encontrar a versão mais recente da lista com juntamente com instruções sobre como instalar [aqui](https://www.microsoft.com/download/details.aspx?id=46842). Reinicie o computador depois de instala a lista de com.
    2. Em **recursos de destino preparar (Azure)** , clique em **baixar e instalar o software adicional (somente para o servidor de destino Linux mestre)**. Copie o arquivo baixado tar na máquina virtual usando um cliente de sftp. Alternativamente você pode fazer logon no servidor de destino mestre linux implantado e use *wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* para baixar o arquivo.
    2. Faça logon no servidor usando um cliente de Secure Shell. Se você estiver conectado à rede Azure através de VPN use o endereço IP interno. Caso contrário, use o endereço IP externo e o ponto de extremidade do SSH público.
    3. Extrair os arquivos do instalador gzip executando: **tar – xvzf Microsoft-ASR_UA_8.4.0.0_RHEL6-64***
    ![servidor de destino mestre Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-tar.png)
    4. Verifique se que você está no diretório ao qual você extraído o conteúdo do arquivo tar.
    5. Copiar a senha do servidor de configuração para um arquivo local usando o comando * *eco* `<passphrase>` * > passphrase.txt**
    6. Execute o comando "**sudo. instalar -t ambas as - a -R hospedar /usr/local/ASR -d MasterTarget -i* `<Configuration server internal IP address>` * -p 443 -s y - c https -P passphrase.txt**".

    ![Registrar o servidor de destino](./media/site-recovery-vmware-to-azure-classic-legacy/linux-mt-install.png)

9. Aguarde alguns minutos (10-15) e na página, verifique se o servidor de destino mestre está listado como registrado nos **servidores** > guia de **Detalhes do servidor** de**Servidores de configuração** . Se você estiver executando Linux e não registrou execute o host de ferramenta de configuração novamente /usr/local/ASR/Vx/bin/hostconfigcli. Você precisará definir permissões de acesso executando chmod como raiz.

    ![Verifique se o servidor de destino](./media/site-recovery-vmware-to-azure-classic-legacy/target-server-list.png)

>[AZURE.NOTE] Pode levar até 15 minutos após a conclusão do servidor de destino mestre seja listado no portal do registro. Para atualizar imediatamente, clique em **Atualizar** na página **Configuração Servers** .

## <a name="step-4-deploy-the-on-premises-process-server"></a>Etapa 4: Implantar o servidor do processo de local

Antes de começar, é recomendável que você configure um endereço IP estático no servidor de processo para que ele garantir que seja persistente reinicializações.

1. Clique em Quick Start > **instalar servidores de processo no local** > **Baixe e instale o servidor processo**.

    ![Instalar o servidor de processo](./media/site-recovery-vmware-to-azure-classic-legacy/ps-deploy.png)

2.  Copie o arquivo zip baixado para o servidor no qual você vai instalar o servidor de processo. O arquivo zip contém dois arquivos de instalação:

    - Microsoft-ASR_CX_TP_8.4.0.0_Windows*
    - Microsoft-ASR_CX_8.4.0.0_Windows*

3. Descompacte o arquivo morto e copiar os arquivos de instalação para um local no servidor.
4. Executar o **Microsoft-ASR_CX_TP_8.4.0.0_Windows*** instalação de arquivo e siga as instruções. Isso instala componentes de terceiros necessários para a implantação.
5. Execute **Microsoft-ASR_CX_8.4.0.0_Windows***.
6. Na página **Modo de servidor** , selecione **Servidor de processo**.
7. Na página **Detalhes do ambiente** , faça o seguinte:


    - Se você deseja proteger VMware virtual máquinas clique em **Sim**
    - Se você só quiser proteger servidores físicos e, portanto, não é necessário vCLI VMware instalado no servidor do processo. Clique em **não** e continue.

8. Ao instalar o VMware vCLI, observe o seguinte:

    - **Somente a VMware vSphere CLI 5.5.0 é suportado**. O servidor processo não funciona com outras versões ou atualizações de vSphere CLI.
    - Baixar vSphere CLI 5.5.0 de [aqui.](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)
    - Se você instalou vSphere CLI logo antes de você começou a instalação do servidor de processo e configuração não o detectar, aguarde até cinco minutos antes de tentar a instalação novamente. Isso garante que todas as variáveis de ambiente necessária para a detecção de CLI vSphere foram inicializadas corretamente.

9.  Em **Seleção de NIC para servidor de processo** , selecione o adaptador de rede que o servidor processo deve usar.

    ![Selecione adaptador](./media/site-recovery-vmware-to-azure-classic-legacy/ps-nic.png)

10. Em **detalhes de configuração do servidor**:

    - Para o endereço IP e porta, se você estiver se conectando através de VPN Especifica o endereço IP interno do servidor configuração e 443 para a porta. Caso contrário, especifique o endereço IP virtual público e mapeado público de ponto de extremidade HTTP.
    - Digite a senha do servidor de configuração.
    - Desmarque a **assinatura de software de serviço de mobilidade verificar** se desejar desativar a verificação quando você usa o envio automático para instalar o serviço. Verificação de assinatura necessidades de conectividade com a internet do servidor de processo.
    - Clique em **Avançar**.

    ![Servidor de configuração de registro](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cs.png)


11. Na **unidade selecione instalação** , selecione uma unidade de cache. O servidor de processo precisa uma unidade de cache com pelo menos 600 GB de espaço livre. Clique em **instalar**. 

    ![Servidor de configuração de registro](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cache.png)

12. Observe que talvez seja necessário reiniciar o servidor para concluir a instalação. No **Servidor de configuração** > **Detalhes do servidor** Verifique se o servidor processo é exibido e registrado com êxito no cofre.

>[AZURE.NOTE]Pode levar até 15 minutos após a conclusão do servidor de processo apareça conforme listado sob o servidor de configuração do registro. Para atualizar imediatamente, atualize o servidor de configuração clicando no botão Atualizar na parte inferior da página do servidor de configuração
 
![Validar servidor de processo](./media/site-recovery-vmware-to-azure-classic-legacy/ps-register.png)

Se você não desabilitar a verificação de assinatura para o serviço de mobilidade ao registrar o servidor de processo, você poderá fazer isso mais tarde da seguinte forma:

1. Fazer logon no servidor de processo como administrador e abra o arquivo C:\pushinstallsvc\pushinstaller.conf para edição. Na seção **[PushInstaller.transport]** adicione esta linha: **SignatureVerificationChecks = "0"**. Salve e feche o arquivo.
2. Reinicie o serviço de InMage PushInstall.


## <a name="step-5-update-site-recovery-components"></a>Etapa 5: Componentes de recuperação de Site de atualização

Componentes de recuperação de site são atualizados de vez em quando. Quando novas atualizações estiverem disponíveis, você deve instalá-los na seguinte ordem:

1. Servidor de configuração
2. Servidor de processo
3. Servidor de destino mestre
4. Ferramenta de failback (vContinuum)

### <a name="obtain-and-install-the-updates"></a>Obter e instalar as atualizações


1. Você pode obter atualizações para a configuração, processo e servidores mestre de destino de recuperação do Site **Dashboard**. Para a instalação Linux extrair os arquivos do instalador gzip e execute o comando "sudo. instalar" para instalar a atualização.
2. [Baixe](http://go.microsoft.com/fwlink/?LinkID=533813) a atualização mais recente para o tool(vContinuum) Failback.
3. Se você estiver executando máquinas virtuais ou servidores físicos que já tem o serviço de mobilidade instalado, você pode obter atualizações para o serviço da seguinte maneira:

    - **Opção 1**: baixar atualizações:
        - [Windows Server (somente 64 bits)](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
        - [CentOS 6.4,6.5,6.6 (somente 64 bits)](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
        - [Oracle Enterprise Linux 6.4,6.5 (somente 64 bits)](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
        - [SUSE Linux Enterprise Server SP3 (somente 64 bits)](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
        - Depois de atualizar o servidor processo a versão atualizada do serviço mobilidade estará disponível na pasta C:\pushinstallsvc\repository no servidor de processo.
    - **Opção 2**: se você tiver um computador com uma versão mais antiga do serviço mobilidade instalado, você pode atualizar automaticamente o serviço de mobilidade na máquina do portal de gerenciamento.

        1. Certifique-se de que o servidor processo é atualizado.
        2. Certifique-se de que o computador protegido é compatível com os [pré-requisitos](#install-the-mobility-service-automatically) para insistem automaticamente o serviço de mobilidade, para que a atualização funciona como esperado.
        2. Selecione o grupo de proteção, realce a máquina protegida e clique em **serviço de mobilidade de atualização**. Esse botão só está disponível se houver uma versão mais recente do serviço mobilidade. 

            ![Selecione vCenter server](./media/site-recovery-vmware-to-azure-classic-legacy/update-mobility.png)

Em selecionadas contas Especifica a conta de administrador a ser usado para atualizar o serviço de mobilidade no servidor protegido. Clique em Okey e aguarde o conclusão do trabalho disparado.


## <a name="step-6-add-vcenter-servers-or-vsphere-hosts"></a>Etapa 6: Adicionar vCenter servidores ou hosts de vSphere

1. Clique em **servidores** > **Servidores de configuração** > servidor de configuração >**Adicionar vCenter Server** para adicionar um host de servidor ou vSphere vCenter.

    ![Selecione vCenter server](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter.png)

2. Especificar detalhes para o servidor ou host e selecione o servidor de processo que será usado para descobri-lo.

    - Se o servidor vCenter não está executando a porta 443 padrão, especifique o número da porta em que o servidor vCenter está executando.
    - O servidor processo deve estar na mesma rede como o host do servidor/vSphere vCenter e deve ter VMware vSphere CLI 5.5.0 instalado.

    ![configurações do servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter4.png)


3. Depois que termina de descoberta servidor vCenter será listado sob os detalhes do servidor de configuração.

    ![configurações do servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter2.png)

4. Se você estiver usando uma conta de não-administrador para adicionar o servidor ou host, verifique se que a conta tem os seguintes privilégios:

    - contas do vCenter devem ter data center, armazenamento de dados, pasta, Host, rede, recurso, armazenamento modos de exibição, Máquina Virtual e privilégios de mudança distribuído vSphere habilitados.
    - contas de host de vSphere devem ter o data center, armazenamento de dados, pasta, Host, rede, recurso, Máquina Virtual e privilégios de mudança distribuído vSphere habilitados



## <a name="step-7-create-a-protection-group"></a>Etapa 7: Criar um grupo de proteção

1. Abrir **Itens protegidos** > **Grupo proteção** > **Criar grupo de proteção**.

    ![Criar grupo de proteção](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg1.png)

2. Na página **Especificar configurações de grupo de proteção** , especifique um nome para o grupo e selecione o servidor de configuração no qual você deseja criar o grupo.

    ![Configurações de grupo de proteção](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg2.png)

3. Na página **Especificar configurações de replicação** de definir as configurações de replicação que serão usadas para todas as máquinas no grupo.

    ![Replicação de grupo de proteção](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg3.png)

4. Configurações:
    - **Consistência de máquina virtual multi**: se você ativar isso cria pontos de recuperação consistentes com o aplicativo compartilhado nos computadores do grupo de proteção. Esta configuração é mais relevante quando todas as máquinas no grupo de proteção estão executando a mesma carga de trabalho. Todas as máquinas serão recuperadas ao mesmo ponto de dados. Disponível apenas para servidores do Windows.
    - **Limite de RPO**: alertas serão geradas quando a replicação de proteção de dados contínua RPO excede o valor de limite RPO configurado.
    - **Retenção de ponto de recuperação**: Especifica a janela de retenção. Máquinas protegidas podem ser recuperadas a qualquer ponto dentro esta janela.
    - **Frequência de instantâneo consistentes com o aplicativo**: Especifica a frequência contendo instantâneos consistentes com o aplicativo de pontos de recuperação serão criados.

Você pode monitorar o grupo de proteção conforme eles são criados na página **Itens protegidos** .

## <a name="step-8-set-up-machines-you-want-to-protect"></a>Etapa 8: Configurar máquinas que você deseja proteger

Você precisará instalar o serviço de mobilidade em máquinas virtuais e servidores físicos que você deseja proteger. Você pode fazer isso de duas maneiras:

- Notificações por push e instalá-lo em cada máquina do servidor processo automaticamente.
- Instale manualmente o serviço. 

### <a name="install-the-mobility-service-automatically"></a>Instalar o serviço de mobilidade automaticamente

Quando você adiciona máquinas a um grupo de proteção no serviço de mobilidade é automaticamente enviado e instalado em cada máquina pelo servidor de processo. 

**Envio instalar automaticamente o serviço de mobilidade em servidores Windows:** 

1. Instale as atualizações mais recentes para o servidor de processo, conforme descrito em [etapa 5: instalar atualizações mais recentes](#step-5-install-latest-updates)e certifique-se de que o servidor processo está disponível. 
2. Certifique-se de conectividade de rede entre o computador de origem e o servidor de processo e que a máquina de origem é acessível a partir do servidor de processo.  
3. Configure o firewall do Windows para permitir que o **arquivo e o compartilhamento de impressora** e **Instrumentação de gerenciamento do Windows**. Em configurações de Firewall do Windows, selecione a opção "Permitir um aplicativo ou recurso pelo Firewall" e selecione os aplicativos, conforme mostrado na figura abaixo. Para máquinas que fazem parte de um domínio, você pode configurar a política de firewall com um GPO.

    ![Configurações de firewall](./media/site-recovery-vmware-to-azure-classic-legacy/push-firewall.png)

4. A conta usada para executar a instalação de envio deve estar no grupo de administradores no computador que você deseja proteger. Essas credenciais são usadas apenas para instalação de envio do serviço de mobilidade e você vai fornecê-los quando você adiciona uma máquina a um grupo de proteção.
5. Se a conta fornecida não é uma conta de domínio, que você precisará desativar o controle de acesso de usuário remoto na máquina local. Para fazer isso adicione a entrada de registro LocalAccountTokenFilterPolicy DWORD com um valor de 1 em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Para adicionar a entrada do registro de um cmd abrir CLI ou powershell e digite **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. 

**Envio instalar automaticamente o serviço de mobilidade em servidores Linux:**

1. Instale as atualizações mais recentes para o servidor de processo, conforme descrito em [etapa 5: instalar atualizações mais recentes](#step-5-install-latest-updates)e certifique-se de que o servidor processo está disponível.
2. Certifique-se de conectividade de rede entre o computador de origem e o servidor de processo e que a máquina de origem é acessível a partir do servidor de processo.  
3. Verifique se que a conta é um usuário de raiz no servidor Linux de origem.
4. Certifique-se de que o arquivo /etc/hosts no servidor Linux de origem contém entradas que mapeiam o nome de host local para endereços IP associados a todas as NICs.
5. Instalar a última openssh, openssh-servidor, pacotes openssl na máquina que deseja proteger.
6. Certifique-se de que SSH está ativado e em execução na porta 22. 
7. Habilite a autenticação de subsistema e a senha SFTP no arquivo sshd_config da seguinte maneira: 

    - a) faça logon como raiz.
    - b) no arquivo de /etc/ssh/sshd_config, localize a linha que começa com **PasswordAuthentication**.
    - c) Tire comentários a linha e altere o valor de "não" para "Sim".

        ![Linux mobilidade](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push.png)

    - d) encontre a linha que começa com subsistema e Tire comentários a linha.
    
        ![Mobilidade de envio do Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push2.png)    

8. Certifique-se de que a variante de Linux de máquina de origem é suportada. 
 
### <a name="install-the-mobility-service-manually"></a>Instalar o serviço de mobilidade manualmente

Os pacotes de software usados para instalar o serviço de mobilidade estão no servidor de processo C:\pushinstallsvc\repository. Fazer logon no servidor de processo e copie o pacote de instalação apropriado para a máquina de origem com base na tabela a seguir:-

| Sistema operacional de origem                               | Pacote de serviço de mobilidade no servidor de processo                                                            |
|---------------------------------------------------    |------------------------------------------------------------------------------------------------------ |
| Windows Server (somente 64 bits)                          | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe`         |
| CentOS 6.4, 6.5, 6.6 (somente 64 bits)                    | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz`     |
| SUSE Linux Enterprise Server 11 SP3 (somente 64 bits)     | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz`|
| Oracle Enterprise Linux 6.4, 6.5 (somente 64 bits)        | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz`       |


**Para instalar o serviço de mobilidade manualmente em um servidor Windows**, faça o seguinte:

1. Copie o pacote do **Microsoft ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe** do caminho do diretório de servidor processo listado na tabela acima na máquina de origem.
2. Instale o serviço de mobilidade executando o executável no computador de origem.
3. Siga as instruções de instalação.
4. Selecione o **serviço de mobilidade** como a função e clique em **Avançar**.
    
    ![Instalar o serviço de mobilidade](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install.png)

5. Deixe o diretório de instalação como o caminho de instalação padrão e clique em **instalar**.
6. Na **Configuração do agente de Host** especifique o endereço IP e porta HTTPS do servidor de configuração.

    - Se você estiver se conectando pela internet especifique o endereço IP virtual público e o ponto de extremidade HTTPS público como a porta.
    - Se você estiver se conectando através de VPN Especifica o endereço IP interno e 443 para a porta. Deixe **Usar HTTPS** verificado.

    ![Instalar o serviço de mobilidade](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install2.png)

7. Especifique a senha do servidor de configuração e clique em **Okey** para registrar o serviço de mobilidade com o servidor de configuração.

**Para executar a partir da linha de comando:**

1. Copiar a senha de CX para o arquivo "C:\connection.passphrase" no servidor e executar este comando. Em nosso exemplo CX i 104.40.75.37 e a porta HTTPS é 62519:

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**Instalar o serviço de mobilidade manualmente em um servidor Linux**:

1. Copie o arquivo tar apropriado com base na tabela acima, do servidor processo na máquina de origem.
2. Abrir um programa de shell e extrair o arquivo tar compactado para um caminho local executando`tar -xvzf Microsoft-ASR_UA_8.2.0.0*`
3. Criar um arquivo de passphrase.txt no diretório local para o qual foi extraído o conteúdo do arquivo tar por meio de *`echo <passphrase> >passphrase.txt`* do shell.
4. Instalar o serviço de mobilidade por meio de *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Especifique o endereço IP e a porta:

    - Se você estiver se conectando ao servidor de configuração pela internet especificar a configuração virtual público endereço IP do servidor e o ponto de extremidade HTTPS público em `<IP address>` e `<port>`.
    - Se você estiver se conectando ao longo de uma conexão de VPN Especifica o endereço IP interno e 443.

**Para executar a partir da linha de comando**:

1. Copiar a senha de CX para o arquivo "passphrase.txt" no servidor e execute este comandos. Em nosso exemplo CX i 104.40.75.37 e a porta HTTPS é 62519:

Para instalar em um servidor de produção:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt
 
Para instalar no servidor de destino:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

>[AZURE.NOTE] Quando você adiciona máquinas a um grupo de proteção que já estejam executando uma versão apropriada do serviço mobilidade, em seguida, a instalação de envio será ignorada.


## <a name="step-9-enable-protection"></a>Etapa 9: Ativar proteção

Para ativar a proteção que você adiciona máquinas virtuais e servidores físicos a um grupo de proteção. Antes de começar, observe que:

- Máquinas virtuais forem descobertas a cada 15 minutos e pode levar até 15 minutos para que elas apareçam na recuperação do Azure Site após a detecção.
- Alterações de ambiente na máquina virtual (como a instalação de ferramentas de VMware) também podem levar até 15 minutos para ser atualizado no recuperação do Site.
- Você pode verificar a última vez descoberta no **Último contato no** campo para o host do servidor/ESXi vCenter na página **Configuração Servers** .
- Se você tiver um grupo de proteção já criou e adicione um host de servidor ou ESXi vCenter depois disso, demora quinze minutos para o portal de recuperação de Site do Azure atualizar e máquinas virtuais seja listado na caixa de diálogo **Adicionar computadores a um grupo de proteção** .
- Se você gostaria de continuar adicionando máquinas ao grupo de proteção sem aguardar a descoberta agendada imediatamente, realce o servidor de configuração (não clique nele) e clique no botão **Atualizar** .
- Quando você adiciona máquinas virtuais ou máquinas físicas a um grupo de proteção, o servidor processo envia automaticamente e instala o serviço de mobilidade no servidor de origem, se o it não esteja instalado.
- Para o mecanismo de envio automático trabalhar certificar-se de que você configurou sua máquinas protegidas conforme descrito na etapa anterior.

Adicione máquinas da seguinte maneira:

1. Clique em **itens protegidos** > **Grupo proteção** > **máquinas** > **Adicionar máquinas**. Como uma prática recomendada é recomendável que os grupos de proteção devem espelhar suas cargas de trabalho para que você adicionar máquinas executando um aplicativo específico ao mesmo grupo.
2. Em **Selecionar máquinas virtuais** se você estiver protegendo servidores físicos, no Assistente para **Adicionar máquinas físicas** forneça o endereço IP e nome amigável. Selecione a família de sistemas operacionais.

    ![Adicionar servidor V-Center](./media/site-recovery-vmware-to-azure-classic-legacy/physical-protect.png)

3. Em **Selecionar máquinas virtuais** se você estiver protegendo máquinas virtuais VMware, selecione um servidor de vCenter que está gerenciando suas máquinas virtuais (ou o host de EXSi em que eles estão sendo executado) e selecione as máquinas.

    ![Adicionar servidor V-Center](./media/site-recovery-vmware-to-azure-classic-legacy/select-vms.png) 
4. Em **Especificar recursos de destino** , selecione os servidores de destino mestre e armazenamento usar para replicação e selecione se as configurações devem ser usadas para todas as cargas de trabalho. Selecione [Conta de armazenamento Premium](../storage/storage-premium-storage.md) durante a configuração da proteção para cargas de trabalho que exigem consistente alto desempenho de IO e baixa latência para hospedar IO intenso cargas de trabalho. Se você quiser usar uma conta de armazenamento Premium para seus discos de carga de trabalho, você precisa usar o destino mestre da série DS. Você não pode usar discos de armazenamento de Premium com destino mestre de série não DS.

    >[AZURE.NOTE] Não há suporte para a movimentação de contas de armazenamento criados usando o [novo portal Azure](../storage/storage-create-storage-account.md) em grupos de recursos.

    ![servidor de vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/machine-resources.png)

5. Em **Especificar contas** , selecione a conta que você deseja usar para instalar o serviço de mobilidade em máquinas protegidas. As credenciais de conta são necessários para instalação automática do serviço de mobilidade. Se você não puder selecionar Verifique uma conta se você configurar um conforme descrito na etapa 2. Observe que essa conta não pode ser acessada por Azure. Para o Windows server a conta deve ter privilégios de administrador no servidor de origem. Para Linux a conta deve ser raiz.

    ![Credenciais de Linux](./media/site-recovery-vmware-to-azure-classic-legacy/mobility-account.png)

6. Clique na marca de seleção para concluir a adição de máquinas ao grupo de proteção e iniciar replicação inicial para cada máquina. Você pode monitorar o status na página de **trabalhos** .

    ![Adicionar servidor V-Center](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs2.png)

7. Além disso, você pode monitorar status de proteção clicando em **Itens protegido** > nome do grupo de proteção > **máquinas virtuais** . Após a conclusão de replicação inicial e as máquinas estão sincronizando dados eles mostrará **protegido** status.

    ![Trabalhos de máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs.png)


### <a name="set-protected-machine-properties"></a>Propriedades de máquina de conjunto protegido

1. Depois que uma máquina tiver um status de **protegido** , você pode configurar suas propriedades de failover. Na proteção detalhes do grupo Selecione o computador em Abra a guia de **Configurar** .
2. Você pode modificar o nome fornecido na máquina no Azure após failover e o tamanho do Azure máquina virtual. Você também pode selecionar a rede Azure ao qual máquina será conectada após failover.

    > [AZURE.NOTE] [Migração de redes](../resource-group-move-resources.md) em grupos de recursos dentro da mesma assinatura ou assinaturas não há suporte para redes usadas para implantar recuperação do Site.

    ![Definir propriedades de máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/vm-props.png)

Observe que:

- O nome da máquina Azure deve estar em conformidade com requisitos Azure.
- Por padrão, máquinas virtuais replicadas no Azure não estão conectadas a uma rede Azure. Se você quiser replicado máquinas virtuais para se comunicar Verifique se definir a mesma rede Azure para eles.
- Se você redimensionar um volume em um servidor físico ou máquina virtual VMware ele entra em um estado crítico. Se você precisar modificar o tamanho, faça o seguinte:

    - a) altere a configuração de tamanho.
    - b) na guia **máquinas virtuais** , selecione a máquina virtual e clique em **Remover**.
    - c) em **Remover Máquina Virtual** selecione a opção **Desabilitar a proteção (uso para recuperação detalhada e volume redimensionar)**. Essa opção desabilita a proteção, mas mantém os pontos de recuperação no Azure.

        ![Definir propriedades de máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/remove-vm.png)

    - d) reabilite a proteção para a máquina virtual. Quando você reativar proteção os dados para o volume redimensionado serão transferidos para o Azure.

    

## <a name="step-10-run-a-failover"></a>Etapa 10: Executar um failover

No momento você só pode executar failovers planejado para máquinas virtuais VMware a protegida e servidores físicos. Observe o seguinte:



- Antes de iniciar um failover, certifique-se de que os servidores de destino de configuração e mestre são eficaz e em execução. Caso contrário, failover falhará.
- Máquinas de origem não estão desligar como parte de um failover não planejado. Executar um failover não planejado interrompe a replicação de dados para os servidores protegidas. Você precisará excluir as máquinas do grupo de proteção e adicioná-los novamente para começar a proteger máquinas novamente após a conclusão do failover não planejado.
- Se você quiser falhar sem perder nenhum dado, certifique-se de que as máquinas virtuais de site primário estão desativadas antes de iniciar o failover.

1. Sobre os **Planos de recuperação** de página e adicionar um plano de recuperação. Especifique os detalhes do plano e selecione **Azure** como o destino.

    ![Configurar o plano de recuperação](./media/site-recovery-vmware-to-azure-classic-legacy/rplan1.png)

2. Em **Selecionar Máquina Virtual** selecione um grupo de proteção e selecione máquinas no grupo para adicionar ao plano de recuperação. [Leia mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.

    ![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic-legacy/rplan2.png)

3. Se necessário você pode personalizar o plano para criar grupos e sequência a ordem em que máquinas na recuperação plano é Falha ao longo. Você também pode adicionar avisos para ações manuais e scripts. Os scripts durante a recuperação no Azure podem ser adicionados usando [Runbooks de automação do Azure](site-recovery-runbook-automation.md).

4. Na página de **Planos de recuperação** selecione o plano e clique em **Failover não planejado**.
5. Em **Confirmar Failover** , verifique se a direção de failover (ao Azure) e selecione o ponto de recuperação para alternar para.
6. Aguarde até que o trabalho de failover concluir e em seguida, verifique se o failover funcionou conforme o esperado e que as máquinas virtuais replicadas inicie com êxito no Azure.




## <a name="step-11-fail-back-failed-over-machines-from-azure"></a>Etapa 11: Falhas volta falhou máquinas do Azure

[Saiba mais](site-recovery-failback-azure-to-vmware-classic-legacy.md) sobre como colocar seu falha sobre máquinas em execução no Azure de volta para seu ambiente local.


## <a name="manage-your-process-servers"></a>Gerenciar seus servidores de processo

O servidor processo envia dados de replicação para o servidor de destino mestre no Azure e descobre novas máquinas virtuais de VMware adicionadas a um servidor de vCenter. Nas seguintes circunstâncias você talvez queira alterar o servidor de processo em sua implantação:

- Se o servidor de processo atual for desligado
- Se seu objetivo de ponto de recuperação (RPO) aumenta a um nível aceitável para sua organização.

Se necessário você pode mover a replicação de algumas ou todas as suas máquinas virtuais VMware a locais e servidores físicos para um servidor de processo diferente. Por exemplo:

- **Falha**— se um servidor de processo falha ou não estiver disponível, você pode mover o replicação de máquina protegida para outro servidor de processo. Metadados da máquina de origem e máquina de réplica serão movidos para o novo servidor de processo e dados são sincronizados novamente. O novo servidor de processo se conectará automaticamente ao servidor vCenter para realizar a detecção automática. Você pode monitorar o estado de servidores de processo no painel recuperação do Site.
- **Balanceamento de carga para ajustar RPO**— para melhor balanceamento você pode selecionar um servidor de processo diferente no portal de recuperação de Site e mover replicação de uma ou mais máquinas-lo para balanceamento de carga manual. Nesse caso metadados das máquinas de origem e réplica selecionados é movido para o novo servidor de processo. O servidor de processo original permanece conectado ao servidor do vCenter. 

### <a name="monitor-the-process-server"></a>Monitore o servidor de processo

Se um servidor de processo está em um estado crítico que um aviso de status será exibido no painel de recuperação do Site. Você pode clicar sobre o status para abrir a guia eventos e, em seguida, fazer drill down trabalhos específicos na guia trabalhos. 

### <a name="modify-the-process-server-used-for-replication"></a>Modificar o servidor de processo usado para replicação

1. Abrir **servidores** > **Servidores de configuração** > servidor de configuração > **Detalhes do servidor**.
2. Clique em **Servidores de processo** > **Servidor do processo de alteração** ao lado do servidor que você deseja modificar.

    ![Alterar o servidor de processo 1](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps1.png)

3. No **Servidor do processo de alteração** > **Servidor do processo de destino** selecione o novo servidor que você deseja usar e, em seguida, selecione as máquinas virtuais que deseja duplicar para o novo servidor. Clique no ícone de informações ao lado do nome do servidor para obter detalhes de espaço livre e memória usada. O espaço médio que serão necessários para replicar cada máquina virtual selecionada para o novo servidor de processo é exibido para ajudar você a tomar decisões de carregar.

    ![Alterar o servidor de processo 2](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps2.png)

4. Clique na marca de seleção para começar a replicar para o novo servidor de processo. Observe que se você remover todas as máquinas virtuais de um servidor de processo que foi fundamental deve já não exibir um aviso crítico no painel de controle.


## <a name="third-party-software-notices-and-information"></a>Avisos de softwares de terceiros e informações

Não traduzir ou localizar

O software e firmware em execução no Microsoft produto ou serviço baseado em ou incorpora material dos projetos listados abaixo (coletivamente, "código de terceiros").  A Microsoft está o autor não original do código de terceiros.  O aviso de direitos autorais original e licença, em que a Microsoft recebido esse código de terceiros, são definidas estabelecidos abaixo.

As informações na seção A se refere componentes de código de terceiros nos projetos listados abaixo. Essas licenças e as informações são fornecidas somente para fins informativos.  Este código de terceiros está sendo relicensed a você pela Microsoft em termos de licenciamento de software da Microsoft para o Microsoft produto ou serviço.  

As informações na seção B for referente a componentes de código de terceiros que estão sendo disponibilizadas a você pela Microsoft sob os termos de licenciamento originais.

O arquivo completo pode ser encontrado no [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). A Microsoft se reserva todos os direitos não expressamente concedidos neste documento, seja por implicação, embargo ou de outra forma.
