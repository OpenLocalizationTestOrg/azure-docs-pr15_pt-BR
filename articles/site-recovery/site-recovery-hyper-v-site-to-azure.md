<properties
    pageTitle="Duplicar máquinas virtuais de Hyper-V (sem VMM) para Azure usando a recuperação de Site Azure com o portal Azure | Microsoft Azure"
    description="Descreve como implantar o Azure recuperação do Site para coordenar replicação, failover e recuperação de VMs Hyper-V no local que não são gerenciados pelo VMM Azure usando o portal do Azure"
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
    ms.date="09/19/2016"
    ms.author="raynew"/>


# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure-using-azure-site-recovery-with-the-azure-portal"></a>Duplicar máquinas virtuais de Hyper-V (sem VMM) para Azure usando a recuperação de Site Azure com o portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-hyper-v-site-to-azure.md)
- [Azure clássico](site-recovery-hyper-v-site-to-azure-classic.md)
- [PowerShell - Gerenciador de recursos](site-recovery-deploy-with-powershell-resource-manager.md)



Bem-vindo ao Site Azure recuperação! Use este artigo se você quiser replicar local Hyper-V virtual máquinas que **não são** gerenciados em nuvens System Center máquinas virtuais Manager (VMM) no Azure. Este artigo descreve como configurar a replicação usando a recuperação de Site Azure no portal do Azure.

> [AZURE.NOTE] Azure tem dois diferentes [modelos de implantação](../resource-manager-deployment-model.md) para criar e trabalhar com recursos: Gerenciador de recursos do Azure e clássico. Azure também tem dois portais – o portal de clássico Azure que suporta o modelo clássico de implantação e o portal do Azure com suporte para ambos os modelos de implantação.

> Recuperação de Site Azure suporta a recuperação e migração de máquinas virtuais Hyper-V Azure. As etapas neste artigo se aplicam idêntico ao configurar a replicação do Azure para recuperação de dados ou para migrar VMs no Azure

Azure recuperação de Site no portal do Azure fornece um número de novos recursos:

- No Azure portal, Backup do Azure e os serviços de recuperação de Site do Azure são combinados em um único compartimento de serviços de recuperação para que você possa configurar e gerenciar continuidade de negócios e recuperação de dados (BCDR) de um único local. Um painel unificado permite monitorar e gerenciar operações em seus sites locais e na nuvem pública do Azure.
- Usuários com assinaturas do Azure provisionados com o programa de provedor de solução da nuvem (CSP) agora podem gerenciar as operações de recuperação de Site no portal do Azure.
- Recuperação de site no portal do Azure pode replicar máquinas para contas de armazenamento do Gerenciador de recursos. Falha a recuperação de Site cria VMs baseadas em Gerenciador de recursos no Azure.
- Recuperação de site continua a oferecer suporte a replicação contas de armazenamento clássico e failover de VMs usando o modelo clássico de implantação.


Depois de ler este artigo postar seus comentários na parte inferior da seção de comentários Disqus. Perguntas técnicas no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Visão geral


As organizações precisam uma estratégia BCDR que determina como dados, cargas de trabalho e aplicativos permanecer em execução e disponível durante o tempo de inatividade planejado e e recuperar a condições de trabalho normal assim que possível. Sua estratégia BCDR será manter dados corporativos seguros e recuperáveis e garantir que as cargas de trabalho estejam continuamente disponíveis quando desastre.

Recuperação de site é um serviço Azure que contribui para sua estratégia BCDR por coordenação replicação de servidores físicos locais e máquinas virtuais para a nuvem (Azure) ou um data center secundário. Quando ocorrem interrupções em seu local principal, você pode falhar ao longo para o local secundário para manter aplicativos e cargas de trabalho disponíveis. Você pode falhar volta para seu local principal quando ele retorna as operações normais. Saiba mais em [o que é recuperação de Site do Azure?](site-recovery-overview.md)

Este artigo fornece todas as informações que você precisa replicar local Hyper-V virtual máquinas que **não são** gerenciados em nuvens System Center máquinas virtuais Manager (VMM) no Azure. Ele inclui uma visão geral arquitetônica, informações e etapas de implantação para configurar os servidores locais, Azure, uma política de replicação e planejamento da capacidade de planejamento. Depois que você configurou a infraestrutura você pode habilitar a replicação em máquinas que você deseja proteger e realizar um failover de teste para validar a configuração. Você pode também migrar suas VMs para Azure primeiro executando um failover planejado e conclua a migração.

## <a name="business-advantages"></a>Vantagens de negócios

- Fornece externo failover (Azure) para cargas de trabalho de negócios e aplicativos executados em máquinas virtuais Hyper-V.
- Fornece um único console de serviços de recuperação de configuração simples e gerenciamento de processos de replicação, failover e recuperação.
- Permite que você facilmente executar failovers de sua infraestrutura de local para o Azure e fail-back (restauração) do Azure para o site local.
- Você pode configurar os planos de recuperação com vários computadores, para que as cargas de trabalho de aplicativo hierárquico falharem juntos.

## <a name="scenario-architecture"></a>Arquitetura de cenário

Estes são os componentes do cenário:

- **Cluster ou host Hyper-V**: servidores de host de locais Hyper-V ou clusters. Os hosts Hyper-V executar VMs que você deseja proteger são coletados em sites de Hyper-V lógicos durante a implantação de recuperação do Site.
- **Provedor de recuperação de Site do Azure e agente de serviços de recuperação**: durante a implantação que você instale o provedor de recuperação de Site do Azure e o agente de serviços de recuperação do Microsoft Azure em servidores de host Hyper-V. O provedor se comunica com recuperação de Site do Azure pela HTTPS 443 replicar coordenação. O agente no servidor host Hyper-V Replica dados para o armazenamento do Azure pela HTTPS 443 por padrão.
- **Azure**: você precisa de uma assinatura do Azure, uma conta de armazenamento do Azure para dados de lojas replicados e uma rede virtual Azure para que o Azure VMs estão conectadas a uma rede após failover.

![Arquitetura do site de Hyper-V](./media/site-recovery-hyper-v-site-to-azure/architecture.png)



## <a name="azure-prerequisites"></a>Pré-requisitos Azure

Veja aqui o que você precisará no Azure implantar esse cenário.

**Pré-requisito** | **Detalhes**
--- | ---
**Conta do Azure**| Você precisará de uma conta [Do Microsoft Azure](http://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação do Site.
**Armazenamento do Azure** | Você precisará de uma conta de armazenamento padrão. Você pode usar uma conta de armazenamento LRS ou GRS. Recomendamos GRS para que dados e apresentam resiliência ocorre uma interrupção regional ou se a região primária não poderão ser recuperada. [Saiba mais](../storage/storage-redundancy.md). A conta deve ser na mesma região como o Cofre de serviços de recuperação.<br/><br/> Armazenamento de Premium não é compatíveis.<br/><br/> Dados replicados são armazenados no armazenamento do Azure e Azure VMs são criadas quando houver falha.<br/><br/> [Leia sobre](../storage/storage-introduction.md) Armazenamento do Azure.
**Rede do Azure** | Você precisará de uma rede virtual Azure que Azure VMs se conectará quando houver falha. A rede virtual Azure deve estar na mesma região como o Cofre de serviços de recuperação.

## <a name="on-premises-prerequisites"></a>Pré-requisitos de local

Veja aqui o que é preciso local.

**Pré-requisito** | **Detalhes**
--- | ---
**Hyper-V** | Um ou mais locais servidores que executam o **Windows Server 2012 R2** com as últimas atualizações e a função Hyper-V habilitada ou **Microsoft Hyper-V Server 2012 R2**.<br/><br/>O servidor Hyper-V deve conter uma ou mais máquinas virtuais.<br/><br/>Servidores Hyper-V devem estar conectados à Internet, diretamente ou por meio de um proxy.<br/><br/>Servidores Hyper-V devem ter correções mencionadas na [KB2961977](https://support.microsoft.com/en-us/kb/2961977 "KB2961977") instalado.
**Provedor e agente** | Durante a implantação do Azure recuperação do Site, você vai instalar o provedor de recuperação de Site do Azure. A instalação do provedor também será instalar o agente de serviços de recuperação do Azure em cada servidor Hyper-V máquinas virtuais que deseja proteger. Todos os servidores de Hyper-V em um cofre de recuperação do Site devem ter as mesmas versões do provedor e agente.<br/><br/>O provedor precisa se conectar a recuperação de Site do Azure pela Internet. Tráfego pode ser enviado diretamente ou por meio de um proxy. Observe que o proxy HTTPS com base não é suportado. O servidor proxy deve permitir acesso a: <br/><br/> *. hypervrecoverymanager.windowsazure.com <br/> <br/> *. accesscontrol.windows.net <br/><br/> *. backup.windowsazure.com <br/> <br/> *. blog.core.windows.net <br/><br/> *Store.Core.Windows.NET <br/><br/> https://www.msftncsi.com/ncsi.txt<br/><br/>Se você tiver regras de firewall baseado em endereço IP no servidor, verifique se as regras permitiu que a comunicação com o Azure. Você precisará permitir que os [Intervalos de IP de data center do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).<br/><br/>Permitir que os intervalos de endereços IP para a região Azure da sua assinatura e Oeste EUA.

## <a name="protected-machine-prerequisites"></a>Pré-requisitos de máquina protegida


**Pré-requisito** | **Detalhes**
--- | ---
**VMs protegidas** | Antes de você Falha ao longo de uma máquina virtual, você precisará certificar-se de que o nome que será atribuído para a máquina virtual do Azure cumpre com os [pré-requisitos Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). Você pode modificar o nome depois habilitado replicação para a máquina virtual.<br/><br/> Capacidade de disco individual em máquinas protegidas não deve ser mais de 1023 GB. Uma máquina virtual pode ter até 16 discos (portanto, até 16 TB).<br/><br/> Compartilhado convidado disco clusters não são suportados.<br/><br/> Se a fonte máquina virtual tiver agrupamento NIC-é convertida em uma única NIC após failover no Azure.<br/><br/>Proteger VMs executando Linux com um endereço IP estático não é compatíveis.

## <a name="prepare-for-deployment"></a>Preparar para implantação

Para se preparar para a implantação, você precisará:

1. [Configurar uma rede Azure](#set-up-an-azure-network) na qual Azure VMs ficarão localizadas quando são criados após failover.
2. [Configurar uma conta de armazenamento do Azure](#set-up-an-azure-storage-account) para dados replicados.
3. [Preparar o Hyper-V hosts](#prepare-the-hyper-v-hosts) para garantir que possam acessar as URLs necessárias.

### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Configure uma rede Azure. Você precisará isso para que as VMs Azure criadas após failover estão conectados a uma rede.

- A rede deve estar na mesma região como aquele no qual você vai implantar o Cofre de serviços de recuperação.
- Dependendo do modelo de recurso que você deseja usar para Falha ao longo do Azure VMs, você configurará a rede Azure no [modo do Gerenciador de recursos](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [modo clássico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Recomendamos que você configurar uma rede antes de começar. Se não estiver você precisará fazer isso durante a implantação de recuperação do Site.

> [AZURE.NOTE] [Migração de redes](../resource-group-move-resources.md) em grupos de recursos dentro da mesma assinatura ou assinaturas não há suporte para redes usadas para implantar recuperação do Site.

### <a name="set-up-an-azure-storage-account"></a>Configurar uma conta de armazenamento do Azure

- Você precisará de uma conta de armazenamento do Azure padrão para armazenar dados duplicados no Azure.
- Dependendo do modelo de recurso que você deseja usar para Falha ao longo do Azure VMs, você irá configurar uma conta no [modo do Gerenciador de recursos](../storage/storage-create-storage-account.md) ou [modo clássico](../storage/storage-create-storage-account-classic-portal.md).
- Recomendamos que você configurar uma conta de armazenamento antes de começar. Se não estiver você precisará fazer isso durante a implantação de recuperação do Site. As contas devem ser na mesma região como o Cofre de serviços de recuperação.

> [AZURE.NOTE] Não há suporte para a [migração de contas de armazenamento](../resource-group-move-resources.md) em grupos de recursos dentro da mesma assinatura ou assinaturas para contas de armazenamento usadas para implantar recuperação do Site.

### <a name="prepare-the-hyper-v-hosts"></a>Preparar os hosts Hyper-V

- Certifique-se de que os hosts Hyper-V cumpram os [pré-requisitos](#on-premises-prerequisites).

### <a name="create-a-recovery-services-vault"></a>Criar um cofre de serviços de recuperação

1. Entrar no [portal do Azure](https://portal.azure.com).
2. Clique em **novo** > **gerenciamento** > **Backup e recuperação de Site (OMS)**. Alternativamente você pode clicar em **Procurar** > compartimentos de**Serviços de recuperação** > **Adicionar**.

    ![Novo cofre](./media/site-recovery-hyper-v-site-to-azure/new-vault3.png)

3. Em **nome** , especifique um nome amigável para identificar o cofre. Se você tiver mais de uma assinatura, selecione um deles.
4. [Criar um novo grupo de recursos](../resource-group-template-deploy-portal.md) ou selecione um existente e especifique uma região Azure. Máquinas serão replicadas para essa região. Para verificar regiões com suporte consulte disponibilidade geográficos em [Detalhes de preços de recuperação do Azure Site](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Se quiser acessar rapidamente o cofre no painel clique em **Fixar no painel de controle** e clique em **criar cofre**.

    ![Novo cofre](./media/site-recovery-hyper-v-site-to-azure/new-vault-settings.png)

O novo cofre aparecerá no **painel** > **todos os recursos**e em principal blade **compartimentos de serviços de recuperação** .

## <a name="getting-started"></a>Guia de Introdução

Recuperação de site fornece uma introdução experiência que ajuda você a implantar assim que possível. Guia de Introdução verifica os pré-requisitos e orienta você a recuperação de Site implantação as etapas na ordem correta.

Na guia de Introdução que você selecione o tipo de máquinas que você deseja duplicar e onde você deseja duplicar para. Configure os servidores locais, contas de armazenamento do Azure e redes. Você cria políticas de replicação e executar planejamento da capacidade. Depois que você configurou sua infraestrutura você habilitar replicação para VMs. Você pode executar failovers para máquinas específicas ou criar planos de recuperação falha em vários computadores.

Começar Introdução escolhendo como você deseja implantar recuperação do Site. As alterações do fluxo Introdução ligeiramente dependendo das suas necessidades de replicação.



## <a name="step-1-choose-your-protection-goals"></a>Etapa 1: Escolher suas metas de proteção

Selecione o que você deseja duplicar e onde você deseja duplicar para.

1. Na lâmina **compartimentos de serviços de recuperação** selecione seu cofre e clique em **configurações**.
2. Nas **configurações** > **Introdução** clique em **Recuperação de Site** > **etapa 1: preparar infraestrutura** > **meta de proteção**.

    ![Escolha metas](./media/site-recovery-hyper-v-site-to-azure/choose-goals.png)

3. Em **meta de proteção** selecione **Azure para**e selecione **Sim, com Hyper-V**. Selecione **Nenhum** para confirmar que você não estiver usando o VMM. Clique em **Okey**.

    ![Escolha metas](./media/site-recovery-hyper-v-site-to-azure/choose-goals2.png)


## <a name="step-2-set-up-the-source-environment"></a>Etapa 2: Configurar o ambiente de origem

Configurar o site de Hyper-V, instalar o provedor de recuperação de Site do Azure e o agente de serviços de recuperação do Azure em hosts Hyper-V e registrar os hosts no cofre.


1. Clique em **etapa 2: preparar infraestrutura** > **fonte**. Para adicionar um novo site de Hyper-V como um contêiner para seus hosts Hyper-V ou clusters, clique em **Site de Hyper-V +**.

    ![Configurar fonte](./media/site-recovery-hyper-v-site-to-azure/set-source1.png)

2. No **site de criar Hyper-V** blade especifique um nome para o site. Clique em **Okey**. Selecione o site que você acabou de criar.

    ![Configurar fonte](./media/site-recovery-hyper-v-site-to-azure/set-source2.png)

3. Clique em **+ Hyper-V Server** para adicionar um servidor para o site.
4. Em **Adicionar servidor** > verificação de**tipo de servidor** que **servidor Hyper-V** é exibido. Certifique-se de que o servidor de Hyper-V que você deseja adicionar cumpre com os [pré-requisitos](#on-premises-prerequisites) e poderá acessar as URLs especificadas.
4. Baixe o arquivo de instalação do provedor de recuperação de Site do Azure. Você executará este arquivo para instalar o provedor e o agente de serviços de recuperação em cada host Hyper-V.
5. Baixe a chave do registro. Você precisará isso quando você executa a configuração. A chave é válida por 5 dias após você gerá-lo.

    ![Configurar fonte](./media/site-recovery-hyper-v-site-to-azure/set-source3.png)

6. Execute o provedor de arquivo de configuração em cada host que é adicionado ao site Hyper-V. Se você estiver instalando em um cluster Hyper-V, execute a instalação em cada nó de cluster. Instalar e registrar cada nós de Cluster Hyper-V garantem que máquinas virtuais protegidas mesmo se eles migram entre nós.

### <a name="install-the-provider-and-agent"></a>Instalar o provedor de serviços e agente

1. Execute o provedor de arquivo de configuração.
2. No **Microsoft Update** você pode optar em atualizações para que as atualizações de provedor são instaladas de acordo com a sua política do Microsoft Update.
3. Em uma **instalação** aceitar ou modificar o local de instalação do provedor padrão e clique em **instalar**.
4. Na página **Configurações do cofre** , clique em **Procurar** para selecionar o arquivo de chave do cofre que você baixou. Especifica a assinatura de recuperação de Site do Azure, o nome do compartimento e o site de Hyper-V ao qual pertence o servidor Hyper-V.

    ![Registro do servidor](./media/site-recovery-hyper-v-site-to-azure/provider3.png)

5. nas **Configurações de Proxy** Especifica como o provedor que será instalado no servidor se conectará a recuperação de Site do Azure pela internet.

- Se quiser que o provedor para conectar-se diretamente selecione **conectar diretamente sem um proxy**.
- Se você quiser conectar-se com o proxy que está configurado no servidor selecione **conectar com configurações de proxy existentes**.
- Se seu proxy existente exige autenticação, ou que você deseja usar um proxy personalizado para selecionar conexão provedor **conectar-se com configurações personalizadas de proxy**.
- Se você usar um proxy personalizado, que você precisará especificar o endereço, porta e credenciais
- Se você estiver usando um proxy, verifique se as URLs descritas nos [pré-requisitos](#on-premises-prerequisites) são permitidas através dele.

    ![Internet](./media/site-recovery-hyper-v-site-to-azure/provider7.PNG)

6. após a conclusão da instalação clique em **registrar** para registrar o servidor no cofre.

![Local de instalação](./media/site-recovery-hyper-v-site-to-azure/provider2.png)

7. após registro conclusão metadados do Hyper-V server é recuperado pela recuperação de Site do Azure e o servidor é exibido na **configurações** > **Infraestrutura de recuperação de Site** > lâmina de**Hosts Hyper-V** .


### <a name="command-line-installation"></a>Instalação de linha de comando

O provedor de recuperação de Site do Azure e o agente também podem ser instalados usando a seguinte linha de comando. Este método pode ser usado para instalar o provedor em um núcleo de servidor para o Windows Server 2012 R2.

1. Baixe a chave de registro e arquivo de instalação do provedor para uma pasta. Por exemplo C:\ASR.
2. A partir de um prompt de comando elevado, execute esses comandos para extrair o instalador do provedor de serviços:

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Execute este comando para instalar os componentes:

            C:\ASR> setupdr.exe /i

4. Execute esses comandos para registrar o servidor no compartimento: CD C:\Program Files\Microsoft Azure Site recuperação Provider\
            Provedor de recuperação de Site Azure C:\Program Files\Microsoft\> DRConfigurator.exe /r /Friendlyname <friendly name of the server> /credenciais<path of the credentials file>

<br/>
Onde:

- **/Credentials** : parâmetro obrigatório que especifica o local em que se encontra o arquivo de chave do registro  
- **/FriendlyName** : parâmetro obrigatório para o nome do servidor host Hyper-V que aparece no portal do Azure recuperação do Site.
- **/proxyAddress** : parâmetro opcional que especifica o endereço do servidor proxy.
- **/proxyPort** : parâmetro opcional que especifica a porta do servidor proxy.
- **/proxyUsername** : parâmetro opcional que especifica o nome de usuário do Proxy (se o proxy exige autenticação).
- **/proxyPassword** : parâmetro opcional que especifica a senha para autenticação com o servidor proxy (se o proxy exige autenticação).


## <a name="step-3-set-up-the-target-environment"></a>Etapa 3: Configurar o ambiente de destino

Especifique a conta de armazenamento do Azure para ser usado para replicação e a rede Azure ao qual o Azure VMs conectará após failover.

1.  Clique em **Preparar infraestrutura** > **destino** e selecione a assinatura do Azure que você deseja usar.
2.  Especifique o modelo de implantação que você deseja usar para VMs após failover.
3.  Recuperação de site verifica se você tem uma ou mais contas de armazenamento do Azure compatível e redes.

    ![Armazenamento](./media/site-recovery-hyper-v-site-to-azure/select-target.png)

4.  Se você ainda não tiver criado uma conta de armazenamento e você deseja criá-la usando o Gerenciador de recursos clique **+ armazenamento** conta para fazer esse embutida. Na lâmina **Criar conta de armazenamento** , especifique um nome de conta, tipo, assinatura e local. A conta deve estar no mesmo local que o Cofre de serviços de recuperação.

    ![Armazenamento](./media/site-recovery-hyper-v-site-to-azure/gs-createstorage.png)

    Se você quiser criar uma conta de armazenamento usando o modelo clássico você fará essa [no portal do Azure](../storage/storage-create-storage-account-classic-portal.md).

5.  Se você ainda não tiver criado uma rede Azure e você deseja criá-la usando o Gerenciador de recursos clique **+ rede** fazer esse embutida. Na lâmina **Criar rede virtual** , especifique um nome de rede, intervalo de endereços, detalhes de sub-rede, assinatura e local. A rede deve estar no mesmo local que o Cofre de serviços de recuperação.

    ![Rede](./media/site-recovery-hyper-v-site-to-azure/gs-createnetwork.png)

    Se você quiser criar uma rede usando o modelo clássico você fará essa [no portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).


## <a name="step-4-set-up-replication-settings"></a>Etapa 4: Configurar as configurações de replicação

1. Para criar uma nova replicação política clique **Preparar infraestrutura** > **Configurações de replicação** > **+ criar e associar**.

    ![Rede](./media/site-recovery-hyper-v-site-to-azure/gs-replication.png)

2. Em **criar e associar diretiva** especifique um nome de política.
3. Em **Copiar frequência** Especifica com que frequência deseja duplicar dados delta após a replicação inicial (a cada 30 segundos, 5 ou 15 minutos).
4. Em **retenção de ponto de recuperação**, especifique em horas quanto tempo a janela de retenção será para cada ponto de recuperação. Máquinas protegidas podem ser recuperadas a qualquer ponto dentro de uma janela.
6. No **aplicativo consistente frequência de instantâneo** especifique frequência (1-12 horas) contendo instantâneos consistentes com o aplicativo de pontos de recuperação será criado. Hyper-V usa dois tipos de instantâneos — um instantâneo padrão que fornece um instantâneo incremental da máquina virtual inteiro e um instantâneo consistente com o aplicativo que usa um instantâneo point-in-time dos dados do aplicativo na máquina virtual. Instantâneos consistentes com o aplicativo usam o serviço de cópia de sombra de Volume (VSS) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo é feito. Observe que, se você habilitar instantâneos consistentes com o aplicativo, ele afetará o desempenho dos aplicativos executados em máquinas virtuais de origem. Certifique-se de que o valor definido é menor que o número de pontos de recuperação adicional que você configura.
3. Na **hora de início de replicação inicial** Especifica quando iniciar a replicação inicial. A replicação ocorre sobre sua largura de banda de internet, então você pode querer agendá-la fora de suas horas ocupadas. Clique em **Okey**.

    ![Política de replicação](./media/site-recovery-hyper-v-site-to-azure/gs-replication2.png)

Quando você cria uma nova política automaticamente associado com o site de Hyper-V. Clique em **Okey**. Você pode associar um site de Hyper-V (e VMs nele) várias políticas de replicação nas **configurações** > **replicação** > nome da política > **Associar Site de Hyper-V**.

## <a name="step-5-capacity-planning"></a>Etapa 5: Planejamento da capacidade

Agora que você tem seu basic infraestrutura configurar você pode pensar sobre o planejamento de capacidade e descobrir se você precisa de recursos adicionais.

Recuperação de site fornece um planejador de capacidade para ajudá-lo a alocar os recursos certos para seu ambiente de origem, os componentes de recuperação do site, rede e armazenamento. Você pode executar o planejador no modo rápido para estimativas com base em um número médio de VMs, discos e armazenamento ou no modo detalhado, na qual você vai entrada figuras no nível de carga de trabalho. Antes de começar você precisará:

- Reunir informações sobre o ambiente de replicação, inclusive VMs, discos por VMs e armazenamento por disco.
- Estime a taxa de alteração (rotatividade) diária que terá para dados replicados. Você pode usar o [Planejador de capacidade para Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) para ajudá-lo a fazer isso.

1.  Clique em **Baixar** para baixar a ferramenta e execute-o. [Leia o artigo](site-recovery-capacity-planner.md) que acompanha a ferramenta.
2.  Quando você terminar selecione **Sim** na **você executou o Planejador de capacidade**?

    ![Planejamento da capacidade](./media/site-recovery-hyper-v-site-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>Considerações de largura de banda de rede

Você pode usar a ferramenta de Planejador de capacidade para calcular a largura de banda que necessária para replicação (replicação inicial e, em seguida, delta). Para controlar a quantidade de uso de largura de banda para replicação, você tem algumas opções:

- **Reduzir largura de banda**: o tráfego Hyper-V Replica para Azure passa por meio de um host Hyper-V específico. Você pode reduzir a largura de banda no servidor host.
- **Ajustar a largura de banda**: você pode influenciar a largura de banda usada para replicação usando algumas das chaves do registro.

#### <a name="throttle-bandwidth"></a>Reduzir largura de banda

1. Abra o snap-in do Microsoft Azure Backup MMC no servidor host Hyper-V. Por padrão, um atalho para o Microsoft Azure Backup está disponível na área de trabalho ou em C:\Program Files\Microsoft Azure recuperação serviços Agent\bin\wabadmin.
2. O snap-in, clique em **Alterar propriedades**.
3. Na guia **Throttling** selecione **Habilitar otimização para operações de backup do uso de largura de banda de internet**e definir os limites de trabalho e não-trabalho de horas. Intervalos válidos são de 512 Kbps a 102 Mbps por segundo.

    ![Reduzir largura de banda](./media/site-recovery-hyper-v-site-to-azure/throttle2.png)

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

## <a name="step-6-enable-replication"></a>Etapa 6: Replicação de habilitar

Agora habilite a replicação da seguinte maneira:

1. Clique em **etapa 2: replicar o aplicativo** > **fonte**. Depois de habilitado replicação pela primeira vez que você vai clique **+ replicar** no cofre para permitir a replicação para máquinas adicionais.

    ![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication.png)

2. Na **origem** lâmina > selecione o site de Hyper-V. Clique em **Okey**.
3. Em **destino** , selecione a assinatura do cofre e o modelo de failover que você deseja usar no Azure (gerenciamento de recurso ou clássico) após failover.
4. Selecione a conta de armazenamento que você deseja usar. Se você quiser usar uma conta de armazenamento diferentes daqueles você ter que você pode [criar uma](#set-up-an-azure-storage-account). Para criar um armazenamento conta usando o modelo do Gerenciador de recursos, clique em **Criar novo**. Se você quiser criar uma conta de armazenamento usando o modelo clássico você fará essa [no portal do Azure](../storage/storage-create-storage-account-classic-portal.md). Clique em **Okey**.
5.  Selecione a rede do Azure e sub-rede ao qual Azure VMs conectará quando eles estão girados backup após failover. Selecione **Configurar agora para máquinas selecionadas** para aplicar a configuração de rede a todas as máquinas que você selecionar para proteção. Selecione **Configurar depois** para selecionar a rede Azure por máquina. Se você quiser usar uma rede diferente daqueles você ter que você pode [criar uma](#set-up-an-azure-network). Para criar uma rede usando o modelo do Gerenciador de recursos, clique em **Criar novo**. Se você quiser criar uma rede usando o modelo clássico você fará essa [no portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selecione uma sub-rede se aplicável. Clique em **Okey**.

    ![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication11.png)

6. Em **máquinas virtuais** > **Selecione máquinas virtuais** clique e selecione cada máquina que você deseja duplicar. Você só poderá selecionar máquinas qual replicação pode ser habilitada. Clique em **Okey**.

    ![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication5.png)

11. Nas **Propriedades** > **Configurar propriedades**, selecione o sistema operacional para as VMs selecionadas e o disco de sistema operacional. Verifique se que a máquina virtual do Azure nome (destino) cumpre com os [requisitos do Azure máquina virtual](site-recovery-best-practices.md#azure-virtual-machine-requirements) e modificá-lo, se necessário. Clique em **Okey**. Você pode definir propriedades adicionais posteriormente.

    ![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication6.png)

12. Nas **configurações de replicação** > **Configurar definições de replicação**, selecione a política de replicação que você deseja aplicar para as VMs protegidas. Clique em **Okey**. Você pode modificar a política de replicação nas **configurações** > **políticas de replicação** > nome da política > **Editar configurações**. Alterações aplicadas serão usadas para máquinas que já estão replicar e novas máquinas.

    ![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication7.png)

Você pode controlar o andamento do trabalho **Habilitar proteção** nas **configurações** > **trabalhos** > **trabalhos de recuperação do Site**. Após o trabalho de **Proteção de finalizar** é executado máquina está pronto para failover.

### <a name="view-and-manage-vm-properties"></a>Exibir e gerenciar propriedades de máquina virtual

Recomendamos que você verifique se as propriedades da máquina de origem.

1. Clique em **configurações** > **Protegido itens** > **Itens duplicados** > e selecione o computador.

    ![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/test-failover1.png)

2. Nas **Propriedades** que você pode exibir informações de replicação e failover para a máquina virtual.

    ![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/test-failover2.png)

3. Em **rede e computação** > a**Calcular propriedades** que você pode especificar o tamanho de nome e destino de máquina virtual do Azure. Modifique o nome em conformidade com requisitos Azure se for necessário. Você também pode exibir e modificar informações sobre a rede de destino, sub-rede e endereço IP que será atribuído para a máquina virtual do Azure. Observe o seguinte:

    - Você pode definir o endereço IP de destino. Se você não fornecer um endereço, a falha máquina usará DHCP. Se você definir um endereço que não está disponível em failover, o failover falhará. O mesmo endereço IP de destino pode ser usado para testar failover se o endereço está disponível na rede de failover de teste.
    - O número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino, da seguinte maneira:

        - Se o número de adaptadores de rede no computador de origem for menor que ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores como fonte.
        - Se o número de adaptadores da máquina virtual de origem exceder o número permitido para o tamanho de destino e em seguida, o tamanho máximo de destino será usado.
        - Por exemplo, se uma máquina de origem tem dois adaptadores de rede e o tamanho da máquina de destino dá suporte a quatro, máquina de destino terá dois adaptadores. Se o computador de origem tem dois adaptadores, mas o tamanho de destino com suporte apenas dá suporte para uma máquina de destino terá apenas um adaptador.     
        - Se a máquina virtual tem vários adaptadores de rede, que ele serão conectado à mesma rede.

    ![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/test-failover4.png)

5.  Em **discos** que você pode ver o sistema operacional e discos de dados sobre a máquina virtual que será duplicada.


## <a name="step-7-test-the-deployment"></a>Etapa 7: Testar a implantação

Para testar a implantação, você pode executar um failover de teste para uma única máquina virtual ou um plano de recuperação que contém uma ou mais máquinas virtuais.


### <a name="prepare-for-test-failover"></a>Preparar para failover de teste

- Para executar um failover de teste, que recomendamos que você crie uma nova rede Azure que tem isolados da sua rede de produção Azure (Isso é comportamento padrão quando você cria uma nova rede no Azure). [Saiba mais](site-recovery-failover.md#run-a-test-failover) sobre como executar failovers de teste.
- Para obter o melhor desempenho quando não conseguir ao longo do Azure, instale o agente do Azure na máquina protegida. Ele torna a inicialização com mais rapidez e ajuda a solucionar problemas. Instale o agente [Linux](https://github.com/Azure/WALinuxAgent) ou [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) .
- Para testar completamente sua implantação, você precisará uma infraestrutura da máquina replicado funcione como esperado. Se você quiser testar o Active Directory e DNS você pode criar uma máquina virtual como controlador de domínio com o DNS e replicar isso no Azure usando a recuperação de Site do Azure. Leia mais em [Considerações de failover de teste para o Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Se você desejar executar um failover não planejado em vez de um failover de teste Observe o seguinte:

    - Se possível deve desligar máquinas primárias antes de executar um failover não planejado. Isso garante que você não tiver a fonte e a réplica máquinas executando ao mesmo tempo.
    - Quando você executa um failover não planejado parar replicação de dados de máquinas primárias para que qualquer delta de dados não possam ser transferido após o início de um failover não planejado. Além disso se você executar um failover não planejado em um plano de recuperação ele será executado até sua conclusão, mesmo se ocorrer um erro.

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para se conectar ao Azure VMs após failover

Se você deseja se conectar VMs Azure usando o RDP após failover, certifique-se de que você faça o seguinte:

**Na máquina local antes do failover**:

- Para obter acesso pela internet habilitar RDP, certifique-se de que as regras TCP e UDP são adicionadas para o **público**e certifique-se de que o RDP é permitida no **Firewall do Windows** -> **permitidos aplicativos e recursos** para todos os perfis.
- Para obter acesso ao longo de uma conexão de-to-site habilitar RDP na máquina e certifique-se de que o RDP é permitida no **Firewall do Windows** -> **permitidos aplicativos e recursos** para redes de **domínio** e **particular** .
- Instale o [agente de máquina virtual do Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) na máquina local.
- Certifique-se de que a política de SAN do sistema operacional está definida como OnlineAll. [Saiba Mais]( https://support.microsoft.com/kb/3031135)
- Desative o serviço IPSec antes de executar o failover.

**Sobre o Azure máquina virtual após failover**:

- Adicione um endereço IP público para a NIC associada com a máquina de virtual do Azure para permitir que o RDP.
- Certifique-se de que você não tem quaisquer diretivas de domínio que impedem a conexão para uma máquina virtual usando um endereço público.
- Tente se conectar. Se você não consegue se conectar, verifique se a máquina virtual está em execução. Para obter mais dicas de solução de problemas leia este [artigo](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Se quiser acessar uma VM Azure executando Linux após failover usando um Secure Shell cliente (ssh), faça o seguinte:

**Na máquina local antes do failover**:

- Certifique-se de que o serviço de Secure Shell na máquina virtual do Azure está definido para iniciar automaticamente na inicialização do sistema.
- Verifique se as regras de firewall permitiu que uma conexão SSH-lo.

**Sobre o Azure máquina virtual após failover**:

- As regras de grupo de segurança de rede na Falha ao longo de máquina virtual e a sub-rede Azure ao qual ele está conectado precisam permitir conexões de entrada para a porta SSH.
- Um ponto de extremidade público deve ser criado para permitir conexões de entrada na porta SSH (porta TCP 22 por padrão).
- Se a máquina virtual for acessada ao longo de uma conexão de VPN (via expressa ou -to-site VPN) o cliente pode ser usado para se conectar diretamente para a máquina virtual via SSH.

### <a name="run-a-test-failover"></a>Executar um failover de teste

Para executar o teste failover faça o seguinte:

1. Falha ao longo de uma única VM nas **configurações** > **Itens duplicados**, clique na máquina virtual > **+ Failover de teste**.

    ![Failover de teste](./media/site-recovery-hyper-v-site-to-azure/run-failover1.png)

2. Falha ao longo de um plano de recuperação, nas **configurações** > de**Planos de recuperação**o plano de atalho > **Failover de teste**. Para criar uma plano de recuperação [siga estas instruções](site-recovery-create-recovery-plans.md).

3. Em **Teste Failover** selecione a rede Azure ao qual serão conectadas Azure VMs após houver falha.

    ![Failover de teste](./media/site-recovery-hyper-v-site-to-azure/run-failover2.png)

4. Clique em **Okey** para iniciar o failover. Você pode controlar o andamento clicando na máquina virtual para abrir suas propriedades ou no trabalho **Failover de teste** nas **configurações** > **trabalhos de recuperação do Site**.
5. Quando o failover atinge a fase de **teste concluída** , faça o seguinte:
    1. Exiba a máquina virtual de réplica no portal do Azure. Verifique se a máquina virtual for iniciado com êxito.
    2. Se estiver configurado para cima até máquinas virtuais de acesso da sua rede local, você pode iniciar uma conexão de área de trabalho remota na máquina virtual.
    3. Clique em **Concluir o teste** para concluí-lo.
    4. Clique em **anotações** para gravar e salvar observações associadas com o failover de teste.
    5. Clique no **failover de teste for concluído**. Limpe o ambiente de teste para automaticamente desligue e excluir na máquina virtual de teste.
    6. Neste estágio qualquer elementos ou VMs criadas automaticamente pelo Site recuperação durante o failover de teste são excluídas. Quaisquer elementos adicionais que você criou para failover de teste não são excluídos.

    > [AZURE.NOTE] Se um failover de teste continua mais de duas semanas que ela seja concluída pela equipe.

6. Após concluir o failover também deve ser capaz de ver a réplica Azure máquina aparecem no portal do Azure > **máquinas virtuais**. Você deverá garantir que a máquina virtual é o tamanho apropriado, que tenha se conectado à rede apropriada, e que ele seja executado.
7. Se você [preparado para conexões após failover](#prepare-to-connect-to-Azure-VMs-after-failover) você deve ser capaz de se conectar para a máquina virtual do Azure.


## <a name="failover"></a>Failover

Depois de replicação inicial for concluída para seus computadores, você pode chamar failovers conforme necessário. Recuperação de site oferece suporte a vários tipos de failovers - failover de teste, failover planejado e failover não planejado.
[Saiba mais](site-recovery-failover.md) sobre os diferentes tipos de failovers e descrições detalhadas de quando e como realizar cada um deles.

> [AZURE.NOTE] Se sua intenção for migrar máquinas virtuais para Azure, é altamente recomendável que você use uma [operação de Failover planejado](site-recovery-failover.md#run-a-planned-failover-primary-to-secondary) para migrar as máquinas virtuais para o Azure. Depois que o aplicativo migrado é validado no Azure usando failover de teste, use as etapas mencionadas em [Migração completa](#Complete-migration-of-your-virtual-machines-to-Azure) para concluir a migração de suas máquinas virtuais. Você não precisa executar uma confirmação ou a exclusão. Migração completa completa a migração, remove a proteção para a máquina virtual e interrompe a cobrança de recuperação de Site do Azure para máquina.


###<a name="run-an-unplanned-failover"></a>Executar um Failover não planejado

Isso deve ser escolhido quando um site primário se tornar inacessível devido a um incidente inesperado, como um ataque de interrupção ou vírus de energia. Este procedimento descreve como executar um 'failover planejado' para um plano de recuperação. Como alternativa, você pode executar o failover para uma única máquina virtual na guia máquinas virtuais. Antes de começar, verifique se todas as máquinas virtuais que você deseja alternar tiver concluído a replicação inicial.

1. Selecione **planos de recuperação > recoveryplan_name**.
2. Na lâmina recuperação plano, clique em **Failover não planejado**.
3. Na página **Failover não planejado** , escolha os locais de origem e destino.
4. Selecione **Desligar máquinas virtuais e sincronizar os dados mais recentes** para especificar que a recuperação de Site deve tentar desligar as máquinas virtuais protegidas e sincronizar os dados para que a versão mais recente dos dados irá falhar.
5. Após o failover, as máquinas virtuais estão em uma confirmação pendente.  Clique em **Confirmar** para confirmar o failover.

[Saiba Mais](site-recovery-failover.md#run-an-unplanned-failover)

## <a name="complete-migration-of-your-virtual-machines-to-azure"></a>Migração completa de suas máquinas virtuais no Azure


>[AZURE.NOTE] As etapas a seguir se aplicam somente se você está migrando máquinas virtuais do Azure

1. Realizar failover planejado como mencionados [aqui](site-recovery-failover.md)
2. No **Configurações > replicados itens**, clique com botão direito na máquina virtual e selecione **Migração completa**

    ![completemigration](./media/site-recovery-hyper-v-site-to-azure/migrate.png)

2. Clique em **Okey** para concluir a migração. Você pode controlar o progresso clicando na máquina virtual para abrir suas propriedades ou usando o trabalho de migração completa no **Configurações > trabalhos de recuperação de Site**.


## <a name="monitor-your-deployment"></a>Monitorar sua implantação

Veja aqui como você pode monitorar as definições de configuração, status e integridade para a sua implantação de recuperação do Site:

1. Clique no nome do cofre para acessar o painel **Essentials** . Neste painel, você pode trabalhos recuperação do Site, status de replicação, planos de recuperação, a integridade do servidor e eventos.  Você pode personalizar Essentials para mostrar os blocos e layouts que são mais úteis para você, incluindo o status dos outros compartimentos de Backup e recuperação do Site.

    ![Essentials](./media/site-recovery-hyper-v-site-to-azure/essentials.png)

2. No bloco **integridade** , você pode monitorar os servidores de site que estão tendo o problema e os eventos gerados pelo Site recuperação nas últimas 24 horas.
3. Você pode gerenciar e monitorar a replicação na **Itens duplicados**, de **Planos de recuperação**e blocos de **Trabalhos de recuperação de Site** . Você pode analisar trabalhos nas **configurações** -> **trabalhos** -> **Trabalhos de recuperação de Site**.
