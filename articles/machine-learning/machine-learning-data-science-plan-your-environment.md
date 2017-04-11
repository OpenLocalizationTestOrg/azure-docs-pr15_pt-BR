<properties
    pageTitle="Como identificar cenários e planejar avançada de processamento de dados de análise | Microsoft Azure"
    description="Planejar para análise avançada, considerando uma série de perguntas principais."
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
    ms.date="09/19/2016"
    ms.author="bradsev" />


# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Como identificar cenários e planejar de processamento de dados de análise avançada

Quais recursos você planeja incluir ao configurar um ambiente para fazer a análise avançada de processamento em um conjunto de dados? Este artigo sugere uma série de perguntas que ajudam a identificar as tarefas e recursos relevantes seu cenário. A ordem das etapas de alto nível para análise de previsão é descrita na [o que é o processo de ciência de dados (TDSP) da equipe?](data-science-process-overview.md). Cada uma dessas etapas exigem recursos específicos para as tarefas relevantes para seu cenário em particular. As principais perguntas para identificar seu cenário interessam logística de dados, características, a qualidade dos conjuntos de dados e as ferramentas e linguagens que preferir fazer a análise.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Perguntas logísticas: movimentação e locais de dados
As perguntas logísticas diz respeito a localização da **fonte de dados**, o **destino de destino** no Azure e requisitos para mover os dados, incluindo o cronograma, quantidade e recursos envolvidos. Os dados talvez precise ser movido várias vezes durante o processo de análise. Um cenário comum é mover dados locais para algum tipo de armazenamento no Azure e, em seguida, em Studio de aprendizado de máquina.

1. **Qual é sua fonte de dados?** É local ou na nuvem? Por exemplo:
    - Os dados estão disponíveis publicamente em endereços HTTP.
    - Os dados residem em um local de arquivo local/rede.
    - Os dados estiverem em um banco de dados do SQL Server.
    - Os dados são armazenados em um contêiner de armazenamento do Azure

2. **O que é o destino do Azure?** Onde ele precisa estar para processamento ou modelagem? Por exemplo:
    - Armazenamento de blob do Microsoft Azure
    - Bancos de dados do SQL Azure
    - SQL Server na máquina virtual Azure
    - HDInsight (Hadoop no Azure) ou tabelas de seção
    - Aprendizado de máquina Azure
    - Montagem em Azure discos virtuais.

3. **Como você vai para mover os dados?** Os procedimentos e os recursos disponíveis para inclusão ou carregar dados em uma variedade de armazenamento diferentes e ambientes de processamento são descritos nos tópicos a seguir.

    -  [Carregar dados em ambientes de armazenamento para análise](machine-learning-data-science-ingest-data.md)
    -  [Importar seus dados de treinamento para o Azure Studio de aprendizado de máquina de várias fontes de dados](machine-learning-data-science-import-data.md).

4. **Os dados precisam ser movidos regularmente ou modificadas durante a migração?** Considere usar fábrica de dados do Azure (AAD) quando dados que precisa ser migrada continuamente, especialmente se um cenário de híbrido que acessa os dois no local e recursos de nuvem é envolvidos, ou quando os dados é transação precisam ser modificada ou tem lógica de negócios adicionada a ele durante sendo migrado. Para obter mais informações, consulte [mover dados do servidor SQL no local para o SQL Azure com fábrica de dados do Azure](machine-learning-data-science-move-sql-azure-adf.md)

5. **A quantidade de dados é para serem movidos para Azure?** Conjuntos de dados que são muito grandes podem exceder a capacidade de armazenamento de determinados ambientes. Por exemplo, consulte a discussão dos limites de tamanho para Studio de aprendizado de máquina na próxima seção. Nesses casos uma amostra dos dados pode ser usada durante a análise. Para obter detalhes sobre como amostra para baixo um conjunto de dados em vários ambientes do Azure, consulte [dados de exemplo do processo de ciência de dados de equipe](machine-learning-data-science-sample-data.md).


## <a name="data-characteristics-questions-type-format-and-size"></a>Perguntas de características de dados: tipo, o formato e o tamanho
Essas perguntas são fundamentais para seu armazenamento de planejamento e ambientes, cada um deles são apropriadas para vários tipos de dados e cada um deles têm certas restrições de processamento.

1. **Quais são os tipos de dados?** Por exemplo:
    - Numéricos
    - Categorias
    - Cadeias de caracteres
    - Binário

2. **Como seus dados são formatados?** Por exemplo:
    - Separado por vírgula (CSV) ou arquivos simples separados por tabulações (TSV)
    - Compactado ou descompactado
    - Blobs Azure
    - Seção de Hadoop tabelas
    - Tabelas do SQL Server

2. **Como grandes são seus dados?**
    - Pequeno: menor que 2GB
    - Médio: Maiores que 2GB e menos de 10GB
    - Grande: Maiores que 10GB

Veja o ambiente do Azure Studio de aprendizado de máquina por exemplo:

- Para obter uma lista dos formatos de dados e tipos aceitos pelo Studio de aprendizado de máquina do Azure, consulte a seção [tipos de dados e formatos de dados com suporte](machine-learning-data-science-import-data.md#data-formats-and-data-types-supported) .
- Para obter informações sobre as limitações de dados para Studio de aprendizado de máquina do Azure, consulte o **como grande conjunto de dados possível para meu módulos?** seção da [importação e exportação de dados para aprendizado de máquina](machine-learning-faq.md#machine-learning-studio-questions)

Para obter informações sobre as limitações de outros serviços Azure usadas no processo de análise, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Perguntas de qualidade de dados: pré-processamento e exploração

1. **O que você sabe sobre seus dados?** Explore dados quando você precisa obter um compreender suas características básicas. Quais padrões ou tendências-apresentações, o que é destaques tem ou quantos valores ausentes. Esta etapa é importante para determinar a extensão da pré-processamento necessário, para formular hipóteses que poderiam sugerir os recursos mais apropriados ou tipo de análise e formular planos de conjunto de dados adicionais. Estatística descritiva de cálculo e a plotagem visualizações são técnicas úteis para inspeção de dados. Para obter detalhes sobre como explorar um conjunto de dados em vários ambientes do Azure, consulte [explorar dados no processo de ciência de dados de equipe](machine-learning-data-science-explore-data.md).

2. **Os dados requer pré-processamento ou limpeza?**
Pré-processamento e limpeza de dados são tarefas importantes que normalmente devem ser realizadas antes de conjunto de dados pode ser usado efetivamente para aprendizado de máquina. Dados processados costuma ruído e não confiáveis e podem estar ausente valores. Usar esses dados para modelagem pode produzir resultados incorretos. Para obter uma descrição, consulte [tarefas para preparar os dados para máquina aprimorado de aprendizagem](machine-learning-data-science-prepare-data.md).

## <a name="tools-and-languages-questions"></a>Ferramentas e linguagens perguntas
Há muitas opções aqui dependendo de quais idiomas e ambientes de desenvolvimento ou ferramentas que você precisa ou está usando mais conformable.

1. **Quais idiomas você prefere usar para análise?**  
    - R
    - Python
    - SQL

2. **Quais ferramentas você deve usar para análise de dados?**
    - [Microsoft Azure Powershell](powershell-install-configure.md) - uma linguagem de script usada para administrar seus recursos Azure em uma linguagem de script.
    - [Studio de aprendizado de máquina Azure](machine-learning-what-is-ml-studio/)
    - [Análise de rotação](http://www.revolutionanalytics.com/revolution-r-open)
    - [RStudio](http://www.rstudio.com)
    - [Ferramentas de Python para Visual Studio](http://microsoft.github.io/PTVS/)
    - [Anaconda](https://www.continuum.io/why-anaconda)
    - [Blocos de anotações de Jupyter](http://jupyter.org/)
    - [Microsoft Power BI](http://powerbi.microsoft.com)


## <a name="identify-your-advanced-analytics-scenario"></a>Identifique seu cenário de análise avançada
Quando você atendeu as perguntas na seção anterior, você estará pronto para determinar qual cenário de melhor se ajuste a seu caso. Os cenários de exemplo são organizados em [cenários para análises avançadas de aprendizado de máquina do Azure](machine-learning-data-science-plan-sample-scenarios.md).
