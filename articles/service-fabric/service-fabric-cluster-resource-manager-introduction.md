<properties
   pageTitle="Apresentando o Gerenciador de recursos de Cluster do serviço tecidos | Microsoft Azure"
   description="Uma introdução para o Gerenciador de recursos de Cluster tecidos do serviço."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Apresentando o Gerenciador de recursos de cluster tecidos de serviço
Gerenciar normalmente sistemas de TI ou um conjunto de serviços como objetivo obtendo algumas máquinas físicas ou virtuais dedicadas aos serviços específicos ou sistemas. Muitos serviços principais foram divididos em um nível de "web" e um nível de "dados" ou "armazenamento", talvez com alguns outros componentes especializados como cache. Outros tipos de aplicativos teria uma camada de mensagens onde solicitações fluíram e reduzir, conectada a um nível de trabalho para qualquer análise ou transformação necessária como parte da mensagem. Cada tipo de carga de trabalho tem um máquinas específicas dedicada a ele: o banco de dados tem algumas máquinas dedicadas a ele, os servidores web alguns. Se um determinado tipo de carga de trabalho causados as máquinas estava em para executar muito quente, então você adicionou mais máquinas com esse tipo de carga de trabalho configurado para executar nele ou substituído algumas das máquinas com máquinas maiores. Fácil. Se uma máquina falhou, essa parte do aplicativo geral executado em menor capacidade até que a máquina pôde ser restaurada. Ainda razoavelmente fácil (se não é necessariamente divertida).

Agora, no entanto, vamos supor que você encontrou necessário dimensionar e adotaram o contêineres e/ou dessa iniciação de microservice. Inesperadamente você encontrará com dezenas, centenas ou até mesmo milhares de máquinas, dezenas de diferentes tipos de serviços, talvez centenas de diferentes instâncias desses serviços, cada uma com um ou mais ocorrências ou réplicas para alta disponibilidade (HA).

Gerenciar inesperadamente seu ambiente não é tão simple como gerenciar algumas máquinas dedicadas aos tipos de único das cargas de trabalho. Os servidores são virtuais e não tiver mais nomes (você *tiver* mudado pensamentos de [animais para cattle](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) afinal). Configuração é menor sobre as máquinas e mais informações sobre os serviços. Hardware dedicado é uma coisa do passado e serviços tornaram pequenos sistemas distribuídos, abrangendo várias partes menores de hardware de mercadoria.

Como resultado de seu aplicativo antigo monolítico, hierárquico invade da separada serviços em execução em hardware de mercadoria, agora você tem muitos mais combinações de lidar com. Quem decide quais tipos de cargas de trabalho podem ser executados em quais hardware, ou quantos? Quais cargas de trabalho funcionam bem no mesmo hardware e que estão em conflito? Quando uma máquina sai... o que foi mesmo em execução lá? Quem é responsável pelo lembrando-se de que carga de trabalho é iniciado executando novamente? Você aguardar máquina (virtual?) voltar ou suas cargas de trabalho automaticamente alternar para outros computadores e manter em execução? É a intervenção necessária? O que são atualizações nesse tipo de ambiente?

Como os desenvolvedores e operadores vivem nesse tipo do mundo, vamos abordar a precisar de ajuda para gerenciar essa complexidade e você receberá sentido um binge contratação e tentando papel sobre a complexidade com pessoas não é a resposta certa.

O que fazer?

## <a name="introducing-orchestrators"></a>Introdução ao orchestrators
Um "Orchestrator" é o termo geral para uma parte do software que ajuda os administradores a gerenciar esses tipos de ambientes. Orchestrators são os componentes que levar em solicitações como "Eu gostaria 5 cópias desse serviço em execução no meu ambiente", torne-o verdadeiro e então (tentar) mantenha dessa forma.

Orchestrators (não humanos) são o que entre em ação quando um computador falha ou uma carga de trabalho termina por algum motivo inesperado. A maioria dos Orchestrators mais do que apenas lidar com falha, como ajudando com novas implantações, tratamento de atualizações e lidando com consumo de recursos, mas todos são bem sobre como manter que alguns desejado estado de configuração no ambiente. Você deseja ser capaz de lhe dizer um Orchestrator o que você deseja e fazer com que ele faça o trabalho pesado. Chronos ou maratona sobre Mesos, frota, por nuvem, Kubernetes e serviço tecidos são todos exemplos de Orchestrators (ou as integrado). Mais estão sendo criados sempre como as complexidades de gerenciamento implantações reais em diferentes tipos de ambientes e condições crescem e mudar.

## <a name="orchestration-as-a-service"></a>Coordenação como um serviço
O trabalho do organizador dentro de um cluster de serviço tecidos é tratado principalmente pelo Gerenciador de recursos de Cluster. O Gerenciador de recursos de Cluster do serviço tecidos é um dos serviços de sistema em estrutura de serviço e é iniciado automaticamente para cima dentro de cada cluster.  Geralmente, o Cluster Gerenciador de recursos de trabalho é dividido em três partes:

1. Impondo regras
2. Otimizar o ambiente
3. Assistência em outros processos

### <a name="what-it-isnt"></a>O que ele não está
No nível de N tradicional web-apps sempre houve alguns noção de um "balanceador de carga", geralmente conhecida como um balanceador de carga de rede (NLB) ou um balanceador de carga de aplicativo (ALB) dependendo de onde ela permaneceu na pilha de rede. Alguns balanceadores de carga são Hardware baseado como oferta de BigIP do F5, outras baseada em como da Microsoft software NLB. Em outros ambientes você talvez veja algo parecido com HAProxy desta função. Essas arquiteturas o trabalho de balanceamento de carga é certificar-se de que todas as máquinas diferentes front-end sem estado ou as máquinas diferentes no cluster (aproximadamente) recebem a mesma quantidade de trabalho. Estratégias para isso variados, enviem cada chamada diferente para um servidor diferente, a sessão fixação/adesão, a alocação de previsão e chamada real com base em seu custo esperado e a carga de máquina atual.

Observe que foi na melhor o mecanismo para garantir que a camada da web permaneceu aproximadamente equilibrada. Estratégias para equilibrando a camada de dados foram completamente diferente e dependentes o mecanismo de armazenamento de dados, geralmente centralizar em torno de fragmentação de dados, armazenamento em cache, modos de exibição de banco de dados gerenciado e procedimentos armazenados, etc.

Embora algumas dessas estratégias são interessantes, o Gerenciador de recursos de Cluster de tecidos do serviço não é algo como um balanceador de carga de rede ou um cache. Embora um balanceador de carga de rede garante que os front-ends são balanceadas movendo o tráfego para onde os serviços estão em execução, o Gerenciador de recursos do serviço tecidos Cluster usa uma estratégia completamente diferente – bem, serviço tecidos move *Serviços* para onde eles fazem mais sentido (e espera tráfego ou carregar para acompanhar). Isso pode ser, por exemplo, nós que estão atualmente fria porque os serviços que estão lá não fazendo muito trabalho agora, ou que tenham sido excluído ou movido em outro lugar. Como outro exemplo o Gerenciador de recursos de Cluster também pode mover um serviço longe de um computador que está prestes a ser atualizado ou que está sobrecarregado devido a um aumento no consumo pelos serviços que estava em execução. Como o Gerenciador de recursos de Cluster é responsável por movendo serviços ao redor (não fornecer o tráfego de rede para onde serviços já estão), ele contém um conjunto de recursos significativamente diferente em comparação com o que você deseja encontrar um balanceador de carga de rede e utiliza bem diferentes estratégias para garantir que os recursos de hardware no cluster são bem utilizados.

## <a name="next-steps"></a>Próximas etapas
- Para obter informações sobre o fluxo de informações e de arquitetura no Gerenciador de recursos de Cluster, confira [Este artigo](service-fabric-cluster-resource-manager-architecture.md)
- O Gerenciador de recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre eles fazer check-out neste artigo [descrevendo um cluster de estrutura de serviço](service-fabric-cluster-resource-manager-cluster-description.md)
- Para obter mais informações sobre as outras opções disponíveis para configurar serviços de check-out o tópico sobre as outras configurações do Gerenciador de recursos de Cluster disponíveis [Saiba como configurar os serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Métricas são como o Gerenciador de recursos de Cluster do serviço tecidos gerencia consumo e a capacidade do cluster. Para saber mais sobre eles e como configurá-los check-out [neste artigo](service-fabric-cluster-resource-manager-metrics.md)
- O Gerenciador de recursos de Cluster funciona com recursos de gerenciamento do serviço tecidos. Para saber mais sobre essa integração, leia [Este artigo](service-fabric-cluster-resource-manager-management-integration.md)
- Para saber mais sobre como o Gerenciador de recursos de Cluster gerencia e equilibra carga no cluster, confira o artigo sobre [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)
