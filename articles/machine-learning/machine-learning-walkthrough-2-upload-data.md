<properties
    pageTitle="Etapa 2: Carregar dados em uma experiência de aprendizado de máquina | Microsoft Azure"
    description="Etapa 2 da desenvolver um passo a passo de solução de previsão: carregamento armazenados dados públicos para Studio de aprendizado de máquina do Azure."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016" 
    ms.author="garye"/>


# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>Etapa 2 do passo a passo: Carregar dados existentes em uma experiência de aprendizado de máquina do Azure

Esta é a segunda etapa da explicação, [desenvolver uma solução de análise de previsão no aprendizado de máquina do Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Criar um espaço de trabalho de aprendizado de máquina](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  **Carregue os dados existentes**
3.  [Criar uma nova experiência](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Treinar e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Implantar o serviço da web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Acessar o serviço web](machine-learning-walkthrough-6-access-web-service.md)

----------

Para desenvolver um modelo de previsão para o risco de crédito, precisamos dados que podemos usar para treinar e, em seguida, teste o modelo. Para este passo a passo, usaremos "UCI Statlog (dados de crédito alemão) conjunto de dados" do repositório UCI aprendizado de máquina. Você pode encontrá-lo aqui:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://Archive.ICS.uci.edu/ML/datasets/Statlog+(German+Credit+data)</a>

Vamos usar o arquivo chamado **german.data**. Baixe esse arquivo em seu disco rígido local.  

Este dataset contém linhas de 20 variáveis para 1000 candidatos passados para crédito. Essas 20 variáveis representam vetor de recurso do dataset, que fornece características de identificação para cada candidato de crédito. Uma coluna adicional em cada linha representa o risco de crédito calculado do candidato, com 700 candidatos identificados como um risco de crédito baixa e 300 como um alto risco.

O site UCI fornece uma descrição dos atributos do vetor do recurso, que incluem informações financeiras, histórico de crédito, status de emprego e informações pessoais. Para cada candidato, uma classificação binária foi determinado indicando se elas são uma baixa ou alto risco de crédito.  

Usaremos esses dados para treinar um modelo de análise de previsão. Quando concluir, nosso modelo deve ser capaz de aceitar informações para novos indivíduos e prever se eles são um risco de crédito alta ou baixa.  

Aqui está uma mudança interessante. A descrição do dataset explica que classifique uma pessoa como um risco de crédito baixa quando elas são realmente um risco alto crédito é 5 vezes mais caro para a instituição financeira que classifique um risco de crédito baixa como alto. Uma maneira simple para levar isso em conta no nosso experimento é duplicar (5 vezes) essas entradas que representam alguém com um risco de crédito alta. Em seguida, se o modelo classificar incorretamente um risco de crédito alta como baixa, ele fará que misclassification 5 vezes, uma vez para cada duplicata. Isso aumentará o custo desse erro nos resultados de treinamento.  

##<a name="convert-the-dataset-format"></a>Converter o formato de conjunto de dados
O conjunto de dados original usa um formato separados em branco. Studio de aprendizado de máquina funciona melhor com um arquivo de valores separados por vírgula (CSV), para que nós converterá dataset substituindo espaços por vírgulas.  

Há várias maneiras para converter esses dados. Uma maneira é usando o seguinte comando do Windows PowerShell:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Outra maneira é usando o comando de sed Unix:  

    sed 's/ /,/g' german.data > german.csv  

Nos dois casos, criamos uma versão separados por vírgulas dos dados em um arquivo denominado **german.csv** que usaremos em nosso experimento.

##<a name="upload-the-dataset-to-machine-learning-studio"></a>Carregar o dataset Studio de aprendizado de máquina

Depois que os dados foi convertidos em formato CSV, precisamos carregá-lo no Studio de aprendizado de máquina. Para obter mais informações sobre como começar com Studio de aprendizado de máquina, consulte [Microsoft Azure máquina aprendizagem Studio Home](https://studio.azureml.net/).

1.  Abra Studio ([https://studio.azureml.net](https://studio.azureml.net)) de aprendizado de máquina. Quando solicitado a entrar, use a conta que você especificou como o proprietário do espaço de trabalho.
1.  Clique na guia **Studio** na parte superior da janela.
1.  Clique em **+ nova** na parte inferior da janela.
1.  Selecione **DATASET**.
1.  Selecione o **arquivo LOCAL FROM**.
1.  Na caixa de diálogo **carregar um novo conjunto de dados** , clique em **Procurar** e localize o arquivo de **german.csv** que você criou.
1.  Insira um nome para o conjunto de dados. Para este passo a passo, será chamamos "Dados do cartão de crédito do alemão UCI".
1.  Para tipo de dados, selecione **arquivo CSV genérico sem cabeçalho (. nh.csv)**.
1.  Adicione uma descrição se desejar.
1.  Clique em **Okey**.  

![Carregar o conjunto de dados][1]  


Isso carrega os dados em um módulo de conjunto de dados que podemos usar em um experimento.

> [AZURE.TIP] Para gerenciar conjuntos de dados que você carregou Studio, clique na guia **conjuntos de dados** à esquerda da janela Studio.

Para obter mais informações sobre como importar vários tipos de dados para um experimento, consulte [importar seus dados de treinamento para o Azure Studio de aprendizado de máquina](machine-learning-data-science-import-data.md).

**Avançar: [criar uma nova experiência](machine-learning-walkthrough-3-create-new-experiment.md)**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png
