<properties
    pageTitle="Assinatura do Microsoft Azure e limites de serviço, cotas e restrições"
    description="Fornece uma lista de assinatura comuns do Azure e limites de serviço, cotas e restrições. Isso inclui informações sobre como aumentar limites juntamente com valores máximos."
    services=""
    documentationCenter=""
    authors="rothja"
    manager="jeffreyg"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="btardif"/>

# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Assinatura do Azure e limites de serviço, cotas e restrições

## <a name="overview"></a>Visão geral

Este documento especifica alguns dos limites Microsoft Azure mais comuns. Isso não aborda atualmente todos os serviços do Azure. Ao longo do tempo, esses limites serão expandidos e atualizados para cobrir mais da plataforma.

Visite a [Visão geral de preços do Azure](https://azure.microsoft.com/pricing/) para saber mais sobre preços Azure. Lá, você pode calcular os custos usando a [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/) ou visitando a página de detalhes de preços para um serviço (por exemplo, [VMs do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)).

> [AZURE.NOTE] Se você quiser aumentar o limite acima do **Limite de padrão**, você pode [Abrir uma solicitação de suporte online do cliente sem nenhum custo](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Os limites não podem ser elevados acima do valor **Limite máximo** nas tabelas abaixo. Se não houver nenhuma coluna **Limite máximo** , o recurso especificado não tem limites ajustáveis.

## <a name="limits-and-the-azure-resource-manager"></a>Gerenciador de recursos do Azure e limites

Agora é possível combinar vários recursos Azure para um único grupo de recursos do Azure. Ao usar grupos de recursos, limites que antes eram globais ficam gerenciados em um nível regional com o Gerenciador de recursos do Azure. Para obter mais informações sobre grupos de recursos do Azure, consulte [Visão geral do Gerenciador de recursos do Azure](azure-resource-manager/resource-group-overview.md).

Em limites abaixo, uma nova tabela foi adicionada para refletir as diferenças nos limites ao usar o Gerenciador de recursos do Azure. Por exemplo, há uma tabela de **Limites de assinatura** e uma tabela de **Limites de assinatura - Gerenciador de recursos do Azure** . Quando um limite se aplica a ambos os cenários, ela é mostrada somente na primeira tabela. A menos que indicado, os limites são globais em todas as regiões.

> [AZURE.NOTE] É importante enfatizar que cotas de recursos em grupos de recursos do Azure são por região acessível por sua assinatura e não por assinatura, assim como as cotas de gerenciamento de serviço. Vamos usar cotas de núcleo como exemplo. Se você precisar solicitar um aumento de cota com suporte para cores, você precisa decidir quantos cores que você deseja usar em quais regiões e faça uma solicitação específica para cotas de núcleo de grupo de recursos do Azure para os valores e regiões que desejar. Portanto, se você precisar usar 30 cores na Europa Ocidental para executar o aplicativo lá; Especificamente, você deve solicitar 30 cores na Europa Ocidental. Mas você não terá uma cota de núcleo aumentar em qualquer outra região – somente Oeste Europa terão a cota de 30-core.
<!-- -->
Como resultado, você pode achar útil considerar a decidir o que suas cotas de grupo de recursos do Azure precisam estar para sua carga de trabalho em qualquer uma região e solicitar esse valor em cada região em que você estiver considerando implantação. Consulte [Solucionando problemas de implantação](./resource-manager-common-deployment-errors.md) para obter mais ajuda descobrindo suas cotas atuais para regiões específicas.

## <a name="service-specific-limits"></a>Limites de serviço específico

- [Do Active Directory](#active-directory-limits)
- [Gerenciamento de API](#api-management-limits)
- [Serviço de aplicativo](#app-service-limits)
- [Gateway de aplicativo](#application-gateway-limits)
- [Obtenção de informações de aplicativo](#application-insights-limits)
- [Automação](#automation-limits)
- [Cache relacionada Azure](#azure-redis-cache-limits)
- [RemoteApp Azure](#azure-remoteapp-limits)
- [Backup](#backup-limits)
- [Em lotes](#batch-limits)
- [Serviços do BizTalk](#biztalk-services-limits)
- [CDN](#cdn-limits)
- [Serviços de nuvem](#cloud-services-limits)
- [Fábrica de dados](#data-factory-limits)
- [Lucerne a análise de dados](#data-lake-analytics-limits)
- [DNS](#dns-limits)
- [DocumentDB](#documentdb-limits)
- [Hubs de evento](#event-hubs-limits)
- [Hub de IoT](#iot-hub-limits)
- [Chave cofre](#key-vault-limits)
- [Serviços de mídia](#media-services-limits)
- [Contrato móvel](#mobile-engagement-limits)
- [Serviços móveis](#mobile-services-limits)
- [Monitoramento](#monitoring-limits)
- [Autenticação multifator](#multi-factor-authentication)
- [Rede](#networking-limits)
- [Serviço de notificação do Hub](#notification-hub-service-limits)
- [Obtenção de informações operacionais](#operational-insights-limits)
- [Grupo de recursos](#resource-group-limits)
- [Agendador](#scheduler-limits)
- [Pesquisa](#search-limits)
- [Barramento de serviço](#service-bus-limits)
- [Recuperação de site](#site-recovery-limits)
- [Banco de dados SQL](#sql-database-limits)
- [Armazenamento](#storage-limits)
- [Sistema de StorSimple](#storsimple-system-limits)
- [Análise de fluxo](#stream-analytics-limits)
- [Assinatura](#subscription-limits)
- [Gerenciador de tráfego](#traffic-manager-limits)
- [Máquinas virtuais](#virtual-machines-limits)
- [Conjuntos de escala de máquina virtual](#virtual-machine-scale-sets-limits)


### <a name="subscription-limits"></a>Limites de assinatura
#### <a name="subscription-limits"></a>Limites de assinatura
[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limites de assinatura - Gerenciador de recursos do Azure

Os seguintes limites se aplicam ao usar o Gerenciador de recursos do Azure e grupos de recursos do Azure. Limites que não foram alterados com o Gerenciador de recursos do Azure não estão listados abaixo. Consulte a tabela anterior para esses limites.

Para obter informações sobre tratamento limita a solicitações do Gerenciador de recursos, consulte [solicitações de Gerenciador de recursos de otimização](resource-manager-request-limits.md).

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


### <a name="resource-group-limits"></a>Limites do grupo de recursos

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limites de máquinas virtuais
#### <a name="virtual-machine-limits"></a>Limites de máquina virtual
[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limites de máquinas virtuais - Gerenciador de recursos do Azure

Os seguintes limites se aplicam ao usar o Gerenciador de recursos do Azure e grupos de recursos do Azure. Limites que não foram alterados com o Gerenciador de recursos do Azure não estão listados abaixo. Consulte a tabela anterior para esses limites.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Limites de conjuntos de escala de máquina virtual

[AZURE.INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="networking-limits"></a>Limites de rede

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Limites de rede
[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Limites de Gateway do aplicativo

[AZURE.INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="traffic-manager-limits"></a>Limites do Gerenciador de tráfego

[AZURE.INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>Limites DNS

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>Limites de armazenamento

Para obter detalhes adicionais sobre limites de armazenamento de conta, consulte [metas de desempenho e escalabilidade de armazenamento do Azure](../articles/storage/storage-scalability-targets.md).

#### <a name="storage-service-limits"></a>Limites de serviço de armazenamento

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### <a name="virtual-machine-disk-limits"></a>Limites de disco de máquina virtual

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Consulte [tamanhos de máquina Virtual](../articles/virtual-machines/virtual-machines-linux-sizes.md) para obter detalhes adicionais.

**Contas de armazenamento padrão**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**Contas de armazenamento Premium**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### <a name="storage-resource-provider-limits"></a>Limites de armazenamento do provedor de recursos

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### <a name="cloud-services-limits"></a>Limites de serviços de nuvem

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### <a name="app-service-limits"></a>Limites de serviço de aplicativo
Os seguintes limites de serviço de aplicativo incluem limites para aplicativos Web, aplicativos Mobile, aplicativos de API e lógica de aplicativos.

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Limites do Agendador

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limites de lote

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

###<a name="biztalk-services-limits"></a>Limites de Serviços BizTalk
A tabela a seguir mostra os limites do Azure Biztalk Services.

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### <a name="documentdb-limits"></a>Limites de DocumentDB

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

Cotas listadas com um asterisco (*) [pode ser ajustado contatando o suporte Azure](./documentdb/documentdb-increase-limits.md).

### <a name="mobile-engagement-limits"></a>Limites de contrato de celular

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### <a name="search-limits"></a>Limites de pesquisa

Níveis de preços determinam a capacidade e limites de seu serviço de pesquisa. Níveis incluem:

- Serviço de vários locatário *livre* , compartilhado com outros assinantes Azure, destinados a avaliação e projetos de desenvolvimento pequeno.
- *Básico* fornece recursos de computação dedicados para cargas de trabalho de produção a uma escala menor, com até três réplicas para cargas de trabalho de consulta altamente disponível.
- *Padrão (S1, S2, S3, S3 alta densidade)* é para maiores cargas de trabalho de produção. Vários níveis existirem dentro a camada padrão para que você possa escolher uma configuração de recursos para cenários específicos.

**Limites por assinatura**

[AZURE.INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limites por serviço de pesquisa**

[AZURE.INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Para obter informações mais granulares sobre outros limites, incluindo o tamanho do documento, consultas por segundo, chaves, solicitações e respostas, consulte [limites de serviço na pesquisa do Azure](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limites de serviços de mídia

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>Limites CDN

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limites de serviços de celular

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitoring-limits"></a>Limites de monitoramento

[AZURE.INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Limites de serviço de notificação do Hub

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Limites de Hubs de evento

[AZURE.INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Limites de barramento de serviço

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Hub de IoT limites

[AZURE.INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="data-factory-limits"></a>Limites de fábrica de dados

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Limites de Lucerne a análise de dados
[AZURE.INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="stream-analytics-limits"></a>Limites de análise de fluxo
[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Limites do Active Directory

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


### <a name="azure-remoteapp-limits"></a>Limites de RemoteApp Azure

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### <a name="storsimple-system-limits"></a>Limites do sistema de StorSimple

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### <a name="operational-insights-limits"></a>Limites de ideias operacionais

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Limites de backup

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Limites de recuperação de sites

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Limites de obtenção de informações de aplicativos

[AZURE.INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Limites de gerenciamento de API

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Limites de Cache relacionada Azure

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limites de chave cofre

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Autenticação multifator
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limites de automação
[AZURE.INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>Limites de banco de dados SQL

Para limites de banco de dados SQL, consulte [Limites de recursos de banco de dados do SQL](sql-database/sql-database-resource-limits.md).

## <a name="see-also"></a>Consulte também

[Compreendendo aumentos e limites do Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Máquina virtual e tamanhos de serviço de nuvem do Azure](virtual-machines/virtual-machines-linux-sizes.md)

[Tamanhos para serviços de nuvem](cloud-services/cloud-services-sizes-specs.md)
