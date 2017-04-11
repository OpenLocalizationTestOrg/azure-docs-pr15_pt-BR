<properties
    pageTitle="Executar a ferramenta de Planejador de capacidade de Hyper-V para recuperação de Site | Microsoft Azure"
    description="Este artigo contém instruções para usar a ferramenta de Planejador de capacidade de Hyper-V for recuperação de Site do Azure"
    services="site-recovery"
    documentationCenter="na"
    authors="rayne-wiselman"
    manager="jwhit"
    editor="" />
<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="07/12/2016"
    ms.author="raynew" />

# <a name="run-the-hyper-v-capacity-planner-tool-for-site-recovery"></a>Executar a ferramenta de Planejador de capacidade de Hyper-V para recuperação de Site

Como parte da sua implantação do Azure recuperação do Site, você precisará descobrir seus requisitos de largura de banda e replicação. A ferramenta de Planejador de capacidade de Hyper-V para recuperação de Site ajuda você a descobrir suas necessidades de replicação e largura de banda para replicação de máquina virtual do Hyper-V.


Este artigo descreve como executar a ferramenta de Planejador de capacidade de Hyper-V. Esta ferramenta deve ser usada juntamente com as outras ferramentas de planejamento de capacidade e informações descritas no [planejamento da capacidade de recuperação do Site](site-recovery-capacity-planner.md).


## <a name="before-you-start"></a>Antes de começar

Executar a ferramenta em um nó de servidor ou cluster Hyper-V em seu site principal. Para executar a ferramenta os servidores de host Hyper-V precisa:

- Sistema operacional: Windows Server® 2012 ou Windows Server® 2012 R2
- Memória: 20 MB (mínimo)
- CPU: sobrecarga de 5 por cento (mínimo)
- Espaço em disco: 5 MB (mínimo)

Antes de executar a ferramenta, você precisará preparar o site principal. Se você estiver replicar entre dois locais sites e que você deseja verificar a largura de banda, você precisará preparar um servidor de réplica também.


## <a name="step-1-prepare-the-primary-site"></a>Etapa 1: Preparar o site principal
1. No site principal, faça uma lista de todas as máquinas virtuais do Hyper-V que você deseja duplicar e os hosts/clusters Hyper-V em que estão localizados. A ferramenta pode executar cada vez para vários hosts autônomo, ou para um único cluster, mas não ambos juntos. Ele também precisa executar separadamente para cada sistema operacional, portanto você deve reunir e observe seus servidores Hyper-V da seguinte maneira:

  - Servidores do Windows Server® 2012 autônomo
  - Windows Server® 2012 clusters
  - Servidores de autônomos do Windows Server® 2012 R2
  - Clusters do Windows Server® 2012 R2

3. Habilite o acesso remoto ao WMI em todos os hosts Hyper-V e clusters. Execute esse comando em cada servidor/cluster para tornar-se de regras de firewall e permissões de usuário são definidas:

        netsh firewall set service RemoteAdmin enable

5. Habilite o monitoramento do desempenho em servidores e clusters, da seguinte maneira:

  - Abra o Firewall do Windows com o snap-in de **Segurança avançada** e, em seguida, habilitar as seguintes regras de entrada: **Acesso à rede COM + (DCOM-IN)** e todas as regras no **grupo de gerenciamento remoto do Log de eventos**.

## <a name="step-2-prepare-a-replica-server-on-premises-to-on-premises-replication"></a>Etapa 2: Preparar um servidor de réplica (local para replicação local)

Não é necessário fazer isso se você estiver replicar no Azure.

Recomendamos que você configurar o um único host Hyper-V como um servidor de recuperação para que uma máquina virtual fictícia pode ser replicada a ele para verificar a largura de banda.  Você pode pular esta, mas você não poderá medir a largura de banda, a menos que você fazê-lo.

1. Se você deseja usar um nó de cluster como a réplica configurar agente de Hyper-V Replica:

    - No **Gerenciador de servidores**, abra o **Gerenciador de Cluster de Failover**.
    - Conectar-se ao cluster, realce o nome do cluster e clique em **ações** > **Função configurar** para abrir o Assistente de alta disponibilidade.
    - Função **Selecione** clique **Corretor de réplica do Hyper-V**. No Assistente para fornecer um **nome NetBIOS** e **endereço IP** a ser usado como o ponto de conexão para o cluster (chamado de ponto de acesso do cliente). O **Agente de réplica do Hyper-V** serão configurados, resultando em um nome de ponto de acesso de cliente que você deve observar.
    - Verifique se a função do agente de réplica do Hyper-V vem com êxito online e pode falhar ao longo entre todos os nós do cluster. Para fazer isso, clique com botão direito a função, aponte para **Mover**e clique em **Selecionar nó**. Selecione um nó > **Okey**.
    - Se você estiver usando a autenticação baseada em certificado, verifique se cada nó de cluster e o cliente todos têm o certificado instalado do ponto de acesso.
2.  Habilite um servidor de réplica:

    - Para um cluster abrir o Gerenciador de Cluster de falha, conectar-se ao cluster e clique em **funções** > Selecionar função > **Configuração de replicação**s > **Habilitar este cluster como um servidor de réplica**. Observe que, se você estiver usando um cluster como a réplica você precisará ter a função de agente de réplica do Hyper-V presente no cluster no site principal também.
    - Para um servidor autônomo abra o Gerenciador de Hyper-V. No painel de **ações** , clique em **Configurações de Hyper-V** para o servidor que você deseja habilitar e, em **Configuração de replicação** , clique em **ativar este computador como um servidor de réplica**.
3. Configure autenticação:

    - Na **portas de autenticação e** selecione como autenticar o servidor primário e as portas de autenticação. Se você estiver usando o certificado, clique em **Selecionar certificado** para selecionar um. Use Kerberos se os hosts de Hyper-V primário e recuperação estejam no mesmo domínio ou domínios confiáveis. Use certificados para domínios diferentes ou uma implantação de grupo de trabalho.
    - Na seção **autorização e armazenamento** , permitir que **qualquer** servidor autenticado (principal) enviar dados de replicação para este servidor de réplica. Clique em **Okey** ou **Aplicar**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

    - Execute o **netsh http Mostrar estado do serviço** para verificar se o ouvinte está executando para a protocolo/porta especificada:  
4. Configure firewalls. Durante a instalação do Hyper-V são criadas regras de firewall para permitir o tráfego no padrão portas (HTTPS em 443, Kerberos em 80). Habilite essas regras da seguinte maneira:

        - Certificate authentication on cluster (443): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}**
        - Kerberos authentication on cluster (80): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}**
        - Certificate authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"**
        - Kerberos authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"**

## <a name="step-3-run-the-capacity-planner-tool"></a>Etapa 3: Executar a ferramenta de Planejador de capacidade

Depois de preparado seu local principal e configurar um servidor de recuperação, você pode executar a ferramenta.

1. [Baixe](https://www.microsoft.com/download/details.aspx?id=39057) a ferramenta de Microsoft Download Center.
2. Execute a ferramenta em um dos servidores primários (ou um de nós do cluster primário). Clique com botão direito no arquivo .exe e escolha **Executar como administrador**.
3. Antes de **começar** Especifica por quanto tempo você deseja coletar dados. Recomendamos que você executar a ferramenta durante as horas de produção para garantir que os dados sejam representante. Se você estiver tentando somente para validar a conectividade de rede, você pode coletar por apenas um minuto.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. Em **detalhes do site principal** especificar o nome do servidor ou FQDN para um host independente ou para um cluster especificar o FQDN do cliente do aceite ponto, nome do cluster ou qualquer nó no cluster e clique em **Avançar**. A ferramenta detecta automaticamente o nome do servidor que ele é executado em. A ferramenta seleciona VMs que possível monitorar para os servidores especificados.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. Em **Detalhes do Site de réplica** se você estiver replicar no Azure ou se você estiver replicar para um data center secundário e ainda não configurou a um servidor de réplica, selecione **Ignorar testes que envolvem o local de réplica**. Se você estiver replicar para um data center secundário e você configurou um tipo de réplica no FQDN do servidor autônomo ou o ponto de acesso do cliente para o cluster no **nome do servidor (ou) Hyper-V réplica agente Capitular**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. Nos **Detalhes de réplica estendido** habilite **Ignorar os testes envolvendo estendido réplica site**. Eles não são suportados pela recuperação de Site.
7. Em **Escolha VMs para duplicar** as ferramentas se conecta ao servidor ou cluster e exibe VMs e discos em execução no servidor primário, de acordo com as configurações que você especificou na página **Detalhes do Site principal** . Observe que VMs que já estiverem ativadas para replicação ou que não estejam executando não será exibida. Selecione as VMs para o qual você deseja coletar métricas. Selecionar os VHDs automaticamente coleta dados para as VMs muito.
9. Se você configurou um servidor de réplica ou cluster, **informações de rede** de especificar a largura de banda WAN aproximada você acha que será usado entre os locais primário e réplica e selecione os certificados caso tenha configurado autenticação de certificado.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. Em **Resumo de** configurações e clique em **Avançar** para começar a coleta de métricas. Status e o andamento de ferramenta é exibida na página de **Capacidade de calcular** . Quando a conclusão da ferramenta executando clique **Exibir relatório** para ir sobre a saída. Por padrão, relatórios e registros são armazenados em **%systemdrive%\Users\Public\Documents\Capacity Planejador**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)


## <a name="step-4-interpret-the-results"></a>Etapa 4: Interpretação dos resultados
Aqui estão as métricas importantes. Você pode ignorar métricas que não estão listadas aqui. Elas não são relevantes para a recuperação do Site.

### <a name="on-premises-to-on-premises-replication"></a>Local para replicação local
  - Impacto da replicação sobre computação do host principal, memória
  - Impacto da replicação no sistema principal, espaço de disco do armazenamento de hosts de recuperação, IOPS
  - Total largura de banda necessária para replicação delta (Mbps)
  - Largura de banda de rede observado entre host principal e o host de recuperação (Mbps)
  - Sugestão para o número ideal de transferências paralelas ativos entre os dois hosts/clusters

### <a name="on-premises-to-azure-replication"></a>Local para replicação Azure
  - Impacto da replicação sobre computação do host principal, memória
  - Impacto da replicação armazenamento espaço do host principal em disco, IOPS
  - Total largura de banda necessária para replicação delta (Mbps)

## <a name="more-resources"></a>Mais recursos

- Para obter informações detalhadas sobre a ferramenta ler o documento que acompanha o download da ferramenta.
- Assista a um passo a passo da ferramenta em de Keith Mayer [blog do TechNet](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx).
- [Obter os resultados](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md) do nosso desempenho testes de locais para replicação de Hyper-V de local



## <a name="next-steps"></a>Próximas etapas

Após terminar de planejamento da capacidade, você pode iniciar Implantando recuperação do Site:

- [Duplicar VMs Hyper-V em nuvens VMM para Azure](site-recovery-vmm-to-azure.md)
- [Duplicar VMs Hyper-V (sem VMM) para Azure](site-recovery-hyper-v-site-to-azure.md)
- [Duplicar VMs Hyper-V entre sites do VMM](site-recovery-vmm-to-vmm.md)
- [Duplicar VMs Hyper-V entre sites VMM com SAN](site-recovery-vmm-san.md)
- [Duplicar hyper-V VMs em único servidor VMM](site-recovery-single-vmm.md)