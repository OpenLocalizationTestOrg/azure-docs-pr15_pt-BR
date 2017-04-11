<properties
    pageTitle="Mover de dados e armazenamento de blob do Microsoft Azure | Microsoft Azure"
    description="Mover de dados e armazenamento de blob do Microsoft Azure"
    services="machine-learning,storage"
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
    ms.date="09/14/2016"
    ms.author="bradsev;sachouks" />

# <a name="move-data-to-and-from-azure-blob-storage"></a>Mover de dados e armazenamento de blob do Microsoft Azure

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

Orientação sobre tecnologias usadas para mover dados para e/ou do armazenamento de Blob do Azure estão vinculados aqui:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]
 
Que método é melhor para você depende do seu cenário. O artigo de [cenários para análises avançadas de aprendizado de máquina do Azure](machine-learning-data-science-plan-sample-scenarios.md) o ajudará a determinar os recursos que necessários para uma variedade de fluxos de trabalho de ciência de dados usada no processo de análise avançada.

> [AZURE.NOTE] Para obter uma introdução completa ao Azure blob storage, consulte [Noções básicas de Blob do Azure](../storage/storage-dotnet-how-to-use-blobs.md) e [Azure Blob](https://msdn.microsoft.com/library/azure/dd179376.aspx)serviço.

Como alternativa, você pode usar [Fábrica de dados do Azure](https://azure.microsoft.com/services/data-factory/) para: 

- criar e agendar um pipeline que downloads de dados do armazenamento de blob do Microsoft Azure, 
- passe para um serviço de web de aprendizado de máquina do Azure publicado, 
- receber os resultados de análise de previsão, e 
- Carregue os resultados ao armazenamento. 

Para obter mais informações, consulte [criar canais de previsão usando fábrica de dados do Azure e aprendizado de máquina do Azure](../data-factory/data-factory-azure-ml-batch-execution-activity.md).

## <a name="prerequisites"></a>Pré-requisitos

Este documento pressupõe que você tenha uma assinatura do Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de carregar/baixar dados, você deve saber a sua chave de nome e uma conta da conta armazenamento do Azure.

- Para configurar uma assinatura do Azure, consulte [avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).
- Para obter instruções sobre como criar uma conta de armazenamento e para obter informações importantes e conta, consulte [contas de armazenamento do Azure sobre](../storage/storage-create-storage-account.md).
