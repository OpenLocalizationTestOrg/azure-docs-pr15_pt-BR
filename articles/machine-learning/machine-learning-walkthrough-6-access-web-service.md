<properties
    pageTitle="Etapa 6: Acessar o serviço da web de aprendizado de máquina | Microsoft Azure"
    description="Etapa 6 da desenvolver um passo a passo de solução de previsão: acessar um serviço da web de aprendizado de máquina do Azure active."
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


# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>Explicação passo a passo etapa 6: Acessar o serviço da web de aprendizado de máquina do Azure

Esta é a última etapa da explicação, [desenvolver uma solução de análise de previsão no aprendizado de máquina do Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Criar um espaço de trabalho de aprendizado de máquina](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Carregue os dados existentes](machine-learning-walkthrough-2-upload-data.md)
3.  [Criar uma nova experiência](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Treinar e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Implantar o serviço da web](machine-learning-walkthrough-5-publish-web-service.md)
6.  **Acessar o serviço web**

----------

Na etapa anterior nesta explicação podemos implantado um serviço web que usa o nosso modelo de previsão do risco de crédito. Agora, os usuários precisam ser capaz de enviar dados para ela e receber os resultados. 

O serviço web é um serviço web Azure que possa receber e retornar dados usando APIs REST em uma de duas maneiras:  

-   **Solicitação/resposta** - usuário envia uma ou mais linhas de dados de crédito para o serviço usando um protocolo HTTP e o serviço responde com um ou mais conjuntos de resultados.
-   **Execução em lotes** - usuário armazena uma ou mais linhas de dados de crédito em um blob Azure e envia a localização de blob para o serviço. O serviço pontuações todas as linhas de dados no blob de entrada, armazena os resultados em outra blob e retorna o URL do contêiner.  

A maneira mais rápida e fácil de acessar o serviço web é por meio de modelos de aplicativo Web disponível no [Azure Marketplace de aplicativo Web](https://azure.microsoft.com/marketplace/web-applications/all/).
Esses modelos de aplicativo da web podem criar um aplicativo web personalizado que sabe dados de entrada e o que ele retornará seu serviço da web. Tudo o que você precisa fazer é fornecer acesso aos seus dados e serviço web e o modelo faz o resto.

Para obter mais informações sobre como usar modelos de aplicativo da web, consulte [Consume um serviço da web de aprendizado de máquina do Azure com um modelo de aplicativo da web](machine-learning-consume-web-service-with-web-app-template.md).

Você também pode desenvolver um aplicativo personalizado para acessar o serviço web usando o código de starter fornecido em R, c# e linguagens de programação Python.
Você pode encontrar detalhes completos [como consumir um serviço da web de aprendizado de máquina do Azure que foi publicado de uma experiência de aprendizado de máquina](machine-learning-consume-web-services.md).
