<properties
 pageTitle="Explicação passo a passo de manutenção de previsão | Microsoft Azure"
 description="Instruções sobre a manutenção de previsão do Azure IoT pré-configurado solução."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="araguila"/>

# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>Explicação passo a passo de solução de manutenção de previsão pré-configurado

## <a name="introduction"></a>Introdução

A solução de manutenção de previsão pré-configurado IoT Suite é uma solução de ponta a ponta para um cenário de negócios que prevê o ponto quando falha é provável que ocorra. Você pode usar esta solução pré-configurada proativamente para atividades como otimizar manutenção. A solução combina chaves serviços de pacote de IoT do Azure, incluindo um [Aprendizado de máquina do Azure] [ lnk_machine_learning] espaço de trabalho. Este espaço de trabalho contém experiências, com base em um conjunto de dados de amostra público, prever o restante útil vida (regra) de um mecanismo de aviões. A solução implementa totalmente o cenário de negócios IoT como ponto de partida para planejar e implementar uma solução que atenda às suas próprias necessidades de negócios específicas.

## <a name="logical-architecture"></a>Arquitetura lógica

O diagrama a seguir descreve os componentes lógicos da solução pré-configurado:

![][img-architecture]

Os itens azuis são serviços do Azure que são provisionados no local que você seleciona quando você provisionar a solução pré-configurado. Você pode provisionar a solução pré-configurada na região Leste EUA, Norte da Europa ou na Ásia Oriental em.

Alguns recursos não estão disponíveis nas regiões onde você provisionar a solução pré-configurado. Os itens laranjas no diagrama representam os serviços do Azure provisionados na mais próximo disponível região (Centro Sul dos EUA, Europa Ocidental ou sudeste asiático) fornecida a região selecionada.

O item verde é um dispositivo simulado que representa um mecanismo de aviões. Você pode aprender mais sobre esses dispositivos simulados na seção a seguir.

Os itens cinzas representam componentes que implementam recursos de *Administração do dispositivo* . A versão atual da solução previsão manutenção pré-configurado não provisionar esses recursos. Para saber mais sobre a administração de dispositivo, consulte o [monitoramento solução pré-configurada remoto][lnk-remote-monitoring].

## <a name="simulated-devices"></a>Dispositivos simulados

Na solução pré-configurado, um dispositivo simulado representa um mecanismo de aviões. A solução está provisionada com dois mecanismos de mapeiam para um único aviões. Cada mecanismo emite quatro tipos de telemetria: Sensor 9, 11 de Sensor, Sensor 14 e 15 de Sensor fornecem os dados necessários para o modelo de aprendizado de máquina calcular o restante útil vida (regra) para o mecanismo. Cada dispositivo simulado envia as seguintes mensagens de telemetria IoT Hub:

*Contagem de ciclo*. Um ciclo representa um voo concluído de comprimento variável entre 2-10 horas na qual os dados de telemetria são capturados cada meia hora durante o voo.

*Telemetria*. Há quatro sensores que representam atributos de mecanismo. Os sensores forma genérica são rotulados Sensor 9, 11 de Sensor, Sensor 14 e 15 de Sensor. Esses 4 sensores representam telemetria suficiente para obter resultados úteis do modelo de aprendizado de máquina regra. Este modelo é criado a partir de um conjunto de dados público que inclua dados mecanismo real. Para obter mais informações sobre como o modelo foi criado no conjunto de dados original, consulte o [Modelo de previsão manutenção do Cortana inteligência Galeria][lnk-cortana-analytics].

Os dispositivos simulados podem manipular os seguintes comandos enviados de um hub IoT:

| Comando | Descrição |
|---------|-------------|
| StartTelemetry | Controla o estado da simulação.<br/>Inicia o dispositivo de envio de telemetria     |
| StopTelemetry  | Controla o estado da simulação.<br/>Parar o dispositivo de envio de telemetria |

Hub de IoT fornece confirmação de comando do dispositivo.

## <a name="azure-stream-analytics-job"></a>Trabalho de análise de fluxo Azure

**Trabalho: telemetria** funciona com o fluxo de telemetria do dispositivo recebidos usando duas instruções. O primeiro seleciona telemetria todos os dos dispositivos e envia esses dados blob storage de onde ele é visualizado no web app. A segunda instrução calcula valores sensor médio ao longo de uma janela deslizante dois minutos e envia esses dados por meio do hub de evento a um **processador de evento**.

## <a name="event-processor"></a>Processador de evento

O **processador de evento** usa os valores de sensor média de um ciclo concluído. Ele a passa esses valores para uma API que expõe o aprendizado de máquina treinamento modelo para calcular a regra para um mecanismo.

## <a name="azure-machine-learning"></a>Aprendizado de máquina Azure

Para obter mais informações sobre como o modelo foi criado no conjunto de dados original, consulte o [Modelo de previsão manutenção do Cortana inteligência Galeria][lnk-cortana-analytics].

## <a name="lets-start-walking"></a>Vamos começar a movimentação

Esta seção o orienta os componentes da solução, descreve o caso de uso pretendido e fornece exemplos.

### <a name="predictive-maintenance-dashboard"></a>Painel de manutenção de previsão

Esta página no aplicativo da web utiliza PowerBI JavaScript controles (consulte o [repositório de visuais PowerBI][lnk-powerbi]) para visualizar:

- Os dados de saída dos trabalhos de análise de fluxo no armazenamento de blob.
- A contagem de regra e ciclo por mecanismo de aviões.

### <a name="observing-the-behavior-of-the-cloud-solution"></a>Observando o comportamento da solução da nuvem

No portal do Azure, navegue até o grupo de recursos com o nome de solução que você escolheu para exibir seus recursos provisionados.

![][img-resource-group]

Quando você provisionar a solução pré-configurada, você recebe um email com um link para o espaço de trabalho de aprendizado de máquina. Você também pode navegar para o espaço de trabalho de aprendizado de máquina do [azureiotsuite.com] [ lnk-azureiotsuite] página de sua solução provisionada quando ela está no estado **pronto** .

![][img-machine-learning]

No portal de solução, você pode ver que a amostra é provisionada com quatro dispositivos simulados para representar dois aviões com dois mecanismos por aviões, cada um com quatro sensores. Quando você primeiro navegue até o portal de solução, a simulação é interrompida.

![][img-simulation-stopped]

Clique em **Iniciar simulação** para iniciar a simulação na qual você ver o histórico de sensor, regra, ciclos, e histórico de regra preencher o painel de controle.

![][img-simulation-running]

Quando a regra for menor que 160 (um limite aleatório escolhido para fins de demonstração), o portal de solução exibe um símbolo de aviso ao lado da exibição de regra e realça o mecanismo de aviões em amarelo. Observe como os valores de regra têm uma tendência descendente geral geral, mas tendem a pular para cima e para baixo. Esse comportamento resulta dos comprimentos de ciclo de variáveis e a precisão do modelo.

![][img-simulation-warning]

A simulação completa leva cerca de 35 minutos para ser concluída 148 ciclos. O limite de regra 160 é atendido pela primeira vez em torno de 5 minutos e o limite de visitas de ambos os mecanismos de cerca de 8 minutos.

A simulação percorre o conjunto de dados completo para 148 ciclos e paga em valores de regra e ciclo finais.

Você pode parar a simulação a qualquer momento, mas clicando em **Iniciar simulação** repete a simulação desde o início do conjunto de dados.

## <a name="next-steps"></a>Próximas etapas

Agora que você já tiver executado a solução de manutenção previsão pré-configurado que talvez você queira modificá-lo, consulte [diretrizes sobre como personalizar soluções pré-configuradas][lnk-customize].

A postagem de blog de [Manutenção de previsão IoT Suite - em bastidores -](http://social.technet.microsoft.com/wiki/contents/articles/33527.iot-suite-under-the-hood-predictive-maintenance.aspx) TechNet fornece detalhes adicionais sobre a solução de manutenção de previsão pré-configurado.

Você também pode explorar alguns dos outros recursos e recursos das soluções Suite IoT pré-configurado:

- [Perguntas frequentes para IoT pacote][lnk-faq]
- [Segurança IoT desde o início][lnk-security-groundup]


[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
