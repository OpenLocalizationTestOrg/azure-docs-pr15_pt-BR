<properties
   pageTitle="Resiliência para recuperação de perda de uma orientação de técnica de região Azure | Microsoft Azure"
   description="Artigo no Compreendendo e criação de aplicativos com tolerância a falhas flexível e altamente disponível, bem como planejamento de recuperação de dados"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-a-region-wide-service-disruption"></a>Orientações técnicas resiliência Azure: recuperação de uma interrupção do serviço de toda a região

Azure é dividido física e logicamente em unidades chamadas regiões. Uma região consiste em um ou mais dos data centers perto. No momento da redação deste artigo, o Azure tem vinte e quatro regiões do mundo.

Em casos raros, é possível que instalações em toda a região podem se tornar inacessíveis, por exemplo, devido a falhas de rede. Ou instalações podem ser perdidas inteiramente, por exemplo devido a um desastre natural. Esta seção explica os recursos do Azure para criar aplicativos que são distribuídos entre regiões. Tal distribuição ajuda a minimizar a possibilidade de que uma falha em uma região pode afetar outras regiões.

##<a name="cloud-services"></a>Serviços de nuvem

###<a name="resource-management"></a>Gerenciamento de recursos

Você pode distribuir instâncias de computação em regiões, criando um serviço de nuvem separada em cada região de destino e, em seguida, o pacote de implantação de publicação para cada serviço na nuvem. Entretanto, observe que a distribuição de tráfego entre os serviços de nuvem em diferentes regiões deve ser implementado pelo desenvolvedor de aplicativos ou com um serviço de gerenciamento de tráfego.

Determinar o número de instâncias de função reserva para implantar com antecedência para recuperação de dados é um aspecto importante de planejamento de capacidade. Ter uma implantação secundária completa garante que capacidade já estará disponível quando necessário; No entanto, isso efetivamente dobra o custo. Um padrão comum é ter uma pequena, secundária implantação, grande o suficiente para executar serviços essenciais. Essa implantação secundária pequena é uma boa ideia, ambos para reservar capacidade e para testar a configuração do ambiente secundário.

>[AZURE.NOTE]A cota de assinatura não é uma garantia de capacidade. A cota é simplesmente um limite de crédito. Para garantir a capacidade, o número necessário de funções deve ser definido no modelo de serviço e as funções devem ser implantadas.

###<a name="load-balancing"></a>Balanceamento de carga

Para carregar saldo tráfego regiões requer uma solução de gerenciamento de tráfego. Azure fornece [Gerenciador de tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/). Você também pode tirar proveito dos serviços de terceiros que fornecem recursos de gerenciamento de tráfego semelhante.

###<a name="strategies"></a>Estratégias

Muitos estratégias alternativas estão disponíveis para implementar computação distribuída em regiões. Esses devem ser adaptados para as necessidades específicas de negócios e circunstâncias do aplicativo. Em um alto nível, as abordagens podem ser divididas nas categorias a seguir:

  * __Reimplantar em desastres__: nesta abordagem o aplicativo é redistribuído do zero no momento do desastre. Isso é adequado para aplicativos não-críticos que não exigem um tempo de recuperação garantia.

  * __Quente reserva (ativo/passivo)__: um serviço hospedado secundário é criado em uma região alternativa e funções são implantadas para garantir a capacidade de mínimo; No entanto, as funções não recebem o tráfego de produção. Essa abordagem é útil para aplicativos que não foram projetados para distribuir tráfego entre regiões.

  * __Quente reserva (ativo/ativo)__: O aplicativo é projetado para receber carga de produção em várias regiões. Os serviços de nuvem em cada região podem estar configurados para capacidade maior do que necessário fins de recuperação. Como alternativa, os serviços de nuvem podem dimensionar out conforme necessário no momento da desastres e failover. Essa abordagem requer investimento significativo no design do aplicativo, mas ela tem benefícios significativos. Eles incluem o tempo de recuperação de garantia e baixos, contínuo de teste de todos os locais de recuperação e uso eficiente da capacidade.

Uma discussão completa de design distribuído está fora do escopo deste documento. Para obter mais informações, consulte [recuperação de dados e alta disponibilidade para aplicativos do Azure](https://aka.ms/drtechguide).

##<a name="virtual-machines"></a>Máquinas virtuais

Recuperação de infraestrutura como um serviço (IaaS) VMs (máquinas virtuais) é semelhante a plataforma como um serviço (PaaS) calcular recuperação em vários aspectos. Existem diferenças importantes, no entanto, devido ao fato de que uma VM IaaS consiste na máquina virtual e o disco de máquina virtual.

  * __Uso do Azure Backup para criar backups de região cruzada que são consistente de aplicativos__.
  [Fazer Backup do Azure](https://azure.microsoft.com/services/backup/) permite aos clientes criar backups consistentes do aplicativo em vários discos de máquina virtual e suporte para replicação de backups entre regiões. Você pode fazer isso escolhendo a localização geográfica replicar o Cofre de backup no momento da criação. Observe que a replicação do backup cofre deve ser configurado no momento da criação. Ele não pode ser definido mais tarde. Se uma região for perdida, Microsoft tornará os backups disponível para clientes. Os clientes poderão restaurar para qualquer um dos seus pontos de restauração configurado.

  * __Separar o disco de dados a partir do disco do sistema operacional__. Uma consideração importante para IaaS VMs é que você não pode alterar o disco de sistema operacional sem recriar a máquina virtual. Isso não é um problema se sua estratégia de recuperação for reimplantar após desastre. No entanto, talvez seja um problema se você estiver usando a abordagem quente reserva para reservar capacidade. Para implementar isso corretamente, você deve ter o disco de sistema operacional correto implantado em ambos os locais primárias e secundários e os dados do aplicativo devem ser armazenados em uma unidade separada. Se possível, use uma configuração de sistema operacional padrão que pode ser fornecida em ambos os locais. Após um failover, você deve anexar a unidade de dados em suas VMs IaaS existente no DC secundário. Use AzCopy para copiar instantâneos dos s dados para um local remoto.

  * __Lembre-se dos possíveis problemas de consistência após um failover de localização geográfica de vários discos de máquina virtual__. Discos de máquina virtual são implementados como blobs de armazenamento do Azure e tem a mesma característica de replicação geográfica. A menos que o [Backup do Azure](https://azure.microsoft.com/services/backup/) é usado, não há nenhuma garantia de consistência entre discos, porque é assíncrona e replica independentemente de replicação geográfica. Discos individuais de máquina virtual garante sejam em uma falha consistente estado após um failover de localização geográfica, mas não é consistente em discos. Isso pode causar problemas em alguns casos (por exemplo, no caso de faixas de disco).

##<a name="storage"></a>Armazenamento

###<a name="recovery-by-using-geo-redundant-storage-of-blob-table-queue-and-vm-disk-storage"></a>Recuperação usando geográfica redundantes armazenamento de blob, tabela, fila e armazenamento de disco de máquina virtual

No Azure, blobs, tabelas, filas e máquina virtual discos são todos geográfica replicado por padrão. Isso é conhecido como armazenamento geográfica redundantes (GRS). GRS replica dados de armazenamento em um data center a par centenas de milhas separadamente dentro de uma região geográfica específica. GRS destina-se a fornecer durabilidade adicional caso haja um desastre do data center principal. Controles do Microsoft quando houver falha e failover está limitada aos grandes desastres no qual o local principal original é considerado irrecuperável em um período de tempo razoável. Em alguns cenários, isso pode ser vários dias. Dados são normalmente replicados em poucos minutos, embora o intervalo de sincronização não está ainda coberto por um contrato de nível de serviço.

Em caso de um failover de localização geográfica, haverá não altera a como a conta é acessada (a chave de URL e a conta não será alterado). A conta de armazenamento, no entanto, será em uma região diferente após failover. Isso poderá afetar aplicativos que exigem afinidade regional com sua conta de armazenamento. Mesmo para serviços e aplicativos que não exigem uma conta de armazenamento no mesmo data center, a latência de data center cruzado e encargos de largura de banda podem ser atraentes motivos para mover o tráfego para a região de failover temporariamente. Isso poderia fator em uma estratégia de recuperação de desastres geral.

Além de failover automático fornecido pelo GRS, Azure introduziu um serviço que fornece acesso de leitura para a cópia dos seus dados no local de armazenamento secundário. Isso é chamado armazenamento de localização geográfica redundantes de acesso de leitura (ar-GRS).

Para obter mais informações sobre o armazenamento GRS e ar GRS, consulte [replicação de armazenamento do Azure](../storage/storage-redundancy.md).

###<a name="geo-replication-region-mappings"></a>Mapeamentos de região geográfica replicação:

É importante saber onde seus dados são replicado geográfica, para saber onde implantar outras instâncias dos dados que exigem afinidade regional com seu armazenamento. A tabela a seguir mostra os pares de local principal e secundário:

[AZURE.INCLUDE [paired-region-list](../../includes/paired-region-list.md)]

###<a name="geo-replication-pricing"></a>Replicação geográfica preços:

Replicação geográfica será incluída no preço atual para o armazenamento do Azure. Isso é chamado armazenamento geográfica redundantes (GRS). Se você não quiser que seus dados replicado geográfica, você pode desabilitar replicação geográfica para sua conta. Isso se chama localmente armazenamento redundante e ele cobrada de acordo com um preço descontado comparado a GRS.

###<a name="determining-if-a-geo-failover-has-occurred"></a>Determinando se ocorreu um failover geográfica

Se houver uma localização geográfica-falha, isso será lançado ao [Painel de integridade de serviço do Azure](https://azure.microsoft.com/status/). Aplicativos podem implementar um meio automatizado de detectar isso, no entanto, monitorando região geográfica para sua conta de armazenamento. Isso pode ser usado para disparar outras operações de recuperação, como a ativação de recursos de computação na região geográfica onde seu armazenamento movido. Você pode executar uma consulta para isso de gerenciamento de serviços API, usando [Obter propriedades da conta de armazenamento](https://msdn.microsoft.com/library/ee460802.aspx). As propriedades relevantes são:

    <GeoPrimaryRegion>primary-region</GeoPrimaryRegion>
    <StatusOfPrimary>[Available|Unavailable]</StatusOfPrimary>
    <LastGeoFailoverTime>DateTime</LastGeoFailoverTime>
    <GeoSecondaryRegion>secondary-region</GeoSecondaryRegion>
    <StatusOfSecondary>[Available|Unavailable]</StatusOfSecondary>

###<a name="vm-disks-and-geo-failover"></a>Discos de máquina virtual e failover de localização geográfica

Conforme discutido na seção em discos de máquina virtual, não há nenhuma garantia de consistência dos dados entre discos de máquina virtual após um failover. Para garantir a precisão de backups, um produto de backup como o Gerenciador de proteção de dados deve ser usado para fazer backup e restaurar os dados de aplicativo.

##<a name="database"></a>Banco de dados

###<a name="sql-database"></a>Banco de dados SQL

Banco de dados do SQL Azure fornece dois tipos de recuperação: geográfica restauração e replicação de localização geográfica ativa.

####<a name="geo-restore"></a>Restaurar a localização geográfica

[Localização geográfica restauração](../sql-database/sql-database-recovery-using-backups.md#geo-restore) também está disponível com bancos de dados Basic, Standard e Premium. Ele fornece a opção de recuperação padrão quando o banco de dados está indisponível por causa de um incidente na região onde seu banco de dados está hospedado. Semelhante ao restaurar no momento, localização geográfica restauração depende de backups de banco de dados no armazenamento do Azure redundantes de localização geográfica. Ele restaura a partir da cópia de backup replicado geográfica e portanto é flexível às interrupções de armazenamento na região primária. Para obter mais detalhes, consulte [restaurar um banco de dados do SQL Azure ou failover para um secundário](../sql-database/sql-database-disaster-recovery.md).

####<a name="active-geo-replication"></a>Replicação de localização geográfica ativa

[Replicação de localização geográfica ativa](../sql-database/sql-database-geo-replication-overview.md) está disponível para todos os níveis de banco de dados. Ele é projetado para aplicativos que têm mais rigorosas exigências de recuperação de restauração geográfica pode oferecer. Usando replicação de localização geográfica ativa, você pode criar até quatro secundários legíveis em servidores em diferentes regiões. Você pode iniciar failover para qualquer um dos secundários. Além disso, localização geográfica-replicação ativa pode ser usada para oferecer suporte os cenários de atualização ou realocação de aplicativos, bem como balanceamento de carga em cargas de trabalho somente leitura. Para obter detalhes, consulte [Configurar replicação geográfica](../sql-database/sql-database-geo-replication-portal.md) e para [Alternar para o banco de dados secundário](../sql-database/sql-database-geo-replication-failover-portal.md). Consulte [Design de um aplicativo para recuperação de nuvem usando replicação geográfica ativo no banco de dados do SQL](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [Gerenciando atualizações sem interrupção de aplicativos de nuvem do banco de dados ativo geográfica replicação do SQL](../sql-database/sql-database-manage-application-rolling-upgrade.md) para obter detalhes sobre como criar e implementar aplicativos e atualização de aplicativos sem tempo de inatividade.

###<a name="sql-server-on-virtual-machines"></a>SQL Server em máquinas virtuais

Uma variedade de opções estão disponíveis para recuperação e alta disponibilidade para SQL Server 2012 (e posteriores) em execução em máquinas virtuais do Azure. Para obter mais informações, consulte [alta disponibilidade e recuperação de dados para o SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

##<a name="other-azure-platform-services"></a>Outros serviços de plataforma Windows Azure

Ao tentar executar seu serviço de nuvem em várias regiões Azure, você deve considerar as implicações para cada uma das suas dependências. Nas seções a seguir, as orientações específicas do serviço supõe que você deve usar o mesmo serviço Azure em um data center de Azure alternativo. Isso envolve a configuração e tarefas de replicação de dados.

>[AZURE.NOTE]Em alguns casos, essas etapas podem ajudar a reduzir uma interrupção de serviço específicas em vez de um evento de data center inteiro. Da perspectiva do aplicativo, uma interrupção de serviço específicas pode estar apenas como limitar e exijam migrando temporariamente o serviço para uma região Azure alternativa.

###<a name="service-bus"></a>Barramento de serviço

Barramento de serviço do Azure usa um namespace exclusivo que não ocupam regiões Azure. Portanto o primeiro requisito é configurar os namespaces de barramento de serviço necessárias na região alternativo. No entanto, também existem considerações para a durabilidade das mensagens na fila. Há várias estratégias de replicação mensagens em regiões Azure. Para obter os detalhes sobre essas estratégias de replicação e outras estratégias de recuperação de desastres, consulte [práticas recomendadas para aplicativos proteger contra interrupções de barramento de serviço e desastres](../service-bus-messaging/service-bus-outages-disasters.md). Para outras considerações de disponibilidade, consulte [Barramento de serviço (disponibilidade)](./resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

###<a name="app-service"></a>Serviço de aplicativo

Para migrar um aplicativo de serviço de aplicativo do Azure, como aplicativos da Web ou de celular, para uma região Azure secundária, você deve ter um backup do site disponível para publicação. Se a interrupção não envolvem data center Azure inteiro, poderá usar o FTP para baixar um backup recente o conteúdo do site. Crie um novo aplicativo na região alternativo, a menos que você fez anteriormente para reservar capacidade. Publicar o site para a nova região e faça as alterações de configuração necessárias. Essas alterações podem incluir cadeias de caracteres de conexão de banco de dados ou outras configurações específicas de região. Se necessário, adicione um certificado SSL do site e altere o registro CNAME DNS para que o nome de domínio personalizado aponta para a URL de aplicativo da Web do Azure reimplantado.

###<a name="hdinsight"></a>HDInsight

Os dados associados HDInsight são armazenados por padrão em armazenamento de Blob do Azure. HDInsight requer que um cluster de Hadoop MapReduce trabalhos de processamento deve estar localizado conjunta na mesma região como a conta de armazenamento que contém os dados analisados. Desde que você use o recurso de replicação geográfica disponível ao armazenamento do Azure, você pode acessar seus dados na região secundário onde os dados foi duplicados se por algum motivo a região primária não está mais disponível. Você pode criar um novo cluster Hadoop na região onde os dados foi duplicados e continuam processando-lo. Para outras considerações de disponibilidade, consulte [HDInsight (disponibilidade)](./resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

###<a name="sql-reporting"></a>Relatório de SQL

No momento, a recuperação a perda de uma região Azure requer várias instâncias de SQL Reporting em diferentes regiões Azure. Essas instâncias SQL Reporting devem acessar os mesmos dados e esses dados devem ter seu próprio recuperação planejar em caso de um desastre. Você também pode manter cópias de backup externas do arquivo RDL para cada relatório.

###<a name="media-services"></a>Serviços de mídia

Serviços de mídia do Azure tem uma abordagem de recuperação diferentes para codificação e streaming. Normalmente, streaming é mais importante durante uma interrupção regional. Para se preparar para isso, você deve ter uma conta de serviços de mídia em duas regiões Azure diferentes. Conteúdo codificado deve estar localizado em ambas as regiões. Durante uma falha, você pode redirecionar o tráfego de streaming à região alternativo. Codificação pode ser executada em qualquer região Azure. Se a codificação é sensível ao tempo, por exemplo durante o processamento de evento ao vivo, você deve estar preparado para enviar trabalhos para um data center alternativo durante falhas.

###<a name="virtual-network"></a>Rede virtual

Arquivos de configuração fornecem a maneira mais rápida para configurar uma rede virtual em uma região Azure alternativa. Depois de configurar a rede virtual na região Azure principal, [Exportar as configurações de rede virtual](../virtual-network/virtual-networks-create-vnet-classic-portal.md) para a rede atual para um arquivo de configuração de rede. Em caso de uma interrupção na região primária, [restaurar a rede virtual](../virtual-network/virtual-networks-create-vnet-classic-portal.md) do arquivo de configuração armazenado. Em seguida, configure outros serviços de nuvem, máquinas virtuais ou configurações de cruz local para trabalhar com a nova rede virtual.

##<a name="checklists-for-disaster-recovery"></a>Listas de verificação de recuperação de dados

##<a name="cloud-services-checklist"></a>Lista de verificação de serviços de nuvem

  1. Examine a seção de serviços de nuvem deste documento.
  2. Crie uma estratégia de recuperação de desastres entre região.
  3. Compreenda variações na reserva de capacidade em regiões alternativos.
  4. Use ferramentas de roteamento de tráfego, como o Gerenciador de tráfego do Azure.

##<a name="virtual-machines-checklist"></a>Lista de verificação de máquinas virtuais

  1. Examine a seção de máquinas virtuais deste documento.
  2. Use o [Backup do Azure](https://azure.microsoft.com/services/backup/) para criar backups consistentes de aplicativo entre regiões.

##<a name="storage-checklist"></a>Lista de verificação de armazenamento

  1. Examine a seção de armazenamento deste documento.
  2. Não desabilite replicação geográfica dos recursos de armazenamento.
  3. Compreenda região alternativo para replicação geográfica em caso de failover.
  4. Crie estratégias de backup personalizadas para estratégias de failover controlado por usuário.

##<a name="sql-database-checklist"></a>Lista de verificação de banco de dados SQL

  1. Examine a seção de banco de dados SQL deste documento.
  2. Use [Localização geográfica restauração](../sql-database/sql-database-recovery-using-backups.md#geo-restore) ou [Replicação geográfica](../sql-database/sql-database-geo-replication-overview.md) conforme apropriado.

##<a name="sql-server-on-virtual-machines-checklist"></a>SQL Server na lista de verificação de máquinas virtuais

  1. Examine o SQL Server em máquinas virtuais seção deste documento.
  2. Use grupos de disponibilidade sempre ativado entre região ou espelhamento de banco de dados.
  3. Como alternativa, usar o backup e restauração para armazenamento de blob.

##<a name="service-bus-checklist"></a>Lista de verificação de barramento de serviço

  1. Examine a seção de barramento de serviço deste documento.
  2. Configure um namespace barramento de serviço em uma região alternativa.
  3. Considere as estratégias de replicação personalizada para mensagens em regiões.

##<a name="app-service-checklist"></a>Lista de verificação de serviço de aplicativo

  1. Examine a seção de serviço de aplicativo deste documento.
  2. Manter backups de site fora da região primária.
  3. Se a interrupção é parcial, tente recuperar site atual com FTP.
  4. Plano para implantar o site em um site novo ou existente em uma região alternativo.
  5. Planeje alterações de configuração para o aplicativo e os registros CNAME DNS.

##<a name="hdinsight-checklist"></a>Lista de verificação de HDInsight

  1. Examine a seção de HDInsight deste documento.
  2. Crie um novo cluster Hadoop na região com dados duplicados.

##<a name="sql-reporting-checklist"></a>Lista de verificação de relatórios do SQL

  1. Examine a seção SQL Reporting deste documento.
  2. Manter uma instância de SQL Reporting alternada em uma região diferente.
  3. Manter um plano separado para replicar o destino para região.

##<a name="media-services-checklist"></a>Lista de verificação de serviços de mídia

  1. Examine a seção de serviços de mídia deste documento.
  2. Crie uma conta de serviços de mídia em uma região alternativa.
  3. Codifica o mesmo conteúdo em ambas as regiões para suportar falhas streaming.
  4. Envie trabalhos de codificação para uma região alternativo em caso de uma interrupção do serviço.

##<a name="virtual-network-checklist"></a>Lista de verificação de rede virtual

  1. Examine a seção de rede Virtual deste documento.
  2. Usar exportados as configurações de rede virtual para recriá-la em outra região.

##<a name="next-steps"></a>Próximas etapas

Este artigo é parte de uma série voltada para [orientações técnicas resiliência Azure](./resiliency-technical-guidance.md). Próximo artigo desta série se concentra nos [recuperação a partir de uma análise de locais no Azure](./resiliency-technical-guidance-recovery-on-premises-azure.md).
