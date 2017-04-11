<properties
    pageTitle="Duplicar máquinas virtuais Hyper-V nuvens do VMM para Azure usando a recuperação de Site com o portal Azure | Microsoft Azure"
    description="Descreve como implantar o Azure recuperação do Site para coordenar replicação, failover e recuperação de VMs Hyper-V em nuvens VMM no Azure usando o portal do Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/16/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-azure-site-recovery-with-the-azure-portal--microsoft-azure"></a>Duplicar máquinas virtuais Hyper-V nuvens do VMM para Azure usando a recuperação de Site Azure com o portal Azure | Microsoft Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmm-to-azure.md)
- [Azure clássico](site-recovery-vmm-to-azure-classic.md)
- [Gerenciador de recursos do PowerShell](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [PowerShell clássico](site-recovery-deploy-with-powershell.md)

Bem-vindo ao Site Azure recuperação! Use este artigo se você quiser replicar máquinas de virtuais Hyper-V local gerenciado no nuvens do System Center Virtual Machine Manager (VMM) no Azure usando a recuperação de Site Azure no portal do Azure.

> [AZURE.NOTE]Azure tem dois diferentes [modelos de implantação](../resource-manager-deployment-model
> ) para criar e trabalhar com recursos: Gerenciador de recursos do Azure e clássico. Azure também tem dois portais – o portal de clássico Azure que suporta o modelo clássico de implantação e o portal do Azure com suporte para ambos os modelos de implantação.


Azure recuperação de Site no portal do Azure oferece vários recursos novos:

- No portal do Azure os serviços de recuperação de Site do Azure e Backup de Azure são combinados em um único compartimento de serviços de recuperação, para que você possa configurar e gerenciar continuidade de negócios e recuperação de dados (BCDR) de um único local. Um painel unificado permite monitorar e gerenciar operações em seus sites locais e na nuvem pública do Azure.
- Usuários com assinaturas do Azure provisionados com o programa de provedor de solução da nuvem (CSP) agora podem gerenciar as operações de recuperação de Site no portal do Azure.
- Recuperação de site no portal do Azure pode replicar máquinas para contas de armazenamento do Gerenciador de recursos do Azure. Falha a recuperação de Site cria VMs baseadas em Gerenciador de recursos no Azure.
- Recuperação de site continua a oferecer suporte à replicação a contas de armazenamento clássico. Falha a recuperação de Site cria VMs usando o modelo clássico.


Após ler este artigo, poste os comentários na parte inferior nos comentários Disqus. Perguntas técnicas no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Visão geral

As organizações precisam uma estratégia BCDR que determina como dados, cargas de trabalho e aplicativos permanecer em execução e disponível durante o tempo de inatividade planejado e e recuperar a condições de trabalho normal assim que possível. Sua estratégia BCDR deve manter dados corporativos seguros e recuperáveis e certifique-se de que cargas de trabalho permanecem disponíveis continuamente quando desastre.

Recuperação de site é um serviço Azure que contribui para sua estratégia BCDR por orquestração replicação de servidores físicos locais e máquinas virtuais para a nuvem (Azure), ou para um data center secundário. Quando ocorrem interrupções em seu local principal, você Falha ao longo para o local secundário para manter aplicativos e cargas de trabalho disponíveis. Você não volta para seu local principal quando ele retorna as operações normais. Saiba mais em [o que é recuperação de Site do Azure?](site-recovery-overview.md)

Este artigo fornece todas as informações que você precisa replicar VMs Hyper-V em nuvens VMM no Azure local. Ele inclui uma visão geral arquitetônica, informações e etapas de implantação para a configuração do Azure, os servidores locais, as configurações de replicação e planejamento da capacidade de planejamento. Depois que você configurou a infraestrutura você pode habilitar replicação em máquinas que você deseja proteger e verificar que o failover funciona.


## <a name="business-advantages"></a>Vantagens de negócios

- Recuperação de site oferece proteção externa para cargas de trabalho de negócios e aplicativos executados em VMs Hyper-V.
- O portal de serviços de recuperação fornece um único local para configurar, gerenciar e monitorar replicação, failover e recuperação.
- Você pode facilmente executar failovers sua infraestrutura local do Azure e failback (restauração) do Azure para os servidores de host Hyper-V em seu site local.
- Você pode configurar os planos de recuperação com vários computadores para que cargas de trabalho de aplicativo hierárquico falharem juntos.


## <a name="scenario-architecture"></a>Arquitetura de cenário


Estes são os componentes do cenário:

- **Servidor VMM**: um servidor VMM local com um ou mais nuvens.
- **Cluster ou host Hyper-V**: servidores de host Hyper-V ou clusters gerenciado no nuvens do VMM.
- **Provedor de recuperação de Site do Azure e agente de serviços de recuperação**: durante a implantação instalar o provedor de recuperação de Site do Azure no servidor VMM e o agente de serviços de recuperação do Microsoft Azure em servidores de host Hyper-V. O provedor do servidor VMM se comunica com recuperação de Site pela HTTPS 443 replicar coordenação. O agente no servidor host Hyper-V Replica dados para o armazenamento do Azure pela HTTPS 443 por padrão.
- **Azure**: você precisa de uma assinatura do Azure, uma conta de armazenamento do Azure para dados de lojas replicados e uma rede virtual Azure para que o Azure VMs estão conectadas a uma rede após failover.

![E2A topologia](./media/site-recovery-vmm-to-azure/architecture.png)


## <a name="azure-prerequisites"></a>Pré-requisitos Azure

Veja aqui o que você precisa no Azure implantar esse cenário.

**Pré-requisito** | **Detalhes**
--- | ---
**Conta do Azure**| Você precisa de uma conta do [Microsoft Azure](http://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação do Site.
**Armazenamento do Azure** | Você precisa de uma conta de armazenamento do Azure padrão para armazenar dados duplicados. Você pode usar uma conta de armazenamento LRS ou GRS. Recomendamos GRS para que dados e apresentam resiliência ocorre uma interrupção regional ou se a região primária não poderão ser recuperada. [Saiba mais](../storage/storage-redundancy.md). A conta deve ser na mesma região como o Cofre de serviços de recuperação.<br/><br/>Armazenamento de Premium não é compatíveis.<br/><br/> Dados replicados são armazenados no armazenamento do Azure e Azure VMs são criadas quando houver falha. <br/><br/> [Leia sobre](../storage/storage-introduction.md) Armazenamento do Azure.
**Rede do Azure** | Você precisa de uma rede virtual Azure que Azure VMs conectar ao quando houver falha. A rede deve estar na mesma região como o Cofre de serviços de recuperação.

## <a name="on-premises-prerequisites"></a>Pré-requisitos de local

Veja aqui o que é necessário no local

**Pré-requisito** | **Detalhes**
--- | ---
**VMM**| Um ou mais servidores VMM em execução no System Center 2012 R2. Cada servidor VMM deve ter um ou mais nuvens configurados. Uma nuvem deve conter:<br/><br/> Um ou mais VMM hospede grupos.<br/><br/> Um ou mais servidores de host Hyper-V ou clusters em cada grupo de host.<br/><br/>[Saiba mais](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html) sobre como configurar nuvens do VMM.
**Hyper-V** | Servidores de host Hyper-V devem estar executando pelo menos o **Windows Server 2012 R2** com a função Hyper-V ou **Microsoft Hyper-V Server 2012 R2** e tem instalado as atualizações mais recentes.<br/><br/> Um servidor Hyper-V deve conter um ou mais VMs.<br/><br/> Um servidor de host Hyper-V ou cluster que inclui VMs deseja duplicar deve ser gerenciado em uma nuvem VMM.<br/><br/>Servidores Hyper-V devem estar conectados à Internet, diretamente ou por meio de um proxy.<br/><br/>Servidores Hyper-V devem ter correções mencionadas no artigo [2961977](https://support.microsoft.com/kb/2961977) instalado.<br/><br/>Servidores de host Hyper-V precisam de acesso à internet para replicação de dados do Azure.
**Provedor e agente** | Durante a implantação do Azure recuperação do Site, você vai instalar o provedor de recuperação de Site do Azure no servidor VMM e o agente de serviços de recuperação em hosts Hyper-V. O provedor de serviços e o agente precisam se conectar ao Azure pela internet diretamente ou através de um proxy. Observe que um proxy baseada em HTTPS não é compatíveis. O servidor de proxy no servidor do VMM e hosts Hyper-V deve permitir acesso a: <br/><br/> *. hypervrecoverymanager.windowsazure.com <br/> <br/> *. accesscontrol.windows.net <br/><br/> *. backup.windowsazure.com <br/> <br/> *. blob.core.windows.net <br/><br/> *. store.core.windows.net<br/><br/>Se você tiver regras de firewall baseado em endereço IP do servidor VMM, verifique se as regras permitiu que a comunicação com o Azure. É preciso permitir que os [Intervalos de IP de data center do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).<br/><br/>Permitir que os intervalos de endereços IP para a região Azure da sua assinatura e Oeste EUA.<br/><br/>Além disso. o servidor de proxy do servidor VMM precisa acessar https://www.msftncsi.com/ncsi.txt


## <a name="protected-machine-prerequisites"></a>Pré-requisitos de máquina protegida


**Pré-requisito** | **Detalhes**
--- | ---
**VMs protegidas** | Antes de você Falha ao longo de uma máquina virtual, certifique-se de que o nome que foi atribuído para a máquina virtual do Azure cumpre com os [pré-requisitos Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). Você pode modificar o nome depois habilitado replicação para a máquina virtual. <br/><br/> Capacidade de disco individual em máquinas protegidas não deve ser mais de 1023 GB. Uma máquina virtual pode ter até 16 discos (portanto, até 16 TB).<br/><br/> Compartilhado convidado disco clusters não são suportados.<br/><br/> Unified Extensible Firmware Interface (UEFI) / Extensible Firmware Interface(EFI) inicialização não é compatíveis.<br/><br/> Se a fonte máquina virtual tiver agrupamento NIC-é convertida em uma única NIC após failover no Azure.<br/><br/>Proteger VMs executando Linux com um endereço IP estático não é compatíveis.

## <a name="prepare-for-deployment"></a>Preparar para implantação

Para se preparar para a implantação, você precisa:

1. [Configurar uma rede Azure](#set-up-an-azure-network) na qual Azure VMs ficarão localizadas após failover.
2. [Configurar uma conta de armazenamento do Azure](#set-up-an-azure-storage-account) para dados replicados.
4. [Preparar o servidor do VMM](#prepare-the-vmm-server) para implantação de recuperação do Site.
5. [Preparar para mapeamento de rede](#prepare-for-network-mapping). Configure redes para que você possa configurar mapeamento de rede durante a implantação de recuperação do Site.

### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Você precisa de uma rede Azure para que as VMs Azure criadas após failover se conectará a ele.

- A rede deve estar na mesma região como aquele no qual você implantar o Cofre de serviços de recuperação.
- Dependendo do modelo de recurso que você deseja usar para Falha ao longo do Azure VMs, você configurará a rede Azure no [modo do Gerenciador de recursos](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [modo clássico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Recomendamos que você configurar uma rede antes de começar. Se não estiver, você precisa fazer isso durante a implantação de recuperação do Site.

> [AZURE.NOTE] [Migração de redes](../resource-group-move-resources.md) em grupos de recursos dentro da mesma assinatura ou assinaturas não há suporte para redes usadas para implantar recuperação do Site.


### <a name="set-up-an-azure-storage-account"></a>Configurar uma conta de armazenamento do Azure

- Você precisará de uma conta de armazenamento do Azure padrão para armazenar dados duplicados no Azure. A conta deve ser na mesma região como o Cofre de serviços de recuperação.
- Dependendo do modelo de recurso que você deseja usar para Falha ao longo do Azure VMs, você configurar uma conta no [modo do Gerenciador de recursos](../storage/storage-create-storage-account.md) ou [modo clássico](../storage/storage-create-storage-account-classic-portal.md).
- Recomendamos que você configurar uma conta antes de começar. Se não estiver, você precisa fazer isso durante a implantação de recuperação do Site.

> [AZURE.NOTE] Não há suporte para a [migração de contas de armazenamento](../resource-group-move-resources.md) em grupos de recursos dentro da mesma assinatura ou assinaturas para contas de armazenamento usadas para implantar recuperação do Site.

### <a name="prepare-the-vmm-server"></a>Preparar o servidor VMM

- Certifique-se de que o servidor do VMM é compatível com os [pré-requisitos](#on-premises-prerequisites).
- Durante a implantação de recuperação do Site, você pode especificar que todas as nuvens em um servidor VMM devem estar disponíveis no portal do Azure. Se você só quiser nuvens específico apareça no portal do, você pode habilitar essa configuração na nuvem no console de administração do VMM.


### <a name="prepare-for-network-mapping"></a>Preparar para mapeamento de rede

Você precisa configurar mapeamento de rede durante a implantação de recuperação do Site. Rede mapas de mapeamento entre VMM VM redes de origem e destino redes Azure para habilitar o seguinte:

- Máquinas que falham sobre na mesma rede podem se conectar uns aos outros, mesmo se eles não estiverem falhou ao longo da mesma maneira ou no mesmo plano de recuperação.
- Se um gateway de rede está configurado no destino rede Azure, Azure máquinas virtuais pode conectar a máquinas virtuais de local.
- Para configurar a rede mapeamento aqui é que você precisa para preparar:

    - Certifique-se de que VMs no servidor de host de origem Hyper-V estão conectadas a uma rede VMM VM. Rede deve ser vinculada a uma rede lógica que está associada com a nuvem.
    - Uma rede Azure conforme descrito [acima](#set-up-an-azure-network)

- [Saiba mais](site-recovery-network-mapping.md) sobre como funciona o mapeamento de rede.


## <a name="create-a-recovery-services-vault"></a>Criar um cofre de serviços de recuperação

1. Entrar no [portal do Azure](https://portal.azure.com).
2. Clique em **novo** > **gerenciamento** > **Serviços de recuperação**. Alternativamente você pode clicar em **Procurar** > compartimentos de**Serviços de recuperação** > **Adicionar**.

    ![Novo cofre](./media/site-recovery-vmm-to-azure/new-vault3.png)

3. Em **nome**, especifique um nome amigável para identificar o cofre. Se você tiver mais de uma assinatura, selecione um deles.
4. [Criar um grupo de recursos](../resource-group-template-deploy-portal.md), ou selecione um existente. Especifique uma região do Azure. Máquinas serão replicadas para essa região. Para verificar regiões com suporte consulte disponibilidade geográficos em [Detalhes de preços de recuperação do Azure Site](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Se quiser acessar rapidamente o cofre no painel, clique em **Fixar no painel** > **criar cofre**.

    ![Novo cofre](./media/site-recovery-vmm-to-azure/new-vault-settings.png)

O novo cofre aparece no **painel** > **todos os recursos**e em principal blade **compartimentos de serviços de recuperação** .

## <a name="getting-started"></a>Guia de Introdução

Recuperação de site fornece uma introdução experiência que ajuda você a implantar assim que possível. Guia de Introdução verifica os pré-requisitos e orienta você a recuperação de Site implantação as etapas na ordem correta.

Na guia de Introdução que você selecione o tipo de máquinas que você deseja duplicar e onde você deseja duplicar para. Configure os servidores locais, contas de armazenamento do Azure e redes. Você cria políticas de replicação e executar planejamento da capacidade. Depois que você configurou sua infraestrutura você habilitar replicação para VMs. Você pode executar failovers para máquinas específicas ou criar planos de recuperação falha em vários computadores.

Começar Introdução escolhendo como você deseja implantar recuperação do Site. As alterações do fluxo Introdução ligeiramente dependendo das suas necessidades de replicação.



## <a name="step-1-choose-your-protection-goals"></a>Etapa 1: Escolher suas metas de proteção

Selecione o que você deseja duplicar e onde você deseja duplicar para.

1. Na lâmina **compartimentos de serviços de recuperação** , selecione seu cofre e clique em **configurações**.
2. Na **Introdução** , clique em **Recuperação de Site** > **etapa 1: preparar infraestrutura** > **meta de proteção**.

    ![Escolha metas](./media/site-recovery-vmm-to-azure/choose-goals.png)

3. Em **meta de proteção** selecione **Azure para**e selecione **Sim, com Hyper-V**. Selecione **Sim** para confirmar que você estiver usando o VMM para gerenciar hosts Hyper-V e o site de recuperação. Clique em **Okey**.

    ![Escolha metas](./media/site-recovery-vmm-to-azure/choose-goals2.png)



## <a name="step-2-set-up-the-source-environment"></a>Etapa 2: Configurar o ambiente de origem

Instalar o provedor de recuperação de Site do Azure no servidor VMM e registrar o servidor no cofre. Instale o agente de serviços de recuperação do Azure em hosts Hyper-V.

1. Clique em **etapa 2: preparar infraestrutura** > **fonte**.

    ![Configurar fonte](./media/site-recovery-vmm-to-azure/set-source1.png)

2. Na **fonte de preparar** clique **+ VMM** para adicionar um servidor VMM.

    ![Configurar fonte](./media/site-recovery-vmm-to-azure/set-source2.png)

3. Na verificação **Adicionar servidor** blade **server System Center VMM** exibida no **tipo de servidor** e que o servidor do VMM atende aos [requisitos de URL e os pré-requisitos](#on-premises-prerequisites).
4. Baixe o arquivo de instalação do provedor de recuperação de Site do Azure.
5. Baixe a chave do registro. Isso é necessário quando você executa a configuração. A chave é válida por cinco dias após você gerá-lo.

    ![Configurar fonte](./media/site-recovery-vmm-to-azure/set-source3.png)

6. Instale o provedor de recuperação de Site do Azure do servidor VMM.


### <a name="set-up-the-azure-site-recovery-provider"></a>Configurar o provedor de recuperação de Site do Azure

1.  Execute o provedor de arquivo de configuração.
2. No **Microsoft Update** você pode optar em atualizações para que as atualizações de provedor são instaladas de acordo com a sua política do Microsoft Update.
3. Na **instalação**, aceite ou modificar o local de instalação do provedor padrão e clique em **instalar**.

    ![Local de instalação](./media/site-recovery-vmm-to-azure/provider2.png)

4. Quando termina de instalação clique em **registrar** para registrar o servidor VMM no cofre.
5. Na página **Configurações do cofre** , clique em **Procurar** para selecionar o arquivo de chave do cofre. Especifique a assinatura de recuperação de Site do Azure e o nome do cofre.

    ![Registro do servidor](./media/site-recovery-vmm-to-azure/provider10.PNG)

6. Na **Conexão de Internet**, especifique como o provedor executando no servidor VMM se conectará a recuperação do Site pela internet.

    - Se quiser que o provedor para conectar-se diretamente selecione **conectar diretamente à recuperação de Site do Azure sem um proxy**.
    - Se seu proxy existente exige autenticação, ou que você deseja usar uma seleção de proxy personalizado **conectar a recuperação de Site do Azure usando um servidor proxy**.
    - Se você usar um proxy personalizado, especifique o endereço, porta e credenciais.
    - Se você estiver usando um proxy, que você deve ter permissão já as URLs descritas em [pré-requisitos](#on-premises-prerequisites).
    - Se você usar um proxy personalizado que uma conta do VMM RunAs (DRAProxyAccount) será criada automaticamente usando as credenciais de proxy especificado. Configure o servidor proxy para que esta conta pode autenticar com êxito. As configurações de conta VMM RunAs podem ser modificadas no console do VMM. Em **configurações**, expanda **segurança** > **Contas executar como**e, em seguida, modificar a senha para DRAProxyAccount. Você precisará reiniciar o serviço VMM para que essa configuração tenha efeito.

    ![Internet](./media/site-recovery-vmm-to-azure/provider13.PNG)

7. Aceite ou modifique o local de um certificado SSL que é gerado automaticamente para criptografia de dados. Este certificado é usado se você habilitar a criptografia de dados para uma nuvem protegida por Azure no portal do Azure recuperação do Site. Manter este certificado de segurança. Quando você executa um failover no Azure você precisará para descriptografar, se a criptografia de dados estiver habilitada.


8. Em **nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Em uma configuração de cluster, especifique o nome da função VMM cluster.
9. Habilite **sincronização nuvem metadados** se você quiser sincronizar metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só precisa acontecer uma vez em cada servidor. Se você não quiser sincronizar todas as nuvens, você pode deixar essa configuração desmarcadas e sincronizar cada nuvem individualmente nas propriedades da nuvem no console do VMM. Clique em **registrar** para concluir o processo.

    ![Registro do servidor](./media/site-recovery-vmm-to-azure/provider16.PNG)

10. Registro é iniciado. Após a conclusão do registro, o servidor é exibido na **configurações** > **servidores** blade no cofre.


#### <a name="command-line-installation-for-the-azure-site-recovery-provider"></a>Instalação de linha de comando para o provedor de recuperação de Site do Azure

O provedor de recuperação de Site do Azure pode ser instalado na linha de comando. Este método pode ser usado para instalar o provedor no servidor núcleo do Windows Server 2012 R2.

1. Baixe a chave de registro e arquivo de instalação do provedor para uma pasta. Por exemplo, C:\ASR.
2. A partir de um prompt de comando elevado, execute esses comandos para extrair o instalador do provedor de serviços:

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Execute este comando para instalar os componentes:

            C:\ASR> setupdr.exe /i

4. Execute esses comandos para registrar o servidor no cofre:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Onde:

- **/Credentials**: parâmetro obrigatório que especifica onde se encontra o arquivo de chave do registro.  
- **/FriendlyName**: parâmetro obrigatório para o nome do servidor host Hyper-V que aparece no portal do Azure recuperação do Site.
- - **/EncryptionEnabled**: parâmetro opcional quando você estiver replicar VMs Hyper-V no VMM nuvens no Azure. Especifique se deseja criptografar máquinas virtuais no Azure (na criptografia rest). Certifique-se de que o nome do arquivo tem uma extensão **. pfx** . Criptografia está desativada por padrão.
- **/proxyAddress**: parâmetro opcional que especifica o endereço do servidor proxy.
- **/proxyPort**: parâmetro opcional que especifica a porta do servidor proxy.
- **/proxyUsername**: parâmetro opcional que especifica o nome de usuário do proxy (se o proxy exige autenticação).
- **/proxyPassword**: parâmetro opcional que especifica a senha para autenticar com o servidor proxy (se o proxy exigir autenticação).


### <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Instalar o agente de serviços de recuperação do Azure em hosts Hyper-V

1. Depois que você configurou o provedor, você precisará baixar o arquivo de instalação para o agente de serviços de recuperação do Azure. Execute a instalação em cada servidor Hyper-V na nuvem VMM.

    ![Sites de Hyper-V](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)

2. Na página **De verificação de pré-requisitos** , clique em **Avançar**. Quaisquer pré-requisitos ausentes serão instalados automaticamente.

    ![Agente de serviços de recuperação de pré-requisitos](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)

3. Na página **Configurações de instalação** , aceite ou modifique o local de instalação e o local do cache. Você pode configurar o cache em uma unidade que tenha pelo menos 5 GB de armazenamento disponível, mas recomendamos uma unidade de cache com 600 GB ou mais de espaço livre. Clique em **instalar**.
4. Após a instalação estiver concluída, clique em **Fechar** para concluir.

    ![Registrar MARTE agente](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

#### <a name="command-line-installation-for-azure-site-recovery-services-agent"></a>Instalação de linha de comando do agente de serviços de recuperação de Site do Azure

Você pode instalar o agente de serviços de recuperação do Microsoft Azure da linha de comando usando o seguinte comando:

     marsagentinstaller.exe /q /nu

#### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Configurar o acesso à internet proxy para recuperação de Site de hosts Hyper-V

O agente de serviços de recuperação em execução no hosts Hyper-V precisa de acesso à internet para Azure para replicação de máquina virtual. Se você estiver acessando a internet através de um proxy, configure-o da seguinte maneira:

1. Abra o snap-in do Microsoft Azure Backup MMC no host Hyper-V. Por padrão, um atalho para o Microsoft Azure Backup está disponível na área de trabalho ou em C:\Program Files\Microsoft Azure recuperação serviços Agent\bin\wabadmin.
2. O snap-in, clique em **Alterar propriedades**.
3. Na guia **Configuração de Proxy** , especifique as informações do servidor proxy.

    ![Registrar MARTE agente](./media/site-recovery-vmm-to-azure/mars-proxy.png)

4. Certifique-se de que o agente pode acessar as URLs descritas nos [pré-requisitos](#on-premises-prerequisites).


## <a name="step-3-set-up-the-target-environment"></a>Etapa 3: Configurar o ambiente de destino

Especifique a conta de armazenamento do Azure para ser usado para replicação e a rede Azure ao qual o Azure VMs conectará após failover.

1.  Clique em **Preparar infraestrutura** > **destino** e selecione a assinatura do Azure que você deseja usar.
2.  Especifique o modelo de implantação que você deseja usar para VMs após failover.
3.  Recuperação de site verifica se você tem uma ou mais contas de armazenamento do Azure compatível e redes.

    ![Armazenamento](./media/site-recovery-vmm-to-azure/compatible-storage.png)

4.  Se você ainda não tiver criado uma conta de armazenamento e você deseja criá-la usando o Gerenciador de recursos, clique em **+ conta de armazenamento** para fazer esse embutida.  Na lâmina **Criar conta de armazenamento** , especifique um nome de conta, tipo, assinatura e local. A conta deve estar no mesmo local que o Cofre de serviços de recuperação.

    ![Armazenamento](./media/site-recovery-vmm-to-azure/gs-createstorage.png)

    Observe que:

    - Se você quiser criar uma conta de armazenamento usando o modelo clássico, você pode fazer isso no portal do Azure. [Saiba Mais](../storage/storage-create-storage-account-classic-portal.md)
    - Se você estiver usando uma conta de armazenamento premium para dados duplicados, você precisa configurar uma conta de armazenamento adicional de padrão para armazenar logs de replicação que capturar alterações contínuas em dados locais.

4.  Se você ainda não tiver criado uma rede Azure e você deseja criá-la usando o Gerenciador de recursos clique **+ rede** fazer esse embutida. Na lâmina **Criar rede virtual** , especifique um nome de rede, intervalo de endereços, detalhes de sub-rede, assinatura e local. A rede deve estar no mesmo local que o Cofre de serviços de recuperação.

    ![Rede](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

    Se você quiser criar uma rede usando o modelo clássico você vai fazer isso no portal do Azure. [Saiba mais](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

### <a name="configure-network-mapping"></a>Configurar mapeamento de rede

- [Ler](#prepare-for-network-mapping) uma descrição rápida das quais mapeamento de rede faz. [Leia isto](site-recovery-network-mapping.md) para obter uma explicação mais detalhadamente.
- Verifique se que máquinas virtuais do servidor VMM estão conectadas a uma rede de máquina virtual e que você criou pelo menos uma rede virtual Azure. Várias redes de máquina virtual podem ser mapeadas para uma única rede Azure.

Configure mapeamento da seguinte maneira:

1. Nas **configurações** > **Infraestrutura de recuperação de Site** > **mapeamentos de rede** > **Mapeamento de rede**, clique no ícone **+ mapeamento de rede** .

    ![Mapeamento de rede](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. No **mapeamento de rede de adicionar** selecione o servidor do VMM de origem e **Azure** como destino.
3. Verifique se a assinatura e o modelo de implantação após failover.
4. Na **rede de origem**, selecione a rede de máquina virtual do local de origem que você deseja mapear na lista associado ao servidor VMM.
5. Em **rede de destino**, selecione a rede Azure na qual réplica Azure VMs ficarão localizadas quando são criados. Clique em **Okey**.

    ![Mapeamento de rede](./media/site-recovery-vmm-to-azure/network-mapping2.png)

Veja aqui o que acontece quando começa de mapeamento de rede:

- VMs existentes na rede de máquina virtual de origem estão conectadas à rede de destino quando começa de mapeamento. Novas VMs conectadas à rede de máquina virtual de origem estão conectadas à rede do Azure mapeada quando ocorrer a replicação.
- Se você modificar um mapeamento de rede existente, máquinas virtuais de réplica serão conectadas usando as novas configurações.
- Se a rede de destino tem várias sub-redes e uma dessas sub-redes tem o mesmo nome de sub-rede na qual a máquina virtual de origem está localizada, em seguida, na máquina virtual de réplica será conectada para aquela sub-rede de destino após failover.
- Se não houver nenhuma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.



## <a name="step-4-set-up-replication-settings"></a>Etapa 4: Configurar as configurações de replicação


1. Para criar uma nova política de replicação, clique em **Preparar infraestrutura** > **Configurações de replicação** > **+ criar e associar**.

    ![Rede](./media/site-recovery-vmm-to-azure/gs-replication.png)

2. Em **criar e associar política**, especifique um nome de política.
3. Em **Copiar frequência**, especifique com que frequência deseja duplicar dados delta após a replicação inicial (a cada 30 segundos, 5 ou 15 minutos).
4. Em **retenção de ponto de recuperação**, especifique em horas quanto tempo a janela de retenção será para cada ponto de recuperação. Máquinas protegidas podem ser recuperadas a qualquer ponto dentro de uma janela.
6. No **aplicativo consistente frequência de instantâneo**, especifique frequência (1-12 horas) contendo instantâneos consistentes com o aplicativo de pontos de recuperação será criado. Hyper-V usa dois tipos de instantâneos — um instantâneo padrão que fornece um instantâneo incremental da máquina virtual inteiro e um instantâneo consistente com o aplicativo que usa um instantâneo point-in-time dos dados do aplicativo na máquina virtual. Instantâneos consistentes com o aplicativo usam o serviço de cópia de sombra de Volume (VSS) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo é feito. Observe que, se você habilitar instantâneos consistentes com o aplicativo, ele afetará o desempenho dos aplicativos executados em máquinas virtuais de origem. Certifique-se de que o valor definido é menor que o número de pontos de recuperação adicional que você configura.
3. Na **hora de início de replicação inicial**, indique quando iniciar a replicação inicial. A replicação ocorre sobre sua largura de banda de internet, então você pode querer agendá-la fora de suas horas ocupadas.
5. **Criptografar dados armazenados no Azure**, especifique se deseja criptografar dados restante no armazenamento do Azure. Clique em **Okey**.

    ![Política de replicação](./media/site-recovery-vmm-to-azure/gs-replication2.png)

6. Quando você cria uma nova política automaticamente associado com a nuvem VMM. Clique em **Okey**. Você pode associar adicionais nuvens do VMM (e VMs nelas) a esta política de replicação nas **configurações** > **replicação** > nome da política > **Associar VMM nuvem**.

    ![Política de replicação](./media/site-recovery-vmm-to-azure/policy-associate.png)

## <a name="step-5-capacity-planning"></a>Etapa 5: Planejamento da capacidade

Agora que você tem seu basic infraestrutura configurar você pode pensar sobre o planejamento de capacidade e descobrir se você precisa de recursos adicionais.

Recuperação de site fornece um planejador de capacidade para ajudá-lo a alocar os recursos certos para seu ambiente de origem, os componentes de recuperação do site, rede e armazenamento. Você pode executar o planejador no modo rápido para estimativas com base em um número médio de VMs, discos e armazenamento ou no modo detalhado, na qual você vai entrada figuras no nível de carga de trabalho. Antes de começar você precisará:

- Reunir informações sobre o ambiente de replicação, inclusive VMs, discos por VMs e armazenamento por disco.
- Estime a taxa de alteração (rotatividade) diária que terá para dados replicados. Você pode usar o [Planejador de capacidade para Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) para ajudá-lo a fazer isso.

1.  Clique em **Baixar** para baixar a ferramenta e execute-o. [Leia o artigo](site-recovery-capacity-planner.md) que acompanha a ferramenta.
2.  Quando você terminar selecione **Sim** na **você executou o Planejador de capacidade**?

    ![Planejamento da capacidade](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>Considerações de largura de banda de rede

Você pode usar a ferramenta de Planejador de capacidade para calcular a largura de banda que necessária para replicação (replicação inicial e, em seguida, delta). Para controlar a quantidade de uso de largura de banda para replicação, você tem algumas opções:

- **Reduzir largura de banda**: o tráfego Hyper-V Replica para um site secundário passa por meio de um host Hyper-V específico. Você pode reduzir a largura de banda no servidor host.
- **Ajustar a largura de banda**: você pode influenciar a largura de banda usada para replicação usando algumas das chaves do registro.

#### <a name="throttle-bandwidth"></a>Reduzir largura de banda

1. Abra o snap-in do Microsoft Azure Backup MMC no servidor host Hyper-V. Por padrão, um atalho para o Microsoft Azure Backup está disponível na área de trabalho ou em C:\Program Files\Microsoft Azure recuperação serviços Agent\bin\wabadmin.
2. O snap-in, clique em **Alterar propriedades**.
3. Na guia **Throttling** selecione **Habilitar otimização para operações de backup do uso de largura de banda de internet**e definir os limites de trabalho e não-trabalho de horas. Intervalos válidos são de 512 Kbps a 102 Mbps por segundo.

    ![Reduzir largura de banda](./media/site-recovery-vmm-to-azure/throttle2.png)

Você também pode usar o cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para definir a otimização. Aqui está um exemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting-NoThrottle** indica que nenhuma limitação é necessária.


#### <a name="influence-network-bandwidth"></a>Largura de banda de rede de influência

O valor de registro **UploadThreadsPerVM** controla o número de segmentos que são usados para transferir dados (replicação inicial ou delta) de um disco. Um valor mais alto aumenta a largura de banda de rede usada para replicação. O valor de registro **DownloadThreadsPerVM** Especifica o número de segmentos usado para transferir dados durante o failback.

1. Navegue até **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**o registro.

    - Modificar o valor **UploadThreadsPerVM** (ou crie a chave se ele não existir) para controlar threads usados para replicação de disco.
    - Modificar o valor **DownloadThreadsPerVM** (ou crie a chave se ele não existir) para controlar threads usadas para tráfego de failback do Azure.
2. O valor padrão é 4. Em uma rede "overprovisioned", essas chaves do registro devem ser alteradas dos valores padrão. O máximo é 32. Monitorar o tráfego para otimizar o valor.

## <a name="step-6-enable-replication"></a>Etapa 6: Replicação de habilitar

Agora habilite a replicação da seguinte maneira:

1. Clique em **etapa 2: replicar o aplicativo** > **fonte**. Depois de habilitado replicação pela primeira vez que você clique **+ replicar** no cofre para permitir a replicação para máquinas adicionais.

    ![Habilitar a replicação](./media/site-recovery-vmm-to-azure/enable-replication1.png)

2. Na **origem** lâmina > Selecionar o servidor do VMM e a nuvem no qual os hosts Hyper-V estão localizados. Clique em **Okey**.

    ![Habilitar a replicação](./media/site-recovery-vmm-to-azure/enable-replication-source.png)

3. Em **destino** , selecione a assinatura, modelo de implantação de postagem failover e a conta de armazenamento que você estiver usando para dados replicados.

    ![Habilitar a replicação](./media/site-recovery-vmm-to-azure/enable-replication-target.png)

4. Selecione a conta de armazenamento que você deseja usar. Se você quiser usar uma conta de armazenamento diferentes daqueles você ter que você pode [criar uma](#set-up-an-azure-storage-account). Para criar um armazenamento conta usando o modelo do Gerenciador de recursos, clique em **Criar novo**. Se você quiser criar uma conta de armazenamento usando o modelo clássico, faça essa [no portal do Azure](../storage/storage-create-storage-account-classic-portal.md). Clique em **Okey**.
5. Selecione a rede do Azure e sub-rede ao qual Azure VMs conectará quando eles estão girados backup após failover. Selecione **Configurar agora para máquinas selecionadas** para aplicar a configuração de rede a todas as máquinas que você selecionar para proteção. Selecione **Configurar depois** para selecionar a rede Azure por máquina. Se você quiser usar uma rede diferente daqueles você ter que você pode [criar uma](#set-up-an-azure-network). Para criar uma rede usando o modelo do Gerenciador de recursos, clique em **Criar novo**. Se você quiser criar uma rede usando o modelo clássico você fará essa [no portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selecione uma sub-rede se aplicável. Clique em **Okey**.
6. Em **máquinas virtuais** > **Selecione máquinas virtuais** clique e selecione cada máquina que você deseja duplicar. Você só poderá selecionar máquinas qual replicação pode ser habilitada. Clique em **Okey**.

    ![Habilitar a replicação](./media/site-recovery-vmm-to-azure/enable-replication5.png)

5. Nas **Propriedades** > **Configurar propriedades**, selecione o sistema operacional para as VMs selecionadas e o disco de sistema operacional. Clique em **Okey**. Você pode definir propriedades adicionais posteriormente.

    ![Habilitar a replicação](./media/site-recovery-vmm-to-azure/enable-replication6.png)


12. Nas **configurações de replicação** > **Configurar definições de replicação**, selecione a política de replicação que você deseja aplicar para as VMs protegidas. Clique em **Okey**. Você pode modificar a política de replicação nas **configurações** > **políticas de replicação** > nome da política > **Editar configurações**. Alterações aplicadas são usadas para máquinas que já estão replicar e novas máquinas.

    ![Habilitar a replicação](./media/site-recovery-vmm-to-azure/enable-replication7.png)

Você pode controlar o andamento do trabalho **Habilitar proteção** nas **configurações** > **trabalhos** > **trabalhos de recuperação do Site**. Após o trabalho de **Proteção de finalizar** é executado máquina está pronto para failover.

### <a name="view-and-manage-vm-properties"></a>Exibir e gerenciar propriedades de máquina virtual

Recomendamos que você verifique se as propriedades da máquina de origem. Lembre-se de que o nome de máquina virtual do Azure deve estar em conformidade com os [requisitos do Azure máquina virtual](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Clique em **configurações** > **Protegido itens** > **Itens duplicados** > e selecione o computador para ver seus detalhes.

    ![Habilitar a replicação](./media/site-recovery-vmm-to-azure/vm-essentials.png)

2. Nas **Propriedades** que você pode exibir informações de replicação e failover para a máquina virtual.

    ![Habilitar a replicação](./media/site-recovery-vmm-to-azure/test-failover2.png)

3. Em **rede e computação** > a**Calcular propriedades** que você pode especificar o tamanho de nome e destino de máquina virtual do Azure. Modifique o nome em conformidade com [requisitos Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) se for necessário. Você também pode exibir e modificar informações sobre a rede de destino, sub-rede e endereço IP que é atribuído para a máquina virtual do Azure. Observe o seguinte:

    - Você pode definir o endereço IP de destino. Se você não fornecer um endereço, a falha máquina usará DHCP. Se você definir um endereço que não está disponível em failover, o failover falhará. O mesmo endereço IP de destino pode ser usado para testar failover se o endereço está disponível na rede de failover de teste.
    - O número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino, da seguinte maneira:

        - Se o número de adaptadores de rede no computador de origem for menor que ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores como fonte.
        - Se o número de adaptadores da máquina virtual de origem exceder o número permitido para o tamanho de destino e em seguida, o tamanho de destino máximo será usado.
        - Por exemplo, se uma máquina de origem tem dois adaptadores de rede e o tamanho da máquina de destino dá suporte a quatro, máquina de destino terá dois adaptadores. Se o computador de origem tem dois adaptadores, mas o tamanho de destino com suporte apenas dá suporte para uma máquina de destino terá apenas um adaptador.     
        - Se a máquina virtual tem vários adaptadores de rede, que ele serão conectado à mesma rede.

    ![Habilitar a replicação](./media/site-recovery-vmm-to-azure/test-failover4.png)

5.  Em **discos** que você pode ver o sistema operacional e discos de dados sobre a máquina virtual que será duplicada.



## <a name="step-7-test-your-deployment"></a>Etapa 7: Testar sua implantação

Para testar a implantação, você pode executar um failover de teste para uma única máquina virtual ou um plano de recuperação que contém uma ou mais máquinas virtuais.


### <a name="prepare-for-failover"></a>Preparar para failover

- Para executar um failover de teste, que recomendamos que você crie uma nova rede Azure que tem isolados da sua rede de produção Azure (Isso é comportamento padrão quando você cria uma nova rede no Azure). [Saiba mais](site-recovery-failover.md#run-a-test-failover) sobre como executar failovers de teste.
- Para obter o melhor desempenho quando não conseguir ao longo do Azure, instale o agente do Azure na máquina protegida. Ele torna a inicialização com mais rapidez e ajuda a solucionar problemas. Instale o agente [Linux](https://github.com/Azure/WALinuxAgent) ou [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) .
- Para testar completamente sua implantação é necessário uma infraestrutura da máquina replicado funcione como esperado. Se você quiser testar o Active Directory e DNS você pode criar uma máquina virtual como controlador de domínio com o DNS e replicar isso no Azure usando a recuperação de Site do Azure. Leia mais em [Considerações de failover de teste para o Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
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


### <a name="run-a-test-failover"></a>Executar um failover de teste

Para executar o teste failover faça o seguinte:

1. Falha ao longo de uma única VM nas **configurações** > **Itens duplicados**, clique na máquina virtual > **+ Failover de teste**.
2. Falha ao longo de um plano de recuperação, nas **configurações** > de**Planos de recuperação**o plano de atalho > **Failover de teste**. Para criar uma plano de recuperação [siga estas instruções](site-recovery-create-recovery-plans.md).

3. Em **Teste Failover** selecione a rede Azure ao qual se conectar Azure VMs após houver falha.
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


## <a name="monitor-your-deployment"></a>Monitorar sua implantação

Veja aqui como você pode monitorar as definições de configuração, status e integridade para a sua implantação de recuperação do Site:

1. Clique no nome do cofre para acessar o painel **Essentials** . Neste painel, você pode trabalhos recuperação do Site, status de replicação, planos de recuperação, a integridade do servidor e eventos.  Você pode personalizar Essentials para mostrar os blocos e layouts que são mais úteis para você, incluindo o status dos outros compartimentos de Backup e recuperação do Site.

    ![Essentials](./media/site-recovery-vmm-to-azure/essentials.png)

2. No bloco **integridade** , você pode monitorar servidores de sites (servidores VMM ou configuração) que estão tendo o problema e os eventos gerados pelo Site recuperação nas últimas 24 horas.
3. Você pode gerenciar e monitorar a replicação na **Itens duplicados**, de **Planos de recuperação**e blocos de **Trabalhos de recuperação de Site** . Você pode analisar trabalhos nas **configurações** -> **trabalhos** -> **Trabalhos de recuperação de Site**.


## <a name="next-steps"></a>Próximas etapas

Após sua implantação está configurado e funcionando, [Saiba mais](site-recovery-failover.md) sobre os diferentes tipos de falha.
