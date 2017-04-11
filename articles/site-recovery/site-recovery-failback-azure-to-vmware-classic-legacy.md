<properties 
   pageTitle="Falha no back máquinas virtuais de VMware e servidores físicos do Azure para VMware (herdado) | Microsoft Azure" 
   description="Este artigo descreve como falha novamente uma máquina virtual VMware foram replicados para Azure com recuperação de Site do Azure." 
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
   ms.workload="storage-backup-recovery" 
   ms.date="10/05/2016"
   ms.author="ruturajd@microsoft.com"/>

# <a name="fail-back-vmware-virtual-machines-and-physical-servers-from-azure-to-vmware-with-azure-site-recovery-legacy"></a>Falha no back máquinas virtuais de VMware e servidores físicos do Azure para VMware com recuperação de Site do Azure (herdado)

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-failback-azure-to-vmware.md)
- [Azure Portal clássico](site-recovery-failback-azure-to-vmware-classic.md)
- [Portal Azure clássico (herdado)](site-recovery-failback-azure-to-vmware-classic-legacy.md)


O serviço de recuperação de Site do Azure contribui para sua estratégia de recuperação (BCDR) de desastre e continuidade de negócios por coordenação replicação, failover e recuperação de máquinas virtuais e servidores físicos. Máquinas podem ser replicadas para Azure ou uma central de dados local secundário. Para obter uma visão geral rápida leia [o que é recuperação de Site do Azure?](site-recovery-overview.md)

## <a name="overview"></a>Visão geral

Este artigo descreve como falha volta máquinas virtuais de VMware e servidores físicos Windows/Linux do Azure para seu site local depois que você já duplicados do seu site local para o Azure.

>[AZURE.NOTE] Este artigo descreve um cenário herdado. Você só deve usar as instruções neste artigo se você replicados para Azure usando [estas instruções herdadas](site-recovery-vmware-to-azure-classic-legacy.md). Se você configurar replicação usando a [implantação aprimorados](site-recovery-vmware-to-azure-classic-legacy.md) , siga as instruções [neste](site-recovery-failback-azure-to-vmware-classic.md) artigo falha novamente. 


## <a name="architecture"></a>Arquitetura

Este diagrama representa o cenário de failover e failback. As linhas azuis são as conexões usadas durante o failover. As linhas vermelhas são as conexões usadas durante o failback. As linhas com setas vá pela internet.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## <a name="before-you-start"></a>Antes de começar 

- Você deve ter falhado sobre suas VMs VMware ou servidores físicos e elas devem estar em execução no Azure.
- Você deve observar que você só pode falhar traseira máquinas virtuais de VMware e servidores físicos Windows/Linux do Azure para máquinas virtuais de VMware no local principal local.  Se você estiver falhando novamente uma máquina física, failover Azure irá convertê-la para uma máquina virtual do Azure e failback para VMware irá convertê-la a uma VM VMware.

Veja aqui como configurar failback:

1. **Configurar componentes de failback**: você precisará configurar um servidor de vContinuum no local e aponte para o servidor de configuração de máquina virtual no Azure. Você também configurará um servidor de processo como uma máquina virtual do Azure para enviar dados para o servidor de destino mestre local. Você registrar o servidor de processo com o servidor de configuração que manipulados o failover. Instalar um servidor de destino mestre local. Se você precisar de um servidor de destino mestra do Windows ela é configurada automaticamente quando você instala o vContinuum. Se você precisar Linux você precisará configurá-lo manualmente em um servidor separado.
2. **Habilitar proteção e failback**: depois que você configurou os componentes, em vContinuum você precisará ativar a proteção para Falha ao longo do Azure VMs. Você vai executar uma verificação de preparação nas VMs e executar um failover do Azure para seu site local. Depois que termina de failback reprotect máquinas locais, para que eles iniciarem replicação no Azure.



## <a name="step-1-install-vcontinuum-on-premises"></a>Etapa 1: Instalar vContinuum local

Você precisará instalar um servidor de vContinuum no local e aponte para o servidor de configuração.

1.  [Baixe o vContinuum](http://go.microsoft.com/fwlink/?linkid=526305). 
2.  Baixe a versão [vContinuum atualizar](http://go.microsoft.com/fwlink/?LinkID=533813) .
3. Instale a versão mais recente do vContinuum. Na página de **boas-vindas** , clique em **Avançar**.
    ![](./media/site-recovery-failback-azure-to-vmware/image2.png)
4.  Na primeira página do Assistente para especificar o endereço IP do servidor CX e a porta do servidor CX. Selecione **usar HTTPS**.

    ![](./media/site-recovery-failback-azure-to-vmware/image3.png)

5.  Localize o servidor de configuração de endereço IP na guia **painel** do servidor configuração máquina virtual no Azure.
    ![](./media/site-recovery-failback-azure-to-vmware/image4.png)

6.  Localize o servidor de configuração porta pública HTTPS na guia **pontos de extremidade** do servidor configuração máquina virtual no Azure.

    ![](./media/site-recovery-failback-azure-to-vmware/image5.png)

7. Na página **Detalhes de senha CS** especifique a senha que você anotou pressionada quando você registrou o servidor de configuração. Se você não se lembra-check-in **c:\\arquivos de programa (x86)\\InMage sistemas\\particular\\connection.passphrase** no servidor de configuração máquina virtual.

    ![](./media/site-recovery-failback-azure-to-vmware/image6.png)

8.  Na página **Selecionar o local de destino** Especifica onde você deseja instalar o servidor de vContinuum e clique em **instalar**.

    ![](./media/site-recovery-failback-azure-to-vmware/image7.png)

9. Após a conclusão da instalação, você pode iniciar vContinuum.
    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## <a name="step-2-install-a-process-server-in-azure"></a>Etapa 2: Instalar um servidor de processo no Azure 

Você precisa instalar um servidor de processo no Azure para que as VMs no Azure podem enviar os dados de volta para um servidor de destino mestre de local. 

1.  Na página **Configuração servidores** no Azure, selecione para adicionar um novo servidor de processo.

    ![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  Especificar um servidor de processo nome e um nome e senha para se conectar à máquina virtual como administrador. Selecione o servidor de configuração à qual você deseja registrar o servidor de processo. Isso deve ser o mesmo servidor em que você está usando para proteger e falhar ao longo de suas máquinas virtuais.
3.  Especifique a rede Azure no qual o servidor de processo deve ser implantado. Ele deve estar na mesma rede que o servidor de configuração. Especifique uma sub-rede de endereço selecionado IP exclusiva e iniciar a implantação.

    ![](./media/site-recovery-failback-azure-to-vmware/image10.png)

4.  Um trabalho é disparado para implantar o servidor de processo.

    ![](./media/site-recovery-failback-azure-to-vmware/image11.png)

5.  Depois que o servidor processo é implantado no Azure você pode fazer logon no servidor usando as credenciais que você especificou. Registre o servidor de processo da mesma maneira que você registrou o servidor de processo local. 

    ![](./media/site-recovery-failback-azure-to-vmware/image12.png)

>[AZURE.NOTE] Os servidores registrados durante failback não serão visíveis em Propriedades de máquina virtual em recuperação do Site. Eles são visíveis apenas na guia **servidores** do servidor de configuração para a qual ele estão registrados. Pode levar cerca de 10 a 15 minutos até que o servidor de processo aparece na guia.


## <a name="step-3-install-a-master-target-server-on-premises"></a>Etapa 3: Instalar um servidor de destino mestre local

Dependendo do seu sistema operacional de máquinas virtuais de origem, você precisa instalar uma Linux ou um servidor de destino mestra do Windows no local.

### <a name="deploy-a-windows-master-target-server"></a>Implantar um servidor de destino mestra do Windows

Um destino mestra do Windows já é fornecido com a instalação do vContinuum. Quando você instala o vContinuum, um servidor mestre também é implantado na mesma máquina e registrado no servidor de configuração.

1.  Para começar a implantação, crie um vazio máquina local no host ESX no qual você deseja recuperar VMs do Azure.

2.  Verifique se há pelo menos dois discos anexado para a máquina virtual – um é usado para o sistema operacional e a outra é usada para a unidade de retenção.

3.  Instale o sistema operacional.

4.  Instale o vContinuum no servidor. Isso também conclui a instalação do servidor mestre de destino.

### <a name="deploy-a-linux-master-target-server"></a>Implantar um servidor de destino mestre Linux

1.  Para começar a implantação, crie um vazio máquina local no host ESX no qual você deseja recuperar VMs do Azure.

2.  Verifique se há pelo menos dois discos anexado para a máquina virtual – um é usado para o sistema operacional e a outra é usada para a unidade de retenção.

3.  Instale o sistema operacional Linux. O sistema de destino mestre Linux não deve usar LVM para espaços de armazenamento de retenção ou raiz. Um servidor de destino mestre Linux está configurado para evitar a descoberta de partições/disco LVM por padrão.
4.  Partições que você pode criar:

    ![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  Executar o abaixo postar etapas de instalação antes de iniciar a instalação de destino mestre.


#### <a name="post-os-installation-steps"></a>Postar etapas de instalação do sistema operacional

Para obter as IDs de SCSI para cada um dos SCSI disco rígido em um computador virtual Linux, ative o parâmetro "disco. EnableUUID = TRUE "da seguinte maneira:

1. Desligar sua máquina virtual.
2. Clique com botão direito na entrada de máquina virtual no painel esquerdo > **Editar configurações**.
3. Clique na guia **Opções** . Selecione **Avançado\>item geral** > **Parâmetros de configuração**. A opção de **Parâmetros de configuração** está disponível somente quando o computador está desligado.

    ![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. Verifica se uma linha com **disco. EnableUUID** existe. Se ele e é definido como **False** , em seguida, configurá-lo como **True** (não diferencia maiusculas e minúsculas). Se existe e está definido como true, clique em **Cancelar** e testar o comando SCSI dentro do sistema operacional convidado após a inicialização para cima. Se não existir clique em **Adicionar linha**.
5. Adicione disco. EnableUUID na coluna **nome** . Defina seu valor como TRUE. Não adicione os valores acima juntamente com aspas duplas.

    ![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### <a name="download-and-install-the-additional-packages"></a>Baixar e instalar pacotes adicionais

Observação: Verifique se que o sistema tem conectividade com a internet antes de baixar e instalar pacotes adicionais.

\#yum instalar -y xfsprogs perl lsscsi rsync wget kexec-ferramentas

Este comando baixa esses 15 pacotes do repositório de CentOS 6.6 e instala-las:

continuidade de negócios-1.06.95-1.el6.x86\_rpm 64

busybox-1.15.1-20.el6.x86\_rpm 64

elfutils-as bibliotecas-0.158-3.2.el6.x86\_rpm 64

kexec-ferramentas-2.0.0-280.el6.x86\_rpm 64

lsscsi-0,23-2.el6.x86\_rpm 64

lzo-2.03-3.1.el6\_5.1.x86\_rpm 64

Perl-5.10.1-136.el6\_6.1.x86\_rpm 64

Perl-módulo-conectável-3.90-136.el6\_6.1.x86\_rpm 64

Perl-Pod-sai-1.04-136.el6\_6.1.x86\_rpm 64
    
Perl-Pod-simples-3,13-136.el6\_6.1.x86\_rpm 64

Perl-as bibliotecas-5.10.1-136.el6\_6.1.x86\_rpm 64

Perl-versão-0.77-136.el6\_6.1.x86\_rpm 64

rsync-3.0.6-12.el6.x86\_rpm 64

1.el6.x86 interessantes 1.1.0\_rpm 64

wget-1.12-5.el6\_6.1.x86\_rpm 64

Observação: Se o computador de origem usa o sistema de arquivos Reiser ou XFS para o dispositivo de inicialização ou raiz, então seguintes pacotes devem ser baixados e instalados no Linux mestre destino antes de proteção.

\#CD /usr/local

\#wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

\#wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

\#rpm - ivh kmod-reiserfs-0,0-1.el6.elrepo.x86\_64 rpm reiserfs-utilitários-3.6.21-1.el6.elrepo.x86\_rpm 64

\#wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

\#rpm - ivh xfsprogs-3.1.1-16.el6.x86\_rpm 64

#### <a name="apply-custom-configuration-changes"></a>Aplicar alterações de configuração personalizada

Antes de aplicar essas alterações certificar-se de que você concluiu a seção anterior e, em seguida, siga estas etapas:


1. Copie o agente de Unificação RHEL 6-64 binários para o sistema operacional recém-criado.

2. Executar este comando para descompacte o binário: **tar - zxvf \<nome de arquivo\> **

3. Executar este comando para conceder permissões: \# **./ApplyCustomChanges.sh 755 chmod**

4. Executar o script: ** \# ./ApplyCustomChanges.sh**. Execute o script apenas uma vez no servidor. Reinicie o servidor após o script é executado.



### <a name="install-the-linux-server"></a>Instalar o servidor Linux


1. [Baixar](http://go.microsoft.com/fwlink/?LinkID=529757) o arquivo de instalação.
2. Copie o arquivo na máquina virtual de destino Linux mestre usando um utilitário de cliente sftp de sua escolha. Alternativamente você pode fazer logon na máquina virtual Linux destino mestre e usar wget para baixar o pacote de instalação do link fornecido
3. Logon o Linux destino mestre server máquina virtual usando um ssh cliente da sua escolha
4. Se você estiver conectado à rede do Azure no qual você distribuiu seu servidor de destino mestre Linux por meio de uma conexão de VPN, em seguida, use o endereço IP interno do servidor que podem ser encontrados na guia do **painel** de máquina virtual e porta 22 para se conectar ao servidor usando Secure Shell destino mestre Linux.
5. Se você estiver se conectando ao servidor de destino mestre de Linux ao longo de uma conexão de internet pública use o Linux destino mestre público virtual endereço IP do servidor (da guia de **Dashboard** máquinas virtuais) e o ponto de extremidade público criado para ssh fazer login servder o Linux.
6. Extrair os arquivos do arquivo gzip Linux destino mestre Server installer tar executando: *"tar – xvzf Microsoft ASR\_UA\_8.2.0.0\_RHEL6 64\"* do diretório que contém o arquivo de instalação.

    ![](./media/site-recovery-failback-azure-to-vmware/image16.png)

7. Se você extraído o instalador alterar arquivos para uma pasta diferente no diretório para que o conteúdo de tar arquivar foram extraídos. Deste caminho de diretório executar "sudo. / install.sh".

    ![](./media/site-recovery-failback-azure-to-vmware/image17.png)

8. Quando solicitado a escolher uma função principal selecione **2 (mestre de destino)**. Deixe as outras opções de instalação interativo em seus valores padrão.
9. Aguarde a instalação para continuar e a Interface de configuração de Host apareça. O utilitário de configuração de Host para o Linux mestre sarget Server é um utilitário de linha de comando. Não redimensionar o ssh janela Utilitário de cliente. Use as teclas de direção para selecionar a opção **Global** e pressione ENTER no teclado.

    ![](./media/site-recovery-failback-azure-to-vmware/image18.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image19.png)


10. No campo **IP** , digite o endereço IP interno do servidor configuração (você pode encontrá-lo na guia **painel** do servidor de configuração de máquina virtual) e pressione ENTER. Em **porta** , insira **22** e pressione ENTER. 
11.  Deixe **Usar HTTPS** como **Sim**e pressione ENTER.
12.  Insira a senha que foi gerada no servidor de configuração. Se você estiver usando um cliente de ACABAMENTO de um computador Windows para ssh à máquina virtual Linux, você pode usar Shift + Insert para colar o conteúdo da área de transferência. Copie a senha na área de transferência local usando Ctrl + C e use Shift + Insert para colá-lo. Pressione ENTER.
13.  Use a tecla de seta para a direita para navegar para encerrar e pressione ENTER. Aguarde a conclusão da instalação.

    ![](./media/site-recovery-failback-azure-to-vmware/image20.png)

Se por algum motivo você Falha ao registrar seu servidor de destino mestre Linux para o servidor de configuração você pode fazer novamente executando o host utilitário de configuração do /usr/local/ASR/Vx/bin/hostconfigcli (você precisará primeiro definir permissões de acesso para este diretório executando chmod como um usuário super).


#### <a name="validate-master-target-registration"></a>Validar o registro de destino mestre

Você pode validar que o servidor de destino mestre foi registrado com êxito para o servidor de configuração em Cofre de recuperação de Site do Azure > **Servidor de configuração** > **Detalhes do servidor**.

>[AZURE.NOTE] Depois de registrar o servidor de destino mestre, se você receber erros de configuração da máquina virtual pode ter sido excluída do Azure ou pontos de extremidade não estão configurados corretamente, isso ocorre porque embora a configuração de destino mestre for detectada pelo Azure dndpoints quando o destino mestre é implantado no Azure, isso não é verdadeiro para um local destino mestre server no local. Isso não afetará failback e você pode ignorar esses erros. 



## <a name="step-4-protect-the-virtual-machines-to-the-on-premises-site"></a>Etapa 4: Proteger as máquinas virtuais para o site local

Você precisa proteger VMs para o site local antes de você falha novamente.

### <a name="before-you-begin"></a>Antes de começar

Quando uma máquina virtual é falha do Azure, ele adiciona uma unidade temp extra para o arquivo da página. Isso é uma unidade extra que normalmente não é necessária por seu falhou máquina virtual desde que ele já pode ter uma unidade dedicada para o arquivo de página. Antes de começar inversa proteção das máquinas virtuais, você precisa garantir que esta unidade é colocada offline para que ele não fique protegido. Faça isso da seguinte maneira:

1.  Abra o gerenciamento do computador e selecione gerenciamento de armazenamento para que lista os discos online e anexado ao computador.
2.  Selecione o disco temporário anexado ao computador e escolha para colocá-lo offline. 

### <a name="protect-the-vms"></a>Proteger as VMs

1. No portal do Azure, verifique os estados da máquina virtual para garantir que eles estiverem falhou.

    ![](./media/site-recovery-failback-azure-to-vmware/image21.png)

2. Inicie vContinuum em sua máquina.

    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)

3. Clique em **Nova proteção** e selecione o tipo de sistema de operação, o 

4.  Na nova janela que abrir selecione o **tipo de sistema operacional** > **Obter detalhes** de VMs que você deseja falhar novamente. Nos **detalhes do servidor primário**, identifique e selecione as máquinas virtuais que você deseja proteger. VMs são listadas sob o nome do host vCenter que estavam na antes do failover.
5.  Quando você seleciona uma máquina virtual para proteger (e não obteve já sobre Azure) uma janela pop-up fornece duas entradas para a máquina virtual. Isso ocorre porque o servidor de configuração detecta duas instâncias das máquinas virtuais registradas para ele. Você precisa remover a entrada para a máquina virtual local para que você pode proteger a máquina virtual correta. Para identificar a entrada de máquina virtual do Azure correta aqui, você pode efetuar login a máquina virtual do Azure e vá para C:\Program Files (x86) \Microsoft Azure Site Recovery\Application Data\etc. Em drscout.conf o arquivo, identifique a identificação de host. Na caixa de diálogo vContinuum, manter a entrada para a qual você encontrou a identificação de host na máquina virtual. Exclua todas as outras entradas. Para selecionar a máquina virtual correta, você pode consultar a seu endereço IP. O IP endereço intervalo local será a máquina virtual local.

    ![](./media/site-recovery-failback-azure-to-vmware/image22.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image23.png)

6. No servidor vCenter pare a máquina virtual. Você também pode excluir o VMs local.
7. Em seguida, especifique o servidor de MT de local ao qual você deseja proteger VMs. Para fazer isso, se conecte ao servidor vCenter à qual você deseja falhar novamente.

    ![](./media/site-recovery-failback-azure-to-vmware/image24.png)

8. Selecione o servidor de destino mestra com base no host ao qual você deseja recuperar a máquina virtual.

    ![](./media/site-recovery-failback-azure-to-vmware/image24.png)

9. Fornece a opção de replicação para cada uma das máquinas virtuais. Para fazer isso, você precisa selecionar recuperação lado armazenamento de dados à qual as VMs serão recuperadas. A tabela resume as diferentes opções que você precisa fornecer para cada máquina virtual.

    ![](./media/site-recovery-failback-azure-to-vmware/image25.png)

    **Opção** | **Opção valor recomendada**
    ---|---
    Endereço IP do servidor de processo | Selecione o servidor de processo implantado no Azure
    Tamanho de retenção em MB| 
    Valor de retenção | 1
    Dias/horas | Dias
    Intervalo de consistência | 1
    Selecione o armazenamento de dados de destino | O armazenamento de dados disponível no site de recuperação. O armazenamento de dados deve ter espaço suficiente e estar disponíveis para o host ESX no qual você deseja restaurar a máquina virtual.


10. Configure as propriedades da máquina virtual será adquirida após failover ao site local. As propriedades são resumidas na tabela a seguir.

    ![](./media/site-recovery-failback-azure-to-vmware/image26.png)

    **Propriedade** | **Detalhes**
    ---|---
    Configuração de rede| Para cada adaptador de rede detectado, selecione-o e clique em **Alterar** para configurar o endereço IP de failback para a máquina virtual. 
    Configuração de hardware| Especifique a CPU e a memória para a máquina virtual. Configurações podem ser aplicadas a todas as VMs que você está tentando proteger. Para identificar os valores corretos para a CPU e memória, você pode se referir ao tamanho de função IAAS VMs e ver o número de cores e memória atribuída.
    Nome para exibição | Após failback para o local, você pode renomear as máquinas virtuais como aparecerão no estoque vCenter. O nome padrão é o nome de host do computador de máquina virtual. Para identificar o nome de máquina virtual, você pode consultar a lista de máquina virtual do grupo de proteção.
    Configuração NAT | Discutidos detalhadamente abaixo

    ![](./media/site-recovery-failback-azure-to-vmware/image27.png)

#### <a name="configure-nat-settings"></a>Configurar definições de NAT

1. Para ativar a proteção das máquinas virtuais, dois canais de comunicação precisam ser estabelecida. O primeiro canal é entre a máquina virtual e o servidor de processo. Esse canal coleta os dados da máquina virtual e envia para o servidor de processo que envia os dados para o servidor mestre de destino. Se o servidor de processo e a máquina virtual para ser protegidos estão na mesma rede virtual Azure, em seguida, não é necessário usar as configurações de NAT. Caso contrário, especifique as configurações de NAT. Exiba o endereço IP público do servidor processo no Azure. 

    ![](./media/site-recovery-failback-azure-to-vmware/image28.png)

2. O segundo canal é entre o servidor de processo e o servidor de destino mestre. A opção para usar NAT ou não depende de se você estiver usando uma conexão VPN com base ou comunicação através da internet. Não selecione NAt se você estiver usando VPN, mas apenas se você estiver usando uma conexão de internet.

    ![](./media/site-recovery-failback-azure-to-vmware/image29.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image30.png)

3. Se você ainda não tiver excluído máquinas virtuais local conforme especificado e se o armazenamento de dados que não está conseguindo volta ainda contém do VMDK antigo, em seguida, você precisará garantir que o failback que máquina virtual é criado em um novo local. Para fazer isso selecionar as configurações **avançadas** e especifique uma pasta alternativa para restaurar nas **Configurações de nome de pasta**. Deixe as outras opções com suas configurações padrão. Aplica as configurações de nome de pasta para todos os servidores.

    ![](./media/site-recovery-failback-azure-to-vmware/image31.png)

4. Execute uma verificação de preparação para garantir que as máquinas virtuais está prontas para ser protegidos para o local. 

    ![](./media/site-recovery-failback-azure-to-vmware/image32.png)


5. Aguarde a conclusão. Se estiver com êxito em todas as VMs, você pode especificar um nome para o plano de proteção. Clique em **proteger** para começar.

    ![](./media/site-recovery-failback-azure-to-vmware/image33.png)


6. Você pode monitorar o progresso no vContinuum.

    ![](./media/site-recovery-failback-azure-to-vmware/image34.png)

7. Após a etapa **Que plano de proteção de ativação** for concluído, você pode monitorar proteção de máquina virtual no portal de recuperação do Site.

    ![](./media/site-recovery-failback-azure-to-vmware/image35.png)

8. Você pode ver o status exato clicando na máquina virtual e monitorar seu andamento.

    ![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## <a name="prepare-the-failback-plan"></a>Preparar o plano de failback

Você pode preparar um plano de failback usando vContinuum para que o aplicativo pode falhar volta para o site local a qualquer momento. Esses planos de recuperação são muito semelhantes aos planos de recuperação em recuperação de Site.

1.  Iniciar vContinuum e selecione **gerenciar planos** > **recuperar.** Você pode ver de lista de todos os planos que foram usados falha sobre VMs. Você pode usar os planos do mesmos recuperar.

    ![](./media/site-recovery-failback-azure-to-vmware/image37.png)

2. Selecione o plano de proteção e todas as VMs que você deseja recuperar nele. Quando você seleciona cada máquina virtual, você pode ver mais detalhes, incluindo o destino ESX server e o disco de máquina virtual de origem. Clique em **Avançar** para iniciar o assistente recuperar e selecione as VMs que você deseja recuperar.

    ![](./media/site-recovery-failback-azure-to-vmware/image38.png)

3. Você pode recuperar com base em várias opções, mas recomendamos que você use **Marca mais recente** e selecione **Aplicar para todas as VMs** para garantir que os dados mais recentes na máquina virtual serão usados.
4. Executar o **verificação de preparação.** Isto irá verificar se os parâmetros direita são configurados para habilitar a recuperação de máquina virtual. Clique em **Avançar** se todas as verificações forem bem-sucedidas. Caso contrário, verifique o log e resolva os erros.

    ![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  Configuração de **Máquina virtual** Certifique-se de que as configurações de recuperação estão definidas corretamente. Se for necessário, você pode alterar as configurações de máquina virtual.

    ![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9. Examine a lista de máquinas virtuais que serão recuperados e especifique uma ordem de recuperação. Observe que VMs são listadas usando o nome de host do computador. Pode ser difícil mapear o nome de host do computador na máquina virtual.
Para mapear os nomes, vá para as máquinas virtuais **Dashboard** no Azure e verifique o nome de host de máquina virtual.

    ![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10. Especifique um nome de plano e selecione **recuperar posteriormente**. É recomendável recuperar mais tarde, como a proteção inicial não pode ser concluída. 
11. Clique em **recuperar** para salvar o plano ou disparar a recuperação se você tiver selecionado para recuperar agora e não mais tarde. Você pode verificar o status de recuperação para ver se o plano do foi salvo.

    ![](./media/site-recovery-failback-azure-to-vmware/image42.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## <a name="recover-virtual-machines"></a>Recupere máquinas virtuais

Após o plano do foi criado, você pode recuperar as máquinas virtuais. Antes você verificar que as máquinas virtuais concluiu a sincronização. Se o status de replicação Okey mostra a proteção for concluída e o limite RPO foi atendido. Você pode verificar integridade nas propriedades de máquina virtual.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

Desative as máquinas virtuais Azure antes de iniciar a recuperação. Isso garante que não há nenhum cérebro dividida e que os usuários terão acesso somente uma cópia do aplicativo. 


1.  Inicie o plano salvo. Na vContinuum selecione **Monitor** os planos. Lista todos os planos que foram executados.

    ![](./media/site-recovery-failback-azure-to-vmware/image45.png)

2.  Selecione o plano de **recuperação** e clique em **Iniciar**. Você pode monitorar recuperação. Depois de VMs foram ativadas por você pode se conectar a eles em vCenter.

    ![](./media/site-recovery-failback-azure-to-vmware/image46.png)

## <a name="protect-to-azure-again-after-failback"></a>Proteger no Azure novamente após failback

Após a conclusão failback provavelmente desejará Proteja as máquinas virtuais novamente. Faça isso da seguinte maneira:

1.  Verifique se as máquinas virtuais locais estão funcionando e que aplicativos são acessíveis.
2.  No portal do Azure recuperação do Site, selecione as máquinas virtuais e excluí-las. Selecione para desativar a proteção das máquinas virtuais. Isso garante que as VMs não mais protegidas.
3.  No Azure excluir a falha ao longo do Azure máquinas virtuais
4.  Exclua a máquina virtual antiga em vSpehere. Estas são as VMs que você anteriormente falhou ao Azure.
5.  No portal de recuperação de Site protege VMs que falhou recentemente. Após eles estiverem protegidos, você pode adicioná-los para um plano de recuperação.
 
## <a name="next-steps"></a>Próximas etapas



- [Leia sobre](site-recovery-vmware-to-azure-classic.md) replicação de máquinas virtuais VMware e servidores físicos para Azure usando a implantação avançada.

 
