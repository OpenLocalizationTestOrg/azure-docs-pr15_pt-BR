<properties
   pageTitle="Arquitetura de serviço tecidos | Microsoft Azure"
   description="Estrutura de serviço é uma plataforma de sistemas distribuídos usada para criar aplicativos scalable, confiáveis e facilmente gerenciados para a nuvem. Este artigo mostra a arquitetura da estrutura de serviço."
   services="service-fabric"
   documentationCenter=".net"
   authors="rishirsinha"
   manager="timlt"
   editor="rishirsinha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/09/2016"
   ms.author="rsinha"/>

# <a name="service-fabric-architecture"></a>Arquitetura de estrutura de serviço

Serviço tecidos baseia-se com subsistemas em camadas. Esses subsistemas permitem escrever aplicativos que são:

* Altamente disponível
* Escaláveis
* Gerenciáveis
* Teste

O diagrama a seguir mostra subsistemas principais do serviço tecidos.

![Diagrama de arquitetura de estrutura de serviço](media/service-fabric-architecture/service-fabric-architecture.png)

Em um sistema distribuído, a capacidade de se comunicar com segurança entre nós em um cluster é fundamental. Na base da pilha é o subsistema de transporte, que fornece comunicação segura entre nós. Acima de transporte subsistema reside o subsistema de federação, que clusters nós diferentes em uma única entidade (chamada clusters) para que o serviço tecidos pode detectar falhas, realizar eleição líder e fornecer roteamento consistentes. O subsistema de confiabilidade, posicionado sobre o subsistema de federação, é responsável pela confiabilidade dos serviços de serviço tecidos através de mecanismos como replicação, gerenciamento de recursos e failover. O subsistema de Federação também serve como base para o subsistema de hospedagem e ativação, que gerencia o ciclo de vida de um aplicativo em um único nó. O subsistema de gerenciamento gerencia o ciclo de vida de aplicativos e serviços. O subsistema de capacidade de teste ajuda os desenvolvedores de aplicativos testar seus serviços por meio de falhas simuladas antes e após a implantação de aplicativos e serviços para ambientes de produção. Serviço tecidos fornece a capacidade de resolver locais de serviço através de seu subsistema de comunicação. Os modelos de programação de aplicativo expostos para os desenvolvedores são posicionados sobre esses subsistemas juntamente com o modelo de aplicativo para habilitar ferramentas.

## <a name="transport-subsystem"></a>Subsistema de transporte
O subsistema de transporte implementa um canal de comunicação de datagrama ponto a ponto. Esse canal é usado para comunicação dentro clusters de estrutura de serviço e comunicação entre o cluster de estrutura de serviço e clientes. Ele dá suporte a unidirecional e padrões de comunicação de solicitação-resposta, que fornece a base para implementar difusão e difusão seletiva na camada de Federação. O subsistema de transporte protege a comunicação usando X509 certificados ou segurança do Windows. Esse subsistema é usado internamente pelo serviço tecidos e não é diretamente acessível para os desenvolvedores de programação de aplicativos.

## <a name="federation-subsystem"></a>Subsistema de Federação
Para ponderar sobre um conjunto de nós em um sistema distribuído, você precisa ter uma exibição consistente do sistema. O subsistema de federação usa os primitivos de comunicação fornecidos pelo subsistema de transporte e costura diversos nós em um único cluster unificado que ele pode ponderar sobre. Ele fornece os primitivos de sistemas distribuídos necessários para os outros subsistemas - detecção de falhas, eleição líder e roteamento consistentes. O subsistema de Federação baseia-se na parte superior de tabelas de hash distribuído com um espaço de token de 128 bits. O subsistema cria uma topologia de anel sobre os nós, com cada nó no anel alocado um subconjunto do espaço de token para a propriedade. Para detecção de falhas, a camada usa um mecanismo de leasing com base em coração superando e arbitragem. O subsistema de Federação também garante por meio de junção complexa e partidas protocolos que apenas um único proprietário de um token existe a qualquer momento. Isso forneça eleição líder e garantias de roteamento consistentes.

## <a name="reliability-subsystem"></a>Subsistema de confiabilidade
O subsistema de confiabilidade fornece o mecanismo para tornar o estado de um serviço de serviço tecidos altamente disponível por meio do uso do _Replicator_, _Gerenciador de Failover_e _Balanceador de recurso_.

* O Replicator garante que as alterações de estado na réplica serviço principal serão automaticamente replicadas para réplicas secundárias, manter a consistência entre as réplicas primárias e secundárias em um conjunto de réplica do serviço. O replicator é responsável por gerenciamento de quorum entre as réplicas do conjunto. Ele interage com a unidade de failover para obter a lista de operações para replicar e o agente de reconfiguração fornece-lo com a configuração do conjunto de réplica. Essa configuração indica aquelas que as operações precisam ser replicados. Serviço tecidos fornece um replicador padrão chamado tecidos Replicator, que podem ser usadas pela API de modelo de programação para verificar o estado do serviço altamente disponíveis e confiáveis.
* O Gerenciador de Failover garante que quando nós são adicionados ou removidos do cluster, a carga é redistribuída automaticamente em todos os nós disponíveis. Se um nó no cluster falhar, cluster reconfigurar automaticamente as réplicas de serviço para manter a disponibilidade.
* O Gerenciador de recursos coloca réplicas de serviço em domínio de falha no cluster e garante que todas as unidades de failover estão funcionando. O Gerenciador de recursos também equilibra serviço recursos entre o pool compartilhado subjacente de nós de cluster para alcançar a distribuição de carga uniforme ideal.

## <a name="management-subsystem"></a>Subsistema de gerenciamento
O subsistema de gerenciamento fornece serviço de ponta a ponta e gerenciamento de ciclo de vida do aplicativo. Cmdlets do PowerShell e APIs administrativos permitem que você provisionar, implantar, patch, atualizar e eliminação provisionar aplicativos sem perda de disponibilidade. O subsistema de gerenciamento realiza isso por meio dos serviços a seguintes.

* **Gerenciador de cluster**: esse é o principal serviço que interage com o Failover Gerenciador de confiabilidade para colocar os aplicativos em nós com base em restrições de posicionamento de serviço. O Gerenciador de recursos no subsistema de failover garante que as restrições nunca são desfeitas. O Gerenciador de cluster gerencia o ciclo de vida dos aplicativos de provisionar eliminação provisionar. Ele integra-se com o Gerenciador de integridade para garantir que a disponibilidade dos aplicativos não seja perdida de uma perspectiva de integridade semântico durante as atualizações.
* **Gerenciador de integridade**: esse serviço permite o monitoramento de integridade de aplicativos, serviços e entidades de cluster. Entidades de cluster (como nós, partições de serviço e réplicas) podem relatar informações de integridade, que são agregadas, em seguida, na loja de integridade centralizado. Essas informações de saúde fornecem um instantâneo de integridade geral no momento dos serviços e nós distribuídos em vários nós no cluster, permitindo que você tome as ações corretivas necessárias. Consulta de integridade que APIs permitem que você os eventos de integridade da consulta relatado no subsistema de integridade. A consulta de integridade APIs retornam os dados de integridade bruto armazenados na loja do integridade ou agregado, interpretada dados de integridade de uma entidade de cluster específico.
* **Repositório de imagem**: este serviço oferece armazenamento e distribuição dos binários do aplicativo. Este serviço oferece um repositório simples de arquivo distribuído onde os aplicativos são carregados e baixados do.


## <a name="hosting-subsystem"></a>Subsistema de hospedagem
O Gerenciador de cluster informa o subsistema hospedagem (executando em cada nó), os serviços que ele precisa gerenciar de um nó específico. O subsistema de hospedagem, em seguida, gerencia o ciclo de vida do aplicativo nesse nó. Ele interage com os componentes de integridade e confiabilidade para garantir que as réplicas são colocadas corretamente e estejam íntegros.

## <a name="communication-subsystem"></a>Subsistema de comunicação
Esse subsistema fornece mensagens confiáveis dentro a descoberta de serviço e cluster através do serviço de nomenclatura. O serviço de nomenclatura resolve nomes de serviço para um local no cluster e permite aos usuários gerenciarem propriedades e nomes de serviço. Usando o serviço de nomenclatura, clientes podem com segurança se comunicar com qualquer nó no cluster para resolver um nome de serviço e recuperar metadados do serviço. Usuários do serviço tecidos usando um cliente de nomenclatura simple API, podem desenvolver serviços e clientes capazes de resolver o local de rede atual apesar dinamismo nó ou novamente de dimensionamento do cluster.

## <a name="testability-subsystem"></a>Subsistema de capacidade de teste
Capacidade de teste é um conjunto de ferramentas projetados especificamente para serviços criados em estrutura de serviço de teste. As ferramentas permitem um desenvolvedor induzem falhas significativas e executar cenários de teste para exercício e validar a vários estados e transições que um serviço terão em todo o seu ciclo de vida, todas em forma controlada e segura com facilidade. Capacidade de teste também fornece um mecanismo para executar testes mais longo que podem iteramos por meio de vários possíveis falhas sem perder a disponibilidade. Isso oferece um ambiente de teste em produção.
