<properties
   pageTitle="Banco de dados do SQL Azure Azure estudo de caso - Daxko/CSI | Microsoft Azure"
   description="Saiba como Daxko/CSI usa o banco de dados SQL para acelerar seu ciclo de desenvolvimento e aprimorar seus serviços de cliente e desempenho"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/08/2016"
   ms.author="carlrab"/>
   
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI usado Azure para acelerar seu ciclo de desenvolvimento e aprimorar seus serviços de cliente e desempenho

![Logotipo de Daxko/CSI](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Software de Daxko/CSI enfrentados um desafio: sua base de clientes de treino e recriação centers aumentou rapidamente, graças ao sucesso da sua solução de software de empresa abrangente, mas acompanhar as necessidades de-infraestrutura do cliente crescente base foi teste da empresa a equipe de TI. A empresa foi cada vez mais restrito por crescentes sobrecarga de operações, particularmente para gerenciar seus bancos de dados crescentes. Pior, essa sobrecarga de operações foi recortando em recursos de desenvolvimento para novas iniciativas, como novos recursos de mobilidade para software da empresa.

De acordo com a David Molina, diretor de desenvolvimento de produto em Daxko/CSI, o Azure fornecido CSI Software com o modelo de (PaaS) de plataforma como serviço necessárias para simplificar o gerenciamento de banco de dados, aumentar a escalabilidade e liberar recursos para focalizar software em vez de operações. "O banco de dados do SQL azure foi uma ótima opção para nós. Não precisar se preocupar em manter um SQL Server, um cluster de failover e todas as necessidades de infraestrutura do foi ideal para nós."

Desde migrando para o Azure, Software CSI precisa de uma equipe de operações de apenas dois para gerenciar mais de 600 bancos de dados do cliente. A empresa usa pools Elástico Azure SQL Database mover bancos de dados do cliente com base no tamanho e precisa.

Molina persistir, "nossos clientes sentiam a alteração imediatamente. Antes de pools Elástico, ocasionalmente tinham tempos limite e outros problemas durante períodos de intermitente. Com o Azure pools Elástico, eles podem intermitente conforme necessário e usar o software sem problemas."

Além de melhorar o desempenho de clientes, Azure elástica banco de dados pools liberados de recursos de Software CSI concentrar-se em desenvolvimento de novos serviços e recursos, em vez de lidar com operações e gerenciamento. Esses recursos de TI ajudaram CSI Software melhorar sua oferta, SpectrumNG, para ajudar a envolva membros Academia, melhorar a eficiência da equipe, um software corporativo e dar acesso móvel de equipe e membros para tarefas interativas e notificações em tempo real.

Azure também ajudou CSI Software acelerar e aprimorar o desenvolvimento e o ciclo de controle de qualidade (p & r) ativando opções de automação. Com implementação Azure da empresa, os gerentes de compilação podem empacotar os componentes com o clique de um botão. Como Molina descreve, "como parte do ciclo de lançamento, p & r agora serão capaz de implantar em um ambiente de teste do Azure, que simule estreitamente nossa pilha de produção. Podemos implantar compilações imediatamente ao nosso ambiente de desenvolvimento para verificar alterações. Que é uma grande vantagem para nós, porque não temos paridade para testes antes que."

## <a name="offloading-to-the-cloud"></a>Transferindo na nuvem

Antes de passar para a nuvem, Software CSI tinha criadas com êxito seu próprio infraestrutura vários locatários em um data center local em Houston. Como a empresa expandida, ele enfrentando problemas crescentes crescentes de compra, provisionamento e mantendo todo o hardware e software necessários para dar suporte a seus clientes. IT pessoal para lidar com operações se tornou afunilamento outro que resultaram uma diminuição em novos recursos de provisionamento e implantar novos serviços aos clientes.

Software CSI procurou em Opções de nuvem para eliminar essa sobrecarga, para que ela pode se concentrar em seu código, em vez de suas operações. A empresa descobriu que muitos dos provedores de serviços de nuvem superior oferecem somente as soluções de infraestrutura como serviço (IaaS) que ainda exigem uma grande equipe de TI para gerenciar a pilha IaaS. No final, Software CSI determinado que a solução do Azure PaaS os mais adequados para suas necessidades. Explica Molina, "Azure obtém o software de hardware e sistema fora do caminho, portanto, podemos pode se concentrar nossas ofertas de software, reduzindo nossa sobrecarga de TI."

## <a name="making-the-transition-to-azure"></a>Fazer a transição para o Azure

Depois de selecionar Azure para sua solução de PaaS, Software CSI começou migrando sua infraestrutura de back-end e bancos de dados para a nuvem. Antes de Azure, clientes SpectrumNG necessário para instalar um aplicativo cliente que comunicadas com um serviço Windows Communication Foundation (WCF) no back-end. De acordo com a Molina, "Embora alguns clientes hospedado tudo em suas próprias dos data centers, criamos check-out do produto seja vários locatários. Podemos tudo em um data center em Houston hospedado usando o SQL Server como o armazenamento de dados.

"Nosso produto oferecendo também incluído um membro voltados ao portal da web (escrito em ASP.net), que foi projetado para ser rotulada em branco para fazer a correspondência de presença na web do cliente e uma API SOAP para dar suporte as páginas online e qualquer integração de terceiros."

A migração para a nuvem não levou tempo para a arquitetura. De acordo com a Molina, "A maioria do esforço resolvida modificando a maneira que podemos ler informações do arquivo de configuração, uma modificação centralizado de cadeia de conexão e automação da embalagem, carregando e implantação do nossos versões."

Para desenvolver a automação de compilação, engenheiros CSI Software usado PowerShell do Azure e APIs REST para criar pacotes e carregue-os para um ambiente de teste para a versão cada noite.
A transição geral para uma implantação Azure baseado em nuvem deu tranquilamente e rapidamente para a equipe de TI do Software CSI. Explica Molina, "em todos, tivemos um ambiente beta na nuvem dentro de três a quatro semanas de fazer no projeto. Isso foi uma surpresa win para nós."

Depois de configurar e testar o ambiente, Software CSI começou migrando clientes. Para clientes que já utilizam CSI Software de hospedagem, a transição foi praticamente perfeita. Para clientes migrando de uma implantação local, a migração para a nuvem levou algum tempo adicional, mas foi ainda principalmente sem complicações para clientes e Software CSI.

Para da novos clientes, Software CSI equipe de TI usa o seguinte processo para integrado-los no Azure:

1.  Azure scripts do PowerShell são usadas para girar um novo banco de dados para o cliente; todos os clientes começam em um nível premium para garantir suficiente produtividade inicial para a transição.
2.  Quando possível, o Software de CSI usa o Assistente de migração do SQL Azure para mover os dados existentes para uma instância do Azure SQL Database.
3.  Finalmente, Microsoft SSIS SQL Server Integration Services () são usadas para reconciliar discrepâncias dos dados ou para executar qualquer limpeza de dados conforme necessário.

Hoje, cerca de 99% dos clientes do Software CSI são hospedados no Azure, nos quatro data centers regionais (América do Norte Central, Sul Central, Leste e Oeste). Por ter dos data centers na área geográfica de cada cliente, latência é mantida em um mínimo.

## <a name="azure-elastic-database-pools-free-up-it-resources"></a>Pools de banco de dados elástica Azure liberar recursos de TI

Vários recursos do Azure ajudaram CSI Software shift sejam infraestrutura e operações com foco em para sendo recurso e desenvolvimento com foco. Talvez o maior benefício foi de pools elástica banco de dados.

Atualmente, o Software de CSI fornece 550 sobre bancos de dados para os clientes. Antes de pools Elástico, era difícil gerenciar bancos de dados que muitos dentro de uma estrutura de camadas. Gerentes de operações tinham atribuir níveis de desempenho com base nas necessidades dos clientes, que requerido significativo recursos de TI sobrecarga intermitente. Com pools de banco de dados elástica, gerentes podem atribuir locatários um premium ou o pool padrão, conforme apropriado e, em seguida, mover clientes com base no tamanho e precisa. Clientes sentiam os efeitos dos pools de banco de dados elástica quase imediatamente; antes de pools Elástico, os clientes tinham tempos limite e outros problemas durante períodos de uso intermitente, mas com pools Elástico, os clientes podem enfrentar picos de atividade conforme necessário e eles podem continuar a usar SpectrumNG sem problemas.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>Localização geográfica-replicação do Azure Active acelera de relatório

Vários clientes de Software CSI também são as vantagens de Azure Active geográfica replicação. Com o Active geográfica replicação, até quatro bancos de dados secundários legíveis pode ser configurado nas regiões a data center iguais ou diferentes. Software CSI utiliza de ativo geográfica replicação de duas maneiras: primeiro, os bancos de dados secundários estão disponíveis no caso de falta de data center ou a impossibilidade de se conectar ao banco de dados principal; e segundo, os bancos de dados secundários são legíveis e podem ser usados para transferir cargas de trabalho somente leitura como trabalhos de relatório. Alguns clientes de Software CSI usar esse benefício para acelerar relatórios de fluxos de trabalho.

## <a name="csi-software-application-logic-and-architecture"></a>Arquitetura e lógica de aplicativo de Software de CSI

SpectrumNG usa funções da web. Porque o aplicativo está vários locatário, um serviço WCF é usado para lidar com a solicitação de conexão inicial de clientes. Como Molina informa, "a solicitação identifica cada cliente, o que, em seguida, nos permite construir uma cadeia de conexão-out para seus bancos de dados para fazer tudo o que precisamos fazer."

Para a camada da web do seu serviço, Software CSI tira proveito do Azure o dimensionamento automático, com base em dia e hora. Recursos disponíveis são automaticamente aumentados para acomodar a maior uso durante o horário comercial, de acordo com o fuso horário do cada comerciais. Recursos também são definidos para dimensionar para baixo nos fins de semana, quando são menores necessidades dos clientes.

     
![Arquitetura de Daxko/CSI](./media/sql-database-implementation-daxko/figure1.png)

Figura 1. Uma função de trabalho de serviços de nuvem desenha dados estruturados do Azure SQL Database e semiestruturados dados do armazenamento de tabela. Usuários de SpectrumNG interagem com que os serviços de dados por meio de uma nuvem função web.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>Usando aplicativos web e uma camada de plano de web para aplicativos móveis

Usando o banco de dados do SQL Azure liberar recursos para Software CSI para habilitar novas iniciativas, incluindo uma plataforma móvel completa com base em uma API personalizada hospedada no Azure web apps. A plataforma permite que os membros de Academia e funcionários usar dispositivos móveis para verificar agendas, classes de livro e receber mensagens.

A plataforma usa arquitetura orientada a serviços (SOA) para fazer um único componente — como um sistema de ponto de venda (POS) ou um sistema de venda — movê-la dinamicamente para outro plano do web e, em seguida, gira um serviço para dar suporte a esse componente, deixando tudo no plano do web original. Essa capacidade fornece flexibilidade enorme CSI Software e ajuda a manter os custos baixos.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Azure permite foco de desenvolvedores de Software CSI em aplicativos e serviços

Banco de dados do SQL Azure não é apenas um benefício para clientes de SpectrumNG, que aproveite o serviço de forma rápido e confiável, também é uma grande vantagem CSI do Software de equipe de TI e desenvolvedores. Diminuindo operações no Azure na nuvem, o Software de CSI reduzido sua sobrecarga para recursos e infraestrutura, muito acelerada seus ciclos de desenvolvimento e não está mais necessidades aos bancos de dados de micromanage para otimizar o desempenho para seus locatários.

## <a name="more-information"></a>Mais informações

- Para saber mais sobre os pools do Azure elástica banco de dados, consulte [pools de elástica banco de dados](sql-database-elastic-pool.md).

- Para saber mais sobre as ferramentas de banco de dados e dimensionamento Elástico, consulte [dimensionamento Elástico e ferramentas de banco de dados elástica](sql-database-elastic-scale-get-started.md).

- Para saber mais sobre como migrar um banco de dados do SQL Server, consulte o [Assistente de migração do SQL Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md).

- Para saber mais sobre Active geográfica replicação, consulte [Replicação de localização geográfica ativa](sql-database-geo-replication-overview.md).

- Para saber mais sobre funções da Web e funções de trabalho, consulte [funções de trabalho](../fundamentals-introduction-to-azure.md#compute). 

- Para saber mais sobre o barramento de serviço do Azure, consulte [Barramento de serviço do Azure](https://azure.microsoft.com/services/service-bus/).

- Para saber mais sobre dimensionar automaticamente, consulte [escala com serviços de nuvem](../cloud-services/cloud-services-how-to-scale.md).
