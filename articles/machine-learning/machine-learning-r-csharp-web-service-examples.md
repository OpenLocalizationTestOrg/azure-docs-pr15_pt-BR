<properties 
    pageTitle="Exemplos construídos com R de serviços web de aprendizado de máquina | Microsoft Azure" 
    description="Encontre um conjunto útil de web exemplos de serviços criados com código de R e aprendizado de máquina e publicado do Azure Marketplace." 
    keywords="CSharp, código de r, exemplos de serviços web"
    services="machine-learning" 
    documentationCenter="" 
    authors="jaymathe" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/14/2016" 
    ms.author="jaymathe"/> 


#<a name="web-services-examples-using-r-code-on-azure-machine-learning-and-published-to-microsoft-azure-marketplace"></a>Web services exemplos usando o código de R em aprendizado de máquina do Azure e publicados no Microsoft Azure Marketplace

Neste artigo são web de exemplo serviços foram criados usando o aprendizado de máquina do Azure e publicados do Azure Marketplace. Cada exemplo de serviço web tem um documento abrangente anexado, incorporação de conjuntos de dados de exemplo para testando os serviços e explicando como o usuário pode criar um serviço semelhante próprios. 

No Azure Studio de aprendizado de máquina, os usuários podem escrever código R e com apenas alguns cliques, publicá-lo como um serviço da web para aplicativos e dispositivos consumir em todo o mundo. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


De produzir calculadoras simples que fornecem funcionalidade estatística para criar uma previsão de análise de sentimento mineração de texto personalizado, novos e experientes usuários de R podem aproveitar a facilidade com a qual os usuários de aprendizado de máquina do Azure podem colocar em operação o código de R. Enquanto essas web services poderia ser consumido por usuários, potencialmente por meio de um aplicativo móvel ou um site, a finalidade desses exemplos de serviços web é mostrar como aprendizado de máquina pode colocar em operação R scripts para fins analíticos e ser usado para criar serviços web sobre código R.

Cada exemplo inclui um exemplo c# para consumo de serviço web.


![Diagrama de código de R em aprendizado de máquina do Azure: soluções de R para proprietários usar ou publicados do Azure Marketplace.][1]

Considere os seguintes cenários.

##<a name="scenario-1-generic-model"></a>Cenário 1: Modelo de genérico 
Um usuário trabalha com um modelo genérico que pode ser aplicado aos dados de um novo usuário, como uma previsão básica de dados da série de tempo ou um método de R personalizado com análises avançadas. Este usuário publica o modelo como um serviço da web para que outras pessoas consumir com seus dados.



* [Classificador binário](machine-learning-r-csharp-binary-classifier.md)
* [Modelo de cluster](machine-learning-r-csharp-cluster-model.md)
* [Multivariate Regressão Linear](machine-learning-r-csharp-multivariate-linear-regression.md)
* [Previsão - Ajuste exponencial](machine-learning-r-csharp-forecasting-exponential-smoothing.md)
* [ETS + STL previsões](machine-learning-r-csharp-retail-demand-forecasting.md)
* [Previsão - Autoregressive integrada movendo média (ARIMA)](machine-learning-r-csharp-arima.md)
* [Análise de sobrevivência](machine-learning-r-csharp-survival-analysis.md)


##<a name="scenario-2-trained-model--specific-data"></a>Cenário 2: Treinamento modelo – dados específicos 
Um usuário tem dados que fornece previsões útil por meio de código de R, como uma amostra grande de questionários personalidade agrupada por meio de um algoritmo de k significa prever tipo de personalidade ou dados de pesquisa de integridade que podem ser usados para prever o risco de um indivíduo para câncer Pulmão com um pacote de análise R de sobrevivência do usuário. O usuário publica os dados por meio de um serviço web que prevê o resultado de um novo usuário.

##<a name="scenario-3-trained-model--generic-data"></a>Cenário 3: Treinamento modelo – dados genéricos 
Um usuário tem dados genéricos (como um corpo de texto) que permite que um serviço web ser criados e aplicados genericamente em diferentes tipos de casos de uso e cenários.

* [Dicionário com base em análise de sentimento](machine-learning-r-csharp-lexicon-based-sentiment-analysis.md)

##<a name="scenario-4-advanced-calculator"></a>Cenário 4: Calculadora de avançadas 
Um usuário fornece cálculos avançados ou simulações que não exigem qualquer modelo de treinamento ou ajuste de um modelo para os dados do usuário.

* [Diferença no teste de proporções](machine-learning-r-csharp-difference-in-two-proportions.md)
* [Pacote de distribuição normal](machine-learning-r-csharp-normal-distribution.md)
* [Distribuição binomial Suite](machine-learning-r-csharp-binomial-distribution.md)

##<a name="faq"></a>Perguntas Freqüentes
Para perguntas frequentes sobre o consumo do serviço da web ou publicação para o Marketplace, veja [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-web-service-examples/machine-learning-r-code-options-for-using-and-sharing-cloud.png


 
