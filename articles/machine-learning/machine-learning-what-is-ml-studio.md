<properties 
    pageTitle="O que é Azure Studio de aprendizado de máquina? | Microsoft Azure"
    description="Visão geral do Azure ML Studio, uma ferramenta de arrastar e soltar para criar rapidamente os modelos de uma biblioteca de prontos para uso de algoritmos e módulos."
    keywords="aprendizado, ml azure, studio ml de máquina Azure"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/09/2016"
    ms.author="garye"/>

# <a name="what-is-azure-machine-learning-studio"></a>O que é Azure Studio de aprendizado de máquina?

Studio de aprendizado de máquina do Microsoft Azure é uma ferramenta de colaboração, arrastar e soltar que você pode usar para criar, testar e implantar soluções de análise de previsão em seus dados. Studio de aprendizado de máquina publica modelos como serviços da web que podem ser facilmente consumidos por aplicativos personalizados ou ferramentas de BI como o Excel.

Studio de aprendizado de máquina é onde se encontram ciência de dados, a análise de previsão, recursos de nuvem e seus dados.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>O espaço de trabalho interativo Studio de aprendizado de máquina

Para desenvolver um modelo de análise de previsão, você normalmente usa dados de uma ou mais fontes, transformação e analisar os dados por meio de várias funções estatísticas e manipulação de dados e gera um conjunto de resultados. Desenvolver um modelo como este é um processo iterativo. Como modificar as várias funções e seus parâmetros, seus resultados convergirem até ficar satisfeito que você tenha um modelo de treinamento e eficaz.

**Studio de aprendizado de máquina do Azure** fornece um espaço de trabalho interativo e visual facilmente construir, testar e iteramos sobre um modelo de análise de previsão. Você arrastar-e-soltar ***conjuntos de dados*** e análise ***módulos*** para uma tela interativo, conectá-los para formar uma ***experimentar***, que você executa no Studio de aprendizado de máquina. Para iteramos sobre o design de modelo, você editar o experimento, salve uma cópia se desejado e executá-la novamente. Quando você estiver pronto, você pode converter seu ***treinamento experimentar*** um ***experimento previsão***e depois publicá-lo como um ***serviço web*** para que seu modelo pode ser acessado por outras pessoas.

>[AZURE.TIP] Para baixar e imprimir um diagrama que oferece uma visão geral dos recursos do Studio de aprendizado de máquina, consulte o [diagrama de visão geral dos recursos do Azure Studio de aprendizado de máquina](machine-learning-studio-overview-diagram.md).

Não há nenhuma necessidade de programação, basta visualmente conectando módulos para construir seu modelo de análise de previsão e conjuntos de dados.

![Diagrama de ML Studio Azure: Crie experiências, ler dados de várias fontes, gravar dados marcados, escrever modelos.][ml-studio-overview]

## <a name="get-started-with-machine-learning-studio"></a>Introdução ao Studio de aprendizado de máquina

Quando você insere primeiro [Studio de aprendizado de máquina](https://studio.azureml.net) , você verá a página **Home** . A partir daqui, você pode exibir documentação, vídeos, webinars e localizar outros recursos valiosos.

Existem três guias na parte superior: **Home** (onde você começar), **Studio**e **Galeria**.

### <a name="studio"></a>Studio

Clique na guia **Studio** e você será solicitado a entrar usando sua conta da Microsoft ou sua conta corporativa ou escolar. Uma vez conectado, você verá as seguintes guias à esquerda:

- **Projetos** - coleções de experiências, conjuntos de dados, notebooks e outros recursos que representa um único projeto
- **Experiências** - experiências que foram criados, executar e salvas como rascunhos
- **Serviços WEB** - serviços da Web que você tenha implantado a partir de suas experiências
- **Blocos de anotações** - Jupyter blocos de anotações que você criou
- **Conjuntos de dados** - conjuntos de dados que você carregou em Studio
- **Modelos de treinamento** - modelos que você treinamento em experiências e salvou no Studio
- **Configurações** - uma coleção de configurações que você pode usar para configurar sua conta e recursos.

### <a name="gallery"></a>Galeria

Clique na guia **Galeria** e você será levado para a Galeria de inteligência de Cortana. A Galeria é o lugar onde uma comunidade de cientistas de dados e os desenvolvedores pode compartilhar as soluções criadas usando componentes do pacote inteligência Cortana.

Para saber mais sobre a Galeria, consulte [compartilhar e descobrir soluções na Galeria de inteligência Cortana](machine-learning-gallery-how-to-use-contribute-publish.md).

## <a name="components-of-an-experiment"></a>Componentes de um experimento

Um experimento consiste em conjuntos de dados que fornecem dados a analíticos módulos, que você conecta para construir um modelo de análise de previsão. Especificamente, um experimento válido possui estas características:

- O experimento tem pelo menos um conjunto de dados e um módulo
- Conjuntos de dados podem estar conectados apenas a módulos
- Módulos podem estar conectados a outros módulos ou conjuntos de dados
- Todas as portas de entrada para módulos devem ter alguns conexão para o fluxo de dados
- Todos os necessários parâmetros para cada módulo devem ser definidos

Você pode criar um experimento do zero, ou você pode usar um experimento de amostra existente como um modelo. Para obter mais informações, consulte [experiências de amostra de uso para criar novas experiências](machine-learning-sample-experiments.md).

Para obter um exemplo de criação de um experimento simple, consulte [criar um experimento simple no Azure Studio de aprendizado de máquina](machine-learning-create-experiment.md).

Para obter uma explicação mais completa de criação de uma solução de análise de previsão, consulte [desenvolver uma solução de previsão com aprendizado de máquina do Azure](machine-learning-walkthrough-develop-predictive-solution.md).

### <a name="datasets"></a>Conjuntos de dados

Um conjunto de dados é dados que foi carregados Studio de aprendizado de máquina para que ele pode ser usado no processo de modelagem. Um número de conjuntos de dados de exemplo está incluído Studio de aprendizado de máquina para você experimentar e você pode carregar conjuntos de dados mais conforme necessário. Aqui estão alguns exemplos de conjuntos de dados incluídos:

- **Dados MPG para os diversos automóveis** - milhas por galão (MPG) valores para os automóveis identificados por número de cilindros, potência, etc.
- **Dados de câncer de mama** - dados de diagnóstico de câncer de mama.
- **Dados da floresta acionado** - tamanhos de fire floresta em Portugal nordeste.

Conforme você cria um experimento que você pode escolher na lista de conjuntos de dados disponíveis para a esquerda da tela.

Para obter uma lista de conjuntos de dados de exemplo incluídos no Studio de aprendizado de máquina, consulte [usar os conjuntos de dados de exemplo no Azure Studio de aprendizado de máquina](machine-learning-use-sample-datasets.md).

### <a name="modules"></a>Módulos

Um módulo é um algoritmo que você pode executar em seus dados. Studio de aprendizado de máquina tem um número de módulos variando de funções de ingresso dados treinamento, pontuação e processos de validação. Aqui estão alguns exemplos de módulos incluídos:

- [Converter em ARFF] [ convert-to-arff] -converte um conjunto de dados do .NET serializado para o formato de arquivo de relação de atributo (ARFF).
- [Calcular estatísticas de ensino fundamental] [ elementary-statistics] -calcula as estatísticas de ensino fundamental como média, desvio padrão, etc.
- [Regressão linear] [ linear-regression] -cria um modelo online gradiente baseado em descendente regressão linear.
- [Modelo de pontuação] [ score-model] -pontuações um modelo de classificação ou regressão treinamento.

Conforme você cria um experimento você pode escolher entre a lista de módulos disponíveis para a esquerda da tela.  

Um módulo pode ter um conjunto de parâmetros que você pode usar para configurar algoritmos internos do módulo. Quando você selecionar um módulo na tela de desenho, parâmetros do módulo são exibidos no painel **Propriedades** à direita da tela. Você pode modificar os parâmetros nesse painel para ajustar seu modelo.

Para alguns ajuda navegar por meio da biblioteca grande de algoritmos de aprendizado de máquina disponíveis, consulte [como escolher algoritmos para aprendizado de máquina do Microsoft Azure](machine-learning-algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Implantando um serviço da web de análise de previsão

Depois que seu modelo de análise de previsão está pronto, você pode implantá-lo como um serviço da web diretamente do Studio de aprendizado de máquina. Para obter mais detalhes sobre esse processo, consulte [implantar um serviço da web de aprendizado de máquina do Azure](machine-learning-publish-a-machine-learning-web-service.md).

[ml-studio-overview]:./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
