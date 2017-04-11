<properties
 pageTitle="Manutenção de previsão pré-configurado solução | Microsoft Azure"
 description="Uma descrição da solução Azure IoT previsão manutenção pré-configurado."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
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

# <a name="predictive-maintenance-preconfigured-solution-overview"></a>Visão geral da solução previsão manutenção pré-configurado

A solução de *manutenção de previsão* pré-configurado é uma das [soluções pré-configuradas] [ lnk_preconfigured_solutions] lançado como parte do [Microsoft Azure IoT Suite][lnk_iot_suite]. Essa solução integra coleção de telemetria do dispositivo em tempo real com um modelo de previsão criado usando o [Aprendizado de máquina do Azure][lnk_machine_learning].


Com o pacote do Azure IoT corporativo pode rapidamente e facilmente conectar-se a monitorar ativos e analisar dados em tempo real. A solução de manutenção de previsão pré-configurado leva dados e usa rich painéis e visualizações para proporcionar a empresas com nova inteligência que pode gerar eficiência e aprimorar fluxos de receita.

## <a name="the-scenario"></a>O cenário

Fabrikam é uma companhia aérea regional que focaliza a experiência do cliente excelentes preços competitiva. Uma causa atrasos de voos é problemas de manutenção e a manutenção do mecanismo de aviões é especialmente desafiador. Falha de mecanismo durante voo deve ser evitar a todo custo, portanto Fabrikam analisa seus mecanismos regularmente e adere a um programa de manutenção agendada. No entanto, mecanismos de aviões não sempre papel na história a mesma. Alguns manutenção desnecessária é executada no mecanismos. Mais importante, surgir problemas que podem Terra um avião até que manutenção seja executada. Isso causa atrasos caros, especialmente se um avião esteja em um local onde os técnicos direita ou peças não estão disponíveis.

Os mecanismos de aviões da Fabrikam são instrumentos com sensores que monitoram as condições de mecanismo durante voo. Fabrikam use Azure IoT Suite para coletar os dados de sensor coletados durante o voo. Após acumulando anos de mecanismo operacional e dados de falha, cientistas de dados da Fabrikam têm modelados uma maneira de prever o restante útil vida (regra) de um mecanismo de aviões. O que eles identificados é uma correlação entre os dados de quatro dos sensores mecanismo com o uso de mecanismo que leva à falha eventual. Enquanto Fabrikam continua a executar inspeções regulares para garantir a segurança, ele agora pode usar os modelos para calcular a regra para cada mecanismo após cada voos usando a telemetria coletados de mecanismos de durante o voo. Fabrikam agora pode prever futuros pontos de falha e plano para manutenção e reparar com antecedência.

> [AZURE.NOTE] O modelo de solução usa dados de uso de mecanismo real.

Por prever o ponto quando a manutenção é necessária, Fabrikam pode otimizar suas operações para reduzir custos. Coordenadores de manutenção trabalham com agendadores para planejar manutenção coincidindo com um avião parar em um local específico e garantindo tempo suficiente para o aviões estará fora serviço sem causar interrupção de cronograma. Fabrikam pode agendar técnicos adequadamente; garantindo aviões são atendidos com eficiência sem tempo de espera. Gerentes de controle de estoque recebem planos de manutenção, para que possam otimizar seu processo de pedidos e inventário de peças de substituição. Tudo isso permite Fabrikam para minimizar o tempo de Terra de aviões e reduzir os custos operacionais ao garantir a segurança de passageiros e equipe.

Para entender como [Azure IoT Suite] [ lnk_iot_suite] fornece os recursos que os clientes precisam realizar o potencial de manutenção de previsão, revise esta [infographic][lnk_infographic].

## <a name="how-the-predictive-maintenance-solution-is-built"></a>Como a solução de manutenção de previsão é criada

A solução utiliza um modelo de aprendizado de máquina do Azure existente disponível como um modelo para mostrar esses recursos trabalhando de telemetria do dispositivo coletada por meio de serviços IoT Suite. A Microsoft criou um [modelo de regressão] [ lnk_regression_model] de um mecanismo de aviões e publicado o modelo concluído, dados<sup>\[1\]</sup>e orientações passo a passo sobre como usar o modelo.

A solução de manutenção de previsão pré-configurado Azure IoT usa o modelo de regressão criado deste modelo; é implantado em sua assinatura do Azure e exposto por meio de uma API gerada automaticamente. A solução inclui um subconjunto dos dados de teste que representa 4 (do total de 100) mecanismos e 4 (de 21 total) fluxos de dados de sensor que fornecem um resultado preciso do modelo de treinamento.

*\[1\] r. Saxena e K. Goebel (2008). "Conjunto de dados do turbofan mecanismo degradação simulação", o repositório de dados de Prognostics do NASA omes (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), centro de pesquisa de omes NASA, campo, CA Moffett*

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como o Azure IoT permite cenários de manutenção de previsão, leia [capturar valor da Internet das coisas][lnk_capture_value].

Faça uma [explicação passo a passo] [ lnk-predictive-walkthrough] da previsão manutenção pré-configurado solução.

[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF

Você também pode explorar alguns dos outros recursos e recursos das soluções Suite IoT pré-configurado:

- [Perguntas frequentes para IoT pacote][lnk-faq]
- [Segurança IoT desde o início][lnk-security-groundup]

[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
