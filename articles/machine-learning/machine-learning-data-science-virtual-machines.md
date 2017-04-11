<properties
    pageTitle="Máquinas virtuais de ciência de dados no Azure | Microsoft Azure"
    description="Definir para cima uma máquina Virtual dados ciências"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard" 
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="data-science-virtual-machines-in-azure"></a>Máquinas virtuais de ciência de dados no Azure

São fornecidas instruções aqui que descrevem como configurar uma máquina virtual do Azure e uma VM Azure com o serviço SQL como servidores de bloco de anotações de IPython. O computador virtual Windows está configurado com ferramentas como o bloco de anotações de IPython, Gerenciador de armazenamento do Azure e AzCopy, bem como outros utilitários que são úteis para projetos de ciência de dados de suporte. Azure Storage Explorer e AzCopy, por exemplo, fornecem maneiras convenientes para carregar dados no armazenamento do Azure do computador local ou baixá-la em sua máquina local do armazenamento. 

Esse links de menu para tópicos que descrevem como configurar os vários ambientes de ciência de dados usados pelo [Processo de ciência de dados da equipe (TDSP)](data-science-process-overview.md).

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

Vários tipos de Azure máquinas virtuais podem ser provisionados e configurados para ser usado como parte de um ambiente de ciência de dados baseada em nuvem. A decisão sobre qual tipo de máquina virtual para usar depende do tipo e a quantidade de dados para ser modelados com aprendizado de máquina e o destino para os dados na nuvem. 

* Para obter orientações sobre as perguntas a serem consideradas ao tomar essa decisão, consulte [Planejar seu ambiente do Azure máquina aprendizagem dados ciência](machine-learning-data-science-plan-your-environment.md). 
* Para um catálogo de alguns dos cenários que podem ocorrer quando fazendo análises avançadas, consulte [cenários para o processo de análise avançada e a tecnologia de aprendizado de máquina do Azure](machine-learning-data-science-plan-sample-scenarios.md)

Dois conjuntos de instruções são fornecidos:

* [Configurar uma máquina virtual Azure como um servidor de bloco de anotações de IPython para análise avançada](machine-learning-data-science-setup-virtual-machine.md) mostra como provisionar uma máquina virtual Azure com o bloco de anotações de IPython e outras ferramentas usadas para fazer ciência de dados para situações em que uma forma de armazenamento do Azure diferente de SQL pode ser usada para armazenar os dados.

* [Configurar uma máquina virtual de Azure SQL Server como um servidor de bloco de anotações de IPython para análise avançada](machine-learning-data-science-setup-sql-server-virtual-machine.md) mostra como provisionar uma máquina virtual de servidor do SQL Azure com o bloco de anotações de IPython e outras ferramentas usadas para fazer ciência de dados para situações em que um banco de dados do SQL pode ser usado para armazenar os dados.

Assim que provisionado e configurado, essas máquinas virtuais estão prontas para uso como servidores de bloco de anotações de IPython para a exploração e o processamento de dados e outras tarefas necessárias em conjunto com aprendizado de máquina do Azure e o processo de ciência de dados de equipe (TDSP). As próximas etapas no processo de ciência de dados são mapeadas no [caminho de aprendizagem TDSP](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) e podem incluir etapas que mover dados para o SQL Server ou HDInsight, processam e amostra-lo em preparação para aprender a partir dos dados com aprendizado de máquina do Azure.


> [AZURE.NOTE] Azure máquinas virtuais têm preços como **pagar somente para o qual você usa**. Para garantir que você não seja sendo cobrado quando não usando sua máquina virtual, ele deve estar no estado **parado (Deallocated)** a partir do [Portal clássico do Azure](http://manage.windowsazure.com/). Para instruções passo a passo ou como desalocar você máquina virtual, consulte [desligamento e desalocar máquina virtual quando não estiver em uso](machine-learning-data-science-setup-virtual-machine.md#shutdown)
 
