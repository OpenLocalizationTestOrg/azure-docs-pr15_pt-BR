<properties
   pageTitle="Visão geral do serviço tecidos | Microsoft Azure"
   description="Uma visão geral da estrutura de serviço, onde os aplicativos são compostos de muitos microservices para fornecer escala e resiliência. Serviço tecidos é uma plataforma de sistemas distribuídos usado para construir scalable, confiável e gerenciado facilmente aplicativos para a nuvem"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/22/2016"
   ms.author="mfussell"/>

# <a name="overview-of-service-fabric"></a>Visão geral da estrutura de serviço
Estrutura de serviço é uma plataforma de sistemas distribuídos que facilita a empacotar, implantar e gerenciar microservices flexível e confiável. Serviço tecidos também aborda os desafios significativos em desenvolvimento e gerenciamento de aplicativos em nuvem. Desenvolvedores e administradores podem evitar Resolvendo problemas de infraestrutura complexa e foco em vez disso implementando essenciais exigentes cargas de trabalho saber que eles estão scalable, confiável e gerenciável. Serviço tecidos representa a plataforma de próxima geração middleware para desenvolver e gerenciar esses corporativos, aplicativos de nuvem escala de nível 1.

Este [vídeo curto](https://aka.ms/servicefabricvideo) fornece uma introdução ao serviço tecidos e microservices.


## <a name="applications-composed-of-microservices"></a>Aplicativos compostos de microservices
Serviço tecidos permite criar e gerenciar aplicativos scalable e confiáveis compostos microservices executado no densidade muito alta em um pool compartilhado de máquinas (referido como um cluster). Ele fornece um tempo de execução sofisticado para criação distribuído, scalable microservices com e sem monitoração. Ele também fornece recursos de gerenciamento de aplicativo abrangente para provisionamento, implantando, monitoramento, atualização/patch e excluindo aplicativos implantados.

Por que uma abordagem de microservices é importante? Os dois motivos principais são:

1. Eles permitem que você dimensionar diferentes partes do seu aplicativo dependendo das suas necessidades.

2. As equipes de desenvolvimento são capazes de ser mais ágil em contínuas alterações e, portanto, fornecem recursos aos seus clientes com mais rapidez e com mais frequência.

Serviço tecidos alimenta muitos serviços da Microsoft hoje, incluindo o banco de dados do SQL Azure, Azure DocumentDB, Cortana, Power BI, Microsoft Intune, Hubs de evento do Azure, IoT Azure, Skype for Business e muitos core services Azure.

Serviço tecidos são adaptados para criando nuvem nativos serviços que podem começar pequeno, conforme necessário e aumentar a escala de massa com centenas ou milhares de máquinas.

Serviços de Internet escala de hoje são criados de microservices. Exemplos de microservices gateways de protocolo, perfis de usuário, compras carrinhos, estoque processamento, filas e armazena. Estrutura de serviço é uma plataforma de microservices que fornece um nome exclusivo que pode ser tanto com ou sem estado de cada microservice.

Serviço tecidos fornece abrangentes recursos de gerenciamento de ciclo de vida e tempo de execução para aplicativos compostos por essas microservices. Ele hospeda microservices dentro de contêineres implantado e ativado no cluster tecidos de serviço. Movendo de VMs possível do contêineres torna um aumento de ordem de magnitude em densidade. Da mesma forma, outra ordem de magnitude em densidade torna-se possível movendo do contêineres para microservices. Por exemplo, um único cluster Azure SQL Database abrange centenas de máquinas executando milhares de contêineres de um total de centenas de milhares de bancos de dados de hospedagem. Cada banco de dados é uma microservice de estado de serviço tecidos. O mesmo acontece dos outros serviços mencionados anteriormente, por isso, o termo "hyperscale" é usado para descrever os recursos de serviço tecidos. Se contêineres dar alta densidade, em seguida, microservices fornecer hyperscale.

Para obter mais informações sobre a abordagem microservices, leia [por uma abordagem de microservices à compilação de aplicativos?](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>Coordenação e implantação do contêiner
Estrutura de serviço é um [orchestrator](service-fabric-cluster-resource-manager-introduction.md) de microservices em um cluster de máquinas. Microservices podem ser desenvolvidos de muitas maneiras de usar os [modelos de programação de serviço tecidos](service-fabric-choose-framework.md) a implantação [executáveis de convidado](service-fabric-deploy-existing-app.md). Serviço tecidos pode implantar os serviços em imagens do contêiner e importante: você pode misturar serviços em processos e serviços em contêineres juntos no mesmo aplicativo. Se você quiser apenas [implantar e gerenciar contêiner imagens](service-fabric-containers-overview.md) em um cluster de máquinas, estrutura de serviço é uma opção ideal para isso.


## <a name="create-service-fabric-clusters-anywhere"></a>Criar clusters de serviço tecidos em qualquer lugar
Você pode criar clusters de serviço tecidos em muitos ambientes, incluindo Azure ou no local, no Windows Server ou no Linux. Além disso, o ambiente de desenvolvimento no SDK é idêntico ao ambiente de produção com nenhum emuladores envolvidas. Em outras palavras, se ele é executado em seu cluster de desenvolvimento local-implanta para o mesmo cluster em outros ambientes.

Para obter mais informações sobre como criar clusters no local, leia [Criando um cluster no Windows Server ou no Linux](service-fabric-deploy-anywhere.md) ou para o Azure criando um cluster [por meio do portal do Azure](service-fabric-cluster-creation-via-portal.md).

![Plataforma de estrutura de serviço][Image1]

## <a name="stateless-and-stateful-service-fabric-microservices"></a>Microservices de serviço tecidos com e sem monitoração

Serviço tecidos permite criar aplicativos consiste em microservices. Sem estado microservices (gateways de protocolo, proxies da web, etc.) não mantêm um estado mutável fora qualquer solicitação e sua resposta do serviço. Funções de trabalho de serviços de nuvem Azure são um exemplo de um serviço sem estado. Estado microservices (contas de usuário, bancos de dados, dispositivos, compras carrinhos, filas, etc.) manter um estado mutável autoritativo além da solicitação e sua resposta. Escala de Internet aplicativos hoje consistem em uma combinação de microservices com e sem monitoração.

Por que tenho estado microservices juntamente com aquelas sem estado? Os dois motivos principais são:

1. A capacidade de criar alta produtividade, baixa latência, tolerância a falha de processamento de transações online services (OLTP), mantendo o código e fechar dados na mesma máquina. Alguns exemplos são interativas lojas, pesquisa, sistemas de Internet das coisas (IoT), sistemas de comércio, sistemas de detecção de processamento e fraudes de cartão de crédito e gerenciamento de registros pessoais.

2. Simplificação de design do aplicativo. Estado microservices remover a necessidade de filas adicionais e armazena, normalmente necessárias para solucionar os requisitos de disponibilidade e latência de um aplicativo puramente sem estado. Serviços de estado estão naturalmente alta disponibilidade e baixa latência, reduzindo o número de partes móveis para gerenciar em seu aplicativo como um todo.

Para obter mais informações sobre padrões de aplicativo com estrutura de serviço, leia [cenários de aplicativos](service-fabric-application-scenarios.md) e [escolhendo uma estrutura de modelo de programação](service-fabric-choose-framework.md) para o seu serviço

## <a name="application-lifecycle-management"></a>Gerenciamento do ciclo de vida de aplicativos
Serviço tecidos fornece suporte de primeira classe para o gerenciamento de ciclo de vida do aplicativo completo (ALM) de aplicativos de nuvem – de desenvolvimento por meio de implantação, gerenciamento diário e manutenção para encerrar o eventual.

Os recursos de serviço tecidos ALM permitem aos administradores do aplicativo / operadores de TI para usar fluxos de trabalho simples, com pouca provisionar, implantar, patch e monitorar aplicativos. Esses fluxos de trabalho internos reduzem significativamente a carga sobre os operadores de TI para manter os aplicativos continuamente disponíveis.

A maioria dos aplicativos consistem em uma combinação de com e sem monitoração microservices e outros executáveis/tempos de execução implantados juntas. Por ter tipos fortes nos aplicativos e empacotado microservices, serviço tecidos permite a implantação de várias instâncias do aplicativo. Cada instância é gerenciada e atualizada independentemente. Importante, serviço tecidos é capaz de implantar *qualquer* executáveis ou runtime e torná-las confiável. Por exemplo, o serviço tecidos implanta ASP.NET Core 1, Node, Java VMs, scripts ou qualquer outra coisa que compõe seu aplicativo.

Para obter mais informações sobre o gerenciamento de ciclo de vida do aplicativo, leia o [ciclo de vida do aplicativo](service-fabric-application-lifecycle.md) e sobre como implantar qualquer código consulte [implantar um convidado executável](service-fabric-deploy-existing-app.md)

## <a name="key-capabilities"></a>Principais recursos
Usando a estrutura de serviço, você pode:

- Desenvolva aplicativos amplamente escaláveis que são AutoCorreção.

- Desenvolva aplicativos compostos microservices usando o modelo de programação de serviço tecidos. Ou simplesmente host convidado executáveis e outras estruturas de aplicativo de sua escolha, como ASP.NET Core 1 ou Node.

- Desenvolva microservices com e sem monitoração altamente confiável.

- Implantar e coordenar contêineres incluem contêineres do Windows e contêineres de Docker em um cluster. Esses contêineres podem contêiner convidado executáveis ou microservices com e sem monitoração confiável.  Em ambos os casos, você recebe porta de contêiner para mapeamento de porta de host, capacidade de descoberta de contêiner e failover automatizado.

- Simplifica o design do seu aplicativo usando o estado microservices no lugar de caches e filas.

- Implante Azure ou nuvens local executando o Windows Server ou Linux com zero alterações de código. Gravar uma vez e, em seguida, implantar em qualquer lugar em qualquer cluster de estrutura de serviço.

- Desenvolva com uma abordagem de "Data Center em sua máquina". O ambiente de desenvolvimento local é o mesmo código que é executado em data centers Azure.

- Implante aplicativos em segundos.

- Implante aplicativos no maior densidade que máquinas virtuais, implantando centenas ou milhares de aplicativos por máquina.

- Implante diferentes versões do mesmo aplicativo lado a lado, cada independentemente atualizável.

- Gerencie o ciclo de vida de seus aplicativos com estado sem qualquer tempo de inatividade, incluindo atualizações mais recentes e incondicionais.

- Gerenciar aplicativos usando APIs do .NET, Java (Linux), o PowerShell, o Azure CLI (Linux) ou o resto interfaces.

- Atualização e patch microservices dentro de aplicativos de maneira independente.

- Monitorar e diagnosticar a integridade dos seus aplicativos e definir políticas para executar reparos automáticos.

- Capacidade de escala- ou escala-o número de nós em um cluster, bem como escala-up ou escala-down o tamanho de cada nó, sabendo que seus aplicativos automaticamente dimensionar e são distribuídos de acordo com os recursos disponíveis.

- Inspeção balanceador de recurso AutoCorreção coordenar a redistribuição de aplicativos no cluster. Serviço tecidos recupera de falhas e otimiza a distribuição de carga com base em recursos disponíveis.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações:
    * [Por que um microservices abordagem à compilação de aplicativos?](service-fabric-overview-microservices.md)
    * [Visão geral de terminologia](service-fabric-technical-overview.md)
* Configurando o serviço tecidos [ambiente de desenvolvimento](service-fabric-get-started.md)  
* [Escolhendo uma estrutura de modelo de programação](service-fabric-choose-framework.md) para o seu serviço

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
