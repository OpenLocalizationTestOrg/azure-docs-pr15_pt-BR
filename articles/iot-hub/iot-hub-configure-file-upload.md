<properties
     pageTitle="Usar o portal do Azure para configurar o carregamento de arquivo | Microsoft Azure"
     description="Uma visão geral de como configurar o carregamento de arquivo usando o portal do Azure"
     services="iot-hub"
     documentationCenter=""
     authors="dominicbetts"
     manager="timlt"
     editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/30/2016"
     ms.author="dobett"/>

# <a name="configure-file-uploads-using-the-azure-portal"></a>Configurar carregamentos de arquivo usando o portal do Azure

## <a name="file-upload"></a>Carregamento de arquivo

Usar a [funcionalidade no IoT Hub de carregamento de arquivo][lnk-upload], você deve primeiro associar uma conta de armazenamento do Azure seu hub. Selecione as configurações de **carregamento de arquivo** para exibir uma lista das propriedades de carregamento de arquivo para o hub de IoT que está sendo modificado.

![][13]

**Contêiner de armazenamento**: Use o portal do Azure para selecionar um contêiner de blob em uma conta de armazenamento do Azure em sua assinatura atual do Azure para associar seu IoT Hub. Se necessário, você pode criar uma conta de armazenamento do Azure no contêiner de blade e blob de **contas de armazenamento** no blade **contêineres** . Hub de IoT gera automaticamente URIs de SAS com permissões de gravação para este contêiner de blob para dispositivos usar quando eles carreguem arquivos.

![][14]

**Receber notificações para arquivos carregados**: habilitar ou desabilitar notificações de carregamento de arquivo via o botão de alternância.

**SAS TTL**: esta configuração é o tempo de vida dos URIs SAS retornados ao dispositivo por IoT Hub. Por padrão, definido para uma hora, mas pode ser personalizado para outros valores usando o controle deslizante.

**Notificação de arquivo configurações padrão TTL**: O tempo de vida de um arquivo de notificação de carregamento antes que ele expirou. Por padrão, definido para um dia, mas pode ser personalizado para outros valores usando o controle deslizante.

**Contagem de entrega máximo de notificação de arquivo**: O número de vezes que o Hub de IoT tenta entregar um arquivo notificação de carregamento. Por padrão, definido como 10, mas pode ser personalizado para outros valores usando o controle deslizante.

![][15]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os recursos de carregamento de arquivo do IoT Hub, consulte [carregar arquivos em um dispositivo] [ lnk-upload] no guia do desenvolvedor.

Siga estes links para saber mais sobre como gerenciar Azure IoT Hub:

- [Em massa gerenciar IoT dispositivos][lnk-bulk]
- [Métricas de uso][lnk-metrics]
- [Operações de monitoramento][lnk-monitor]

Para explorar os recursos do IoT Hub, consulte:

- [Guia Desenvolvedor][lnk-devguide]
- [Simular um dispositivo com o SDK do Gateway IoT][lnk-gateway]
- [Proteger sua solução IoT desde o início para cima][lnk-securing]


  [13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
  [14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
  [15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md