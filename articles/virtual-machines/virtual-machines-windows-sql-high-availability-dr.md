<properties
    pageTitle="Alta disponibilidade e recuperação de dados para o SQL Server | Microsoft Azure"
    description="Uma discussão dos vários tipos de estratégias HADR para o SQL Server em máquinas virtuais do Azure em execução."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/20/2016"
    ms.author="MikeRayMSFT" />

# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Alta disponibilidade e recuperação de desastres para o SQL Server em máquinas virtuais do Azure

## <a name="overview"></a>Visão geral

Microsoft Azure VMs (máquinas virtuais) com o SQL Server pode ajudar a reduzir o custo de uma solução de banco de dados de recuperação (HADR) desastres e disponibilidade alta. A maioria das soluções de HADR do SQL Server são suportados no Azure máquinas virtuais, como somente do Azure e soluções híbridas. Em uma solução somente Azure, todo o sistema HADR é executado no Azure. Em uma configuração híbrida, parte da solução é executado no Azure e o outro parte é executado no local em sua organização. A flexibilidade do ambiente Azure permite mover parcialmente ou completamente para Azure para atender a requisitos orçamento e HADR seus sistemas de banco de dados do SQL Server.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="understanding-the-need-for-an-hadr-solution"></a>Noções básicas sobre a necessidade de uma solução HADR

É até você para assegurar que o seu sistema de banco de dados possui os recursos HADR que requer o contrato de nível de serviço (SLA). O fato de que Azure fornece mecanismos de alta disponibilidade, como o serviço de reparo para serviços de nuvem e detecção de recuperação de falhas para máquinas virtuais, próprio não garante que pode atender SLA desejado. Esses mecanismos protegem a alta disponibilidade das VMs, mas não a alta disponibilidade do SQL Server em execução nas VMs. É possível para a instância do SQL Server falha enquanto a máquina virtual está online e íntegra. Além disso, mesmo a alta disponibilidade mecanismos fornecidos pelo Azure permitem para o tempo de inatividade das VMs devido a eventos como recuperação de atualizações do sistema operacional ou falhas de hardware e software.

Além disso, armazenamento redundantes de localização geográfica (GRS) no Azure, que é implementado com um recurso chamado replicação geográfica, pode não ser uma adequado solução de recuperação dos bancos de dados. Como replicação geográfica envia dados de forma assíncrona, atualizações recentes podem ser perdidas em caso de desastres. Obter mais informações sobre as limitações de replicação geográfica estão cobertos na seção [localização geográfica replicação não tem suporte para arquivos de log e de dados em discos separados](#geo-replication-support) .

## <a name="hadr-deployment-architectures"></a>Arquiteturas de implantação de HADR

Tecnologias do SQL Server HADR que são compatíveis com o Azure incluem:

- [Sempre em grupos de disponibilidade](https://technet.microsoft.com/library/hh510230.aspx)
- [Espelhamento de banco de dados](https://technet.microsoft.com/library/ms189852.aspx)
- [Envio de log](https://technet.microsoft.com/library/ms187103.aspx)
- [Backup e restauração com o serviço de armazenamento de blob do Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx)
- [Sempre em instâncias de Cluster de Failover](https://technet.microsoft.com/library/ms189134.aspx)

É possível combinar as tecnologias juntos para implementar uma solução de SQL Server com alta disponibilidade e recursos de recuperação de desastres. Dependendo da tecnologia que você usa, uma implantação híbrida pode exigir um túnel VPN com a rede virtual Azure. As seções a seguir mostram algumas das arquiteturas de implantação de exemplo.

## <a name="azure-only-high-availability-solutions"></a>Somente Azure: soluções de alta disponibilidade

Você pode ter uma solução de alta disponibilidade para seus bancos de dados do SQL Server no Azure usando sempre em grupos de disponibilidade ou espelhamento de banco de dados.

| Tecnologia                               | Arquiteturas de exemplo                    |
| ---------------------------------------- | ---------------------------------------- |
| **Sempre em grupos de disponibilidade**        | Todas as réplicas de disponibilidade em execução no Azure VMs para alta disponibilidade dentro da mesma região. Você precisa configurar um controlador de domínio máquina virtual, porque clusters de Failover do Windows Server (WSFC) requer um domínio Active Directory.<br/> ![Sempre em grupos de disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>Para obter mais informações, consulte [Configurar sempre na disponibilidade de grupos no Azure (interface gráfica)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Sempre em instâncias de Cluster de Failover** | Instâncias de Cluster de failover (FCI), que exigem armazenamento compartilhado, podem ser criadas de maneiras diferentes 2.<br/><br/>1. um FCI em um WSFC de dois nós em execução no Azure VMs com armazenamento compatível com uma solução de cluster de terceiros. Para um exemplo específico que usa SIOS DataKeeper, consulte [alta disponibilidade para um compartilhamento de arquivos usando o software de terceiros 3º SIOS Datakeeper e WSFC](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>2. um FCI em um WSFC de dois nós em execução no Azure VMs com iSCSI remotos destino compartilhado armazenamento de bloco via rota expressa. Por exemplo, NetApp particular armazenamento (NPS) expõe um destino iSCSI via rota expressa com Equinix em VMs do Azure.<br/><br/>Para armazenamento compartilhado de terceiros e soluções de replicação de dados, você deve contatar o fornecedor para quaisquer problemas relacionados ao acesso a dados no failover.<br/><br/>Observe que usar FCI na parte superior de [armazenamento de arquivos do Azure](https://azure.microsoft.com/services/storage/files/) ainda não é suportada, pois essa solução não utiliza armazenamento Premium. Estamos trabalhando para oferecer suporte a esse breve. |

## <a name="azure-only-disaster-recovery-solutions"></a>Somente Azure: soluções de recuperação de desastres

Você pode ter uma solução de recuperação de seus bancos de dados do SQL Server no Azure usando sempre em grupos de disponibilidade, espelhamento de banco de dados ou backup e restauração com blobs de armazenamento.

| Tecnologia                               | Arquiteturas de exemplo                    |
| ---------------------------------------- | ---------------------------------------- |
| **Sempre em grupos de disponibilidade**        | Réplicas de disponibilidade executando vários data centers em VMs do Azure para recuperação de dados. Essa solução entre região protege contra interrupção completo do site. <br/> ![Sempre em grupos de disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>Dentro de uma região, todas as réplicas devem estar dentro do mesmo serviço de nuvem e o mesmo VNet. Porque cada região terá um VNet separada, essas soluções exigem VNet à VNet conectividade. Para obter mais informações, consulte [Configurar uma VPN to-Site no portal de clássico do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md). |
| **Espelhamento de banco de dados**                   | Capital e espelho e servidores que executam em diferentes dos data centers de recuperação de dados. Você deve implantar usando certificados de servidor porque um domínio active directory não pode abranger vários data centers.<br/>![Espelhamento de banco de dados](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Backup e restauração com o serviço de armazenamento de blob do Microsoft Azure** | Bancos de dados de produção backup diretamente ao armazenamento de blob em um data center diferente para recuperação de dados.<br/>![Backup e restauração](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>Para obter mais informações, consulte [Backup e restauração para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md). |

## <a name="hybrid-it-disaster-recovery-solutions"></a>TI híbridos: Soluções de recuperação de desastres

Você pode ter uma solução de recuperação para seus bancos de dados do SQL Server em um ambiente de TI híbrida usando sempre em grupos de disponibilidade, espelhamento de banco de dados, envio de log e backup e restaurar com armazenamento de blog Azure.

| Tecnologia                               | Arquiteturas de exemplo                    |
| ---------------------------------------- | ---------------------------------------- |
| **Sempre em grupos de disponibilidade**        | Algumas réplicas de disponibilidade em execução no Azure VMs e outras executando o local de recuperação de dados entre sites. O site de produção pode ser locais ou em um data center Azure.<br/>![Sempre em grupos de disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Como todas as réplicas de disponibilidade devem estar no mesmo cluster WSFC, cluster WSFC deve abranger ambas as redes (um cluster WSFC várias sub-redes). Essa configuração requer uma conexão VPN entre Azure e da rede local.<br/><br/>Para bem-sucedida recuperação de seus bancos de dados, você também deve instalar um controlador de domínio de réplica no local de recuperação de desastres.<br/><br/>É possível usar o Assistente para adicionar réplica no SSMS para adicionar uma réplica Azure a um sempre na disponibilidade grupo existente. Para obter mais informações, consulte Tutorial: estender sua sempre no grupo de disponibilidade no Azure. |
| **Espelhamento de banco de dados**                   | Um parceiro executado em uma máquina virtual do Azure e o outro executado local intersite de recuperação usando certificados de servidor. Parceiros não precisam estar no mesmo domínio do Active Directory e nenhuma conexão de VPN é necessária.<br/>![Espelhamento de banco de dados](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Outro banco de dados espelhamento cenário envolve um parceiro executado em uma máquina virtual do Azure e os outros em execução no local no mesmo domínio do Active Directory para recuperação de dados entre sites. É necessária uma [conexão VPN entre a rede virtual Azure e da rede local](../vpn-gateway/vpn-gateway-site-to-site-create.md) .<br/><br/>Para bem-sucedida recuperação de seus bancos de dados, você também deve instalar um controlador de domínio de réplica no local de recuperação de desastres. |
| **Envio de log**                         | Um servidor executando em uma máquina virtual do Azure e o outro executado local para recuperação de dados entre sites. O envio de log depende de compartilhamento de arquivo do Windows, portanto, uma conexão VPN entre a rede virtual Azure e da rede local é necessária.<br/>![Envio de log](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>Para bem-sucedida recuperação de seus bancos de dados, você também deve instalar um controlador de domínio de réplica no local de recuperação de desastres. |
| **Backup e restauração com o serviço de armazenamento de blob do Microsoft Azure** | Em locais bancos de dados de produção backup diretamente ao armazenamento de blob do Microsoft Azure para recuperação de dados.<br/>![Backup e restauração](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>Para obter mais informações, consulte [Backup e restauração para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Considerações importantes para SQL Server HADR no Azure

Azure VMs, armazenamento e redes têm características operacionais diferentes um local, não virtualizado infraestrutura. Uma implementação bem-sucedida de uma solução de HADR SQL Server no Azure requer que você compreender essas diferenças e criar sua solução para acomodá-los.

### <a name="high-availability-nodes-in-an-availability-set"></a>Nós de alta disponibilidade em um conjunto de disponibilidade

Conjuntos de disponibilidade no Azure permitem que você colocar os nós de alta disponibilidade em separado falhas domínios (FDs) e atualizar (UDs). Para VMs Azure deve ser posicionado no mesmo conjunto de disponibilidade, você deve implantá-los no serviço de nuvem do mesmo. Somente os nós no serviço de nuvem mesmo podem participar o mesmo conjunto de disponibilidade. Para obter mais informações, consulte [Gerenciar a disponibilidade das máquinas virtuais](virtual-machines-windows-manage-availability.md).

### <a name="wsfc-cluster-behavior-in-azure-networking"></a>Comportamento de cluster WSFC na rede do Azure

O serviço DHCP não compatível com RFC no Azure pode causar a criação de certas configurações de cluster WSFC falha, devido ao nome de rede do cluster sendo atribuído a um endereço IP duplicado, como o mesmo endereço IP como um de nós do cluster. Esse é um problema ao implementar sempre na disponibilidade de grupos, que depende do recurso WSFC.

Considere o cenário quando um cluster de dois nós for criado e colocado online:

1. Cluster fica online e, em seguida, NODE1 solicita um endereço IP atribuído dinamicamente para o nome de rede de cluster.

2. Nenhum endereço IP diferente de endereço IP do NODE1 é fornecido pelo serviço DHCP, como o serviço DHCP reconhece que a solicitação é proveniente de NODE1 em si.

3. O Windows detecta que um endereço duplicado é atribuído para NODE1 e o nome de rede do cluster e o grupo de cluster padrão não consegue ficar online.

4. Grupo cluster padrão move para o Nó2, que trata o endereço IP do NODE1 como endereço IP do cluster e traz grupo cluster padrão online.

5. Quando NODE2 tenta estabelecer conectividade com NODE1, pacotes direcionados para NODE1 nunca deixe NODE2 porque ele resolve o endereço IP do NODE1 a mesmo. NODE2 não pode estabelecer conectividade com NODE1, perder quorum e desliga o cluster.

6. Enquanto isso, NODE1 podem enviar pacotes para NODE2, mas não é possível responder NODE2. Node1 perder quorum e desliga o cluster.

Este cenário pode evitar designando um não utilizado endereço IP estático, como um endereço IP de link-local como 169.254.1.1, o nome de rede do cluster para trazer o nome de rede do cluster online. Para simplificar esse processo, consulte [Configurando Cluster de Failover Windows no Azure para sempre em grupos de disponibilidade](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Para obter mais informações, consulte [Configurar sempre na disponibilidade de grupos no Azure (interface gráfica)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Suporte de ouvinte do grupo de disponibilidade

Ouvintes de grupo de disponibilidade são suportados no Azure VMs executando o Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Esse suporte é feita possível pelo uso de pontos de extremidade balanceamento de carga habilitado nas VMs Azure que são nós de grupo de disponibilidade. Você deve seguir as etapas de configuração especial para os ouvintes trabalhar para ambos os aplicativos cliente que estejam em execução no Azure, bem como aquelas executando o local.

Há duas opções principais para configurar seu ouvinte: externo (público) ou interno. O ouvinte (público) externo usa internet opostas balanceador de carga e está associado com um público IP Virtual (VIP) que é acessível pela internet. Um ouvinte interno usa um balanceador de carga interna e oferecer suporte somente a clientes dentro da mesma rede Virtual. Para um tipo de Balanceador de carga, você deve habilitar direto servidor retornar. 

Se o grupo de disponibilidade abrange várias sub-redes Azure (como uma implantação que cruza regiões Azure), a cadeia de conexão do cliente deve incluir "**MultisubnetFailover = True**". Isso resulta em tentativas de conexão paralela para as réplicas as sub-redes diferentes. Para obter instruções sobre como configurar um ouvinte, consulte

- [Configurar um ouvinte ILB para sempre em grupos de disponibilidade no Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
- [Configurar um ouvinte externo para sempre em grupos de disponibilidade no Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).

Você pode ainda conectar a cada réplica de disponibilidade separadamente conectando-se diretamente para a instância do serviço. Além disso, como sempre em grupos de disponibilidade são compatíveis com espelhamento clientes do banco de dados, você pode se conectar a réplicas de disponibilidade, como espelhamento parceiros desde que as réplicas estão configuradas semelhante ao espelhamento de banco de dados do banco de dados:

- Uma réplica principal e uma réplica secundária

- A réplica secundária está configurada como não legível (**Legíveis secundário** opção definida como **não**)

Uma cadeia de conexão do cliente de exemplo que corresponde à configuração de semelhante espelhamento banco de dados usando o ADO.NET ou o SQL Server Native Client está abaixo:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Para obter mais informações sobre conectividade de cliente, consulte:

- [Usando palavras-chave de cadeia de Conexão com o SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
- [Conectar clientes a um banco de dados espelhamento sessão (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
- [Conectando ao ouvinte de grupo de disponibilidade de TI híbridos](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
- [Grupo de disponibilidade ouvintes, conectividade de cliente e (SQL Server) de reserva de aplicativo](https://technet.microsoft.com/library/hh213417.aspx)
- [Usando cadeias de caracteres de Conexão de espelhamento de banco de dados com grupos de disponibilidade](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latência de rede em TI híbridos

Você deve implantar sua solução HADR com a suposição de que pode haver períodos de tempo com alta latência de rede entre sua rede local e o Azure. Ao implantar réplicas Azure, você deve usar confirmação assíncrona em vez de confirmação síncrona para o modo de sincronização. Quando Implantando espelhamento servidores de banco de dados local tanto no Azure, use o modo de alto desempenho em vez do modo de segurança alta.

### <a name="geo-replication-support"></a>Suporte para replicação geográfica

Replicação geográfica no Azure discos não suporta o arquivo de dados e o arquivo de log do mesmo banco de dados sejam armazenados em discos separados. GRS replica alterações em cada disco independentemente e assíncrona. Esse mecanismo garante a ordem de gravação dentro de um único disco na cópia replicado geográfica, mas não em replicado geográfica cópias de vários discos. Se você configurar um banco de dados para armazenar seu arquivo de dados e seu arquivo de log em discos separados, os discos recuperados após um desastre podem conter uma cópia mais atualizada do arquivo de dados que o arquivo de log, que divide o registro com previsão de escrita no SQL Server e as propriedades ACID das transações. Se você não tem a opção para desabilitar replicação geográfica na conta de armazenamento, você deve manter todos os dados e arquivos de log para um determinado banco de dados no mesmo disco. Se você deve usar mais de um disco devido ao tamanho do banco de dados, você precisa implantar uma das soluções de recuperação de desastres listadas acima para assegurar a redundância de dados.

## <a name="next-steps"></a>Próximas etapas

Se você precisa criar uma máquina virtual Azure com o SQL Server, consulte [um SQL Server Virtual Machine no Azure de provisionamento](virtual-machines-windows-portal-sql-server-provision.md).

Para obter o melhor desempenho do SQL Server em execução em uma máquina virtual do Azure, consulte as orientações no [Desempenho práticas recomendadas para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md).

Para outros tópicos relacionados a execução do SQL Server no Azure VMs, consulte [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Outros recursos

- [Instalar uma nova floresta do Active Directory do Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
- [Criar WSFC Cluster para sempre em grupos de disponibilidade na máquina virtual Azure](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)
