<properties
    pageTitle="Duplicar máquinas virtuais Hyper-V nuvens do VMM para um site secundário do VMM | Microsoft Azure"
    description="Este artigo descreve como replicar VMs Hyper-V em nuvens VMM para um site secundário do VMM com recuperação de Site do Azure."
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

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>Duplicar máquinas virtuais Hyper-V nuvens do VMM para um site VMM secundário

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmm-to-vmm.md)
- [Portal clássico](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - Gerenciador de recursos](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

O serviço de recuperação de Site do Azure contribui para sua estratégia de recuperação (BCDR) de desastre e continuidade de negócios por coordenação replicação, failover e recuperação de máquinas virtuais e servidores físicos. Máquinas podem ser replicadas para Azure ou uma central de dados local secundário. Para obter uma visão geral rápida leia [o que é recuperação de Site do Azure?](site-recovery-overview.md)

## <a name="overview"></a>Visão geral

Este artigo descreve como replicar máquinas virtuais de Hyper-V em servidores de host Hyper-V que são gerenciados em nuvens VMM para site VMM secundário usando a recuperação de Site do Azure.

O artigo inclui os pré-requisitos, mostra como configurar um cofre de recuperação do Site, instalar o provedor de recuperação de Site do Azure em origem VMM servidores de destino, registrar os servidores no cofre, definir configurações de proteção para nuvens do VMM e, em seguida, ative a proteção VMs Hyper-V. Concluir a configuração testando o failover para garantir que tudo está funcionando conforme esperado.

Poste quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="architecture"></a>Arquitetura

A imagem abaixo mostra os canais de comunicação diferentes e as portas usadas pelo recuperação de Site do Azure para coordenação e replicação

![Topologia de E2E](./media/site-recovery-vmm-to-vmm-classic/e2e-topology.png)

## <a name="before-you-start"></a>Antes de começar

Verifique se que você tem estes pré-requisitos no lugar:

**Pré-requisitos** | **Detalhes**
--- | ---
**Azure**| Você precisa de uma conta do [Microsoft Azure](https://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação do Site.
**VMM** | Você precisa de pelo menos um servidor VMM.<br/><br/>O servidor VMM deve estar executando pelo menos System Center 2012 SP1 com as atualizações cumulativas mais recentes.<br/><br/>Se você deseja configurar proteção com um único servidor VMM, é necessário pelo menos duas nuvens configurados no servidor.<br/><br/>Se você quiser implantar proteção com dois servidores VMM, cada servidor deve ter pelo menos uma nuvem configurado no servidor VMM principal que você deseja proteger e uma nuvem configurado no servidor VMM secundário que você deseja usar para recuperação e proteção<br/><br/>Todas as nuvens VMM devem ter o perfil de capacidade de Hyper-V definida.<br/><br/>A nuvem de fonte que você deseja proteger deve conter um ou mais grupos de host VMM.<br/><br/>Saiba mais sobre como configurar nuvens do VMM no [passo a passo: Criando nuvens privadas com System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) no blog de Keith Mayer.
**Hyper-V** | Você precisa de um ou mais servidores de host Hyper-V nos grupos primárias e secundários VMM host e uma ou mais máquinas virtuais em cada servidor de host Hyper-V.<br/><br/>Os servidores de Hyper-V host e de destino devem estar executando pelo menos o Windows Server 2012 com a função Hyper-V e instalaram as últimas atualizações.<br/><br/>Qualquer servidor Hyper-V contendo VMs que você deseja proteger deve estar localizada em uma nuvem VMM.<br/><br/>Se você estiver executando o Hyper-V em um cluster, observe que corretor de cluster não é criada automaticamente se você tiver um cluster de baseado no endereço IP estático. Você precisa configurar o corretor de cluster manualmente. [Saiba mais](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) em entrada de blog do Anibal Finn.
**Mapeamento de rede** | Você pode configurar mapeamento de rede para certificar-se de que replicado máquinas virtuais ideal são colocadas em servidores de host Hyper-V secundários após failover e que eles podem se conectar a redes de máquina virtual apropriados. Se você não configurar mapeamento de rede, réplica VMs não estiver conectada a qualquer rede após failover.<br/><br/>Para configurar mapeamento de rede durante a implantação, certifique-se de que as máquinas virtuais no servidor de host de origem Hyper-V estão conectadas a uma rede VMM VM. Rede deve ser vinculada a uma rede lógica que está associada com a nuvem. < br /<br/>A nuvem de destino no servidor VMM secundário que você usa para recuperação deve ter uma rede de máquina virtual correspondente configurada e ele alternadamente deve ser vinculado a uma rede lógica correspondente que está associada com a nuvem de destino.<br/><br/>[Saiba mais](site-recovery-network-mapping.md) sobre o mapeamento de rede.
**Mapeamento de armazenamento** | Por padrão quando você replica uma máquina virtual em um servidor de host de Hyper-V de origem para um servidor de host de Hyper-V de destino, dados replicados são armazenados no local padrão que é indicado para o host do Hyper-V de destino no Gerenciador de Hyper-V. Para ter mais controle sobre onde os dados replicados estão armazenados, você pode configurar mapeamento de armazenamento<br/><br/> Para configurar mapeamento de armazenamento, você precisa configurar classificações de armazenamento na origem e destino servidores VMM antes de começar a implantação. [Saiba mais](site-recovery-storage-mapping.md).


## <a name="step-1-create-a-site-recovery-vault"></a>Etapa 1: Criar um cofre de recuperação do Site

1. Entrar no [Portal de gerenciamento](https://portal.azure.com) do servidor VMM que você deseja registrar.

2. Expanda **Serviços de dados** > **Serviços de recuperação** e clique em **Cofre de recuperação do Site**.

3. Clique em **Criar novo** > **criação rápida**.

4. Em **nome**, digite um nome amigável para identificar o cofre.

5. Na **região** , selecione a região geográfica para o cofre. Para verificar a regiões com suporte consulte disponibilidade geográficos em [Detalhes de preços de recuperação do Azure Site](http://go.microsoft.com/fwlink/?LinkId=389880).

6. Clique em **criar cofre**.

    ![Criar cofre](./media/site-recovery-vmm-to-vmm-classic/create-vault.png)

Na barra de status, verifique se o cofre foi criado. O cofre será listado como **ativo** na página de serviços de recuperação de principal.

## <a name="step-2-generate-a-vault-registration-key"></a>Etapa 2: Gerar uma chave de registro do cofre

Gere uma chave de registro no cofre. Depois de baixar o provedor de recuperação de Site do Azure e instalá-lo no servidor VMM, você usará esta tecla para registrar o servidor VMM no cofre.

1. Na página **Serviços de recuperação** , clique no cofre para abrir a página de início rápido. Início rápido também pode ser aberto a qualquer momento usando o ícone.

    ![Ícone de início rápido](./media/site-recovery-vmm-to-vmm-classic/quick-start-icon.png)

2. Na lista suspensa, selecione **entre dois sites VMM local**.
3. Em **Preparar VMM servidores**, clique em **arquivo de chave de registro de gerar**. O arquivo de chave é gerado automaticamente e é válido por 5 dias depois que ela é gerada. Se você não estiver acessando o portal do Azure do servidor VMM você precisa copiar este arquivo no servidor.

    ![Chave do registro](./media/site-recovery-vmm-to-vmm-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>Etapa 3: Instalar o provedor de recuperação de Site Azure

4. Na página do **Início rápido** , em **servidores VMM preparar**, clique em **Baixar o provedor do recuperação do Microsoft Azure Site para instalação nos servidores VMM** para obter a versão mais recente do arquivo de instalação do provedor.

2. Execute esse arquivo no servidor VMM de origem.

    >[AZURE.NOTE] Se VMM é implantado em um cluster e você estiver instalando o provedor para a primeira vez instalá-lo em um nó ativo e concluir a instalação para registrar o servidor VMM no cofre. Em seguida, instale o provedor nos outros nós. Observe que, se você estiver atualizando o provedor de que você precisa atualizar em todos os nós porque eles devem todos estar executando a mesma versão do provedor.

3. O instalador faz algumas **Verificação de pré-requisitos** e solicitações de permissão para interromper o serviço VMM para iniciar a instalação do provedor. O serviço de VMM será reiniciado automaticamente quando a instalação terminar. Se você estiver instalando em um cluster VMM que você será solicitado a interromper a função de Cluster.

4. No **Microsoft Update** , você pode optar em atualizações. Com essa configuração habilitada provedor serão instaladas de acordo com a sua política do Microsoft Update.

    ![Atualizações da Microsoft](./media/site-recovery-vmm-to-vmm-classic/ms-update.png)

5. O local de instalação está definido como ** <SystemDrive>\Program Files\Microsoft System Center 2012 R2\Virtual máquina Manager\bin**. Clique no botão Instalar para iniciar a instalação do provedor.

    ![InstallLocation](./media/site-recovery-vmm-to-vmm-classic/install-location.png)

6. Após a instalação do provedor de clique em **registrar** para registrar o servidor no cofre.

    ![InstallComplete](./media/site-recovery-vmm-to-vmm-classic/install-complete.png)
9. Em **nome do cofre**, verifique o nome do cofre em que o servidor será registrado. Clique em *Avançar*.

    ![Registro do servidor](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)

7. Na **Conexão de Internet** , especifique como o provedor executando no servidor VMM se conecta à Internet. Selecione **conectar-se com as configurações de proxy existente** para usar as configurações de conexão de Internet padrão configuradas no servidor.

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

13.  Clique em **Avançar** para concluir o processo. Depois do registro, metadados do servidor VMM é recuperado por recuperação de Site do Azure. O servidor é exibido no **VMM servidores** > **servidores** no cofre.

    ![Servidores](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)

### <a name="command-line-installation"></a>Instalação de linha de comando

O provedor de recuperação de Site do Azure também pode ser instalado na linha de comando. Este método pode ser usado para instalar o provedor em um núcleo de servidor para o Windows Server 2012 R2.

1. Baixe a chave de registro e arquivo de instalação do provedor para uma pasta. Por exemplo C:\ASR.
2. Interromper o serviço de Gerenciador de máquina Virtual do System Center
3. Extrai o instalador do provedor executando esses comandos em um prompt de comando com privilégios de **administrador** :

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Instale o provedor executando:

        C:\ASR> setupdr.exe /i

5. Registre o provedor executando:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

Onde os parâmetros são:

 - **/Credentials**: parâmetro obrigatório que especifica o local em que se encontra o arquivo de chave do registro  
 - **/FriendlyName**: parâmetro obrigatório para o nome do servidor host Hyper-V que aparece no portal do Azure recuperação do Site.
 - **/EncryptionEnabled**: parâmetros opcionais que você precisa usar apenas no VMM Azure cenário, se você precisar de criptografia de suas máquinas virtuais em inativos no Azure. Verifique se o nome do arquivo fornecido tem uma extensão **. pfx** .
 - **/proxyAddress**: parâmetro opcional que especifica o endereço do servidor proxy.
 - **/proxyPort**: parâmetro opcional que especifica a porta do servidor proxy.
 - **/proxyUsername**: parâmetro opcional que especifica o nome de usuário do Proxy (se o proxy exige autenticação).
 - **/proxyPassword**: parâmetro opcional que especifica a senha para autenticação com o servidor proxy (se o proxy exige autenticação).  

## <a name="step-4-configure-cloud-protection-settings"></a>Etapa 4: Configure nuvem as configurações de proteção

Depois de servidores VMM são registrados, você pode configurar as configurações de proteção de nuvem. Se você habilitou a opção **sincronizar dados de nuvem com o cofre** quando você instalou o provedor para que todas as nuvens no servidor VMM aparecerão na guia **Itens protegidos** no cofre. Se você não tiver que você pode sincronizar uma nuvem específica com recuperação de Site do Azure na guia **Geral** da página de propriedades de nuvem de console do VMM.

![Nuvem publicado](./media/site-recovery-vmm-to-vmm-classic/clouds-list.png)

1. Na página de início rápido, clique em **Configurar proteção para nuvens do VMM**.
2. Na guia **VMM nuvens** , selecione a nuvem que você deseja configurar e vá para a guia de **configuração** .
3. Em **destino**, selecione **VMM**.
4. No **local de destino**, selecione o servidor VMM no local que gerencia a nuvem que você deseja usar para recuperação.
4. Na **nuvem de destino**, selecione a nuvem de destino que você deseja usar recursos de máquinas virtuais na nuvem fonte. Observe que:

    - Recomendamos que você selecione uma nuvem de destino que atende aos requisitos de recuperação para as máquinas virtuais que você vai proteger.
    - Uma nuvem só pode pertencer a um par de nuvem único — como um primário ou uma nuvem de destino.

5. Em **Copiar frequência**, especificar a frequência dados devem ser sincronizados entre os locais de origem e destino 5he. Observe que esta configuração só é relevante quando o host do Hyper-V estiver executando o Windows Server 2012 R2. Para outros servidores de uma configuração padrão de cinco minutos é usada.
6. Em **pontos de recuperação adicionais**, especifique se deseja criar pontos de recuperação adicionais. O valor padrão zero indica que somente o ponto de recuperação de mais recente para uma máquina virtual primária é armazenado em um servidor de host de réplica. Observe que a habilitação de vários pontos de recuperação requer armazenamento adicional para os instantâneos que estão armazenados em cada ponto de recuperação. Por padrão, os pontos de recuperação são criados a cada hora, para que cada ponto de recuperação contém o valor de uma hora de dados. O valor de ponto de recuperação que você atribui para a máquina virtual no console do VMM não deve ser menor que o valor que você atribui no console de recuperação de Site do Azure.
7. Em **frequência de instantâneos consistentes com o aplicativo**, especifique a frequência de criar instantâneos consistentes com o aplicativo. Hyper-V usa dois tipos de instantâneos — um instantâneo padrão que fornece um instantâneo incremental da máquina virtual inteiro e um instantâneo consistente com o aplicativo que usa um instantâneo point-in-time dos dados do aplicativo na máquina virtual. Instantâneos consistentes com o aplicativo usam o serviço de cópia de sombra de Volume (VSS) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo é feito. Observe que, se você habilitar instantâneos consistentes com o aplicativo, ele afetará o desempenho dos aplicativos executados em máquinas virtuais de origem. Certifique-se de que o valor definido é menor que o número de pontos de recuperação adicional que você configura.

    ![Definir configurações de proteção](./media/site-recovery-vmm-to-vmm-classic/cloud-settings.png)

8. Em **compactação de transferência de dados**, especifique se os dados replicados que são transferidos devem ser compactados.
9. Em **autenticação**, especifique como o tráfego é autenticado entre os servidores primário e recuperação de host Hyper-V. Selecione HTTPS, a menos que tenha um ambiente de Kerberos configurado em funcionamento. Recuperação de Site Azure configurará automaticamente certificados para autenticação HTTPS. Nenhuma configuração manual é necessária. Se você selecionar Kerberos, um tíquete Kerberos será usado para autenticação comum de servidores de host. Por padrão, porta 8083 e 8084 (para certificados) será aberta no Firewall do Windows nos servidores de host Hyper-V. Observe que esta configuração só é relevante para os servidores de host Hyper-V em execução no Windows Server 2012 R2.
10. Em **porta**, modifique o número da porta que os computadores de host de origem e destino ouvir tráfego de replicação. Por exemplo, você pode modificar a configuração se você quiser aplicar a largura de banda de rede de qualidade do serviço (QoS) a otimização de tráfego de replicação. Verifique se a porta não é usada por outro aplicativo e que é aberto nas configurações do firewall.
11. **Método de replicação**, especifique como a replicação inicial dos dados de origem para locais de destino será tratada, antes de replicação normal é iniciado:

    - **Rede**— copiando dados através da rede pode ser demorada e recursos. Recomendamos que você use essa opção se a nuvem contém máquinas virtuais com relativamente pequenos discos virtuais e se o local principal estiver conectado ao site secundário ao longo de uma conexão com largura de banda larga. Você pode especificar que a cópia deve iniciar imediatamente ou selecione uma hora. Se você usar replicação de rede, recomendamos que você agende horários de pico.
    - **Offline**— esse método Especifica que a replicação inicial será executada usando mídia externa. É útil se você quiser evitar a degradação no desempenho da rede ou para locais geograficamente remotos. Para usar esse método você especificar o local de exportação em nuvem fonte e o local de importação na nuvem destino. Quando você ativa a proteção para uma máquina virtual, o disco rígido virtual é copiado para o local de exportação especificado. Você enviá-lo ao site de destino e copiá-lo para o local de importação. O sistema copia as informações importadas para máquinas virtuais réplica.

12. Selecione **Excluir réplica Virtual Machine** para especificar que a máquina virtual de réplica deve ser excluída se você parar protegendo a máquina virtual selecionando a opção **Excluir proteção para a máquina virtual** na guia máquinas virtuais das propriedades nuvem. Com essa configuração habilitada, quando você desativar a proteção da máquina virtual é removida de recuperação de Site do Azure, as configurações de recuperação de Site para a máquina virtual são removidas no console do VMM e a réplica é excluída.

    ![Definir configurações de proteção](./media/site-recovery-vmm-to-vmm-classic/cloud-settings-replica.png)

Depois de salvar as configurações de um trabalho será criado e é possível monitorar na guia **trabalhos** . Todos os servidores de host Hyper-V na nuvem VMM fonte serão configurados para replicação. Configurações de nuvem podem ser modificadas na guia **Configurar** . Se você quiser modificar a nuvem local ou de destino de destino que você deve remover a configuração de nuvem e reconfigure na nuvem.

### <a name="prepare-for-offline-initial-replication"></a>Preparar para replicação inicial offline

Você precisará fazer as seguintes ações para se preparar para replicação inicial offline:

- No servidor de origem, você deverá especificar um local de caminho do qual será realizada a exportação de dados. Atribua controle total para permissões de compartilhamento e NTFS ao serviço VMM no caminho de exportação. No servidor de destino, você deverá especificar um local de caminho da qual ocorrerá a importação de dados. Atribua as mesmas permissões neste caminho de importação.
- Se o caminho de importação ou exportação for compartilhado, atribua as associações de grupo administrador, usuário avançado, o operador de impressão ou operador de servidor para a conta de serviço VMM no computador remoto no qual compartilhado está localizado.
- Se você estiver usando qualquer contas executar como adicionar hosts, sobre os caminhos de importação e exportação, atribuir leitura e permissões de gravação para as contas executar como no VMM.
- Os compartilhamentos de importação e exportação não devem estar localizados em qualquer computador usado como um servidor de host Hyper-V, pois a configuração de autoretorno não é suportada pelo Hyper-V.
- No Active Directory, em cada Hyper-V server host que contém máquinas virtuais que você deseja proteger, habilitar e configurar delegação restrita confiar em computadores remotos no qual os caminhos de importação e exportação estão localizados, da seguinte maneira:
    1. No controlador de domínio, abra o **Active Directory Users e computadores**.
    2. Na árvore do console clique **nome_do_domínio** > **computadores**.
    3. Clique com botão direito no nome do servidor de host Hyper-V > **Propriedades**.
    4. Na guia **delegação** clique T**rust computador para delegação apenas aos serviços especificados**.
    5. Clique em **usar qualquer protocolo de autenticação**.
    6. Clique em **Adicionar** > **usuários e computadores**.
    7. Digite o nome do computador que hospeda o caminho de exportação > **Okey**. Na lista de serviços disponíveis, mantenha pressionada a tecla CTRL e clique em **cifs** > **Okey**. Repita para o nome do computador que hospeda o caminho de importação. Repita conforme necessário para os servidores de host Hyper-V adicionais.

## <a name="step-5-configure-network-mapping"></a>Etapa 5: Configurar mapeamento de rede
1. Na página de início rápido, clique em **Mapear redes**.
2. Selecione o servidor do VMM de origem do qual você deseja mapear redes e o servidor do VMM de destino para a qual as redes serão mapeadas. Na lista de redes de origem e seu destino associado são exibidas. Um valor em branco é mostrado para redes que não estão mapeados.
3. Selecione uma rede na **rede na fonte** > **mapa**. O serviço detecta as redes de máquina virtual no servidor de destino e exibe-os. Clique no ícone de informações ao lado dos nomes de rede de origem e destino para exibir as sub-redes para cada rede.

    ![Configurar mapeamento de rede](./media/site-recovery-vmm-to-vmm-classic/network-mapping1.png)

4. Na caixa de diálogo Selecione uma das redes de máquina virtual do servidor VMM de destino.

    ![Selecione uma rede de destino](./media/site-recovery-vmm-to-vmm-classic/network-mapping2.png)

5. Quando você selecionar uma rede de destino, as nuvens protegidas que usam a rede de origem são exibidas. Redes de destino disponível que são associadas com as nuvens usadas para proteção também são exibidos. Recomendamos que você selecionar uma rede de destino que está disponível para todas as nuvens que você está usando para proteção. Ou, você também pode ir para o servidor do VMM e modificar as propriedades de nuvem para adicionar a rede lógica correspondente à rede máquina virtual que você deseja escolher.
6. Clique na marca de seleção para concluir o processo de mapeamento. Um trabalho é iniciado acompanhar o andamento de mapeamento. Você pode exibi-la na guia **trabalhos** .


## <a name="step-6-configure-storage-mapping"></a>Etapa 6: Configurar mapeamento de armazenamento
Por padrão quando você replica uma máquina virtual em um servidor de host de Hyper-V de origem para um servidor de host de Hyper-V de destino, dados replicados são armazenados no local padrão que é indicado para o host do Hyper-V de destino no Gerenciador de Hyper-V. Para ter mais controle sobre onde os dados replicados estão armazenados, você pode configurar mapeamentos de armazenamento da seguinte maneira:



1. Defina classificações de armazenamento em servidores VMM de origem e de destino. [Saiba mais](https://technet.microsoft.com/library/gg610685.aspx). Classificações devem estar disponíveis para os servidores de host Hyper-V em nuvens de origem e destino. Classificações não precisam ter o mesmo tipo de armazenamento. Por exemplo, você pode mapear uma classificação de origem que contém compartilhamentos SMB para uma classificação de destino que contém CSVs.
2. Depois de classificações estão no lugar, você pode criar mapeamentos. Para fazer isso, na página de **Início rápido** > **Mapear armazenamento**.
3. Clique na guia **armazenamento** > **Mapear classificações de armazenamento**.
4. Na guia **Mapear classificações de armazenamento** , selecione classificações na origem e VMM servidores de destino. Salve suas configurações.

    ![Selecione uma rede de destino](./media/site-recovery-vmm-to-vmm-classic/storage-mapping.png)


## <a name="step-7-enable-virtual-machine-protection"></a>Etapa 7: Habilitar proteção de máquina virtual
Depois de servidores, nuvens e redes estão configurados corretamente, você pode habilitar a proteção para máquinas virtuais na nuvem.

1. Na guia **máquinas virtuais** na nuvem em que a máquina virtual está localizada, clique em **Habilitar proteção** > **Adicionar máquinas virtuais**.
2. Na lista de máquinas virtuais na nuvem, selecione aquele que você deseja proteger.

    ![Ativar a proteção de máquina virtual](./media/site-recovery-vmm-to-vmm-classic/enable-protection.png)

3. Controlar o andamento da ação Habilitar proteção na guia **trabalhos** , incluindo a replicação inicial. Após a execução do trabalho de proteção de finalizar a máquina virtual está pronta para failover. Depois de proteção está habilitada e máquinas virtuais são replicadas, você poderá visualizá-las no Azure.

    ![Trabalho de proteção de máquina virtual](./media/site-recovery-vmm-to-vmm-classic/vm-jobs.png)

>[AZURE.NOTE] Você também pode habilitar proteção para máquinas virtuais no console do VMM. Clique em **Habilitar proteção** na barra de ferramentas na guia de **Recuperação de Site do Azure** nas propriedades de máquina virtual.

### <a name="on-board-existing-virtual-machines"></a>Integrado máquinas virtuais existentes

Se você tiver máquinas virtuais existentes no VMM que são duplicar com Hyper-V Replica você precisará integrado-los para proteção de recuperação de Site do Azure da seguinte maneira:

1. Verifique se que você tem nuvens primárias e secundários. Certifique-se de que o servidor de Hyper-V que hospeda a máquina virtual existente estiver localizado na nuvem primária e que o servidor de Hyper-V que hospeda a máquina virtual de réplica estiver localizado na nuvem secundária. Verifique se você configurou as configurações de proteção para as nuvens. As configurações devem corresponder àquelas atualmente configurado para Hyper-V Replica. Caso contrário, replicação de máquina virtual pode não funcionar conforme o esperado.
2. Ative a proteção para a máquina virtual primária. Azure recuperação de Site e VMM garantirá que o mesmo host de réplica e máquina virtual for detectado, e a recuperação de Site Azure será reutilizar e restabelecer replicação usando as configurações definidas durante a configuração de nuvem.


## <a name="test-your-deployment"></a>Testar sua implantação

Para testar sua implantação, você pode executar um failover de teste para uma única máquina virtual, ou criar um plano de recuperação consiste em várias máquinas virtuais e executar um failover de teste para o plano.  Failover de teste simula o mecanismo de failover e recuperação em uma rede isolada.

### <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

1. Na guia **Planos de recuperação** , clique em **Criar plano de recuperação**.
2. Especifique um nome para o plano de recuperação e servidores VMM de origem e destino. O servidor de origem deve ter máquinas virtuais que estão habilitadas para failover e recuperação. Selecione **Hyper-V** para exibir apenas as nuvens que estão configuradas para replicação de Hyper-V.

    ![Criar plano de recuperação](./media/site-recovery-vmm-to-vmm-classic/recovery-plan1.png)

3. Em **Selecionar Máquina Virtual**, selecione grupos de replicação. Todas as máquinas virtuais associadas ao grupo de replicação serão selecionadas e adicionadas ao plano de recuperação. Essas máquinas virtuais são adicionadas ao grupo de padrão do plano de recuperação — grupo 1. Se necessário, você pode adicionar mais grupos. Observe que após máquinas virtuais de replicação iniciará de acordo com a ordem dos grupos de plano de recuperação.

    ![Adicionar máquinas virtuais](./media/site-recovery-vmm-to-vmm-classic/recovery-plan2.png)

Após a recuperação do plano foi criado, ele aparece na lista na guia **Planos de recuperação** .

###<a name="run-a-test-failover"></a>Executar um failover de teste

1. Na guia **Planos de recuperação** , selecione o plano e clique em **Testar Failover**.
2. Na página **Confirmar Failover de teste** , selecione **Nenhum**. Observe que, com essa opção, ativados com falha máquinas virtuais de réplica não estiver conectado a qualquer rede. Isso fará testado que a máquina virtual falhar sobre conforme esperado, mas não testar seu ambiente de rede de replicação. Examine como [executar um failover de teste](site-recovery-failover.md#run-a-test-failover) para obter mais detalhes sobre como usar as opções de rede diferentes.
3. Na máquina virtual de teste será criada no mesmo host como o host em que a máquina virtual de réplica exista. Ele é adicionado à nuvem mesma na qual máquina virtual réplica está localizada.

### <a name="run-a-recovery-plan"></a>Executar um plano de recuperação
Após a replicação da máquina virtual de réplica talvez não tenha um endereço IP que é a mesma que o endereço IP da máquina virtual primária. Máquinas virtuais atualizará o servidor DNS que elas estiverem usando após iniciarem. Você também pode adicionar um script para atualizar o servidor DNS para garantir uma atualização em tempo hábil.

#### <a name="script-to-retrieve-the-ip-address"></a>Script para recuperar o endereço IP
Execute esse script de amostra para recuperar o endereço IP.

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### <a name="script-to-update-dns"></a>Script para atualizar o DNS
Execute esse script de amostra para atualizar o DNS, especificando o endereço IP que você recuperou usando o script de exemplo anterior.

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="privacy-information-for-site-recovery"></a>Informações de privacidade para recuperação de Site

Esta seção fornece informações adicionais sobre privacidade para o recuperação de Site do Microsoft Azure serviço (""). Para exibir a declaração de privacidade do Microsoft Azure services, consulte a [Declaração de privacidade do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899)

**Recurso: registro**

- **O que ela faz**: registra o servidor com o serviço de forma que máquinas virtuais podem ser protegidas
- **Informações coletadas**: depois de registrar o serviço coleta, processa e transmitir informações de certificado de gerenciamento do servidor VMM que designou para fornecer recuperação de dados usando o nome do serviço do servidor VMM e o nome da máquina virtual nuvens no seu servidor VMM.
- **Uso das informações**:
    - Certificado de gerenciamento — isso é usado para ajudar a identificar e autenticar o servidor VMM registrado para acessar o serviço. O serviço usa a parte de chave pública do certificado para proteger um token que apenas o servidor VMM registrado pode acessar. O servidor precisa usar esse token para acessar os recursos de serviço.
    - Nome do servidor VMM — o nome do servidor o VMM é necessário para identificar e se comunicar com o servidor VMM apropriado no qual as nuvens estão localizadas.
    - Nuvem de nomes do servidor VMM — é necessário o nome de nuvem usando a nuvem de serviço emparelhamento/unpairing recurso descrito abaixo. Quando você decide emparelhar seu nuvem de um data center principal com outro nuvem no Centro de recuperação de dados, os nomes de todas as nuvens desde o data center de recuperação são apresentados.

- **Escolha**: essas informações são uma parte essencial do processo de registro de serviço, pois ela ajuda a você e o serviço para identificar o servidor VMM para o qual você deseja fornecer proteção de recuperação de Site do Azure, bem como para identificar o servidor VMM registrado correto. Se você não quiser enviar essas informações para o serviço, não use esse serviço. Se você registra seu servidor e, em seguida, mais tarde deseja cancelar o registro-lo, você poderá fazer isso excluindo as informações do servidor VMM a partir do portal de serviço (que é o portal do Azure).

**Recurso: Habilitar a proteção de recuperação de Site do Azure**

- **O que ela faz**: O provedor de recuperação do Site Azure instalados no servidor VMM é o canal de comunicação com o serviço. O provedor é uma biblioteca de vínculo dinâmico (DLL) hospedada no processo VMM. Depois de instalar o provedor, o recurso de "Recuperação de data center" obtém habilitado no console de administrador do VMM. Qualquer máquinas virtuais novas ou existentes em uma nuvem pode habilitar uma propriedade chamada "Data Center recuperação" para ajudar a proteger a máquina virtual. Quando essa propriedade estiver definida, o provedor envia o nome e ID da máquina virtual para o serviço. A proteção virtual está habilitada por tecnologia de replicação do Windows Server 2012 ou Windows Server 2012 R2 Hyper-V. Os dados de máquina virtual são replicados de um host Hyper-V para outro (normalmente localizado em um data center "recuperação" diferentes).

- **Informações coletadas**: O serviço coleta, processa e transmitem metadados da máquina virtual, que inclui o nome, ID, rede virtual e o nome da nuvem à qual ela pertence.

- **Uso das informações**: O serviço usa as informações acima para preencher as informações de máquina virtual em seu portal de serviço.

- **Escolha**: esta é uma parte essencial do serviço e não pode ser desativada. Se você não quiser que essas informações enviadas para o serviço, não habilite proteção de recuperação de Site do Azure para qualquer máquinas virtuais. Observe que todos os dados enviados pelo provedor de serviço é enviada por HTTPS.

**Recurso: Plano de recuperação**

- **O que ela faz**: este recurso ajuda você a criar um plano de coordenação para o Centro de dados de "recuperação". Você pode definir a ordem na qual as máquinas virtuais ou um grupo de máquinas virtuais deve ser iniciado no local de recuperação. Você também pode especificar qualquer scripts automatizados para ser executada ou manual qualquer ação a ser executada, no momento da recuperação para cada máquina virtual. Failover (abordado na próxima seção) geralmente é disparada no nível do plano de recuperação para recuperação coordenado.

- **Informações coletadas**: O serviço coleta, processa e transmitem metadados para o plano de recuperação, incluindo metadados de máquina virtual e de quaisquer scripts de automação e anotações de ação manual.

- **Uso das informações**: os metadados descrito acima é usado para criar o plano de recuperação em seu portal de serviço.

- **Escolha**: esta é uma parte essencial do serviço e não pode ser desativada. Se você não quiser que essas informações enviadas para o serviço, não crie planos de recuperação nesse serviço.

**Recurso: Mapeamento de rede**

- **O que ela faz**: esse recurso permite mapear informações de rede do data center principal para o Centro de recuperação de dados. Quando as máquinas virtuais são recuperadas no site de recuperação, esse mapeamento ajuda para estabelecer a conectividade de rede para eles.

- **Informações coletadas**: como parte do recurso de mapeamento de rede, o serviço coleta, processa e transmitem os metadados das redes lógicas para cada site (primário e Data Center).

- **Uso das informações**: O serviço usa os metadados para preencher seu portal de serviço, onde você poderá mapear as informações de rede.

- **Escolha**: esta é uma parte essencial do serviço e não pode ser desativada. Se você não quiser que essas informações enviadas para o serviço, não use o recurso de mapeamento de rede.

**Recurso: Failover - planejada, não planejada, teste**

- **O que ela faz**: esse recurso ajuda failover de uma máquina virtual de um centro de dados gerenciados VMM para outro VMM gerenciado data center. A ação de failover é acionada pelo usuário no seu portal de serviço. Razões possíveis para um failover incluem um evento não planejado (por exemplo, no caso uma disaster0 natural; um evento planejado (por exemplo data center de balanceamento de carga); um failover de teste (por exemplo, um ensaio de plano de recuperação).

O provedor do servidor VMM será notificado do evento do serviço e executa uma ação de failover no host Hyper-V por meio de interfaces VMM. Failover real da máquina virtual de um host Hyper-V para outro (normalmente executando em um data center diferente "recuperação") é tratado pela tecnologia de replicação do Windows Server 2012 ou Windows Server 2012 R2 Hyper-V. Após o failover estiver concluído, o provedor instalado no servidor VMM do data center "recuperação" envia as informações de sucesso para o serviço.

- **Informações coletadas**: O serviço usa as informações acima para preencher o status das informações de ação de failover em seu portal de serviço.

- **Uso das informações**: O serviço usa as informações acima da seguinte maneira:

    - Certificado de gerenciamento — isso é usado para ajudar a identificar e autenticar o servidor VMM registrado para acessar o serviço. O serviço usa a parte de chave pública do certificado para proteger um token que apenas o servidor VMM registrado pode acessar. O servidor precisa usar esse token para acessar os recursos de serviço.
    - Nome do servidor VMM — o nome do servidor o VMM é necessário para identificar e se comunicar com o servidor VMM apropriado no qual as nuvens estão localizadas.
    - Nuvem de nomes do servidor VMM — é necessário o nome de nuvem usando a nuvem de serviço emparelhamento/unpairing recurso descrito abaixo. Quando você decide emparelhar seu nuvem de um data center principal com outro nuvem no Centro de recuperação de dados, os nomes de todas as nuvens desde o data center de recuperação são apresentados.

- **Escolha**: esta é uma parte essencial do serviço e não pode ser desativada. Se você não quiser que essas informações enviadas para o serviço, não use esse serviço.

## <a name="next-steps"></a>Próximas etapas

Depois que você já tiver executado um failover de teste para verificar que seu ambiente está funcionando conforme esperado, [Saiba mais sobre](site-recovery-failover.md) os tipos diferentes de failovers.
