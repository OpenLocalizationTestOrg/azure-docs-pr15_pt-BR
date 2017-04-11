<properties
 pageTitle="Métricas de diagnóstico IoT Hub"
 description="Uma visão geral de métricas de Azure IoT Hub, permitindo que os usuários avaliar a integridade geral dos seus recursos"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="nberdy"/>

# <a name="introduction-to-diagnostic-metrics"></a>Introdução às métricas de diagnósticos

Métricas de diagnósticos oferecem melhores dados sobre o estado dos recursos do Azure em sua assinatura do Azure. Métricas permitem que você avaliar a integridade geral do serviço e os dispositivos conectados a ele. Estatísticas de voltado para o usuário são importantes, pois elas ajudam a ver o que está acontecendo com seus problemas de causas raiz IoT hub e ajuda sem precisar contatar o suporte Azure.

Você pode habilitar métricas de diagnósticos do portal do Azure.

## <a name="how-to-enable-diagnostic-metrics"></a>Como habilitar métricas de diagnósticos

1. Crie um hub IoT. Você pode encontrar instruções sobre como criar um hub IoT na [Introdução] [ lnk-get-started] guia.

2. Abra a lâmina de seu hub IoT. A partir daí, clique em **Diagnóstico**.

    ![][1]

3. Configure seu diagnósticos definindo o status como **em** e selecionando uma conta de armazenamento do Azure para armazenar os dados de diagnóstico. Marque **métricas**e pressione em **Salvar**. Observe que a conta de armazenamento do Azure deve ser criada antecedência e que cobrado separadamente para armazenamento. Você também pode optar por enviar seus dados de diagnóstico para um ponto de extremidade de Hubs de evento.

    ![][2]

4. Depois que você configurou o diagnóstico, volte para a lâmina de hub IoT **Visão geral** . Informações de métricas são preenchidas na seção **monitoramento** da lâmina. Clicando no gráfico abre o painel de métricas onde você pode exibir um resumo das informações de métricas para seu hub IoT e editar a seleção de métricas exibidas no gráfico. Você também pode configurar alertas com base em valores métricas.

    ![][3]

## <a name="metrics-and-how-to-use-them"></a>Métricas e como usá-las

Hub de IoT fornece várias métricas para dar uma visão geral da integridade de seu hub e o número total de dispositivos conectados a ele. Você pode combinar informações de várias métricas para pintar uma imagem maior do estado do hub IoT. A tabela a seguir descreve as métricas que cada hub IoT rastreia e como cada métrica se relaciona ao status geral do hub IoT.

| Métrica | Descrição métrica | A métrica para que serve |
| ---- | ---- | ---- |
| d2c.telemetry.ingress.allProtocol | A contagem de mensagens enviadas em todos os dispositivos | Envia dados de visão geral na mensagem |
| d2c.telemetry.ingress.Success | A contagem de todas as mensagens bem-sucedido no hub | Visão geral de ingresso de mensagem bem-sucedida no hub |
| c2d.Commands.egress.Complete.Success | A contagem de todas as mensagens de comando concluída por dispositivo receptor em todos os dispositivos | Junto com as métricas em abandonar e rejeitar, oferece uma visão geral de taxa de sucesso do comando C2D geral |
| c2d.Commands.egress.Abandon.Success | A contagem de todas as mensagens com êxito abandonados pelo dispositivo receptor em todos os dispositivos | Destaca possíveis problemas se mensagens estão recebendo abandonadas com mais frequência do que o esperado |
| c2d.Commands.egress.Reject.Success | A contagem de todas as mensagens com êxito rejeitada pelo dispositivo receptor em todos os dispositivos | Destaca possíveis problemas se mensagens são obtendo rejeitadas com mais frequência do que o esperado |
| devices.totalDevices | A média, mínimo e máximo do número de dispositivos registrados ao hub IoT | O número de dispositivos registrados ao hub |
| devices.connectedDevices.allProtocol | A média, mínimo e máximo do número de dispositivos conectados simultâneos | Visão geral do número de dispositivos conectados ao hub |

## <a name="next-steps"></a>Próximas etapas

Agora que você já viu uma visão geral de métricas de diagnósticos, siga este link para saber mais sobre como gerenciar Azure IoT Hub:

- [Operações de monitoramento][lnk-monitor]

Para explorar os recursos do IoT Hub, consulte:

- [Guia Desenvolvedor][lnk-devguide]
- [Simular um dispositivo com o SDK do Gateway IoT][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
