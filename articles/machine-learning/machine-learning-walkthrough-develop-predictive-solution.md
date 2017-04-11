<properties
    pageTitle="Uma solução de previsão para riscos de crédito com aprendizado de máquina | Microsoft Azure"
    description="Um detalhadas passo a passo mostra como criar uma solução de análise de previsão para avaliação de risco de crédito no Azure Studio de aprendizado de máquina."
    keywords="risco de crédito, solução de análise de previsão, avaliação de risco"
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
    ms.date="09/16/2016"
    ms.author="garye"/>


# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Passo a passo: Desenvolver uma solução de análise de previsão para avaliação de risco de crédito no aprendizado de máquina do Azure

Suponha que você precise prever o risco de crédito de um indivíduo com base nas informações que dar a eles em um aplicativo de crédito.  

Avaliação de risco de crédito é um problema complexo, naturalmente, mas vamos simplificar um pouco os parâmetros da pergunta. Em seguida, podemos usá-lo como um exemplo de como você pode usar o aprendizado de máquina do Microsoft Azure com Studio de aprendizado de máquina e o serviço da web de aprendizado de máquina para criar uma solução de análise de previsão desse tipo.  

Este passo a passo detalhadas, podemos será seguem o processo de desenvolvimento de um modelo de análise de previsão no Studio de aprendizado de máquina e depois implantá-lo como um serviço da web de aprendizado de máquina do Azure. Podemos vai iniciar com dados de risco de crédito publicamente disponível, desenvolver treinar um modelo de previsão com base em dados e, em seguida, implantar o modelo como um serviço web que pode ser usado por outras pessoas para avaliação de risco de crédito.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<!-- -->

>[AZURE.TIP] Para baixar e imprimir um diagrama que oferece uma visão geral dos recursos do Studio de aprendizado de máquina, consulte o [diagrama de visão geral dos recursos do Azure Studio de aprendizado de máquina](machine-learning-studio-overview-diagram.md).

Para criar uma solução de avaliação de risco de crédito, podemos será siga estas etapas:  

1.  [Criar um espaço de trabalho de aprendizado de máquina](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Carregue os dados existentes](machine-learning-walkthrough-2-upload-data.md)
3.  [Criar uma nova experiência](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Treinar e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Implantar o serviço da web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Acessar o serviço web](machine-learning-walkthrough-6-access-web-service.md)

Este passo a passo baseia-se em uma versão simplificada do [classificação binária: previsão de risco de crédito](http://go.microsoft.com/fwlink/?LinkID=525270) experimento de amostra na [Galeria de inteligência de Cortana](http://gallery.cortanaintelligence.com/).
