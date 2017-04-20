<properties
    pageTitle="Um experimento simple na máquina Learning Studio | Microsoft Azure"
    description="Este tutorial de aprendizado de máquina o orienta durante um experimento ciência de dados fácil. Podemos vai prever o preço de um carro usando um algoritmo de regressão."
    keywords="experimentar, regressão linear, algoritmos de aprendizado de máquina, tutorial de aprendizado de máquina, técnicas de modelagem de previsão, dados ciência experimento"
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
    ms.topic="hero-article"
    ms.date="07/14/2016"
    ms.author="garye"/>

# <a name="machine-learning-tutorial-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>Tutorial de aprendizado de máquina: criar seu primeiro experimento de ciência de dados no Azure máquina aprendizagem Studio

Este tutorial de aprendizado de máquina o orienta durante um experimento ciência de dados fácil. Criaremos um modelo de regressão linear prevê o preço de um automóvel com base em diferentes variáveis como fazer e especificações técnicas. Para fazer isso, usaremos o Studio de aprendizado de máquina do Windows Azure para desenvolver e iterar em um simples previsão experiências de análise.

*Análise de previsão* é um tipo de ciência de dados que usa dados atuais para prever resultados futuros. Para obter um exemplo muito simple de análise de previsão, assista ciência de dados para iniciantes vídeo 4: [prever uma resposta com um modelo simples](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) (tempo de execução: 7:42).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="how-does-machine-learning-studio-help"></a>Como o Studio de aprendizado de máquina ajuda?

Máquina Learning Studio facilita configurar um experimento usando módulos de arrastar e soltar pré-programadas com as técnicas de modelagem de previsão. Para executar seu experimento e prever uma resposta, você usará o Studio de aprendizado de máquina para *criar um modelo*, *treinar o modelo*e *pontuação e testar o modelo*.

Insira o Studio de aprendizado de máquina: [https://studio.azureml.net](https://studio.azureml.net). Se você tiver entrado no Studio de aprendizado de máquina antes, clique em **entrar aqui**. Caso contrário, clique em **Inscrever-se** e escolher entre as opções pagas e gratuitos.

Para obter informações gerais sobre o Studio de aprendizado de máquina, consulte [Studio de aprendizado de máquina de novidades?](machine-learning-what-is-ml-studio.md)

## <a name="five-steps-to-create-an-experiment"></a>Cinco etapas para criar um experimento

Este tutorial de aprendizado de máquina, você seguirá cinco etapas básicas para construir um experimento na máquina aprendizagem Studio para criar, treinar e pontuação seu modelo:

- Criar um modelo
    - [Etapa 1: Inserir dados]
    - [Etapa 2: Pré-processar dados]
    - [Etapa 3: Definir recursos]
- Treinar o modelo
    - [Etapa 4: Escolher e aplique um algoritmo de aprendizagem]
- Pontuação e testar o modelo
    - [Etapa 5: Prever novos preços de automóveis]

[Etapa 1: Inserir dados]: #step-1-get-data
[Etapa 2: Pré-processar dados]: #step-2-preprocess-data
[Etapa 3: Definir recursos]: #step-3-define-features
[Etapa 4: Escolher e aplique um algoritmo de aprendizagem]: #step-4-choose-and-apply-a-learning-algorithm
[Etapa 5: Prever novos preços de automóveis]: #step-5-predict-new-automobile-prices


## <a name="step-1-get-data"></a>Etapa 1: Inserir dados

Há um número de conjuntos de dados de amostra incluídos Studio de aprendizado de máquina que você pode escolher e você pode importar dados de várias fontes. Neste exemplo, usaremos o conjunto de dados de exemplo incluídos, **dados de preço do automóvel (Raw)**.
Este conjunto de dados inclui entradas para um número de veículos individuais, incluindo informações sobre como tornar, modelo, especificações técnicas e preço.

1. Iniciar um novo experimento clicando em **+ novo** na parte inferior da janela Studio de aprendizado de máquina, selecione **Testar**e selecione **Testar em branco**. Selecione o nome do experimento padrão na parte superior da tela e renomeie-o para algo significativo, por exemplo, **previsão de preço do automóvel**.

2. À esquerda da experiência tela é uma paleta de conjuntos de dados e módulos. Tipo de **automóvel** na caixa Pesquisar na parte superior desta paleta para localizar o conjunto de dados rotulada como **dados de preço do automóvel (Raw)**.

    ![Pesquisa de paleta][screen1a]

3. Arraste o conjunto de dados à tela experimento.

    ![Conjunto de dados][screen1]

Para ver o que esses dados aparência, clique na porta de saída na parte inferior do conjunto de dados automóvel e selecione **Visualizar**.

![Porta de saída do módulo][screen1c]

As variáveis no conjunto de dados aparecem como colunas, e cada instância de um automóvel aparece como uma linha. A coluna da extrema direita (coluna 26 e intitulado "preço") é a variável de destino, vamos tentar prever.

![Visualização do conjunto de dados][screen1b]

Feche a janela de visualização clicando no "**x**" no canto superior direito.

## <a name="step-2-preprocess-data"></a>Etapa 2: Pré-processar dados

Um dataset normalmente exige algumas pré-processamento antes que ele pode ser analisado. Você pode ter percebido os valores ausentes presentes nas colunas de várias linhas. Esses valores ausentes precisam ser limpos para que o modelo e exibir pode analisar os dados corretamente. No nosso caso, podemos removerá qualquer linhas que possuem valores ausentes. Além disso, a coluna de **perdas normalizado** tem uma grande proporção de valores ausentes, então a vai excluímos àquela coluna do modelo de todo.

> [AZURE.TIP] Limpar os valores ausentes da entrada de dados é um pré-requisito para a maioria dos módulos.

Primeiro vai removemos a coluna de **perdas normalizado** e, em seguida, você removerá qualquer linha que tem dados ausentes.

1. Digite **Selecionar colunas** na caixa Pesquisar na parte superior da paleta módulo para encontrar as [Selecionar colunas em Dataset] [ select-columns] módulo, em seguida, arraste-o para o experimento tela e conectá-lo para a porta de saída do conjunto de dados de **dados de preços do automóvel (Raw)** . Este módulo nos permite selecionar quais colunas de dados que queremos incluir ou excluir no modelo.

2. Selecione [Selecionar colunas em Dataset] [ select-columns] módulo e clique em **Iniciar o seletor de coluna** no painel **Propriedades** .

    - À esquerda, clique em **com regras**
    - Em **Começar com**, clique em **todas as colunas**. Isso instrui [Selecionar colunas em Dataset] [ select-columns] a passagem de todas as colunas (exceto aqueles que estamos prestes a serem excluídas).
    - Nos menus suspensos, selecione **Excluir** e **nomes de coluna**e, em seguida, clique dentro da caixa de texto. É exibida uma lista de colunas. Selecione **perdas normalizado**e ele serão adicionado à caixa de texto.
    - Clique no botão de marca de seleção (Okey) para fechar o seletor de colunas.

    ![Selecionar colunas][screen3]

    O painel de propriedades para **Selecionar colunas em Dataset** indica que passa por todas as colunas do conjunto de dados, exceto **perdas normalizado**.

    ![Selecione colunas nas propriedades do conjunto de dados][screen4]

    > [AZURE.TIP] Você pode adicionar um comentário a um módulo clicando duas vezes em módulo e inserindo texto. Isso pode ajudá-lo a ver rapidamente o que o módulo está fazendo em seu experimento. Nesse caso, clique duas vezes em [Selecionar colunas no conjunto de dados] [ select-columns] módulo e o comentário do tipo "excluem perdas normalizado."

3. Arraste a [Limpeza de dados ausentes] [ clean-missing-data] módulo para o experimento tela e conectá-lo a [Selecionar colunas em Dataset] [ select-columns] módulo. No painel **Propriedades** , selecione **Remover linha inteira** em **modo de limpeza** para limpar os dados por meio da remoção de linhas que possuem valores ausentes. Clique duas vezes no módulo e digite o comentário "Remover linhas de valor ausente".

    ![Propriedades de dados ausentes limpas][screen4a]

4. Execute o experimento clicando em **Executar** , sob a tela de desenho experimento.

Quando o experimento for concluído, todos os módulos têm uma marca de seleção verde para indicar que concluído com êxito. Observe também o status **concluído executando** no canto superior direito.

![Experimento primeiro execute][screen5]

Tudo o que fizemos no experimento até esse ponto é limpar os dados. Se você deseja exibir o conjunto de dados limpo, clique na porta de saída à esquerda dos [Dados ausentes limpos] [ clean-missing-data] módulo ("limpo dataset") e selecione **Visualizar**. Observe que a coluna de **perdas normalizado** não está mais incluída, e não existem valores ausentes.

Agora que os dados são limpos, estamos prontos para especificar quais recursos, vamos usar no modelo de previsão.

## <a name="step-3-define-features"></a>Etapa 3: Definir recursos

Na máquina aprendizagem, *recursos* são propriedades mensuráveis individuais de algo que esteja interessado. Em nosso dataset, cada linha representa um automóvel e cada coluna é um recurso de que automóvel.

Localizando um bom conjunto de recursos para a criação de um modelo de previsão requer experimentação e conhecimentos sobre o problema que você deseja resolver. Alguns recursos são melhores para prever o destino que outros. Além disso, alguns recursos têm uma forte correlação com outros recursos (por exemplo, cidade-mpg versus estrada-mpg), para que eles não adicionará quantidade novas informações para o modelo e só podem ser removidas.

Vamos criar um modelo que usa um subconjunto dos recursos em nosso dataset. Você pode voltar e selecione recursos diferentes, execute o experimento novamente e ver se você obter melhores resultados. Mas, para começar, vamos tentar os seguintes recursos:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Arraste outro [Selecionar colunas em Dataset] [ select-columns] módulo para o experimento tela e conectá-lo para a porta de saída à esquerda dos [Dados ausentes limpos] [ clean-missing-data] módulo. Clique duas vezes no módulo e digite "Selecionar recursos para previsão."

2. No painel **Propriedades** , clique em **Iniciar o seletor de colunas** .

3. Clique **com as regras**.

4. Em **Começar com** clique **sem colunas**e selecione **incluir** e **nomes de coluna** na linha do filtro. Insira a nossa lista de nomes de coluna. Isso instrui o módulo a passagem somente as colunas que podemos especificar.

    > [AZURE.TIP] Executando o experimento, garantimos que as definições de coluna para os nossos dados passam do conjunto de dados através dos [Dados ausentes limpos] [ clean-missing-data] módulo. Isso significa que outros módulos de que se conectar também terá informações do conjunto de dados.

5. Clique no botão de marca de seleção (Okey).

![Selecionar colunas][screen6]

Isso produz o conjunto de dados que será usado no algoritmo de aprendizado nas próximas etapas. Posteriormente, você pode retornar e tente novamente com uma seleção diferente de recursos.

## <a name="step-4-choose-and-apply-a-learning-algorithm"></a>Etapa 4: Escolher e aplique um algoritmo de aprendizagem

Agora que os dados estiverem prontos, construir um modelo de previsão de indisponibilidade consiste em treinamento e testes. Usaremos nossos dados para treinar o modelo e depois testar o modelo para ver como fechar é capaz de prever preços. No momento, não se preocupe por que precisamos treinar e, em seguida, um modelo de teste.

*Classificação* e *regressão* são dois tipos de máquina supervisionada técnicas de aprendizagem. Classificação prevê uma resposta de um conjunto definido de categorias, como uma cor (vermelha, azul ou verde). Regressão é usado para prever um número.

Porque queremos prever preço, que é um número, usaremos um modelo de regressão. Neste exemplo, podemos vai treinar um modelo de *regressão linear* de simples e na próxima etapa, podemos vai testá-lo.

1. Usamos nossos dados de treinamento e o teste dividindo-o em treinamento separado e conjuntos de teste. Selecione e arraste os [Dados de divisão] [ split] módulo para o experimento tela e conectá-lo a saída do último [Selecionar colunas em Dataset] [ select-columns] módulo. Defina a **fração de linhas em que o primeiro conjunto de dados de saída** para 0,75. Dessa forma, vamos usar 75 por cento dos dados para treinar o modelo e reter 25 por cento de teste.

    > [AZURE.TIP] Alterando o parâmetro de **propagação aleatória** , você pode produzir diferentes amostras aleatórias para treinamento e teste. Esse parâmetro controla a propagação do gerador de números pseudo-aleatório.

2. Execute o experimento. Isso permite [Selecionar colunas em Dataset] [ select-columns] e [Dados de divisão] [ split] módulos passe definições de coluna para os módulos serão adicionados Avançar.  

3. Para selecionar o algoritmo de aprendizado, expanda a categoria de **Aprendizado de máquina** na paleta módulo à esquerda da tela e expanda **Inicializar o modelo**. Isso exibe várias categorias de módulos que podem ser usados para inicializar algoritmos de aprendizado de máquina.

    Para esse experimento, selecione a [Regressão Linear] [ linear-regression] módulo sob a categoria de **regressão** (você também pode encontrar o módulo digitando "regressão linear" na caixa de pesquisa paleta) e arraste-o à tela experimento.

4. Localizar e arraste o [Modelo de treinar] [ train-model] módulo à tela experimento. Conecte-se a porta de entrada à esquerda, a saída de [Regressão Linear] [ linear-regression] módulo. Conectar-se a porta de entrada correta na saída de dados de treinamento (porta esquerda) dos [Dados da divisão] [ split] módulo.

5. Selecionar um [Modelo de treinar] [ train-model] módulo, clique em **Iniciar o seletor de colunas** no painel de **Propriedades** e selecione a coluna **preço** . Esse é o valor que nosso modelo será prever.

    ![Selecione a coluna "preço"][screen7]

6. Execute o experimento.

O resultado é um modelo de regressão treinados que pode ser usado para novos exemplos para fazer previsões de pontuação.

![Aplicando o algoritmo de aprendizado de máquina][screen8]

## <a name="step-5-predict-new-automobile-prices"></a>Etapa 5: Prever novos preços de automóveis

Agora que podemos ter treinados do modelo usando 75% de nossos dados, podemos usá-lo para pontuação os outros 25 por cento dos dados para ver o quão bem funções nosso modelo.

1. Localizar e arraste o [Modelo de pontuação] [ score-model] módulo para o experimento tela e conecte-se a porta de entrada à esquerda na saída do [Modelo de treinar] [ train-model] module. Conecte-se a porta de entrada correta na saída de dados de teste (direita porta) dos [Dados da divisão] [ split] módulo.  

    ![Módulo do modelo de pontuação][screen8a]

2. Para executar o experimento e visualizar a saída do [Modelo de pontuação] [ score-model] módulo, clique na porta de saída e selecione **Visualizar**. A saída mostra os valores previstos para preço e os valores conhecidos dos dados de teste.  

3. Finalmente, para testar a qualidade dos resultados, selecione e arraste o [Modelo avaliar] [ evaluate-model] módulo para o experimento tela e conecte-se a porta de entrada à esquerda na saída do [Modelo de pontuação] [ score-model] módulo. (Existem duas portas de entrada porque o [Modelo de avaliar] [ evaluate-model] módulo pode ser usado para comparar dois modelos.)

4. Execute o experimento.

Para exibir a saída do [Modelo de avaliar] [ evaluate-model] módulo, clique na porta de saída e selecione **Visualizar**. As seguintes estatísticas são mostradas para nosso modelo:

- **Média de erro absoluto** (Tornar): A média dos erros absolutos (um *erro* é a diferença entre o valor previsto e o valor real).
- **Erro de quadrados média raiz** (RMSE): A raiz quadrada da média dos quadrados erros de previsões feitas no conjunto de dados de teste.
- **Erro absoluta relativa**: A média dos erros absolutas em relação a diferença absoluta entre os valores reais e a média de todos os valores reais.
- **Erro de quadrados relativa**: A média dos quadrados erros em relação a diferença de quadrados entre os valores reais e a média de todos os valores reais.
- **Coeficiente de determinação**: também conhecido como o **valor de R quadrado**, isso é uma métrica estatística que indica o quão bem um modelo é adequado para os dados.

Para cada do erro estatísticas, menor é melhor. Um valor menor indica que as previsões mais de perto correspondem os valores reais. Para o **Coeficiente de determinação**, quanto mais perto seu valor é uma (1.0), melhor as previsões.

![Resultados de avaliação][screen9]

O experimento final deve ter esta aparência:

![Tutorial de aprendizado de máquina: Concluir experimento de regressão linear que usa as técnicas de modelagem de previsão.][screen10]

## <a name="next-steps"></a>Próximas etapas

Agora que você concluir um primeiro tutorial de aprendizado de máquina e ter seu experimento configurado, você pode iterar para tentar melhorar o modelo. Por exemplo, você pode alterar os recursos que você usar em sua previsão. Ou você pode modificar as propriedades de [Regressão Linear] [ linear-regression] algoritmo ou experimentar um algoritmo diferente todo. Até mesmo, você pode adicionar máquina de vários algoritmos para seu experimento de aprendizado de uma só vez e comparar dois usando o [Modelo de avaliar] [ evaluate-model] módulo.

> [AZURE.TIP] Use o botão **Salvar como** em tela experimento para copiar qualquer iteração do seu experimento. Você pode ver todas as iterações de seu experimento clicando em **Exibir histórico de executar** em tela de desenho. Consulte [Manage experimentar iterações no Azure máquina Learning Studio] [ runhistory] para obter mais detalhes.

[runhistory]: machine-learning-manage-experiment-iterations.md

Quando estiver satisfeito com o seu modelo, você pode implantá-lo como um serviço web a serem usadas para prever preços de automóveis usando novos dados. Consulte [implantar um serviço web de aprendizado de máquina do Azure] [ publish] para obter mais detalhes.

[publish]: machine-learning-publish-a-machine-learning-web-service.md

Para uma explicação mais extensa e detalhada das técnicas de modelagem de previsão para a criação, treinamento, pontuação e implantar um modelo, consulte [desenvolver uma solução de previsão usando o aprendizado de máquina do Azure][walkthrough].

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]:./media/machine-learning-create-experiment/screen1.png
[screen1a]:./media/machine-learning-create-experiment/screen1a.png
[screen1b]:./media/machine-learning-create-experiment/screen1b.png
[screen1c]: ./media/machine-learning-create-experiment/screen1c.png
[screen2]:./media/machine-learning-create-experiment/screen2.png
[screen3]:./media/machine-learning-create-experiment/screen3.png
[screen4]:./media/machine-learning-create-experiment/screen4.png
[screen4a]:./media/machine-learning-create-experiment/screen4a.png
[screen5]:./media/machine-learning-create-experiment/screen5.png
[screen6]:./media/machine-learning-create-experiment/screen6.png
[screen7]:./media/machine-learning-create-experiment/screen7.png
[screen8]:./media/machine-learning-create-experiment/screen8.png
[screen8a]:./media/machine-learning-create-experiment/screen8a.png
[screen9]:./media/machine-learning-create-experiment/screen9.png
[screen10]:./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
