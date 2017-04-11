<properties
 pageTitle="Tópicos da guia Desenvolvedor para IoT Hub | Microsoft Azure"
 description="Guia de desenvolvedor do IoT Hub Azure que inclui pontos de extremidade do IoT Hub, segurança, registro de identidade de dispositivo, gerenciamento de dispositivo e de mensagens"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="azure-iot-hub-developer-guide"></a>Guia de desenvolvedor do Azure IoT Hub

Hub de IoT Azure é um serviço totalmente gerenciado que ajuda a habilitar confiável e segura comunicação bidirecional entre milhões de dispositivos IoT e encerrar um aplicativo novamente.

Hub de IoT Azure oferece:

* Proteger as comunicações usando credenciais de segurança de cada dispositivo e controle de acesso.
* Dispositivo na nuvem e nuvem para dispositivo hyper escala mensagens confiáveis.
* Conectividade de dispositivos fácil com bibliotecas de dispositivo para as plataformas e idiomas mais populares.

Este guia de desenvolvedor do IoT Hub inclui os seguintes artigos:

- [Enviar e receber mensagens com IoT Hub] [ devguide-messaging] descreve os recursos de mensagens (dispositivo na nuvem e nuvem para dispositivo) que IoT Hub expõe. O artigo também inclui informações sobre tópicos como formatos de mensagem e os protocolos de comunicação com suporte e os números de porta usarem.
- [Carregar arquivos em um dispositivo] [ devguide-upload] descreve como você pode carregar arquivos em um dispositivo. O artigo também inclui informações sobre tópicos como as notificações o processo de uplaod pode enviar.
- [Gerenciar identidades de dispositivo no IoT Hub] [ devguide-identities] descreve quais informações de registro de identidade de dispositivos do hub cada IoT armazena e como você pode acessar e modificá-lo.
- [Controlar o acesso ao IoT Hub] [ devguide-security] descreve o modelo de segurança usado para conceder acesso a funcionalidade de IoT Hub para ambos os dispositivos e componentes de nuvem. O artigo inclui informações sobre como usar tokens e certificados x. 509, os detalhes e as permissões que você pode conceder.
- [Usar Gêmeos de dispositivo para sincronizar o estado e configurações] [ devguide-device-twins] descreve o conceito de *twin do dispositivo* e a funcionalidade que ele expõe como sincronizar um dispositivo com seu twin. O artigo inclui informações sobre os dados armazenados em um twin do dispositivo.
- [Invocar um método direto em um dispositivo] [ devguide-directmethods] descreve o ciclo de vida de um método direto, informações sobre como chamar métodos em um dispositivo de seu aplicativo de back-end e manipular o método direto no seu dispositivo.
- [Agendar trabalhos em vários dispositivos] [ devguide-jobs] descreve como você pode agendar trabalhos em vários dispositivos. O artigo descreve como enviar trabalhos que executam tarefas como executando um método direto, atualizando um devcie usando um twin devcie. Ele também descreve como consultar o status de um trabalho.
- [Referência - pontos de extremidade do IoT Hub] [ devguide-endpoints] descreve os vários pontos de extremidade que cada hub IoT expõe para operações de gerenciamento e de tempo de execução. O artigo também descreve como você pode usar um gateway de campo para habilitar alguns dispositivos para se conectar a seus pontos de extremidade do IoT Hub.
- [Referência - linguagem de consulta para gêmeos, métodos e trabalhos] [ devguide-query] descreve essa linguagem de consulta que permite recuperar informações de seu hub sobre suas Gêmeos de dispositivo e trabalhos.
- [Referência - cotas e a otimização de] [ devguide-quotas] resume as cotas definidas no serviço de IoT Hub e o comportamento de otimização que você pode esperar ver quando você excedeu uma cota.
- [Referência - dispositivo e serviço SDKs] [ devguide-sdks] listas SDK do qual você pode usar desenvolvem aplicativos de dispositivo e serviço que interagem com seu hub IoT. O artigo inclui links para documentação de API online.
- [Referência - suporte IoT Hub MQTT] [ devguide-mqtt] fornece informações detalhadas sobre como o Hub de IoT suporta o protocolo MQTT. O artigo descreve o suporte para o protocolo MQTT interno para SDK do e fornece informações sobre como usar o protocolo MQTT diretamente.
- [Glossário do] [ devguide-glossary] uma lista de termos relacionados IoT Hub comuns.



[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md

