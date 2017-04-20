<properties
    pageTitle="Qual é o aprendizado de máquina no Azure? | Microsoft Azure"
    description="Explica os conceitos básicos de aprendizado de máquina na nuvem, descreve o que você pode usá-lo para e define os termos de aprendizado de máquina."
    keywords="qual é o aprendizado de máquina, máquina aprendizagem termos, previsão, o que há de análise de previsão, colocar em operação"
    services="machine-learning"
    documentationCenter=""
    authors="cjgronlund"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="cgronlun;tedway;olgali"/>


# <a name="introduction-to-machine-learning-in-the-cloud"></a>Introdução à aprendizagem na nuvem do computador

## <a name="what-is-machine-learning"></a>Qual é o aprendizado de máquina?

Aprendizado de máquina é uma técnica de ciência de dados que ajuda os computadores a aprender com os dados existentes para previsão de tendências, resultados e comportamentos futuros.  

Essas previsões ou previsões de aprendizado de máquina podem tornar os aplicativos e dispositivos mais inteligente. Quando você faz compras online, aprendizado de máquina ajuda recomendável outros produtos que podem desejar com base no que você comprou. Ao passar o seu cartão de crédito, aprendizado de máquina compara a transação para um banco de dados de transações e ajuda a detectar fraude. Quando seu aspirador robô vacuums uma sala, aprendizado de máquina ajuda-lo a decidir se o trabalho está concluído.

Para obter uma visão resumida, tente os série de vídeos [Ciência de dados para iniciantes](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md). Sem usar jargão ou matemática, ciência de dados para iniciantes introduz o aprendizado de máquina e o orienta através de um modelo de previsão de indisponibilidade simples.

## <a name="what-is-machine-learning-in-the-microsoft-azure-cloud"></a>Qual é o aprendizado de máquina na nuvem Microsoft Azure?

Aprendizado de máquina Azure é um serviço de poderosa análise de previsão baseada na nuvem que torna possível rapidamente, criar e implantar modelos de previsão como soluções de análise. Quando você usa o Azure nuvem experiências de aprendizado de máquina de executar ou criar soluções, você não precisa adquirir hardware caro ou infraestrutura.

![Qual é o aprendizado de máquina? Fluxo de trabalho básico tornem operacional a análise de previsão no aprendizado de máquina do Azure.](./media/machine-learning-what-is-machine-learning/machine-learning-service-parts-and-workflow.png)

Azure aprendizado de máquina não só oferece ferramentas para análise de previsão do modelo, mas também oferece um serviço totalmente gerenciado, que você pode usar para implantar seus modelos de previsão como serviços web pronto para consumir. Aprendizado de máquina Azure fornece ferramentas para criar soluções de análise de previsão concluída na nuvem: criar, testar, colocar em operação e gerenciar modelos de previsão rapidamente.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="what-is-predictive-analytics"></a>Qual é a análise de previsão?

Análise de previsão usa vários fórmulas de matemática chamadas algoritmos analisar dados históricos ou atuais e padrões ou tendências para previsão eventos futuros.

Aprendizado de máquina Azure é uma maneira particularmente eficiente para fazer a análise de previsão: você pode trabalhar a partir de uma biblioteca de prontos para uso dos algoritmos, use os algoritmos para criar modelos em um PC conectados à internet e implanta a solução de previsão rapidamente. Para obter resultados rapidamente, comece com soluções na [Galeria de inteligência de Cortana](http://gallery.cortanaintelligence.com/)e exemplos de pronto para ser usado.

## <a name="build-complete-machine-learning-solutions-in-the-cloud"></a>Construir soluções na nuvem de aprendizado de máquina completa

Aprendizado de máquina Azure tem tudo o que você precisa criar soluções de análise de previsão na nuvem, de uma biblioteca de algoritmo grandes, para um studio para a criação de modelos, para uma maneira fácil de implantar o seu modelo como um serviço web.

### <a name="machine-learning-studio-create-predictive-models"></a>Máquina Studio de aprendizagem: Criar modelos de previsão

Na [Máquina Learning Studio](machine-learning-what-is-ml-studio.md), você pode criar rapidamente arrastando, soltando e conectando módulos de modelos de previsão. É fácil fazer experiências com diferentes combinações e [Experimente-o gratuitamente](https://studio.azureml.net/?selectAccess=true&o=2).

* Na [Galeria de inteligência de Cortana](machine-learning-gallery-how-to-use-contribute-publish.md), tente soluções analytics criadas por outras pessoas ou contribuir seus próprios. Poste perguntas ou comentários sobre experiências na comunidade ou compartilhar links para experiências através de redes sociais, como LinkedIn e Twitter.

  ![Tente experiências previsão ou contribuir seus próprios na Galeria de inteligência de Cortana do Windows Azure](./media/machine-learning-what-is-machine-learning/machine-learning-cortana-intelligence-gallery.png)

* Use uma grande biblioteca de [algoritmos de aprendizado de máquina e módulos](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) na máquina Learning Studio rapidamente seus modelos de previsão. Escolha do melhor classe algoritmos de empresas da Microsoft como Xbox e Bing, R e Python pacotes e experiências de amostra. Estenda os módulos de Studio com seus próprios scripts personalizados de [R](machine-learning-r-quickstart.md) e [Python](machine-learning-execute-python-scripts.md) .

  ![Qual é a análise de previsão: exemplo de um experimento de análise de previsão no Azure máquina Learning Studio](./media/machine-learning-what-is-machine-learning/azure-machine-learning-studio-predictive-score-experiment.png)


### <a name="operationalize-predictive-analytics-solutions-purchase-web-services-or-publish-your-own"></a>Colocar em operação soluções de análise de previsão: serviços da web de compra ou publicar seus próprios

* Web pronto para consumir serviços de compra do [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning), como recomendações, análise de texto e detecção de anomalias.

* Colocar em operação seus modelos de análise de previsão:
    * [Implantar serviços da web](machine-learning-publish-a-machine-learning-web-service.md)
    * [Treinar e treinar novamente os modelos através de APIs](machine-learning-retrain-models-programmatically.md)
    * [Gerenciar pontos de extremidade de serviço web](machine-learning-create-endpoint.md)
    * [Escala de um serviço web](machine-learning-scaling-webservice.md)
    * [Use serviços da web](machine-learning-consume-web-services.md)

## <a name="key-machine-learning-terms-and-concepts"></a>Máquina principal termos e conceitos de aprendizagem

Termos de aprendizado de máquina podem ser confusos. Aqui estão as definições de termos principais para ajudá-lo. Use os comentários seguindo para conte para nós sobre qualquer outro termo definidos que você gostaria definidos.

### <a name="data-exploration-descriptive-analytics-and-predictive-analytics"></a>Exploração de dados, análise descritivo e análise de previsão

**Exploração de dados** é o processo de coleta de informações sobre um conjunto de dados grande e geralmente não estruturada para localizar as características para análises focalizadas. **Mineração de dados** se refere a exploração de dados automatizada.

**Análise descritivo** é o processo de análise de um conjunto de dados para resumir o que aconteceu. A maior parte da análise de negócios, como relatórios de vendas, métricas de web e análise de redes sociais - são descritivo.

**Análise de previsão** é o processo de criação de modelos de dados de históricos ou atuais para o futuros resultados de previsão.


### <a name="supervised-and-unsupervised-learning"></a>Aprendizado supervisionado e sem supervisão
 Algoritmos **de aprendizado Supervised** são treinados com dados rotulados - em outras palavras, dados composto por exemplos das respostas queria. Por exemplo, um modelo que identifica o uso de cartão de crédito fraudulentos seria ser treinado de um conjunto de dados com pontos de dados com rótulo de conhecidos encargos fraudulentos e válidos. A maioria dos aprendizado de máquina seja supervisionado.

 **Aprendizado Unsupervised** é usada em dados com sem rótulos e o objetivo é encontrar relações nos dados. Por exemplo, você talvez queira localizar agrupamentos da demografia do cliente com hábitos de compra semelhantes.

### <a name="model-training-and-evaluation"></a>Avaliação e treinamento de modelo
Um modelo de aprendizado de máquina é uma abstração da pergunta que você está tentando atender ou o resultado que você deseja prever. Modelos são treinados e avaliados de dados existentes.

#### <a name="training-data"></a>Dados de treinamento
Quando você treinar um modelo de dados, use um conjunto de dados conhecido e fazer ajustes para o modelo com base nas características dos dados para obter a resposta mais precisa. No aprendizado de máquina do Azure, um modelo é construído a partir de um módulo de algoritmo que processa os dados de treinamento e módulos funcionais, por exemplo, um módulo de pontuação.

Aprendizado supervisionado, se você estiver treinamento um modelo de detecção de fraude, você usa um conjunto de transações que são rotulados como fraudulentos ou válido. Você dividir aleatoriamente o conjunto de dados e use parte para treinar o modelo e a parte para testar ou avaliar o modelo.

#### <a name="evaluation-data"></a>Dados de avaliação
Depois que você tiver um modelo treinado, avalie o modelo usando os dados de teste restantes. Você usar dados que você já conhece as saídas, para que você possa determinar se o seu modelo prevê com precisão.

## <a name="other-common-machine-learning-terms"></a>Outros termos comuns de aprendizado de máquina

* **algoritmo**: um conjunto independente de regras usado para resolver problemas por meio de processamento de dados, matemática ou raciocínio automatizado.
* **detecção de anomalias**: um modelo eventos incomuns ou valores de sinalizadores e ajuda você a descobrir problemas. Por exemplo, a detecção de fraude de cartão de crédito procura compras incomuns.
* **dados categóricos**: dados que é organizado por categorias e que podem ser divididos em grupos. Por exemplo, um conjunto de dados categóricos automóveis pode especificar ano, tornar, modelo e preço.
* **classificação**: um modelo para organizar os pontos de dados em categorias com base em um conjunto de dados para a qual categoria agrupamentos já conhecidos.
* **engenharia do recurso**: O processo de extração ou selecionando recursos relacionados a um conjunto de dados para aprimorar o conjunto de dados e melhorar resultados. Por exemplo, dados de passagem aérea poderiam ser aprimorados pelo dias da semana e feriados. Consulte [engenharia no aprendizado de máquina do Windows Azure e seleção de recursos](machine-learning-feature-selection-and-engineering.md).
* **módulo**: uma parte funcional em um modelo de máquina Learning Studio, como o módulo de inserir dados que permite a inserção e edição de conjuntos de dados pequenos. Um algoritmo também é um tipo de módulo no Studio de aprendizado de máquina.
* **modelo**: um modelo de aprendizado supervisionadas é o produto de uma máquina experimento composto de dados de treinamento, um módulo de algoritmo e módulos funcionais, como um módulo do modelo de pontuação de aprendizagem.
* **dados numéricos**: dados que tem significado como medidas (contínua de dados) ou contagens (discretos dados). Também conhecido como *dados quantitativos*.
* **partição**: O método pelo qual divide dados em amostras. Para obter mais informações, consulte [exemplo e partição](https://msdn.microsoft.com/library/azure/dn905960.aspx) .
* **previsão**: uma previsão é uma previsão de um valor ou valores de um modelo de aprendizado de máquina. Você também poderá ver o termo "prevista score." No entanto, as pontuações previstas não são a saída final de um modelo. Uma avaliação do modelo segue a pontuação.
* **regressão**: um modelo para prever um valor com base em variáveis independentes, como prever o preço de um carro com base em sua ano e tornar.
* **pontuação**: um valor previsto gerado a partir de um treinados modelo de classificação ou de regressão, utilizando o [módulo de modelo de pontuação](https://msdn.microsoft.com/library/azure/dn905995.aspx) no Studio de aprendizado de máquina. Modelos de classificação também retornará uma pontuação para a probabilidade de valor previsto. Depois de ter gerado as pontuações de um modelo, você pode avaliar a precisão do modelo usando o [módulo de avaliar o modelo](https://msdn.microsoft.com/library/azure/dn905915.aspx).
* **amostra**: uma parte de um conjunto de dados deve ser um representante do todo. Amostras podem ser selecionadas aleatoriamente ou com base em recursos específicos do conjunto de dados.



## <a name="next-steps"></a>Próximas etapas
Você pode aprender os fundamentos de análise de previsão e aprendizado de máquina usando um [tutorial passo a passo](machine-learning-create-experiment.md) e com [base em amostras](machine-learning-sample-experiments.md).  


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/
