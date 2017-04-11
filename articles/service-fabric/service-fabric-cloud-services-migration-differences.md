<properties
   pageTitle="Diferenças entre serviços de nuvem e serviço tecidos | Microsoft Azure"
   description="Uma visão geral conceitual para migrar aplicativos dos serviços de nuvem para tecidos de serviço."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Saiba mais sobre as diferenças entre serviços de nuvem e serviço tecidos antes de migrar aplicativos.
Estrutura de serviço do Microsoft Azure é a plataforma de aplicativo de nuvem de última geração para aplicativos distribuídos altamente flexível e altamente confiáveis. Ele apresenta muitos recursos novos para embalagem, implantar, atualizar e gerenciar aplicativos de nuvem distribuído. 

Este é um guia de Introdução para migrar aplicativos dos serviços de nuvem para tecidos de serviço. Ele se concentra principalmente em arquitetônico e diferenças de design entre os serviços de nuvem e estrutura de serviço.
 
## <a name="applications-and-infrastructure"></a>Aplicativos e infraestrutura

Uma diferença fundamental entre os serviços de nuvem e estrutura do serviço é a relação entre VMs, cargas de trabalho e aplicativos. Veja uma carga de trabalho é definida como o código que você escreve para realizar uma tarefa específica ou fornecer um serviço.
 
 - **Serviços de nuvem é sobre como implantar aplicativos como VMs.** O código que você escreve está associado a uma instância de máquina virtual, como um Web ou uma função de trabalho. Para implantar uma carga de trabalho nos serviços de nuvem é implantar uma ou mais instâncias de máquina virtual que executam a carga de trabalho. Não há nenhuma separação de aplicativos e VMs e então há uma definição formal de um aplicativo. Um aplicativo pode ser considerado como um conjunto de instâncias de Web ou função de trabalho dentro de uma implantação de serviços de nuvem ou uma implantação de serviços de nuvem inteira. Neste exemplo, um aplicativo é mostrado como um conjunto de instâncias de função.
 
![Topologia e aplicativos de serviços de nuvem][1]

 - **Estrutura de serviço está implantando aplicativos VMs existentes ou máquinas em execução tecidos de serviço no Windows ou Linux.** Os serviços que você escreve são completamente desacoplados da infraestrutura subjacente, que é abstraída pela plataforma de aplicativo de serviço tecidos, para que um aplicativo pode ser implantado em vários ambientes. Uma carga de trabalho em estrutura de serviço é chamada de "serviço", e um ou mais serviços são agrupados em um aplicativo definido anteriormente que é executado na plataforma de aplicativo de serviço tecidos. Vários aplicativos podem ser implantados com um único cluster de estrutura de serviço.
 
![Aplicativos de serviço tecidos e topologia][2]
 
Estrutura de serviço em si é uma camada de plataforma de aplicativo que é executado no Windows ou Linux, enquanto os serviços de nuvem é um sistema para implantar gerenciados Azure VMs com cargas de trabalho anexadas.
O modelo de aplicativo de serviço tecidos tem várias vantagens:

 - Horários de implantação rápida. Criando instâncias de máquina virtual pode ser demorada. Em estrutura de serviço, VMs são implantadas apenas uma vez para formar um cluster que hospeda a plataforma de aplicativo de serviço tecidos. Desse ponto em diante, pacotes de aplicativo podem ser implantados ao cluster rapidamente.
 - Alta densidade de hospedagem. Em serviços de nuvem, uma função de trabalho máquina virtual hospeda uma carga de trabalho. Em estrutura de serviço, aplicativos são separados do VMs que execução-los, significando que você pode implantar um grande número de aplicativos para um pequeno número de VMs, que pode reduzir o custo total para implantações maiores.
 - Estrutura do serviço de plataforma pode ser executados em qualquer lugar que tem máquinas Windows Server ou Linux, seja ele Azure ou local. A plataforma fornece uma camada de abstração sobre a infraestrutura subjacente para que seu aplicativo pode ser executados em diferentes ambientes. 
 - Gerenciamento de aplicativo distribuído. Estrutura de serviço é uma plataforma que não apenas hospeda aplicativos distribuídos, mas também ajuda a gerenciar o ciclo de vida independentemente da máquina virtual hospedagem ou ciclo de vida de máquina.

## <a name="application-architecture"></a>Arquitetura do aplicativo

A arquitetura de um aplicativo de serviços de nuvem geralmente inclui várias dependências de serviço externo, como o barramento de serviço, tabela do Azure e armazenamento de Blob, SQL, relacionada e outras pessoas para gerenciar o estado e dados de um aplicativo e a comunicação entre funções da Web e trabalhador em uma implantação de serviços de nuvem. Um exemplo de um aplicativo completo de serviços de nuvem pode ter esta aparência:  

![Arquitetura de serviços de nuvem][9]

Aplicativos de serviço tecidos também podem optar por usar os mesmos serviços externos em um aplicativo completo. Usando esse exemplo arquitetura de serviços de nuvem, o caminho de migração mais simples de serviços de nuvem para serviço tecidos é substituir somente a implantação de serviços de nuvem com um aplicativo de serviço tecidos, mantendo a arquitetura geral igual. As funções da Web e trabalhador podem ser transferidas para serviços de estado de serviço tecidos com alterações mínimas de códigos.

![Arquitetura de serviço tecidos após a migração simple][10]

Neste estágio, o sistema deve continuar funciona da mesma forma antes. Aproveitando os recursos com informações de estado do serviço tecidos, armazena estado externo pode ser internalizado como com informações de estado de serviços onde aplicável. Isso é mais atrativo que uma migração simples de funções da Web e trabalhador aos serviços de estado de serviço tecidos, pois ela requer escrevendo serviços personalizados que fornecem funcionalidade equivalente ao seu aplicativo, como os serviços externos faziam anteriormente. As vantagens de fazê-lo: 

 - Removendo dependências externas 
 - Unificação a implantação, o gerenciamento e a atualização de modelos. 
 
Uma arquitetura de exemplo resultante de internalizing esses serviços pode ter esta aparência:

![Arquitetura de serviço tecidos após a migração completa][11]

## <a name="communication-and-workflow"></a>Comunicação e fluxo de trabalho

A maioria dos aplicativos de serviço de nuvem consistem em mais de um nível. Da mesma forma, um aplicativo de serviço tecidos consiste em mais de um serviço (normalmente muitos serviços). Dois modelos de comunicação comuns são comunicação direta e por meio de um armazenamento durável externo.

### <a name="direct-communication"></a>Comunicação direta

Com comunicação direta, os níveis podem se comunicar diretamente por meio de ponto de extremidade exposto por cada nível. Em ambientes sem estado como os serviços de nuvem, este significa selecionando uma instância de uma função de máquina virtual, ou aleatoriamente ou alternada carga de saldo e conectar seu ponto de extremidade diretamente.

![Serviços de nuvem direcionar comunicação][5]

 Comunicação direta é um modelo de comunicação comum em estrutura de serviço. A principal diferença entre tecidos de serviço e os serviços de nuvem é que em serviços de nuvem que você se conectar a uma máquina virtual, enquanto em estrutura de serviço, você se conectar a um serviço. Isso é uma distinção importante por alguns motivos:

 - Serviços no serviço tecidos não são vinculados às VMs que hospedam-los; serviços pode mover-se no cluster e, na verdade, espera mover-se por vários motivos: recurso balanceamento, failover, atualizações de aplicativo e infraestrutura e restrições de posicionamento ou carregar. Isso significa que o endereço de uma instância serviço pode mudar a qualquer momento. 
 - Uma máquina virtual em estrutura de serviço pode hospedar vários serviços, cada uma com pontos de extremidade exclusivos.

Serviço tecidos fornece um mecanismo de descoberta de serviço, chamado de serviço de nomenclatura, que pode ser usado para resolver endereços de ponto de extremidade dos serviços. 

![Comunicação direta tecidos de serviço][6]

### <a name="queues"></a>Filas

Um mecanismo de comunicação comum entre os níveis em ambientes sem estado como os serviços de nuvem é usar uma fila do armazenamento externo para armazenar permanentemente tarefas de trabalho de um nível para outro. Um cenário comum é uma camada de web que envia trabalhos a uma fila do Azure ou barramento de serviço onde instâncias de função de trabalho podem retirar e processar os trabalhos.

![Comunicação de fila de serviços de nuvem][7]

O mesmo modelo de comunicação pode ser usado em estrutura de serviço. Isso pode ser útil ao migrar um aplicativo de serviços de nuvem existente para tecidos de serviço. 

![Comunicação direta tecidos de serviço][8]
 
## <a name="next-steps"></a>Próximas etapas

O caminho de migração mais simples de serviços de nuvem para serviço tecidos é substituir somente a implantação de serviços de nuvem com um aplicativo de serviço tecidos, mantendo a arquitetura geral do seu aplicativo aproximadamente iguais. O artigo a seguir fornece um guia para ajudar a converter uma Web ou uma função de trabalho em um serviço de estado de serviço tecidos.

 - [Migração simples: converter uma Web ou uma função de trabalho em um serviço de estado de estrutura de serviço](./service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
