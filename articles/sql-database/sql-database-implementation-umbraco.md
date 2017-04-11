<properties
   pageTitle="Banco de dados do SQL Azure Azure estudo de caso - Umbraco | Microsoft Azure"
   description="Saiba mais sobre como o Umbraco usa o banco de dados SQL para provisionar rapidamente e serviços de escala para milhares de locatários na nuvem"
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
   ms.date="09/22/2016"
   ms.author="carlrab"/>

# <a name="umbraco-uses-azure-sql-database-to-quickly-provision-and-scale-services-for-thousands-of-tenants-in-the-cloud"></a>Umbraco usa Azure SQL Database para serviços rapidamente provisionar e escala para milhares de locatários na nuvem

![Logotipo de Umbraco](./media/sql-database-implementation-umbraco/umbracologo.png)

Umbraco é um popular sistema código-fonte aberto gerenciamento de conteúdo (CMS) que pode executar qualquer coisa sites pequenos de campanha ou folheto para aplicativos complexos para empresas da Fortune 500 e sites de mídia global. 

> "Temos muito uma grande comunidade de desenvolvedores que usam o sistema, com mais de 100.000 desenvolvedores de nossos fóruns e mais de 350.000 sites ao vivo, executando Umbraco."

> — Luís Christensen, líder técnico, Umbraco

> [AZURE.VIDEO azure-sql-database-case-study-umbraco]

Para simplificar implantações de cliente, Umbraco adicionado Umbraco-como um serviço (UaaS): uma oferta de software-como um serviço (SaaS) que elimina a necessidade de implantações locais, oferece dimensionamento internos e remove gerenciamento sobrecarga habilitando desenvolvedores concentrar-se em gerenciamento de inovações em vez de solução de produtos. Umbraco é capaz de fornecer todos esses benefícios por confiar no flexível plataforma como serviço (PaaS) modelo de oferecidas pelo Microsoft Azure.

UaaS permite aos clientes SaaS usar recursos de Umbraco CMS que estavam anteriormente fora seu alcance. Esses clientes são provisionados com um ambiente de CMS de trabalho que inclui um banco de dados de produção. Os clientes podem adicionar até dois bancos de dados adicionais para ambientes de desenvolvimento e teste, dependendo das suas necessidades. Quando um novo ambiente é solicitado, um processo automatizado atribui cliente um banco de dados automaticamente. O novo banco de dados está pronto em segundos, porque o banco de dados já foi provisionado previamente pela Umbraco de um pool de Elástico Azure dos bancos de dados disponíveis (consulte a Figura 1).


![Ciclo de vida de provisionamento do Umbraco](./media/sql-database-implementation-umbraco/figure1.png)

Figura 1. Ciclo de vida de provisionamento do Umbraco como um serviço (UaaS)
 
##<a name="azure-elastic-pools-and-automation-simplify-deployments"></a>Azure pools Elástico e automação simplificam as implantações

Com o banco de dados do Azure SQL e outros serviços do Azure, os clientes de Umbraco auto podem provisionar a seus ambientes e Umbraco facilmente pode monitorar e gerenciar bancos de dados como parte de um fluxo de trabalho intuitivo:

1.  Provisionar

    Umbraco mantém a capacidade de 200 previamente provisionados bancos de dados disponíveis de pools Elástico. Quando um novo cliente se inscreve para UaaS, Umbraco fornece ao cliente com um novo ambiente de CMS praticamente em tempo real designando-lhes um banco de dados do pool de disponibilidade.

    Quando um pool de disponibilidade atinge seu limite, um novo pool Elástico é criado e novos bancos de dados são previamente provisionados para serem atribuídos a clientes conforme necessário.

    Implementação é totalmente automatizada usando bibliotecas de gerenciamento do c# e filas de barramento de serviço do Azure.

2.  Utilizar

    Os clientes usar um a três ambientes (produção, teste e/ou desenvolvimento), cada com seu próprio banco de dados. Bancos de dados do cliente estão em pools de banco de dados elástica, que permite Umbraco fornecer eficiente de dimensionamento sem ter de provisionamento excessivo.

    ![Visão geral do projeto Umbraco](./media/sql-database-implementation-umbraco/figure2.png)

    ![Umbraco detalhe do projeto](./media/sql-database-implementation-umbraco/figure3.png)

    Figura 2. Site de cliente Umbraco-como um serviço (UaaS), mostrando detalhes e visão geral do projeto

    Banco de dados do SQL Azure usa unidades de transação de banco de dados (DTUs) para representar a energia relativa necessária para as transações de banco de dados do mundo real. Para clientes de UaaS, bancos de dados geralmente operam a 10 DTUs, mas cada tem a elasticidade de dimensionar sob demanda. Isso significa que UaaS pode garantir que os clientes sempre recursos necessários, mesmo durante as horas de pico. Por exemplo, durante um evento de esportes domingo recente, um UaaS experiente banco de dados cliente picos até 100 DTUs para a duração do jogo. Azure pools Elástico feitas possíveis para Umbraco dar suporte a esse alta demanda sem degradação de desempenho.

3.  Monitor

    Monitores Umbraco atividade usando painéis no portal do Azure, juntamente com os alertas de email personalizado de banco de dados.

4.  Recuperação de dados

    Azure oferece duas opções de recuperação (DR): replicação geográfica ativo e a restauração de localização geográfica. A opção de DR que uma empresa deve selecionar depende de seus [objetivos de continuidade de negócios](sql-database-business-continuity.md).

    Replicação de localização geográfica ativa fornece o nível mais rápido de resposta em caso de tempo de inatividade. Usando replicação de localização geográfica ativa, você pode criar até quatro secundários legíveis em servidores em diferentes regiões e, em seguida, você pode iniciar failover para qualquer um dos secundários em caso de uma falha.

    Umbraco não exige replicação geográfica, mas ele tirar proveito do Azure geográfica restauração para ajudar a garantir o tempo de inatividade mínimo em caso de uma interrupção. Localização geográfica restauração depende de backups de banco de dados no armazenamento do Azure redundantes de localização geográfica. Que permite aos usuários restaurar a partir de uma cópia de backup quando há uma interrupção na região primária.

5.  Provisionar eliminação

    Quando um ambiente de projeto é excluído, qualquer bancos de dados associados (desenvolvimento, teste ou ao vivo) serão removidos durante a limpeza de fila barramento de serviço do Azure. Esse processo automatizado restaura os bancos de dados não utilizados ao pool de elástica de disponibilidade de banco de dados do Umbraco, disponibilizá-las para provisionamento futuro mantendo utilização máxima.

##<a name="elastic-pools-allow-uaas-to-scale-with-ease"></a>Pools Elástico permitem UaaS dimensionar com facilidade

Aproveitando os pools do banco de dados elástica Azure, Umbraco pode otimizar desempenho para seus clientes sem precisar sobre ou provisionar insuficiente. Umbraco atualmente tem aproximadamente 3.000 bancos de dados em 19 pools elástica banco de dados, com a capacidade de redimensionar com facilidade conforme necessário para acomodar qualquer um dos seus 325.000 clientes existentes ou novos clientes que estão prontos para implantar um CMS na nuvem.

Na verdade, de acordo com a Morten Christensen, técnico-chefe da Umbraco, "UaaS está agora enfrentando crescimento de 30 novos clientes por dia. Nossos clientes estão muito satisfeitos em com a conveniência de poder provisionar novos projetos em segundos, instantaneamente publicar atualizações em seus sites ao vivo de um ambiente de desenvolvimento usando 'um único clique implantação' e faça alterações tão rapidamente se eles encontraram erros. "

Se um cliente não exige um segundo e/ou ambiente terceiro mais, ele poderá simplesmente remova esses ambientes. Que libera recursos que podem ser usados para outros clientes como parte do pool Umbraco elástica de disponibilidade de banco de dados.

![Arquitetura de implantação Umbraco](./media/sql-database-implementation-umbraco/figure4.png)

Figura 3. Arquitetura de implantação de UaaS no Microsoft Azure

##<a name="the-path-from-datacenter-to-cloud"></a>O caminho do data center para a nuvem

Quando os desenvolvedores Umbraco inicialmente feita a decisão de mudar para um modelo de SaaS, eles sabiam que eles teriam um modo econômico e scalable construir o serviço.

> "Pools de banco de dados elástica são um ajuste perfeito para nossa oferta SaaS porque podemos pode discar capacidade para cima e para baixo conforme necessário. Provisionamento é fácil, e a nossa configuração, podemos pode manter utilização máxima."

> — Luís Christensen, líder técnico, Umbraco

"Queremos perder nosso tempo na solução de problemas de nossos clientes, não Gerenciando infraestrutura. Queremos facilite para nossos clientes a obter o valor máximo,"diz Niels Hartvig, fundador da Umbraco. "Podemos inicialmente considerado hospedando os servidores de nós, mas planejamento da capacidade seria um pesadelo." Coincidentemente, Umbraco não utilizam os administradores de banco de dados, que enfatiza uma proposta de valor da chave para usar UaaS.

Um objetivo importante para os desenvolvedores Umbraco era oferece uma maneira para clientes UaaS provisionar ambientes rapidamente e sem limitações de capacidade. Mas fornecer um serviço hospedado dedicado em data centers Umbraco exigiria muita capacidade excedente para lidar com picos de processamento. Que seria tem como objetivo adicionando infraestrutura de computação considerável que faria tiver sido subutilizada regularmente.

Além disso, a equipe de desenvolvimento Umbraco desejava uma solução que permite que reutilizar o máximo do seu código existente como possíveis. Como desenvolvedor Umbraco Mikkel Madsen informa, "foi satisfeitos com as ferramentas de desenvolvimento do Microsoft que já conhecidos, como o Microsoft SQL Server, o banco de dados do Microsoft Azure SQL, ASP.net e serviços de informações da Internet (IIS). Antes de investir em um IaaS ou uma PaaS nuvem solução, queremos certificar-se de que ele seria nosso Microsoft e ferramentas de suporte plataformas, para que não precisamos fazer alterações de massa em nosso código base. "

Para atender a todos os seus critérios, Umbraco estava procurando um parceiro de nuvem com as seguintes qualificações:

-   Capacidade e confiabilidade suficientes
-   Suporte para ferramentas de desenvolvimento da Microsoft, para que os engenheiros Umbraco não seriam forçados reinventar completamente seu ambiente de desenvolvimento
-   Presença em todos os mercados geográficos na qual UaaS concorrente (necessidade de negócios para garantir que eles podem acessar seus dados rapidamente e que seus dados são armazenados em um local que atende às suas necessidades regulamentares regionais)

##<a name="why-umbraco-chose-azure-for-uaas"></a>Por que o Umbraco escolheu Azure para UaaS

De acordo com Morten Christensen "Após considerar todos os nossas opções, selecionamos Azure porque ele atendido todos os nossos critérios, da capacidade de gerenciamento e escalabilidade para familiaridade e economia. Podemos configurar os ambientes em VMs do Azure e cada ambiente tem sua própria instância do Azure SQL Database, com todas as instâncias em pools elástica banco de dados. Separando bancos de dados entre ambientes ao vivo, teste e desenvolvimento, podemos oferecer nossos clientes isolamento de desempenho robusto corresponde a escala — uma enorme win. "

Luís persistir, "antes, tivemos provisionar servidores para bancos de dados da web manualmente. Agora, não precisamos pensar sobre isso. Tudo é automatizado — de provisionamento para limpeza. "

Luís também está satisfeito com os recursos de dimensionamento fornecidos pelo Azure. "Pools de banco de dados elástica são um ajuste perfeito para nossa oferta SaaS porque podemos pode discar capacidade para cima e para baixo conforme necessário. Provisionamento é fácil, e a nossa configuração, podemos pode manter utilização máxima." Estados Luís, "a simplicidade do pools elástica, juntamente com a garantia de serviço baseado em camada DTUs, nos dá a potência provisionar novos grupos de recursos sob demanda. Recentemente, um dos nossos clientes maiores pico para 100 DTUs em seu ambiente. Usando o Azure, nossos pools Elástico fornecido bancos de dados do cliente com os recursos que eles necessários em tempo real sem precisar prever os requisitos de DTU. Resumindo, nossos clientes a obter o tempo ativar que esperam e podemos atender nossos contratos de nível de serviço de desempenho."

Mikkel Madsen resume: "Podemos já adotaram o algoritmo Azure poderoso que conecta um cenário de SaaS comum (integração novos clientes em tempo real em escala) nosso padrão de aplicativo (previamente provisionar bancos de dados, os dois desenvolvimento e live) sobre a tecnologia subjacente (usando filas Azure barramento de serviço em conjunto com o banco de dados do SQL Azure)."

##<a name="with-azure-uaas-is-exceeding-customer-expectations"></a>Com o Azure, UaaS está ultrapassando às expectativas dos clientes

Desde escolher Azure como seu parceiro de nuvem, Umbraco foi capaz de fornecer aos clientes UaaS otimizar o desempenho de gerenciamento de conteúdo, sem o investimento de recursos de TI necessário de uma solução hospedada automaticamente. Como Luís diz, "podemos adora a conveniência de desenvolvedor e escalabilidade que Azure nos dá e nossos clientes são entusiasmados com os recursos e confiabilidade. Em geral, ele foi um ótimo win para nós!"
 
## <a name="more-information"></a>Mais informações

- Para saber mais sobre os pools do Azure elástica banco de dados, consulte [pools de elástica banco de dados](sql-database-elastic-pool.md).

- Para saber mais sobre o barramento de serviço do Azure, consulte [Barramento de serviço do Azure](https://azure.microsoft.com/services/service-bus/).

- Para saber mais sobre funções da Web e funções de trabalho, consulte [funções de trabalho](../fundamentals-introduction-to-azure.md#compute). 

- Para saber mais sobre redes virtuais, consulte [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/).    

- Para saber mais sobre backup e recuperação, consulte [continuidade de negócios](sql-database-business-continuity.md).  

- Para saber mais sobre o monitoring ppols, consulte [monitoramento pools](sql-database-elastic-pool-manage-portal.md). 

- Para saber mais sobre Umbraco como um serviço, consulte [Umbraco](https://umbraco.com/cloud).

