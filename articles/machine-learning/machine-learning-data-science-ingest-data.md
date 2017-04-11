<properties 
    pageTitle="Carregar dados em ambientes de armazenamento para análise | Microsoft Azure" 
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
    ms.date="09/19/2016" 
    ms.author="bradsev" />

# <a name="load-data-into-storage-environments-for-analytics"></a>Carregar dados em ambientes de armazenamento para análise

O processo de ciência de dados de equipe requer que dados ser incluídos ou carregados em uma variedade de ambientes de armazenamento diferente seja processada ou analisados na forma mais apropriada em cada fase do processo. Destinos de dados usados para processamento incluem armazenamento de Blob Azure, bancos de dados do SQL Azure, SQL Server na máquina virtual do Azure HDInsight (Hadoop) e de aprendizado de máquina do Azure. 

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

Este **menu** fornece links para tópicos que descrevem como a inclusão de dados para esses ambientes de destino onde os dados são armazenados e processados.

Técnicos e necessidades de negócios, bem como o local inicial, formatar e tamanho dos seus dados determinará os ambientes de destino no qual os dados precisam ser incluídos para atingir as metas de sua análise. Não é incomum para um cenário exigir dados para serem movidos entre vários ambientes alcançar a variedade de tarefas necessárias para construir um modelo de previsão. Essa sequência de tarefas pode incluir, por exemplo, exploração de dados, pré-processamento, limpeza, amostragem para baixo e treinamento do modelo.
