
<properties
   pageTitle="Orientação Azure | padrões & práticas | Microsoft Azure"
   description="Práticas recomendadas e orientações para o Azure"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="christb"/>

# <a name="azure-guidance"></a>Orientação do Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

A equipe de padrões e práticas Microsoft faz parte da equipe comunicado de cliente do Azure. Nosso objetivo é ajudar os desenvolvedores, arquitetos e profissionais de TI tenha êxito na plataforma do Microsoft Azure. Podemos desenvolver orientações que mostra as práticas recomendadas para a criação de soluções de nuvem no Azure.

## <a name="checklists"></a>Listas de verificação

Essas listas são uma referência rápida para revisar os aspectos fundamentais de disponibilidade e escalabilidade. 

- [Lista de verificação de disponibilidade][AvailabilityChecklist] 

    Um resumo das práticas recomendadas para garantir a disponibilidade.

- [Lista de verificação de escalabilidade][ScalabilityChecklist]

    Um resumo das práticas recomendadas para projetar e implementar serviços escaláveis e manipulação de gerenciamento de dados.

## <a name="best-practices-articles"></a>Artigos de práticas recomendadas

Estes artigos fornecem uma discussão aprofundada dos conceitos importantes comumente associadas ao nuvem computação. 

- [Design de API][APIDesign] 

    Uma discussão de questões a serem consideradas ao criar uma web API.

- [Implementação de API][APIImplementation] 

    Um conjunto de práticas recomendadas para implementar e uma web API de publicação.

- [Diretrizes de segurança de API](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 

    Uma discussão de problemas de autenticação e autorização (por exemplo, tipos de token, protocolos de autorização, fluxos de autorização e atenuação de ameaças).

- [Orientação de AutoScaling][AutoscalingGuidance] 

    Um resumo das considerações para dimensionar soluções sem a necessidade de intervenção manual.

- [Orientação de trabalhos de plano de fundo][BackgroundJobsGuidance] 

    Uma descrição das opções disponíveis e as práticas recomendadas para implementar tarefas que devem ser executadas em segundo plano, independentemente de qualquer primeiro plano ou operações interativas.

- [Conteúdo orientações de rede de entrega (CDN)][CDNGuidance] 

    Orientação geral e prática recomendada para usar a CDN para minimizar a carga em seus aplicativos e maximizar a disponibilidade e desempenho.

- [Diretrizes de cache][CachingGuidance] 

    Um resumo de como usar o armazenamento em cache para melhorar o desempenho e escalabilidade de um sistema.

- [Orientação de particionamento de dados][DataPartitioningGuidance]

    Estratégias que você pode usar para dados de partição para melhorar a escalabilidade, reduza disputa e otimizar o desempenho.

- [Orientação de monitoramento e diagnóstico][MonitoringandDiagnosticsGuidance] 

    Orientação sobre controle como os usuários utilizam o seu sistema, rastrear a utilização de recursos e geralmente monitoram a integridade e o desempenho do sistema.

- [Convenções de nomenclatura recomendadas][naming-conventions] 

    Convenções de nomenclatura recomendadas para recursos Azure.

- [Repetir a orientação geral][RetryGeneralGuidance] 

    Discussão dos conceitos gerais para lidar com falhas temporárias.

- [Repetir orientações específicas de serviço][RetryServiceSpecificGuidance]

    Um resumo dos recursos de Repetir para muitos dos serviços Azure, incluindo informações para ajudá-lo a usar, adaptar ou estender o mecanismo de repetição para esse serviço.

## <a name="scenario-guides"></a>Guias de cenário

- [Elasticsearch em execução no Azure][elasticsearch] 
    
    Elasticsearch é um mecanismo de pesquisa de código-fonte aberto altamente escaláveis e o banco de dados. Ele é adequado para situações que exigem a detecção de informações mantidas em grandes conjuntos de dados e análise rápida. Este guia examina alguns principais aspectos a considerar ao criar um cluster de Elasticsearch.

- [Gerenciamento de identidades para vários locatários aplicativos][identity-multitenant] 
    
    Multilocação é uma arquitetura onde vários locatários compartilham um aplicativo, mas são isolados uns dos outros. Este guia mostra como gerenciar identidades de usuário em um aplicativo de vários locatários, usando o [Active Directory do Azure] [ AzureAD] tratar entrar e autenticação.
    
- [Desenvolver soluções de grande volume](https://msdn.microsoft.com/library/dn749874.aspx)

    Este guia examina o uso de HDInsight para cenários como exploração iterativo, como um depósito de dados, para processos ETL e integração com sistemas de BI existentes. Ele também inclui orientação sobre noções básicas sobre os conceitos de dados grandes, planejamento e soluções de grande volume de projetar e implementar essas soluções.
    
## <a name="patterns"></a>Padrões

- [Padrões de Design de nuvem: Orientações sobre a arquitetura dirigidas para aplicativos em nuvem](https://msdn.microsoft.com/library/dn568099.aspx)

    Padrões de Design de nuvem é uma biblioteca de padrões de design e tópicos relacionados a orientação. Ele explica o benefício da aplicação de padrões mostrando como cada parte pode se encaixa no arquiteturas de aplicativos de nuvem.
    
- [Otimização de desempenho para aplicativos em nuvem](https://github.com/mspnp/performance-optimization)

    Este guia é uma exploração dos anticomuns padrões de que impedem aplicativos de dimensionamento sob carga. Ele inclui exemplos demonstram oito antie padrões de uma [Introdução de análise de desempenho](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md) e um guia para a [avaliação de desempenho em relação às principais métricas](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md).

## <a name="reference-architectures"></a>Arquiteturas de referência

Nossas arquiteturas de referência são organizadas por cenário.
Cada arquitetura individual oferece práticas recomendadas e etapas dirigidas e um componente executável que incorpora as recomendações.

A biblioteca atual de arquiteturas de referência está disponível em [http://aka.ms/architecture](http://aka.ms/architecture).

## <a name="resiliency-guidance"></a>Orientação de resiliência

Estes tópicos descrevem como projetar aplicativos que são flexíveis a falha em um ambiente de nuvem distribuído.   

- [Visão geral de resiliência][ResiliencyOvervew]

     Como criar aplicativos na plataforma Windows Azure que podem recuperar de falhas e continuar a funcionar. Descreve uma abordagem estruturada para atingir resiliência, do projeto à implementação, implantação e operações.

- [Lista de verificação de resiliência][resiliency-checklist]

    Uma lista de verificação de recomendações que ajudará você a planejar uma variedade de modos de falha que podem ocorrer.

- [Análise de modo de falha][resiliency-fma] 

    Análise de modo de falha (FMA) é um processo para a criação de resiliência em um sistema, identificando possíveis pontos de falha. Como ponto de partida para o seu processo FMA, este artigo contém um catálogo de possíveis modos de falha e seus reduções. 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md

