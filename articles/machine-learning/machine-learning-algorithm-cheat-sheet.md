<properties
    pageTitle="Máquina de folha de enganar algoritmo de aprendizagem | Microsoft Azure"
    description="Uma folha de enganar algoritmo de aprendizado de máquina imprimível ajuda você a escolher o algoritmo certo para seu modelo de previsão no Studio de aprendizado de máquina do Azure."
    keywords="algoritmo enganar folha, folha de enganar, algoritmo de aprendizado de máquina"
    services="machine-learning"
    documentationCenter=""
    authors="brohrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/09/2016"
    ms.author="brohrer;garye" />


# <a name="machine-learning-algorithm-cheat-sheet-for-microsoft-azure-machine-learning-studio"></a>Máquina folha de enganar algoritmo de aprendizagem do Studio de aprendizado de máquina do Microsoft Azure

A **Microsoft Azure máquina aprendizagem algoritmo roteiro folha** ajuda você a escolher o algoritmo certo para um modelo de análise de previsão.

[Studio de aprendizado de máquina do Azure](https://studio.azureml.net/) tem uma biblioteca grande de algoritmos da ***regressão***, ***classificação***, ***agrupamento***e famílias de ***detecção de anomalias*** . Cada é projetado para um tipo de problema de aprendizado de máquina diferente de endereços.


## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Download: Planilha de enganar algoritmo de aprendizado de máquina

**Baixe a folha de enganar aqui: [Máquina aprendizagem algoritmo roteiro folha (11x17 pol.)](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v6.pdf)**

![Planilha de enganar de algoritmo de aprendizado de máquina: Saiba como escolher um algoritmo de aprendizado de máquina.][cheat-sheet]

[cheat-sheet]: ./media/machine-learning-algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png

Baixar e imprimir a máquina aprendizagem algoritmo roteiro planilha no tamanho Tabloide para mantê-lo à mão e obter ajuda para escolher um algoritmo.

> [AZURE.NOTE] Para um guia detalhado para usar esta planilha de enganar, consulte o artigo [como escolher algoritmos para aprendizado de máquina do Microsoft Azure](machine-learning-algorithm-choice.md) .

## <a name="more-help-with-algorithms"></a>Mais ajuda com algoritmos

* Para uma discussão mais aprofundada dos tipos diferentes de algoritmos de aprendizado de máquina, como eles são usados e como usar esta planilha de enganar para escolher o algoritmo certo, consulte [como escolher algoritmos para aprendizado de máquina do Microsoft Azure](machine-learning-algorithm-choice.md).
* Para obter uma lista por categoria de todos os algoritmos de aprendizado de máquina disponíveis no Studio de aprendizado de máquina, consulte [Modelo inicializar] [ initialize-model] no algoritmo de Studio de aprendizado de máquina e ajudar a módulo.
* Para obter uma lista completa de algoritmos e módulos no Studio de aprendizado de máquina, consulte [lista de A-Z de módulos Studio de aprendizado de máquina] [ a-z-list] na Ajuda de módulo e algoritmo de Studio de aprendizado de máquina.
* Para baixar e imprimir um diagrama que oferece uma visão geral dos recursos do Studio de aprendizado de máquina, consulte o [diagrama de visão geral dos recursos do Azure Studio de aprendizado de máquina](machine-learning-studio-overview-diagram.md).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<!-- This needs to be updated based on the new Choosing and Algorithm article

## Notes and terminology definitions for the machine learning algorithm cheat sheet

* The suggestions offered in this algorithm cheat sheet are approximate rules-of-thumb. Some can be bent, and some can be flagrantly violated. This is intended to suggest a starting point. Don’t be afraid run a head-to-head competition between several algorithms on your data. There is simply no substitute for understanding the principles of each algorithm and understanding the system that generated your data.

* Every machine learning algorithm has its own style or *inductive bias*. For a specific problem, several algorithms may be appropriate and one algorithm may be a better fit than others. But knowing which will be the best fit beforehand is not always possible. In cases like these, several algorithms are listed together in the cheat sheet. An appropriate strategy would be to try one algorithm, and if the results are not yet satisfactory, try the others. Here’s an example from the [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/) of an experiment that tries several algorithms against the same data and compares the results: [Compare Multi-class Classifiers: Letter recognition](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

* There are three main categories of machine learning: **supervised learning**, **unsupervised learning**, and **reinforcement learning**.

  * In **supervised learning**, each data point is labeled or associated with a category or value of interest.  An example of a categorical label is assigning an image as either a ‘cat’ or a ‘dog’.  An example of a value label is the sale price associated with a used car. The goal of supervised learning is to study many labeled examples like these, and then to be able to make predictions about future data points - for example, to identify new photos with the correct animal or to assign accurate sale prices to other used cars. This is a popular and useful type of machine learning. All of the modules in Azure Machine Learning are supervised learning algorithms except for [K-Means Clustering][k-means-clustering].

  * In **unsupervised learning**, data points have no labels associated with them. Instead, the goal of an unsupervised learning algorithm is to organize the data in some way or to describe its structure. This can mean grouping it into clusters, as K-means does, or finding different ways of looking at complex data so that it appears simpler.

  * In **reinforcement learning**, the algorithm gets to choose an action in response to each data point. It is a common approach in robotics, where the set of sensor readings at one point in time is a data point, and the algorithm must choose the robot’s next action. It's also a natural fit for Internet of Things applications. The learning algorithm also receives a reward signal a short time later, indicating how good the decision was. Based on this, the algorithm modifies its strategy in order to achieve the highest reward. Currently there are no reinforcement learning algorithm modules in Azure ML.

* **Bayesian methods** make the assumption of statistically independent data points. This means that the unmodeled variability in one data point is uncorrelated with others, that is, it can’t be predicted. For example, if the data being recorded is the number of minutes until the next subway train arrives, two measurements taken a day apart are statistically independent. However, two measurements taken a minute apart are not statistically independent - the value of one is highly predictive of the value of the other.

* **Boosted decision tree regression** takes advantage of feature overlap or interaction among features. That means that, in any given data point, the value of one feature is somewhat predictive of the value of another. For example, in daily high/low temperature data, knowing the low temperature for the day allows you to make a reasonable guess at the high. The information contained in the two features is somewhat redundant.

* Classifying data into more than two categories can be done by either using an inherently multi-class classifier, or by combining a set of two-class classifiers into an **ensemble**. In the ensemble approach, there is a separate two-class classifier for each class - each one separates the data into two categories:  “this class” and “not this class.” Then these classifiers vote on the correct assignment of the data point. This is the operational principle behind [One-vs-All Multiclass][one-vs-all-multiclass].

* Several methods, including logistic regression and the Bayes point machine, assume **linear class boundaries**, that is, that the boundaries between classes are approximately straight lines (or hyperplanes in the more general case). Often this is a characteristic of the data that you don’t know until after you’ve tried to separate it, but it’s something that typically can be learned by visualizing beforehand. If the class boundaries look very irregular, stick with decision trees, decision jungles, support vector machines, or neural networks.

* Neural networks can be used with categorical variables by creating a **dummy variable** for each category and setting it to 1 in cases where the category applies, 0 where it doesn’t.

-->

<!-- This is how you can add a link to the image in HTML. Don't know how to do this in markdown.
<a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet.pdf">
<img src="C:\Users\garye\azure-content-pr\articles\media\machine-learning-algorithm-cheat-sheet\cheat-sheet-small.png">
</a>
-->

<!-- Module References -->
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx
[initialize-model]: https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[one-vs-all-multiclass]: https://msdn.microsoft.com/library/azure/7191efae-b4b1-4d03-a6f8-7205f87be664/
