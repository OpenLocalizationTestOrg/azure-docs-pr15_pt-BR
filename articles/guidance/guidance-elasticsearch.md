
<properties
   pageTitle="Elasticsearch orientação Azure | Microsoft Azure"
   description="Elasticsearch orientação Azure."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>

# <a name="elasticsearch-on-azure-guidance"></a>Elasticsearch orientação Azure 

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Elasticsearch é um mecanismo de pesquisa de código-fonte aberto altamente escaláveis e o banco de dados. É útil em situações que exigem a detecção de informações mantidas em grandes conjuntos de dados e análise rápida. Este guia examina alguns principais aspectos a considerar ao criar um cluster de Elasticsearch, com foco em formas para otimizar e testar a configuração.

> [AZURE.NOTE]Este guia pressupõe familiaridade básica com Elasticsearch. Para obter informações mais detalhadas, visite o [site Elasticsearch](https://www.elastic.co/products/elasticsearch). 

- **[Elasticsearch em execução no Azure][]** fornece uma breve introdução a estrutura geral do Elasticsearch e descreve como implementar um cluster de Elasticsearch uso do Azure. 
- **[Ajuste de desempenho de inclusão de dados para Elasticsearch no Azure][]** descreve a implantação de um cluster de Elasticsearch e fornece uma análise detalhada das várias opções de configuração a serem consideradas quando você espera uma alta taxa de inclusão de dados.
- **[Agregação de dados de ajuste e desempenho de consulta para Elasticsearch no Azure][]** fornece uma análise detalhada das opções a considerar ao decidir como otimizar seu sistema de pesquisa e consulta de desempenho.
- **[Configurando resiliência e recuperação em Elasticsearch no Azure][]** descreve alguns recursos importantes de um cluster de Elasticsearch que podem ajudar a minimizar as chances de perda de dados e tempos de recuperação de dados estendido.
- **[Criando um ambiente de teste de desempenho para Elasticsearch no Azure][]** descreve como configurar um ambiente para testar a inclusão de dados de desempenho e cargas de trabalho de consulta em um cluster de Elasticsearch. 
- **[Implementar um JMeter testar o plano para Elasticsearch][]** resume implementados usando planos de teste de JMeter junto com o código de Java incorporada como um teste de JUnit para realizar tarefas como carregar dados em cluster Elasticsearch de testes de desempenho em execução.
- **[Implantando um classificador JMeter JUnit para testar o desempenho de Elasticsearch][]** descreve como criar e usar uma amostra de JUnit que pode gerar e carregar dados em um cluster de Elasticsearch. Isso fornece uma abordagem altamente flexível para teste de carga que pode gerar grandes quantidades de dados de teste sem dependendo de arquivos de dados externos. 
- **[Execução dos testes de resiliência Elasticsearch automatizados][]** resume como executar os testes de resiliência usados nesta série. 
- **[Execução dos testes de desempenho Elasticsearch automatizados][]** resume como executar os testes de desempenho usados nesta série.


[Elasticsearch em execução no Azure]: guidance-elasticsearch-running-on-azure.md
[Ajuste de desempenho de inclusão de dados para Elasticsearch no Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Criando um ambiente de teste para Elasticsearch no Azure de desempenho]: guidance-elasticsearch-creating-performance-testing-environment.md
[Implementando um plano de teste de JMeter para Elasticsearch]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[Implantando um classificador JMeter JUnit para testar o desempenho de Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Ajuste de desempenho de consulta para Elasticsearch no Azure e de agregação de dados]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Configurando resiliência e recuperação em Elasticsearch no Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Execução dos testes de resiliência Elasticsearch automatizada]: guidance-elasticsearch-running-automated-resilience-tests.md
[Execução dos testes de desempenho Elasticsearch automatizada]: guidance-elasticsearch-running-automated-performance-tests.md
