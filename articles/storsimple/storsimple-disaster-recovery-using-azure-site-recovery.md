<properties 
   pageTitle="Automatizar DR para compartilhamentos de arquivos em StorSimple usando a recuperação de Site Azure | Microsoft Azure"
   description="Descreve as etapas e práticas recomendadas para criar uma solução de recuperação para compartilhamentos de arquivos hospedado no armazenamento StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="vidarmsft"
   manager="syadav"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/16/2016"
   ms.author="vidarmsft" />

# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Solução automatizada de recuperação de dados usando a recuperação de Site do Azure para compartilhamentos de arquivos hospedados em StorSimple

## <a name="overview"></a>Visão geral

Microsoft Azure StorSimple é uma solução de armazenamento de nuvem híbrida que resolve as complexidades de dados não estruturados comumente associadas ao compartilhamentos de arquivos. StorSimple usa armazenamento em nuvem como uma extensão da solução local e automaticamente dados de níveis em locais de armazenamento e armazenamento em nuvem. Proteção de dados, integrada com local e nuvem instantâneos, elimina a necessidade de uma infraestrutura de armazenamento amplas.

[Recuperação de Site do Azure](../site-recovery/site-recovery-overview.md) é um serviço baseado em Azure que fornece recursos de recuperação (DR) de desastres por coordenação replicação, failover e recuperação de máquinas virtuais. Recuperação de Site Azure oferece suporte a um número de tecnologias de replicação consistentemente replicar, proteger e perfeitamente falhar sobre máquinas virtuais e aplicativos para nuvens público/particular ou hospedados.

Usando a recuperação de Site do Azure, replicação de máquina virtual e recursos de instantâneo de nuvem StorSimple, você pode proteger o ambiente do servidor de arquivo completo. Em caso de uma interrupção, você pode usar um único clique para trazer seus compartilhamentos de arquivos online no Azure em poucos minutos.

Este documento explica em detalhes como você pode criar uma solução de recuperação para seus compartilhamentos de arquivos hospedados no armazenamento de StorSimple, executar planejadas, não planejadas e testar failovers usando um plano de recuperação de um único clique. Em essência, ele mostra como você pode modificar o plano de recuperação em seu Cofre de recuperação de Site do Azure para habilitar StorSimple failovers durante cenários de desastres. Além disso, ele descreve configurações suportadas e os pré-requisitos. Este documento pressupõe que você esteja familiarizado com as Noções básicas de arquiteturas de recuperação de Site do Azure e StorSimple.

## <a name="supported-azure-site-recovery-deployment-options"></a>Opções de implantação de recuperação de Site do Azure com suporte

Clientes podem implantar servidores de arquivos como servidores físicos ou máquinas virtuais (VMs) em execução no Hyper-V ou VMware e, em seguida, criar compartilhamentos de arquivos de volumes gravados sem armazenamento StorSimple. Azure recuperação de Site pode proteger implantações físicas e virtuais para um site de secundário ou Azure. Este documento aborda os detalhes de uma solução de DR com o Azure como o site de recuperação para um servidor de arquivos que máquina virtual hospedado no Hyper-V e compartilhamentos de arquivos de armazenamento de StorSimple. Outros cenários em que o servidor de arquivo máquina virtual está em uma VM VMware ou uma máquina física podem ser implementados de maneira semelhante.

## <a name="prerequisites"></a>Pré-requisitos

Implementar uma um único clique solução de recuperação que usa a recuperação de Site do Azure para compartilhamentos de arquivos hospedados no armazenamento StorSimple tem os seguintes pré-requisitos:

-   Servidor de arquivos do Windows Server 2012 R2 que máquina virtual hospedado em Hyper-V ou VMware ou uma máquina física local

-   StorSimple armazenamento dispositivo local registrado com o Gerenciador de StorSimple do Azure

-   Aparelho de nuvem StorSimple criado no Gerenciador de Azure StorSimple (Isso pode ser mantido em estado de desligamento)

-   Compartilhamentos de arquivos hospedados nos volumes configurados no dispositivo de armazenamento StorSimple

-   [Compartimento de serviços de recuperação de Site do azure](../site-recovery/site-recovery-vmm-to-vmm.md) criado em uma assinatura do Microsoft Azure

Além disso, se Azure é seu site de recuperação, execute a [ferramenta de avaliação de prontidão de máquina Virtual do Azure](http://azure.microsoft.com/downloads/vm-readiness-assessment/) em VMs para garantir que eles são compatíveis com os serviços de recuperação de Site do Azure e VMs do Azure.

Contas evitar problemas (que podem resultar em custos mais altos), certifique-se de que você crie seu aparelho de nuvem StorSimple, conta de automação e armazenamento de latência na mesma região.

## <a name="enable-dr-for-storsimple-file-shares"></a>Habilitar DR para compartilhamentos de arquivos de StorSimple  

Cada componente do ambiente local precisa ser protegido para habilitar a recuperação e replicação completa. Esta seção descreve como:

-   Configurar a replicação do Active Directory e DNS (opcional)

-   Use a recuperação de Site do Azure para ativar a proteção do servidor de arquivos máquina virtual

-   Ativar a proteção de volumes de StorSimple

-   Configurar a rede

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Configurar a replicação do Active Directory e DNS (opcional)

Se você deseja proteger máquinas que executam o Active Directory e DNS para que elas fiquem disponíveis no site do DR, você precisa explicitamente protegê-las (de forma que os servidores de arquivos são acessíveis após falhas sobre com autenticação). Há duas opções recomendadas baseadas na complexidade do ambiente de local do cliente.

#### <a name="option-1"></a>Opção 1

Se o cliente tiver um pequeno número de aplicativos, um único controlador de domínio para todo o site local e estar falhando sobre o site inteiro, em seguida, recomendamos o uso de replicação de recuperação de Site do Azure para replicar a máquina de controlador de domínio para um site secundário (isto é aplicável-to-site e o site ao Azure).

#### <a name="option-2"></a>Opção 2

Se o cliente tem um grande número de aplicativos, está executando uma floresta do Active Directory e será estar falhando sobre alguns aplicativos por vez, é recomendável configurar um controlador de domínio adicionais no site do DR (um site de secundário ou no Azure).

Consulte a [solução automatizada DR para o Active Directory e DNS usando a recuperação de Site do Azure](../site-recovery/site-recovery-active-directory.md) para obter instruções ao disponibilizar um controlador de domínio no site do DR. Para o restante deste documento, podemos assumirá que um controlador de domínio está disponível no site do DR.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Use a recuperação de Site do Azure para ativar a proteção do servidor de arquivos máquina virtual

Esta etapa requer que você preparar o ambiente de servidor de arquivo local, cria e preparar um cofre de recuperação de Site do Azure e habilitar proteção de arquivo da máquina virtual.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Preparar o ambiente de servidor de arquivo local

1.  Defina o **controle de conta de usuário** para **nunca notificar**. Isso é necessário para que você possa usar scripts de automação Azure para conectar os destinos iSCSI após falhas pela recuperação de Site do Azure.

    1.  Pressione a tecla Windows + P e procure **UAC**.

    2.  Selecione **configurações de controle de conta de usuário de alteração**.

    3.  Arraste a barra para a parte inferior na direção **Nunca notificar**.

    4.  Clique em **Okey** e selecione **Sim** quando solicitado.

        ![](./media/storsimple-dr-using-asr/image1.png)

1.  Instale o agente de máquina virtual em cada uma das VMs de servidor de arquivos. Isso é necessário para que você pode executar scripts de automação Azure em com falha sobre VMs.

    1.  [Baixar o agente](http://aka.ms/vmagentwin) para `C:\\Users\\<username>\\Downloads`.

    2.  Abrir o Windows PowerShell no modo de administrador (Executar como administrador) e, em seguida, digite o seguinte comando para navegar até o local de download:

        `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

        > [AZURE.NOTE] O nome do arquivo pode mudar dependendo da versão.

1.  Clique em **Avançar**.

2.  Aceite os **Termos do contrato** e clique em **Avançar**.

3.  Clique em **Concluir**.


1.  Crie compartilhamentos de arquivos usando volumes gravados sem armazenamento StorSimple. Para obter mais informações, consulte [usar o serviço de Gerenciador de StorSimple para gerenciar volumes](storsimple-manage-volumes.md).

    1.  No seu VMs local, pressione a tecla Windows + P e procure **iSCSI**.

    2.  Selecione **iniciador iSCSI**.

    3.  Selecione a guia **configuração** e copie o nome do iniciador.

    4.  Faça logon no [portal de clássico Azure](https://manage.windowsazure.com/).

    5.  Selecione a guia **StorSimple** e selecione o serviço de Gerenciador de StorSimple que contém o dispositivo físico.

    6.  Criar contêiner de volume (s) e, em seguida, criar volumes. (Esses volumes são para os compartilhamentos de arquivo no servidor de arquivos VMs). Copie o nome do iniciador e dê um nome apropriado para os registros de controle de acesso ao criar os volumes.

    7.  Selecione a guia de **Configurar** e anotação para baixo o endereço IP do dispositivo.

    8.  Na sua VMs local, vá para o **iniciador iSCSI** novamente e insira o IP na seção conexão rápida. Clique em **Conectar rápida** (o dispositivo agora deve estar conectado).

    9.  Abra o Portal de gerenciamento do Azure e selecione a guia **Volumes e dispositivos** . Clique em **configuração automática**. O volume que você acabou de criar deverá aparecer.

    10. No portal do, selecione a guia **dispositivos** e selecione **criar um novo dispositivo Virtual.** (Esse dispositivo virtual será usado se houver uma falha). Este novo dispositivo virtual pode ser mantido em um estado offline para evitar custos extras. Para colocar o dispositivo virtual offline, vá para a seção de **máquinas virtuais** no Portal e fechá-lo.

    11. Voltar para as VMs local e abra o gerenciamento de disco (pressione a tecla Windows + X e selecione **Gerenciamento de disco**).

    12. Você observará alguns discos extras (dependendo do número de volumes que você criou). Clique com botão direito primeiro, selecione **Inicializar disco**e selecione **Okey**. A seção **Unallocated** de atalho, selecione **Novo Volume simples**, atribuir uma letra de unidade e concluir o assistente.

    13. Repita a etapa l para todos os discos. Agora você pode ver todos os discos em **Neste PC** no Windows Explorer.

    14. Use a função de serviços de arquivo e armazenamento para criar compartilhamentos de arquivos nesses volumes.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Criar e preparar um cofre de recuperação de Site do Azure

Consulte a [documentação de recuperação de Site do Azure](../site-recovery/site-recovery-hyper-v-site-to-azure.md) para começar a usar a recuperação do Azure Site antes de proteger o máquina virtual do servidor de arquivos.

#### <a name="to-enable-protection"></a>Para ativar a proteção

1.  Desconecte o destino (s) iSCSI VMs no local que você deseja proteger através da recuperação de Site do Azure:

    1.  Pressione a tecla Windows + P e procure **iSCSI**.

    2.  Selecione **Configurar o iniciador iSCSI**.

    3.  Desconecte o dispositivo de StorSimple que você conectou anteriormente. Como alternativa, você pode desativar o servidor de arquivos por alguns minutos ao habilitar proteção.

    > [AZURE.NOTE] Isso fará com que os compartilhamentos de arquivo está temporariamente indisponível

1.  [Ativar a proteção de máquina virtual](../site-recovery/site-recovery-hyper-v-site-to-azure.md##step-6-enable-replication) do servidor de arquivos máquina virtual a partir do portal de recuperação de Site do Azure.

2.  Quando começa a sincronização inicial, você pode reconectar o destino novamente. Vá para o iniciador iSCSI, selecione o dispositivo de StorSimple e clique em **Conectar**.

3.  Quando a sincronização é concluída e o status da máquina virtual estiver **protegido**, selecione a máquina virtual, selecione a guia **Configurar** e atualizar a rede da máquina virtual adequadamente (essa é a rede que o VM(s) falhou será parte de). Se a rede não aparecer, significa que a sincronização ainda está acontecendo.

### <a name="enable-protection-of-storsimple-volumes"></a>Ativar a proteção de volumes de StorSimple

Se você não selecionou a opção **Habilitar um backup padrão para este volume** para os volumes de StorSimple, vá para **Políticas de Backup** no serviço do Gerenciador de StorSimple e criar uma política de backup adequada para todos os volumes. Recomendamos que você defina a frequência de backups para o objetivo de ponto de recuperação (RPO) que você gostaria de ver para o aplicativo.

### <a name="configure-the-network"></a>Configurar a rede

Para o servidor do arquivo máquina virtual, definir configurações de rede na recuperação de Site do Azure para que as redes de máquina virtual são anexadas à rede de DR correta após failover.

Você pode selecionar a máquina virtual na **Nuvem VMM** ou o **Grupo de proteção** para definir as configurações de rede, conforme mostrado na ilustração a seguir.

![](./media/storsimple-dr-using-asr/image2.png)

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

Você pode criar um plano de recuperação no ASR para automatizar o processo de failover de compartilhamentos de arquivos. Se ocorrer uma interrupção, você poderá exibir os compartilhamentos de arquivos em poucos minutos com apenas um único clique. Para habilitar essa automação, você precisará de uma conta de automação Azure.

#### <a name="to-create-the-account"></a>Para criar a conta

1.  Acesse o portal de clássico Azure e vá para a seção de **automação** .

1.  Crie uma nova conta de automação. Mantê-lo na mesma localização geográfica/região em que as contas de armazenamento e o aparelho de nuvem StorSimple foram criadas.

2.  Clique em **novo** &gt; **Os serviços de aplicativo** &gt; **automação** &gt; **Runbook** &gt; **Da Galeria** para importar todas as runbooks necessários para a conta de automação.

    ![](./media/storsimple-dr-using-asr/image3.png)

1.  Adicione as seguintes runbooks no painel de **Recuperação** na Galeria de:

    -   Falha ao longo de contêineres de volume StorSimple

    -   Limpar de volumes de StorSimple após o teste Failover (TFO)

    -   Montar volumes em StorSimple dispositivo após failover

    -   Iniciar o dispositivo Virtual StorSimple

    -   Desinstalar a extensão de script personalizado na máquina virtual do Azure

        ![](./media/storsimple-dr-using-asr/image4.png)


1.  Publica todos os scripts selecionando runbook na conta de automação e indo para a guia de **autor** . Após essa etapa, a guia **Runbooks** aparecerá da seguinte maneira:

     ![](./media/storsimple-dr-using-asr/image5.png)

1.  Na conta de automação vá para a guia de **ativos** , clique em **Adicionar configuração** &gt; **Adicionar credencial**e adicionar suas credenciais do Azure – nome ativo AzureCredential.

    Use a credencial do Windows PowerShell. Isso deve ser uma credencial que contém um nome de usuário de ID da organização e uma senha com acesso a essa assinatura Azure e autenticação multifator desabilitado. Isso é necessário para autenticar em nome de usuário durante a failovers e exibir os volumes de servidor de arquivos no site do DR.

1.  Na conta de automação, selecione a guia de **ativos** e clique em **Adicionar configuração** &gt; **Adicionar variável** e adicione as seguintes variáveis. Você pode optar por criptografar esses ativos. Essas variáveis são específicos do plano de recuperação. Se sua recuperação planeja (que você criará na próxima etapa) nome é TestPlan, então as variáveis devem ser StorSimRegKey do plano de teste, AzureSubscriptionName do plano de teste e assim por diante.

    -   *RecoveryPlanName* **-StorSimRegKey**: A chave do registro para o serviço do Gerenciador de StorSimple.

    -   *RecoveryPlanName* **-AzureSubscriptionName**: O nome da assinatura do Azure.

    -   *RecoveryPlanName* **-ResourceName**: O nome do recurso StorSimple que tem o dispositivo StorSimple.

    -   *RecoveryPlanName* **-Dispositivo**: O dispositivo que tenha a falhar.

    -   *RecoveryPlanName* **-TargetDeviceName**: O aparelho de nuvem StorSimple nas quais os contêineres devem ser falhou.

    -   *RecoveryPlanName* **-VolumeContainers**: uma cadeia de caracteres separados por vírgulas de contêineres de volume presentes no dispositivo que precisa ser falhou; Por exemplo, volcon1, volcon2, volcon3.

    -   RecoveryPlanName**-TargetDeviceDnsName**: O nome do serviço do dispositivo de destino (Isso pode ser encontrado na seção de **Máquina Virtual** : o nome do serviço é a mesma que o nome DNS).

    -   *RecoveryPlanName* **-StorageAccountName**: O nome da conta de armazenamento no qual o script (que tem para executar na Falha ao longo de máquina virtual) será armazenado. Isso pode ser qualquer conta de armazenamento que tenha espaço para armazenar o script temporariamente.

    -   *RecoveryPlanName* **-StorageAccountKey**: A tecla de acesso para a conta de armazenamento acima.

    -   *RecoveryPlanName* **-ScriptContainer**: O nome do contêiner no qual o script será armazenado na nuvem. Se o contêiner não existir, ele será criado.

    -   *RecoveryPlanName* **-VMGUIDS**: Após proteger uma máquina virtual, recuperação de Site do Azure atribui cada máquina virtual uma identificação exclusiva que fornece os detalhes sobre a falha ao longo de máquina virtual. Para obter o VMGUID, selecione a guia **Serviços de recuperação** e clique em **Item protegido** &gt; **Grupos de proteção** &gt; **máquinas** &gt; **Propriedades**. Se você tiver várias VMs, em seguida, adicione os GUIDs como uma cadeia de caracteres separados por vírgulas.

    -   *RecoveryPlanName* **-AutomationAccountName** – o nome da conta de automação na qual você adicionou o runbooks e os ativos.

    Por exemplo, se o nome do plano de recuperação for fileServerpredayRP, em seguida, sua guia de **ativos** deverá aparecer da seguinte maneira depois de adicionar todos os ativos.

    ![](./media/storsimple-dr-using-asr/image6.png)


1.  Vá para a seção de **Serviços de recuperação** e selecione o Cofre de recuperação de Site do Azure que você criou anteriormente.

2.  Selecione a guia **Planos de recuperação** e criar um novo plano de recuperação da seguinte maneira:

    a.  Especifique um nome e selecione o **Grupo de proteção**de apropriado.

    b.  Selecione o grupo de proteção que você deseja incluir no plano de recuperação de VMs.

    c.  Após a recuperação plano for criado, selecione-o para abrir o modo de exibição de personalização de plano de recuperação.

    d.  Selecione **todos os grupos desligar**, clique em **Script**e escolha **Adicionar um script de lado primária antes do desligamento todos do grupo**.

    e.  Selecione a conta de automação (no qual você adicionou o runbooks) e selecione runbook **falhar sobre-StorSimple-contêineres de Volume** .

    f.  Clique em **grupo 1: Iniciar**, escolha **máquinas virtuais**e adicionar VMs que devem ser protegidos no plano de recuperação.

    g.  Clique em **grupo 1: Iniciar**, escolha **Script**e adicione os seguintes scripts em ordem como etapas **após 1 de grupo** .

    - Início-StorSimple--dispositivo Virtual runbook
    - Falha runbook sobre-StorSimple-contêineres de volume
    - Montagem volumes após failover runbook
    - Desinstale-custom--extensão script runbook

1.  Adicionar uma ação manual após os scripts de 4 acima na mesma **grupo 1: etapas POST** seção. Esta ação é o ponto em que você pode verificar se tudo está funcionando corretamente. Esta ação precisa ser adicionado somente como parte de falha de teste (somente escolha a **Failover testar a** caixa de seleção).

2.  Após a ação manual, adicione o script de limpeza usando o mesmo procedimento que você usou para os outros runbooks. Salve o plano de recuperação.

    > [AZURE.NOTE] Ao executar um failover de teste, você deve verificar tudo na etapa de ação manual porque os volumes de StorSimple que tinham clonagem no dispositivo de destino serão excluídos como parte da limpeza após a ação manual.

    ![](./media/storsimple-dr-using-asr/image7.png)

## <a name="perform-a-test-failover"></a>Realizar um failover de teste

Consulte o guia de companion [Solução DR do Active Directory](../site-recovery/site-recovery-active-directory.md) para considerações específicas para o Active Directory durante o failover de teste. A configuração de local não será afetada de forma alguma quando houver a falha de teste. Os volumes de StorSimple que estavam conectados para a máquina virtual locais são clonar para o dispositivo de nuvem StorSimple no Azure. Uma máquina virtual para fins de teste é exibida no Azure e os volumes clonados são anexados para a máquina virtual.

#### <a name="to-perform-the-test-failover"></a>Para executar o failover de teste

1.  No portal do Azure clássico, selecione seu Cofre de recuperação do site.

1.  Clique no plano de recuperação criado para o servidor de arquivos máquina virtual.

2.  Clique em **Testar Failover**.

3.  Selecione a rede virtual para iniciar o processo de failover de teste.

    ![](./media/storsimple-dr-using-asr/image8.png)

1.  Quando o ambiente secundário é para cima, você pode executar sua validações.

2.  Após as validações forem concluídas, clique em **Validações concluída**. O ambiente de teste de failover será limpo e a operação de TFO será concluída.

## <a name="perform-an-unplanned-failover"></a>Executar um failover não planejado

Durante um failover não planejado, os volumes de StorSimple são falhou para o dispositivo virtual, uma réplica máquina virtual será levada para cima no Azure e os volumes são anexados para a máquina virtual.

#### <a name="to-perform-an-unplanned-failover"></a>Para executar um failover não planejado

1.  No portal do Azure clássico, selecione seu Cofre de recuperação do site.

1.  Clique no plano de recuperação criado para máquina virtual do servidor de arquivos.

2.  Clique em **Failover** e selecione **Failover não planejado**.

    ![](./media/storsimple-dr-using-asr/image9.png)

1.  Selecione a rede de destino e, em seguida, clique no ícone de seleção ✓ para iniciar o processo de failover.

## <a name="perform-a-planned-failover"></a>Realizar um failover planejado

Durante um failover planejado, o local servidor de arquivos que máquina virtual desligar normalmente e um instantâneo de backup dos volumes em dispositivo StorSimple é feito na nuvem. Os volumes de StorSimple são falhou para o dispositivo virtual, uma réplica máquina virtual é exibida no Azure e os volumes são anexados para a máquina virtual.

#### <a name="to-perform-a-planned-failover"></a>Para realizar um failover planejado

1.  No portal do Azure clássico, selecione seu Cofre de recuperação do site.

1.  Clique no plano de recuperação criado para o servidor de arquivos máquina virtual.

2.  Clique em **Failover** e selecione **Failover planejado**.

3.  Selecione a rede de destino e, em seguida, clique no ícone de seleção ✓ para iniciar o processo de failover.

## <a name="perform-a-failback"></a>Executar um failback

Durante failback, contêineres de volume StorSimple são falhou novamente para o dispositivo físico após um backup é feito.

#### <a name="to-perform-a-failback"></a>Para executar um failback

1.  No portal do Azure clássico, selecione seu Cofre de recuperação do site.

1.  Clique no plano de recuperação criado para o servidor de arquivos máquina virtual.

2.  Clique em **Failover** e selecione **planejada failover** ou **failover não planejado**.

3.  Clique em **alterar a direção**.

4.  Selecione as opções de criação de máquina virtual e sincronização de dados apropriado.

5.  Clique no ícone de seleção ✓ para iniciar o processo de failback.

    ![](./media/storsimple-dr-using-asr/image10.png)

## <a name="best-practices"></a>Práticas recomendadas

### <a name="capacity-planning-and-readiness-assessment"></a>Avaliação de planejamento e preparação da capacidade


#### <a name="hyper-v-site"></a>Site de Hyper-V

Use a [ferramenta de Planejador de capacidade de usuário](http://www.microsoft.com/download/details.aspx?id=39057) para o servidor, armazenamento e infraestrutura de rede para o seu ambiente de réplica do Hyper-V de design.

#### <a name="azure"></a>Azure

Você pode executar a [ferramenta de avaliação de prontidão de máquina Virtual do Azure](http://azure.microsoft.com/downloads/vm-readiness-assessment/) em VMs para garantir que eles são compatíveis com VMs do Azure e serviços de recuperação de Site do Azure. A ferramenta de avaliação de prontidão verifica as configurações de máquina virtual e avisa quando configurações são incompatíveis com o Azure. Por exemplo, ele emite um aviso se uma unidade c: for maior que 127 GB.


Planejamento da capacidade consiste em pelo menos dois processos importantes:

-   Mapeamento local VMs Hyper-V em tamanhos de máquina virtual do Azure (como A6, A7, A8 e A9).

-   Determinando a largura de banda de Internet necessária.

## <a name="limitations"></a>Limitações

- Atualmente, apenas 1 StorSimple dispositivo pode ser falhou (para um único dispositivo de nuvem StorSimple). O cenário de um servidor de arquivos que se expande por vários dispositivos de StorSimple ainda não é suportado.

- Se você receber um erro ao ativar a proteção para uma máquina virtual, certifique-se de que você tenha desconectado os destinos iSCSI.

- Todos os contêineres de volume que foram agrupados por causa de políticas de backup abranger contêineres de volume serão falhar juntos.

- Todos os volumes nos contêineres de volume que você escolheu irá falhar.

- Volumes que constituem a mais de 64 TB não podem falhar porque a capacidade máxima de um único dispositivo de nuvem StorSimple é 64 TB.

- Se o failover planejadas falha e as VMs são criadas no Azure, em seguida, limpar as VMs. Em vez disso, faça um failback. Se você excluir as VMs, VMs local não podem ser ativadas novamente.

- Após um failover, se você não conseguir ver os volumes, vá para as VMs, abra o gerenciamento de disco, examine novamente os discos e colocá-los online.

- Em alguns casos, as letras de unidade no site do DR podem ser diferentes do letras local. Nesse caso, você precisará corrigir o problema manualmente após o failover.

- Autenticação multifator deve ser desativada para a credencial Azure inseridas na conta de automação como um ativo. Se essa autenticação não estiver desabilitada, scripts não poderão ser executadas automaticamente e o plano de recuperação falhará.

- Tempo limite do trabalho failover: StorSimple o script irá expirar se o failover de contêineres de volume levar mais tempo do que o limite de recuperação de Site do Azure por script (atualmente 120 minutos).

- Tempo limite do trabalho de backup: StorSimple o script atinge o tempo limite se o backup de volumes levar mais tempo do que o limite de recuperação de Site do Azure por script (atualmente 120 minutos).
 
    > [AZURE.IMPORTANT] Executar o backup manualmente a partir do portal do Azure e execute o plano de recuperação novamente.

- Clonar tempo limite do trabalho: StorSimple o script atinge o tempo limite se clonagem de volumes levar mais tempo do que o limite de recuperação de Site do Azure por script (atualmente 120 minutos).

- Erro de sincronização de horário: StorSimple o erro dizendo que os backups foram malsucedidos mesmo que o backup seja bem-sucedida no portal de scripts. Uma possível causa pode ser que a hora do equipamento StorSimple esteja fora de sincronia com a hora atual no fuso horário.
 
    > [AZURE.IMPORTANT] Sincronize a hora do dispositivo com a hora atual no fuso horário.

- Erro de failover aparelho: StorSimple o script pode falhar se houver um failover de dispositivo quando o plano de recuperação está em execução.
    
    > [AZURE.IMPORTANT] Execute novamente o plano de recuperação após o failover aparelho estiver concluído.

## <a name="summary"></a>Resumo

Usando o Azure recuperação do Site, você pode criar um plano de recuperação de desastres automatizada concluída para um servidor de arquivos máquina virtual tendo hospedados no StorSimple armazenamento de compartilhamentos de arquivos. Você pode iniciar o failover no segundos de praticamente qualquer lugar em caso de uma interrupção e obter o aplicativo em funcionamento em poucos minutos.
