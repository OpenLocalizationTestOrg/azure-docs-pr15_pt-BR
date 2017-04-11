<properties
   pageTitle="Orientações técnicas: recuperação de falhas locais no Azure | Microsoft Azure"
   description="Artigo na compreensão e projetar flexível e altamente disponível, aplicativos tolerância, bem como planejamento para recuperação voltada para falhas locais dentro do Azure."
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

#<a name="azure-resiliency-technical-guidance-recovery-from-local-failures-in-azure"></a>Orientações técnicas resiliência Azure: recuperação de falhas locais no Azure

Há dois principais ameaças a disponibilidade de aplicativos:

* A falha de dispositivos, como unidades e servidores
* A exaustão de recursos essenciais, como computação em condições de carga de pico

Azure fornece uma combinação de gerenciamento de recursos, elasticidade, balanceamento de carga e partição para habilitar alta disponibilidade nessas circunstâncias. Alguns desses recursos são executadas automaticamente para todos os serviços do Azure. Entretanto, em alguns casos, o desenvolvedor do aplicativo deverá fazer algum trabalho adicional para tirar proveito delas.

##<a name="cloud-services"></a>Serviços de nuvem

Serviços de nuvem Azure consiste em coleções de uma ou mais funções da web ou de trabalho. Uma ou mais instâncias de uma função podem executar simultaneamente. A configuração determina o número de instâncias. Instâncias de função monitoramento e gerenciadas por meio de um componente chamado controlador de tecidos. Controlador de tecidos detecta e responde a falhas de hardware e software automaticamente.

Cada instância de função é executado em seu próprio virtuais de máquina e se comunica com seu controlador de tecidos através de um agente de convidado. O agente de convidado coleta métricas de recurso e de nó, incluindo o uso de máquina virtual, status, logs, uso do recurso, exceções e condições de falha. Controlador de tecidos consultas o agente de convidado em intervalos configuráveis, e ele é reiniciado a máquina virtual se o agente de convidado falhar ao responder. Em caso de falha de hardware, o controlador de tecidos associado move todas as instâncias de função afetado para um novo nó de hardware e reconfigurará a rede para rotear o tráfego lá.

Para aproveitar esses recursos, os desenvolvedores devem garantir que todas as funções de serviço Evite armazenar estado em instâncias de função. Em vez disso, todos os dados persistentes devem ser acessados de armazenamento durável, como o armazenamento do Azure ou Azure SQL Database. Isso permite que as funções lidar com solicitações. Isso também significa que instâncias de função podem ir para baixo a qualquer momento sem criar inconsistências no estado temporário ou persistente do serviço.

O requisito para armazenar o estado externamente às funções tem várias implicações. Por exemplo, isso significa que todas as alterações relacionadas a uma tabela de armazenamento do Azure devem ser alteradas em uma única transação de entidade-grupo, se possível. Claro, nem sempre é possível fazer todas as alterações em uma única transação. Você deve tomar cuidado especial para garantir que falhas de instância da função não causam problemas quando eles interromper as operações de execução longa que se estendem por duas ou mais atualizações para o estado persistente do serviço. Se outra função tentar repetir essa operação, ele deve prever e manipular o caso em que o trabalho foi parcialmente concluído.

Por exemplo, considere um serviço que partições dados em diversos armazenamentos. Se uma função de trabalho falhar enquanto ele está realocação um fragmentar, a realocação da fragmentar não pode concluir. Ou a realocação pode ser repetida do início de uma função de trabalho diferentes, possivelmente causando dados órfãos ou corrupção de dados. Para evitar problemas, operações de longa duração devem ser um destes procedimentos ou ambos:

 * *Idempotente*: repetitivo sem efeitos de lado. Para ser idempotentes, uma operação de execução demorada deve ter o mesmo efeito independentemente de quantas vezes ele é executado, mesmo quando ela for interrompida durante a execução.
 * *Forma incremental reinicializável*: capaz de continuar do ponto mais recente de falha. Para ser incremental reinicializável, uma operação de execução demorada deve consistir em uma sequência de menores atômicas operações. Ele também deve registro seu progresso no armazenamento durável, para que cada invocação subsequente seleciona onde seu antecessor parou.

Finalmente, todas as operações de longa duração devem ser chamadas repetidamente até que forem bem-sucedidas. Por exemplo, uma operação de provisionamento pode ser colocada em uma fila Azure e, em seguida, removida da fila por uma função de trabalho somente quando ele é bem sucedida. Coleta de lixo pode ser necessária limpar dados que interrompido operações criar.

###<a name="elasticity"></a>Elasticidade

O número inicial das instâncias em execução para cada função é determinado na configuração de cada função. Os administradores devem configurar inicialmente cada função para ser executado com duas ou mais ocorrências com base em carga esperada. Mas você pode facilmente dimensionar instâncias de função para cima ou para baixo como uso padrões alterar. Você pode fazer isso manualmente no portal do Azure, ou você pode automatizar o processo usando o Windows PowerShell, a API de gerenciamento de serviço ou ferramentas de terceiros. Para obter mais informações, consulte [como escala automática de um aplicativo](../cloud-services/cloud-services-how-to-scale.md).

###<a name="partitioning"></a>Partição

Controlador de tecidos Azure usa dois tipos de partições:

* Uma *atualização de domínio* é usada para atualizar instâncias de função de um serviço em grupos. Azure implanta instâncias de serviço em vários domínios de atualização. Para uma atualização in-loco, o controlador de tecidos traz para baixo todas as instâncias no domínio de uma atualização, atualiza-los e, em seguida, reinicia-las antes de mover para o próximo domínio de atualização. Essa abordagem impede que todo o serviço indisponível durante o processo de atualização.
* Um *domínio de falha* define possíveis pontos de falha de hardware ou de rede. Para qualquer função que tem mais de uma instância, o controlador de tecidos garante que as instâncias são distribuídas entre vários domínios de falhas, para impedir que falhas de hardware isolados interromper o serviço. Domínios de falha regem todos exposição a falhas do servidor e cluster.

O [Azure contrato de nível de serviço (SLA)](https://azure.microsoft.com/support/legal/sla/) garante que quando duas ou mais ocorrências de função web são implantadas falhas diferentes e atualizar domínios, ele terá conectividade externa pelo menos 99,95% do tempo. Ao contrário de domínios de atualização, não há nenhuma maneira de controlar o número de domínios de falha. Azure automaticamente aloca domínios de falha e distribui instâncias de função entre elas. Pelo menos as primeiras duas instâncias de cada função são colocadas na falhas diferentes e atualizar domínios para garantir que qualquer função pelo menos duas instâncias atenderão SLA. É representado no diagrama a seguir.

![Exibição simplificada domínio de isolamento de falhas](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-1.png)

###<a name="load-balancing"></a>Balanceamento de carga

Todo o tráfego de entrada para uma função web passa por um balanceador de carga sem estado, que distribui solicitações de cliente entre as instâncias de função. Instâncias de função individuais não têm endereços IP públicos e não estiverem endereçamento diretamente da Internet. Funções da Web são sem estado para que qualquer solicitação de cliente pode ser roteada para qualquer instância de função. Um evento de [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck.aspx) é gerado a cada 15 segundos. Você pode usar isso para indicar se a função está pronta para receber tráfego, ou se ele está ocupado e deve ser levadas fora a rotação de Balanceador de carga.

##<a name="virtual-machines"></a>Máquinas virtuais

Azure máquinas virtuais difere plataforma como um serviço (PaaS) calcular funções em vários aspectos em relação a alta disponibilidade. Em alguns casos, você deve fazer o trabalho adicional para garantir alta disponibilidade.

###<a name="disk-durability"></a>Durabilidade de disco

Diferentemente das instâncias de função de PaaS, dados armazenados em unidades de máquina virtual são persistentes mesmo quando a máquina virtual realocação. Azure máquinas virtuais use discos de máquina virtual que existem como blobs no armazenamento do Azure. Devido as características de disponibilidade de armazenamento do Azure, os dados armazenados em unidades de uma máquina virtual também são altamente disponíveis.

Observe que a unidade de disco D (em Windows VMs) é a exceção a essa regra. Unidade de disco D é realmente físicos de armazenamento no servidor de rack que hospeda a máquina virtual e seus dados serão perdidos se a máquina virtual é reciclada. Unidade de disco D destina-se somente armazenamento temporário. No Linux, Azure "geralmente" (mas não sempre) expõe disco local temporário como dispositivo de bloco de /dev/sdb. Muitas vezes, ele é montado pelo agente de Linux do Azure como /mnt/resource ou /mnt pontos de montagem (configuráveis via /etc/waagent.conf).

###<a name="partitioning"></a>Partição

Azure nativamente entende as camadas em um aplicativo de PaaS (função web e função de trabalho) e, portanto, pode corretamente distribuí-las entre domínios falhas e atualização. Em contraste, as camadas em uma infraestrutura como um aplicativo de serviço (IaaS) devem ser definidas manualmente por meio de conjuntos de disponibilidade. Os conjuntos de disponibilidade são necessários para um SLA em IaaS.

![Disponibilidade define para Azure máquinas virtuais](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-2.png)

No diagrama anterior, a camada de serviços de informações da Internet (IIS) (que funciona como uma camada de aplicativo web) e a camada SQL (que funciona como uma camada de dados) são atribuídos a disponibilidade de diferentes conjuntos. Isso garante que todas as instâncias de cada nível tem redundância de hardware distribuindo máquinas virtuais entre domínios de falha e níveis de inteiros não são levados para baixo durante uma atualização.

###<a name="load-balancing"></a>Balanceamento de carga

Se as VMs devem ter tráfego distribuído-los, você deve agrupar VMs em um aplicativo e carregar saldo em um ponto de extremidade TCP ou UDP específico. Para obter mais informações, consulte [balanceamento de carga de máquinas virtuais](../virtual-machines/virtual-machines-linux-load-balance.md). Se as VMs recebem entrada de outra fonte (por exemplo, mecanismo de fila), um balanceador de carga não é necessário. O balanceador de carga usa uma verificação de integridade básica para determinar se o tráfego deve ser enviado para o nó. Também é possível criar seus próprios testes para implementar métricas de integridade de aplicativos específicos que determinam se a máquina virtual deve receber tráfego.

##<a name="storage"></a>Armazenamento

Armazenamento do Azure é o serviço de dados duráveis da linha de base para o Azure. Ele fornece blob, tabela, fila e armazenamento de disco de máquina virtual. Ele usa uma combinação de replicação e gerenciamento de recursos para fornecer alta disponibilidade em um único data center. A disponibilidade de armazenamento do Azure SLA garante que pelo menos 99,9 por cento do tempo:

* Solicitações corretamente formatadas para adicionar, atualizar, ler e excluir dados serão corretamente e com êxito processados.
* Contas de armazenamento terão a conectividade com o gateway da Internet.

###<a name="replication"></a>Replicação

Armazenamento do Azure facilita durabilidade de dados mantendo várias cópias de todos os dados em unidades diferentes nos subsistemas de armazenamento físico totalmente independentes dentro da região. Os dados são replicados sincronia e todas as cópias são confirmadas antes da gravação é confirmada. Armazenamento do Azure é altamente consistente, significando que leituras são garantia para refletir as gravações mais recentes. Além disso, cópias de dados continuamente são verificadas para detectar e reparar bit corrompido, um geralmente esquecido risco à integridade dos dados armazenados.

Serviços beneficiam replicação apenas usando o armazenamento do Azure. O desenvolvedor do serviço não precisa fazer trabalho adicional para recuperar de uma falha local.

###<a name="resource-management"></a>Gerenciamento de recursos

Contas de armazenamento criadas após maio de 2014, pode crescer para até 500 TB (o máximo anterior era 200 TB). Se o espaço adicional é necessário, aplicativos devem ser projetados para usar várias contas de armazenamento.

###<a name="virtual-machine-disks"></a>Discos de máquina virtual

Disco de uma máquina virtual é armazenado como um blob de página no armazenamento do Azure, dando a ele todos os mesmos propriedades durabilidade e escalabilidade como armazenamento de Blob. Este design torna os dados em disco de uma máquina virtual persistentes, mesmo se o servidor que executa a máquina virtual falha e a máquina virtual deve ser reiniciada em outro servidor.

##<a name="database"></a>Banco de dados

###<a name="sql-database"></a>Banco de dados SQL

Banco de dados do SQL Azure fornece o banco de dados como um serviço. Ele permite que aplicativos rapidamente provisionar, inserir dados e bancos de dados relacionais de consulta. Ele fornece muitos dos recursos do SQL Server e funcionalidade, familiar enquanto abstraindo a carga de hardware, configuração, patches e resiliência.

>[AZURE.NOTE] Banco de dados do SQL Azure não oferece paridade recurso individual com o SQL Server. Ela tem a intenção de atender a um conjunto diferente de requisitos--um que tenha exclusivamente adequado para aplicativos de nuvem (dimensionamento Elástico, banco de dados como um serviço para reduzir os custos de manutenção e assim por diante). Para obter mais informações, consulte [Escolha uma opção do SQL Server de nuvem: banco de dados do SQL Azure (PaaS) ou SQL Server no Azure VMs (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

####<a name="replication"></a>Replicação

Banco de dados do SQL Azure fornece resiliência interna para falha de nível de nó. Todas as gravações em um banco de dados são automaticamente replicadas para dois ou mais nós de plano de fundo por meio de uma técnica de confirmação de quorum. (O principal e secundário pelo menos um devem confirmar que a atividade sejam gravada no log de transação antes da transação é considerada bem-sucedido e retorna.) No caso de falha de nó, o banco de dados passam automaticamente para uma das réplicas secundárias. Isso causa uma interrupção de conexão temporárias para aplicativos cliente. Por esse motivo, todos os clientes do Azure SQL Database devem implementar alguma forma de tratamento de conexão temporárias. Para obter mais informações, consulte [Repetir orientações específicas do serviço](../best-practices-retry-service-specific.md).

####<a name="resource-management"></a>Gerenciamento de recursos

Cada banco de dados, quando criado, está configurado com um tamanho limite superior. O tamanho máximo disponível atualmente é 1 TB (tamanho limites variam com base em seu nível de serviço, consulte [níveis de serviço e níveis de desempenho de bancos de dados do SQL Azure](../sql-database/sql-database-resource-limits.md#service-tiers-and-performance-levels). Quando um banco de dados atinge seu limite de tamanho superior, ele rejeita comandos adicionais de inserir ou atualizar. (Consultando e excluindo dados ainda é possível.)

Dentro de um banco de dados, o Azure SQL Database usa uma estrutura para gerenciar recursos. No entanto, em vez de um controlador de tecidos, ele usa uma topologia de anel para detectar falhas. Cada réplica em um cluster tem dois vizinhos e é responsável por detectar quando eles descem. Quando uma réplica falhar, suas vizinhas disparam um agente de reconfiguração para recriá-la em outro computador. Otimização do mecanismo é fornecido para garantir que um servidor lógico não usar muitos recursos em um computador ou exceder limites físicos da máquina.

###<a name="elasticity"></a>Elasticidade

Se o aplicativo exigir mais do que o limite de banco de dados de 1 TB, ele deve implementar uma abordagem de fora de escala. Você dimensionar com o Azure SQL Database particionando manualmente, também conhecido como fragmentação, dados em vários bancos de dados do SQL. Essa abordagem fora de escala oferece a oportunidade para atingir crescimento de custos quase linear com escala. Crescimento Elástico ou capacidade sob demanda pode crescer com custos incrementais conforme necessário porque bancos de dados são cobrados com base na médio tamanho real usado por dia, não baseado em tamanho máximo possível.

##<a name="sql-server-on-virtual-machines"></a>SQL Server em máquinas virtuais

Instalando o SQL Server (versão 2014 ou posterior) em máquinas virtuais do Azure, você pode aproveitar os recursos de disponibilidade tradicional do SQL Server. Esses recursos incluem espelhamento de banco de dados e grupos de disponibilidade sempre ativado. Observe que o Azure VMs, armazenamento e redes têm características operacionais diferentes um local, não virtualizado infraestrutura. Uma implementação bem-sucedida de uma alta disponibilidade/recuperação solução (HA/DR) SQL Server no Azure requer que você compreender essas diferenças e criar sua solução para acomodá-los.

###<a name="high-availability-nodes-in-an-availability-set"></a>Nós de alta disponibilidade em um conjunto de disponibilidade

Quando você implementa uma solução de alta disponibilidade no Azure, você pode usar a disponibilidade definida no Azure para colocar os nós de alta disponibilidade em domínios de falha separada e atualizar domínios. Para ser claro, o conjunto de disponibilidade é um conceito Azure. É uma prática recomendada que devem ser seguidas para certificar-se de que seus bancos de dados são realmente altamente disponíveis, se você estiver usando grupos de disponibilidade sempre ativado, espelhamento de banco de dados ou algo mais. Se você não siga esta prática recomendada, talvez seja sob a suposição false o sistema está altamente disponível. Mas na realidade, os nós podem todos falhar simultaneamente porque eles ocorrem deve ser posicionado no mesmo domínio falhas na região do Azure.

Esta recomendação não é conforme aplicável com o envio de log. Como um recurso de recuperação de desastres, você deve garantir que os servidores estão funcionando em regiões Azure separadas. Por definição, essas regiões são domínios de falha separada.

Para Azure nuvem serviços VMs implantadas por meio do portal clássico seja no mesmo conjunto de disponibilidade, você deve implantá-los no serviço de nuvem do mesmo. VMs implantadas por meio do Azure Gerenciador de recursos (o portal atual) não tem essa limitação. Para implantar o portal clássico VMs em serviço de nuvem do Azure, somente os nós no serviço de nuvem mesmo podem participar o mesmo conjunto de disponibilidade. Além disso, as VMs de serviços de nuvem deve ser na mesma rede virtual para garantir que eles mantenham seus IPs mesmo depois de reparo de serviço. Isso evita interrupções de atualização DNS.

###<a name="azure-only-high-availability-solutions"></a>Somente Azure: soluções de alta disponibilidade

Você pode ter uma solução de alta disponibilidade para seus bancos de dados do SQL Server no Azure usando grupos de disponibilidade sempre ativado ou espelhamento de banco de dados.

O diagrama a seguir demonstra a arquitetura de grupos de disponibilidade sempre ativado em execução em máquinas virtuais do Azure. Este diagrama foi extraído o artigo detalhado sobre este assunto, [alta disponibilidade e recuperação de dados para o SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Grupos de disponibilidade sempre ativado no Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-1.png)

Você pode também automaticamente provisionar uma grupos de disponibilidade sempre ativado implantação-a ponta em VMs Azure usando o modelo sempre ativado no portal do Azure. Para obter mais informações, consulte [SQL Server AlwaysOn oferecendo na Galeria de Portal do Microsoft Azure](https://blogs.technet.microsoft.com/dataplatforminsider/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery/).

O diagrama a seguir demonstra o uso de banco de dados espelhar em máquinas virtuais do Azure. Ela também foi retirada do tópico profundamente [alta disponibilidade e recuperação de dados para o SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Espelhamento do banco de dados no Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-2.png)

>[AZURE.NOTE] As duas arquiteturas exigem um controlador de domínio. No entanto, com o banco de dados espelhar, é possível usar certificados de servidor para eliminar a necessidade de um controlador de domínio.

##<a name="other-azure-platform-services"></a>Outros serviços de plataforma Windows Azure

Aplicativos que são criados no Azure beneficiar recursos de plataforma para recuperar de falhas locais. Em alguns casos, você pode realizar ações específicas para aumentar a disponibilidade para seu cenário específico.

###<a name="service-bus"></a>Barramento de serviço

Para atenuar contra uma interrupção temporária do barramento de serviço do Azure, considere a criação de uma fila durável do lado do cliente. Isso temporariamente usa um mecanismo de armazenamento local alternativo para armazenar as mensagens que não podem ser adicionadas à fila barramento de serviço. O aplicativo pode decidir como lidar com as mensagens temporariamente armazenadas depois que o serviço for restaurado. Para obter mais informações, consulte [práticas recomendadas para melhorias de desempenho usando o barramento de serviço orientado a mensagens](../service-bus-messaging/service-bus-performance-improvements.md) e [Barramento de serviço (recuperação de dados)](./resiliency-technical-guidance-recovery-loss-azure-region.md#other-azure-platform-services).

###<a name="hdinsight"></a>HDInsight

Os dados que está associada a Azurehdinsight são armazenados por padrão em armazenamento de Blob do Azure. Armazenamento do Azure Especifica as propriedades de alta disponibilidade e durabilidade para armazenamento de Blob. O processamento de vários nós associado ao Hadoop MapReduce trabalhos ocorre em uma temporárias Hadoop distribuído arquivo System (HDFS) que está provisionado quando precisa HDInsight. Resultados de um trabalho MapReduce também são armazenados por padrão em armazenamento de Blob do Azure, para que os dados processados é duráveis e permanecem altamente disponíveis após o cluster Hadoop será desprovisionado. Para obter mais informações, consulte [HDInsight (recuperação de dados)](./resiliency-technical-guidance-recovery-loss-azure-region.md#other-azure-platform-services).

##<a name="checklists-for-local-failures"></a>Listas de verificação para falhas locais

###<a name="cloud-services"></a>Serviços de nuvem

  1. Examine a seção de serviços de nuvem deste documento.
  2. Configure pelo menos duas instâncias para cada função.
  3. Manter estado no armazenamento durável, não em instâncias de função.
  4. Trate corretamente o evento StatusCheck.
  5. Quebre alterações relacionadas em transações quando possível.
  6. Verifique se as tarefas de função de trabalho são idempotente e reinicializáveis.
  7. Continue a invocar operações até que forem bem-sucedidas.
  8. Considere autoscaling estratégias.

###<a name="virtual-machines"></a>Máquinas virtuais

  1. Examine a seção de máquinas virtuais deste documento.
  2. Não use a unidade de disco D para armazenamento persistente.
  3. Agrupar máquinas em um nível de serviço em um conjunto de disponibilidade.
  4. Configure balanceamento de carga e testes opcionais.

###<a name="storage"></a>Armazenamento

  1. Examine a seção de armazenamento deste documento.
  2. Use várias contas de armazenamento quando dados ou largura de banda excede cotas.

###<a name="sql-database"></a>Banco de dados SQL

  1. Examine a seção de banco de dados SQL deste documento.
  2. Implemente uma diretiva de Repetir para tratar erros temporárias.
  3. Use partição/fragmentação como uma estratégia de escala-out.

###<a name="sql-server-on-virtual-machines"></a>SQL Server em máquinas virtuais

  1. Examine o SQL Server em máquinas virtuais seção deste documento.
  2. Siga as recomendações anteriores para máquinas virtuais.
  3. Use recursos de alta disponibilidade do SQL Server, como sempre ativado.

###<a name="service-bus"></a>Barramento de serviço

  1. Examine a seção de barramento de serviço deste documento.
  2. Considere a criação de uma fila durável do lado do cliente como um backup.

###<a name="hdinsight"></a>HDInsight

  1. Examine a seção de HDInsight deste documento.
  2. Não há etapas de disponibilidade adicionais são necessárias para falhas locais.

##<a name="next-steps"></a>Próximas etapas

Este artigo é parte de uma série voltada para [orientações técnicas resiliência Azure](./resiliency-technical-guidance.md). Próximo artigo desta série é a [recuperação de uma interrupção do serviço de toda a região](./resiliency-technical-guidance-recovery-loss-azure-region.md).
