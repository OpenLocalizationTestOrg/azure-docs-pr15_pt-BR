<properties
   pageTitle="Criar seu primeiro aplicativo de serviço tecidos no Linux usando c# | Microsoft Azure"
   description="Criar e implantar um aplicativo de serviço tecidos usando C#"
   services="service-fabric"
   documentationCenter="csharp"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="csharp"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/04/2016"
   ms.author="subramar"/>


# <a name="create-your-first-azure-service-fabric-application"></a>Criar seu primeiro aplicativo tecidos de serviço do Azure

> [AZURE.SELECTOR]
- [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
- [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
- [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)

Serviço tecidos fornece SDKs para a criação de serviços no Linux no .NET Core e Java. Neste tutorial, examinaremos como criar um aplicativo para Linux e criar um serviço usando c# (.NET núcleo).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que você tenha [configurar seu ambiente de desenvolvimento do Linux](service-fabric-get-started-linux.md). Se você estiver usando o Mac OS X, você pode [Configurar um ambiente de uma caixa Linux em uma máquina virtual usando Vagrant](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Criar o aplicativo

Um aplicativo de serviço tecidos pode conter um ou mais serviços, cada um com uma função específica no fornecimento a funcionalidade do aplicativo. O SDK do serviço tecidos para Linux inclui um gerador de [Yeoman](http://yeoman.io/) que torna mais fácil para criar seu primeiro serviço e adicionar mais posteriormente. Vamos usar Yeoman para criar um aplicativo com um único serviço.

1. Em um terminal, digite o seguinte comando para começar a criar a estrutura:`yo azuresfcsharp`

2. Nome de seu aplicativo.

3. Escolha o tipo de seu serviço primeiro e nomeie. Para os fins deste tutorial, escolhemos um serviço de ator confiável.

  ![Gerador de serviço tecidos Yeoman para C#][sf-yeoman]

>[AZURE.NOTE] Para obter mais informações sobre as opções, consulte [Visão geral do modelo programação tecidos de serviço](service-fabric-choose-framework.md).

## <a name="build-the-application"></a>Criar o aplicativo

Os modelos de serviço tecidos Yeoman incluem um script de compilação que você pode usar para criar o aplicativo do terminal (após navegar para a pasta de aplicativo).

  ```bash
 cd myapp 
 ./build.sh 
  ```

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

## <a name="start-the-test-client-and-perform-a-failover"></a>Inicie o cliente de teste e realizar um failover

Projetos de ator não fizer nada por conta própria. Eles exigem outro serviço ou cliente para enviar mensagens. O modelo de ator inclui um script de teste simples que você pode usar para interagir com o serviço de ator.

1. Execute o script usando o utilitário de inspeção para ver o resultado do serviço de ator.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. No Explorador de estrutura de serviço, localize o nó que hospeda a réplica principal para o serviço de ator. Captura de tela abaixo, é nó 3.

    ![Localizando a réplica principal no Explorador de estrutura de serviço][sfx-primary]

3. Clique no nó que você encontrou na etapa anterior e selecione **Desativar (Reiniciar)** no menu Ações. Esta ação reinicia um dos cinco nós no seu cluster local forçando um failover para uma réplica secundária em execução no outro nó. Como executar esta ação, observe a saída do cliente de teste e observe que o contador continua a incrementar Apesar o failover.


## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre atores confiável](service-fabric-reliable-actors-introduction.md)
- [Interagindo com clusters de serviço tecidos utilizando a CLI do Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
