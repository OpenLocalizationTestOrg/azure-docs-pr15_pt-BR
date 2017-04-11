<properties
    pageTitle="Duplicar VMs Hyper-V em uma nuvem VMM para um site secundário com recuperação de Site do Azure usando SAN | Microsoft Azure"
    description="Este artigo descreve como replicar máquinas virtuais Hyper-V entre dois sites com recuperação de Site do Azure usando replicação SAN."
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
    ms.date="07/06/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-vms-in-a-vmm-cloud-to-a-secondary-site-with-azure-site-recovery-using-san"></a>Duplicar VMs Hyper-V em uma nuvem VMM para um site secundário com recuperação de Site do Azure usando SAN

Neste artigo, você aprenderá como implantar recuperação de Site para coordenar e automatizar replicação SAN e failover para máquinas virtuais de Hyper-V localizado em nuvens System Center VMM para um site VMM secundário.

Depois de ler este artigo postar quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr). 


## <a name="overview"></a>Visão geral

As organizações precisam de uma estratégia de recuperação (BCDR) de desastre e continuidade de negócios que um determina como dados, cargas de trabalho e aplicativos permanecer em execução e disponível durante o tempo de inatividade planejado e e recuperar a condições de trabalho normal assim que possível. BCDR estratégia do seu center em torno de soluções que manter dados de negócios confiáveis e recuperáveis e cargas de trabalho continuamente disponíveis, quando desastre.

Recuperação de site é um serviço Azure que contribui para sua estratégia BCDR por coordenação replicação de servidores físicos locais e máquinas virtuais para a nuvem (Azure) ou um data center secundário. Quando ocorrem interrupções em seu local principal, você não sobre o site secundário para manter aplicativos e cargas de trabalho disponíveis. Você não volta para seu local principal quando ele retorna as operações normais. Recuperação de site pode ser usada em um número de cenários e pode proteger um número de cargas de trabalho. Saiba mais em [o que é recuperação de Site do Azure?](site-recovery-overview.md)

Este artigo inclui instruções para configurar a replicação de VMs Hyper-V de um site VMM para outro usando relication SAN. Ele inclui uma visão geral arquitetônica e os pré-requisitos de implantação e instruções. Você vai descobrir e classificar o armazenamento de SAN no VMM, provisionar LUNs e alocação de armazenamento para clusters Hyper-V. Ele termina testando failover para certificar-se de que tudo está funcionando conforme esperado.


## <a name="why-replicate-with-san"></a>Por que replicar com SAN?

Veja aqui o que este cenário fornece:

- Fornece uma solução de replicação scalable enterprise automatizada pela recuperação de Site.
- Tira proveito de SAN recursos de replicação fornecidos por parceiros de armazenamento enterprise entre os dois conjuntos de armazenamento fibre canal e iSCSI. Veja nossos [parceiros de armazenamento de SAN](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).
- Utiliza sua infraestrutura de SAN existente para proteger aplicativos essenciais implantados em clusters Hyper-V.
- Fornece suporte para clusters de convidado.
- Garante a consistência de replicação em níveis diferentes de um aplicativo com replicação sincronizada para baixo RTO e RPO e replicação sincronizada para maior flexibilidade, dependendo de recursos de matriz de armazenamento.  
- Se integra ao VMM fornece gerenciamento de SAN dentro do console do VMM e SMI-S no VMM descobre armazenamento existente.  

## <a name="architecture"></a>Arquitetura

Este cenário protege suas cargas de trabalho fazendo backup máquinas virtuais Hyper-V de um site VMM local para outro usando replicação de SAN.

![Arquitetura SAN](./media/site-recovery-vmm-san/architecture.png)

Componentes neste cenário incluem:

- **Máquinas virtuais no local**— seus servidores de Hyper-V no local que são gerenciados em nuvens privadas do VMM contêm máquinas virtuais que deseja proteger.
- **Os servidores locais VMM**— você pode um ou mais servidores VMM executando no local principal que você deseja proteger e no local secundário.
- **Armazenamento SAN**— array de SAN de um local principal e um no site secundário
-  **Recuperação de Site do Azure cofre**— o cofre coordenadas e organiza réplica de dados, failover e recuperação entre seus sites de local.
- **Provedor de recuperação de Site do Azure**— provedor é instalado em cada servidor VMM.

## <a name="before-you-start"></a>Antes de começar

Verifique se que você tem estes pré-requisitos no lugar:

**Pré-requisitos** | **Detalhes** 
--- | ---
**Azure**| Você precisará de uma conta [Do Microsoft Azure](https://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação do Site. 
**VMM** | Você precisará pelo menos um servidor VMM implantado como um servidor físico ou virtual autônomo ou como um cluster virtual. <br/><br/>O servidor do VMM deve estar executando o System Center 2012 R2 com as atualizações cumulativas mais recentes.<br/><br/>Você precisará pelo menos uma nuvem configurado no servidor VMM principal que você deseja proteger e uma nuvem configurado no servidor VMM secundário que você deseja usar para recuperação e proteção<br/><br/>A nuvem de fonte que você deseja proteger deve conter um ou mais grupos de host VMM.<br/><br/>Todas as nuvens VMM devem ter o perfil de capacidade de Hyper-V definida.<br/><br/>Saiba mais sobre como configurar as nuvens VMM [Configurando a estrutura de nuvem VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric), e [passo a passo: Criando nuvens privadas com System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).
**Hyper-V** | Você precisará de um ou mais Hyper-V clusters em locais primário e secundários e um ou mais VMs no cluster Hyper-V fonte. VMM hospede grupos nos locais primárias e secundários devem ter um ou mais clusters Hyper-V em cada grupo.<br/><br/>Os servidores de Hyper-V host e de destino devem estar executando pelo menos o Windows Server 2012 com a função Hyper-V e instalaram as últimas atualizações.<br/><br/>Qualquer servidor Hyper-V contendo VMs que você deseja proteger deve estar localizada em uma nuvem VMM.<br/><br/>Se você estiver executando Hyper-V em uma anotação de cluster que corretor de cluster não é criado automaticamente se você tiver um cluster de baseado no endereço IP estático. Você precisará configurar o corretor de cluster manualmente. [Saiba mais](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) em entrada de blog do Anibal Finn.
**Armazenamento SAN** | Usando replicação SAN, você pode replicar agrupada convidado máquinas virtuais com iSCSI ou armazenamento ou compartilhados discos virtuais (vhdx).<br/><br/>Você precisará duas matrizes SAN configurar, um no local principal e um secundário.<br/><br/>Infraestrutura de rede deve ser configurada entre os conjuntos. Correspondência e replicação devem ser configurados. Licenças de replicação devem ser configuradas de acordo com os requisitos de matriz de armazenamento.<br/><br/>Uma rede deve ser configurada entre os servidores de host Hyper-V e a matriz de armazenamento para que hosts possam se comunicar com os LUNs de armazenamento usando ISCSI ou FibreChannel.<br/><br/> Verifique a lista [com suporte matrizes de armazenamento](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).<br/><br/>Provedores de SMI-S, fornecido por fabricantes de matriz o armazenamento deve ser instalados e as matrizes SAN devem ser gerenciadas pelo provedor. Configure o provedor de acordo com sua documentação.<br/><br/>Certifique-se de que o provedor de SMI-S para a matriz está em um servidor que o servidor VMM pode acessar através da rede pelo endereço IP ou FQDN.<br/><br/>Cada matriz SAN deve ter um ou mais pools de armazenamento disponíveis para uso nesta implantação. O servidor do VMM no local principal será necessário gerenciar o array principal e secundário servidor VMM gerenciará o conjunto secundário.<br/><br/>O servidor do VMM no local principal deve gerenciar o array principal e o servidor VMM secundário deve array secundário.
**Mapeamento de rede** | Você pode configurar mapeamento de rede para certificar-se de que replicado máquinas virtuais ideal são colocadas em servidores de host Hyper-V secundários após failover e que eles podem se conectar a redes de máquina virtual apropriados. Se você não configurar réplica de mapeamento de rede que VMs não estiver conectadas a qualquer rede após failover.<br/><br/>Para configurar a rede mapeamento durante a implantação Certifique-se de que as máquinas virtuais no servidor de host de origem Hyper-V estão conectadas a uma rede VMM VM. Rede deve ser vinculada a uma rede lógica que está associada com a nuvem. < br /<br/>A nuvem de destino no servidor VMM secundário que você usa para recuperação deve ter uma rede de máquina virtual correspondente configurada e ele alternadamente deve ser vinculado a uma rede lógica correspondente que está associada com a nuvem de destino.<br/><br/>[Saiba mais](site-recovery-network-mapping.md) sobre o mapeamento de rede.


## <a name="step-1-prepare-the-vmm-infrastructure"></a>Etapa 1: Preparar a infraestrutura de VMM

Para preparar sua infraestrutura VMM que você precisa:

1. Certifique-se de nuvens do VMM estão configurados
2. Integrar e classificar o armazenamento de SAN no VMM
3. Criar LUNs e alocar armazenamento
4. Criar grupos de replicação
5. Configurar redes de máquina virtual

### <a name="ensure-vmm-clouds-are-set-up"></a>Certifique-se de nuvens do VMM estão configurados

Recuperação de site organiza proteção para máquinas virtuais localizados em servidores de host Hyper-V em nuvens do VMM. Você precisará garantir que esses nuvens estão configurados corretamente antes de começar a implantação de recuperação do Site. Saiba mais na [criação de nuvens privadas](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) no blog de Keith Mayer.

### <a name="integrate-and-classify-san-storage-in-vmm"></a>Integrar e classificar o armazenamento de SAN no VMM

Adicione uma classificação SANs no console do VMM:

1. No espaço de trabalho **tecidos** clique em **armazenamento**. Clique em **página inicial** > **Adicionar recursos** > **Dispositivos de armazenamento** para iniciar o Assistente para adicionar dispositivos de armazenamento.
2. Na página **Selecionar um tipo de provedor de armazenamento** , selecione **dispositivos SAN e NAS descobertas e gerenciado por um provedor de SMI-S**.

    ![Tipo de provedor](./media/site-recovery-vmm-san/provider-type.png)

3. Na página **especificar protocolo e endereço do provedor SMI-S de armazenamento** selecione **SMI-S CIMXML** e especifique as configurações de conexão com o provedor.
4. Em **endereço IP do provedor ou FQDN** e **porta de TCP/IP**, especifique as configurações de conexão com o provedor. Você só pode usar uma conexão SSL para CIMXML SMI-S.

    ![Provedor de conexão](./media/site-recovery-vmm-san/connect-settings.png)

5. Na caixa **Executar como conta** Especifica uma conta VMM executar como que pode acessar o provedor ou criar uma nova conta.
6. Na página **Informações de reunir** , VMM tenta automaticamente descobrir e importe as informações de dispositivo de armazenamento. Para repetir a descoberta, clique em **Digitalizar provedor**. Se o processo de descoberta for bem-sucedida, a matrizes de armazenamento detectados, pools de armazenamento, fabricante, modelo e capacidade estão listados na página.

    ![Descubra o armazenamento](./media/site-recovery-vmm-san/discover.png)

7. Em **Selecione pools de armazenamento para colocar o sob gerenciamento e atribuir uma classificação**, selecione os pools de armazenamento que VMM gerenciará e atribuí-las uma classificação. Informações de LUN serão importadas de pools de armazenamento. Crie LUNs com base nos aplicativos que você precisa proteger, seus requisitos de capacidade e o requisito de que precisa replicar juntos.

    ![Classificar armazenamento](./media/site-recovery-vmm-san/classify.png)

### <a name="create-luns-and-allocate-storage"></a>Criar LUNs e alocar armazenamento

1. Depois de armazenamento SAN é integrado ao VMM você criar unidades lógicas (Configurar) (LUNs):

    - [Como selecionar um método para criar unidades lógicas no VMM](https://technet.microsoft.com/library/gg610624.aspx)
    - [Como provisionar unidades lógicas de armazenamento no VMM](https://technet.microsoft.com/library/gg696973.aspx)

    >[AZURE.NOTE] Depois que você habilitar replicação para uma máquina não devem adicione VHDs para ele LUNs que não estão localizados em um grupo de replicação de recuperação do Site. Se você não fizer eles não serão detectados pelo recuperação do Site.

2. Em seguida, alocar capacidade de armazenamento para o cluster de host Hyper-V para que o VMM pode implantar dados de máquina virtual para o armazenamento provisionado:

    - Antes de alocação de armazenamento ao cluster, você precisará alocá-lo para o grupo de host VMM no qual o cluster reside. Veja [como alocar unidades lógicas de armazenamento para um grupo de host](https://technet.microsoft.com/library/gg610686.aspx) e [como alocar pools de armazenamento para um grupo de host](https://technet.microsoft.com/library/gg610635.aspx). </a>.
    - Em seguida, alocar capacidade de armazenamento para cluster conforme descrito em [como configurar armazenamento em um cluster de host Hyper-V no VMM](https://technet.microsoft.com/library/gg610692.aspx). </a>.

### <a name="create-replication-groups"></a>Criar grupos de replicação

Crie um grupo de replicação que inclui todos os LUNs que precisarão replicar juntos.

1. No console do VMM abrir a guia de **Grupos de replicação** das propriedades de matriz de armazenamento e clique em **novo**.
2. Em seguida, crie o grupo de replicação.

    ![Grupo de replicação de SAN](./media/site-recovery-vmm-san/rep-group.png)

### <a name="set-up-networks"></a>Configurar redes

Se você deseja configurar rede mapeamento o seguinte:

1. [Leia sobre](site-recovery-network-mapping.md) o mapeamento de rede.
2. Prepare redes de máquina virtual no VMM:

    - [Configurar redes lógicas](https://technet.microsoft.com/library/jj721568.aspx).
    - [Configurar redes de máquina virtual](https://technet.microsoft.com/library/jj721575.aspx).

## <a name="step-2-create-a-vault"></a>Etapa 2: Criar um cofre


1. Entrar no [Portal de gerenciamento](https://portal.azure.com) do servidor VMM que você deseja registrar.

2. Expanda **Serviços de dados** > **Serviços de recuperação** e clique em **Cofre de recuperação do Site**.

3. Clique em **Criar novo** > **criação rápida**.

4. Em **nome**, digite um nome amigável para identificar o cofre.

5. Na **região** , selecione a região geográfica para o cofre. Para verificar a regiões com suporte consulte disponibilidade geográficos em [Detalhes de preços de recuperação do Azure Site](https://azure.microsoft.com/pricing/details/site-recovery/).

6. Clique em **criar cofre**.

    ![Novo cofre](./media/site-recovery-vmm-san/create-vault.png)

Verifique a barra de status para confirmar que o cofre foi criado com êxito. O cofre será listado como **ativo** na página de **Serviços de recuperação** de principal.


### <a name="register-the-vmm-servers"></a>Registrar os servidores VMM

1. Abra a página de início rápido na página de **Serviços de recuperação** . Início rápido também pode ser aberto a qualquer momento usando o ícone.

    ![Ícone de início rápido](./media/site-recovery-vmm-san/quick-start-icon.png)

2. Na lista suspensa, selecione **entre Hyper-V site usando replicação de array local**.

    ![Chave do registro](./media/site-recovery-vmm-san/select-san.png)


3. Em **servidores de preparar VMM**, baixe a versão mais recente do arquivo de instalação do provedor de recuperação de Site do Azure.
4. Execute esse arquivo no servidor VMM de origem. Se VMM é implantado em um cluster e você estiver instalando o provedor para a primeira vez instalá-lo em um nó ativo e concluir a instalação para registrar o servidor VMM no cofre. Em seguida, instale o provedor nos outros nós. Observe que, se você estiver atualizando o provedor você precisará atualizar em todos os nós porque eles devem todos estar executando a mesma versão do provedor.
5. O instalador faz algumas **Verificação de pré-requisitos** e solicitações de permissão para interromper o serviço VMM para iniciar a instalação do provedor. O serviço de VMM será reiniciado automaticamente quando a instalação terminar. Se você estiver instalando em um cluster VMM que você será solicitado a interromper a função de Cluster.
6. No **Microsoft Update** , você pode optar em atualizações. Com essa configuração habilitada provedor serão instaladas de acordo com a sua política do Microsoft Update.

    ![Atualizações da Microsoft](./media/site-recovery-vmm-san/ms-update.png)

7. O local de instalação está definido como ** <SystemDrive>\Program Files\Microsoft System Center 2012 R2\Virtual máquina Manager\bin**. Clique no botão Instalar para iniciar a instalação do provedor.

    ![InstallLocation](./media/site-recovery-vmm-san/install-location.png)

8. Após a instalação do provedor de clique em botão 'Registrar' para registrar o servidor no cofre.

    ![InstallComplete](./media/site-recovery-vmm-san/install-complete.png)

9. Na **Conexão de Internet** , especifique como o provedor executando no servidor VMM se conecta à Internet. Selecione **usar configurações de proxy do sistema padrão** para usar as configurações de conexão de Internet padrão configuradas no servidor.

    ![Configurações da Internet](./media/site-recovery-vmm-san/proxy-details.png)

    - Se você quiser usar um proxy personalizado, que você deve configurá-lo antes de instalar o provedor. Quando você definir configurações de proxy personalizado um teste será executado para verificar a conexão de proxy.
    - Se você usar um proxy personalizado, ou seu proxy padrão requer autenticação, que você precisará inserir os detalhes de proxy, incluindo o endereço de proxy e porta.
    - Seguintes urls devem ser acessíveis a partir do servidor do VMM e os hosts Hyper-v
        - *. hypervrecoverymanager.windowsazure.com
        - *. accesscontrol.windows.net
        - *. backup.windowsazure.com
        - *. blob.core.windows.net
        - *. store.core.windows.net
    - Permitir que os endereços IP descritos em [Intervalos de IP de data center do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e HTTPS protocolo (443). Você teria intervalos de IP de lista branca do Azure região que você planeja usar e do Oeste EUA.
    - Se você usar um proxy personalizado que uma conta do VMM RunAs (DRAProxyAccount) será criada automaticamente usando as credenciais de proxy especificado. Configure o servidor proxy para que esta conta pode autenticar com êxito. As configurações de conta VMM RunAs podem ser modificadas no console do VMM. Para fazer isso, abra o espaço de trabalho de configurações, expanda segurança, clique contas executar como e, em seguida, modificar a senha para DRAProxyAccount. Você precisará reiniciar o serviço VMM para que essa configuração tenha efeito.

10. Na **Chave do registro**, selecione o que você baixou de recuperação de Site do Azure e copiado para o servidor VMM.
11. Em **nome do cofre**, verifique o nome do cofre em que o servidor será registrado. 

    ![Registro do servidor](./media/site-recovery-vmm-san/vault-creds.png)

12. A configuração de criptografia é usada apenas para VMM Azure cenário, se você for um único usuário VMM VMM e em seguida, você pode ignorar esta tela.

    ![Registro do servidor](./media/site-recovery-vmm-san/encrypt.png)

13. Em **nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Em uma configuração de cluster, especifique o nome da função VMM cluster.
14. Na **sincronização de metadados de nuvem inicial** especifique um nome amigável para o servidor que aparecerão no compartimento e selecione se deseja sincronizar metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só precisa acontecer uma vez em cada servidor. Se você não quiser sincronizar todas as nuvens, você pode deixar essa configuração desmarcadas e sincronizar cada nuvem individualmente nas propriedades da nuvem no console do VMM.

    ![Registro do servidor](./media/site-recovery-vmm-san/friendly-name.png)

15. Clique em **Avançar** para concluir o processo. Depois do registro, metadados do servidor VMM é recuperado por recuperação de Site do Azure. O servidor é exibido na guia *Servidores de VMM* na página **Servers** no cofre.

### <a name="command-line-installation"></a>Instalação de linha de comando

O provedor de recuperação de Site do Azure também pode ser instalado usando a seguinte linha de comando. Este método pode ser usado para instalar o provedor em um núcleo de servidor para o Windows Server 2012 R2.

1. Baixar o arquivo de instalação do provedor e a chave do registro a uma pasta dizer C:\ASR
2. Interromper o serviço de Gerenciador de máquina Virtual do System Center
3. Extrai o instalador do provedor executando o seguinte no prompt de comando com privilégios de **administrador** :

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Instale o provedor executando o seguinte:

        C:\ASR> setupdr.exe /i

5. Registre o provedor executando o seguinte:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

Onde os parâmetros são:

 - **/Credentials** : parâmetro obrigatório que especifica o local em que se encontra o arquivo de chave do registro  
 - **/FriendlyName** : parâmetro obrigatório para o nome do servidor host Hyper-V que aparece no portal do Azure recuperação do Site.
 - **/EncryptionEnabled** : parâmetros opcionais que você precisa usar apenas no VMM Azure cenário, se você precisar de criptografia de suas máquinas virtuais em inativos no Azure. Verifique se o nome do arquivo fornecido tem uma extensão **. pfx** .
 - **/proxyAddress** : parâmetro opcional que especifica o endereço do servidor proxy.
 - **/proxyPort** : parâmetro opcional que especifica a porta do servidor proxy.
 - **/proxyUsername** : parâmetro opcional que especifica o nome de usuário do Proxy (se o proxy exige autenticação).
 - **/proxyPassword** : parâmetro opcional que especifica a senha para autenticação com o servidor proxy (se o proxy exige autenticação).


## <a name="step-3-map-storage-arrays-and-pools"></a>Etapa 3: Mapear matrizes de armazenamento e pools

Mapeie matrizes para especificar qual pool de armazenamento secundário recebe dados de replicação do pool principal. Você deve mapear armazenamento antes de configurar proteção de nuvem porque as informações de mapeamento são usadas quando você ativa a proteção para os grupos de replicação.

Antes de começar a seleção que aparecem de nuvens no cofre. Nuvens são detectados selecionando para sincronizar todas as nuvens ao instalar o provedor ou selecionando para sincronizar uma nuvem específico na guia **Geral** das propriedades nuvem no console do VMM. Mapeie matrizes de armazenamento da seguinte maneira:

1. Clique em **recursos** > **servidor armazenamento** > **origem de mapa e matrizes de destino**.
    ![Registro do servidor](./media/site-recovery-vmm-san/storage-map.png)
2. Selecione os conjuntos de armazenamento no local principal e mapeá-los para conjuntos de armazenamento no local secundário.
3.  Mapeie pools de armazenamento de origem e destino dentro das matrizes. Para fazer isso, em **Pools de armazenamento** , selecione um pool de armazenamento de origem e destino para mapear.

    ![Registro do servidor](./media/site-recovery-vmm-san/storage-map-pool.png)

## <a name="step-4-configure-cloud-protection-settings"></a>Etapa 4: Configure nuvem as configurações de proteção

Depois de servidores VMM são registrados, você pode configurar as configurações de proteção de nuvem. Você habilitou a opção **sincronizar dados de nuvem com o cofre** quando você instalou o provedor para que todas as nuvens no servidor VMM aparecerão na guia <b>Itens protegidos</b> no cofre.

![Nuvem publicado](./media/site-recovery-vmm-san/clouds-list.png)

1. Na página de início rápido, clique em **Configurar proteção para nuvens do VMM**.
2. Na guia **Itens protegido** , selecione a nuvem que você deseja configurar e vá para a guia de **configuração** . Observe que:
3. Em **destino**, selecione **VMM**.
4. No **local de destino**, selecione o servidor VMM no local que gerencia a nuvem que você deseja usar para recuperação.
5. Na **nuvem de destino**, selecione a nuvem de destino que você deseja usar recursos de máquinas virtuais na nuvem fonte. Observe que:
    - Recomendamos que você selecione uma nuvem de destino que atende aos requisitos de recuperação para as máquinas virtuais que você vai proteger.
    - Uma nuvem só pode pertencer a um par de nuvem único — como um primário ou uma nuvem de destino.
6. Recuperação de Site Azure verifica que nuvens tenham acesso ao armazenamento capaz de replicação de SAN e que os conjuntos de armazenamento são peered. Participantes colegas de matriz são exibidas.
7. Se a verificação for bem-sucedida, em **tipo de replicação**, selecione **SAN**.

Depois de salvar as configurações de um trabalho será criado e é possível monitorar na guia **trabalhos** . Configurações de nuvem podem ser modificadas na guia **Configurar** . Se você quiser modificar a nuvem local ou de destino de destino que você deve remover a configuração de nuvem e reconfigure na nuvem.

## <a name="step-5-enable-network-mapping"></a>Etapa 5: Habilitar o mapeamento de rede

1. Na página de início rápido, clique em **Mapear redes**.
2. Selecione o servidor do VMM de origem do qual você deseja mapear redes e o servidor do VMM de destino para a qual as redes serão mapeadas. Na lista de redes de origem e seu destino associado são exibidas. Um valor em branco é mostrado para redes que não estão mapeados. Clique no ícone de informações ao lado dos nomes de rede de origem e destino para exibir as sub-redes para cada rede.
3. Selecione uma rede na **rede na fonte**e clique em **mapa**. O serviço detecta as redes de máquina virtual no servidor de destino e exibe-os.

    ![Arquitetura SAN](./media/site-recovery-vmm-san/network-map1.png)

4. Na caixa de diálogo exibida, selecione uma das redes de máquina virtual do servidor VMM de destino.

    ![Arquitetura SAN](./media/site-recovery-vmm-san/network-map2.png)

5. Quando você selecionar uma rede de destino, as nuvens protegidas que usam a rede de origem são exibidas. Redes de destino disponível que são associadas com as nuvens usadas para proteção também são exibidos. Recomendamos que você selecionar uma rede de destino que está disponível para todas as nuvens que você está usando para proteção.
6.  Clique na marca de seleção para concluir o processo de mapeamento. Um trabalho é iniciado acompanhar o andamento de mapeamento. Você pode exibi-la na guia **trabalhos** .


## <a name="step-6-enable-replication-for-replication-groups"></a>Etapa 6: Habilitar replicação para grupos de replicação

Antes de habilitar proteção para máquinas virtuais, você precisará habilitar replicação para grupos de replicação de armazenamento.

1. No portal do Azure recuperação do Site, na página Propriedades da nuvem primária, abra a guia de **máquinas virtuais** . Clique em **Adicionar grupo de replicação**.
2. Selecione um ou mais grupos de replicação VMM que estão associados com a nuvem, verifique se os conjuntos de origem e destino e especificar a frequência de replicação.

Quando essa operação estiver concluída, recuperação de Site do Azure, junto com o VMM e os provedores de SMI-S provisionar o armazenamento de site de destino LUNs e habilitam a replicação de armazenamento. Se o grupo de replicação já esteja replicado, recuperação de Site do Azure reutiliza o relacionamento de replicação existente e atualiza as informações de recuperação de Site do Azure.

## <a name="step-7-enable-protection-for-virtual-machines"></a>Etapa 7: Habilitar proteção para máquinas virtuais

Após um armazenamento grupo é replicar, ativar a proteção para máquinas virtuais no console do VMM usando um dos seguintes métodos:

- **Nova máquina virtual**— no VMM console quando você cria uma nova máquina virtual você habilitar proteção de recuperação de Site do Azure e associar a máquina virtual no grupo de replicação.
Com essa opção VMM usa posicionamento inteligente ideal colocar o armazenamento de máquina virtual nos LUNs do grupo de replicação. Recuperação de Site Azure organiza a criação de uma máquina virtual de sombra no local secundário e aloca capacidade de modo que máquinas virtuais de réplica pode ser iniciadas após failover.
- **Máquina virtual de existente**— se uma máquina virtual já está implantada no VMM, você pode habilitar a proteção de recuperação de Site do Azure e fazer uma migração de armazenamento para um grupo de replicação. Após a conclusão, VMM e recuperação de Site do Azure detectam a nova máquina virtual e gerenciá-lo na recuperação de Site do Azure para proteção. Uma máquina virtual de sombra é criada no local secundário e capacidade alocada para que possa ser iniciada na máquina virtual de réplica após failover.

    ![Habilitar proteção](./media/site-recovery-vmm-san/enable-protect.png)

Depois de máquinas virtuais estão habilitadas para proteção aparecem no console de recuperação de Site do Azure. Você pode exibir as propriedades de máquina virtual, controlar status e falhar sobre grupos de replicação que contêm várias máquinas virtuais. Observe que em replicação de SAN todas as máquinas virtuais associadas a um grupo de replicação deve falhar sobre juntos. Isso ocorre porque failover ocorre na camada de armazenamento primeiro. É importante agrupar seus grupos de replicação corretamente e local associado somente máquinas virtuais juntos.

>[AZURE.NOTE] Depois que você habilitar replicação para uma máquina não devem adicione VHDs para ele LUNs que não estão localizados em um grupo de replicação de recuperação do Site. Se você não fizer eles não serão detectados pelo recuperação do Site.

Você pode controlar o progresso da ação Habilitar proteção na guia **trabalhos** , incluindo a replicação inicial. Após a execução do trabalho de proteção de finalizar a máquina virtual está pronta para failover.

![Trabalho de proteção de máquina virtual](./media/site-recovery-vmm-san/job-props.png)

## <a name="step-8-test-the-deployment"></a>Etapa 8: Testar a implantação

Teste sua implantação para certificar-se de que máquinas virtuais e dados falharem mais conforme esperado. Para fazer isso, você vai criar um plano de recuperação selecionando grupos de replicação. Execute um failover de teste no plano.

1. Na guia **Planos de recuperação** , clique em **Criar plano de recuperação**.
2. Especifique um nome para o plano de recuperação e servidores VMM de origem e destino. O servidor de origem deve ter máquinas virtuais que estão habilitadas para failover e recuperação. Selecione **SAN** para exibir apenas as nuvens que estão configuradas para replicação SAN.
3.
    ![Criar plano de recuperação](./media/site-recovery-vmm-san/r-plan.png)

4. Em **Selecionar Máquina Virtual**, selecione grupos de replicação. Todas as máquinas virtuais associadas ao grupo de replicação serão selecionadas e adicionadas ao plano de recuperação. Essas máquinas virtuais são adicionadas ao grupo de padrão do plano de recuperação — grupo 1. Se necessário, você pode adicionar mais grupos. Observe que após máquinas virtuais de replicação iniciará de acordo com a ordem dos grupos de plano de recuperação.

    ![Adicionar máquinas virtuais](./media/site-recovery-vmm-san/r-plan-vm.png)
5. Após a recuperação do plano foi criado, ele aparece na lista na guia **Planos de recuperação** .
6. Na guia **Planos de recuperação** , selecione o plano e clique em **Testar Failover**.
7. Na página **Confirmar Failover de teste** , selecione **Nenhum**. Observe que, com essa opção, ativados com falha máquinas virtuais de réplica não estiver conectado a qualquer rede. Isso fará testado que a máquina virtual falhar sobre conforme esperado, mas não testar seu ambiente de rede de replicação. Examine como [executar um failover de teste](site-recovery-failover.md#run-a-test-failover) para obter mais detalhes sobre como usar as opções de rede diferentes.


    ![Rede de teste Select](./media/site-recovery-vmm-san/test-fail1.png)

8. Na máquina virtual de teste será criada no mesmo host como o host em que a máquina virtual de réplica exista. Ele não será adicionado para a nuvem em que máquina virtual réplica está localizada.
9. Após a replicação da máquina virtual de réplica terá um endereço IP que não é a mesma que o endereço IP da máquina virtual primária. Se você estiver emitir endereços do DHCP depois serão atualizados automaticamente. Se você não estiver usando DHCP e quiser certificar-se de que os endereços são as mesmas que você precisará executar alguns scripts.
10. Execute esse script de amostra para recuperar o endereço IP.

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

11. Execute esse script de amostra para atualizar o DNS, especificando o endereço IP que você recuperou usando o script de exemplo anterior.

        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## <a name="next-steps"></a>Próximas etapas

Depois que você já tiver executado um failover de teste para verificar que seu ambiente está funcionando conforme esperado, [Saiba mais sobre](site-recovery-failover.md) os tipos diferentes de failovers.
