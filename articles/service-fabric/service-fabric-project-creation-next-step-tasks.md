<properties
   pageTitle="As próximas etapas de criação de projeto serviço tecidos | Microsoft Azure"
   description="Este artigo contém links para um conjunto de tarefas básicas de desenvolvimento tecidos de serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# <a name="your-service-fabric-application-and-next-steps"></a>Seu aplicativo de serviço tecidos e próximas etapas
Seu aplicativo do Azure serviço tecidos foi criado. Este artigo descreve a composição do seu projeto e possíveis próximas etapas.

## <a name="your-application"></a>Seu aplicativo
Cada novo aplicativo inclui um projeto de aplicativo. Pode haver um ou dois projetos adicionais, dependendo do tipo de serviço escolhido.

### <a name="the-application-project"></a>O projeto de aplicativo
O projeto de aplicativo consiste em:

- Um conjunto de referências para os serviços que constituem seu aplicativo.

- Dois publicar perfis (Local e nuvem) que você pode usar para manter preferências para trabalhar com diferentes ambientes – como preferências relacionadas a um ponto de extremidade de cluster e se deseja realizar implantações de atualização por padrão.

- Dois aplicativo parâmetro arquivos (Local e nuvem) que você pode usar para manter as configurações de aplicativo de ambiente específicas, como o número de partições para criar para um serviço.

- Um script de implantação que você pode usar para implantar seu aplicativo na linha de comando ou como parte de um pipeline de implantação e a integração contínuo automatizado.

- O manifesto do aplicativo, que descreve o aplicativo. Você pode encontrar o manifesto na pasta ApplicationPackageRoot.

### <a name="stateless-service"></a>Serviço de estado
Quando você adiciona um novo serviço sem estado, o Visual Studio adiciona um projeto de serviço à sua solução que inclui um tipo descendentes do `StatelessService`. O serviço é incrementado uma variável local em um contador.

### <a name="stateful-service"></a>Serviço de estado
Quando você adiciona um novo serviço de estado, o Visual Studio adiciona um projeto de serviço à sua solução que inclui um tipo descendentes do `StatefulService`. O serviço é incrementado um contador no seu `RunAsync` método e armazena o resultado em uma `ReliableDictionary`.

### <a name="actor-service"></a>Serviço de ator
Quando você adiciona um novo ator confiável, o Visual Studio adiciona dois projetos à sua solução: um projeto de ator e um projeto de interface.

O projeto de ator fornece métodos para configuração e Obtendo o valor de um contador que é mantido confiavelmente dentro de estado do ator. O projeto de interface fornece uma interface que outros serviços podem usar para chamar o ator.

### <a name="stateless-web-api"></a>API da Web sem estado
O projeto de API da Web sem estado fornece um serviço da web básicas que você pode usar para abrir o aplicativo para clientes externos. Para obter mais informações sobre como o projeto estruturado, consulte [Serviços de Web API do serviço tecidos com OWIN hospedagem interna](service-fabric-reliable-services-communication-webapi.md).

### <a name="aspnet-core"></a>Principais do ASP.NET

O SDK do serviço tecidos fornece o mesmo conjunto de núcleo de ASP.NET modelos que estão disponíveis para autônomo ASP.NET Core projetos: esvaziar, [API Web][aspnet-webapi]e o [Aplicativo Web][aspnet-webapp].

## <a name="next-steps"></a>Próximas etapas
### <a name="create-an-azure-cluster"></a>Criar um cluster Azure
O SDK do serviço tecidos fornece um cluster local para desenvolvimento e teste. Para criar um cluster no Azure, consulte [Configurar um cluster de estrutura de serviço do portal do Azure][create-cluster-in-portal].

### <a name="try-deploying-to-azure-for-free-with-party-clusters"></a>Tente para implantar Azure gratuitamente com clusters de festa

Se você quiser tentar implantação e gerenciamento de aplicativos no Azure sem configurar seus próprio clusters, você pode usar o [serviço de cluster de terceiros](http://aka.ms/tryservicefabric)de livre.

### <a name="publish-your-application-to-azure"></a>Publicar seu aplicativo no Azure
Você pode publicar seu aplicativo diretamente do Visual Studio para um cluster Azure. Para saber como, consulte [publicar seu aplicativo no Azure][publish-app-to-azure].

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>Usar o serviço tecidos Explorer para visualizar seu cluster
Serviço tecidos Explorer oferece uma maneira fácil de visualizar seu cluster, incluindo aplicativos implantados e layout físico. Para saber mais, consulte [visualizando seu cluster usando o serviço tecidos Explorer][visualize-with-sfx].

### <a name="version-and-upgrade-your-services"></a>Versão e atualização de seus serviços
Serviço tecidos habilita independente controle de versão e atualização dos serviços independentes em um aplicativo. Para saber mais, consulte [Atualizando seus serviços e controle de versão][app-upgrade-tutorial].

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>Configurar integração contínua com os serviços de equipe do Visual Studio
Para saber como você pode configurar um processo de integração contínua para seu aplicativo de serviço tecidos, consulte [Configurar integração contínua com os serviços de equipe do Visual Studio][ci-with-vso].


<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html
