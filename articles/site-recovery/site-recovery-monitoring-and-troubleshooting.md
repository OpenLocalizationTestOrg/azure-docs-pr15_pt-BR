<properties
    pageTitle="Monitorar e solucionar problemas de proteção para máquinas virtuais e servidores físicos | Microsoft Auzre" 
    description="Recuperação de Site Azure coordenadas a replicação, o failover e a recuperação de máquinas virtuais localizados no servidores locais do Azure ou um data center secundário. Use este artigo para monitorar e solucionar problemas de proteção VMM ou Site de Hyper-V." 
    services="site-recovery" 
    documentationCenter="" 
    authors="anbacker" 
    manager="mkjain" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/13/2016"    
    ms.author="rajanaki"/>
    
# <a name="monitor-and-troubleshoot-protection-for-virtual-machines-and-physical-servers"></a>Monitorar e solucionar problemas de proteção para máquinas virtuais e servidores físicos

Este guia de solução de problemas e monitoramento permite que você saiba a integridade de replicação de rastreamento e técnicas de solução de problemas para recuperação de Site do Azure.

## <a name="understanding-the-components"></a>Noções básicas sobre os componentes

### <a name="vmwarephysical-site-deployment-for-replication-between-on-premises-and-azure"></a>Implantação de Site de VMware/física para replicação entre locais e do Azure.
Configurar o DR entre máquina de VMware/física local; Servidor de configuração, destino de mestre e servidor processo precisa configurado. Ao ativar a proteção do servidor de origem Azure recuperação de Site irá instalar serviço de mobilidade. Postagem locais interrupção depois que o servidor de origem falha-acima do Azure, necessidades de clientes para configurar um servidor de processo no Azure e um servidor de destino de mestre local para proteger o servidor de origem para o local recriado. 

![Implantação de Site de VMware/física para replicação entre locais e do Azure](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### <a name="vmm-site-deployment-for-replication-between-on-premises-site"></a>Implantação de Site VMM para replicação entre o site local.

Como parte da configuração DR entre dois local na; Provedor de recuperação de Site Azure precisa ser baixadas e instaladas no servidor VMM. Provedor de necessidades de conectividade com a internet para garantir que todas as operações acionadas a partir do Portal Azure obtém traduzido para operações de locais como habilitar proteção, desligamento lado primária virtual máquinas como parte de failovers etc.

![Implantação de Site VMM para replicação entre o site local](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### <a name="vmm-site-deployment-for-replication-between-on-premises--azure"></a>Implantação de Site VMM para replicação entre locais & Azure.

Como parte da configuração DR entre locais & Azure; Provedor de recuperação de Site Azure precisa ser baixadas e instaladas no servidor VMM juntamente com o agente de serviços de recuperação do Azure que deve ser instalado em cada host Hyper-V. Para obter mais informações, consulte o [Site de Noções básicas sobre a proteção do Azure](./site-recovery-understanding-site-to-azure-protection.md) .

![Implantação de Site VMM para replicação entre locais e do Azure](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### <a name="hyper-v-site-deployment-for-replication-between-on-premises--azure"></a>Implantação de Site de Hyper-V para replicação entre locais e do Azure

Isso é igual a de implantação do VMM – somente diferença sendo provedor & agente obtém instalada no host Hyper-V em si. Para obter mais informações, consulte o [Site de Noções básicas sobre a proteção do Azure](./site-recovery-understanding-site-to-azure-protection.md) .

## <a name="monitor-configuration-protection-and-recovery-operations"></a>Monitorar operações de configuração, proteção e recuperação

Cada operação em ASR obtém auditada e é controlada sob a guia "Trabalhos". No caso de qualquer configuração, proteção ou erro de recuperação navegue até a guia trabalhos e ver se há alguma falha.

![Monitorar operações de configuração, proteção e recuperação](media/site-recovery-monitoring-and-troubleshooting/image3.png)

Depois de encontrar falhas sob o modo de exibição de trabalhos, selecione o trabalho e clique em detalhes do erro do trabalho.

![Monitorar operações de configuração, proteção e recuperação](media/site-recovery-monitoring-and-troubleshooting/image4.png)

Os detalhes do erro ajudará você a identificar possível causa e recomendação para o problema.

![Monitorar operações de configuração, proteção e recuperação](media/site-recovery-monitoring-and-troubleshooting/image5.png)

No caso acima parece haver outra operação que está em andamento devido ao qual a configuração de proteção está falhando. Certifique-se de que você resolver o problema de acordo com as a recomendação – lá depois clique RESART para iniciar novamente a operação.

![Monitorar operações de configuração, proteção e recuperação](media/site-recovery-monitoring-and-troubleshooting/image6.png)

Opção de reiniciar não está disponível para todas as operações – para aqueles que não tem a opção de reiniciar navegar de volta para o objeto e refazer a operação novamente. Todos os trabalhos podem ser cancelados em qualquer momento enquanto usando o botão Cancelar em andamento.

![Monitorar operações de configuração, proteção e recuperação](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## <a name="monitor-replication-health-for-virtual-machine"></a>Monitorar a saúde de replicação para máquina virtual

Provedor de ASR central & monitoramento por meio do Portal do Azure para cada uma das entidades protegidas remoto. Navegue até os itens de protegido lá depois selecione VMM NUVENS ou grupos de proteção. Guia de NUVENS do VMM é apenas para implantações de VMM com base e todos os outros cenários tem as entidades protegidas guia de grupos de proteção.

![Monitorar a saúde de replicação para máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image8.png)

Lá depois selecione a entidade protegida em nuvem respectiva ou o grupo de proteção. Depois que você selecionar a entidade protegida todos os permitidos operações são mostradas no painel inferior.

![Monitorar a saúde de replicação para máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image9.png)

Como mostrado acima no caso a máquina virtual que integridade é fundamental – você pode clicar no botão Detalhes do erro na parte inferior para ver o erro. Com base em "causas possíveis" e "Recomendação" mencionada resolver o problema.

![Monitorar a saúde de replicação para máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Monitorar a saúde de replicação para máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image11.png)

Observação: Se houver quaisquer operações ativas que estão em andamento ou falha, em seguida, navegue até o modo de exibição de trabalhos conforme mencionado anteriormente para exibir o erro específico de trabalho.

## <a name="troubleshoot-on-premises-hyper-v-issues"></a>Solucionar problemas de Hyper-V de local

Conectar o console do Gerenciador de Hyper-V no local, selecione a máquina virtual e ver a integridade de replicação.

![Solucionar problemas de Hyper-V de local](media/site-recovery-monitoring-and-troubleshooting/image12.png)

Nesse caso a *Integridade de replicação* está sendo indicado como crítica – *Integridade de replicação do modo de exibição* para ver os detalhes.

![Solucionar problemas de Hyper-V de local](media/site-recovery-monitoring-and-troubleshooting/image13.png)

Para casos onde replicação está pausada da máquina virtual, clique com botão direito selecione *replicação*->*replicação de currículo*
![solucionar problemas de Hyper-V local](media/site-recovery-monitoring-and-troubleshooting/image19.png)

No caso de máquina virtual migra um novo host Hyper-V (dentro do cluster ou um aparelho autônomo), que tenha sido configurado por meio de recuperação automatizada do sistema, replicação da máquina virtual não ser afetada. Verifique se o novo host Hyper-V atende a todos os por-requisitos e é configurado usando a recuperação automatizada do sistema.

### <a name="event-log"></a>Log de eventos

| Fontes de evento                | Detalhes                                                                                                                                                                                           |
|-------------------------  |:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |
| **Aplicativos e serviços Logs/Microsoft/VirtualMachineManager / / Admin do servidor** (VMM Server)   |  Isso fornece log útil para muitos problemas VMM diferentes de solução de problemas. |
| **Aplicativos e serviços Logs/MicrosoftAzureRecoveryServices/replicação** (Host hyper-V)   | Isso fornece útil log para solucionar problemas de agente de serviços de recuperação do Microsoft Azure muitos. <br/> ![Fonte de evento para host Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **Aplicativos e serviços Logs/Microsoft/Azure Site recuperação/provedor/operacionais** (Host hyper-V)   | Isso fornece útil log para solucionar problemas de serviço de recuperação de Site do Microsoft Azure muitos. <br/> ![Fonte de evento para host Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **Aplicativos e serviços Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** (Host hyper-V) | Isso fornece útil log para solucionar problemas de gerenciamento de máquina virtual de Hyper-V muitos. <br/> ![Fonte de evento para host Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |


### <a name="hyper-v-replication-logging-options"></a>Opções de log de replicação de Hyper-V

Todos os eventos relacionados à Hyper-V Replica estiver conectados os Hyper-V-VMMS\\administrador log localizado em **Logs de aplicativos e serviços\\Microsoft\\Windows**. Além disso, um log analítico pode ser habilitado para Hyper-V-VMMS. Para habilitar o log, primeiro faça os logs analíticos e de depuração visível no Visualizador de eventos. Abra o Visualizador de eventos, em seguida, no **menu Exibir**, clique em **Mostrar analíticos e logs de depuração**.

![Solucionar problemas de Hyper-V de local](media/site-recovery-monitoring-and-troubleshooting/image14.png)

Um log analítico está visível em Hyper-V-VMMS

![Solucionar problemas de Hyper-V de local](media/site-recovery-monitoring-and-troubleshooting/image15.png)

No painel de **ações** , clique em **Habilitar Log**. Quando ativada, ela aparecerá no **Monitor de desempenho** como uma sessão de rastreamento de evento localizado em **conjuntos de Coletores de dados.**

![Solucionar problemas de Hyper-V de local](media/site-recovery-monitoring-and-troubleshooting/image16.png)

Para exibir as informações coletadas, primeiro interromper a sessão de rastreamento desabilitando o log e, em seguida, salve o log e reabri-lo no Visualizador de eventos ou use outras ferramentas para convertê-lo conforme desejado.



## <a name="reaching-out-for-microsoft-support"></a>Alcançando-out Microsoft Support

### <a name="log-collection"></a>Coleta de log

Para proteção de Site VMM, consulte [coleta de Log de recuperação automatizada do sistema usando a ferramenta de plataforma de diagnóstico de suporte (SDP)](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) para coletar os registros necessários.

Proteção de Site de Hyper-V, baixe a [ferramenta](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) & executá-la no host Hyper-V para coletar os logs.

Cenários de VMware/física, consulte [Coleta de Log de recuperação do Azure Site VMware e físico proteção de site](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) para coletar os registros necessários.

Ferramenta coleta os logs localmente em uma subpasta nomeada aleatoriamente em **%LocalAppData%\ElevatedDiagnostics**

![Exemplo de etapas mostradas da proteção de site de Hyper-V.](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### <a name="opening-a-support-ticket"></a>Abrindo um tíquete

Para aumentar o tíquete para recuperação automatizada do sistema, comunique-se para dar suporte a Azure usando a URL em <http://aka.ms/getazuresupport>

## <a name="kb-articles"></a>Artigos

-   [Como preservar a letra protegidas máquinas virtuais que são falhou ou migrados para o Azure](http://support.microsoft.com/kb/3031135)
-   [Como gerenciar locais para uso de largura de banda de rede de proteção do Azure](https://support.microsoft.com/kb/3056159)
-   [ASR: erro de "o recurso de cluster não pôde ser encontrado" quando você tenta ativar a proteção para uma máquina virtual](http://support.microsoft.com/kb/3010979)
-   [Entender & réplica Hyper-V guia de solução de problemas](http://www.microsoft.com/en-in/download/details.aspx?id=29016) 

## <a name="common-asr-errors-and-their-resolutions"></a>Erros comuns de recuperação automatizada do sistema e suas soluções

Abaixo estão os erros comuns que você pode pressionar e suas soluções. Cada erro é documentada em uma página WIKI separada.

### <a name="general"></a>Geral
-   <span style="color:green;">Novo</span> Trabalhos de [Falha com o erro "uma operação está em andamento." Erro 505, 514, 532](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
-   <span style="color:green;">Novo</span> Trabalhos de [Falha com o erro "O servidor não está conectado à Internet". Erro 25018](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### <a name="setup"></a>Configuração
-   [O servidor do VMM não pode ser registrado devido a um erro interno. Consulte o modo de exibição de trabalhos no Portal de recuperação do Site para obter mais detalhes sobre o erro. Execute a instalação novamente para registrar o servidor.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
-   [Não é possível estabelecer uma conexão ao Cofre Gerenciador de recuperação de Hyper-V. Verifique se as configurações de proxy ou tente novamente mais tarde.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### <a name="configuration"></a>Configuração
-   [Não é possível criar o grupo de proteção: Ocorreu um erro ao recuperar a lista de servidores.](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
-   [Host Hyper-V cluster contém pelo menos um adaptador de rede estática ou nenhum adaptadores conectados estão configurados para usar o DHCP.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
-   [VMM não tem permissões para concluir uma ação](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
-   [Não é possível selecionar a conta de armazenamento na assinatura durante a configuração de proteção](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### <a name="protection"></a>Proteção
- <span style="color:green;">Novo</span> Falha de [Ativar proteção com erro "proteção não pôde ser configurada para a máquina virtual". Erro 60007, 40003](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
- <span style="color:green;">Novo</span> Falha de [Ativar proteção com erro "proteção não pôde ser habilitada para a máquina virtual." Erro 70094](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
- <span style="color:green;">Novo</span> Erro de migração ao vivo [23848 - máquina virtual vier a ser movida usando tipo Live. Isso pode dividir o status de proteção de recuperação da máquina virtual.](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx) 
- [Habilitar proteção falhou, pois o agente não instalado na máquina host](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
- [Um host adequado para a máquina virtual de réplica não pode ser encontrado - devido a recursos de computação baixa](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
- [Um host adequado para a máquina virtual de réplica não pode ser encontrado - devido a nenhuma rede lógica anexado](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
- [Não consigo me conectar ao computador host réplica - não foi possível estabelecer conexão](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)


### <a name="recovery"></a>Recuperação
- VMM não consegue concluir a operação de host-
    -   [Alternar para o ponto de recuperação selecionado para máquina virtual: geral erro de acesso negado.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
    -   [Hyper-V Falha ao alternar para o ponto de recuperação selecionado para máquina virtual: operação anulada tente um ponto de recuperação mais recente. (0x80004004)](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
    -   Uma conexão com o servidor não pôde ser estabelecida (0x00002EFD)
        -   [Falha ao habilitar replicação reversa para máquina virtual Hyper-V](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
        -   [Falha ao habilitar replicação para máquina virtual máquina virtual Hyper-V](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
    -   [Não foi possível confirmar failover para máquina virtual](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
-   [O plano de recuperação contém máquinas virtuais que não estão prontas para failover planejado](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
-   [A máquina virtual não está pronta para failover planejado](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   [Máquina virtual não está sendo executado e não está desligada](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
-   [Fora da operação de banda aconteceu em uma máquina virtual e failover de confirmação falhou](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   Testar Failover
    -   [Failover não pôde ser iniciado como failover de teste está em andamento](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)
-   <span style="color:green;">Novo</span>  Failover atinge o tempo limite com 'PreFailoverWorkflow tarefa WaitForScriptExecutionTaskTimeout' devido às configurações no grupo de segurança de rede associados a máquina Virtual ou a sub-rede ao qual a máquina pertence. Consulte ['PreFailoverWorkflow task WaitForScriptExecutionTaskTimeout'](https://aka.ms/troubleshoot-nsg-issue-azure-site-recovery) para obter detalhes.


### <a name="configuration-server-process-server-master-target"></a>Configuração Server, servidor de processo, mestre de destino
Servidor de configuração (CS), servidor de processo (PS), mestre Targer (TA)
-   [O host ESXi em que o PS/CS está hospedado como uma máquina virtual falha com uma tela roxa de morte.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### <a name="remote-desktop-troubleshooting-after-failover"></a>Solução de problemas após failover da área de trabalho remota
-   Muitos clientes enfrentam problemas conectem a falha pela máquina virtual no Azure. [Usar o documento de solução de problemas para RDP para a máquina virtual](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)

#### <a name="adding-a-public-ip-on-a-resource-manager-virtual-machine"></a>Adicionando um IP público em um computador de virtual do Gerenciador de recursos
Se o botão **Conectar** no portal estiver acinzentado e você não estiver conectado ao Azure por meio de um roteiro de Express ou conexão de-to-Site VPN, você precisa criar e atribuir um endereço IP público de sua máquina virtual antes de poder usar RDP/SSH. Siga as etapas abaixo para adicionar um público IP na interface de rede da máquina virtual.  

![Falha ao adicionar um público IP na interface de rede de máquina virtual](media/site-recovery-monitoring-and-troubleshooting/createpublicip.gif)