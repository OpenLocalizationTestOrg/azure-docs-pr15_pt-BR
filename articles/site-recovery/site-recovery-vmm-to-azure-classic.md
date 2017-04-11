<properties
    pageTitle="Duplicar máquinas virtuais Hyper-V nuvens do VMM para Azure | Microsoft Azure"
    description="Este artigo descreve como replicar máquinas virtuais Hyper-V hosts Hyper-V localizados em nuvens System Center VMM no Azure."
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
    ms.topic="hero-article"
    ms.date="05/06/2016"
    ms.author="raynew"/>

#  <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure"></a>Replicar máquinas virtuais Hyper-V nuvens do VMM no Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmm-to-azure.md)
- [PowerShell - Gerenciador de recursos](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portal clássico](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - clássico](site-recovery-deploy-with-powershell.md)



O serviço de recuperação de Site do Azure contribui para sua estratégia de recuperação (BCDR) de desastre e continuidade de negócios por coordenação replicação, failover e recuperação de máquinas virtuais e servidores físicos. Máquinas podem ser replicadas para Azure ou uma central de dados local secundário. Para obter uma visão geral rápida leia [o que é recuperação de Site do Azure?](site-recovery-overview.md).

## <a name="overview"></a>Visão geral

Este artigo descreve como implantar recuperação de Site para replicar máquinas virtuais de Hyper-V em servidores de host Hyper-V que se encontram em nuvens privadas do VMM no Azure.

O artigo inclui os pré-requisitos para o cenário e mostra como configurar um cofre de recuperação do Site, obter o provedor de recuperação de Site Azure instalados no servidor VMM de origem, registrar o servidor no cofre, adicionar uma conta de armazenamento do Azure, instalar o agente de serviços de recuperação do Azure em servidores de host Hyper-V, definir configurações de proteção para nuvens VMM que serão aplicadas a todas as máquinas virtuais protegidas e em seguida, Habilitar proteção para essas máquinas virtuais. Concluir a configuração testando o failover para garantir que tudo está funcionando conforme esperado.

Poste quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="architecture"></a>Arquitetura

![Arquitetura](./media/site-recovery-vmm-to-azure-classic/topology.png)

- O provedor de recuperação de Site do Azure está instalado no VMM durante a implantação de recuperação do Site e do servidor VMM está registrado no cofre de recuperação do Site. O provedor se comunica com a recuperação de Site para lidar com coordenação de replicação.
- O agente de serviços de recuperação do Azure é instalado em servidores de host Hyper-V durante a implantação de recuperação do Site. Trata replicação de dados para o armazenamento do Azure.


## <a name="azure-prerequisites"></a>Pré-requisitos Azure

Veja aqui o que você vai precisar no Azure.

**Pré-requisito** | **Detalhes**
--- | ---
**Conta do Azure**| Você precisará de uma conta [Do Microsoft Azure](https://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação do Site.
**Armazenamento do Azure** | Você precisará de uma conta de armazenamento do Azure para armazenar dados duplicados. Dados replicados são armazenados no armazenamento do Azure e Azure VMs são giradas para cima quando houver falha. <br/><br/>Você precisa de uma [conta de armazenamento redundantes de localização geográfica padrão](../storage/storage-redundancy.md#geo-redundant-storage). A conta deve na mesma região como o serviço de recuperação de Site e ser associadas a mesma assinatura. Observe que a duplicação para contas de armazenamento premium atualmente não são compatíveis e não devem ser usados.<br/><br/>[Leia sobre](../storage/storage-introduction.md) Armazenamento do Azure.
**Rede do Azure** | Você precisará de uma rede virtual Azure que Azure VMs se conectará quando houver falha. A rede virtual Azure deve estar na mesma região como o Cofre de recuperação do Site.

## <a name="on-premises-prerequisites"></a>Pré-requisitos de local

Veja aqui o que é preciso local.

**Pré-requisito** | **Detalhes**
--- | ---
**VMM** | Você precisará pelo menos um servidor VMM implantado como um servidor físico ou virtual autônomo ou como um cluster virtual. <br/><br/>O servidor do VMM deve estar executando o System Center 2012 R2 com as atualizações cumulativas mais recentes.<br/><br/>Você precisará pelo menos uma nuvem configurada no servidor VMM.<br/><br/>A nuvem de fonte que você deseja proteger deve conter um ou mais grupos de host VMM.<br/><br/>Saiba mais sobre como configurar nuvens do VMM no [passo a passo: Criando nuvens privadas com System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) no blog de Keith Mayer.
**Hyper-V** | Você precisará de um ou mais servidores de host Hyper-V ou clusters na nuvem VMM. O servidor host deve ter um ou mais VMs e. <br/><br/>O servidor Hyper-V deve estar executando pelo menos no **Windows Server 2012 R2** com a função Hyper-V ou **Microsoft Hyper-V Server 2012 R2** e tem instalado as atualizações mais recentes.<br/><br/>Qualquer servidor Hyper-V contendo VMs que você deseja proteger deve estar localizada em uma nuvem VMM.<br/><br/>Se você estiver executando Hyper-V em uma anotação de cluster que corretor de cluster não é criado automaticamente se você tiver um cluster de baseado no endereço IP estático. Você precisará configurar o corretor de cluster manualmente. [Saiba mais](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) em entrada de blog do Anibal Finn.
**Máquinas protegidas** | VMs que você deseja proteger devem ser compatíveis com os [requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).


## <a name="network-mapping-prerequisites"></a>Pré-requisitos de mapeamento de rede
Quando você proteger máquinas virtuais em mapas de mapeamento de rede Azure entre redes de máquina virtual no servidor VMM de origem e destino redes Azure para habilitar o seguinte:

- Todas as máquinas que failover na mesma rede pode se conectar uns aos outros, independentemente de qual plano de recuperação estiverem em.
- Se um gateway de rede está configurado no destino rede Azure, máquinas virtuais pode se conectar com outras máquinas virtuais de local.
- Se você não configurar mapeamento somente máquinas virtuais de rede que falham sobre no mesmo plano de recuperação será capaz de se conectar uns aos outros após failover no Azure.

Se você deseja implantar o mapeamento de rede será necessário o seguinte:

- As máquinas virtuais que você deseja proteger no servidor VMM de origem deve estar conectadas a uma rede de máquina virtual. Rede deve ser vinculada a uma rede lógica que está associada com a nuvem.
- Uma rede Azure ao qual replicadas máquinas virtuais podem se conectar após failover. Você selecionará esta rede no momento de falha. A rede deve estar na mesma região como sua assinatura de recuperação de Site do Azure.

Prepare para o mapeamento de rede da seguinte maneira:

1. [Leia sobre](site-recovery-network-mapping.md) requisitos de mapeamento de rede.
2. Prepare redes de máquina virtual no VMM:

    - [Configurar redes lógicas](https://technet.microsoft.com/library/jj721568.aspx).
    - [Configurar redes de máquina virtual](https://technet.microsoft.com/library/jj721575.aspx).


## <a name="step-1-create-a-site-recovery-vault"></a>Etapa 1: Criar um cofre de recuperação do Site

1. Entrar no [Portal de gerenciamento](https://portal.azure.com) do servidor VMM que você deseja registrar.
2. Clique em **Serviços de dados** > **Serviços de recuperação** > **cofre de recuperação do Site**.
3. Clique em **Criar novo** > **criação rápida**.
4. Em **nome**, digite um nome amigável para identificar o cofre.
5. Na **região**, selecione a região geográfica para o cofre. Para verificar a regiões com suporte consulte disponibilidade geográficos em [Detalhes de preços de recuperação do Azure Site](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Clique em **criar cofre**.

    ![Novo cofre](./media/site-recovery-vmm-to-azure-classic/create-vault.png)

Verifique a barra de status para confirmar que o cofre foi criado com êxito. O cofre será listado como **ativo** na página de serviços de recuperação de principal.

## <a name="step-2-generate-a-vault-registration-key"></a>Etapa 2: Gerar uma chave de registro do cofre

Gere uma chave de registro no cofre. Depois de baixar o provedor de recuperação de Site do Azure e instalá-lo no servidor VMM, você usará esta tecla para registrar o servidor VMM no cofre.

1. Na página **Serviços de recuperação** , clique no cofre para abrir a página de início rápido. Início rápido também pode ser aberto a qualquer momento usando o ícone.

    ![Ícone de início rápido](./media/site-recovery-vmm-to-azure-classic/qs-icon.png)

2. Na lista suspensa, selecione **entre um site VMM locais e do Microsoft Azure**.
3. Em **Preparar VMM servidores**, clique em arquivo de **Gerar chave do registro** . O arquivo de chave é gerado automaticamente e é válido por 5 dias depois que ela é gerada. Se você não estiver acessando o portal do Azure do servidor VMM você precisará copiar esse arquivo para o servidor.

    ![Chave do registro](./media/site-recovery-vmm-to-azure-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>Etapa 3: Instalar o provedor de recuperação de Site Azure

1. No **Início rápido** > **VMM preparar servidores**, clique em **Baixar o provedor do recuperação do Microsoft Azure Site para instalação nos servidores VMM** para obter a versão mais recente do arquivo de instalação do provedor.
2. Execute esse arquivo no servidor VMM de origem.

    >[AZURE.NOTE] Se VMM é implantado em um cluster e você estiver instalando o provedor para a primeira vez instalá-lo em um nó ativo e concluir a instalação para registrar o servidor VMM no cofre. Em seguida, instale o provedor nos outros nós. Observe que, se você estiver atualizando o provedor você precisará atualizar em todos os nós porque eles devem todos estar executando a mesma versão do provedor.

3. O instalador faz uma verificação de prerequirements e as solicitações de permissão para interromper o serviço VMM para iniciar a instalação do provedor. O serviço de VMM será reiniciado automaticamente quando a instalação terminar. Se você estiver instalando em um cluster VMM que você será solicitado a interromper a função de Cluster.

4. No **Microsoft Update** , você pode optar em atualizações. Com essa configuração habilitada provedor serão instaladas de acordo com a sua política do Microsoft Update.

    ![Atualizações da Microsoft](./media/site-recovery-vmm-to-azure-classic/updates.png)


5.  O local de instalação para o provedor está definido como ** <SystemDrive>\Program Files\Microsoft System Center 2012 R2\Virtual máquina Manager\bin**. Clique em **instalar**.

    ![InstallLocation](./media/site-recovery-vmm-to-azure-classic/install-location.png)

6. Após a instalação do provedor de clique em **registrar** para registrar o servidor no cofre.

    ![InstallComplete](./media/site-recovery-vmm-to-azure-classic/install-complete.png)

9. Em **nome do cofre**, verifique o nome do cofre em que o servidor será registrado. Clique em *Avançar*.

    ![Registro do servidor](./media/site-recovery-vmm-to-azure-classic/vaultcred.PNG)

7. Na **Conexão de Internet** , especifique como o provedor executando no servidor VMM se conecta à Internet. Selecione **conectar-se com as configurações de proxy existentes** para usar as configurações de conexão de Internet padrão configuradas no servidor.

    ![Configurações da Internet](./media/site-recovery-vmm-to-azure-classic/proxydetails.PNG)

    - Se você quiser usar um proxy personalizado, que você deve configurá-lo antes de instalar o provedor. Quando você definir configurações de proxy personalizado um teste será executado para verificar a conexão de proxy.
    - Se você usar um proxy personalizado, ou seu proxy padrão requer autenticação, que você precisará inserir os detalhes de proxy, incluindo o endereço de proxy e porta.
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

    ![LastPage](./media/site-recovery-vmm-to-azure-classic/provider13.PNG)

Depois do registro, metadados do servidor VMM é recuperado por recuperação de Site do Azure. O servidor é exibido na guia **Servidores de VMM** na página **Servers** no cofre.

### <a name="command-line-installation"></a>Instalação de linha de comando

O provedor de recuperação de Site do Azure também pode ser instalado usando a seguinte linha de comando. Este método pode ser usado para instalar o provedor em um núcleo de servidor para o Windows Server 2012 R2.

1. Baixe a chave de registro e arquivo de instalação do provedor para uma pasta. Por exemplo: C:\ASR.
2. Interromper o serviço do Gerenciador de máquina Virtual do System Center
3. Em um prompt de comando elevado, extrai o instalador do provedor com estes comandos:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Instale o provedor da seguinte maneira:

        C:\ASR> setupdr.exe /i

5. Registre o provedor da seguinte maneira:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Onde os parâmetros são da seguinte maneira:

 - **/Credentials** : parâmetro obrigatório que especifica o local em que se encontra o arquivo de chave do registro  
 - **/FriendlyName** : parâmetro obrigatório para o nome do servidor host Hyper-V que aparece no portal do Azure recuperação do Site.
 - **/EncryptionEnabled** : parâmetro opcional para especificar se deseja criptografia suas máquinas virtuais no Azure (na criptografia rest). O nome do arquivo deve ter uma extensão **. pfx** .
 - **/proxyAddress** : parâmetro opcional que especifica o endereço do servidor proxy.
 - **/proxyPort** : parâmetro opcional que especifica a porta do servidor proxy.
 - **/proxyUsername** : parâmetro opcional que especifica o nome de usuário de proxy.
 - **/proxyPassword** : parâmetro opcional que especifica a senha de proxy.  


## <a name="step-4-create-an-azure-storage-account"></a>Etapa 4: Criar uma conta de armazenamento do Azure

1. Se você não tiver uma conta de armazenamento do Azure clique em **Adicionar uma conta de armazenamento do Azure** para criar uma conta.
2. Crie uma conta com replicação geográfica habilitada. Ele deve na mesma região como o serviço de recuperação de Site do Azure e ser associadas a mesma assinatura.

    ![Conta de armazenamento](./media/site-recovery-vmm-to-azure-classic/storage.png)

> [AZURE.NOTE] Não há suporte para a [migração de contas de armazenamento](../resource-group-move-resources.md) em grupos de recursos dentro da mesma assinatura ou assinaturas para contas de armazenamento usadas para implantar recuperação do Site.

## <a name="step-5-install-the-azure-recovery-services-agent"></a>Etapa 5: Instalar o agente de serviços de recuperação Azure

Instale o agente de serviços de recuperação do Azure em cada servidor de host Hyper-V na nuvem VMM.

1. Clique em **Quick Start** > **Baixar agente de serviços de recuperação de Site de Azure e instalar em hosts** para obter a versão mais recente do arquivo de instalação do agente.

    ![Instalar o agente de recuperação de serviços](./media/site-recovery-vmm-to-azure-classic/install-agent.png)

2. Execute o arquivo de instalação em cada servidor de host Hyper-V.
3. Na página **De verificação de pré-requisitos** , clique em **Avançar**. Quaisquer pré-requisitos ausentes serão instalados automaticamente.

    ![Agente de serviços de recuperação de pré-requisitos](./media/site-recovery-vmm-to-azure-classic/agent-prereqs.png)

4. Na página **Configurações de instalação** , especifique onde você deseja instalar o agente e selecione o local do cache no qual metadados de backup serão instalado. Clique em **instalar**.
5. Após concluir instalação clique em **Fechar** para concluir o assistente.

    ![Registrar MARTE agente](./media/site-recovery-vmm-to-azure-classic/agent-register.png)

### <a name="command-line-installation"></a>Instalação de linha de comando

Você também pode instalar o agente de serviços de recuperação do Microsoft Azure da linha de comando usando este comando:

    marsagentinstaller.exe /q /nu

## <a name="step-6-configure-cloud-protection-settings"></a>Etapa 6: Configurar nuvem as configurações de proteção

Depois do servidor VMM é registrado, você pode configurar as configurações de proteção de nuvem. Você habilitou a opção **sincronizar dados de nuvem com o cofre** quando você instalou o provedor para que todas as nuvens no servidor VMM aparecerão na guia <b>Itens protegidos</b> no cofre.

![Nuvem publicado](./media/site-recovery-vmm-to-azure-classic/clouds-list.png)

1. Na página de início rápido, clique em **Configurar proteção para nuvens do VMM**.
2. Na guia **Itens protegido** , clique na nuvem que você deseja configurar e vá para a guia de **configuração** .
3. Em **destino** , selecione **Azure**.
4. Na **Conta de armazenamento** , selecione a conta de armazenamento do Azure usada para replicação.
5. Definir a **criptografar dados armazenados** para **desativado**. Esta configuração especifica que dados devem ser criptografados replicados entre o site local e o Azure.
6. Em **Copiar frequência** deixe a configuração padrão. Esse valor Especifica a frequência dados devem ser sincronizados entre locais de origem e destino.
7. Em **pontos de recuperação de reter para**, deixe a configuração padrão. Com um valor padrão igual a zero, apenas o ponto de recuperação de mais recente para uma máquina virtual primária é armazenado em um servidor de host de réplica.
8. Na **frequência de instantâneos consistentes com o aplicativo**, deixe a configuração padrão. Esse valor Especifica a frequência de criar instantâneos. Instantâneos usam o serviço de cópia de sombra de Volume (VSS) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo é feito.  Se você definir um valor, verifique se que ele for menor que o número de pontos de recuperação adicional que você configura.
9. Na **hora de início de replicação**, especifique quando replicação inicial dos dados no Azure deve começar. O fuso horário no servidor host Hyper-V será usado. Recomendamos que você agende a replicação inicial durante o horário de pico.

    ![Configurações de replicação de nuvem](./media/site-recovery-vmm-to-azure-classic/cloud-settings.png)

Depois de salvar as configurações de um trabalho será criado e é possível monitorar na guia **trabalhos** . Todos os servidores de host Hyper-V na nuvem VMM fonte serão configurados para replicação.

Depois de salvar, configurações de nuvem podem ser modificadas na guia **Configurar** . Para modificar o local de destino ou a conta de armazenamento de destino, você precisará remover a configuração de nuvem e depois reconfigure na nuvem. Observe que se você alterar a conta de armazenamento a alteração é aplicada somente para máquinas virtuais que estão habilitadas para proteção depois que a conta de armazenamento foi modificada. Máquinas virtuais existentes não são migradas para a nova conta de armazenamento.

## <a name="step-7-configure-network-mapping"></a>Etapa 7: Configurar mapeamento de rede
Antes de começar o mapeamento de rede verificar se máquinas virtuais no servidor VMM de origem estão conectadas a uma rede de máquina virtual. Além disso, crie uma ou mais redes virtuais Azure. Observe que várias redes de máquina virtual podem ser mapeadas para uma única rede Azure.

1. Na página de início rápido, clique em **Mapear redes**.
2. Na guia **redes** , no **local de origem**, selecione o servidor VMM de origem. No **local de destino** , selecione Azure.
3. Em redes de **fonte de** uma lista de redes de máquina virtual associado ao servidor VMM são exibidas. Em redes de **destino** das redes Azure associadas a assinatura são exibidas.
4. Selecione a rede de máquina virtual de origem e clique em **mapa**.
5. Na página **Selecionar uma rede de destino** , selecione o destino Azure rede que você deseja usar.
6. Clique na marca de seleção para concluir o processo de mapeamento.

    ![Configurações de replicação de nuvem](./media/site-recovery-vmm-to-azure-classic/map-networks.png)

Depois de salvar as configurações de um trabalho é iniciado acompanhar o andamento de mapeamento e ele possível monitorar na guia trabalhos. Qualquer máquinas virtuais réplica existentes que correspondem à rede de máquina virtual do fonte serão conectadas ao destino Azure redes. Novas máquinas virtuais que estão conectadas à rede de máquina virtual do fonte serão conectadas à rede do Azure mapeada após a replicação. Se você modificar um mapeamento com uma nova rede existente, máquinas virtuais de réplica serão conectadas usando as novas configurações.

Observe que, se a rede de destino tem várias sub-redes e uma dessas sub-redes tem o mesmo nome de sub-rede na qual a máquina virtual de origem está localizada, em seguida, na máquina virtual de réplica será conectada ao sub-rede destino após failover. Se não houver nenhuma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.

> [AZURE.NOTE] [Migração de redes](../resource-group-move-resources.md) em grupos de recursos dentro da mesma assinatura ou assinaturas não há suporte para redes usadas para implantar recuperação do Site.

## <a name="step-8-enable-protection-for-virtual-machines"></a>Etapa 8: Habilitar proteção para máquinas virtuais

Depois de servidores, nuvens e redes estão configurados corretamente, você pode habilitar a proteção para máquinas virtuais na nuvem. Observe o seguinte:

- Máquinas virtuais devem atender aos [requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).
- Para ativar a proteção do sistema operacional e o sistema operacional propriedades de disco devem ser definidas para a máquina virtual. Quando você cria uma máquina virtual no VMM usando um modelo de máquina virtual, você pode definir a propriedade. Você também pode definir essas propriedades para máquinas virtuais existentes nas guias **Geral** e **Configuração de Hardware** das propriedades de máquina virtual. Se você não definir essas propriedades no VMM, você poderá configurá-los no portal do Azure recuperação do Site.

    ![Criar máquina virtual](./media/site-recovery-vmm-to-azure-classic/enable-new.png)

    ![Modificar as propriedades de máquina virtual](./media/site-recovery-vmm-to-azure-classic/enable-existing.png)


1. Para ativar a proteção, na guia **máquinas virtuais** na nuvem em que a máquina virtual está localizada, clique em **Habilitar proteção** > **Adicionar máquinas virtuais**.
2. Na lista de máquinas virtuais na nuvem, selecione aquele que você deseja proteger.

    ![Ativar a proteção de máquina virtual](./media/site-recovery-vmm-to-azure-classic/select-vm.png)

    Controlar o andamento da ação **Habilitar proteção** na guia **trabalhos** , incluindo a replicação inicial. Depois que o trabalho de **Proteção de finalizar** é executado na máquina virtual está pronta para failover. Depois de proteção está habilitada e máquinas virtuais são replicadas, você poderá visualizá-las no Azure.


    ![Trabalho de proteção de máquina virtual](./media/site-recovery-vmm-to-azure-classic/vm-jobs.png)

3. Verifique as propriedades de máquina virtual e modificar conforme necessário.

    ![Verifique se máquinas virtuais](./media/site-recovery-vmm-to-azure-classic/vm-properties.png)


4. Na guia **Configurar** as propriedades de máquina virtual seguintes propriedades de rede podem ser modificadas.





- **Número de adaptadores de rede na máquina virtual destino** - o número de adaptadores de rede é determinada pelo tamanho especificado para a máquina virtual de destino. Verifique as [especificações de tamanho de máquina virtual](../virtual-machines/virtual-machines-linux-sizes.md#size-tables) para o número de adaptadores suportados pelo tamanho da máquina virtual. Quando você modifica o tamanho de uma máquina virtual e salva as configurações, o número de adaptador de rede serão alteradas quando abrir **Configurar** página na próxima vez. O número de adaptadores de rede de máquinas virtuais de destino é o número mínimo de adaptadores de rede na máquina virtual de origem e o número máximo de adaptadores de rede compatíveis com o tamanho da máquina virtual escolhido, da seguinte maneira:

    - Se o número de adaptadores de rede no computador de origem for menor que ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores como fonte.
    - Se o número de adaptadores da máquina virtual de origem exceder o número permitido para o tamanho de destino e em seguida, o tamanho máximo de destino será usado.
    - Por exemplo, se uma máquina de origem tem dois adaptadores de rede e o tamanho da máquina de destino oferecer suporte a quatro, máquina de destino terá dois adaptadores. Se o computador de origem tem dois adaptadores, mas o tamanho de destino com suporte apenas dá suporte para uma máquina de destino terá apenas um adaptador.    

- **Rede da máquina virtual destino** - a rede à qual a máquina virtual se conecta é determinada pelo mapeamento de rede da rede de máquina virtual de origem. Se a máquina virtual de origem tem mais de um adaptador de rede e redes de origem são mapeados para redes diferentes no destino, você precisará escolher entre uma das redes de destino.
- **Sub-rede de cada adaptador de rede** - para cada adaptador de rede que você pode selecionar a sub-rede à qual o falhou ao longo de máquina virtual se conectará a.
- **Endereço IP de destino** - se adaptador de rede de máquina virtual de origem está configurado para usar um endereço IP estático, em seguida, você pode fornecer o endereço IP da máquina virtual de destino. Use este recurso manter o endereço IP de uma máquina virtual de origem após um failover. Se nenhum endereço IP for fornecido, em seguida, qualquer endereço IP disponível recebe ao adaptador de rede no momento de falha. Se o endereço IP de destino for especificado, mas está sendo usado por outra máquina virtual em execução no Azure failover falhará.  

    ![Modificar propriedades de rede](./media/site-recovery-vmm-to-azure-classic/multi-nic.png)

>[AZURE.NOTE] Máquinas virtuais de Linux com endereço IP estático não têm suporte.

## <a name="test-the-deployment"></a>Testar a implantação

Para testar sua implantação, você pode executar um failover de teste para uma única máquina virtual, ou criar um plano de recuperação consiste em várias máquinas virtuais e execute um failover de teste para o plano.  

Failover de teste simula o mecanismo de failover e recuperação em uma rede isolada. Observe que:

- Se você deseja se conectar à máquina virtual no Azure usando a área de trabalho remota após o failover, habilite Conexão de área de trabalho remota na máquina virtual antes de executar o failover de teste.
- Após failover você vai usar um endereço IP público para conectar-se à máquina virtual no Azure usando a área de trabalho remota. Se você quiser fazer isso, certifique-se de que você não tem quaisquer diretivas de domínio que impedem a conexão para uma máquina virtual usando um endereço público.

>[AZURE.NOTE] Para obter o melhor desempenho quando você fizer um failover no Azure, certifique-se de que você tenha instalado o agente do Azure na máquina protegida. Isso ajuda na inicialização com mais rapidez e também ajuda no diagnóstico em caso de problemas. Agente de Linux pode ser encontradas [aqui](https://github.com/Azure/WALinuxAgent) - e Windows agente pode ser encontrada [aqui](http://go.microsoft.com/fwlink/?LinkID=394789)

### <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

1. Na guia **Planos de recuperação** , adicione um novo plano. Especifique um nome, **VMM** no **tipo de fonte**, e o servidor do VMM de origem na **fonte**, o destino será Azure.

    ![Criar plano de recuperação](./media/site-recovery-vmm-to-azure-classic/recovery-plan1.png)

2. Na página **Selecionar máquinas virtuais** , selecione máquinas virtuais para adicionar ao plano de recuperação. Essas máquinas virtuais são adicionadas ao grupo de padrão do plano de recuperação — grupo 1. Um máximo de 100 máquinas virtuais em um plano de recuperação único foram testadas.

- Se você quiser verificar as propriedades de máquina virtual antes de adicioná-los ao plano, clique na máquina virtual na página Propriedades da nuvem na qual está localizada. Você também pode configurar as propriedades de máquina virtual no console do VMM.
- Todas as máquinas virtuais que são exibidas foram habilitadas para proteção. A lista inclui as duas máquinas virtuais que estão ativadas para proteção e replicação inicial foi concluída e aqueles que estão habilitados para proteção com inicial replicação pendentes. Somente as máquinas virtuais com replicação inicial concluída pode falhar sobre como parte de um plano de recuperação.

    ![Criar plano de recuperação](./media/site-recovery-vmm-to-azure-classic/select-rp.png)

Depois de um plano de recuperação tiver sido criado ele aparece na guia **Planos de recuperação** . Você também pode adicionar [runbooks de automação Azure](site-recovery-runbook-automation.md) para o plano de recuperação para automatizar ações durante tolerância a falhas.

### <a name="run-a-test-failover"></a>Executar um failover de teste

Há duas maneiras de executar um failover de teste para o Azure.

- **Testar failover sem uma rede Azure**— esse tipo de falha de teste verifica que a máquina virtual surgir corretamente no Azure. A máquina virtual não estiver conectada a qualquer rede Azure após failover.
- **Testar failover com uma rede Azure**— este tipo de failover verificações que o ambiente inteiro de replicação vem até conforme o esperado e que falhou máquinas virtuais será conectado ao destino especificado rede Azure. Para a manipulação de sub-rede, para teste failover a sub-rede da máquina virtual teste será ser descoberto com base na sub-rede da máquina virtual réplica. Isso é diferente de replicação normal quando a sub-rede de uma máquina virtual de réplica baseia-se na sub-rede da máquina virtual de origem.

Se você desejar executar um failover de teste para uma máquina virtual habilitada para proteção do Azure sem especificar uma rede de destino Azure não é necessário preparar nada. Para executar um failover de teste com um destino rede Azure, você precisará criar uma nova rede Azure que tem isolados da sua rede de produção Azure (comportamento padrão quando você cria uma nova rede no Azure). Examine como [executar um failover de teste](site-recovery-failover.md#run-a-test-failover) para obter mais detalhes.


Você também precisará configurar a infraestrutura para a máquina virtual replicada funcione como esperado. Por exemplo, uma máquina virtual com controlador de domínio e DNS podem ser replicada para Azure usando a recuperação de Site do Azure e pode ser criada na rede de teste usando testar Failover. Examine a seção [Testar failover considerações para o active directory](site-recovery-active-directory.md#considerations-for-test-failover) para obter mais detalhes.

Para executar um teste failover, faça o seguinte:

1. Na guia **Planos de recuperação** , selecione o plano e clique em **Testar Failover**.
2. Na página **Confirmar teste Failover** selecione **Nenhuma** ou uma rede Azure específica.  Observe que, se você selecionar nenhum o failover de teste verificará que a máquina virtual replicados corretamente para Azure mas não verifica a configuração da sua rede de replicação.

    ![Nenhuma rede](./media/site-recovery-vmm-to-azure-classic/test-no-network.png)

3. Se a criptografia de dados está habilitada para a nuvem, na **Chave de criptografia** , selecione o certificado que foi emitido durante a instalação do provedor no servidor VMM, quando você ativou a opção para habilitar a criptografia de dados para uma nuvem.
4. Na guia **trabalhos** você pode controlar o progresso de failover. Você também deve ser capaz de ver a réplica de teste de máquina virtual no portal do Azure. Se estiver configurado para cima até máquinas virtuais de acesso da sua rede local, você pode iniciar uma conexão de área de trabalho remota na máquina virtual.
5. Quando o failover atinge a fase de **teste concluída** , clique em **Testar concluído** para concluir o failover de teste. Você pode fazer drill down a guia de **trabalho** para controlar o status e o andamento de failover e para realizar ações que são necessários.
6. Após o failover, você poderá ver a réplica de teste de máquina virtual no portal do Azure. Se estiver configurado para cima até máquinas virtuais de acesso da sua rede local, você pode iniciar uma conexão de área de trabalho remota na máquina virtual. Faça o seguinte:

    1. Verifique se as máquinas virtuais inicie com êxito.
    2. Se você deseja se conectar à máquina virtual no Azure usando a área de trabalho remota após o failover, habilite Conexão de área de trabalho remota na máquina virtual antes de executar o failover de teste. Você também precisará adicionar um ponto de extremidade do RDP na máquina virtual. Você pode aproveitar um [Runbooks de automação do Azure](site-recovery-runbook-automation.md) para fazê-lo.
    3. Depois de failover se você usar um endereço IP público para se conectar à máquina virtual no Azure usando a área de trabalho remota, verifique se você não tem quaisquer diretivas de domínio que impedem a conexão para uma máquina virtual usando um endereço público.

7.  Depois que o teste for concluído, faça o seguinte:
    - Clique no **failover de teste for concluído**. Limpe o ambiente de teste para automaticamente desligue e excluir as máquinas virtuais de teste.
    - Clique em **anotações** para gravar e salvar observações associadas com o failover de teste.

>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [configuração de planos de recuperação](site-recovery-create-recovery-plans.md) e [failover](site-recovery-failover.md).
