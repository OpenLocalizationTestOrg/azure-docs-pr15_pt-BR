<properties
    pageTitle="Como um modelo de aprendizado de máquina progride de um experimento para um serviço da Web operationalized | Microsoft Azure"
    description="Uma visão geral da mecânica de como seu progride de modelo de aprendizado de máquina do Azure de um desenvolvimento experimentar para um serviço da Web operationalized."
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
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="garye"/>


# <a name="how-a-machine-learning-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>Como um modelo de aprendizado de máquina progride de um experimento para um serviço da Web operationalized

Azure Studio de aprendizado de máquina fornece uma tela de desenho interativa que permite que você desenvolver, executar, testar e iteramos um ***experimentar*** que representa um modelo de análise de previsão. Há uma grande variedade de módulos disponíveis que podem:

* Dados de entrada em seu experimento
* Manipular os dados
* Treinar um modelo usando algoritmos de aprendizado de máquina
* Pontuação o modelo
* Avaliar os resultados
* Valores de final de saída

Quando estiver satisfeito com o seu experimento, você pode implantá-lo como um ***serviço Web de aprendizado de máquina Azure clássico*** ou um ***serviço Web de aprendizado de máquina Azure novo*** para que os usuários podem enviá-lo a novos dados e receber os resultados de volta.

Neste artigo, vamos dar uma visão geral da mecânica de como seu progride de modelo de aprendizado de máquina de um desenvolvimento experimentar para um serviço da Web operationalized.

>[AZURE.NOTE] Existem outras maneiras para desenvolver e implantar modelos de aprendizado de máquina, mas este artigo é focado sobre como usar Studio de aprendizado de máquina. Para uma discussão sobre como criar um serviço Web previsão clássico com R, consulte o postagem de blog [Construir e implantar previsão Web Apps usando RStudio e Azure ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).

Embora Studio de aprendizado de máquina do Azure é projetado para ajudá-lo a desenvolver e implantar um *modelo de análise de previsão*, é possível usar Studio para desenvolver um experimento que não inclui um modelo de análise de previsão. Por exemplo, um experimento pode apenas os dados de entrada, manipulá-lo e, em seguida, os resultados de saída. Assim como um experimento de análise de previsão, você pode implantar esse experimento não previsão como um serviço Web, mas é um processo mais simples porque o experimento não treinamento ou um modelo de aprendizado de máquina de pontuação. Embora não seja típica usar Studio dessa maneira, podemos será incluí-lo na discussão para que podemos dar uma explicação completa do funcionamento do Studio.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Desenvolvendo e implantando um serviço Web previsão

Aqui estão os estágios que uma solução típica segue como desenvolver e implantar usando Studio de aprendizado de máquina:

![Fluxo de implantação](media\machine-learning-model-progression-experiment-to-web-service\model-stages-from-experiment-to-web-service.png)

*Figura 1 - estágios de um modelo de análise de previsão típica*

### <a name="the-training-experiment"></a>O experimento de treinamento

O ***treinamento experimentar*** é a fase inicial de desenvolvimento de seu serviço da Web no Studio de aprendizado de máquina. O objetivo da experiência treinamento é fornecer um local para desenvolver, testar, iteramos e treinar eventualmente uma modelo de aprendizado de máquina. Você pode até mesmo treinar vários modelos simultaneamente como você procurar a melhor solução, mas depois que terminar a experimentar você selecionará um único treinado modelar e eliminar o restante do experimento. Para obter um exemplo de desenvolvimento de uma análise de previsão experimentar, consulte [desenvolver uma solução de análise de previsão para avaliação de risco de crédito no aprendizado de máquina do Azure](machine-learning-walkthrough-develop-predictive-solution.md).

### <a name="the-predictive-experiment"></a>O experimento previsão

Quando você tiver um modelo de treinamento em seu experimento de treinamento, clique em **Configurar o serviço Web** e selecione o **Serviço de Web previsão** no Studio de aprendizado de máquina para iniciar o processo de conversão de seu experimento de treinamento em um ***experimento previsão***. A finalidade da experiência previsão é usar o seu modelo de treinamento para pontuação novos dados, com o objetivo de eventualmente se tornar operacionalizada como um serviço Web do Azure.

Essa conversão é feita para você pelas seguintes etapas:

-   Converter o conjunto de módulos usado para o treinamento em um único módulo e salvá-lo como um modelo de treinamento

-   Eliminar quaisquer módulos estranhos não relacionados a pontuação

-   Adicionar portas de entrada e saídas que o serviço Web eventual usará

Pode haver mais alterações que você deseja fazer para preparar seu experimento previsão implantar como um serviço da Web. Por exemplo, se desejar que o serviço Web para apenas um subconjunto dos resultados de saída, você pode adicionar um módulo de filtragem antes da porta de saída.

Nesse processo de conversão, a experiência de treinamento não é descartada. Quando o processo for concluído, você tem duas guias no Studio: uma para a experiência de treinamento e outra para o experimento previsão. Dessa maneira você pode fazer alterações a experiência de treinamento antes de implantar o serviço da Web e recriar o experimento previsão. Ou você pode salvar uma cópia da experiência treinamento para iniciar outra linha do experimento.

>[AZURE.NOTE] Quando você clica em **Previsão serviço da Web** que você iniciar um processo automático para converter seu experimento de treinamento para um experimento previsão e isso funciona bem na maioria dos casos. Se o seu experimento treinamento for complexo (por exemplo, se você tiver vários caminhos de treinamento que você unir), talvez você prefira fazer essa conversão manualmente. Para obter mais informações, consulte [converter um experimento de treinamento de aprendizado de máquina para um experimento previsão](machine-learning-convert-training-experiment-to-scoring-experiment.md).

### <a name="the-web-service"></a>O serviço da Web

Quando estiver satisfeito que seu experimento previsão estiver pronto, você pode implantar seu serviço como qualquer serviço Web clássico ou um serviço Web novo com base em Gerenciador de recursos do Azure. Para colocar em operação seu modelo Implantando-o como um *serviço Web de aprendizado de máquina clássico*, clique em **Implantar o serviço da Web** e selecione **Implantar o serviço Web [clássico]**. Para implantar como *serviço Web de aprendizado de máquina novo*, clique em **Implantar o serviço da Web** e selecione **Implantar [novo] serviço Web**. Agora, os usuários podem enviar dados para o seu modelo usando o serviço de Web API REST e receber os resultados de volta. Para obter mais informações, consulte [como consumir um serviço da Web de aprendizado de máquina Azure que tenha sido implantado de uma experimento de aprendizado de máquina](machine-learning-consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Caso não seja típica: Criando um serviço Web não previsão

Se seu experimento não treinar um modelo de análise de previsão, então você não precisa criar uma experiência de treinamento e um experimento pontuação - não há apenas um experimento e você pode implantá-lo como um serviço Web. Studio de aprendizado de máquina detecta se o seu experimento contém um modelo de previsão analisando os módulos que você usou.

Depois que você já iteração em seu experimento e estiver satisfeito com ele:

1.  Clique em **Configurar o serviço da Web** e selecione o **Serviço da Web de treinamento** - nós de entrada e saídos são adicionados automaticamente

2.  Clique em **Executar**

3. Clique em **Implantar o serviço da Web** e selecione **implantar Web [clássico]** ou **Implantar o serviço Web [novo]** dependendo do ambiente à qual você deseja implantar.

Seu serviço da Web agora é implantado e você pode acessar e gerenciá-lo como um serviço da Web de previsão.

## <a name="updating-your-web-service"></a>Atualizando seu serviço da Web

Agora que você já implantou o seu experimento como um serviço Web, o que acontece se você precisa atualizá-lo?

Que depende o que você precisa atualizar:

**Você deseja alterar a entrada ou saída, ou que você deseja modificar como o serviço Web manipula dados**

Se você não estiver alterando o modelo, mas estiver apenas alterando como o serviço Web lida com dados, você pode editar o experimento previsão e, em seguida, clique em **Implantar o serviço da Web** e selecione **implantar Web [clássico]** ou **Implantar o serviço Web [novo]** novamente. O serviço Web for interrompido, o experimento previsão atualizado é implantado e o serviço da Web seja reiniciado.

Aqui está um exemplo: suponha que seu experimento previsão retornará a linha inteira de dados de entrada com o resultado previsto. Você pode decidir o que você deseja que o serviço da Web para retornar apenas o resultado. Portanto, você pode adicionar um módulo de **Colunas de projeto** a previsão experimento, logo antes da porta de saída, excluir colunas diferente de resultado. Quando você clicar **Implantar o serviço da Web** e selecione **implantar Web [clássico]** ou **Implantar o serviço Web [novo]** novamente, o serviço da Web é atualizado.

**Você quer treinar novamente o modelo com novos dados**

Se você deseja manter seu modelo de aprendizado de máquina, mas você gostaria de treiná-la novamente com novos dados, você tem duas opções:

1.  **Treinar novamente o modelo enquanto o serviço da Web está em execução** - se quiser treinar novamente seu modelo durante a previsão serviço da Web está em execução, você pode fazer isso fazendo algumas modificações para a experiência de treinamento para torná-lo um ***experimento novos treinamentos***, em seguida, você pode implantá-lo como um **serviço *web novos treinamentos* **. Para obter instruções sobre como fazer isso, consulte [modelos de aprendizado de máquina treinar novamente programaticamente](machine-learning-retrain-models-programmatically.md).

2.  **Volte para o original experimento de treinamento e os dados de treinamento diferentes de uso para desenvolver seu modelo** - seu experimento previsão está vinculado ao serviço da Web, mas a experiência de treinamento não está diretamente vinculada dessa maneira. Se você modificar o experimento treinamento original e clique em **Configurar o serviço Web**, ele criará uma *nova* previsão experimentar que, quando implantado, irá criar um *novo* serviço Web. Ele não atualiza apenas o serviço da Web original.

    Se você precisar modificar o experimento de treinamento, abra-o e clique em **Salvar como** para fazer uma cópia. Isso deixar intacto o experimento treinamento original, experimento previsão e o serviço da Web. Agora você pode criar um novo serviço Web com suas alterações. Depois de implantar o novo serviço da Web, que você pode decidir se parar o serviço Web anterior ou mantê-lo funcionando junto com o novo.

**Você deseja treinar um modelo diferente**

Se você deseja fazer alterações em seu experimento de previsão original, como selecionar um algoritmo de aprendizado de máquina diferente, tentar um método de treinamento diferentes, etc., você precisa siga o segundo procedimento descrito acima para treinamento seu modelo: Abra o experimento de treinamento, clique em **Salvar como** para fazer uma cópia e inicie o novo caminho de desenvolvimento de seu modelo , criando o experimento previsão e implantando o serviço web. Isso criará uma nova Web serviço não relacionados ao original - você pode decidir qual delas, ou ambas, para continuar a execução.

## <a name="next-steps"></a>Próximas etapas

Para obter mais detalhes sobre o processo de desenvolvimento e experimento, consulte os seguintes artigos:

-   convertendo o experimento - [converter um experimento de treinamento de aprendizado de máquina para um experimento previsão](machine-learning-convert-training-experiment-to-scoring-experiment.md)

-   Implantando o serviço Web - [implantar um serviço da web de aprendizado de máquina do Azure](machine-learning-publish-a-machine-learning-web-service.md)

-   treinamento o modelo - [modelos de aprendizado de máquina treinar novamente programaticamente](machine-learning-retrain-models-programmatically.md)

Para obter exemplos de todo o processo, consulte:

-   [Tutorial de aprendizado de máquina: criar seu primeiro experimento no Studio de aprendizado de máquina do Azure](machine-learning-create-experiment.md)

-   [Passo a passo: Desenvolver uma solução de análise de previsão para avaliação de risco de crédito no aprendizado de máquina do Azure](machine-learning-walkthrough-develop-predictive-solution.md)