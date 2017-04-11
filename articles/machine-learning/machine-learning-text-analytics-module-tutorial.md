<properties
    pageTitle="Criar modelos de análise de texto em Studio de aprendizado de máquina do Azure | Microsoft Azure"
    description="Como criar modelos de análise de texto no Studio de aprendizado de máquina Azure usando módulos para texto pré-processamento, N-gramas ou recurso hash"
    services="machine-learning"
    documentationCenter=""
    authors="rastala"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="roastala" />


#<a name="create-text-analytics-models-in-azure-machine-learning-studio"></a>Criar modelos de análise de texto em Studio de aprendizado de máquina do Azure

Você pode usar aprendizado de máquina do Azure para construir e colocar em operação modelos de análise de texto. Esses modelos podem ajudá-lo a resolver, por exemplo, problemas de análise de classificação ou sentimento de documento.

Em um experimento de análise de texto, você faria normalmente:

 1. Limpar e pré-processo de conjunto de dados de texto
 2. Extrair vetores de recurso numéricos de texto previamente processado
 3. Modelo de classificação ou regressão trem
 4. Pontuação e validar o modelo
 5. Implantar o modelo de produção

Neste tutorial, você vai aprender estas etapas como podemos percorrer um modelo de análise de sentimento usando revisões de livros da Amazon dataset (consulte esta pesquisa "biografias, Bollywood, caixas de explosão e liquidificadores: adaptação de domínio para classificação sentimento" John Blitzer, Mark Dredze e Fernando Pereira; Associação de computação linguística (ACL), 2007.) Este dataset consiste em pontuações de revisão (1-2 ou 4-5) e um texto de forma livre. O objetivo é prever a pontuação de revisão: baixa (1 - 2) ou alto (4-5).

Você pode encontrar experiências abordadas neste tutorial na Galeria de inteligência de Cortana:

[Prever revisões de livros] (https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[Prever revisões de livros - experimento previsão] (https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Etapa 1: Limpar e pré-processo de conjunto de dados de texto

Vamos começar o experimento dividindo a pontuação de revisão em classificações de altas e baixos categorias para Formula o problema como duas classe classificação. Usamos [Editar metadados] (https://msdn.microsoft.com/library/azure/dn905986.aspx) e módulos [grupo valores categóricos] (https://msdn.microsoft.com/library/azure/dn906014.aspx).

![Criar rótulo](./media/machine-learning-text-analytics-module-tutorial/create-label.png)

Em seguida, podemos limpe o texto usando o módulo de [pré-processar texto] (https://msdn.microsoft.com/library/azure/mt762915.aspx). A limpeza reduz o ruído no dataset, ajudar você a localizar os recursos mais importantes e melhora a precisão do modelo final. Podemos remover stopwords - palavras comuns como "a" ou "a" - e números, caracteres especiais, caracteres duplicados, endereços de email e URLs. Nós também converter o texto para minúsculas, lemmatize as palavras e detectar limites de sentença que depois são indicados por "| |" símbolo no texto previamente processado.

![Pré-processar texto](./media/machine-learning-text-analytics-module-tutorial/preprocess-text.png)

O que acontece se você quiser usar uma lista personalizada de stopwords? Você pode passar no como entrada opcional. Você também pode usar personalizado c# sintaxe expressões regulares para substituir substrings e remover palavras por parte da fala: substantivos, verbos ou adjetivos.

Após o pré-processamento for concluída, podemos dividir os dados entre trem e conjuntos de teste.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Etapa 2: Extrair vetores de recurso numéricos de texto previamente processado

Para criar um modelo de dados de texto, você normalmente precisa converter texto de forma livre em vetores de recurso numéricos. Neste exemplo, usamos [extrair recursos N-gramática do texto] módulo (https://msdn.microsoft.com/library/azure/mt762916.aspx) para transformar os dados de texto para esse formato. Este módulo leva uma coluna de palavras separados espaço em branco e calcula um dicionário de palavras ou N-Gramas de palavras, que aparecem no seu conjunto de dados. Em seguida, ele conta contagem quantas vezes cada palavra, ou N-gramática, aparece em cada registro e cria vetores de recurso daqueles. Neste tutorial, definimos N-gramática tamanho como 2, portanto nossas vetores de recurso incluem palavras isoladas e combinações de palavras subsequentes dois.

![Extrair N-gramas](./media/machine-learning-text-analytics-module-tutorial/extract-ngrams.png)

Podemos aplicar TF * contagens de IDF (termo frequência frequência inverso do documento) ponderação para N-gramática. Essa abordagem adiciona espessura de palavras que aparecem frequentemente em um único registro, mas são raros entre o conjunto de dados inteiro. Outras opções incluem binário, TF e gráfico ponderar.

Esses recursos de texto geralmente têm alta dimensionalidade. Por exemplo, se seu corpo tiver 100.000 palavras exclusivas, seu espaço de recurso teria 100.000 dimensões, ou mais se N-gramas são usadas. O módulo de recursos de gramática N extrair oferece um conjunto de opções para reduzir a dimensionalidade. Você pode optar por excluir palavras que são curto ou longo ou muito incomum muito frequente tenha o valor de previsão significativo. Neste tutorial, podemos excluir N-gramas que aparecem em menos de 5 registros ou em mais de 80% de registros.

Além disso, você pode usar a seleção de recursos para selecionar somente os recursos que são mais correlacionadas com seu destino de previsão. Usamos seleção de recursos qui-quadrada para selecionar 1000 recursos. Você pode exibir o vocabulário de palavras selecionadas ou N-gramas clicando a saída direita de extrair N-gramas módulo.

Como uma abordagem alternativa para usar recursos de gramática N extrair, você pode usar o recurso hash módulo. Observação Embora essa [recurso hash] (https://msdn.microsoft.com/library/azure/dn906018.aspx) não tem recursos de seleção de compilação do recurso ou TF * IDF ponderar.

## <a name="step-3-train-classification-or-regression-model"></a>Etapa 3: Treinar modelo de classificação ou regressão

Agora o texto tem foram transformado para colunas numéricas recurso. O conjunto de dados ainda contém colunas de cadeia de caracteres de estágios anteriores, por isso usamos selecionar colunas no Dataset para excluí-los.

Em seguida, usamos [dois classe Regressão logística] (https://msdn.microsoft.com/library/azure/dn905994.aspx) para prever nosso destino: pontuação de revisão de alta ou baixa. Neste ponto, o problema de análise de texto tem foram transformado em um problema de classificação regular. Você pode usar as ferramentas disponíveis no aprendizado de máquina do Azure para melhorar o modelo. Por exemplo, você pode experimentar diferentes classificador descobrir resultados precisos como dar a eles ou usar o hyperparameter ajuste para melhorar a precisão.

![Treinar e pontuação](./media/machine-learning-text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Etapa 4: Pontuação e validar o modelo

Como você deseja validar o modelo de treinamento? Estamos pontuação-la contra o conjunto de dados de teste e avaliar a precisão. No entanto, o modelo aprendeu vocabulário do N-gramas e seus pesos do dataset treinamento. Portanto, devemos usar esses pesos e desse vocabulário quando extraindo recursos de dados de teste, ao invés de criar o vocabulário novamente. Portanto, podemos adicionar módulo de recursos de gramática N extrair à ramificação pontuação da experiência, conectar o vocabulário de saída de ramificação de treinamento e defina o modo de vocabulário como somente leitura. Também desabilitar a filtragem de N-gramas pela frequência Configurando o mínimo como 1 instância e máximo como 100% e desativar a seleção do recurso.

Depois que a coluna de texto nos dados teste forem transformados logaritmos para colunas numéricas recurso, podemos excluir as colunas de cadeia de caracteres de estágios anterior como na ramificação de treinamento. Em seguida, usamos o módulo do modelo de pontuação para fazer previsões e módulo de avaliar modelo para avaliar a precisão.

## <a name="step-5-deploy-the-model-to-production"></a>Etapa 5: Implantar o modelo de produção

O modelo é quase pronto para ser implantado em produção. Quando implantado como serviço da web, ele leva a cadeia de caracteres de texto de forma livre como entrada e retornar uma previsão "alta" ou "baixa". Ele usa o vocabulário N-gramática aprendido para transformar texto para recursos e modelo de regressão logística treinamento para fazer uma previsão desses recursos. 

Para configurar o experimento previsão, nós primeiro salvar o vocabulário N-gramática como dataset e o modelo de regressão logística treinamento da filial treinamento da experiência. Então, podemos salvar o experimento usando "Salvar como" para criar um gráfico de experimento para previsão experimento. Podemos remover o módulo de dados de divisão e a ramificação de treinamento do experimento. Podemos conecte-se a gramática N vocabulário e o modelo salvo anteriormente aos recursos de gramática N extrair e módulos do modelo de pontuação, respectivamente. Nós também remover o módulo de avaliar o modelo.

Vamos inserir selecionar colunas no módulo de Dataset antes módulo de pré-processar texto para remover a coluna de rótulo e desmarcar a opção "Acrescentar pontuação coluna dataset" no módulo de pontuação. Dessa maneira, o serviço da web não solicita o rótulo que ele está tentando prever e faz não eco recursos de entrada em resposta.

![Experimento previsão](./media/machine-learning-text-analytics-module-tutorial/predictive-text.png)

Agora, temos um experimento que pode ser publicado como um serviço da web e chamado usando APIs de execução de solicitação-resposta ou lote.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre módulos de análise de texto de [documentação MSDN] (https://msdn.microsoft.com/library/azure/dn905886.aspx).
