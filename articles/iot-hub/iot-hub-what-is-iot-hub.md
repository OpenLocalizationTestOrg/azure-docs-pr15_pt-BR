<properties
 pageTitle="Visão geral de IoT Hub Azure | Microsoft Azure"
 description="Visão geral do serviço do Azure IoT Hub: o que é o hub de iot, conectividade de dispositivo, internet de padrões de comunicação de coisas e padrão de comunicação assistido de serviço"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/25/2016"
 ms.author="dobett"/>

# <a name="what-is-azure-iot-hub"></a>O que é Azure IoT Hub?

Bem-vindo ao Azure IoT Hub. Este artigo fornece uma visão geral do Azure IoT Hub e descreve por que você deve usar esse serviço para implementar uma solução de Internet das coisas (IoT). Hub de IoT Azure é um serviço totalmente gerenciado que permite comunicações de seguro e confiável bidirecional entre milhões de dispositivos IoT e back-end uma solução. Hub de IoT Azure:

- Fornece confiável dispositivo na nuvem e nuvem para dispositivo mensagens em escala.
- Permite proteger as comunicações usando credenciais de segurança de cada dispositivo e controle de acesso.
- Fornece monitoramento abrangente de conectividade de dispositivos e eventos de gerenciamento de identidade do dispositivo.
- Inclui bibliotecas de dispositivo para as plataformas e idiomas mais populares.

O artigo de [Hub de comparação de IoT e Hubs de evento] [ lnk-compare] descreve as principais diferenças entre esses dois serviços e realça as vantagens de usar IoT Hub em suas soluções IoT.

![Hub de IoT Azure como gateway de nuvem no internet da solução de itens][img-architecture]

> [AZURE.NOTE] Para uma discussão aprofundada da arquitetura de IoT, consulte a [Arquitetura de referência do Microsoft Azure IoT][lnk-refarch].

## <a name="iot-device-connectivity-challenges"></a>IoT desafios de conectividade de dispositivos

Hub de IoT e as bibliotecas de dispositivo ajudam você a enfrentar os desafios dos como confiável e com segurança conectar dispositivos ao back-end solução. IoT dispositivos:

- São geralmente sistemas incorporados sem operador humanos.
- Podem estar em locais remotos, onde o acesso físico é caro.
- Só pode ser acessado por meio de back-end solução.
- Poderá ter limitados de energia e recursos de processamento.
- Pode ter conectividade de rede intermitente, lenta ou cara.
- Talvez seja necessário usar protocolos de aplicativo proprietário, personalizado ou específicas do setor.
- Pode ser criado usando um conjunto grande de plataformas de hardware e software populares.

Além dos requisitos acima, qualquer solução IoT também deve fornecer escala, segurança e confiabilidade. O conjunto de requisitos de conectividade resultante é difícil e demorado implementar quando você usa tecnologias tradicionais, como contêineres web e os agentes de mensagens.

## <a name="why-use-azure-iot-hub"></a>Por que usar Azure IoT Hub

Hub de IoT Azure aborda os desafios de conectividade de dispositivos das seguintes maneiras:

-   **Por dispositivo autenticação e conectividade segura**. Você pode provisionar cada dispositivo com sua própria [chave de segurança] [ lnk-devguide-security] para ativá-lo para se conectar ao IoT Hub. O [registro de identidade IoT Hub] [ lnk-devguide-identityregistry] armazena identidades do dispositivo e chaves em uma solução. Um back-end solução pode adicionar dispositivos individuais para permitir ou negar listas para habilitar o controle total sobre acesso de dispositivos.

-   **Monitoramento de operações de conectividade do dispositivo**. Você pode receber logs de operação detalhadas sobre operações de gerenciamento de identidade do dispositivo e eventos de conectividade do dispositivo. Esse recurso de monitoramento possibilita sua solução IoT identificar problemas de conectividade, como dispositivos que tentam se conectar com credenciais erradas, enviar mensagens muito com frequência, ou rejeitar todas as mensagens de nuvem para dispositivo.

-   **Um conjunto abrangente de bibliotecas de dispositivo**. [Dispositivo de IoT Azure SDKs] [ lnk-device-sdks] estão disponíveis e com suporte para vários idiomas e plataformas – C para muitos distribuições Linux, Windows e sistemas operacionais em tempo real. SDKs de dispositivo do Azure IoT também oferecem suporte a idiomas gerenciados, como c#, Java e JavaScript.

-   **Extensibilidade e IoT protocolos**. Se sua solução não pode usar as bibliotecas de dispositivo, IoT Hub expõe um protocolo público que permite que dispositivos nativamente usar o AMQP 1.0 protocolos, HTTP 1.1 ou v 3.1.1 MQTT. Você também pode estender IoT Hub para oferecer suporte para protocolos personalizados por:

    - Criar um gateway de campo com o [SDK do Azure IoT Gateway] [ lnk-gateway-sdk] que converte seu protocolo personalizado a um dos três protocolos compreendidos por IoT Hub. 
    - Personalizando o [gateway de protocolo do Azure IoT][protocol-gateway], um componente de código-fonte aberto que é executado na nuvem.

-   **Escala**. Hub de IoT Azure expande para milhões de dispositivos conectados simultaneamente e milhões de eventos por segundo.

Esses benefícios são genéricos para muitos padrões de comunicação. Hub de IoT atualmente permite implementar os seguintes padrões de comunicação específicos:

-   **Inclusão de dispositivo à nuvem baseada em evento.** Hub de IoT confiável pode receber milhões de eventos por segundo de seus dispositivos. Ele pode processá-los no caminho quente usando um mecanismo de processador de eventos. Ele também pode armazená-las no seu caminho frio para análise. Hub de IoT retém os dados de evento para até sete dias para garantir o processamento confiável e absorvê picos na carga.

-   * *Mensagens confiáveis de nuvem para dispositivo (ou *comandos*). * * back-end solução pode usar IoT Hub para enviar mensagens com uma garantia de entrega ao menos uma para dispositivos individuais. Cada mensagem tem uma configuração de time to live individual e back-end pode solicitar confirmações de entrega e expiração. Estas confirmações garantir visibilidade completa de ciclo de vida de uma mensagem de nuvem para dispositivo. Em seguida, você pode implementar lógica de negócios que inclui operações que são executadas em dispositivos.

-   **Carregar arquivos e dados armazenados em cache para a nuvem.** Seus dispositivos podem carregar arquivos para o armazenamento do Azure utilizando URIs de SAS gerenciada para você por IoT Hub. Hub de IoT pode gerar notificações quando chegarem arquivos na nuvem para habilitar o back-end processá-los.

## <a name="gateways"></a>Gateways

Um gateway em uma solução IoT normalmente é um [gateway de protocolo] [ lnk-gateway] que é implantado em nuvem ou um [gateway de campo] [ lnk-field-gateway] que é implantado localmente com seus dispositivos. Um gateway de protocolo executa a conversão de protocolo, por exemplo MQTT para AMQP. Um gateway de campo pode executar análise na borda, tomar decisões de temporais para reduzir a latência, forneça serviços de gerenciamento de dispositivo, impor restrições de privacidade e segurança e também executar tradução de protocolo. Os dois tipos de gateway agem como intermediários entre seus dispositivos e seu hub de IoT.

Um gateway de campo difere em um dispositivo de roteamento de tráfego simples (como um dispositivo de tradução de endereço de rede ou firewall) porque ele normalmente executa uma função ativa no gerenciamento de fluxo de informações e acesso em sua solução.

Uma solução pode incluir gateways de protocolo e o campo.

## <a name="how-does-iot-hub-work"></a>Como funciona o Hub de IoT?

Hub de IoT Azure implementa a [comunicação assistido serviço] [ lnk-service-assisted-pattern] padrão para mediar as interações entre seus dispositivos e back-end sua solução. O objetivo de comunicação assistido serviço é estabelecer confiável, caminhos de comunicação bidirecional entre um sistema de controle, como o Hub de IoT e dispositivos de propósito especial que são implantadas no não confiável espaço físico. O padrão estabelece os princípios a seguir:

- Segurança tem precedência sobre todos os outros recursos.
- Dispositivos não aceitar as informações de rede não solicitado. Um dispositivo estabelece todas as conexões e rotas de maneira apenas de saída. Para um dispositivo recebe um comando de back-end, o dispositivo regularmente deve iniciar uma conexão para verificar se há algum comando pendente para processar.
- Dispositivos só devem conectar ou estabelecer rotas para serviços conhecidos, que eles são peered com, como o Hub de IoT.
- O caminho de comunicação entre o dispositivo e serviço ou dispositivo e o gateway está protegido na camada de protocolo de aplicativo.
- Autenticação e autorização de nível de sistema são baseados em identidades por dispositivo. Eles tornam credenciais de acesso e permissões revogável quase instantaneamente.
- Comunicação bidirecional para dispositivos que se conectam esporadicamente devido a potência ou a conectividade preocupações é facilitada mantendo comandos e notificações de dispositivo até que um dispositivo se conecta para recebê-los. Hub de IoT mantém filas de dispositivo específicos para os comandos que ele envia.
- Dados de carga de aplicativo são protegidos separadamente para trânsito protegido por meio de gateways para um serviço específico.

O setor móvel usou o padrão de comunicação assistido serviço em escala enorme implementar serviços de notificação de envio como [Os serviços de notificação do Windows Push][lnk-wns], [Mensagens de nuvem do Google][lnk-google-messaging], [Serviço de notificações de envio do Apple]e[lnk-apple-push].

Hub de IoT é suportada sobre caminho de correspondência público da rota expressa.

## <a name="next-steps"></a>Próximas etapas

Para saber como o Hub de IoT Azure permite gerenciamento de dispositivo de IoT baseada em padrões para você gerenciar, configurar e atualizar seus dispositivos remotamente, consulte [gerenciamento de dispositivo de visão geral do Azure IoT Hub][lnk-device-management].

Para implementar aplicativos cliente em uma ampla variedade de plataformas de hardware do dispositivo e sistemas operacionais, você pode usar o dispositivo IoT SDKs. O dispositivo de IoT SDKs incluem bibliotecas que facilitam o envio telemetria a um hub IoT e receber comandos de nuvem para dispositivo. Quando você usa o SDK do, você pode escolher entre vários protocolos de rede para se comunicar com IoT Hub. Para saber mais, consulte as [informações sobre dispositivo SDKs][lnk-device-sdks].

Para começar a escrever algum código e executando alguns exemplos, consulte a [Introdução ao IoT Hub] [ lnk-get-started] tutorial.

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png


[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Serviço assistido comunicação, postagem de blog por Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-device-management]: iot-hub-device-management-overview.md
