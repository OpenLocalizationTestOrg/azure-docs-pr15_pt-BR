<properties 
    pageTitle="Guia estratégico de solução de análise de telemetria veículo | Microsoft Azure" 
    description="Usar os recursos de inteligência de Cortana para obter ideias de previsão e em tempo real sobre integridade de um veículo e conduzir hábitos." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="bradsev" />


# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Guia estratégico de solução de análise de telemetria veículo

Esse links de **menu** para os capítulos neste manual. 

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Visão geral
Computadores super foram movidos fora do laboratório e estacionadas em nossa garagem! Estas automóveis ponta contêm uma infinidade de sensores, dando-lhes a capacidade de controlar e monitorar milhões de eventos de cada segundo. Esperamos que por 2020, na maioria destas carros será tiver sido conectada à internet. Imagine tocando em integridade dos dados para fornecer o melhor em segurança de classe, a confiabilidade e a experiência de carro! A Microsoft disponibilizou isso sonho uma realidade com inteligência de Cortana.

Inteligência de Cortana da Microsoft é totalmente gerenciados grandes de dados e pacote de análises avançadas que permite transformar seus dados em ação inteligente. Queremos apresentá-lo para o modelo de solução Cortana inteligência veículo telemetria Analytics. Esta solução demonstra como revendedores de carro, fabricantes de automóvel e companhias podem usar os recursos de inteligência de Cortana para obter em tempo real e hábitos de obtenção de informações de previsão sobre integridade de um veículo e conduzir. 

A solução é implementada como um [padrão de arquitetura de lambda](https://en.wikipedia.org/wiki/Lambda_architecture) mostrando completo potenciais da plataforma inteligência de Cortana para em tempo real e processamento em lotes. A solução: 

- Fornece um simulador Telematics de veículo
- utiliza Hubs de evento para a inclusão de milhões de eventos de telemetria do veículo simulado no Azure 
- usa a análise de fluxo para obter ideias em tempo real sobre integridade de um veículo
-  persistir os dados para o armazenamento de longo prazo para análise de lote mais elaborado. 
- tira proveito de aprendizado de máquina para detecção de anomalias em tempo real e em lote de processamento para obter ideias de previsão.
- utiliza HDInsight para transformar dados em escala e fábrica de dados para lidar com coordenação, agendamento, gerenciamento de recursos e monitoramento do pipeline de processamento em lotes 
- oferece um painel rich a esta solução para dados em tempo real e visualizações de análise de previsão usando o Power BI

## <a name="architecture"></a>Arquitetura

![](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)
*Figura 1 – arquitetura de solução de análise de telemetria do veículo*

Essa solução inclui os seguintes **componentes de inteligência de Cortana** e apresenta sua integração de ponta a ponta


- **Hubs de evento** para a inclusão de milhões de eventos de telemetria do veículo em Azure.
- **Análise de fluxo** para obter ideias em tempo real sobre integridade de um veículo e persistir esses dados para o armazenamento de longo prazo para análise de lote mais elaborado.
- **Aprendizado de máquina** para detecção de anomalias em tempo real e processamento em lotes para obter ideias de previsão.
- **HDInsight** é utilizado para transformar dados em escala
- **Data Factory** manipula coordenação, cronogramas, gerenciamento de recursos e monitoramento do pipeline de processamento em lotes.
- **Power BI** dá esta solução um painel rich dados em tempo real e visualizações de análise de previsão.

Essa solução acessa duas diferentes **fontes de dados**: 

- **Simulado sinais de veículo e diagnósticos**: um simulador de telematics veículo emite informações de diagnóstico e de sinais que correspondem ao estado de veículo e o determinante padrão em um determinado ponto no tempo. 
- **Catálogo de veículo**: um conjunto de dados de referência que contém um VIN para mapeamento de modelo.
