> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-get-started.md)

Este artigo fornece uma passo a passo detalhado do [código de exemplo Hello World] [ lnk-helloworld-sample] para ilustrar os componentes fundamentais do [SDK do Windows Azure IoT Gateway] [ lnk-gateway-sdk] arquitetura. O exemplo usa o SDK do Gateway de Hub IoT para construir um gateway simple que registra uma mensagem de "Olá mundo" em um arquivo a cada cinco segundos.

Este passo a passo aborda:

- **Conceitos**: uma visão geral conceitual dos componentes que compõem a qualquer gateway que você criar com o SDK do Gateway IoT.  
- **Arquitetura de amostra Hello World**: descreve como os conceitos se aplicam ao exemplo Hello World e como os componentes se encaixam.
- **Como criar a amostra**: as etapas necessárias para compilar o exemplo.
- **Como executar a amostra**: as etapas necessárias para executar a amostra. 
- **A saída típica**: um exemplo da saída esperar quando você executa o exemplo.
- **Trechos de código**: uma coleção de trechos de código para mostrar como a amostra de Hello World implementa os componentes principais do gateway.

## <a name="azure-iot-gateway-sdk-concepts"></a>Conceitos do Azure IoT Gateway SDK

Antes de examinar o código de amostra ou criar seu próprio gateway campo usando o SDK do Gateway IoT, você deve compreender os principais conceitos que são a base a arquitetura do SDK.

### <a name="modules"></a>Módulos

Você pode construir um gateway com o SDK do Windows Azure IoT Gateway criando e montando *módulos*. Módulos usam *mensagens* para trocar dados entre si. Um módulo recebe uma mensagem, executa alguma ação nele, transforma opcionalmente em uma nova mensagem e, em seguida, publicá-lo para outros módulos processar. Alguns módulos podem produzir apenas novas mensagens e nunca processar as mensagens recebidas. Uma cadeia de módulos cria um pipeline de processamento de dados com cada módulo realizando uma transformação nos dados em um ponto desse pipeline.

![Uma cadeia de módulos no gateway criadas com o SDK do Windows Azure IoT Gateway][1]
 
O SDK contém o seguinte:

- Módulos previamente redigidos que executam funções comuns de gateway.
- As interfaces de que um desenvolvedor pode usar para gravar módulos personalizados.
- A infraestrutura necessária para implantar e executar um conjunto de módulos.

O SDK fornece uma camada de abstração que permite que você crie gateways para executar em uma variedade de sistemas operacionais e plataformas.

![Camada de abstração do Azure IoT Gateway SDK][2]

### <a name="messages"></a>Mensagens

Embora uma maneira conveniente de conceitualizar como funções de um gateway pensar módulos passando mensagens entre si, ele não refletem com precisão o que acontece. Módulos de usam um corretor para se comunicar entre si, eles publicar mensagens ao broker (barramento, pubsub ou qualquer outro padrão de mensagens) e deixe o corretor rotear a mensagem para os módulos conectados a ele.

Um módulos utiliza a função de **Broker_Publish** para publicar uma mensagem ao broker. O agente de entrega mensagens a um módulo invocando-se uma função de retorno de chamada. Uma mensagem consiste em um conjunto de propriedades de chave/valor e o conteúdo passado como um bloco de memória.

![A função de agente no Azure IoT Gateway SDK][3]

### <a name="message-routing-and-filtering"></a>Filtragem e roteamento de mensagens

Existem duas maneiras de direcionamento de mensagens para os módulos corretos. Um conjunto de links pode ser passado para o agente de forma que o broker reconheça a origem e o coletor de eventos para cada módulo, ou o módulo pode filtrar as propriedades da mensagem. Um módulo somente deve agir sobre uma mensagem se a mensagem é destinada. Os links e a filtragem de mensagens é o que efetivamente cria um pipeline de mensagem.

## <a name="hello-world-sample-architecture"></a>Arquitetura de amostra Olá mundo

O exemplo de Hello World ilustra os conceitos descritos na seção anterior. O exemplo de Hello World implementa um gateway que tem um pipeline composto por dois módulos:

-   O módulo *Olá, mundo* cria uma mensagem a cada cinco segundos e o passa para o módulo de registro.
-   O módulo de *registro* grava as mensagens que recebe um arquivo.

![Arquitetura de amostra Olá mundo criada com o SDK do Windows Azure IoT Gateway][4]

Conforme descrito na seção anterior, o módulo Olá mundo não passa mensagens diretamente para o módulo de registro a cada cinco segundos. Em vez disso, ele publica uma mensagem ao broker cada cinco segundos.

O módulo de registro recebe uma mensagem do agente e age sobre o, gravar o conteúdo da mensagem em um arquivo.

O módulo de registro só consome mensagens de agente, ele nunca publica novas mensagens ao broker.

![Como o corretor roteia mensagens entre módulos no Azure IoT Gateway SDK][5]

A figura acima mostra a arquitetura de amostra de Hello World e os caminhos relativos para os arquivos de origem que implementam diferentes partes da amostra no [repositório][lnk-gateway-sdk]. Explorar o código por conta própria ou use os trechos de código abaixo como guia.

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-getstarted-selector/modules.png
[2]: media/iot-hub-gateway-sdk-getstarted-selector/modules_2.png
[3]: media/iot-hub-gateway-sdk-getstarted-selector/messages_1.png
[4]: media/iot-hub-gateway-sdk-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-gateway-sdk-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/hello_world
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk