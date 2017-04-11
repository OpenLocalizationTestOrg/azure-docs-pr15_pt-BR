<properties
    pageTitle="Duplicar máquinas virtuais Hyper-V nuvens do VMM para um site secundário do VMM usando o portal de Azure | Microsoft Azure"
    description="Descreve como implantar o Azure recuperação do Site para coordenar replicação, failover e recuperação de VMs Hyper-V em nuvens VMM para um site secundário do VMM usando o portal do Azure."
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
    ms.date="08/23/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-the-azure-portal"></a>Duplicar máquinas virtuais Hyper-V nuvens do VMM para um site secundário do VMM usando o portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmm-to-vmm.md)
- [Portal clássico](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - Gerenciador de recursos](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Bem-vindo ao Site Azure recuperação! Use este artigo se você quiser replicar máquinas de virtuais Hyper-V local gerenciado no nuvens do System Center Virtual Machine Manager (VMM) para um site secundário. Este artigo descreve como configurar a replicação usando a recuperação de Site Azure no portal do Azure.

> [AZURE.NOTE] Azure tem dois diferentes [modelos de implantação](../resource-manager-deployment-model.md) para criar e trabalhar com recursos: Gerenciador de recursos do Azure e clássico. Azure também tem dois portais – o portal de clássico Azure que suporta o modelo clássico de implantação e o portal do Azure com suporte para ambos os modelos de implantação.


Azure recuperação de Site no portal do Azure oferece vários recursos novos:

- No Azure portal, o Backup do Azure e serviços de recuperação de Site do Azure são combinados em um único compartimento de serviços de recuperação, para que você possa configurar e gerenciar continuidade de negócios e recuperação de dados (BCDR) de um único local. Um painel unificado permite monitorar e gerenciar operações em seus sites locais e na nuvem pública do Azure.
- Usuários com assinaturas do Azure provisionados com o programa de provedor de solução da nuvem (CSP) agora podem gerenciar as operações de recuperação de Site no portal do Azure.


Após ler este artigo, poste os comentários na parte inferior nos comentários Disqus. Perguntas técnicas no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Visão geral

As organizações precisam uma estratégia BCDR que determina como dados, cargas de trabalho e aplicativos permanecer em execução e disponível durante o tempo de inatividade planejado e e recuperar a condições de trabalho normal assim que possível. Sua estratégia BCDR deve manter dados corporativos seguros e recuperáveis e certifique-se de que cargas de trabalho permanecem disponíveis continuamente quando desastre.

Recuperação de site é um serviço Azure que contribui para sua estratégia BCDR por coordenação replicação de servidores físicos locais e máquinas virtuais para a nuvem (Azure) ou um data center secundário. Quando ocorrem interrupções em seu local principal, você Falha ao longo para o local secundário para manter aplicativos e cargas de trabalho disponíveis. Você não volta para seu local principal quando ele retorna as operações normais. Saiba mais em [o que é recuperação de Site do Azure?](site-recovery-overview.md)

Este artigo fornece todas as informações que você precisa replicar VMs Hyper-V em nuvens VMM para um site secundário do VMM local. Ele inclui uma visão geral arquitetônica, informações e etapas de implantação para configurar os servidores locais, configurações de replicação e planejamento da capacidade de planejamento. Depois que você configurou a infraestrutura você pode habilitar replicação em máquinas que você deseja proteger e verificar que o failover funciona.

## <a name="business-advantages"></a>Vantagens de negócios

- Recuperação de site oferece proteção para cargas de trabalho de negócios e aplicativos executados em VMs Hyper-V por replicá-las em um servidor Hyper-V secundário.
- O portal de serviços de recuperação fornece um único local para configurar, gerenciar e monitorar replicação, failover e recuperação.
- Você pode executar failovers facilmente executar sua infraestrutura local principal para o site secundário e failback (restauração) do site secundário para o principal.
- Você pode configurar os planos de recuperação com vários computadores para que cargas de trabalho de aplicativo hierárquico falharem juntos.

## <a name="scenario-architecture"></a>Arquitetura de cenário

Estes são os componentes do cenário:

- **Site principal**: no site principal, há um ou mais servidores de host Hyper-V executar VMs de fonte que você deseja duplicar. Esses servidores de host principal estão localizados em uma nuvem privada VMM.
- **Site secundário**: no site secundário, há um ou mais servidores host Hyper-V executar VMs de destino à qual você replica VMs principais. Esses servidores de host estão localizados em uma nuvem privada VMM. Nuvem pode ser no servidor primário (se tiver apenas um único servidor VMM) ou em um servidor VMM secundário.
- **Provedor**: durante a implantação de recuperação do Site, você instalar o provedor de recuperação de Site do Azure nos servidores VMM e registrar esses servidores em um cofre de serviços de recuperação. O provedor executando no servidor VMM se comunica com recuperação de Site pela HTTPS 443 replicar coordenação. Replicação de dados ocorre entre servidores de host Hyper-V primário e secundário. Dados replicados permanecem em redes e sites de locais e não são enviados ao Azure. Leia mais sobre [privacidade](#privacy-information-for-site-recovery).

![Topologia de E2E](./media/site-recovery-vmm-to-vmm/architecture.png)

### <a name="data-privacy-overview"></a>Visão geral de privacidade de dados

Esta tabela resume como os dados são armazenados neste cenário:
****
Ação | **Detalhes** | **Dados coletados** | **Usar** | **Necessário**
--- | --- | --- | --- | ---
**Registro** | Você pode registrar um servidor VMM em um cofre de serviços de recuperação. Se posteriormente você deseja cancelar o registro de um servidor, você poderá fazer isso excluindo as informações do servidor do portal do Azure. | Depois que um servidor VMM é registrado recuperação de Site coleta, processos e transfere metadados sobre o servidor do VMM e os nomes das nuvens VMM detectados pelo recuperação do Site. | Os dados são usados para identificar e se comunicar com o servidor VMM apropriado e definir configurações para nuvens do VMM apropriados. | Esse recurso é necessário. Se você não quiser enviar essas informações à recuperação do Site que você não deve usar o serviço de recuperação de Site.
**Habilitar a replicação** | O provedor de recuperação de Site do Azure é instalado no servidor VMM e é o canal de comunicação com o serviço de recuperação de Site. O provedor é uma biblioteca de vínculo dinâmico (DLL) hospedada no processo VMM. Depois de instalar o provedor, o recurso de "Recuperação de data center" obtém habilitado no console de administrador do VMM. Novas e existentes VMs podem ativar esse recurso ativar a proteção para uma máquina virtual. | Com essa propriedade definida, o provedor envia o nome e ID da máquina virtual para recuperação do Site.  Replicação está habilitada por Windows Server 2012 ou Windows Server 2012 R2 Hyper-V Replica. Os dados de máquina virtual são replicados de um host Hyper-V para outro (normalmente localizado em um data center "recuperação" diferentes). | Recuperação de site usa os metadados para preencher as informações de máquina virtual no portal do Azure. | Esse recurso é uma parte essencial do serviço e não pode ser desativado. Se você não quiser enviar essas informações, não habilite proteção de recuperação de Site para VMs. Observe que todos os dados enviados pelo provedor de recuperação de Site é enviada por HTTPS.
**Plano de recuperação** | Planos de recuperação ajudarão-lo a criar um plano de coordenação para o Centro de recuperação de dados. Você pode definir a ordem na qual VMs ou um grupo de máquinas virtuais deve ser iniciado no local de recuperação. Você também pode especificar qualquer scripts automatizados para ser executada ou manual qualquer ação a ser executada, no momento da recuperação para cada máquina virtual. Failover geralmente é disparada no nível do plano de recuperação para recuperação coordenado. | Recuperação de site coleta, processa e transmite metadados para o plano de recuperação, incluindo metadados de máquina virtual e de quaisquer scripts de automação e anotações de ação manual. | Os metadados são usados para criar o plano de recuperação no portal do Azure. | Esse recurso é uma parte essencial do serviço e não pode ser desativado. Se você não quiser enviar essas informações à recuperação do Site, não crie planos de recuperação.
**Mapeamento de rede** | Informações de rede de mapas do data center principal para o Centro de recuperação de dados. Quando VMs são recuperadas no site de recuperação, mapeamento de rede ajuda para estabelecer a conectividade de rede. | Recuperação de site coleta, processa e transmite os metadados das redes lógicas para cada site (primário e Data Center). | Os metadados é usado para preencher as configurações de rede para que você possa mapear as informações de rede. | Esse recurso é uma parte essencial do serviço e não pode ser desativado. Se você não quiser enviar essas informações para recuperação do Site, não use o mapeamento de rede.
**Failover (planejado/planejado/teste)** | Failover falha sobre VMs de um centro de dados gerenciadas pelo VMM para outro. A ação de failover é acionada manualmente no portal do Azure. | O provedor do servidor VMM é notificado do evento failover pela recuperação de Site e executa uma ação de failover no host Hyper-V por meio de interfaces VMM. Failover real de uma máquina virtual é de um host Hyper-V para outra e manipulados por Windows Server 2012 ou Windows Server 2012 R2 Hyper-V Replica. Após failover estiver concluída, o provedor do servidor VMM no Centro de recuperação de dados envia informações de sucesso para recuperação do Site. | Recuperação de site usa as informações enviadas para preencher o status das informações de ação de failover no portal do Azure. | Esse recurso é uma parte essencial do serviço e não pode ser desativado. Se você não quiser enviar essas informações para recuperação do Site, não use failover.


## <a name="azure-prerequisites"></a>Pré-requisitos Azure

Veja aqui o que você precisa no Azure implantar esse cenário:

**Pré-requisitos** | **Detalhes**
--- | ---
**Azure**| Você precisa de uma conta do [Microsoft Azure](http://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação do Site.


## <a name="on-premises-prerequisites"></a>Pré-requisitos de local

Veja aqui o que você precisa nos sites primário e secundário local implantar esse cenário:

**Pré-requisitos** | **Detalhes**
--- | ---
**VMM** | Recomendamos que você implantar um servidor VMM no local principal e um servidor VMM no site secundário.<br/><br/> Você também pode [replicar entre nuvens em um único servidor VMM](site-recovery-single-vmm.md). Para fazer isso você precisa de pelo menos duas nuvens configurados no servidor VMM.<br/><br/> Servidores VMM devem estar executando pelo menos System Center 2012 SP1 com as últimas atualizações.<br/><br/> Cada servidor VMM deve ter em um ou mais nuvens configurados e todas as nuvens devem ter o perfil de capacidade de Hyper-V definida. <br/><br/>Nuvens devem conter um ou mais grupos de host VMM.<br/><br/>Saiba mais sobre como configurar as nuvens VMM [Configurando a estrutura de nuvem VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric), e [passo a passo: Criando nuvens privadas com System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).<br/><br/> Servidores VMM precisam de acesso à internet.
**Hyper-V** | Servidores Hyper-V devem estar executando pelo menos o Windows Server 2012 com a função Hyper-V e instalaram as últimas atualizações.<br/><br/> Um servidor Hyper-V deve conter um ou mais VMs.<br/><br/>  Servidores de host Hyper-V devem estar localizados em grupos de host nas nuvens VMM primárias e secundários.<br/><br/> Se você estiver executando o Hyper-V em um cluster no Windows Server 2012 R2 você deve instalar a [atualização 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Se você estiver executando o Hyper-V em um cluster no Windows Server 2012, observe que corretor de cluster não é criada automaticamente se você tiver um cluster de baseado no endereço IP estático. Você precisa configurar o corretor de cluster manualmente. [Leia mais](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).
**Provedor** | Durante a implantação de recuperação do Site, você pode instalar o provedor de recuperação de Site do Azure em servidores VMM. O provedor se comunica com recuperação de Site pela HTTPS 443 para coordenar replicação. Replicação de dados ocorre entre os servidores de Hyper-V primárias e secundários através da LAN ou uma conexão de VPN.<br/><br/> O provedor executando no servidor VMM precisa acessar essas URLs: *. hypervrecoverymanager.windowsazure.com; *. AccessControl.Windows.NET; *. backup.windowsazure.com; *. blob.Core.Windows.NET; *. store.core.windows.net.<br/><br/> Além disso, permitir a comunicação de firewall dos servidores VMM para os [intervalos de IP do data center Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e permitir que o protocolo HTTPS (443).

## <a name="prepare-for-deployment"></a>Preparar para implantação

Para se preparar para a implantação, você precisa:

1. [Preparar o servidor do VMM](#prepare-the-vmm-server) para implantação de recuperação do Site.
2. [Preparar para mapeamento de rede](#prepare-for-network-mapping). Configure redes para que você possa configurar mapeamento de rede.

### <a name="prepare-the-vmm-server"></a>Preparar o servidor VMM

Certifique-se de que o servidor do VMM cumpre com os [pré-requisitos](#on-premises-prerequisites) e pode acessar as URLs listadas.


### <a name="prepare-for-network-mapping"></a>Preparar para mapeamento de rede

Mapas de mapeamento de rede entre redes VMM VM nos servidores VMM primárias e secundários para:

- Coloque ideal réplica VMs em hosts Hyper-V secundários após failover.
- Conecte réplica VMs às redes de máquina virtual apropriados.
- Se você não configurar réplica de mapeamento de rede que VMs não estiver conectadas a qualquer rede após failover.
- Se você quiser configurar rede mapeamento durante a recuperação de Site implantação aqui é o que você precisa:

    - Certifique-se de que VMs no servidor de host de origem Hyper-V estão conectadas a uma rede VMM VM. Rede deve ser vinculada a uma rede lógica que está associada com a nuvem.
    - Verifique se a nuvem secundária que você usa para recuperação possui uma rede de máquina virtual correspondente configurado. Essa rede de máquina virtual deve ser vinculado a uma rede lógica associado com a nuvem secundária.

- [Saiba mais](site-recovery-network-mapping.md) sobre como funciona o mapeamento de rede.

## <a name="prepare-for-deployment-with-a-single-vmm-server"></a>Preparar para implantação com um único servidor VMM

Se você tiver apenas um único servidor VMM você pode replicar VMs em hosts Hyper-V na nuvem VMM [Azure](site-recovery-vmm-to-azure.md) ou uma nuvem VMM secundária. Recomendamos que a primeira opção replicar entre nuvens não perfeita, mas se você precisar fazer isso aqui é o que você precisa fazer:

1. **Configurar o VMM em uma VM Hyper-V**. Quando fazemos isso sugerimos que você colocar a instância do SQL Server usada pelo VMM na mesma VM. Isso economiza tempo como máquina virtual somente uma deve ser criado. Se você quiser usar uma instância remota do SQL Server e uma interrupção ocorre, você precisa recuperar essa instância antes que você pode recuperar VMM.
2. **Verifique se o servidor do VMM tem pelo menos duas nuvens configurados**. Uma nuvem conterá VMs deseja duplicar e outra nuvem servirá como local secundário. Na nuvem que contém as VMs que você deseja proteger deve ser compatíveis com [os pré-requisitos](#on-premises-prerequisites).
3. Configure a recuperação de Site, conforme descrito neste artigo. Criar e registrar o servidor VMM no cofre, definir uma política de replicação e habilitar a replicação. Você deve especificar que a replicação inicial ocorrerá através da rede.
4. Ao configurar o mapeamento de rede mapeie a rede de máquina virtual para a nuvem primária para a rede de máquina virtual para a nuvem secundária.
5. No console Gerenciador Hyper-V, habilitar Hyper-V Replica no host Hyper-V que contém a VM VMM e habilitar a replicação na máquina virtual. Verifique se que você não adicionar na máquina virtual do VMM para nuvens protegidos por recuperação de Site, para garantir que as configurações de Hyper-V Replica não são substituídas por recuperação do Site.
6. Se você criar planos de recuperação para failover você usar o mesmo servidor VMM para origem e destino.
7. Você falha e recuperar da seguinte maneira:

    - Manualmente falhe sobre a VM VMM para o site secundário usando o Gerenciador de Hyper-V com um failover planejado.
    - Falha ao longo de VMs.
    - Após o principal VMM VM foi recuperado, logon no portal do Azure -> Serviços de recuperação cofre e execute um failover não planejado das VMs do local secundário para o site principal.
    - Após o failover planejado for concluído, todos os recursos podem ser acessados do local principal novamente.


### <a name="create-a-recovery-services-vault"></a>Criar um cofre de serviços de recuperação

1. Entrar no [portal do Azure](https://portal.azure.com).
2. Clique em **novo** > **gerenciamento** > **Serviços de recuperação**. Alternativamente você pode clicar em **Procurar** > compartimentos de**Serviços de recuperação** > **Adicionar**.

    ![Novo cofre](./media/site-recovery-vmm-to-vmm/new-vault3.png)

3. Em **nome** , especifique um nome amigável para identificar o cofre. Se você tiver mais de uma assinatura, selecione um deles.
4. [Criar um novo grupo de recursos](../resource-group-template-deploy-portal.md) ou selecione um existente e especifique uma região Azure. Máquinas serão replicadas para essa região. Para verificar regiões com suporte consulte disponibilidade geográficos em [Detalhes de preços de recuperação do Azure Site](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Se quiser acessar rapidamente o cofre no painel, clique em **Fixar no painel** > **criar cofre**.

    ![Novo cofre](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

O novo cofre aparecerá no **painel** > **todos os recursos**e em principal blade **compartimentos de serviços de recuperação** .




## <a name="getting-started"></a>Guia de Introdução

Recuperação de site fornece uma introdução experiência que ajuda você a implantar assim que possível. Guia de Introdução verifica os pré-requisitos e orienta você a recuperação de Site implantação as etapas na ordem correta.

Na guia de Introdução que você selecione o tipo de máquinas que você deseja duplicar e onde você deseja duplicar para. Você configurar os servidores locais, cria políticas de replicação e executar planejamento da capacidade. Depois que você configurou sua infraestrutura você habilitar replicação para VMs. Você pode executar failovers para máquinas específicas ou criar planos de recuperação falha em vários computadores.

Começar Introdução escolhendo como você deseja implantar recuperação do Site. As alterações do fluxo Introdução ligeiramente dependendo das suas necessidades de replicação.

## <a name="step-1-choose-your-protection-goal"></a>Etapa 1: Escolher sua meta de proteção

Selecione o que você deseja duplicar e onde você deseja duplicar para.

1. Na lâmina **compartimentos de serviços de recuperação** selecione seu cofre e clique em **configurações**.
2. Nas **configurações** > **Introdução** clique em **Recuperação de Site** > **etapa 1: preparar infraestrutura** > **meta de proteção**.
3. Em **meta de proteção** selecione **site de recuperação**e selecione **Sim, com Hyper-V**.
4. Selecione **Sim** para indicar que você está usando o VMM para gerenciar os hosts Hyper-V e selecione **Sim** se você tiver um servidor VMM secundário. Se você estiver implantando replicação entre nuvens em um único servidor VMM você clique em **não**. Clique em **Okey**.

    ![Escolha metas](./media/site-recovery-vmm-to-vmm/choose-goals.png)


## <a name="step-2-set-up-the-source-environment"></a>Etapa 2: Configurar o ambiente de origem

Instalar o provedor de recuperação de Site do Azure em servidores VMM e registrar servidores no cofre.

1. Clique em **etapa 2: preparar infraestrutura** > **fonte**.

    ![Configurar fonte](./media/site-recovery-vmm-to-vmm/goals-source.png)

2. Na **fonte de preparar** clique **+ VMM** para adicionar um servidor VMM.

    ![Configurar fonte](./media/site-recovery-vmm-to-vmm/set-source1.png)

2. Na verificação **Adicionar servidor** blade **server System Center VMM** exibida no **tipo de servidor** e que o servidor do VMM atende aos [requisitos de URL e os pré-requisitos](#on-premises-prerequisites).
4. Baixe o arquivo de instalação do provedor de recuperação de Site do Azure.
5. Baixe a chave do registro. Isso é necessário quando você executa a configuração. A chave é válida por 5 dias após você gerá-lo.

    ![Configurar fonte](./media/site-recovery-vmm-to-vmm/set-source3.png)

6. Instale o provedor de recuperação de Site do Azure do servidor VMM.

> [AZURE.NOTE] Não é necessário instalar nada explicitamente em servidores de host Hyper-V.


### <a name="set-up-the-azure-site-recovery-provider"></a>Configurar o provedor de recuperação de Site do Azure

1. Execute o provedor de arquivo de configuração em cada servidor VMM. Se VMM é implantado em um cluster e você estiver instalando o provedor para a primeira vez instalá-lo em um nó ativo e concluir a instalação para registrar o servidor VMM no cofre. Em seguida, instale o provedor nos outros nós. Nós de cluster devem executar a mesma versão do provedor.
2. Configuração executa algumas verificações prerequirement e as solicitações de permissão para interromper o serviço do VMM. O serviço de VMM será reiniciado automaticamente quando a instalação terminar. Se você estiver instalando em um cluster VMM que você será solicitado a interromper a função de Cluster.

2.  No **Microsoft Update** você pode optar em atualizações para que as atualizações de provedor são instaladas de acordo com a sua política do Microsoft Update.
3. Em uma **instalação** aceitar ou modificar o local de instalação do provedor padrão e clique em **instalar**.

    ![Local de instalação](./media/site-recovery-vmm-to-vmm/provider-location.png)

3. Após a conclusão da instalação clique em **registrar** para registrar o servidor no cofre.

    ![Local de instalação](./media/site-recovery-vmm-to-vmm/provider-register.png)

9. Em **nome do cofre**, verifique o nome do cofre em que o servidor será registrado. Clique em *Avançar*.

    ![Registro do servidor](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)

7. Na **Conexão de Internet** , especifique como o provedor executando no servidor VMM se conecta à Internet. Selecione **conectar-se com as configurações de proxy existentes** para usar as configurações de conexão de Internet padrão configuradas no servidor.

    ![Configurações da Internet](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

    - Se você quiser usar um proxy personalizado, que você deve configurá-lo antes de instalar o provedor. Quando você definir configurações de proxy personalizado um teste será executado para verificar a conexão de proxy.
    - Se você usar um proxy personalizado, ou seu proxy de padrão exige autenticação que você precise inserir os detalhes de proxy, incluindo o endereço de proxy e porta.
    - Seguintes urls devem ser acessíveis a partir do servidor do VMM e os hosts Hyper-v
        - *. hypervrecoverymanager.windowsazure.com
        - *. accesscontrol.windows.net
        - *. backup.windowsazure.com
        - *. blob.core.windows.net
        - *. store.core.windows.net
    - Permitir que os endereços IP descritos em [Intervalos de IP de data center do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e HTTPS protocolo (443). Você teria intervalos de IP de lista branca do Azure região que você planeja usar e do Oeste EUA.
    - Se você usar um proxy personalizado que uma conta do VMM RunAs (DRAProxyAccount) será criada automaticamente usando as credenciais de proxy especificado. Configure o servidor proxy para que esta conta pode autenticar com êxito. As configurações de conta VMM RunAs podem ser modificadas no console do VMM. Para fazer isso, abra o espaço de trabalho de **configurações** , expanda **segurança**, clique **Contas executar como**e, em seguida, modificar a senha para DRAProxyAccount. Você precisará reiniciar o serviço VMM para que essa configuração tenha efeito.


8. Na **Chave do registro**, selecione a chave que você baixou de recuperação de Site do Azure e copiado para o servidor VMM.


10.  A configuração de criptografia é usada somente quando você estiver replicar VMs Hyper-V em nuvens VMM no Azure. Se você estiver replicar para um site secundário não é usado.

11.  Em **nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Em uma configuração de cluster, especifique o nome da função VMM cluster.
12.  No **metadados de nuvem de sincronizar** , selecione se deseja sincronizar metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só precisa acontecer uma vez em cada servidor. Se você não quiser sincronizar todas as nuvens, você pode deixar essa configuração desmarcadas e sincronizar cada nuvem individualmente nas propriedades da nuvem no console do VMM.

13.  Clique em **Avançar** para concluir o processo. Depois do registro, metadados do servidor VMM é recuperado por recuperação de Site do Azure. O servidor é exibido na guia **Servidores de VMM** na página **Servers** no cofre.

    ![Servidor](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)

11. Depois que o servidor está disponível no console de recuperação do Site, na **fonte** > **Preparar fonte** selecione do servidor VMM e a nuvem na qual o host Hyper-V está localizado. Clique em **Okey**.

#### <a name="command-line-installation"></a>Instalação de linha de comando

O provedor de recuperação de Site do Azure pode ser instalado na linha de comando. Este método pode ser usado para instalar o provedor no servidor núcleo do Windows Server 2012 R2.

1. Baixe a chave de registro e arquivo de instalação do provedor para uma pasta. Por exemplo C:\ASR.
2. Interrompa o serviço de Gerenciador de máquina Virtual do System Center.
3. A partir de um prompt de comando elevado, execute esses comandos para extrair o instalador do provedor de serviços:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Execute este comando para instalar o provedor:

        C:\ASR> setupdr.exe /i

5. Execute esses comandos para registrar o servidor no cofre:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

Onde os parâmetros são:

 - **/Credentials**: parâmetro obrigatório que especifica o local em que se encontra o arquivo de chave do registro  
 - **/FriendlyName**: parâmetro obrigatório para o nome do servidor host Hyper-V que aparece no portal do Azure recuperação do Site.
 - **/EncryptionEnabled**: parâmetro opcional que você use somente quando replicação de VMM para Azure.
 - **/proxyAddress**: parâmetro opcional que especifica o endereço do servidor proxy.
 - **/proxyPort**: parâmetro opcional que especifica a porta do servidor proxy.
 - **/proxyUsername**: parâmetro opcional que especifica o nome de usuário do Proxy (se o proxy exige autenticação).
 - **/proxyPassword**: parâmetro opcional que especifica a senha para autenticação com o servidor proxy (se o proxy exige autenticação).  

## <a name="step-3-set-up-the-target-environment"></a>Etapa 3: Configurar o ambiente de destino

Selecione o servidor do VMM de destino e a nuvem.

1. Clique em **Preparar infraestrutura** > **destino** e selecione o servidor do VMM de destino que você deseja usar.
2.  Nuvens no servidor que são sincronizados com a recuperação do Site serão exibidas. Selecione a nuvem de destino.

    ![Destino](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## <a name="step-4-set-up-replication-settings"></a>Etapa 4: Configurar as configurações de replicação

1. Para criar uma nova replicação política clique **Preparar infraestrutura** > **Configurações de replicação** > **+ criar e associar**.

    ![Rede](./media/site-recovery-vmm-to-vmm/gs-replication.png)

2. Em **criar e associar diretiva** especifique um nome de política. O tipo de origem e destino deve ser **Hyper-V**.
3. Na **versão de host Hyper-V** selecione qual sistema operacional está em execução no host.

    > [AZURE.NOTE] Nuvem VMM pode conter hosts Hyper-V executando versões diferentes (com suporte) do Windows Server, mas uma política de replicação é aplicados hosts executando o mesmo sistema operacional. Se você tiver hosts executando mais de um sistema operacional versão Crie políticas de replicação separada.

4. Em **tipo de autenticação** e **porta de autenticação** Especifica como o tráfego é autenticado entre os servidores primário e recuperação de host Hyper-V. Selecione o **certificado** a menos que tenha um ambiente de Kerberos de trabalho. Recuperação de Site Azure configurará automaticamente certificados para autenticação HTTPS. Você não precisa fazer nada manualmente. Por padrão, porta 8083 e 8084 (para certificados) será aberta no Firewall do Windows nos servidores de host Hyper-V. Se você selecionar **Kerberos**, um tíquete Kerberos será usado para autenticação comum de servidores de host. Observe que esta configuração só é relevante para os servidores de host Hyper-V em execução no Windows Server 2012 R2.
3. Em **Copiar frequência** Especifica com que frequência deseja duplicar dados delta após a replicação inicial (a cada 30 segundos, 5 ou 15 minutos).
4. Em **retenção de ponto de recuperação**, especifique em horas quanto tempo a janela de retenção será para cada ponto de recuperação. Máquinas protegidas podem ser recuperadas a qualquer ponto dentro de uma janela.
6. No **aplicativo consistente frequência de instantâneo** especifique frequência (1-12 horas) contendo instantâneos consistentes com o aplicativo de pontos de recuperação será criado. Hyper-V usa dois tipos de instantâneos — um instantâneo padrão que fornece um instantâneo incremental da máquina virtual inteiro e um instantâneo consistente com o aplicativo que usa um instantâneo point-in-time dos dados do aplicativo na máquina virtual. Instantâneos consistentes com o aplicativo usam o serviço de cópia de sombra de Volume (VSS) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo é feito. Observe que, se você habilitar instantâneos consistentes com o aplicativo, ele afetará o desempenho dos aplicativos executados em máquinas virtuais de origem. Certifique-se de que o valor definido é menor que o número de pontos de recuperação adicional que você configura.
7. Em **compactação de transferência de dados** , especifique se os dados replicados que são transferidos devem ser compactados.
8. Selecione **Excluir réplica máquina virtual** para especificar que a máquina virtual de réplica deve ser excluída se você desativar a proteção para a fonte de máquina virtual. Se você ativar essa configuração, quando você desativar a proteção para a fonte de máquina virtual, ele será removido do console de recuperação do Site, configurações de recuperação de Site para o VMM são removidas do console do VMM e a réplica é excluída.
3. No **método de replicação inicial** se você estiver replicar pela rede especifica se iniciar a replicação inicial ou agendá-la. Para salvar largura de banda de rede, convém agendá-la fora de suas horas ocupadas. Clique em **Okey**.

    ![Política de replicação](./media/site-recovery-vmm-to-vmm/gs-replication2.png)

6. Quando você cria uma nova política automaticamente associado com a nuvem VMM. Na **política de replicação** clique **Okey**. Você pode associar adicionais nuvens do VMM (e VMs nelas) a esta política de replicação nas **configurações** > **replicação** > nome da política > **Associar VMM nuvem**.

    ![Política de replicação](./media/site-recovery-vmm-to-vmm/policy-associate.png)

### <a name="prepare-for-offline-initial-replication"></a>Preparar para replicação inicial offline

Você pode fazer replicação offline para a cópia de dados iniciais. Você pode preparar isso da seguinte maneira:

- No servidor de origem, você deverá especificar um local de caminho do qual será realizada a exportação de dados. Atribua controle total para permissões de compartilhamento e NTFS ao serviço VMM no caminho de exportação. No servidor de destino, você deverá especificar um local de caminho da qual ocorrerá a importação de dados. Atribua as mesmas permissões neste caminho de importação.
- Se o caminho de importação ou exportação for compartilhado, atribua as associações de grupo administrador, usuário avançado, o operador de impressão ou operador de servidor para a conta de serviço VMM no computador remoto no qual compartilhado está localizado.
- Se você estiver usando qualquer contas executar como adicionar hosts, sobre os caminhos de importação e exportação, atribuir leitura e permissões de gravação para as contas executar como no VMM.
- Os compartilhamentos de importação e exportação não devem estar localizados em qualquer computador usado como um servidor de host Hyper-V, pois a configuração de autoretorno não é suportada pelo Hyper-V.
- No Active Directory, em cada Hyper-V server host que contém máquinas virtuais que você deseja proteger, habilitar e configurar delegação restrita confiar em computadores remotos no qual os caminhos de importação e exportação estão localizados, da seguinte maneira:
    1. No controlador de domínio, abra o **Active Directory Users e computadores**.
    2. Na árvore do console clique **nome_do_domínio** > **computadores**.
    3. Clique com botão direito no nome do servidor de host Hyper-V > **Propriedades**.
    4. Na guia **delegação** , clique em **Confiar no computador para delegação apenas aos serviços especificados**.
    5. Clique em **usar qualquer protocolo de autenticação**.
    6. Clique em **Adicionar** > **usuários e computadores**.
    7. Digite o nome do computador que hospeda o caminho de exportação > **Okey**. Na lista de serviços disponíveis, mantenha pressionada a tecla CTRL e clique em **cifs** > **Okey**. Repita para o nome do computador que hospeda o caminho de importação. Repita conforme necessário para os servidores de host Hyper-V adicionais.


### <a name="configure-network-mapping"></a>Configurar mapeamento de rede

Configure mapeamento de rede entre redes de origem e destino.

- [Leitura](#prepare-for-network-mapping) para uma rápida visão geral de quais mapeamento de rede faz. Em adição [ler](site-recovery-network-mapping.md) para uma explicação mais detalhada.
- Verificar se máquinas virtuais em servidores VMM estão conectadas a uma rede de máquina virtual.

Configure mapeamento da seguinte maneira:

1. **Configurações de** > **Infraestrutura de recuperação de Site** > **Mapeamento de rede** > **mapeamentos de rede** clique **+ mapeamento de rede**.

    ![Mapeamento de rede](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. Na guia **Adicionar mapeamento de rede** , selecione a fonte e VMM servidores de destino. As redes de máquina virtual associadas com os servidores VMM são recuperadas.
3. Em **rede de origem**, selecione a rede que você deseja usar na lista de redes de máquina virtual associado ao servidor VMM principal.
6. Na **rede de destino** , selecione a rede que você deseja usar no servidor VMM secundário. Clique em **Okey**.

    ![Mapeamento de rede](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

Veja aqui o que acontece quando começa de mapeamento de rede:

- Qualquer máquinas virtuais réplica existentes que correspondem à rede de máquina virtual do fonte serão conectadas à rede de máquina virtual de destino.
- Novas máquinas virtuais que estão conectadas à rede de máquina virtual do fonte serão conectadas à rede de destino mapeado após a replicação.
- Se você modificar um mapeamento com uma nova rede existente, máquinas virtuais de réplica serão conectadas usando as novas configurações.
- Se a rede de destino tem várias sub-redes e uma dessas sub-redes tem o mesmo nome de sub-rede na qual a máquina virtual de origem está localizada, em seguida, na máquina virtual de réplica será conectada para aquela sub-rede de destino após failover. Se não houver nenhuma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.

### <a name="configure-storage-mapping"></a>Configurar mapeamento de armazenamento
Por padrão quando você replica uma máquina virtual em um servidor de host de Hyper-V de origem para um servidor de host de Hyper-V de destino, dados replicados são armazenados no local padrão que é indicado para o host do Hyper-V de destino no Gerenciador de Hyper-V. Para ter mais controle sobre onde os dados replicados estão armazenados, você pode configurar mapeamento de armazenamento<br/><br/> Para configurar mapeamento de armazenamento, você precisa configurar classificações de armazenamento na origem e destino servidores VMM antes de começar a implantação. Atualmente não há suporte para mapeamento de armazenamento por meio do novo portal Azure. No entanto, ele pode ser habilitado por meio do Powershell. [Saiba mais](site-recovery-vmm-to-vmm-powershell-resource-manager.md#step-6-configure-storage-mapping).

## <a name="step-5-capacity-planning"></a>Etapa 5: Planejamento da capacidade

Agora que você tem seu basic infraestrutura configurar você pode pensar sobre o planejamento de capacidade e descobrir se você precisa de recursos adicionais.

Recuperação de site fornece um planejador de capacidade baseada em Excel para ajudá-lo a alocar os recursos certos para seu ambiente de origem, os componentes de recuperação do site, rede e armazenamento. Você pode executar o planejador no modo rápido para estimativas com base em um número médio de VMs, discos e armazenamento ou no modo detalhado, na qual você vai entrada figuras no nível de carga de trabalho. Antes de começar você precisará:

- Reunir informações sobre o ambiente de replicação, inclusive VMs, discos por VMs e armazenamento por disco.
- Estime a taxa de alteração (rotatividade) diária que terá para dados de delta duplicado. Você pode usar o [Planejador de capacidade para Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) para ajudá-lo a fazer isso.

1.  Clique em **Baixar** para baixar a ferramenta e execute-o. [Leia o artigo](site-recovery-capacity-planner.md) que acompanha a ferramenta.
2.  Quando você terminar selecione **Sim** na **você executou o Planejador de capacidade**?

    ![Planejamento da capacidade](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="control-bandwidth"></a>Largura de banda de controle

Depois de você coletadas informações de replicação de delta em tempo real usando a capacidade Planejador Hyper-V Replica, a ferramenta de Planejador de capacidade baseada em Excel ajudará a calcular a largura de banda que necessária para replicação (inicial e delta). Para controlar a quantidade de largura de banda usada para replicação, você pode configurar a política de NetQos usando política de grupo ou o Windows PowerShell. Há algumas maneiras que você pode fazer isso:

**PowerShell** | **Detalhes**
--- | ---
**Novo - NetQosPolicy "QoS à sub-rede de destino"** | Otimizar o tráfego de um host Hyper-V executando o Windows Server 2012 R2 para uma sub-rede secundária. Use se suas sub-redes primárias e secundários são diferentes.
**Novo - NetQosPolicy "QoS a porta de destino"** | Otimizar o tráfego de um host Hyper-V executando o Windows Server 2012 R2 para uma porta de destino.
**Novo - NetQosPolicy "Reduza o tráfego do VMM"** | Otimizar o tráfego de vmms.exe. Isso irá reduzir o tráfego de Hyper-V e migração ao vivo. Você pode fazer a correspondência de protocolos IP e portas para refinar.

Você pode usar as configurações de espessura de largura de banda ou limitar o tráfego por bits por secundário. Se você estiver usando um cluster que você precisa fazer isso em todos os nó de cluster. Para obter mais informações, consulte:


- Blog de Thomas Maurer [a otimização de](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/) tráfego de réplica do Hyper-V
- Informações adicionais sobre o [cmdlet New-NetQosPolicy](https://technet.microsoft.com/library/hh967468.aspx).


## <a name="step-6-enable-replication"></a>Etapa 6: Replicação de habilitar

Agora habilite a replicação da seguinte maneira:

1. Clique em **etapa 2: replicar o aplicativo** > **fonte**. Depois de habilitado replicação pela primeira vez, você clique **+ replicar** no cofre para permitir a replicação para máquinas adicionais.

    ![Habilitar a replicação](./media/site-recovery-vmm-to-vmm/enable-replication1.png)

2. Na **origem** lâmina > Selecionar servidor VMM e a nuvem no qual os hosts Hyper-V deseja duplicar estão localizados. Clique em **Okey**.

    ![Habilitar a replicação](./media/site-recovery-vmm-to-vmm/enable-replication2.png)

3. Na lâmina **destino** , verifique se o servidor secundário do VMM e a nuvem.
4. Em **máquinas virtuais** selecione VMs que você deseja proteger na lista.

    ![Ativar a proteção de máquina virtual](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

Você pode controlar o andamento da ação **Habilitar proteção** em Configurações > **trabalhos** > **trabalhos de recuperação do Site**. Depois que o trabalho de **Proteção de finalizar** é executado na máquina virtual está pronta para failover.


>[AZURE.NOTE] Você também pode habilitar proteção para máquinas virtuais no console do VMM. Clique em **Habilitar proteção** na barra de ferramentas as propriedades de máquina virtual > guia **Recuperação de Site do Azure** .

Depois de habilitado replicação você pode exibir as propriedades para a máquina virtual nas **configurações** > **Itens duplicados** > nome da máquina virtual. No painel **Essentials** , você pode ver informações sobre a política de replicação para a máquina virtual e seu status. Para obter mais detalhes, clique em **Propriedades** .

### <a name="onboard-existing-virtual-machines"></a>Integrado máquinas virtuais existentes

Se você tiver máquinas virtuais existentes no VMM que são duplicar com Hyper-V Replica poderá integrado-los para proteção de recuperação de Site do Azure da seguinte forma:

1. Certifique-se de que o servidor de Hyper-V que hospeda a máquina virtual existente está localizado na nuvem primária e que o servidor de Hyper-V que hospeda a máquina virtual de réplica estiver localizado na nuvem secundária.
2. Certifique-se de que uma política de replicação está configurada para a nuvem VMM principal.
2. Habilite a replicação da máquina virtual primária. Azure recuperação de Site e VMM garantirá que o mesmo host de réplica e máquina virtual for detectado, e a recuperação de Site Azure será reutilizar e restabelecer replicação usando as configurações especificadas.


## <a name="step-7-test-your-deployment"></a>Etapa 7: Testar sua implantação

Para testar sua implantação, você pode executar um failover de teste para uma única máquina virtual ou criar um plano de recuperação que contém uma ou mais máquinas virtuais.

### <a name="prepare-for-failover"></a>Preparar para failover

- Para testar completamente sua implantação é necessário uma infraestrutura da máquina replicado funcione como esperado. Se você quiser testar o Active Directory e DNS você pode criar uma máquina virtual como controlador de domínio com o DNS e replicar isso no Azure usando a recuperação de Site do Azure. Leia mais em [Considerações de failover de teste para o Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- As instruções neste artigo descrevem como executar um failover de teste com nenhuma rede. Esta opção irá testar que a máquina virtual Falha ao longo, mas não testar as configurações de rede para a máquina virtual. [Saiba mais](site-recovery-failover.md#run-a-test-failover) sobre outras opções.
- Se você desejar executar um failover não planejado em vez de um failover de teste Observe o seguinte:

    - Se possível deve desligar máquinas primárias antes de executar um failover não planejado. Isso garante que você não tiver a fonte e a réplica máquinas executando ao mesmo tempo.
    - Quando você executa um failover não planejado parar replicação de dados de máquinas primárias para que qualquer delta de dados não possam ser transferido após o início de um failover não planejado. Além disso se você executar um failover não planejado em um plano de recuperação ele será executado até sua conclusão, mesmo se ocorrer um erro.




### <a name="run-a-test-failover"></a>Executar um failover de teste

1. Falha ao longo de uma única VM nas **configurações** > **Itens duplicados**, clique na máquina virtual > **+ Failover de teste**.

    ![Failover de teste](./media/site-recovery-vmm-to-vmm/test-failover.png)

2. Falha ao longo de um plano de recuperação, nas **configurações** > de**Planos de recuperação**o plano de atalho > **Failover de teste**. Para criar uma plano de recuperação [siga estas instruções](site-recovery-create-recovery-plans.md).
2. Em **Failover de teste**, selecione **Nenhum**. Com essa opção, você testar que a máquina virtual falha sobre conforme o esperado, mas a máquina virtual replicada não estiver conectada a qualquer rede.

    ![Failover de teste](./media/site-recovery-vmm-to-vmm/test-failover1.png)

3. Clique em **Okey** para iniciar o failover. Você pode controlar o andamento clicando na máquina virtual para abrir suas propriedades ou no trabalho **Failover de teste** nas **configurações** > **trabalhos** > **trabalhos de recuperação do Site**.
4. Quando o trabalho de failover atinge a fase de **teste concluída** , faça o seguinte:

    -  Exiba a réplica máquina virtual na nuvem VMM secundária.
    -  Clique em **Concluir o teste** para concluir o failover de teste.
    -  Clique em **anotações** para gravar e salvar observações associadas com o failover de teste.

5. Na máquina virtual de teste será criada no mesmo host como o host em que a máquina virtual de réplica exista. Ele é adicionado à nuvem mesma na qual máquina virtual réplica está localizada.
6. Depois de verificar que comecem VMs com êxito clique **o failover de teste for concluído**. Neste estágio quaisquer elementos criados automaticamente pelo Site recuperação durante o failover de teste são excluídos.  

    > [AZURE.NOTE] Se um failover de teste continua mais de duas semanas que ela seja concluída pela equipe.



### <a name="update-dns-with-the-replica-vm-ip-address"></a>Atualizar o DNS com a endereço IP de VM réplica

Após failover réplica máquina virtual não pode ter o mesmo endereço IP na máquina virtual primária.

- Máquinas virtuais atualizará o servidor DNS que elas estiverem usando após iniciarem.
- Você também pode atualizar manualmente DNS da seguinte maneira:

#### <a name="retrieve-the-ip-address"></a>Recuperar o endereço IP

Execute esse script de amostra para recuperar o endereço IP.

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### <a name="update-dns"></a>Atualizar o DNS

Execute esse script de amostra para atualizar o DNS, especificando o endereço IP que você recuperou usando o script de exemplo anterior.

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## <a name="next-steps"></a>Próximas etapas

Após sua implantação está configurado e funcionando, [Saiba mais](site-recovery-failover.md) sobre os diferentes tipos de failovers.
