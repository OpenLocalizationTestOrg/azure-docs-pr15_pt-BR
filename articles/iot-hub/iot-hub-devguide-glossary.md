<properties
 pageTitle="Guia do desenvolvedor - Glossário | Microsoft Azure"
 description="Um glossário de termos comuns relacionados à IoT Hub"
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

# <a name="glossary-of-iot-hub-terms"></a>Glossário de termos de IoT Hub

Este artigo lista alguns dos termos comuns associados IoT Hub.

## <a name="advanced-message-queueing-protocol-amqp"></a>Protocolo de fila de mensagens avançado (AMQP)

[AMQP](https://www.amqp.org/) é um dos protocolos mensagens que IoT Hub suporta para se comunicar com dispositivos. Para obter mais informações sobre os protocolos mensagens que IoT Hub dá suporte, consulte [Enviar e receber mensagens com IoT Hub](iot-hub-devguide-messaging.md).

## <a name="cloud-to-device-c2d"></a>Nuvem para dispositivo (C2D)

Geralmente usado para se referir a mensagens enviadas de IoT Hub em um dispositivo conectado. Muitas vezes, essas mensagens são comandos que instruem o dispositivo para executar alguma ação. Para obter mais informações, consulte [Enviar e receber mensagens com IoT Hub](iot-hub-devguide-messaging.md).

## <a name="condition"></a>Condição

Se refere a informações de estado de dispositivo, como o método de conectividade atualmente em uso, conforme relatado por um aplicativo do dispositivo. Dispositivos também poderá reportar seus recursos. Você pode consultar condição e capacidade usando o twin do dispositivo.

## <a name="data-point-message"></a>Mensagem de ponto de dados

Um ponto de dados é uma mensagem de nuvem para dispositivo que contém os dados de telemetria como velocidade do vento ou temperatura.

## <a name="desired-properties"></a>Propriedades desejadas

No contexto de Gêmeos de dispositivo, propriedades desejadas são usadas em conjunto com *relatados propriedades* para sincronizar a configuração de dispositivo ou condição. Propriedades desejadas só podem ser definidas pelo aplicativo volta final e são observadas pelo aplicativo do dispositivo. 

## <a name="device-to-cloud-d2c"></a>Dispositivo à nuvem (D2C)

Geralmente usado para se referir a mensagens enviadas de um dispositivo conectado a IoT Hub. Essas mensagens sejam mensagens de [ponto de dados](#data-point-message) ou [interativas](#interactive-message) . Para obter mais informações, consulte [Enviar e receber mensagens com IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device-identity-registry"></a>Registro de identidade de dispositivo

O [registro de identidade do dispositivo](iot-hub-devguide-identity-registry.md) é o componente interno de um hub IoT que armazena informações sobre os dispositivos individuais permitida para se conectar a um hub.

## <a name="device"></a>Dispositivo

No contexto de IoT, um dispositivo normalmente é um dispositivo de computação de autônomo pequena, que pode coletar dados ou outros dispositivos de controle. Por exemplo um dispositivo pode ser, um dispositivo de monitoramento ambiental ou um controlador para os sistemas de regar e ventilação em um efeito.

## <a name="device-twin"></a>Twin de dispositivo

Um [twin dispositivo](iot-hub-devguide-device-twins.md) é uma cópia no IoT Hub das configurações condição e uma configuração de um dispositivo físico. Você pode usar um twin dispositivo para gerenciar a configuração do dispositivo físico.

## <a name="direct-method"></a>Método direto

Um [método direto](iot-hub-devguide-direct-methods.md) é uma maneira de acionar um método para executar em um dispositivo chamando uma API em seu hub IoT.

## <a name="event-hub-compatible-endpoint"></a>Ponto de extremidade de compatível com o Hub de evento

Para ler mensagens de dispositivo à nuvem enviadas para seu hub IoT, você pode se conectar a um ponto de extremidade no seu hub e usar qualquer método compatível com o evento Hub para ler essas mensagens. Métodos de compatível com o Hub de evento incluem usando o evento Hubs SDKs e a análise de fluxo Azure.

## <a name="field-gateway"></a>Gateway de campo

Um gateway de campo permite a conectividade para dispositivos que não consigo conectar diretamente ao IoT Hub e geralmente é implantado localmente com seus dispositivos. Para obter mais informações, consulte [o que é Azure IoT Hub?](iot-hub-what-is-iot-hub.md).

## <a name="job"></a>Trabalho

Back-end sua solução pode usar trabalhos para agendar e controlar atividades em um conjunto de dispositivos registrado com seu hub IoT. As atividades incluem atualizar propriedades do dispositivo twin desejado, atualizando marcas de twin do dispositivo e invocar métodos diretos.

## <a name="protocol-gateway"></a>Gateway de protocolo

Um gateway de protocolo geralmente é implantado na nuvem e fornece serviços de tradução para dispositivos que se conectam a IoT Hub de protocolo. Para obter mais informações, consulte [o que é Azure IoT Hub?](iot-hub-what-is-iot-hub.md).

## <a name="interactive-message"></a>Mensagem interativa

Uma mensagem de interativa é uma mensagem de nuvem para dispositivo que aciona uma ação imediata no back-end do aplicativo. Por exemplo, um dispositivo pode enviar um alarme sobre uma falha que deve ser registrado automaticamente em um sistema CRM.

## <a name="iot-hub"></a>Hub de IoT

Hub de IoT é um serviço de Azure totalmente gerenciado que permite comunicações de seguro e confiável bidirecional entre milhões de dispositivos IoT e um back-end de solução. Para obter mais informações, consulte [o que é Azure IoT Hub?](iot-hub-what-is-iot-hub.md).

## <a name="iot-suite"></a>IoT Suite

Azure Suite IoT juntos pacotes vários serviços Azure com soluções pré-configurado. Essas soluções pré-configuradas permitem que você a começar rapidamente com implementações de ponta a ponta dos cenários comuns de IoT. Para obter mais informações, consulte [o que é Azure IoT Suite?](../iot-suite/iot-suite-overview.md).

## <a name="job"></a>Trabalho

Um [trabalho](iot-hub-devguide-jobs.md) no IoT Hub permite executar operações como um firmware atualização em vários dispositivos conectados a seu hub.

## <a name="mqtt"></a>MQTT

[MQTT](http://mqtt.org/) é um dos protocolos mensagens que IoT Hub suporta para se comunicar com dispositivos. Para obter mais informações sobre os protocolos mensagens que IoT Hub dá suporte, consulte [Enviar e receber mensagens com IoT Hub](iot-hub-devguide-messaging.md).

## <a name="reported-properties"></a>Propriedades de relatados

No contexto de Gêmeos de dispositivo, propriedades relatadas são usadas em conjunto com as *propriedades desejadas* para sincronizar a configuração de dispositivo ou condição. Propriedades relatadas só pode ser definidas pelo aplicativo do dispositivo e podem ser ler e consultadas pelo back-end do aplicativo.

## <a name="tags"></a>Marcas

No contexto de devcie gêmeos, marcas são dispositivo metadados armazenados e recuperados pelo back-end aplicativo na forma de um documento JSON. Marcas não estão visíveis para aplicativos em um dispositivo.

## <a name="system-properties"></a>Propriedades do sistema

No contexto de Gêmeos de dispositivo, propriedades do sistema são somente leitura em incluem informações sobre o uso do dispositivo como o último estado de tempo e conexão de atividade.