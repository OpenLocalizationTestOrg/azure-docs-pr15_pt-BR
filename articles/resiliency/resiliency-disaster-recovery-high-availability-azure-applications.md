<properties
   pageTitle="Recuperação de dados e alta disponibilidade para aplicativos do Azure | Microsoft Azure"
   description="Visões gerais técnicas e informações detalhadas sobre como desenvolver aplicativos para alta disponibilidade e recuperação de desastres aplicativos criados no Microsoft Azure."
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

#<a name="disaster-recovery-and-high-availability-for-applications-built-on-microsoft-azure"></a>Recuperação de dados e alta disponibilidade em aplicativos criados no Microsoft Azure

##<a name="introduction"></a>Introdução

Este artigo se concentra em alta disponibilidade para aplicativos em execução no Azure. Uma estratégia geral para alta disponibilidade também inclui o aspecto da recuperação de dados. Planejamento para falhas e desastres na nuvem requer que você reconhecer as falhas rapidamente. Você então implementa uma estratégia que corresponde à sua tolerância de tempo de inatividade do aplicativo. Além disso, você deve considerar a extensão de perda de dados, que o aplicativo pode suportar sem causar consequências de negócios negativos como ele é restaurado.

A maioria das empresas que eles são preparados para falhas temporárias e grande escala. No entanto, antes de você responder essa pergunta para si mesmo, sua empresa testa essas falhas? Você testa a recuperação de bancos de dados para garantir que você tenha os processos corretos no lugar? Provavelmente não. Isso ocorre porque bem-sucedida de recuperação for iniciado com muitas planejamento e arquitetura para implementar esses processos. Assim como muitos outros requisitos não-funcionais, como segurança, recuperação de dados raramente obtém a análise prévio e alocação de tempo requer. Além disso, a maioria das empresas não têm o orçamento para regiões distribuídas geograficamente com capacidade redundante. Consequentemente, até mesmo aplicativos essenciais com frequência são excluídos do planejamento de recuperação de desastres adequado.

Plataformas de nuvem, como o Azure, fornecem dispersos geograficamente regiões do mundo. Essas plataformas também oferecem recursos que oferecem suporte a disponibilidade e uma variedade de cenários de recuperação de desastres. Agora, todos os aplicativos de nuvem crítico de missão podem ser fornecidos conclusão consideração para desastres de revisão de texto do sistema. Azure tem resiliência e recuperação de dados criadas para muitos dos seus serviços. Você deve estudar esses recursos de plataforma cuidadosamente e complementar com estratégias de aplicativos.

Este artigo descreve as etapas necessárias arquitetura você deve tomar para desastres prova uma implantação do Azure. Em seguida, você pode implementar o processo de continuidade de negócios maior. Um plano de continuidade de negócios é um roteiro para continuar as operações em condições negativos. Isso pode ser uma falha com a tecnologia, como um serviço inativo ou um desastre natural, como uma interrupção tempestade ou alimentação. Resiliência de aplicativo para desastres é apenas um subconjunto do processo de recuperação desastres maior, conforme descrito neste documento NIST: [Guia de planejamento de contingência para sistemas de tecnologia da informação](https://www.fismacenter.com/sp800-34.pdf).

As seções a seguir definem diferentes níveis de falhas, técnicas para lidar com eles e arquiteturas que essas técnicas de suporte. Essas informações fornecem entrada para seus processos de recuperação de desastres e procedimentos, para garantir que sua estratégia de recuperação de desastres funcione corretamente e eficiente.

##<a name="characteristics-of-resilient-cloud-applications"></a>Características de aplicativos de nuvem flexível

Um aplicativo bem-arquitetado pode suportar falhas de recurso em um nível tática e, em seguida, ele também pode suportar falhas de todo o sistema estratégicas no nível de região. As seções a seguir definem a terminologia referenciada em todo o documento para descrever vários aspectos dos serviços de nuvem flexível.

###<a name="high-availability"></a>Alta disponibilidade

Um aplicativo em nuvem altamente disponível implementa estratégias para absorvê a interrupção de dependências, como os serviços gerenciados oferecidos pela plataforma de nuvem. Apesar possíveis falhas dos recursos da plataforma de nuvem, essa abordagem permite que o aplicativo para continuar a exibem as características de sistemática funcionais e inoperante esperadas. Isso é coberto profundamente a série de vídeos de 9 de canal, [Failsafe: orientação para arquiteturas de nuvem flexível](https://channel9.msdn.com/Series/FailSafe).

Ao implementar o aplicativo, você deve considerar a probabilidade de uma falta de recurso. Além disso, considere o impacto que uma interrupção terá sobre o aplicativo a partir da perspectiva de negócios, antes de mergulhar profunda as estratégias de implementação. Sem vencimento consideração o impacto de negócios e a probabilidade de atingir a condição de risco, a implementação pode ser caro e possivelmente desnecessários.

Considere uma analogia automotiva para alta disponibilidade. Até mesmo partes de qualidade e engenharia superior não impede eventuais falhas. Por exemplo, quando seu carro obtém pneus de planas, ainda executa o carro, mas ele está funcionando com funcionalidade degradada. Se você planejado para esta ocorrência potencial, você pode usar um desses pneus reserva fino-Coroados até atingir uma loja de reparo. Embora o pneus reserva não permitem velocidades, você ainda pode operar o veículo até que você substitua os pneus. Da mesma forma, um serviço de nuvem que planos para possibilidade de perda de recursos pode impedir um problema relativamente secundário desativação todo o aplicativo. Isso é verdadeiro mesmo se o serviço de nuvem deve executar com funcionalidade degradada.

Há algumas características principais dos serviços de nuvem altamente disponível: disponibilidade, escalabilidade e tolerância. Embora essas características são inter-relacionados, é importante entender cada, e como eles contribuam para a disponibilidade geral da solução.

###<a name="availability"></a>Disponibilidade

Um aplicativo disponível considera a disponibilidade de seus serviços dependentes e infraestrutura subjacente. Aplicativos disponíveis remover pontos únicos de falha por meio de redundância e design flexível. Quando você ampliar o escopo a considerar disponibilidade no Azure, é importante entender o conceito da disponibilidade eficaz da plataforma. Disponibilidade eficaz considera os contratos de nível de serviço (SLA) de cada serviço dependente e seus efeitos acumulado na disponibilidade total do sistema.

Disponibilidade do sistema é a medida de porcentagem de uma janela de tempo que o sistema será capaz de operar. Por exemplo, a disponibilidade SLA de pelo menos duas instâncias de uma função da web ou de trabalho no Azure é 99,95% (ausência 100 por cento). Ele não medir o desempenho ou a funcionalidade dos serviços em execução nessas funções. No entanto, a disponibilidade eficaz de seu serviço de nuvem também é afetada pelos vários SLAs dos outros serviços dependentes. As partes móveis mais dentro do sistema, o mais cuidado que você deverá tomar para garantir que o aplicativo resiliently pode atender aos requisitos de disponibilidade de seus usuários finais.

Considere os seguintes SLAs para um serviço Azure que usa os serviços do Azure: computação, Azure SQL Database e armazenamento do Azure.

|Serviço do Azure|SLA   |Possíveis minutos tempo de inatividade/mês (30 dias)|
|:------------|:-----|:----------------------------------------:|
|Calcular      |99,95%|21.6 minutos                              |
|Banco de dados SQL |99,99%|4.3 minutos                              |
|Armazenamento      |99.90%|minutos 43,2                              |

Você deve planejar para todos os serviços para potencialmente ir para baixo em momentos diferentes. Neste exemplo simplificado, o número total de minutos por mês em que o aplicativo pode estar inativo é 108 minutos. Um mês de 30 dias tem um total de 43,200 minutos. 108 minutos é.25 porcentagem do número total de minutos em um mês de 30 dias (43,200 minutos). Isso lhe dá uma disponibilidade eficaz de porcentagem de 99.75 para o serviço de nuvem.

No entanto, usando técnicas de disponibilidade descritas neste artigo pode melhorar isso. Por exemplo, se você criar seu aplicativo para continuar a execução quando o banco de dados do SQL não estiver disponível, você pode remover que da equação. Isso significa que o aplicativo é executado com recursos reduzidos, portanto, também existem necessidades de negócios a considerar. Para obter uma lista completa dos SLAs do Azure, consulte [Contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/).

###<a name="scalability"></a>Escalabilidade

Escalabilidade afeta diretamente a disponibilidade. Um aplicativo que falha em aumento de carga não está mais disponível. Aplicativos Scalable são capazes de atender à demanda crescente com resultados consistentes, no windows de tempo aceitável. Quando um sistema é scalable, ele pode ser expandido horizontalmente ou verticalmente para gerenciar aumentos de carga, mantendo o desempenho consistente. Em termos básicos, dimensionamento horizontal adiciona mais máquinas do mesmo tamanho (processador, memória e largura de banda), enquanto aumenta escala vertical o tamanho das máquinas existentes. Para Azure, você tem opções de escala verticais para selecionar vários tamanhos de máquina para computação. Entretanto, alterar o tamanho da máquina requer uma implantação novamente. Portanto, as soluções mais flexíveis foram projetadas para dimensionamento horizontal. Isso é especialmente verdadeiro para computação, porque você pode facilmente aumentar o número de instâncias de qualquer função web ou de trabalho em execução. Essas instâncias adicionais lidar com maior tráfego através do portal da Web do Azure, scripts do PowerShell ou código. Base essa decisão nos aumentos de métricas de monitoradas específicas. Neste cenário, o desempenho do usuário ou métricas não sofrem uma queda perceptível sob carga. Normalmente, as funções da web e trabalhador armazenam qualquer estado externamente. Isso permite balanceamento de carga flexível e manipulação normal de quaisquer alterações feitas contagens de instância. Dimensionamento horizontal também funciona bem com serviços, como o armazenamento do Azure, que não fornecem opções hierárquicas para dimensionamento vertical.

Implantações de nuvem devem ser consideradas como um conjunto de unidades de escala. Isso permite que o aplicativo seja elástica no atendendo às necessidades de produtividade dos usuários finais. As unidades de escala são mais fáceis de visualizar no nível do servidor web e de aplicativos. Isso ocorre porque o Azure já fornece nós de computação sem estado através de funções de trabalho e da web. Adicionar que mais unidades de escala para a implantação de computação não causará quaisquer efeitos de lado de gerenciamento de estado de aplicativo, como unidades de escala de computação são sem estado. Uma unidade de escala de armazenamento é responsável por gerenciar uma partição de dados (estruturados ou não). Exemplos de unidades de escala de armazenamento incluem partição de tabela do Azure, contêiner de Blob do Azure e Azure SQL Database. Mesmo ao uso de várias contas de armazenamento do Azure tem um impacto direto na escalabilidade do aplicativo. Você deve projetar um serviço de nuvem altamente escaláveis para incorporar unidades de escala de armazenamento vários. Por exemplo, se um aplicativo usa dados relacionais, partição os dados em vários bancos de dados do SQL. Isso permite que o armazenamento acompanhar o modelo de unidade de escala de computação elástica. Da mesma forma, o armazenamento do Azure permite dados partição esquemas que exigem proposital designs para atender às necessidades de produtividade da camada de computação. Para uma lista de práticas recomendadas para criar serviços de nuvem scalable, consulte [Práticas recomendadas para o Design de serviços de em grande escala em serviços de nuvem do Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/).

###<a name="fault-tolerance"></a>Tolerância

Aplicativos devem assumir que cada recurso de nuvem dependentes pode e irão para baixo em algum momento no tempo. Um aplicativo de tolerância a falhas detecta e manobras em torno de elementos falhou, continue e retornar os resultados corretos dentro de um período específico. Condições de erro temporárias, um sistema de tolerância a falhas será usam uma política de repetição. Para mais sérias falhas, o aplicativo pode detectar problemas e alternar para hardware alternativo ou planos de contingência até que a falha seja corrigida. Um aplicativo confiável corretamente pode gerenciar a falha de uma ou mais partes e continuar funcionando corretamente. Aplicativos com tolerância a falhas podem usar um ou mais estratégias de design, como redundância, replicação ou funcionalidade degradada.

##<a name="disaster-recovery"></a>Recuperação de dados

Uma implantação de nuvem pode parar de funcionar devido a uma interrupção sistemática dos serviços dependentes ou a infraestrutura subjacente. Sob tais condições, um plano de continuidade de negócios aciona o processo de recuperação de desastres. Esse processo normalmente envolve a equipe de operações e procedimentos automatizados para reativar o aplicativo em uma região disponível. Isso requer a transferência de dados, os usuários de aplicativos e serviços para a nova região. Ele também envolve o uso de mídia de backup ou replicação contínua.

Considere a analogia anterior que comparados alta disponibilidade para a capacidade de recuperação de pneus de planas por meio do uso um spare. Em contraste, recuperação envolve as etapas feitas após uma falha de carro, onde o carro já não está funcionando. Nesse caso, a melhor solução é encontrar uma maneira eficiente de alterar carros, ligando para um serviço de viagem ou um amigo. Nesse cenário, há provavelmente será um atraso mais em obter de volta em trânsito. Também há mais complexidade no reparando e retornar para o veículo original. Da mesma forma, a recuperação de dados para outra região é uma tarefa complexa que normalmente envolve algum tempo de inatividade e a possibilidade de perda de dados. Para entender melhor e avaliar estratégias de recuperação de desastres, é importante definir dois termos: objetivo (RPO) de ponto de recuperação e objetivo de tempo de recuperação (RTO).

###<a name="recovery-time-objective"></a>Objetivo de tempo de recuperação

O RTO é a quantidade máxima de tempo alocado para restaurar a funcionalidade do aplicativo. Isso é com base nas necessidades de negócios e ele está relacionado à importância do aplicativo. Aplicativos de negócios críticos exigem um RTO baixo.

###<a name="recovery-point-objective"></a>Objetivo de ponto de recuperação

O RPO é a janela de tempo aceitável de perda de dados devido ao processo de recuperação. Por exemplo, se o RPO é uma hora, você deve completamente backup ou replicar os dados pelo menos a cada hora. Após você trazer o aplicativo em uma região alternativa, os dados de backup podem estar ausentes até uma hora de dados. Como o RTO, aplicativos essenciais direcionar um RPO muito menor.

##<a name="checklist"></a>Lista de verificação

Vamos resumir os pontos-chave que foram abordados neste artigo (e seus artigos relacionados em [alta disponibilidade](./resiliency-high-availability-azure-applications.md) e [recuperação de dados](./resiliency-disaster-recovery-azure-applications.md) para aplicativos do Azure). Este resumo irá atuar como uma lista de verificação de itens que você deve considerar para suas próprias disponibilidade e planejamento de recuperação de desastres. Estas são as práticas recomendadas que foram úteis para clientes que buscam seriamente sobre como implementar uma solução bem-sucedida.

1. Conduza uma avaliação de risco para cada aplicativo, porque cada pode ter requisitos diferentes. Alguns aplicativos são mais importantes que outros e seriam justificar o custo para projetá-los para recuperação extra.
1. Use essas informações para definir o RTO e RPO para cada aplicativo.
1. Design falha, começando com a arquitetura do aplicativo.
1. Implementar práticas recomendadas para alta disponibilidade, e equilibrando custo, complexidade e risco.
1. Implementar planos de recuperação de desastres e processos.
  * Considere a possibilidade de falhas que se estendem por nível de módulo totalmente para uma interrupção de nuvem concluída.
  * Estabelece estratégias de backup para todos os referência e dados de transações.
  * Escolha uma arquitetura de recuperação de desastres em vários locais.
1. Defina um proprietário específico para processos de recuperação de desastres, automação e teste. O proprietário deve gerenciar e o proprietário de todo o processo.
1. Documento os processos para que elas fiquem facilmente repetitivo. Embora haja um proprietário, várias pessoas devem ser capazes de entender e siga os processos em uma emergência.
1. Treine a equipe para implementar o processo.
1. Use simulações de desastres normal para treinamento e validação do processo.

##<a name="summary"></a>Resumo

Quando hardware ou aplicativos falham dentro do Azure, as técnicas e estratégias para gerenciá-los são diferentes quando ocorre falha em sistemas locais. O motivo principal para isso é que as soluções de nuvem geralmente têm mais dependências na infraestrutura que é distribuída por toda uma região Azure e gerenciada como serviços separados. Você deve lidar com falhas parciais usando técnicas de alta disponibilidade. Para gerenciar mais graves falhas, possivelmente devido a um evento de desastres, use estratégias de recuperação de desastres.

Azure detecta e lida com muitas falhas, mas há muitos tipos de falhas que exigem estratégias específicas do aplicativo. Você ativamente deve preparar e gerenciar as falhas de aplicativos, serviços e dados.

Ao criar seu aplicativo disponibilidade e plano de recuperação de desastres, considere as consequências de negócios de falha do aplicativo. Definindo a processos, políticas e procedimentos para restaurar sistemas críticos após um evento grave leva tempo, planejamento e compromisso. E depois de estabelecer os planos, você não pode parar lá. Você deve analisar regularmente, testar e aprimorar continuamente os planos com base em seu portfólio de aplicativos, necessidades de negócios e as tecnologias disponíveis para você. Azure fornece recursos novos e gera novos desafios para a criação de aplicativos robustos que suportar falhas.

##<a name="additional-resources"></a>Recursos adicionais

[Alta disponibilidade em aplicativos criados no Microsoft Azure](resiliency-high-availability-azure-applications.md)

[Recuperação de dados em aplicativos criados no Microsoft Azure](resiliency-disaster-recovery-azure-applications.md)

[Orientações técnicas resiliência Azure](resiliency-technical-guidance.md)

[Visão geral: Nuvem business banco de dados e continuidade de recuperação de dados com o banco de dados SQL](../sql-database/sql-database-business-continuity.md)

[Alta disponibilidade e recuperação de desastres para o SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)

[Failsafe: Orientação para arquiteturas de nuvem flexível](https://channel9.msdn.com/Series/FailSafe)

[Práticas recomendadas para o design de serviços de grande escala em serviços de nuvem do Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/)

##<a name="next-steps"></a>Próximas etapas

Este artigo faz parte de uma série de artigos voltada para recuperação de dados e alta disponibilidade para aplicativos do Azure. Próximo artigo desta série é [alta disponibilidade em aplicativos criados no Microsoft Azure](resiliency-high-availability-azure-applications.md).
