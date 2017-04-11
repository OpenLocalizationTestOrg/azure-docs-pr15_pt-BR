<properties
   pageTitle="Serviço tecidos e contêineres de implantação no Linux | Microsoft Azure"
   description="Serviço tecidos e o uso de contêineres de Docker para implantar aplicativos de microservice. Este artigo descreve os recursos que o serviço tecidos fornece para contêineres e como implantar uma imagem de contêiner Docker em um cluster"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/24/2016"
   ms.author="msfussell"/>

# <a name="deploy-a-docker-container-to-service-fabric"></a>Implantar um contêiner de Docker em tecidos de serviço

> [AZURE.SELECTOR]
- [Implantar o contêiner do Windows](service-fabric-deploy-container.md)
- [Implantar o contêiner de Docker](service-fabric-deploy-container-linux.md)

Este artigo o orienta a criação de serviços contidos em contêineres de Docker no Linux.

Serviço tecidos tem vários recursos de contêiner que ajudarão-lo com a criação de aplicativos que são compostos de microservices que estão em contêiner. Estes são chamados contida em recipiente serviços.

Os recursos incluem;

- Ativação e implantação de imagem de contêiner
- Gestão de recursos
- Autenticação de repositório
- Porta de contêiner para mapeamento de porta de host
- Comunicação e descoberta de recipiente para outro
- Capacidade de configurar e definir variáveis de ambiente


## <a name="packaging-a-docker-container-with-yeoman"></a>Empacotando um contêiner de docker com yeoman
Quando um contêiner no Linux de embalagem, você pode escolher para usar um modelo yeoman ou [criar o pacote de aplicativo manualmente](service-fabric-deploy-container.md#manually-packaging-and-deploying-a-container).

Um aplicativo de serviço tecidos pode conter um ou mais contêineres, cada uma com uma função específica no fornecimento a funcionalidade do aplicativo. O SDK do serviço tecidos para Linux inclui um gerador de [Yeoman](http://yeoman.io/) que torna mais fácil criar seu aplicativo e adicione uma imagem de contêiner. Vamos usar Yeoman para criar um novo aplicativo com um único contêiner de Docker chamado *SimpleContainerApp*. Você pode adicionar que mais serviços posteriormente editando gerado arquivos de manifesto.

## <a name="create-the-application"></a>Criar o aplicativo

1. Em um terminal, digite **yo azuresfguest**.

2. Para o framework escolha **contêiner** .

3. Nomeie seu aplicativo por exemplo, SimpleContainerApp

4. Fornece a URL para a imagem de contêiner de um repo DockerHub. Isso o leva o formulário [repo] / [nome da imagem]

![Gerador de serviço tecidos Yeoman para contêineres][sf-yeoman]

## <a name="deploy-the-application"></a>Implantar o aplicativo

Depois que o aplicativo é criado, você pode implantá-lo para o cluster local usando a CLI do Azure.

1. Conectar-se ao cluster tecidos de serviço local.

    ```bash
    azure servicefabric cluster connect
    ```

2. Use o script de instalação fornecido no modelo para copiar o pacote de aplicativo para armazenamento de imagens do cluster, registrar o tipo de aplicativo e criar uma instância do aplicativo.

    ```bash
    ./install.sh
    ```

3. Abra um navegador e navegue até o serviço tecidos Explorer em http://localhost:19080/Explorer (substituir host local com o IP particular da máquina virtual se usando Vagrant no Mac OS X).

4. Expanda o nó de aplicativos e observe que agora há uma entrada para o tipo de aplicativo e outro para a primeira instância desse tipo.

5. Use o script de desinstalação fornecido no modelo para excluir a instância do aplicativo e cancelar o registro do tipo de aplicativo.

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da estrutura de serviço e contêineres](service-fabric-containers-overview.md)
- [Interagindo com clusters de serviço tecidos utilizando a CLI do Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman.png

