<properties
    pageTitle="Visão geral do Microsoft Azure IoT Suite | Microsoft Azure"
    description="Visão geral de como o Azure IoT Suite oferece internet de soluções pré-configuradas coisas para coletar, analisar, armazenar dados, forneça visualizações e integrar com outros sistemas."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/09/2016"
     ms.author="dobett"/>

# <a name="what-is-azure-iot-suite"></a>O que é Azure IoT Suite?

Azure internet dos serviços de coisas (IoT) oferecem uma ampla variedade de recursos. Esses serviços de nível empresarial permitem que você:

- Coletar dados de dispositivos
- Analisar fluxos de dados em movimento
- Armazenar e grandes conjuntos de dados da consulta
- Visualizar dados históricos e em tempo real
- Integração com sistemas de back-office

Para fornecer esses recursos, os pacotes do Azure IoT Suite juntos vários serviços Azure com extensões personalizadas como *soluções pré-configurado*. Essas soluções pré-configuradas são implementações básicas da padrões de solução IoT comuns que ajudam a reduzir o tempo que necessário para oferecer suas soluções IoT. Usando [kits de desenvolvimento de software IoT][lnk-sdks], você pode personalizar e estender essas soluções para atender às suas próprias necessidades. Você também pode usar essas soluções como exemplos ou modelos quando você está desenvolvendo soluções de IoT novo.

O vídeo a seguir fornece uma introdução ao Azure IoT conjunto:

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## <a name="azure-iot-services-in-azure-iot-suite"></a>Serviços de IoT Azure no Azure IoT Suite

As soluções pré-configuradas geralmente usam os seguintes serviços:

- Principais no Azure IoT pacote é o [Azure IoT Hub] [ lnk-iot-hub] serviço. Este serviço oferece as capacidades de mensagens do dispositivo na nuvem e nuvem para dispositivo e atua como gateway para a nuvem e os outros serviços de pacote de IoT chaves. O serviço permite que você receber mensagens de seus dispositivos em escala e enviar comandos para seus dispositivos.

- [A análise de fluxo Azure] [ lnk-asa] fornece a análise de dados em movimento. Pacote de IoT utiliza este serviço para processar telemetria recebida, execute a agregação e detectar eventos. As soluções pré-configuradas também usam a análise de fluxo para processar mensagens informativas que contêm dados como respostas de metadados ou comando de dispositivos. As soluções usam a análise de fluxo para processar as mensagens de seus dispositivos e entregar as mensagens para outros serviços.

- [Armazenamento do Azure] [ lnk-azure-storage] e [Azure DocumentDB] [ lnk-document-db] oferecer os recursos de armazenamento de dados. As soluções predefinidas usam armazenamento de blob para armazenar telemetria e disponibilizá-lo para análise. As soluções usam DocumentDB para armazenar metadados de dispositivo e habilitar os recursos de gerenciamento de dispositivo das soluções.

- [Azure Web Apps] [ lnk-web-apps] e o [Microsoft Power BI] [ lnk-power-bi] fornecem os recursos de visualização de dados. A flexibilidade do Power BI permite criar seus próprios painéis interativos que usam dados IoT Suite rapidamente.

Para obter uma visão geral da arquitetura de uma solução IoT típica, consulte [Microsoft Azure e Internet das coisas (IoT)][iot-suite-what-is-azure-iot].

## <a name="preconfigured-solutions"></a>Soluções pré-configuradas

IoT Suite inclui soluções pré-configuradas que permitem para começar rapidamente com e explorar os cenários IoT comuns, como *monitoramento remoto* e *manutenção previsão*, que possibilita Azure IoT Suite. Você pode implantar essas soluções à sua assinatura do Azure e execute um cenário de IoT completo e de ponta a ponta.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral do que pode fazer IoT Suite e quais são seus componentes principais, você pode saber mais sobre as soluções pré-configuradas no IoT Suite, consulte [quais são os IoT Azure soluções pré-configuradas?][lnk-what-are-preconfig]

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
