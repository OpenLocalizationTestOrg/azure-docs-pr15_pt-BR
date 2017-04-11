<properties
   pageTitle="Recuperação de dados para aplicativos do Azure | Microsoft Azure"
   description="Visão geral técnica e informações detalhadas sobre a criação de aplicativos para recuperação de dados no Microsoft Azure."
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

#<a name="disaster-recovery-for-applications-built-on-microsoft-azure"></a>Recuperação de dados em aplicativos criados no Microsoft Azure

Enquanto a alta disponibilidade é sobre o gerenciamento de falha temporária, recuperação de dados (DR) é sobre o grave de perda de funcionalidade do aplicativo. Por exemplo, considere o cenário onde desce uma região. Nesse caso, você precisa ter um plano para executar o aplicativo ou acessar seus dados fora da região Azure. Execução deste plano envolve pessoas, processos e aplicativos de suporte que permitem a função do sistema. Os proprietários de negócios e tecnologia que definem o modo do sistema operacional para um desastre também determinam o nível de funcionalidade para o serviço durante um desastre. O nível de funcionalidade pode levar alguns formulários: totalmente indisponível, parcialmente disponível (degradado funcionalidade ou atrasada processamento), ou totalmente disponível.

##<a name="azure-disaster-recovery-features"></a>Recursos de recuperação de desastres Azure

Como com considerações de disponibilidade, Azure tem [orientações técnicas resiliência](./resiliency-technical-guidance.md) projetado para dar suporte a recuperação de dados. Também há uma relação entre alguns dos recursos de disponibilidade do Azure e recuperação de desastres. Por exemplo, o gerenciamento de funções em domínios de falhas aumenta a disponibilidade de um aplicativo. Sem que o gerenciamento, uma falha de hardware não tratada tornaria um cenário de "desastres". Portanto, a aplicação correta dos recursos de disponibilidade e estratégias é uma parte importante de revisão de texto de desastres seu aplicativo. No entanto, este artigo vai além dos problemas de disponibilidade geral para eventos de desastres mais sério (e mais raros).

##<a name="multiple-datacenter-regions"></a>Várias regiões de data center

Azure mantém dos data centers em muitos regiões do mundo. Este infraestrutura oferece suporte a vários cenários de recuperação de desastres, como o fornecido pelo sistema geográfica replicação de armazenamento do Azure para regiões secundários. Isso também significa que você pode facilmente e econômica implantar um serviço de nuvem para vários locais em todo o mundo. Compare isso com o custo e a dificuldade de executar seu próprio dos data centers em várias regiões. Implantar dados e serviços para várias regiões ajuda a proteger seu aplicativo contra interrupções principais em uma única região.

##<a name="azure-traffic-manager"></a>Gerenciador de tráfego Azure

Quando ocorrer uma falha específica de região, você deve redirecionar o tráfego para serviços ou implantações em outra região. Você pode fazer esse roteamento manualmente, mas é mais eficiente usar um processo automatizado. Gerenciador de tráfego Azure destina-se esta tarefa. Você pode usá-lo para gerenciar o failover de tráfego de usuário para outra região automaticamente caso a região primária falha. Como o gerenciamento de tráfego é uma parte importante da estratégia de geral, é importante entender as Noções básicas do Gerenciador de tráfego.

No diagrama a seguir, os usuários se conectar a um URL que tenha especificado para o gerente de tráfego (__http://myATMURL.trafficmanager.net__) e que resumos as URLs do site real (__http://app1URL.cloudapp.net__ e __http://app2URL.cloudapp.net__). Com base em como você configurar os critérios para quando a usuários de roteiro, os usuários serão enviados para o site real correto quando a política dita. As opções de política são alternada, desempenho ou failover. Neste artigo, vamos será preocupados com apenas a opção de failover.

![Roteamento através do Azure tráfego Manager](./media/resiliency-disaster-recovery-azure-applications/routing-using-azure-traffic-manager.png)

Quando você estiver configurando o Gerenciador de tráfego, você fornece um novo prefixo de tráfego Gerenciador DNS. Este é o prefixo de URL que você irá fornecer aos usuários para acessar seu serviço. Gerenciador de tráfego agora elimina um nível para cima e não no nível de região de balanceamento de carga. O Gerenciador de tráfego DNS mapeia para um CNAME para todas as implantações que gerencia.

No Gerenciador de tráfego, você especifica a prioridade das implantações de que os usuários serão roteados para quando ocorre falha. Gerenciador de tráfego monitora os pontos de extremidade das implantações e anotações quando a implantação primário falha. Na falha, Gerenciador de tráfego analisa a lista de implantações prioridades rotas e os usuários para a próxima na lista.

Embora o gerente de tráfego decide para onde ir a um failover, você pode decidir se o seu domínio de failover está inativo ou ativo enquanto você não estiver no modo de tolerância. Essa funcionalidade tem nada a ver com o Gerenciador de tráfego do Azure. Gerenciador de tráfego detecta uma falha no local principal e muda para o site de failover. Gerenciador de tráfego passa sobre independentemente se esse site está servindo atualmente usuários ou não.

Para obter mais informações sobre como funciona o Gerenciador de tráfego do Azure, consulte:

 * [Visão geral do Gerenciador de tráfego](../traffic-manager/traffic-manager-overview.md)
 * [Configurar o método de roteamento de failover](../traffic-manager/traffic-manager-configure-failover-routing-method.md)

##<a name="azure-disaster-scenarios"></a>Cenários de desastres Azure

As seções a seguir abrangem vários tipos diferentes de cenários de desastres. Interrupções de serviço de toda a região não são a causa somente das falhas de todo o aplicativo. Erros de design ou administração má também podem levar a interrupções. É importante levar em consideração as possíveis causas de uma falha durante a criação e a fase de testes de seu plano de recuperação. Um bom plano tira proveito dos recursos do Azure e aumenta-los com estratégias específicas do aplicativo. A resposta escolhida é determinada pela importância do aplicativo, o objetivo de ponto de recuperação (RPO) e o objetivo de tempo de recuperação (RTO).

###<a name="application-failure"></a>Falha de aplicativo

Gerenciador de tráfego Azure automaticamente trata falhas resultantes do software subjacente de hardware ou sistema operacional na máquina virtual host. Azure cria uma nova instância da função em um servidor de funcionamento e adiciona à rotação de Balanceador de carga. Se o número de instâncias de função for maior do que um, o Azure desloca processamento para as outras instâncias de função em execução ao substituir o nó que falhou.

Há erros de aplicativos séria que acontecem independentemente dos quaisquer falhas de hardware ou sistema operacional. O aplicativo pode falhar devido às exceções graves causadas por lógica incorreta ou problemas de integridade de dados. Você deve incorporar suficiente telemetria o código para que um sistema de monitoramento pode detectar condições de falha e notificar um administrador do aplicativo. Um administrador que tenha total conhecimento dos processos de recuperação de desastres pode tomar uma decisão invocar um processo de failover. Como alternativa, um administrador pode simplesmente aceitar uma interrupção de disponibilidade para resolver os erros críticos.

###<a name="data-corruption"></a>Corrupção de dados

Azure armazena automaticamente os dados do Azure SQL Database e armazenamento do Azure três vezes redundante dentro de domínios de falha diferente na mesma região. Se você usar replicação geográfica, os dados são armazenados três vezes adicionais em uma região diferente. No entanto, se seu aplicativo ou seus usuários danificar dados na cópia primária, os dados rapidamente replica para as outras cópias. Infelizmente, isso resulta em três cópias de dados corrompidos.

Para gerenciar possível corrupção de dados, você tem duas opções. Primeiro, você pode gerenciar uma estratégia de backup personalizada. Você pode armazenar backups em Azure ou local, dependendo das suas necessidades de negócios ou normas de controle. Outra opção é usar a nova opção de restauração no momento para recuperar um banco de dados do SQL. Para obter mais informações, consulte a seção sobre [estratégias de dados para recuperação de dados](#data-strategies-for-disaster-recovery).

###<a name="network-outage"></a>Interrupção de rede

Quando partes da rede Azure estão inacessíveis, você não poderá chegar ao seu aplicativo ou seus dados. Se uma ou mais instâncias de função não estão disponíveis devido a problemas de rede, o Azure usa as instâncias restantes disponíveis do seu aplicativo. Se seu aplicativo não pode acessar seus dados devido a uma interrupção de rede Azure, você pode potencialmente executar em modo degradado localmente usando dados armazenados em cache. Você precisa planejar a estratégia de recuperação de desastres para executar em modo degradado em seu aplicativo. Para alguns aplicativos, isso pode não ser prático.

Outra opção é armazenar dados em um local alternativo até a conectividade é restaurada. Se modo degradado não for uma opção, as opções restantes são inatividade ou failover para uma região alternativo. O design de um aplicativo executado em modo degradado é quanto uma decisão de negócios como um técnico. Isso é discutido mais na seção [degradado funcionalidade do aplicativo](#degraded-application-functionality).

###<a name="failure-of-a-dependent-service"></a>Falha de um serviço dependente

Azure fornece muitos serviços que podem enfrentar o tempo de inatividade periódico. Considere [Azure relacionada Cache](https://azure.microsoft.com/services/cache/) como um exemplo. Esse serviço de vários locatário fornece recursos de cache para seu aplicativo. É importante levar em consideração o que acontece em seu aplicativo se o serviço dependente não estiver disponível. De várias maneiras, esse cenário é semelhante ao cenário de interrupção de rede. No entanto, considerando cada serviço independentemente resulta em possíveis aprimoramentos para o seu plano geral.

Cache de relacionada Azure fornece armazenamento em cache para seu aplicativo de dentro sua implantação de serviço de nuvem, que fornece benefícios de recuperação de desastres. Primeiro, o serviço agora é executado em funções que são locais para sua implantação. Portanto, conseguir melhor monitorar e gerenciar o status do cache como parte dos seus processos de gerenciamento geral para o serviço de nuvem. Esse tipo de cache também expõe novos recursos. Um dos novos recursos é alta disponibilidade para dados armazenados em cache. Isso ajuda a preservar dados armazenados em cache se um único nó falhar mantendo cópias duplicadas em outros nós.

Observe que alta disponibilidade diminui a produtividade e aumenta a latência devido a atualização da cópia secundária nas gravações. Ele também dobra a quantidade de memória que é usada para cada item, portanto, planeje para isso. Este exemplo específico demonstra que cada serviço dependente pode ter recursos que melhoram sua disponibilidade geral e resistência à falhas graves.

Com cada serviço dependente, você deve compreender as implicações de uma interrupção do serviço. No exemplo cache, talvez seja possível acessar os dados diretamente a partir de um banco de dados até você restaurar seu cache. Isso seria um modo degradado em termos de desempenho, mas deve fornecer funcionalidade total com relação aos dados.

###<a name="region-wide-service-disruption"></a>Interrupção do serviço de toda a região

Falhas de anterior principalmente foram falhas que podem ser gerenciadas dentro da mesma região Azure. No entanto, você também deve preparar para a possibilidade de que não há uma interrupção do serviço de toda a região. Se ocorrer uma interrupção do serviço de toda a região, as cópias localmente redundantes dos seus dados não estão disponíveis. Se você tiver ativado replicação geográfica, há três cópias adicionais de seus blobs e tabelas em uma região diferente. Se Microsoft declarar a região perdida, Azure remapeia todas as entradas DNS para a região geográfica replicado.

>[AZURE.NOTE] Lembre-se de que você não tem qualquer controle sobre esse processo, e ela ocorrerá somente para interrupção do serviço de toda a região. Por isso, você deve confiar em outras estratégias de backup específicas do aplicativo para atingir o nível mais alto de disponibilidade. Para obter mais informações, consulte a seção sobre [estratégias de dados para recuperação de dados](#data-strategies-for-disaster-recovery).

###<a name="azure-wide-service-disruption"></a>Interrupção do serviço de todo o Azure

No planejamento de desastres, você deve considerar todo o intervalo de possíveis desastres. Uma das interrupções de serviço mais graves envolva todas as regiões Azure simultaneamente. Como com outras interrupções de serviço, você pode decidir que você vai levar o risco de tempo de inatividade temporário nesse caso. Interrupções de serviço amplo que se estendem por regiões devem ser muito mais raras que interrupções de serviço isolados que envolvem serviços dependentes ou regiões único.

No entanto, para alguns aplicativos essenciais, você pode decidir que deve haver um plano de backup para esse cenário também. O plano para este evento pode incluir falhando sobre serviços em uma [nuvem alternativo](#alternative-cloud) ou um [local e na nuvem solução híbrida](#hybrid-on-premises-and-cloud-solution).

###<a name="degraded-application-functionality"></a>Funcionalidade do aplicativo degradado

Um aplicativo elaborado geralmente usa um conjunto de módulos que comunicarem apesar a implementação de padrões de troca de informações flexíveis. Um aplicativo de DR amigável requer separação de tarefas no nível do módulo. Isso é para impedir que a interrupção de um serviço dependente desativação todo o aplicativo. Por exemplo, considere um aplicativo de comércio web para Y de empresa. Os seguintes módulos podem constituem o aplicativo:

 * __Catálogo de produtos__ permite aos usuários procurar produtos.
 * __Carrinho de compras__ permite aos usuários adicionar/remover produtos no seu carrinho de compras.
 * __Ordem de Status__ mostra o status de entrega de ordens de usuário.
 * __Envio de pedido__ finaliza a sessão de compra enviando a ordem de pagamento.
 * __Processamento de pedidos__ valida a ordem de integridade de dados e executa uma verificação de disponibilidade de quantidade.

Quando um dependente de um módulo neste aplicativo falhar, como o módulo funcionar até que essa parte recupera? Um sistema bem-arquitetado implementa limites de isolamento por meio de separação de tarefas no tempo de design e em tempo de execução. Você pode categorizar cada falha como recuperável e não recuperável. Erros não recuperável levará para baixo o módulo, mas você pode reduzir um erro recuperável por meio de alternativas. Conforme discutido na seção de alta disponibilidade, você pode ocultar alguns problemas de usuários Manipulando falhas e tomar ações alternativas. Durante uma interrupção de serviço mais séria, o aplicativo pode ser totalmente indisponível. Entretanto, uma terceira opção é continuar atendendo usuários em modo degradado.

Por exemplo, se o banco de dados para hospedar pedidos falhar, o módulo de processamento de pedidos perde sua capacidade de processar transações de vendas. Dependendo da arquitetura, pode ser difícil ou impossível para as partes de envio do pedido e processamento de pedidos do aplicativo para continuar. Se o aplicativo não é projetado para manipular esse cenário, todo o aplicativo pode ficar offline.

No entanto, neste cenário mesmo, é possível que os dados de produto são armazenados em um local diferente. Nesse caso, o módulo do catálogo de produtos ainda pode ser usado para exibir produtos. Em modo degradado, o aplicativo continua esteja disponível para usuários para funcionalidade disponível como exibir o catálogo de produtos. Outras partes do aplicativo, no entanto, estão disponíveis, como consultas ordenação ou estoque.

Outra variação de centros de modo degradado em desempenho em vez de recursos. Por exemplo, considere um cenário onde o catálogo de produtos está sendo armazenados em cache através do Azure relacionada Cache. Se o cache ficar indisponível, o aplicativo pode ir diretamente para o armazenamento de servidor para recuperar informações de catálogo de produtos. Mas esse acesso pode ser menor do que a versão em cache. Por isso, o desempenho do aplicativo está degradado até que o serviço de cache seja totalmente restaurado.

Decidir quanto de um aplicativo continuará a função em modo degradado é uma decisão de negócios e uma decisão técnica. O aplicativo também deve decidir como informar os usuários dos problemas temporários. Neste exemplo, o aplicativo pode permitir exibindo produtos e até mesmo adicionando-os ao carrinho de compras. No entanto, quando o usuário tentar fazer uma compra, o aplicativo notifica o usuário que o módulo de vendas está temporariamente inacessível. Não é ideal para o cliente, mas não impede uma interrupção do serviço de todo o aplicativo.

##<a name="data-strategies-for-disaster-recovery"></a>Estratégias de dados para recuperação de dados

Manipular dados corretamente é a área mais difícil obter direito em qualquer plano de recuperação de desastres. Restaurando dados também é parte do processo de recuperação que geralmente demora mais tempo. Opções diferentes nos modos de degradação resultarem em desafios difíceis de recuperação de dados de falha e consistência após falha.

Um dos fatores é a necessidade de restaurar ou manter uma cópia dos dados do aplicativo. Você usará esses dados para fins de transações em um local secundário e de referência. Um local configuração requer um processo de planejamento caro e demorado implementar uma estratégia de recuperação de desastres de várias regiões. Convenientemente, a maioria dos provedores de nuvem, incluindo Azure, permitir prontamente a implantação de aplicativos para várias regiões. Essas regiões são distribuídas geograficamente de forma que a interrupção do serviço de várias regiões deve ser muito rara. A estratégia de manipulação de dados em regiões é um dos fatores que contribuem para o sucesso de qualquer plano de recuperação de desastres.

As seções a seguir discutem técnicas de recuperação de desastres relacionadas a backups de dados, dados de referência e dados de transações.

###<a name="backup-and-restore"></a>Backup e restauração

Backups regulares dos dados de aplicativo podem oferecer suporte a alguns cenários de recuperação de desastres. Recursos de armazenamento diferentes requerem técnicas diferentes.

Para as camadas Basic, Standard e Premium banco de dados do SQL, você pode aproveitar restauração no momento para recuperar o banco de dados. Para obter mais informações, consulte [Visão geral: nuvem business banco de dados e continuidade de recuperação de dados com o banco de dados do SQL](../sql-database/sql-database-business-continuity.md). Outra opção é usar replicação de localização geográfica ativa do banco de dados do SQL. Isso automaticamente replica alterações de banco de dados para bancos de dados secundários na mesma região Azure ou mesmo em uma região diferente de Azure. Isso fornece uma alternativa possível para algumas das técnicas de sincronização de dados mais manuais apresentadas neste artigo. Para obter mais informações, consulte [Visão geral: banco de dados ativo geográfica replicação do SQL](../sql-database/sql-database-geo-replication-overview.md).

Você também pode usar uma abordagem mais manual para backup e restauração. Use o comando Copiar do banco de dados para criar uma cópia do banco de dados. Você deve usar este comando para obter um backup com consistência transacional. Você também pode usar o serviço de importação/exportação do Azure SQL Database. Oferece suporte à exportação bancos de dados para arquivos BACPAC que estão armazenados no armazenamento de Blob do Azure.

A redundância interna do armazenamento do Azure cria duas réplicas do arquivo de backup na mesma região. Entretanto, a frequência de execução do processo de backup determina seu RPO, que é a quantidade de dados que você pode perder em cenários de desastres. Por exemplo, imagine que você executar um backup na parte superior da hora e um desastre dois minutos antes do início da hora. Você perde 58 minutos de dados que aconteceram após o último backup foi executado. Além disso, para proteger contra uma interrupção de serviço de toda a região, você deve copiar os arquivos BACPAC para uma região alternativa. Então, você tem a opção de restaurar esses backups na região alternativo. Para obter mais detalhes, consulte [Visão geral: nuvem business banco de dados e continuidade de recuperação de dados com o banco de dados do SQL](../sql-database/sql-database-business-continuity.md).

Para o armazenamento do Azure, você pode desenvolver seu próprio processo de backup personalizado ou usar um dos muitos ferramentas de backup de terceiros. Observe que a maioria dos designs de aplicativos têm complexidades adicionais onde recursos de armazenamento referenciam entre si. Por exemplo, considere um banco de dados do SQL que tenha uma coluna que está vinculado a um blob no armazenamento do Azure. Se os backups não ocorrer simultaneamente, o banco de dados pode ter o ponteiro para um blob que não foi feito backup antes da falha. O aplicativo ou o plano de recuperação de desastres deve implementar processos para lidar com essa inconsistência após a recuperação.

###<a name="reference-data-pattern-for-disaster-recovery"></a>Padrão de dados de referência de recuperação de dados

Referência dados são somente leitura que ofereça suporte a funcionalidade do aplicativo. Geralmente não é alterada com frequência. Embora o backup e restauração é um método para lidar com interrupções no serviço de toda a região, o RTO é relativamente longo. Quando você implanta o aplicativo para uma região secundário, algumas estratégias podem melhorar o RTO dados de referência.

Porque alterações nos dados de referência raramente, você pode melhorar o RTO mantendo uma cópia permanente os dados de referência na região secundário. Isso elimina o tempo necessário para restaurar backups em caso de um desastre. Para atender os requisitos de recuperação de desastres de várias regiões, você deve implantar o aplicativo e os dados de referência juntos em várias regiões. Conforme mencionado no [padrão de dados de referência de alta disponibilidade](./resiliency-high-availability-azure-applications.md#reference-data-pattern-for-high-availability), você pode implantar dados de referência à função em si, para armazenamento externo ou uma combinação de ambos.

O modelo de implantação de dados de referência dentro de nós de computação implicitamente atende aos requisitos de recuperação de desastres. Implantação de dados de referência ao banco de dados do SQL requer que você implantar uma cópia dos dados de referência para cada região. A mesma estratégia aplica-se ao armazenamento do Azure. Você deve implantar uma cópia de quaisquer dados de referência que estão armazenados em armazenamento Azure até as regiões primárias e secundárias.

![Publicação de dados de referência para regiões primárias e secundários](./media/resiliency-disaster-recovery-azure-applications/reference-data-publication-to-both-primary-and-secondary-regions.png)

Você deve implementar sua própria rotina de backup específicas do aplicativo para todos os dados, incluindo dados de referência. Localização geográfica replicado cópias em regiões são usadas apenas em uma interrupção do serviço de toda a região. Para impedir que o tempo de inatividade estendido, implante as partes essenciais dos dados do aplicativo para a região secundária. Para um exemplo dessa topologia, consulte o [modelo ativo-passivo](#active-passive).

###<a name="transactional-data-pattern-for-disaster-recovery"></a>Padrão de dados de transações de recuperação de dados

Implementação de uma estratégia de modo de desastres totalmente funcional requer replicação assíncrona dos dados transações à região secundário. As janelas de tempo prático dentro do qual a replicação pode ocorrer determinará as características RPO do aplicativo. Você ainda poderá recuperar os dados que foram perdidos da região primária durante a janela de replicação. Você também poderá mesclar com a região secundária mais tarde.

Os exemplos de arquitetura a seguir fornecem algumas ideias sobre diferentes maneiras de lidar com dados de transações em um cenário de failover. É importante observar que esses exemplos não são completos. Por exemplo, locais de armazenamento intermediária como filas podem ser substituídos com o Azure SQL Database. As próprias filas podem ser filas de armazenamento do Azure ou barramento de serviço do Azure (consulte [filas Azure e barramento de serviço – comparada e comparados](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)). Os destinos de armazenamento do servidor também podem variar, como tabelas do Azure em vez de banco de dados SQL. Além disso, funções de trabalho podem ser inseridas como intermediários em várias etapas. O mais importante é não emular essas arquiteturas exatamente, mas levar em consideração várias alternativas de recuperação de dados de transações e módulos relacionados.

####<a name="replication-of-transactional-data-in-preparation-for-disaster-recovery"></a>Replicação de dados de transações em preparação para a recuperação de dados

Considere um aplicativo que usa filas de armazenamento do Azure para manter dados de transações. Isso permite que as funções de trabalho processar os dados de transação no banco de dados do servidor em uma arquitetura desacoplada. Isso requer que as transações usar alguma forma de cache temporário se as funções de front-end exigem a consulta imediata dos dados. Dependendo do nível de tolerância de perda de dados, você pode optar por replicar filas, o banco de dados ou todos os recursos de armazenamento. Com apenas replicação de banco de dados, se a região primária falhar, você ainda poderá recuperar os dados nas filas quando volta a região principal.

O diagrama a seguir mostra uma arquitetura onde o banco de dados do servidor esteja sincronizado em regiões.

![Replicação de dados de transações em preparação para a recuperação de dados](./media/resiliency-disaster-recovery-azure-applications/replicate-transactional-data-in-preparation-for-disaster-recovery.png)

O maior desafio para implementar essa arquitetura é a estratégia de replicação entre regiões. O serviço de sincronização de dados do SQL Azure permite esse tipo de replicação. No entanto, o serviço ainda está em visualização e não é recomendado para ambientes de produção. Para obter mais informações, consulte [Visão geral: nuvem business banco de dados e continuidade de recuperação de dados com o banco de dados do SQL](../sql-database/sql-database-business-continuity.md). Para aplicativos de produção, você deve investir em uma solução de terceiros ou criar sua própria lógica de replicação no código. Dependendo da arquitetura, a replicação será bidirecional, que também é mais complexa.

Uma implementação potencial pode fazer uso da fila intermediária no exemplo anterior. A função de trabalho que processa os dados para o destino de armazenamento final pode fazer a alteração na região de primário e secundário região. Estas não são tarefas simples e orientação completa para código de replicação está além do escopo deste artigo. O ponto importante é que muitas seu tempo e teste deve se concentrar em como você replicar seus dados para a região secundária. Processamento e testes adicionais podem ajudar a garantir que os processos de recuperação e failover corretamente manipulam dados possíveis inconsistências ou transações duplicadas.

>[AZURE.NOTE] A maioria deste artigo se concentra na plataforma como um serviço (PaaS). Entretanto, opções adicionais de replicação e disponibilidade para aplicativos híbridos usam máquinas virtuais do Azure. Esses aplicativos híbridos usam infraestrutura como um serviço (IaaS) para hospedar o SQL Server em máquinas virtuais no Azure. Isso permite abordagens tradicionais disponibilidade no SQL Server, como grupos de disponibilidade sempre ativado ou o envio de Log. Algumas técnicas, como sempre ativado, trabalhe somente entre instâncias do SQL Server local e Azure máquinas virtuais. Para obter mais informações, consulte [alta disponibilidade e recuperação de dados para o SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

####<a name="degraded-application-mode-for-transaction-capture"></a>Modo de aplicativo degradado para captura de transação

Considere uma arquitetura de segunda que operam em modo degradado. O aplicativo na região secundário desativa todas as funcionalidades, como relatórios, inteligência de negócios (BI), ou descarregar filas. Ele aceita somente os tipos mais importantes de transações fluxos de trabalho, conforme definido pelo necessidades de negócios. O sistema de captura as transações e as grava filas. O sistema pode adiar processamento dos dados durante a fase inicial da interrupção do serviço. Se o sistema na região primária é reativado dentro da janela de tempo esperado, as funções de trabalho na região primária podem consumir filas. Esse processo elimina a necessidade de mesclagem de banco de dados. Se a interrupção do serviço de região primária vai além da janela tolerável, pode iniciar o aplicativo as filas de processamento.

Neste cenário, o banco de dados no secundário contém dados de transações incrementais que devem ser mesclados após o principal é reativado. O diagrama a seguir mostra essa estratégia para armazenar temporariamente dados das transações até que a região primária seja restaurada.

![Modo de aplicativo degradado para captura de transação](./media/resiliency-disaster-recovery-azure-applications/degraded-application-mode-for-transaction-capture.png)

Para obter mais discussões de técnicas de gerenciamento de dados para aplicativos do Azure e apresentam resiliência, consulte [Failsafe: orientação para arquiteturas de nuvem flexível](https://channel9.msdn.com/Series/FailSafe).

##<a name="deployment-topologies-for-disaster-recovery"></a>Topologias de implantação de recuperação de dados

Você deve preparar aplicativos essenciais para a possibilidade de interrupção de um serviço de toda a região. Você pode fazer isso por incorporar uma estratégia de implantação de várias regiões o planejamento operacional.

Implantações de várias regiões podem envolver processos de IT pro para publicar os aplicativo e dados de referência da região secundária após um desastre. Se o aplicativo exigir failover instantânea, o processo de implantação pode envolver uma instalação de ativo/passivo ou um ativo/ativo. Esse tipo de implantação tem instâncias existentes do aplicativo em execução na região alternativo. Uma ferramenta de roteamento como o Azure tráfego Manager fornece serviços de balanceamento de carga no nível de DNS. Ele pode detectar interrupções do serviço e direcionar os usuários para diferentes regiões quando necessário.

Parte de uma recuperação Azure bem-sucedida é arquitetura que a recuperação à solução desde o início. A nuvem fornece opções adicionais para a recuperação de falhas durante um desastre que não estão disponíveis em um provedor de hospedagem tradicional. Especificamente, você pode rapidamente e dinamicamente alocar recursos para uma região diferente. Portanto, você não pagar muito para recursos ociosos enquanto você está aguardando uma falha ocorra.

As seções a seguir abrangem diferentes topologias de implantação de recuperação de dados. Normalmente, há uma ligação no aumento de custo ou complexidade para disponibilidade adicional.

###<a name="single-region-deployment"></a>Implantação de região

Uma implantação única região não é realmente uma topologia de recuperação de desastres, mas serve para contraste com as outras arquiteturas. Implantações de única região são comuns para aplicativos no Azure. No entanto, esse tipo de implantação não é um desafiante grave para um plano de recuperação de desastres.

O diagrama a seguir ilustra um aplicativo executado em uma única região Azure. Gerenciador de tráfego Azure e o uso de domínios de falhas e atualização de aumentam a disponibilidade do aplicativo dentro da região.

![Implantação de região](./media/resiliency-disaster-recovery-azure-applications/single-region-deployment.png)

Aqui, é claro que o banco de dados é um único ponto de falha. Embora o Azure replica os dados em domínios de falha diferente para réplicas internos, tudo isso ocorre na mesma região. O aplicativo não pode suportar uma falha grave. Se a região falhar, todos os domínios de falhas descem – incluindo todas as instâncias de serviço e recursos de armazenamento.

Todas, exceto os aplicativos menos críticos, você deve desenvolver um plano para implantar seus aplicativos em várias regiões. Você também deve considerar RTO e restrições em considerando que topologia de implantação usar de custo.

Vamos dar uma olhada agora padrões específicos para suportar falhas em diferentes regiões. Todos os exemplos usam duas regiões para descrever o processo.

###<a name="redeployment-to-a-secondary-azure-region"></a>Reimplantação para uma região Azure secundária

No padrão de reimplantação para uma região secundária, apenas a região primária tem aplicativos e bancos de dados em execução. A região secundária não está configurada para um failover automático. Portanto quando ocorre um desastre, você deve girar todas as partes do serviço na nova região. Isso inclui o upload de um serviço de nuvem no Azure, implantar o serviço de nuvem, restaurar os dados e alterar o DNS para redirecionar o tráfego.

Embora seja mais acessível das opções de várias regiões, ele tem as características RTO piores. Nesse modelo, os backups de banco de dados e pacote de serviço são armazenados locais ou da instância do armazenamento de Blob do Azure da região secundário. No entanto, você deve implantar um novo serviço e restaurar os dados antes de poder retomá-la operação. Mesmo se você automatizar totalmente a transferência de dados de armazenamento de backup, girando o ambiente de banco de dados novo consome um tempo considerável. Mover dados do armazenamento de disco de backup para o banco de dados vazio na região secundário é a parte mais cara o processo de restauração. Você deve fazer isso, no entanto, para trazer o novo banco de dados para um estado operacional porque ele não é replicado.

A melhor abordagem é armazenar os pacotes de serviço no armazenamento de Blob na região secundário. Isso elimina a necessidade de carregar o pacote no Azure, que é o que acontece quando você implanta de uma máquina de desenvolvimento local. Você pode implantar rapidamente os pacotes de serviço para um novo serviço de nuvem do armazenamento de Blob usando scripts do PowerShell.

Essa opção é prática somente para aplicativos não-críticos que podem suportar um RTO alto. Por exemplo, isso pode funcionar para um aplicativo que pode ser pressionada por várias horas, mas deve ser executado novamente dentro de 24 horas.

![Reimplantação para uma região Azure secundária](./media/resiliency-disaster-recovery-azure-applications/redeploy-to-a-secondary-azure-region.png)

###<a name="active-passive"></a>Ativo-passivo

O padrão de ativo-passivo é a opção que preferir muitas empresas. Este padrão fornece aprimoramentos para o RTO com um aumento relativamente pequeno em custo sobre o padrão de reimplantação.
Nesse cenário, há novamente um principal e uma região Azure secundária. Todo o tráfego vai para a implantação ativa na região primária. A região secundária melhor está preparada para recuperação de dados, porque o banco de dados está sendo executado em ambas as regiões. Além disso, um mecanismo de sincronização é in-loco entre elas. Essa abordagem espera pode envolver duas variações: uma abordagem somente banco de dados ou uma implantação completa na região secundário.

####<a name="database-only"></a>Somente banco de dados

A primeira variação do padrão ativo-passivo, apenas a região primária tem um aplicativo de serviço de nuvem implantado. No entanto, ao contrário o padrão de reimplantação, ambas as regiões são sincronizadas com o conteúdo do banco de dados. (Para obter mais informações, consulte a seção no [padrão de dados de transações de recuperação de dados](#transactional-data-pattern-for-disaster-recovery).) Quando ocorre um desastre, há menos requisitos de ativação. Você inicia o aplicativo na região secundário, alterar cadeias de caracteres de conexão para o novo banco de dados e alterar as entradas DNS para redirecionar o tráfego.

Como o padrão de reimplantação, você deve ter já armazenado os pacotes de serviço no armazenamento de Blob do Azure na região secundário para implantação mais rápida. Ao contrário de padrão de reimplantação, você não provoca a maioria dos sobrecarga que exige operação de restauração do banco de dados. O banco de dados está pronto e em execução. Isso salva uma quantidade significativa de tempo, tornando isso um padrão de DR acessível. Também é o padrão de DR mais popular.

![Somente a banco de dados ativo-passivo,](./media/resiliency-disaster-recovery-azure-applications/active-passive-database-only.png)

####<a name="full-replica"></a>Réplica completa

Na segunda variação do padrão ativo-passivo, a região principal e secundário região tem uma implantação completa. Essa implantação inclui os serviços de nuvem e um banco de dados sincronizado. No entanto, apenas a região primária ativamente está lidando com solicitações de rede dos usuários. A região secundária fica ativa somente quando a região primária experiências uma interrupção do serviço. Nesse caso, todas as novas solicitações de rede rotear a região secundário. Gerenciador de tráfego Azure pode gerenciar esse failover automaticamente.

Failover mais rápido do que a variação somente banco de dados ocorre porque os serviços já foram implantados. Este padrão fornece um RTO muito baixo. A região de failover secundário deve ser pronta imediatamente após falha da região primária.

Juntamente com um tempo de resposta mais rápido, esse padrão tem a vantagem de pré-alocação e implantando serviços de backup. Você não precisa se preocupar em uma região não ter o espaço para alocar novas instâncias no caso de um. Isso é importante se sua região Azure secundário está se aproximando capacidade. Não há nenhuma garantia (contrato de nível de serviço) que você instantaneamente poderão implantar um número de novos serviços de nuvem em qualquer região.

O tempo de resposta mais rápido com esse modelo, você deve ter escala semelhante (número de instâncias de função) nas regiões primárias e secundários. Apesar das vantagens, pagando para instâncias de computação não utilizada é dispendioso e isso pode não ser a escolha financeira mais recomendável. Por isso, é mais comum para usar uma versão de um pouco reduzida dos serviços de nuvem na região secundário. Em seguida, você pode rapidamente falhar ao longo e dimensionar a implantação secundário, se necessário. Você deve automatizar o processo de failover para que depois que a região principal estiver inacessível, ativar instâncias adicionais, dependendo da carga. Isso pode envolver o uso de um mecanismo de autoscaling como [escala de máquina virtual define](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

O diagrama a seguir mostra o modelo onde as regiões primárias e secundárias contêm um serviço de nuvem totalmente implantado em um padrão de ativo-passivo.

![Réplica completa, ativo-passivo](./media/resiliency-disaster-recovery-azure-applications/active-passive-full-replica.png)

###<a name="active-active"></a>Ativa

Agora, você está provavelmente descobrir a evolução dos padrões: diminuir o RTO aumenta os custos e a complexidade. A solução ativa realmente quebras essa tendência com relação ao custo.

Um padrão de ativa, os serviços de nuvem e o banco de dados são totalmente implantados em ambas as regiões. Ao contrário de modelo ativo-passivo, ambas as regiões recebem tráfego de usuário. Esta opção produz o tempo de recuperação mais rápido. Os serviços já são dimensionados para lidar com uma parte da carga em cada região. DNS já está habilitado para usar a região secundária. Não há complexidade adicional na determinação como rotear os usuários para a região apropriada. Agendamento alternado talvez seja possível. É mais provável que determinados usuários usaria uma região específica onde reside a cópia primária de seus dados.

No caso de failover, simplesmente desative o DNS para a região principal. Isso roteia todo o tráfego para a região secundária.

Mesmo nesse modelo, existem algumas variações. Por exemplo, o diagrama a seguir mostra um modelo de onde a região primária possui a cópia mestra do banco de dados. Os serviços de nuvem em ambas as regiões gravar esse banco de dados principal. A implantação secundária pode ler do banco de dados principal ou replicado. Replicação neste exemplo acontece uma maneira.

![Ativa](./media/resiliency-disaster-recovery-azure-applications/active-active.png)

Não há uma desvantagem a arquitetura ativa no diagrama anterior. A segunda região deve acessar o banco de dados na primeira região porque a cópia mestra reside lá. O desempenho cai significativamente quando você acessa dados de fora de uma região. Em chamadas de banco de dados de região cruzado, você deve considerar algum tipo de processamento em lotes estratégia para melhorar o desempenho dessas chamadas. Para obter mais informações, consulte [como usar o processamento em lotes para melhorar o desempenho do aplicativo de banco de dados SQL](../sql-database/sql-database-use-batching-to-improve-performance.md).

Uma arquitetura alternativa pode envolver cada região acessar seu próprio banco de dados diretamente. Neste modelo, algum tipo de replicação bidirecional é necessário para sincronizar os bancos de dados em cada região.

No padrão de ativa, você talvez não precise quantas instâncias na região primária como faria no padrão ativo-passivo. Se você tiver 10 instâncias na região primária em uma arquitetura de ativo-passivo, talvez seja necessário apenas 5 em cada região em uma arquitetura de ativa. Ambas as regiões agora compartilham a carga. Isso pode ser uma economia de custos sobre o padrão de ativo-passivo se mantiver um quentes espera na região passivo com 10 instâncias aguardando failover.

Perceba que até você restaurar a região principal, secundário região pode receber uma sobrecarga súbita de novos usuários. Se houver 10.000 usuários em cada servidor quando a região primária experiências uma interrupção do serviço, a região secundária inesperadamente tem lidar com 20.000 usuários. Monitoramento regras na região secundário deve detectar esse aumento e duplo as instâncias na região secundário. Para obter mais informações sobre isso, consulte a seção sobre [a detecção de falha](#failure-detection).

##<a name="hybrid-on-premises-and-cloud-solution"></a>Locais e na nuvem solução híbrida

Uma estratégia adicional para recuperação de dados é projetar um aplicativo híbrido que executa o local e na nuvem. Dependendo do aplicativo, a região principal pode ser qualquer local. Considere as arquiteturas anterior e imagine a região primária ou secundária como um local na.

Há alguns desafios essas arquiteturas híbrido. Primeiro, a maior parte deste artigo atendeu padrões de arquitetura de PaaS. Aplicativos de PaaS típicos no Azure dependem construções do Azure específicos como funções, serviços de nuvem e Gerenciador de tráfego. Para criar uma solução local para este tipo de aplicativo de PaaS exijam uma arquitetura consideravelmente diferente. Isso pode não ser viável de uma perspectiva de custo ou de gerenciamento.

Entretanto, uma solução híbrida para recuperação de dados tem menos desafios para arquiteturas tradicionais que simplesmente movidos para a nuvem. Isso é verdadeiro de arquiteturas que usam IaaS. Aplicativos de IaaS usam máquinas virtuais na nuvem, que pode ter equivalentes direto no local. Você também pode usar redes virtuais para se conectar a máquinas na nuvem com recursos de rede local. Isso abre várias possibilidades que não são possíveis com aplicativos somente PaaS. Por exemplo, SQL Server possa aproveitar soluções de recuperação de desastres como grupos de disponibilidade sempre ativado e espelhamento de banco de dados. Para obter detalhes, consulte [alta disponibilidade e recuperação de dados para SQL Server no Azure máquinas virtuais](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

Soluções IaaS também fornecem um caminho mais fácil para aplicativos de local para usar o Azure como a opção de failover. Você pode ter um aplicativo totalmente funcional em uma região local existente. No entanto, o que acontece se você não tem os recursos para manter uma região geograficamente separada para failover? Você pode decidir usar máquinas virtuais e redes virtuais para obter o aplicativo em execução no Azure. Nesse caso, defina processos que sincronizar dados com a nuvem. Implantação do Azure, em seguida, torna-se a região secundária para usar para failover. A região primária permanece o aplicativo de local. Para obter mais informações sobre recursos e arquiteturas de IaaS, consulte a [documentação de máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/).

##<a name="alternative-cloud"></a>Nuvem alternativo

Há situações em que até mesmo a eficiência do Microsoft Cloud pode não atender aos regras de conformidade internos ou políticas que sua organização necessita. O melhor design para implementar sistemas de backup durante um desastre e preparação ficar curtos se houver uma interrupção de serviço global de um provedor de serviços de nuvem.

Você vai querer comparar os requisitos de disponibilidade com o custo e a complexidade da maior disponibilidade. Executar uma análise de risco e defina o RTO e RPO para sua solução. Se seu aplicativo não é possível suportar qualquer tempo de inatividade, ele pode fazer sentido para você considerar usando outra solução de nuvem. A menos que toda a Internet falhar, outra solução de nuvem ainda pode estar disponível se Azure ficar globalmente inacessível.

Como com cenário híbrida, as implantações de failover nas arquiteturas de recuperação de desastres anteriores também podem existir dentro de outra solução de nuvem. Sites de nuvem alternativo DR devem ser usados apenas para as soluções cujo RTO permite muito pouco, se houver, tempo de inatividade. Observe que uma solução que usa um site de DR fora do Azure exigirá mais trabalho para configurar, desenvolver, implantar e manter. Também é mais difícil implementar práticas recomendadas em uma arquitetura de nuvem cruzado. Embora plataformas de nuvem tem conceitos de alto nível semelhantes, as APIs e arquiteturas são diferentes.

Se você decidir dividir seu DR entre plataformas diferentes, faria sentido projetar camadas de abstração no design da solução. Se você fizer isso, não será preciso desenvolver e manter as duas versões diferentes do mesmo aplicativo para plataformas de nuvem diferentes no caso de desastres. Como com o cenário de implantação híbrida, o uso de máquinas virtuais do Azure ou serviço de contêiner do Azure talvez seja mais fácil nesses casos que o uso de designs de PaaS nuvem específicas.

##<a name="automation"></a>Automação

Alguns dos padrões que discutimos apenas exigem ativação rápida de implantações offline, bem como a restauração de partes específicas de um sistema. Automação ou script, suporta a capacidade de ativar recursos sob demanda e implantar soluções rapidamente. Neste artigo, automação relacionados DR seja equiparada com [PowerShell do Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx), mas a [API do serviço de gerenciamento de REST](https://msdn.microsoft.com/library/azure/ee460799.aspx) também é uma opção.

Desenvolver scripts ajuda a gerenciar as partes do DR Azure não tratar transparente. Isso tem o benefício de produzir resultados consistentes cada vez, o que minimiza a chance de erros humanos. Também tendo predefinidos DR scripts reduz o tempo para reconstruir um sistema e suas partes constituintes no meio de um desastre. Você não quiser tentar descobrir manualmente como restaurar seu site enquanto ela estiver inativa e perder dinheiro cada minuto.

Depois de criar os scripts, testá-las repetidamente do início ao fim. Depois de verificar sua funcionalidade básica, certifique-se de que você testá-las em [simulação de desastre](#disaster-simulation). Isso ajuda a descobrir falhas na scripts ou processos.

Uma prática recomendada com a automação é criar um repositório de scripts do PowerShell ou scripts de interface de linha (comando) para recuperação Azure. Claramente marcar e categorizá-los para pesquisa fácil. Designe uma pessoa para gerenciar o repositório e controle de versão dos scripts. Documento-los bem com explicações sobre parâmetros e exemplos de uso de script. Também, certifique-se de que você mantenha esta documentação em sincronia com as implantações do Azure. Isso enfatiza a finalidade de ter uma pessoa responsável pelo todas as partes do repositório.

##<a name="failure-detection"></a>Detecção de falhas

Para corretamente lidar com problemas com a disponibilidade e a recuperação de dados, você deve ser capaz de detectar e diagnosticar falhas. Você deve fazer servidor avançado e monitoramento de implantação para que você possa saber rapidamente quando um sistema ou suas partes são inesperadamente para baixo. Ferramentas que examinar a integridade geral do serviço de nuvem e suas dependências de monitoramento pode realizar parte desse trabalho. Uma ferramenta de Microsoft é o [sistema central 2016](https://www.microsoft.com/en-us/server-cloud/products/system-center-2016/). Ferramentas de terceiros também podem fornecer recursos de monitoramento. A maioria das soluções de monitoramento rastrear contadores de chave de desempenho e disponibilidade do serviço.

Embora essas ferramentas sejam vitais, eles não substituem a necessidade de planejar para a detecção de falhas e relatório de dentro de um serviço de nuvem. Você deve planejar usar corretamente diagnóstico do Azure. Contadores de desempenho personalizados ou entradas de log de eventos também podem ser parte da estratégia de geral. Isso fornece mais dados durante falhas para diagnosticar o problema e restaurar recursos completos rapidamente. Ele também fornece métricas adicionais que podem usar as ferramentas de monitoramento para determinar a integridade do aplicativo. Para obter mais informações, consulte [Habilitando o diagnóstico do Azure nos serviços de nuvem do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md). Para uma discussão sobre como planejar geral "modelo de integridade", consulte [Failsafe: orientação para arquiteturas de nuvem flexível](https://channel9.msdn.com/Series/FailSafe).

##<a name="disaster-simulation"></a>Simulação de desastre

Testes de simulação envolve a criação de pequenas situações reais no andar trabalho para observar como os membros da equipe reagem. Simulações também mostram como eficaz as soluções são no plano de recuperação. Realize simulações de forma que os cenários criados não interrompem comerciais reais enquanto ainda se sinta como situações reais.

Considere a possibilidade de arquitetura de um tipo de "menu de controle" no aplicativo para simular manualmente os problemas de disponibilidade. Por exemplo, por meio de um switch suave, dispare exceções de acesso de banco de dados de um módulo de pedidos fazendo com que ele não funcione corretamente. Você pode levar abordagens leves semelhantes para outros módulos no nível da interface de rede.

A simulação realça os problemas que foram endereçados inadequada. Os cenários simulados devem ser completamente controláveis. Isso significa que, mesmo se o plano de recuperação parece estar falhando, você pode restaurar a situação voltar ao normal sem causando danos significativos. Também é importante que você informar gerenciamento de alto nível sobre quando e como os exercícios de simulação serão executados. Este plano deve incluir informações sobre tempo ou recursos que podem se tornar improdutivos enquanto o teste de simulação está em execução. Quando você estiver submeter seu plano de recuperação de desastres a um teste, também é importante definir como sucesso será avaliado.

Há várias outras técnicas que você pode usar para testar planos de recuperação de desastres. No entanto, a maioria delas são versões alteradas simplesmente dessas técnicas básicas. A principal motive atrás esse teste é avaliar como viável e funcione como o plano de recuperação. Recuperação testes enfatiza os detalhes de descobrir furos no plano de recuperação básica.

##<a name="next-steps"></a>Próximas etapas

Este artigo faz parte de uma série de artigos voltada para [recuperação de dados e alta disponibilidade em aplicativos criados no Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). O artigo anterior desta série é [alta disponibilidade em aplicativos criados no Microsoft Azure](./resiliency-high-availability-azure-applications.md).
