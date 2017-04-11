<properties 
   pageTitle="Falha volta máquinas virtuais de VMware e servidores físicos para o site local | Microsoft Azure"
   description="Saiba mais sobre falha volta para o site local após failover de VMs VMware e servidores físicos no Azure." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="08/22/2016"
   ms.author="ruturajd"/>

# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>Falhas back máquinas virtuais VMware a e servidores físicos para o site local

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-failback-azure-to-vmware.md)
- [Azure Portal clássico](site-recovery-failback-azure-to-vmware-classic.md)
- [Portal Azure clássico (herdado)](site-recovery-failback-azure-to-vmware-classic-legacy.md)



Este artigo descreve como falha volta Azure máquinas virtuais do Azure para o site local. Siga as instruções neste artigo quando você estiver pronto para falhar novamente suas máquinas virtuais de VMware ou Windows/Linux servidores físicos depois que eles falhou ao longo do local do site para o Azure usando este [tutorial](site-recovery-vmware-to-azure-classic.md).



## <a name="overview"></a>Visão geral

Este diagrama mostra a arquitetura de failback para esse cenário.

Use essa arquitetura quando o servidor de processo é local e você estiver usando uma rota expressa.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Use essa arquitetura quando o servidor de processo está no Azure e tiver uma VPN ou uma conexão de rota expressa.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Para ver a lista completa de portas e o failback diagrama de arquitetura consulte a imagem abaixo

![](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Aqui está como failback funciona:

- Depois que você já não conseguiu ao longo do Azure, você não volta para seu site local em poucas etapas:
    - **Estágio 1**: você reprotect VMs Azure para que eles iniciarem replicação de volta para VMs VMware executado em seu site local. Habilitar reprotection move a máquina virtual para um grupo de proteção de failback que foi criado automaticamente quando você criou o grupo de proteção de failover. Se você adicionou seu grupo de proteção de failover para um plano de recuperação, em seguida, o grupo de proteção de failback foi adicionado automaticamente ao plano.  Durante reprotect você especifica como planejar falha novamente.
    - **Etapa 2**: após suas VMs Azure são replicar ao seu site local, você executa uma falha falha volta do Azure.
    - **Etapa 3**: depois de seus dados falhou novamente, você reprotect VMs no local que você não conseguiu novamente, para que eles iniciarem replicação no Azure.

> [AZURE.VIDEO enhanced-vmware-to-azure-failback]


### <a name="failback-to-the-original-or-alternate-location"></a>Failback para o local original ou alternativo

Caso você tenha falhado ao longo de uma VM VMware você pode falhar voltar à mesma fonte de máquina virtual se ele ainda existe no local. Neste cenário apenas as alterações delta resultado serão a falha novamente. Observe que:

- Caso você tenha falhado sobre os servidores físicos depois failback é sempre uma nova VM VMware.
    - Antes de volta falhar uma máquina física, observe que
    - Máquina física protegida retornarão como máquina Virtual quando falhou volta do Azure para VMware
    - Certifique-se de que você a descobrir pelo menos um destino de mestre Server junto com os hosts ESX/ESXi necessários aos quais você precisa failback.
- Se você não voltar para a máquina virtual original é necessário o seguinte:
    - Se a máquina virtual é gerenciada por um servidor vCenter host ESX do destino mestre deve ter acesso ao armazenamento de dados VMs.
    - Se a máquina virtual estiver em um host ESX, mas não é gerenciada por vCenter no disco rígido da máquina virtual deve ser em um armazenamento de dados acessível pelo host do MT.
    - Se sua máquina virtual estiver em um host ESX e não use vCenter você deve concluir descoberta de host ESX de a MT antes de você reprotect. Isso se aplica se você estiver falhando servidores volta físicos muito.
    - Outra opção (se existir a máquina virtual local) é para excluí-la antes de fazer um failback. Em seguida, failback irá criar uma nova VM no mesmo host como o host ESX de destino mestre.
    
- Quando você failback para um local alternativo os dados será recuperado ao mesmo armazenamento de dados e o mesmo host ESX usado pelo servidor local mestre de destino. 


## <a name="prerequisites"></a>Pré-requisitos

- Você precisará de um ambiente VMware para falhar VMs VMware e servidores físicos de volta. Falha de volta para um servidor físico não é compatíveis.
- Para falhar novamente você deve criar uma rede Azure ao configurar inicialmente proteção. Failback precisa de uma conexão VPN ou rota expressa do Azure rede na qual as VMs Azure estão localizadas no site local.
- Se as VMs que você deseja falhar volta ao são gerenciadas por um servidor de vCenter será necessário para garantir que você tem as permissões necessárias para descoberta de VMs em servidores vCenter. [Leia mais](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
- Se instantâneos estão presentes em uma máquina virtual reprotection falhará. Você pode excluir os instantâneos ou discos. 
- Antes de você falha novamente, você precisará criar um número de componentes:
    - **Criar um servidor de processo no Azure**. Esta é uma máquina virtual do Azure que você precisará criar e manter durante o failback. Você pode excluir a máquina após a conclusão da failback.
    - **Criar um servidor de destino mestre**: O servidor de destino mestre envia e recebe dados de failback. O servidor de gerenciamento de que você criou no local tem um servidor de destino mestre instalado por padrão. No entanto, dependendo do volume de tráfego de volta falha talvez seja necessário criar um servidor de destino mestre separado para failback.
    - Se você quiser criar um servidor de destino mestre adicional em execução no Linux, você precisará configurar a VM Linux antes de instalar o servidor de destino mestre, conforme descrito abaixo.
- Servidor de configuração é necessária no local quando você fizer um failback. Durante failback, a máquina virtual devem constar o banco de dados de servidor de configuração, falhando quais failback não quer ser bem-sucedido. Portanto, garantindo que regulares backup agendado do seu servidor. No caso de um desastre, você precisará restaurá-lo com o mesmo endereço IP para que failback funcionem corretamente.

## <a name="set-up-the-process-server-in-azure"></a>Configurar o servidor de processo no Azure

Você precisa instalar um servidor de processo no Azure para que VMs Azure possa enviar os dados de volta ao servidor de destino mestre local. 

1.  No portal de recuperação do Site > selecionar **Servidores de configuração** para adicionar um novo servidor de processo.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps1.png)

2.  Especifique um nome de servidor de processo e insira um nome e senha que você usará para conectar-se para a máquina virtual de Azure como administrador. No **Servidor de configuração** selecione o servidor de gerenciamento do local e especificar o Azure rede na qual o servidor de processo deve ser implantado. Isso deve ser a rede na qual as VMs Azure estão localizadas. Especifique um endereço IP exclusivo da sub-rede select e iniciar a implantação.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps2.png)

    Um trabalho para implantar o servidor processo será acionado

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps3.png)

    Depois que o servidor processo é implantado no Azure é pode efetuar logon usando as credenciais que você especificou. Na primeira vez que você faça logon na caixa de diálogo de servidor processo será executado. Digite o endereço IP do servidor de gerenciamento de locais e sua senha. Deixe a configuração de porta 443 padrão. Você também pode deixar o padrão 9443 porta para replicação de dados, a menos que especificamente modificada esta configuração quando você configura o servidor de gerenciamento de local. 

    >[AZURE.NOTE] O servidor não ficarão visível em **Propriedades de máquina virtual**. Só é visível na guia **servidores** no servidor de gerenciamento ao qual ele está registrado. Ele pode levar cerca de 10 a 15 minutos para o servidor de processo apareça.


## <a name="set-up-the-master-target-server-on-premises"></a>Configurar o destino mestre server local

O servidor de destino mestre recebe os dados de failback. Um servidor de destino mestre é instalado automaticamente no servidor de gerenciamento de locais, mas se você estiver falhando volta muitos dados talvez seja necessário configurar um servidor de destino mestre adicional. Faça isso da seguinte maneira:

>[AZURE.NOTE] Se você quiser instalar um servidor de destino mestre no Linux, siga as instruções no próximo procedimento.

1. Se você estiver instalando o servidor de destino mestre no Windows, abra a página de início rápido da máquina virtual em que você estiver instalando o servidor de destino mestre e baixar o arquivo de instalação para o Assistente de configuração de Unificação de recuperação do Azure Site.
2. Execute a instalação e selecione **Adicionar servidores de processo adicional para dimensionar a implantação**no **antes de começar** .
3. Conclua o Assistente da mesma maneira que você fez quando você [Configurar o servidor de gerenciamento](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). Na página **Detalhes de configuração do servidor** , especifique o endereço IP do servidor este destino mestre e uma senha para acessar a máquina virtual.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>Configurar uma VM Linux como o servidor de destino mestre
Para configurar o servidor de gerenciamento executando o servidor de destino mestre como uma VM Linux será preciso instalar o centavos) S 6.6 mínimo sistema operacional, recuperar as IDs de SCSI cada disco SCSI, instalar alguns pacotes adicionais e aplicar algumas alterações personalizadas.

#### <a name="install-centos-66"></a>Instalar CentOS 6.6

1.  Instale o sistema operacional mínimo CentOS 6.6 em servidor de gerenciamento de máquina virtual. Manter o ISO em uma unidade de DVD e inicializar o sistema. Ignorar o teste de mídia, selecione inglês (EUA) no idioma, selecione **Dispositivos de armazenamento básica**, verificar que o disco rígido não tem quaisquer dados importantes e clique em **Sim**, descartar todos os dados. Insira o nome de host do servidor de gerenciamento e selecione o adaptador de rede do servidor.  Na caixa de diálogo **Editar sistema** selecione** conectar-se automaticamente** e adicione um endereço IP estático, rede e configurações de DNS. Especifica um fuso horário e uma senha de raiz para acessar o servidor de gerenciamento. 
2.  Quando perguntado o tipo de instalação que você gostaria selecione **Create Custom Layout** como a partição. Depois de clicar em **próxima** selecione **livre** e clique em criar. Criar **/**, **/var/crash** e **/ home partições** com **FS tipo:** **ext4**. Criar a partição de troca como **FS tipo: trocar**.
3.  Se forem encontrados dispositivos preexistentes que será exibida uma mensagem de aviso. Clique em **Formatar** para formatar a unidade com as configurações de partição. Clique em **gravar alteração disco** para aplicar as alterações de partição.
4.  Selecione **carregador de inicialização de instalação** > **Avançar** para instalar o carregador de inicialização na partição raiz.
5.  Quando a instalação estiver concluída clique em **Reiniciar**.


#### <a name="retrieve-the-scsi-ids"></a>Recuperar as IDs SCSI

1. Após a instalação recupere as IDs de SCSI para cada disco rígido SCSI a máquina virtual. Para fazer este desligar o servidor de gerenciamento de máquina virtual, nas propriedades da máquina virtual no VMware com o botão direito na entrada de máquina virtual > **Editar configurações** > **Opções**.
2. Selecione **Avançado** > **item geral** e clique em **Parâmetros de configuração**. Esta opção será de-active quando o computador está executando. Para torná-la ativa máquina deve ser desligada.
3. Se a linha **disco. EnableUUID** existe certificar-se de que o valor é definido como **True** (diferencia maiusculas de minúscula). Se ele já estiver você pode cancelar e testar o comando SCSI dentro de um sistema operacional convidado após a inicialização. 
4.  Se a linha não existente clique em **Adicionar linha** – e adicioná-la com o valor **True** . Não use aspas duplas.

#### <a name="install-additional-packages"></a>Instalar pacotes adicionais

Você precisará baixar e instalar alguns pacotes adicionais. 

1.  Verifique se que o servidor de destino mestre está conectado à internet.
2.  Executar este comando para baixar e instalar 15 pacotes do repositório CentOS: **# yum instalar – y xfsprogs perl lsscsi rsync wget kexec-ferramentas**.
3.  Se as máquinas de origem estiver protegendo estiver executando Linux co Reiser ou XFS sistema para o dispositivo raiz ou de inicialização, de arquivos, você deverá baixar e instalar pacotes adicionais da seguinte maneira:

    - # <a name="cd-usrlocal"></a>CD /usr/local
    - # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
    - # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
    - # <a name="rpm-ivh-kmod-reiserfs-00-1el6elrepox8664rpm-reiserfs-utils-3621-1el6elrepox8664rpm"></a>rpm-ivh kmod-reiserfs-0,0-1.el6.elrepo.x86_64.rpm reiserfs-utilitários-3.6.21-1.el6.elrepo.x86_64.rpm
    - # <a name="wget-httpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpmhttpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpm"></a>wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
    - # <a name="rpm-ivh-xfsprogs-311-16el6x8664rpm"></a>rpm-ivh xfsprogs-3.1.1-16.el6.x86_64.rpm

#### <a name="apply-custom-changes"></a>Aplicar alterações personalizadas

Siga este procedimento para aplicar alterações personalizadas depois que você concluir as etapas de pós-instalação e instalou os pacotes:

1.  Copie o agente de Unificação RHEL 6-64 binário para a máquina virtual. Executar este comando para descompacte o binário: **tar – zxvf <file name> **
2.  Executar este comando para conceder permissões: **# chmod 755./ApplyCustomChanges.sh**
3.  Executar o script: **#./ApplyCustomChanges.sh**. Você só deve executar o script de uma vez. Reinicie o servidor após o script é executado com êxito.


## <a name="run-the-failback"></a>Executar o failback

### <a name="reprotect-the-azure-vms"></a>Reprotect VMs Azure

1.  No portal de recuperação do Site > guia de **máquinas** , selecione a máquina virtual que foram falhou ao longo do e clique em **proteger novamente**.
2.  No **Servidor de destino de mestre** e **Servidor processo** , selecione o servidor de destino mestre do local e o servidor de processo de máquina virtual do Azure.
3.  Selecione a conta que você configurou para conexão com a máquina virtual.
4.  Selecione a versão de failback do grupo de proteção. Por exemplo, se a máquina virtual estiver protegida em PG1 e em seguida, você precisará selecionar PG1_Failback.
5.  Se você quiser recuperar para um local alternativo, selecione a unidade de retenção e armazenamento de dados configurado para o servidor de destino mestre. Quando você não volta para o site local VMs VMware no plano de proteção de failback usarão o mesmo armazenamento de dados como o servidor de destino mestre. Se você quiser recuperar a réplica máquina virtual do Azure para o mesmo local máquina virtual e a máquina virtual local já deve estar no mesmo armazenamento de dados como o servidor de destino mestre. Se não houver nenhuma máquina virtual local será criado um novo durante reprotection.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/failback1.png)

6.  Depois de clicar em **Okey** para começar reprotection um trabalho começa replicar a máquina virtual do Azure para o site local. Você pode controlar o andamento na guia **trabalhos** .

    ![](./media/site-recovery-failback-azure-to-vmware-classic/failback2.png)

### <a name="run-a-failover-to-the-on-premises-site"></a>Executar um failover para o site local

Após reprotection a máquina virtual é movida para a versão de failback do seu grupo de proteção e é adicionada automaticamente ao plano de recuperação que você usou para o failover Azure se houver uma.

1.  Na página de **Planos de recuperação** selecione o plano de recuperação que contém o grupo de failback e clique em **Failover** > **Failover não planejado**.
2.  Em **Confirmar Failover** , verifique se a direção de failover (ao Azure) e selecione o ponto de recuperação que você deseja usar para o failover (mais recente). Se você ativou **Multi-máquina virtual** quando você tiver configurado Propriedades de replicação, você pode recuperar para o aplicativo mais recente ou o ponto de recuperação consistente de falha. Clique na marca de seleção para iniciar o failover.
3.  Durante o failover recuperação do Site será encerrado VMs Azure. Depois de verificar que o failback concluiu conforme esperado, você pode você pode verificar que as VMs Azure ter sido encerradas conforme esperado.

### <a name="reprotect-the--on-premises-site"></a>Reprotect o site local

Após a conclusão de failback seus dados serão novamente no site local, mas não estará protegidos. Para iniciar a replicação no Azure novamente, faça o seguinte:

1.  No portal de recuperação do Site > selecione **máquinas** VMs com falha de volta e clique em **proteger novamente**. 
2.  Depois que você verificar que a duplicação para Azure está funcionando conforme esperado, no Azure você pode excluir as VMs Azure (não executando atualmente) que foram falha novamente.


### <a name="common-issues-in-failback"></a>Problemas comuns no failback

1. Se você executa a descoberta do vCenter de usuário de somente leitura e proteger máquinas virtuais, ele é bem sucedida e failover funciona. No momento da Reprotect, ele falhará pois os armazenamentos de dados não podem ser descobertos. Como um sintoma você não verá os armazenamentos de dados listados enquanto protege novamente. Para resolver isso, você pode atualizar a credencial vCenter com apropriado conta que tenha permissões e repetir o trabalho. [Leia mais](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
2. Quando você failback uma VM Linux e executá-lo local, você verá que o pacote do Gerenciador de rede é desinstalado da máquina. Isso ocorre porque quando a máquina virtual é recuperada no Azure, o pacote do Gerenciador de rede é removido.
3. Quando uma máquina virtual está configurada com o endereço IP estático e é falha do Azure, o endereço IP é adquirido por meio de DHCP. Quando você Falha ao longo de volta para o local, a máquina virtual continua a usar DHCP adquirir o endereço IP. Você precisará manualmente login na máquina e definir o endereço IP de volta ao endereço estático se necessário.
4. Se você estiver usando edição gratuita ESXi 5.5 ou vSphere 6 hipervisor gratuito edition, failover seria bem-sucedida, mas failback não funcionará. Você irá ned de atualização para qualquer uma das licenças de avaliação para habilitar o failback.

## <a name="failing-back-with-expressroute"></a>Voltar falhando com rota expressa

Você pode falhar volta ao longo de uma conexão de VPN ou rota expressa do Azure. Se você quiser usar a rota expressa Observe o seguinte:

- Rota expressa deve ser configurada em uma rede virtual Azure à qual fonte máquinas falhas sobre e, em quais VMs Azure estão localizados após o failover ocorre.
- Dados são replicados para uma conta de armazenamento do Azure em um ponto de extremidade público. Você deve configurar correspondência público em rota expressa com o Centro de dados de destino para replicação de recuperação do Site usar a rota expressa.



